---
title: Types d’entité-EF Core
description: Comment configurer et mapper des types d’entité à l’aide de Entity Framework Core
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: ca8cb8560afe374218e763bc0476839187a40ece
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635768"
---
# <a name="entity-types"></a>Types d'entités

L’inclusion d’un DbSet d’un type dans votre contexte signifie qu’il est inclus dans le modèle de EF Core ; Nous faisons généralement référence à un type de ce type en tant qu' *entité*. EF Core pouvez lire et écrire des instances d’entité à partir de/vers la base de données, et si vous utilisez une base de données relationnelle, EF Core pouvez créer des tables pour vos entités via des migrations.

## <a name="including-types-in-the-model"></a>Inclusion de types dans le modèle

Par Convention, les types qui sont exposés dans les propriétés DbSet de votre contexte sont inclus dans le modèle en tant qu’entités. Les types d’entités qui sont spécifiés dans la `OnModelCreating` méthode sont également inclus, comme tous les types trouvés en explorant de manière récursive les propriétés de navigation d’autres types d’entités découverts.

Dans l’exemple de code ci-dessous, tous les types sont inclus :

* `Blog` est inclus, car il est exposé dans une propriété DbSet sur le contexte.
* `Post` est inclus, car il est découvert via la `Blog.Posts` propriété de navigation.
* `AuditEntry` parce qu’il est spécifié dans `OnModelCreating` .

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>Exclusion de types du modèle

Si vous ne souhaitez pas qu’un type soit inclus dans le modèle, vous pouvez l’exclure :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a>Exclusion des migrations

> [!NOTE]
> La possibilité d’exclure des tables des migrations a été introduite dans EF Core 5,0.

Il est parfois utile d’avoir le même type d’entité mappé dans plusieurs `DbContext` types. Cela est particulièrement vrai lorsque vous utilisez des [contextes délimités](https://www.martinfowler.com/bliki/BoundedContext.html), pour lesquels il est courant d’avoir un `DbContext` type différent pour chaque contexte délimité.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

Avec cette configuration, les migrations ne créent pas la `AspNetUsers` table, mais elles `IdentityUser` sont toujours incluses dans le modèle et peuvent être utilisées normalement.

Si vous devez commencer à gérer la table à l’aide des migrations, une nouvelle migration doit être créée, où `AspNetUsers` n’est pas exclu. La migration suivante contiendra désormais toutes les modifications apportées à la table.

## <a name="table-name"></a>Nom de la table

Par Convention, chaque type d’entité sera configuré pour être mappé à une table de base de données portant le même nom que la propriété DbSet qui expose l’entité. S’il n’existe aucun DbSet pour l’entité donnée, le nom de la classe est utilisé.

Vous pouvez configurer manuellement le nom de la table :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

**_

## <a name="table-schema"></a>Schéma de table

Lorsque vous utilisez une base de données relationnelle, les tables sont par convention créées dans le schéma par défaut de votre base de données. Par exemple, Microsoft SQL Server utilise le `dbo` schéma (SQLite ne prend pas en charge les schémas).

Vous pouvez configurer des tables à créer dans un schéma spécifique comme suit :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

_*_

Au lieu de spécifier le schéma pour chaque table, vous pouvez également définir le schéma par défaut au niveau du modèle à l’aide de l’API Fluent :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

Notez que la définition du schéma par défaut affectera également d’autres objets de base de données, tels que les séquences.

## <a name="view-mapping"></a>Afficher le mappage

Les types d’entités peuvent être mappés à des vues de base de données à l’aide de l’API Fluent.

> [!Note]
> EF suppose que la vue référencée existe déjà dans la base de données, elle ne la crée pas automatiquement dans une migration.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 Le mappage à une vue supprime le mappage de table par défaut, mais à partir d’EF 5,0, le type d’entité peut également être mappé à une table de manière explicite. Dans ce cas, le mappage de requête est utilisé pour les requêtes et le mappage de table est utilisé pour les mises à jour.

> [!TIP]
> Pour tester les types d’entités mappés à des vues à l’aide du fournisseur en mémoire, mappez-les à une requête via `ToInMemoryQuery` . Pour plus d’informations, consultez un [exemple exécutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) à l’aide de cette technique.

## <a name="table-valued-function-mapping"></a>Mappage de fonction table

Il est possible de mapper un type d’entité à une fonction table (TVF) au lieu d’une table dans la base de données. Pour illustrer cela, nous allons définir une autre entité qui représente le blog avec plusieurs publications. Dans l’exemple, l’entité est [sans clé](xref:core/modeling/keyless-entity-types), mais elle n’a pas besoin d’être.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#BlogWithMultiplePostsEntity)]

Ensuite, créez la fonction table suivante dans la base de données, qui retourne uniquement les blogs avec plusieurs publications, ainsi que le nombre de publications associées à chacun de ces blogs :

```sql
CREATE FUNCTION dbo.BlogsWithMultiplePosts()
RETURNS TABLE
AS
RETURN
(
    SELECT b.Url, COUNT(p.BlogId) AS PostCount
    FROM Blogs AS b
    JOIN Posts AS p ON b.BlogId = p.BlogId
    GROUP BY b.BlogId, b.Url
    HAVING COUNT(p.BlogId) > 1
)
```

À présent, l’entité `BlogWithMultiplePost` peut être mappée à cette fonction de la façon suivante :

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#QueryableFunctionConfigurationToFunction)]

> [!NOTE]
> Pour mapper une entité à une fonction table, la fonction doit être sans paramètre.

Conventionnellement, les propriétés d’entité sont mappées aux colonnes correspondantes retournées par la fonction TVF. Si les colonnes retournées par TVF ont un nom différent de la propriété d’entité, elle peut être configurée à l’aide de la `HasColumnName` méthode, comme lors du mappage à une table normale.

Lorsque le type d’entité est mappé à une fonction table, la requête :

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Program.cs#ToFunctionQuery)]

Génère l’instruction SQL suivante :

```sql
SELECT [b].[Url], [b].[PostCount]
FROM [dbo].[BlogsWithMultiplePosts]() AS [b]
WHERE [b].[PostCount] > 3
```

## <a name="table-comments"></a>Commentaires de table

Vous pouvez définir un commentaire de texte arbitraire qui est défini sur la table de base de données, ce qui vous permet de documenter votre schéma dans la base de données :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

> [!NOTE]
> La définition de commentaires via des annotations de données a été introduite dans EF Core 5,0.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableComment.cs?name=TableComment&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableComment.cs?name=TableComment&highlight=4)]

_**
