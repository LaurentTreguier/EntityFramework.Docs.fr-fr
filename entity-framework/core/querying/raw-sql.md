---
title: 'Requêtes SQL brutes : EF Core'
description: Utilisation du SQL brut pour les requêtes dans Entity Framework Core
author: smitpatel
ms.date: 10/08/2019
uid: core/querying/raw-sql
ms.openlocfilehash: 13f5cbfbd7a110394402bff74d51b5fcda04c642
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071132"
---
# <a name="raw-sql-queries"></a>Requêtes SQL brutes

Entity Framework Core vous permet d’examiner les requêtes SQL brutes lorsque vous travaillez avec une base de données relationnelle. Les requêtes SQL brutes sont utiles si la requête que vous souhaitez ne peut pas être exprimée à l’aide de LINQ. Les requêtes SQL brutes sont également utilisées si une requête LINQ aboutit à une requête SQL inefficace. Les requêtes SQL brutes peuvent retourner des types d’entité standard ou des [types d’entité sans clé](xref:core/modeling/keyless-entity-types) qui font partie de votre modèle.

> [!TIP]  
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/) sur GitHub.

## <a name="basic-raw-sql-queries"></a>Requêtes SQL brutes de base

Vous pouvez utiliser la `FromSqlRaw` méthode d’extension pour commencer une requête LINQ basée sur une requête SQL brute. `FromSqlRaw` peut uniquement être utilisé sur les racines de requête, qui est directement sur le `DbSet<>` .

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRaw)]

Les requêtes SQL brutes peuvent servir à exécuter une procédure stockée.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a>Passage de paramètres

> [!WARNING]
> **Toujours utiliser le paramétrage pour les requêtes SQL brutes**
>
> Lorsque vous introduisez des valeurs fournies par l’utilisateur dans une requête SQL brute, vous devez veiller à éviter les attaques par injection SQL. En plus de valider le fait que ces valeurs ne contiennent pas de caractères non valides, utilisez toujours le paramétrage qui envoie les valeurs séparées du texte SQL.
>
> En particulier, ne transmettez jamais une chaîne concaténée ou interpolée ( `$""` ) avec des valeurs non validées fournies par l’utilisateur dans `FromSqlRaw` ou `ExecuteSqlRaw` . Les `FromSqlInterpolated` `ExecuteSqlInterpolated` méthodes et autorisent l’utilisation de la syntaxe d’interpolation de chaîne d’une manière qui protège contre les attaques par injection SQL.

L’exemple suivant passe un paramètre unique à une procédure stockée en incluant un espace réservé de paramètre dans la chaîne de requête SQL et en fournissant un argument supplémentaire. Alors que cette syntaxe peut ressembler `String.Format` à la syntaxe, la valeur fournie est encapsulée dans un `DbParameter` et le nom de paramètre généré est inséré à l’emplacement où l' `{0}` espace réservé a été spécifié.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureParameter)]

`FromSqlInterpolated` est semblable à `FromSqlRaw` , mais vous permet d’utiliser la syntaxe d’interpolation de chaîne. Tout comme `FromSqlRaw` , `FromSqlInterpolated` ne peut être utilisé que sur des racines de requête. Comme dans l’exemple précédent, la valeur est convertie en `DbParameter` et n’est pas vulnérable à l’injection SQL.

> [!NOTE]
> Avant la version 3,0, `FromSqlRaw` `FromSqlInterpolated` deux surcharges étaient nommées `FromSql` . Pour plus d’informations, consultez la [section versions précédentes](#previous-versions).

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedStoredProcedureParameter)]

Vous pouvez également construire un objet DbParameter et le fournir en tant que valeur de paramètre. Étant donné qu’un espace réservé de paramètre SQL standard est utilisé, plutôt qu’un espace réservé de chaîne, `FromSqlRaw` peut être utilisé en toute sécurité :

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureSqlParameter)]

`FromSqlRaw` vous permet d’utiliser des paramètres nommés dans la chaîne de requête SQL, ce qui est utile lorsqu’une procédure stockée a des paramètres facultatifs :

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

> [!NOTE]
> **Classement des paramètres** Entity Framework Core passe des paramètres en fonction de l’ordre du `SqlParameter[]` tableau. Lors du passage `SqlParameter` de plusieurs s, le classement dans la chaîne SQL doit correspondre à l’ordre des paramètres dans la définition de la procédure stockée. Si vous ne le faites pas, vous risquez d’obtenir des exceptions de conversion de type et/ou un comportement inattendu lors de l’exécution de la procédure.

## <a name="composing-with-linq"></a>Composition avec LINQ

Vous pouvez composer en haut de la requête SQL brute initiale à l’aide des opérateurs LINQ. EF Core le traitera comme sous-requête et composera dessus dans la base de données. L’exemple suivant utilise une requête SQL brute qui effectue une sélection à partir d’une fonction table (TVF). Puis le compose à l’aide de LINQ pour effectuer un filtrage et un tri.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedComposed)]

La requête ci-dessus génère le code SQL suivant :

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a>Inclusion de données associées

La méthode `Include` peut être utilisée pour inclure des données associées, comme avec toute autre requête LINQ :

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedInclude)]

La composition avec LINQ nécessite que votre requête SQL brute soit composable, car EF Core traite le SQL fourni comme sous-requête. Les requêtes SQL qui peuvent être composées commencent par le mot clé `SELECT`. En outre, SQL passé ne doit pas contenir de caractères ou d’options qui ne sont pas valides dans une sous-requête, par exemple :

- Point-virgule de fin
- Sur le serveur SQL Server, une indication de niveau de requête en fin (par exemple, `OPTION (HASH JOIN)`)
- Sur SQL Server, une `ORDER BY` clause qui n’est pas utilisée avec `OFFSET 0` ou `TOP 100 PERCENT` dans la `SELECT` clause

SQL Server n’autorise pas la composition sur les appels de procédure stockée, toute tentative d’appliquer des opérateurs de requête supplémentaires à un tel appel aura pour résultat un SQL non valide. Utilisez `AsEnumerable` `AsAsyncEnumerable` la méthode ou juste après `FromSqlRaw` ou `FromSqlInterpolated` pour vous assurer que EF Core n’essaie pas de composer une procédure stockée.

## <a name="change-tracking"></a>Suivi des modifications

Les requêtes qui utilisent `FromSqlRaw` les `FromSqlInterpolated` méthodes ou suivent exactement les mêmes règles de suivi des modifications que toute autre requête LINQ dans EF Core. Par exemple, si la requête projette des types d’entités, les résultats sont suivis par défaut.

L’exemple suivant utilise une requête SQL brute qui effectue une sélection à partir d’une fonction table (TVF), puis désactive le suivi des modifications avec l’appel à `AsNoTracking` :

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a>Limites

Il existe quelques limitations à connaître lors de l’utilisation des requêtes SQL brutes :

- La requête SQL doit retourner des données pour toutes les propriétés du type d’entité.
- Les noms de colonne dans le jeu de résultats doivent correspondre aux noms de colonne mappés aux propriétés. Notez que ce comportement est différent de EF6. EF6 ignore la propriété pour le mappage de colonnes pour les requêtes SQL brutes et les noms de colonnes du jeu de résultats devaient correspondre aux noms de propriété.
- La requête SQL ne peut pas contenir de données associées. Toutefois, dans de nombreux cas, vous pouvez composer au-dessus de la requête à l’aide de l’opérateur `Include` pour retourner des données associées (consultez [Inclusion de données associées](#including-related-data)).

## <a name="previous-versions"></a>Versions précédentes

EF Core version 2,2 et les versions antérieures comportaient deux surcharges de méthode nommées `FromSql` , qui se présentaient de la même façon que les plus récents `FromSqlRaw` et `FromSqlInterpolated` . Il était facile d’appeler par erreur la méthode de chaîne brute lorsque l’intention était d’appeler la méthode de chaîne interpolée, et d’inverse. L’appel accidentel d’une surcharge incorrecte peut entraîner des requêtes qui ne sont pas paramétrables quand elles devraient l’être.
