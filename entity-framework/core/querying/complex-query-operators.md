---
title: Opérateurs de requête complexes-EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 2e187a2a-4072-4198-9040-aaad68e424fd
uid: core/querying/complex-query-operators
ms.openlocfilehash: 350a7fa6a3ee1de16bad4b63e10842f9356a1b60
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72186259"
---
# <a name="complex-query-operators"></a>Opérateurs de requête complexes

LINQ (Language Integrated Query) contient de nombreux opérateurs complexes, qui combinent plusieurs sources de données ou effectuent un traitement complexe. Les opérateurs LINQ n’ont pas tous des traductions appropriées côté serveur. Parfois, une requête dans un formulaire se traduit par le serveur, mais s’il est écrit dans un autre formulaire, même si le résultat est le même. Cette page décrit certains des opérateurs complexes et leurs variations prises en charge. Dans les versions ultérieures, nous pouvons reconnaître plus de modèles et ajouter les traductions correspondantes. Il est également important de garder à l’esprit que la prise en charge de la traduction varie d’un fournisseur à l’autre. Une requête particulière, qui est traduite dans SqlServer, peut ne pas fonctionner pour les bases de données SQLite.

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) sur GitHub.

## <a name="join"></a>Join

L’opérateur de jointure LINQ vous permet de connecter deux sources de données en fonction du sélecteur de clé pour chaque source, en générant un tuple de valeurs lorsque la clé correspond. Il se traduit naturellement par `INNER JOIN` sur les bases de données relationnelles. Alors que la jointure LINQ a des sélecteurs de clé externe et interne, la base de données requiert une seule condition de jointure. Ainsi EF Core génère une condition de jointure en comparant le sélecteur de clé externe au sélecteur de clé interne pour l’égalité. En outre, si les sélecteurs de clé sont des types anonymes, EF Core génère une condition de jointure pour comparer l’égalité des composants.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a>GroupJoin

L’opérateur LINQ GroupJoin vous permet de connecter deux sources de données similaires à Join, mais elle crée un groupe de valeurs internes pour les éléments externes correspondants. L’exécution d’une requête comme l’exemple suivant génère un résultat de `Blog` & `IEnumerable<Post>`. Étant donné que les bases de données (notamment les bases de données relationnelles) n’ont pas de moyen de représenter une collection d’objets côté client, GroupJoin ne se traduit pas par le serveur dans de nombreux cas. Elle nécessite que vous obteniez toutes les données du serveur pour effectuer la GroupJoin sans sélecteur spécial (première requête ci-dessous). Toutefois, si le sélecteur limite les données sélectionnées, l’extraction de toutes les données du serveur peut entraîner des problèmes de performances (deuxième requête ci-dessous). C’est la raison pour laquelle EF Core ne traduit pas GroupJoin.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a>SelectMany

L’opérateur SelectMany de LINQ vous permet d’énumérer un sélecteur de collection pour chaque élément externe et de générer des tuples de valeurs à partir de chaque source de données. En d’autres termes, il s’agit d’une jointure, mais sans aucune condition, chaque élément externe est connecté avec un élément de la source de la collection. Selon la façon dont le sélecteur de collection est lié à la source de données externe, SelectMany peut traduire en différentes requêtes différentes côté serveur.

### <a name="collection-selector-doesnt-reference-outer"></a>Le sélecteur de collection ne fait pas référence à l’extérieur

Lorsque le sélecteur de collection ne référence rien de la source externe, le résultat est un produit cartésien des deux sources de données. Il se traduit par `CROSS JOIN` dans les bases de données relationnelles.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a>Le sélecteur de collection fait référence à Outer dans une clause WHERE

Lorsque le sélecteur de collection a une clause WHERE, qui fait référence à l’élément externe, EF Core le convertit en une jointure de base de données et utilise le prédicat comme condition de jointure. Ce cas se produit généralement lors de l’utilisation de la navigation de collection sur l’élément externe comme sélecteur de collection. Si la collection est vide pour un élément externe, aucun résultat ne sera généré pour cet élément externe. Toutefois, si `DefaultIfEmpty` est appliqué au sélecteur de collection, l’élément externe est connecté avec une valeur par défaut de l’élément interne. En raison de cette distinction, ce type de requêtes se traduit par `INNER JOIN` en l’absence de `DefaultIfEmpty` et de `LEFT JOIN` quand `DefaultIfEmpty` est appliqué.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a>Le sélecteur de collection fait référence à l’extérieur dans un cas autre que Where

Lorsque le sélecteur de collection fait référence à l’élément externe, qui n’est pas dans une clause WHERE (comme le cas ci-dessus), il ne se convertit pas en jointure de base de données. C’est pourquoi nous devons évaluer le sélecteur de collection pour chaque élément externe. Il se traduit par `APPLY` opérations dans de nombreuses bases de données relationnelles. Si la collection est vide pour un élément externe, aucun résultat ne sera généré pour cet élément externe. Toutefois, si `DefaultIfEmpty` est appliqué au sélecteur de collection, l’élément externe est connecté avec une valeur par défaut de l’élément interne. En raison de cette distinction, ce type de requêtes se traduit par `CROSS APPLY` en l’absence de `DefaultIfEmpty` et de `OUTER APPLY` quand `DefaultIfEmpty` est appliqué. Certaines bases de données telles que SQLite ne prennent pas en charge les opérateurs `APPLY`, de sorte que ce type de requête peut ne pas être traduit.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a>GroupBy

Les opérateurs LINQ GroupBy créent un résultat de type `IGrouping<TKey, TElement>` où `TKey` et `TElement` peut être n’importe quel type arbitraire. En outre, `IGrouping` implémente `IEnumerable<TElement>`, ce qui signifie que vous pouvez le composer à l’aide d’un opérateur LINQ après le regroupement. Dans la mesure où aucune structure de base de données ne peut représenter un `IGrouping`, les opérateurs GroupBy n’ont pas de traduction dans la plupart des cas. Quand un opérateur d’agrégation est appliqué à chaque groupe, qui retourne une valeur scalaire, il peut être traduit en SQL `GROUP BY` dans des bases de données relationnelles. Le `GROUP BY` SQL est également restrictif. Elle vous oblige à regrouper uniquement par valeurs scalaires. La projection peut uniquement contenir des colonnes clés de regroupement ou n’importe quel agrégat appliqué à une colonne. EF Core identifie ce modèle et le convertit en serveur, comme dans l’exemple suivant :

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

EF Core convertit également les requêtes où un opérateur d’agrégation sur le regroupement s’affiche dans un opérateur LINQ WHERE ou OrderBy (ou autre classement). Elle utilise `HAVING` clause dans SQL pour la clause WHERE. La partie de la requête avant l’application de l’opérateur GroupBy peut être une requête complexe, à condition qu’elle puisse être traduite en serveur. En outre, une fois que vous appliquez des opérateurs d’agrégation sur une requête de regroupement pour supprimer des regroupements de la source résultante, vous pouvez composer en plus de celle-ci comme n’importe quelle autre requête.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

Les opérateurs d’agrégation EF Core prend en charge sont les suivants :

- Moyenne
- Nombre
- LongCount
- Max
- Min
- Sum

## <a name="left-join"></a>Jointure gauche

Alors que la jointure de gauche n’est pas un opérateur LINQ, les bases de données relationnelles ont le concept d’une jointure de gauche qui est fréquemment utilisée dans les requêtes. Un modèle particulier dans les requêtes LINQ donne le même résultat qu’une `LEFT JOIN` sur le serveur. EF Core identifie de tels modèles et génère l’équivalent `LEFT JOIN` côté serveur. Le modèle implique la création d’une GroupJoin entre les sources de données, puis l’aplatissement du regroupement à l’aide de l’opérateur SelectMany avec DefaultIfEmpty sur la source de regroupement pour correspondre à NULL lorsque le interne n’a pas d’élément associé. L’exemple suivant montre à quoi ressemble ce modèle et ce qu’il génère.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

Le modèle ci-dessus crée une structure complexe dans l’arborescence de l’expression. Pour cette raison, EF Core vous oblige à aplatir les résultats de regroupement de l’opérateur GroupJoin dans une étape qui suit immédiatement l’opérateur. Même si GroupJoin-DefaultIfEmpty-SelectMany est utilisé, mais dans un modèle différent, il est possible qu’il ne soit pas identifié comme une jointure de gauche.
