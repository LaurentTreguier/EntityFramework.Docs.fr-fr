---
title: Classements et respect de la casse-EF Core
description: Comment configurer les classements et le respect de la casse dans la base de données et les requêtes
author: roji
ms.date: 04/27/2020
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/collations-and-case-sensitivity
ms.openlocfilehash: b3874847922cb39aa57d50813e6e50ff7db72eb9
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370563"
---
# <a name="collations-and-case-sensitivity"></a>Classements et respect de la casse

> [!NOTE]
> Cette fonctionnalité est introduite dans EF Core 5,0.

Le traitement de texte dans les bases de données peut être complexe et nécessite plus d’attention à l’utilisateur. Pour une chose, les bases de données varient considérablement dans la façon dont elles gèrent le texte. par exemple, bien que certaines bases de données respectent la casse par défaut (par exemple, SQLite, PostgreSQL), d’autres ne sont pas sensibles à la casse (SQL Server, MySQL). En outre, en raison de l’utilisation de l’index, le respect de la casse et les aspects similaires peuvent avoir un impact important sur les performances des requêtes : s’il peut être tentant d’utiliser pour forcer une comparaison ne respectant pas la casse `string.Lower` dans une base de données qui respecte la casse, cela peut empêcher votre application d’utiliser des index. Cette page explique comment configurer le respect de la casse, ou plus généralement, les classements et comment le faire de manière efficace sans compromettre les performances des requêtes.

## <a name="introduction-to-collations"></a>Présentation des classements

Un concept fondamental du traitement du texte est le *classement*, qui est un ensemble de règles déterminant comment les valeurs de texte sont classées et comparées à des fins d’égalité. Par exemple, bien qu’un classement ne respectant pas la casse ignore les différences entre les lettres majuscules et minuscules dans le cadre de la comparaison d’égalité, le classement qui respecte la casse ne le fait pas. Toutefois, étant donné que le respect de la casse est dépendant de la culture (par exemple, `i` et `I` représente une lettre différente en turc), il existe plusieurs classements qui ne respectent pas la casse, chacun avec son propre ensemble de règles. L’étendue des classements s’étend également au-delà du respect de la casse, aux autres aspects des données de caractères. en allemand, par exemple, il est parfois (mais pas toujours) souhaitable de traiter `ä` et `ae` comme identiques. Enfin, les classements définissent également le mode de *Tri*des valeurs de texte : en Allemand `ä` `a` , le place à la fin de l’alphabet.

Toutes les opérations de texte dans une base de données utilisent un classement, qu’il s’agisse de manière explicite ou implicite, pour déterminer comment l’opération compare et trie les chaînes. La liste réelle des classements disponibles et leurs schémas de nommage sont spécifiques à la base de données. consultez [la section ci-dessous](#database-specific-information) pour obtenir des liens vers des pages de documentation pertinentes de différentes bases de données. Heureusement, la base de données permet généralement de définir un classement par défaut au niveau de la base de données ou de la colonne, et de spécifier explicitement le classement à utiliser pour des opérations spécifiques dans une requête.

## <a name="database-collation"></a>Classement de base de données

Dans la plupart des systèmes de base de données, un classement par défaut est défini au niveau de la base de données. à moins d’être remplacé, ce classement s’applique implicitement à toutes les opérations de texte qui se produisent dans cette base de données. Le classement de base de données est généralement défini au moment de la création de la base de données (par le biais de l' `CREATE DATABASE` instruction DDL) et, s’il n’est pas spécifié, une valeur de niveau serveur est déterminée par défaut au moment de l’installation. Par exemple, le classement par défaut au niveau du serveur dans SQL Server est `SQL_Latin1_General_CP1_CI_AS` , qui est un classement qui ne respecte pas la casse et qui respecte les accents. Bien que les systèmes de base de données autorisent généralement la modification du classement d’une base de données existante, cela peut entraîner des complications. Il est recommandé de choisir un classement avant la création de la base de données.

Lors de l’utilisation de EF Core migrations pour gérer votre schéma de base de données, le code suivant dans la méthode de votre modèle `OnModelCreating` configure une base de données SQL Server pour utiliser un classement qui respecte la casse :

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=40)]

## <a name="column-collation"></a>Classement par colonnes

Les classements peuvent également être définis sur des colonnes de texte, remplaçant la valeur par défaut de la base de données. Cela peut être utile si certaines colonnes ne doivent pas être sensibles à la casse, tandis que le reste de la base de données doit respecter la casse.

Lors de l’utilisation de EF Core migrations pour gérer votre schéma de base de données, le code suivant configure la colonne pour `Name` que la propriété ne respecte pas la casse dans une base de données configurée pour respecter la casse :

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=OnModelCreating&highlight=6)]

## <a name="explicit-collation-in-a-query"></a>Classement explicite dans une requête

Dans certains cas, la même colonne doit être interrogée à l’aide de différents classements par différentes requêtes. Par exemple, une requête peut être amenée à effectuer une comparaison respectant la casse sur une colonne, alors qu’une autre peut avoir besoin d’effectuer une comparaison qui ne respecte pas la casse sur la même colonne. Cela peut être accompli en spécifiant explicitement un classement dans la requête elle-même :

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=SimpleQueryCollation)]

Cela génère une `COLLATE` clause dans la requête SQL, qui applique un classement qui respecte la casse, quel que soit le classement défini au niveau de la colonne ou de la base de données :

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE [c].[Name] COLLATE SQL_Latin1_General_CP1_CS_AS = N'John'
```

### <a name="explicit-collations-and-indexes"></a>Classements et index explicites

Les index sont l’un des facteurs les plus importants en matière de performances de base de données : une requête qui s’exécute efficacement avec un index peut se broyer à une interruption sans cet index. Les index héritent implicitement du classement de leur colonne ; Cela signifie que toutes les requêtes sur la colonne sont automatiquement éligibles à l’utilisation d’index définis sur cette colonne, à condition que la requête ne spécifie pas un classement différent. La spécification d’un classement explicite dans une requête empêche généralement cette requête d’utiliser un index défini sur cette colonne, car les classements ne correspondent plus. par conséquent, il est recommandé de faire preuve de prudence lors de l’utilisation de cette fonctionnalité. Il est toujours préférable de définir le classement au niveau de la colonne (ou de la base de données), ce qui permet à toutes les requêtes d’utiliser implicitement ce classement et de tirer parti de n’importe quel index.

Notez que certaines bases de données permettent de définir le classement lors de la création d’un index (par exemple, PostgreSQL, SQLite). Cela permet de définir plusieurs index sur la même colonne, accélérant ainsi les opérations avec des classements différents (par exemple, les comparaisons sensibles à la casse et les comparaisons ne respectant pas la casse). Pour plus d’informations, consultez la documentation de votre fournisseur de base de données.

> [!WARNING]
> Inspectez toujours les plans de requête de vos requêtes et assurez-vous que les index appropriés sont utilisés dans les requêtes critiques pour les performances qui s’exécutent sur de grandes quantités de données. Le respect de la casse dans une requête via `EF.Functions.Collate` (ou en appelant `string.ToLower` ) peut avoir un impact très important sur les performances de votre application.

## <a name="translation-of-built-in-net-string-operations"></a>Traduction d’opérations de chaînes .NET intégrées

Dans .NET, l’égalité des chaînes respecte la casse par défaut : `s1 == s2` effectue une comparaison ordinale qui requiert que les chaînes soient identiques. Étant donné que le classement par défaut des bases de données varie et parce qu’il est souhaitable que l’égalité simple utilise des index, EF Core n’effectue aucune tentative de conversion de l’égalité simple en une opération de base de données qui respecte la casse : l’égalité C# est traduite directement en égalité SQL, qui peut ou non respecter la casse, en fonction de la base de données en cours d’utilisation et

En outre, .NET fournit des surcharges de [`string.Equals`](https://docs.microsoft.com/dotnet/api/system.string.equals#System_String_Equals_System_String_System_StringComparison_) l’acceptation d’un [`StringComparison`](https://docs.microsoft.com/dotnet/api/system.stringcomparison) enum, ce qui permet de spécifier le respect de la casse et la culture pour la comparaison. Par défaut, EF Core s’abstient de traduire ces surcharges en SQL, et la tentative de les utiliser entraînera une exception. Pour une chose, EF Core ne sait pas quel classement sensible à la casse ou non sensible à la casse doit être utilisé. Plus important encore, l’application d’un classement dans la plupart des cas empêche l’utilisation de l’index, ce qui a un impact significatif sur les performances d’une construction .NET très basique et couramment utilisée. Pour forcer une requête à utiliser une comparaison qui respecte la casse ou qui ne respecte pas la casse, spécifiez explicitement le classement via `EF.Functions.Collate` comme [détaillé ci-dessus](#explicit-collations-and-indexes).

## <a name="database-specific-information"></a>Informations spécifiques à la base de données

* [SQL Server de la documentation sur les classements](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support).
* [Documentation de Microsoft. Data. sqlite sur les classements](https://docs.microsoft.com/dotnet/standard/data/sqlite/collation).
* [Documentation PostgreSQL sur les classements](https://www.postgresql.org/docs/current/collation.html).
* [Documentation MySQL sur les classements](https://dev.mysql.com/doc/refman/en/charset-general.html).
