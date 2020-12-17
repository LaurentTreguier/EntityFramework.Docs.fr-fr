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
# <a name="entity-types"></a><span data-ttu-id="a7e86-103">Types d'entités</span><span class="sxs-lookup"><span data-stu-id="a7e86-103">Entity Types</span></span>

<span data-ttu-id="a7e86-104">L’inclusion d’un DbSet d’un type dans votre contexte signifie qu’il est inclus dans le modèle de EF Core ; Nous faisons généralement référence à un type de ce type en tant qu' *entité*.</span><span class="sxs-lookup"><span data-stu-id="a7e86-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="a7e86-105">EF Core pouvez lire et écrire des instances d’entité à partir de/vers la base de données, et si vous utilisez une base de données relationnelle, EF Core pouvez créer des tables pour vos entités via des migrations.</span><span class="sxs-lookup"><span data-stu-id="a7e86-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="a7e86-106">Inclusion de types dans le modèle</span><span class="sxs-lookup"><span data-stu-id="a7e86-106">Including types in the model</span></span>

<span data-ttu-id="a7e86-107">Par Convention, les types qui sont exposés dans les propriétés DbSet de votre contexte sont inclus dans le modèle en tant qu’entités.</span><span class="sxs-lookup"><span data-stu-id="a7e86-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="a7e86-108">Les types d’entités qui sont spécifiés dans la `OnModelCreating` méthode sont également inclus, comme tous les types trouvés en explorant de manière récursive les propriétés de navigation d’autres types d’entités découverts.</span><span class="sxs-lookup"><span data-stu-id="a7e86-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="a7e86-109">Dans l’exemple de code ci-dessous, tous les types sont inclus :</span><span class="sxs-lookup"><span data-stu-id="a7e86-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="a7e86-110">`Blog` est inclus, car il est exposé dans une propriété DbSet sur le contexte.</span><span class="sxs-lookup"><span data-stu-id="a7e86-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="a7e86-111">`Post` est inclus, car il est découvert via la `Blog.Posts` propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="a7e86-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="a7e86-112">`AuditEntry` parce qu’il est spécifié dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="a7e86-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="a7e86-113">Exclusion de types du modèle</span><span class="sxs-lookup"><span data-stu-id="a7e86-113">Excluding types from the model</span></span>

<span data-ttu-id="a7e86-114">Si vous ne souhaitez pas qu’un type soit inclus dans le modèle, vous pouvez l’exclure :</span><span class="sxs-lookup"><span data-stu-id="a7e86-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a7e86-115">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="a7e86-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="a7e86-116">API Fluent</span><span class="sxs-lookup"><span data-stu-id="a7e86-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a><span data-ttu-id="a7e86-117">Exclusion des migrations</span><span class="sxs-lookup"><span data-stu-id="a7e86-117">Excluding from migrations</span></span>

> [!NOTE]
> <span data-ttu-id="a7e86-118">La possibilité d’exclure des tables des migrations a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="a7e86-118">The ability to exclude tables from migrations was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="a7e86-119">Il est parfois utile d’avoir le même type d’entité mappé dans plusieurs `DbContext` types.</span><span class="sxs-lookup"><span data-stu-id="a7e86-119">It is sometimes useful to have the same entity type mapped in multiple `DbContext` types.</span></span> <span data-ttu-id="a7e86-120">Cela est particulièrement vrai lorsque vous utilisez des [contextes délimités](https://www.martinfowler.com/bliki/BoundedContext.html), pour lesquels il est courant d’avoir un `DbContext` type différent pour chaque contexte délimité.</span><span class="sxs-lookup"><span data-stu-id="a7e86-120">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different `DbContext` type for each bounded context.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

<span data-ttu-id="a7e86-121">Avec cette configuration, les migrations ne créent pas la `AspNetUsers` table, mais elles `IdentityUser` sont toujours incluses dans le modèle et peuvent être utilisées normalement.</span><span class="sxs-lookup"><span data-stu-id="a7e86-121">With this configuration migrations will not create the `AspNetUsers` table, but `IdentityUser` is still included in the model and can be used normally.</span></span>

<span data-ttu-id="a7e86-122">Si vous devez commencer à gérer la table à l’aide des migrations, une nouvelle migration doit être créée, où `AspNetUsers` n’est pas exclu.</span><span class="sxs-lookup"><span data-stu-id="a7e86-122">If you need to start managing the table using migrations again then a new migration should be created where `AspNetUsers` is not excluded.</span></span> <span data-ttu-id="a7e86-123">La migration suivante contiendra désormais toutes les modifications apportées à la table.</span><span class="sxs-lookup"><span data-stu-id="a7e86-123">The next migration will now contain any changes made to the table.</span></span>

## <a name="table-name"></a><span data-ttu-id="a7e86-124">Nom de la table</span><span class="sxs-lookup"><span data-stu-id="a7e86-124">Table name</span></span>

<span data-ttu-id="a7e86-125">Par Convention, chaque type d’entité sera configuré pour être mappé à une table de base de données portant le même nom que la propriété DbSet qui expose l’entité.</span><span class="sxs-lookup"><span data-stu-id="a7e86-125">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="a7e86-126">S’il n’existe aucun DbSet pour l’entité donnée, le nom de la classe est utilisé.</span><span class="sxs-lookup"><span data-stu-id="a7e86-126">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="a7e86-127">Vous pouvez configurer manuellement le nom de la table :</span><span class="sxs-lookup"><span data-stu-id="a7e86-127">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a7e86-128">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="a7e86-128">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="a7e86-129">API Fluent</span><span class="sxs-lookup"><span data-stu-id="a7e86-129">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

<span data-ttu-id="a7e86-130">\*\*_</span><span class="sxs-lookup"><span data-stu-id="a7e86-130">\*\*_</span></span>

## <a name="table-schema"></a><span data-ttu-id="a7e86-131">Schéma de table</span><span class="sxs-lookup"><span data-stu-id="a7e86-131">Table schema</span></span>

<span data-ttu-id="a7e86-132">Lorsque vous utilisez une base de données relationnelle, les tables sont par convention créées dans le schéma par défaut de votre base de données.</span><span class="sxs-lookup"><span data-stu-id="a7e86-132">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="a7e86-133">Par exemple, Microsoft SQL Server utilise le `dbo` schéma (SQLite ne prend pas en charge les schémas).</span><span class="sxs-lookup"><span data-stu-id="a7e86-133">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="a7e86-134">Vous pouvez configurer des tables à créer dans un schéma spécifique comme suit :</span><span class="sxs-lookup"><span data-stu-id="a7e86-134">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a7e86-135">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="a7e86-135">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="a7e86-136">API Fluent</span><span class="sxs-lookup"><span data-stu-id="a7e86-136">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

_*_

<span data-ttu-id="a7e86-137">Au lieu de spécifier le schéma pour chaque table, vous pouvez également définir le schéma par défaut au niveau du modèle à l’aide de l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="a7e86-137">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="a7e86-138">Notez que la définition du schéma par défaut affectera également d’autres objets de base de données, tels que les séquences.</span><span class="sxs-lookup"><span data-stu-id="a7e86-138">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>

## <a name="view-mapping"></a><span data-ttu-id="a7e86-139">Afficher le mappage</span><span class="sxs-lookup"><span data-stu-id="a7e86-139">View mapping</span></span>

<span data-ttu-id="a7e86-140">Les types d’entités peuvent être mappés à des vues de base de données à l’aide de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="a7e86-140">Entity types can be mapped to database views using the Fluent API.</span></span>

> [!Note]
> <span data-ttu-id="a7e86-141">EF suppose que la vue référencée existe déjà dans la base de données, elle ne la crée pas automatiquement dans une migration.</span><span class="sxs-lookup"><span data-stu-id="a7e86-141">EF will assume that the referenced view already exists in the database, it will not create it automatically in a migration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 <span data-ttu-id="a7e86-142">Le mappage à une vue supprime le mappage de table par défaut, mais à partir d’EF 5,0, le type d’entité peut également être mappé à une table de manière explicite.</span><span class="sxs-lookup"><span data-stu-id="a7e86-142">Mapping to a view will remove the default table mapping, but starting with EF 5.0 the entity type can also be mapped to a table explicitly.</span></span> <span data-ttu-id="a7e86-143">Dans ce cas, le mappage de requête est utilisé pour les requêtes et le mappage de table est utilisé pour les mises à jour.</span><span class="sxs-lookup"><span data-stu-id="a7e86-143">In this case the query mapping will be used for queries and the table mapping will be used for updates.</span></span>

> [!TIP]
> <span data-ttu-id="a7e86-144">Pour tester les types d’entités mappés à des vues à l’aide du fournisseur en mémoire, mappez-les à une requête via `ToInMemoryQuery` .</span><span class="sxs-lookup"><span data-stu-id="a7e86-144">To test entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="a7e86-145">Pour plus d’informations, consultez un [exemple exécutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) à l’aide de cette technique.</span><span class="sxs-lookup"><span data-stu-id="a7e86-145">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>

## <a name="table-valued-function-mapping"></a><span data-ttu-id="a7e86-146">Mappage de fonction table</span><span class="sxs-lookup"><span data-stu-id="a7e86-146">Table-valued function mapping</span></span>

<span data-ttu-id="a7e86-147">Il est possible de mapper un type d’entité à une fonction table (TVF) au lieu d’une table dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="a7e86-147">It's possible to map an entity type to a table-valued function (TVF) instead of a table in the database.</span></span> <span data-ttu-id="a7e86-148">Pour illustrer cela, nous allons définir une autre entité qui représente le blog avec plusieurs publications.</span><span class="sxs-lookup"><span data-stu-id="a7e86-148">To illustrate this, let's define another entity that represents blog with multiple posts.</span></span> <span data-ttu-id="a7e86-149">Dans l’exemple, l’entité est [sans clé](xref:core/modeling/keyless-entity-types), mais elle n’a pas besoin d’être.</span><span class="sxs-lookup"><span data-stu-id="a7e86-149">In the example, the entity is [keyless](xref:core/modeling/keyless-entity-types), but it doesn't have to be.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#BlogWithMultiplePostsEntity)]

<span data-ttu-id="a7e86-150">Ensuite, créez la fonction table suivante dans la base de données, qui retourne uniquement les blogs avec plusieurs publications, ainsi que le nombre de publications associées à chacun de ces blogs :</span><span class="sxs-lookup"><span data-stu-id="a7e86-150">Next, create the following table-valued function in the database, which returns only blogs with multiple posts as well as the number of posts associated with each of these blogs:</span></span>

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

<span data-ttu-id="a7e86-151">À présent, l’entité `BlogWithMultiplePost` peut être mappée à cette fonction de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="a7e86-151">Now, the entity `BlogWithMultiplePost` can be mapped to this function in a following way:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#QueryableFunctionConfigurationToFunction)]

> [!NOTE]
> <span data-ttu-id="a7e86-152">Pour mapper une entité à une fonction table, la fonction doit être sans paramètre.</span><span class="sxs-lookup"><span data-stu-id="a7e86-152">In order to map an entity to a table-valued function the function must be parameterless.</span></span>

<span data-ttu-id="a7e86-153">Conventionnellement, les propriétés d’entité sont mappées aux colonnes correspondantes retournées par la fonction TVF.</span><span class="sxs-lookup"><span data-stu-id="a7e86-153">Conventionally the entity properties will be mapped to matching columns returned by the TVF.</span></span> <span data-ttu-id="a7e86-154">Si les colonnes retournées par TVF ont un nom différent de la propriété d’entité, elle peut être configurée à l’aide de la `HasColumnName` méthode, comme lors du mappage à une table normale.</span><span class="sxs-lookup"><span data-stu-id="a7e86-154">If the columns returned by TVF has different name than entity property then it can be configured using `HasColumnName` method, just like when mapping to a regular table.</span></span>

<span data-ttu-id="a7e86-155">Lorsque le type d’entité est mappé à une fonction table, la requête :</span><span class="sxs-lookup"><span data-stu-id="a7e86-155">When the entity type is mapped to a table-valued function, the query:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Program.cs#ToFunctionQuery)]

<span data-ttu-id="a7e86-156">Génère l’instruction SQL suivante :</span><span class="sxs-lookup"><span data-stu-id="a7e86-156">Produces the following SQL:</span></span>

```sql
SELECT [b].[Url], [b].[PostCount]
FROM [dbo].[BlogsWithMultiplePosts]() AS [b]
WHERE [b].[PostCount] > 3
```

## <a name="table-comments"></a><span data-ttu-id="a7e86-157">Commentaires de table</span><span class="sxs-lookup"><span data-stu-id="a7e86-157">Table comments</span></span>

<span data-ttu-id="a7e86-158">Vous pouvez définir un commentaire de texte arbitraire qui est défini sur la table de base de données, ce qui vous permet de documenter votre schéma dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="a7e86-158">You can set an arbitrary text comment that gets set on the database table, allowing you to document your schema in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a7e86-159">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="a7e86-159">Data Annotations</span></span>](#tab/data-annotations)

> [!NOTE]
> <span data-ttu-id="a7e86-160">La définition de commentaires via des annotations de données a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="a7e86-160">Setting comments via data annotations was introduced in EF Core 5.0.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableComment.cs?name=TableComment&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="a7e86-161">API Fluent</span><span class="sxs-lookup"><span data-stu-id="a7e86-161">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableComment.cs?name=TableComment&highlight=4)]

<span data-ttu-id="a7e86-162">_\*\*</span><span class="sxs-lookup"><span data-stu-id="a7e86-162">_\*\*</span></span>
