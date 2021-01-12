---
title: 'Suppression en cascade : EF Core'
description: Configuration des comportements en cascade déclenchés lorsqu’une entité est supprimée ou endommagée de son principal/parent
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 7c35de900930cf42da0e534df76124b5fb19ca52
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128860"
---
# <a name="cascade-delete"></a><span data-ttu-id="9c4e9-103">Suppression en cascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-103">Cascade Delete</span></span>

<span data-ttu-id="9c4e9-104">Entity Framework Core (EF Core) représente des relations à l’aide de clés étrangères.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-104">Entity Framework Core (EF Core) represents relationships using foreign keys.</span></span> <span data-ttu-id="9c4e9-105">Une entité avec une clé étrangère est l’entité enfant ou dépendante dans la relation.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-105">An entity with a foreign key is the child or dependent entity in the relationship.</span></span> <span data-ttu-id="9c4e9-106">La valeur de clé étrangère de cette entité doit correspondre à la valeur de clé primaire (ou à une autre valeur de clé) de l’entité principale/parente associée.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-106">This entity's foreign key value must match the primary key value (or an alternate key value) of the related principal/parent entity.</span></span>

<span data-ttu-id="9c4e9-107">Si l’entité principale/parente est supprimée, les valeurs de clé étrangère des objets dépendants/enfants ne correspondent plus à la clé primaire ou secondaire d' _un_ principal/parent.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-107">If the principal/parent entity is deleted, then the foreign key values of the dependents/children will no longer match the primary or alternate key of _any_ principal/parent.</span></span> <span data-ttu-id="9c4e9-108">Il s’agit d’un État non valide et entraîne une violation de contrainte référentielle dans la plupart des bases de données.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-108">This is an invalid state, and will cause a referential constraint violation in most databases.</span></span>

<span data-ttu-id="9c4e9-109">Il existe deux options pour éviter cette violation de contrainte référentielle :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-109">There are two options to avoid this referential constraint violation:</span></span>

1. <span data-ttu-id="9c4e9-110">Définir les valeurs FK sur null</span><span class="sxs-lookup"><span data-stu-id="9c4e9-110">Set the FK values to null</span></span>
2. <span data-ttu-id="9c4e9-111">Supprimer également les entités dépendantes/enfants</span><span class="sxs-lookup"><span data-stu-id="9c4e9-111">Also delete the dependent/child entities</span></span>

<span data-ttu-id="9c4e9-112">La première option dans valide uniquement pour les relations facultatives où la propriété de clé étrangère (et la colonne de base de données à laquelle elle est mappée) doit être Nullable.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-112">The first option in only valid for optional relationships where the foreign key property (and the database column to which it is mapped) must be nullable.</span></span>

<span data-ttu-id="9c4e9-113">La deuxième option est valide pour tout type de relation et est connue sous le nom de « suppression en cascade ».</span><span class="sxs-lookup"><span data-stu-id="9c4e9-113">The second option is valid for any kind of relationship and is known as "cascade delete".</span></span>

> [!TIP]
> <span data-ttu-id="9c4e9-114">Ce document décrit les suppressions en cascade (et la suppression des orphelins) du point de vue de la mise à jour de la base de données.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-114">This document describes cascade deletes (and deleting orphans) from the perspective of updating the database.</span></span> <span data-ttu-id="9c4e9-115">Cela permet d’utiliser de façon intensive les concepts introduits dans [change Tracking dans EF Core](xref:core/change-tracking/index) et de [modifier les clés étrangères et les navigations](xref:core/change-tracking/relationship-changes).</span><span class="sxs-lookup"><span data-stu-id="9c4e9-115">It makes heavy use of concepts introduced in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="9c4e9-116">Veillez à bien comprendre ces concepts avant de traiter le matériel ici.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-116">Make sure to fully understand these concepts before tackling the material here.</span></span>

> [!TIP]  
> <span data-ttu-id="9c4e9-117">Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes).</span><span class="sxs-lookup"><span data-stu-id="9c4e9-117">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes).</span></span>

## <a name="when-cascading-behaviors-happen"></a><span data-ttu-id="9c4e9-118">Quand des comportements en cascade se produisent</span><span class="sxs-lookup"><span data-stu-id="9c4e9-118">When cascading behaviors happen</span></span>

<span data-ttu-id="9c4e9-119">Des suppressions en cascade sont nécessaires lorsqu’une entité dépendante/enfant ne peut plus être associée à son principal/parent actuel.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-119">Cascading deletes are needed when a dependent/child entity can no longer be associated with its current principal/parent.</span></span> <span data-ttu-id="9c4e9-120">Cela peut se produire en raison de la suppression du principal/parent, ou lorsque le principal/parent existe toujours, mais que le dépendant/enfant n’est plus associé.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-120">This can happen because the principal/parent is deleted, or it can happen when the principal/parent still exists but the dependent/child is no longer associated with it.</span></span>

### <a name="deleting-a-principalparent"></a><span data-ttu-id="9c4e9-121">Suppression d’un principal/parent</span><span class="sxs-lookup"><span data-stu-id="9c4e9-121">Deleting a principal/parent</span></span>

<span data-ttu-id="9c4e9-122">Considérons ce modèle simple `Blog` , où est le principal/le parent dans une relation avec `Post` , qui est le dépendant/enfant.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-122">Consider this simple model where `Blog` is the principal/parent in a relationship with `Post`, which is the dependent/child.</span></span> <span data-ttu-id="9c4e9-123">`Post.BlogId` est une propriété de clé étrangère, dont la valeur doit correspondre `Post.Id` à la clé primaire de la publication à laquelle le blog appartient.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-123">`Post.BlogId` is a foreign key property, the value of which must match the `Post.Id` primary key of the post to which the blog belongs.</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

<span data-ttu-id="9c4e9-124">Par Convention, cette relation est configurée en tant que obligatoire, car la `Post.BlogId` propriété de clé étrangère n’accepte pas les valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-124">By convention, this relationship is configured as a required, since the `Post.BlogId` foreign key property is non-nullable.</span></span> <span data-ttu-id="9c4e9-125">Les relations requises sont configurées pour utiliser des suppressions en cascade par défaut.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-125">Required relationships are configured to use cascade deletes by default.</span></span> <span data-ttu-id="9c4e9-126">Pour plus d’informations sur les relations de modélisation, consultez [relations](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-126">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships.</span></span>

<span data-ttu-id="9c4e9-127">Lors de la suppression d’un blog, toutes les publications sont supprimées en cascade.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-127">When deleting a blog, all posts are cascade deleted.</span></span> <span data-ttu-id="9c4e9-128">Exemple :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-128">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

<span data-ttu-id="9c4e9-129">SaveChanges génère le code SQL suivant, à l’aide de SQL Server comme exemple :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-129">SaveChanges generates the following SQL, using SQL Server as an example:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a><span data-ttu-id="9c4e9-130">Séparation d’une relation</span><span class="sxs-lookup"><span data-stu-id="9c4e9-130">Severing a relationship</span></span>

<span data-ttu-id="9c4e9-131">Au lieu de supprimer le blog, nous pourrions à présent rompre la relation entre chaque publication et son blog.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-131">Rather than deleting the blog, we could instead sever the relationship between each post and its blog.</span></span> <span data-ttu-id="9c4e9-132">Pour ce faire, vous pouvez définir la navigation `Post.Blog` de référence sur null pour chaque publication :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-132">This can be done by setting the reference navigation `Post.Blog` to null for each post:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

<span data-ttu-id="9c4e9-133">La relation peut également être interrompue en supprimant chaque publication du `Blog.Posts` volet de navigation de la collection :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-133">The relationship can also be severed by removing each post from the `Blog.Posts` collection navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

<span data-ttu-id="9c4e9-134">Dans les deux cas, le résultat est le même : le blog n’est pas supprimé, mais les publications qui ne sont plus associées à un blog sont supprimées :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-134">In either case the result is the same: the blog is not deleted, but the posts that are no longer associated with any blog are deleted:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="9c4e9-135">La suppression d’entités qui ne sont plus associées à un principal/dépendant est connue sous le nom de « suppression des orphelins ».</span><span class="sxs-lookup"><span data-stu-id="9c4e9-135">Deleting entities that are no longer associated with any principal/dependent is known as "deleting orphans".</span></span>

> [!TIP]
> <span data-ttu-id="9c4e9-136">La suppression en cascade et la suppression des orphelins sont étroitement liées.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-136">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="9c4e9-137">Les deux entraînent la suppression d’entités dépendantes/enfants lorsque la relation avec le principal/parent requis est interrompue.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-137">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="9c4e9-138">Pour la suppression en cascade, ce problème se produit car le principal/le parent est lui-même supprimé.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-138">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="9c4e9-139">Pour les orphelins, l’entité principale/parente existe toujours, mais n’est plus liée aux entités dépendantes/enfants.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-139">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>  

## <a name="where-cascading-behaviors-happen"></a><span data-ttu-id="9c4e9-140">Où se produisent les comportements en cascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-140">Where cascading behaviors happen</span></span>

<span data-ttu-id="9c4e9-141">Les comportements en cascade peuvent être appliqués aux éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-141">Cascading behaviors can be applied to:</span></span>

- <span data-ttu-id="9c4e9-142">Entités suivies par le actuel <xref:Microsoft.EntityFrameworkCore.DbContext></span><span class="sxs-lookup"><span data-stu-id="9c4e9-142">Entities tracked by the current <xref:Microsoft.EntityFrameworkCore.DbContext></span></span>
- <span data-ttu-id="9c4e9-143">Entités de la base de données qui n’ont pas été chargées dans le contexte</span><span class="sxs-lookup"><span data-stu-id="9c4e9-143">Entities in the database that have not been loaded into the context</span></span>

### <a name="cascade-delete-of-tracked-entities"></a><span data-ttu-id="9c4e9-144">Suppression en cascade des entités suivies</span><span class="sxs-lookup"><span data-stu-id="9c4e9-144">Cascade delete of tracked entities</span></span>

<span data-ttu-id="9c4e9-145">EF Core applique toujours les comportements en cascade configurés aux entités suivies.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-145">EF Core always applies configured cascading behaviors to tracked entities.</span></span> <span data-ttu-id="9c4e9-146">Cela signifie que si l’application charge toutes les entités dépendantes/enfants pertinentes dans DbContext, comme indiqué dans les exemples ci-dessus, les comportements en cascade sont correctement appliqués, quelle que soit la façon dont la base de données est configurée.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-146">This means that if the application loads all relevant dependent/child entities into the DbContext, as is shown in the examples above, then cascading behaviors will be correctly applied regardless of how the database is configured.</span></span>

> [!TIP]
> <span data-ttu-id="9c4e9-147">Le minutage exact du moment où les comportements en cascade se produisent pour les entités suivies peut être contrôlé à l’aide <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> de et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-147">The exact timing of when cascading behaviors happen to tracked entities can be controlled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType>.</span></span> <span data-ttu-id="9c4e9-148">Pour plus d’informations [, consultez Modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-148">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

### <a name="cascade-delete-in-the-database"></a><span data-ttu-id="9c4e9-149">Suppression en cascade dans la base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-149">Cascade delete in the database</span></span>

<span data-ttu-id="9c4e9-150">De nombreux systèmes de base de données proposent également des comportements en cascade qui sont déclenchés lorsqu’une entité est supprimée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-150">Many database systems also offer cascading behaviors that are triggered when an entity is deleted in the database.</span></span> <span data-ttu-id="9c4e9-151">EF Core configure ces comportements en fonction du comportement de suppression en cascade dans le modèle EF Core quand une base de données est créée à l’aide de <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> ou EF Core migrations.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-151">EF Core configures these behaviors based on the cascade delete behavior in the EF Core model when a database is created using <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> or EF Core migrations.</span></span> <span data-ttu-id="9c4e9-152">Par exemple, à l’aide du modèle ci-dessus, le tableau suivant est créé pour les publications quand vous utilisez SQL Server :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-152">For example, using the model above, the following table is created for posts when using SQL Server:</span></span>

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

<span data-ttu-id="9c4e9-153">Notez que la contrainte de clé étrangère définissant la relation entre les blogs et les publications est configurée avec `ON DELETE CASCADE` .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-153">Notice that the foreign key constraint defining the relationship between blogs and posts is configured with `ON DELETE CASCADE`.</span></span>

<span data-ttu-id="9c4e9-154">Si nous savons que la base de données est configurée de la même manière, nous pouvons supprimer un blog _sans charger au préalable les publications_ et la base de données s’occupera de la suppression de toutes les publications associées à ce blog.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-154">If we know that the database is configured like this, then we can delete a blog _without first loading posts_ and the database will take care of deleting all the posts that were related to that blog.</span></span> <span data-ttu-id="9c4e9-155">Exemple :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-155">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

<span data-ttu-id="9c4e9-156">Notez qu’il n’existe aucune `Include` pour les publications, donc elles ne sont pas chargées.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-156">Notice that there is no `Include` for posts, so they are not loaded.</span></span> <span data-ttu-id="9c4e9-157">Dans ce cas, SaveChanges supprimera uniquement le blog, car il s’agit de la seule entité faisant l’objet d’un suivi :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-157">SaveChanges in this case will delete just the blog, since that's the only entity being tracked:</span></span>

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="9c4e9-158">Cela entraînerait une exception si la contrainte de clé étrangère dans la base de données n’est pas configurée pour les suppressions en cascade.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-158">This would result in an exception if the foreign key constraint in the database is not configured for cascade deletes.</span></span> <span data-ttu-id="9c4e9-159">Toutefois, dans ce cas, les publications sont supprimées par la base de données, car elle a été configurée avec le `ON DELETE CASCADE` moment où elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-159">However, in this case the posts are deleted by the database because it has been configured with `ON DELETE CASCADE` when it was created.</span></span>

> [!NOTE]
> <span data-ttu-id="9c4e9-160">En règle générale, les bases de données n’ont aucun moyen de supprimer automatiquement des orphelins.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-160">Databases don't typically have any way to automatically delete orphans.</span></span> <span data-ttu-id="9c4e9-161">Cela est dû au fait que EF Core représente des relations utilisant des navigations aussi bien que des clés étrangères, les bases de données ont uniquement des clés étrangères et aucune navigation.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-161">This is because while EF Core represents relationships using navigations as well of foreign keys, databases have only foreign keys and no navigations.</span></span> <span data-ttu-id="9c4e9-162">Cela signifie qu’il n’est généralement pas possible de rompre une relation sans charger les deux côtés dans le DbContext.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-162">This means that it is usually not possible to sever a relationship without loading both sides into the DbContext.</span></span>

> [!NOTE]
> <span data-ttu-id="9c4e9-163">La base de données en mémoire EF Core ne prend pas actuellement en charge les suppressions en cascade dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-163">The EF Core in-memory database does not currently support cascade deletes in the database.</span></span>

> [!WARNING]
> <span data-ttu-id="9c4e9-164">Ne configurez pas la suppression en cascade dans la base de données lors de la suppression réversible d’entités.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-164">Do not configure cascade delete in the database when soft-deleting entities.</span></span> <span data-ttu-id="9c4e9-165">Cela peut entraîner la suppression accidentelle des entités plutôt que la suppression réversible.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-165">This may cause entities to be accidentally really deleted instead of soft-deleted.</span></span>

### <a name="database-cascade-limitations"></a><span data-ttu-id="9c4e9-166">Limitations de cascade de base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-166">Database cascade limitations</span></span>

<span data-ttu-id="9c4e9-167">Certaines bases de données, plus particulièrement SQL Server, ont des limitations sur les comportements en cascade qui forment des cycles.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-167">Some databases, most notably SQL Server, have limitations on the cascade behaviors that form cycles.</span></span> <span data-ttu-id="9c4e9-168">Prenons l’exemple du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-168">For example, consider the following model:</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

<span data-ttu-id="9c4e9-169">Ce modèle a trois relations, toutes obligatoires et par conséquent configurées pour une suppression en cascade par Convention :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-169">This model has three relationships, all required and therefore configured to cascade delete by convention:</span></span>

- <span data-ttu-id="9c4e9-170">La suppression d’un blog entraîne la suppression en cascade de toutes les publications associées</span><span class="sxs-lookup"><span data-stu-id="9c4e9-170">Deleting a blog will cascade delete all the related posts</span></span>
- <span data-ttu-id="9c4e9-171">La suppression de l’auteur des publications entraîne la suppression en cascade des publications créées</span><span class="sxs-lookup"><span data-stu-id="9c4e9-171">Deleting the author of posts will cause the authored posts to be cascade deleted</span></span>
- <span data-ttu-id="9c4e9-172">Le fait de supprimer le propriétaire d’un blog entraînera la suppression en cascade du blog</span><span class="sxs-lookup"><span data-stu-id="9c4e9-172">Deleting the owner of a blog will cause the blog to be cascade deleted</span></span>

<span data-ttu-id="9c4e9-173">Tout cela est raisonnable (si un bit draconiennes dans les stratégies de gestion des blogs !), mais la tentative de création d’une base de données SQL Server avec ces cascades configurés entraîne l’exception suivante :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-173">This is all reasonable (if a bit draconian in blog management policies!) but attempting to create a SQL Server database with these cascades configured results in the following exception:</span></span>

> <span data-ttu-id="9c4e9-174">Microsoft. Data. SqlClient. SqlException (0x80131904) : l’introduction de la contrainte de clé étrangère « FK_Posts_Person_AuthorId » sur la table « Posts » peut entraîner des cycles ou plusieurs chemins d’accès en cascade.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-174">Microsoft.Data.SqlClient.SqlException (0x80131904): Introducing FOREIGN KEY constraint 'FK_Posts_Person_AuthorId' on table 'Posts' may cause cycles or multiple cascade paths.</span></span> <span data-ttu-id="9c4e9-175">Spécifiez ON DELETE NO ACTION ou ON UPDATE NO ACTION, ou modifiez d'autres contraintes FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-175">Specify ON DELETE NO ACTION or ON UPDATE NO ACTION, or modify other FOREIGN KEY constraints.</span></span>

<span data-ttu-id="9c4e9-176">Il existe deux façons de gérer cette situation :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-176">There are two ways to handle this situation:</span></span>

1. <span data-ttu-id="9c4e9-177">Modifiez une ou plusieurs des relations pour ne pas supprimer en cascade.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-177">Change one or more of the relationships to not cascade delete.</span></span>
2. <span data-ttu-id="9c4e9-178">Configurez la base de données sans une ou plusieurs de ces suppressions en cascade, puis vérifiez que toutes les entités dépendantes sont chargées afin que EF Core puisse effectuer le comportement en cascade.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-178">Configure the database without one or more of these cascade deletes, then ensure all dependent entities are loaded so that EF Core can perform the cascading behavior.</span></span>

<span data-ttu-id="9c4e9-179">En adoptant la première approche de notre exemple, nous pourrions rendre la relation blog-owner facultative, en lui attribuant une propriété de clé étrangère Nullable :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-179">Taking the first approach with our example, we could make the blog-owner relationship optional by giving it a nullable foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="9c4e9-180">Une relation facultative permet au blog d’exister sans propriétaire, ce qui signifie que la suppression en cascade ne sera plus configurée par défaut.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-180">An optional relationship allows the blog to exist without an owner, which means cascade delete will no longer be configured by default.</span></span> <span data-ttu-id="9c4e9-181">Cela signifie qu’il n’y a plus de cycle dans les actions en cascade et que la base de données peut être créée sans erreur sur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-181">This means there is no longer a cycle in cascading actions, and the database can be created without error on SQL Server.</span></span>

<span data-ttu-id="9c4e9-182">En adoptant plutôt la deuxième approche, nous pouvons conserver la relation de propriétaire de blog requise et configurée pour la suppression en cascade, mais faire en sorte que cette configuration s’applique uniquement aux entités suivies, et non à la base de données :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-182">Taking the second approach instead, we can keep the blog-owner relationship required and configured for cascade delete, but make this configuration only apply to tracked entities, not the database:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="9c4e9-183">À présent, que se passe-t-il si nous chargeons une personne et le blog qu’elle possède, puis que vous supprimez la personne ?</span><span class="sxs-lookup"><span data-stu-id="9c4e9-183">Now what happens if we load both a person and the blog they own, then delete the person?</span></span>

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

<span data-ttu-id="9c4e9-184">EF Core met en cascade la suppression du propriétaire afin que le blog soit également supprimé :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-184">EF Core will cascade the delete of the owner so that the blog is also deleted:</span></span>

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="9c4e9-185">Toutefois, si le blog n’est pas chargé lors de la suppression du propriétaire :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-185">However, if the blog is not loaded when the owner is deleted:</span></span>

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

<span data-ttu-id="9c4e9-186">Une exception est alors levée en raison de la violation de la contrainte de clé étrangère dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-186">Then an exception will be thrown due to violation of the foreign key constraint in the database:</span></span>

> <span data-ttu-id="9c4e9-187">Microsoft. Data. SqlClient. SqlException : l’instruction DELETE est en conflit avec la contrainte de référence « FK_Blogs_People_OwnerId ».</span><span class="sxs-lookup"><span data-stu-id="9c4e9-187">Microsoft.Data.SqlClient.SqlException: The DELETE statement conflicted with the REFERENCE constraint "FK_Blogs_People_OwnerId".</span></span> <span data-ttu-id="9c4e9-188">Le conflit s’est produit dans la base de données « Scratch », table «dbo. Blogs», colonne « OwnerId ».</span><span class="sxs-lookup"><span data-stu-id="9c4e9-188">The conflict occurred in database "Scratch", table "dbo.Blogs", column 'OwnerId'.</span></span>
<span data-ttu-id="9c4e9-189">L'instruction a été arrêtée.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-189">The statement has been terminated.</span></span>

## <a name="cascading-nulls"></a><span data-ttu-id="9c4e9-190">Valeurs NULL en cascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-190">Cascading nulls</span></span>

<span data-ttu-id="9c4e9-191">Les relations facultatives ont des propriétés de clé étrangère Nullable mappées à des colonnes de base de données Nullable.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-191">Optional relationships have nullable foreign key properties mapped to nullable database columns.</span></span> <span data-ttu-id="9c4e9-192">Cela signifie que la valeur de clé étrangère peut être définie sur Null lorsque le principal/parent actuel est supprimé ou est élevé du dépendant/enfant.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-192">This means that the foreign key value can be set to null when the current principal/parent is deleted or is severed from the dependent/child.</span></span>

<span data-ttu-id="9c4e9-193">Revenons à l’exemple à partir de [lorsque des comportements en cascade se produisent](#when-cascading-behaviors-happen), mais cette fois avec une relation facultative représentée par une `Post.BlogId` propriété de clé étrangère Nullable :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-193">Let's look again at the examples from [When cascading behaviors happen](#when-cascading-behaviors-happen), but this time with an optional relationship represented by a nullable `Post.BlogId` foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="9c4e9-194">Cette propriété de clé étrangère sera définie sur null pour chaque publication lors de la suppression de son blog associé.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-194">This foreign key property will be set to null for each post when its related blog is deleted.</span></span> <span data-ttu-id="9c4e9-195">Par exemple, ce code, qui est le même que précédemment :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-195">For example, this code, which is the same as before:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

<span data-ttu-id="9c4e9-196">Entraîne désormais les mises à jour de la base de données suivantes lorsque SaveChanges est appelé :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-196">Will now result in the following database updates when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="9c4e9-197">De même, si la relation est rompue à l’aide de l’un des exemples ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-197">Likewise, if the relationship is severed using either of the examples from above:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

<span data-ttu-id="9c4e9-198">Ou :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-198">Or:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

<span data-ttu-id="9c4e9-199">Les publications sont ensuite mises à jour avec des valeurs de clé étrangère NULL lorsque SaveChanges est appelé :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-199">Then the posts are updated with null foreign key values when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="9c4e9-200">Pour plus d’informations sur la façon dont EF Core gère les clés étrangères et les navigations, consultez [modification des clés étrangères](xref:core/change-tracking/relationship-changes) et des navigations, car leurs valeurs sont modifiées.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-200">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on how EF Core manages foreign keys and navigations as their values are changed.</span></span>

> [!NOTE]
> <span data-ttu-id="9c4e9-201">La correction des relations comme celle-ci a été le comportement par défaut de Entity Framework depuis la première version de 2008.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-201">The fixup of relationships like this has been the default behavior of Entity Framework since the first version in 2008.</span></span> <span data-ttu-id="9c4e9-202">Avant de EF Core il n’avait pas de nom et n’était pas possible de le modifier.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-202">Prior to EF Core it didn't have a name and was not possible to change.</span></span> <span data-ttu-id="9c4e9-203">Il est maintenant connu comme `ClientSetNull` décrit dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-203">It is now known as `ClientSetNull` as described in the next section.</span></span>

<span data-ttu-id="9c4e9-204">Les bases de données peuvent également être configurées pour mettre en cascade des valeurs NULL comme ceci lorsqu’un principal/parent dans une relation facultative est supprimé.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-204">Databases can also be configured to cascade nulls like this when a principal/parent in an optional relationship is deleted.</span></span> <span data-ttu-id="9c4e9-205">Toutefois, cela est bien moins courant que d’utiliser des suppressions en cascade dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-205">However, this is much less common than using cascading deletes in the database.</span></span> <span data-ttu-id="9c4e9-206">L’utilisation des suppressions en cascade et des valeurs NULL en cascade dans la base de données en même temps entraîne presque toujours des cycles de relation lors de l’utilisation de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-206">Using cascading deletes and cascading nulls in the database at the same time will almost always result in relationship cycles when using SQL Server.</span></span> <span data-ttu-id="9c4e9-207">Consultez la section suivante pour plus d’informations sur la configuration des valeurs NULL en cascade.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-207">See the next section for more information on configuring cascading nulls.</span></span>

## <a name="configuring-cascading-behaviors"></a><span data-ttu-id="9c4e9-208">Configuration des comportements en cascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-208">Configuring cascading behaviors</span></span>

> [!TIP]
> <span data-ttu-id="9c4e9-209">Veillez à lire les sections ci-dessus avant de vous y trouver.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-209">Be sure to read sections above before coming here.</span></span> <span data-ttu-id="9c4e9-210">Les options de configuration n’auront probablement pas de sens si la matière précédente n’est pas comprise.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-210">The configuration options will likely not make sense if the preceding material is not understood.</span></span>

<span data-ttu-id="9c4e9-211">Les comportements en cascade sont configurés par relation à l’aide <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> de la méthode dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-211">Cascade behaviors are configured per relationship using the <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> method in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="9c4e9-212">Exemple :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-212">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="9c4e9-213">Pour plus d’informations sur la configuration des relations entre les types d’entités, consultez [relations](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-213">See [Relationships](xref:core/modeling/relationships) for more information on configuring relationships between entity types.</span></span>

<span data-ttu-id="9c4e9-214">`OnDelete` accepte une valeur de l’enum, qui est d’une certaine confusion <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-214">`OnDelete` accepts a value from the, admittedly confusing, <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> enum.</span></span> <span data-ttu-id="9c4e9-215">Cet enum définit le comportement de EF Core sur les entités suivies, ainsi que la configuration de la suppression en cascade dans la base de données quand EF est utilisé pour créer le schéma.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-215">This enum defines both the behavior of EF Core on tracked entities, and the configuration of cascade delete in the database when EF is used to create the schema.</span></span>

### <a name="impact-on-database-schema"></a><span data-ttu-id="9c4e9-216">Impact sur le schéma de base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-216">Impact on database schema</span></span>

<span data-ttu-id="9c4e9-217">Le tableau suivant indique le résultat de chaque `OnDelete` valeur sur la contrainte de clé étrangère créée par EF Core migrations ou <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-217">The following table shows the result of each `OnDelete` value on the foreign key constraint created by EF Core migrations or <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A>.</span></span>

| <span data-ttu-id="9c4e9-218">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="9c4e9-218">DeleteBehavior</span></span>        | <span data-ttu-id="9c4e9-219">Impact sur le schéma de base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-219">Impact on database schema</span></span>
|:----------------------|--------------------------
| <span data-ttu-id="9c4e9-220">Cascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-220">Cascade</span></span>               | <span data-ttu-id="9c4e9-221">ON DELETE CASCADE</span><span class="sxs-lookup"><span data-stu-id="9c4e9-221">ON DELETE CASCADE</span></span>
| <span data-ttu-id="9c4e9-222">Restreindre</span><span class="sxs-lookup"><span data-stu-id="9c4e9-222">Restrict</span></span>              | <span data-ttu-id="9c4e9-223">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="9c4e9-223">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="9c4e9-224">NoAction</span><span class="sxs-lookup"><span data-stu-id="9c4e9-224">NoAction</span></span>              | <database default>
| <span data-ttu-id="9c4e9-225">SetNull</span><span class="sxs-lookup"><span data-stu-id="9c4e9-225">SetNull</span></span>               | <span data-ttu-id="9c4e9-226">ON DELETE SET NULL</span><span class="sxs-lookup"><span data-stu-id="9c4e9-226">ON DELETE SET NULL</span></span>
| <span data-ttu-id="9c4e9-227">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="9c4e9-227">ClientSetNull</span></span>         | <span data-ttu-id="9c4e9-228">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="9c4e9-228">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="9c4e9-229">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-229">ClientCascade</span></span>         | <span data-ttu-id="9c4e9-230">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="9c4e9-230">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="9c4e9-231">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="9c4e9-231">ClientNoAction</span></span>        | <database default>

> [!NOTE]
> <span data-ttu-id="9c4e9-232">Ce tableau est confus et nous prévoyons de le revoir dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-232">This table is confusing and we plan to revisit this in a future release.</span></span> <span data-ttu-id="9c4e9-233">Consultez [#21252 du problème GitHub](https://github.com/dotnet/efcore/issues/21252).</span><span class="sxs-lookup"><span data-stu-id="9c4e9-233">See [GitHub Issue #21252](https://github.com/dotnet/efcore/issues/21252).</span></span>

<span data-ttu-id="9c4e9-234">Les comportements de `ON DELETE NO ACTION` et `ON DELETE RESTRICT` dans les bases de données relationnelles sont généralement identiques ou très similaires.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-234">The behaviors of `ON DELETE NO ACTION` and `ON DELETE RESTRICT` in relational databases are typically either identical or very similar.</span></span> <span data-ttu-id="9c4e9-235">Malgré ce que `NO ACTION` peut impliquer, ces deux options entraînent l’application de contraintes référentielles.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-235">Despite what `NO ACTION` may imply, both of these options cause referential constraints to be enforced.</span></span> <span data-ttu-id="9c4e9-236">La différence, le cas échéant, réside dans le _moment où_ la base de données vérifie les contraintes.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-236">The difference, when there is one, is _when_ the database checks the constraints.</span></span>  <span data-ttu-id="9c4e9-237">Consultez la documentation de votre base de données pour obtenir les différences spécifiques entre `ON DELETE NO ACTION` et `ON DELETE RESTRICT` sur votre système de base de données.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-237">Check your database documentation for the specific differences between `ON DELETE NO ACTION` and `ON DELETE RESTRICT` on your database system.</span></span>

<span data-ttu-id="9c4e9-238">Les seules valeurs qui provoqueront des comportements en cascade sur la base de données sont `Cascade` et `SetNull` .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-238">The only values that will cause cascading behaviors on the database are `Cascade` and `SetNull`.</span></span> <span data-ttu-id="9c4e9-239">Toutes les autres valeurs configurent la base de données pour qu’elle n’effectue aucune modification en cascade.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-239">All other values will configure the database to not cascade any changes.</span></span>

### <a name="impact-on-savechanges-behavior"></a><span data-ttu-id="9c4e9-240">Impact sur le comportement d’SaveChanges</span><span class="sxs-lookup"><span data-stu-id="9c4e9-240">Impact on SaveChanges behavior</span></span>

<span data-ttu-id="9c4e9-241">Les tableaux des sections suivantes couvrent ce qui arrive aux entités dépendantes/enfants lorsque le principal/parent est supprimé, ou sa relation avec les entités dépendantes/enfants est interrompue.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-241">The tables in the following sections cover what happens to dependent/child entities when the principal/parent is deleted, or its relationship to the dependent/child entities is severed.</span></span> <span data-ttu-id="9c4e9-242">Chaque table couvre l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-242">Each table covers one of:</span></span>

- <span data-ttu-id="9c4e9-243">Relations facultatives (FK Nullable) et obligatoires (FK non Nullable)</span><span class="sxs-lookup"><span data-stu-id="9c4e9-243">Optional (nullable FK) and required (non-nullable FK) relationships</span></span>
- <span data-ttu-id="9c4e9-244">Lorsque les objets dépendants/enfants sont chargés et suivis par DbContext et lorsqu’ils existent uniquement dans la base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-244">When dependents/children are loaded and tracked by the DbContext and when they exist only in the database</span></span>

#### <a name="required-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="9c4e9-245">Relation requise avec les dépendants/enfants chargés</span><span class="sxs-lookup"><span data-stu-id="9c4e9-245">Required relationship with dependents/children loaded</span></span>

| <span data-ttu-id="9c4e9-246">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="9c4e9-246">DeleteBehavior</span></span>    | <span data-ttu-id="9c4e9-247">En cas de suppression du principal/parent</span><span class="sxs-lookup"><span data-stu-id="9c4e9-247">On deleting principal/parent</span></span>             | <span data-ttu-id="9c4e9-248">Sur la séparation du principal/du parent</span><span class="sxs-lookup"><span data-stu-id="9c4e9-248">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="9c4e9-249">Cascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-249">Cascade</span></span>           | <span data-ttu-id="9c4e9-250">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-250">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="9c4e9-251">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-251">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="9c4e9-252">Restreindre</span><span class="sxs-lookup"><span data-stu-id="9c4e9-252">Restrict</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="9c4e9-253">NoAction</span><span class="sxs-lookup"><span data-stu-id="9c4e9-253">NoAction</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="9c4e9-254">SetNull</span><span class="sxs-lookup"><span data-stu-id="9c4e9-254">SetNull</span></span>           | <span data-ttu-id="9c4e9-255">`SqlException` sur la création de la base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-255">`SqlException` on creating database</span></span>      | <span data-ttu-id="9c4e9-256">`SqlException` sur la création de la base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-256">`SqlException` on creating database</span></span>
| <span data-ttu-id="9c4e9-257">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="9c4e9-257">ClientSetNull</span></span>     | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="9c4e9-258">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-258">ClientCascade</span></span>     | <span data-ttu-id="9c4e9-259">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-259">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="9c4e9-260">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-260">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="9c4e9-261">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="9c4e9-261">ClientNoAction</span></span>    | `DbUpdateException`                      | `InvalidOperationException`

<span data-ttu-id="9c4e9-262">Remarques :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-262">Notes:</span></span>

- <span data-ttu-id="9c4e9-263">La valeur par défaut pour les relations requises comme celle-ci est `Cascade` .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-263">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="9c4e9-264">L’utilisation de tout autre chose que la suppression en cascade pour les relations requises entraîne une exception lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-264">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="9c4e9-265">En règle générale, il s’agit d’un `InvalidOperationException` EF Core, car l’état non valide est détecté dans les enfants/dépendants chargés.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-265">Typically, this is an `InvalidOperationException` from EF Core since the invalid state is detected in the loaded children/dependents.</span></span>
  - <span data-ttu-id="9c4e9-266">`ClientNoAction` Force EF Core à ne pas vérifier les dépendants de correction avant de les envoyer à la base de données. par conséquent, dans ce cas, la base de données lève une exception, qui est ensuite encapsulée dans un `DbUpdateException` par SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-266">`ClientNoAction` forces EF Core to not check fixup dependents before sending them to the database, so in this case the database throws an exception, which is then wrapped in a `DbUpdateException` by SaveChanges.</span></span>
  - <span data-ttu-id="9c4e9-267">`SetNull` est rejeté lors de la création de la base de données, car la colonne clé étrangère n’accepte pas la valeur null.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-267">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>
- <span data-ttu-id="9c4e9-268">Étant donné que les dépendants/enfants sont chargés, ils sont toujours supprimés par EF Core et ne sont jamais laissés à la suppression de la base de données.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-268">Since dependents/children are loaded, they are always deleted by EF Core, and never left for the database to delete.</span></span>

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="9c4e9-269">Relation requise avec les dépendants/enfants non chargés</span><span class="sxs-lookup"><span data-stu-id="9c4e9-269">Required relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="9c4e9-270">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="9c4e9-270">DeleteBehavior</span></span>    | <span data-ttu-id="9c4e9-271">En cas de suppression du principal/parent</span><span class="sxs-lookup"><span data-stu-id="9c4e9-271">On deleting principal/parent</span></span>             | <span data-ttu-id="9c4e9-272">Sur la séparation du principal/du parent</span><span class="sxs-lookup"><span data-stu-id="9c4e9-272">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="9c4e9-273">Cascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-273">Cascade</span></span>           | <span data-ttu-id="9c4e9-274">Dépendants supprimés par la base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-274">Dependents deleted by database</span></span>           | <span data-ttu-id="9c4e9-275">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-275">N/A</span></span>
| <span data-ttu-id="9c4e9-276">Restreindre</span><span class="sxs-lookup"><span data-stu-id="9c4e9-276">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="9c4e9-277">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-277">N/A</span></span>
| <span data-ttu-id="9c4e9-278">NoAction</span><span class="sxs-lookup"><span data-stu-id="9c4e9-278">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="9c4e9-279">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-279">N/A</span></span>
| <span data-ttu-id="9c4e9-280">SetNull</span><span class="sxs-lookup"><span data-stu-id="9c4e9-280">SetNull</span></span>           | <span data-ttu-id="9c4e9-281">`SqlException` sur la création de la base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-281">`SqlException` on creating database</span></span>      | <span data-ttu-id="9c4e9-282">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-282">N/A</span></span>
| <span data-ttu-id="9c4e9-283">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="9c4e9-283">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="9c4e9-284">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-284">N/A</span></span>
| <span data-ttu-id="9c4e9-285">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-285">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="9c4e9-286">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-286">N/A</span></span>
| <span data-ttu-id="9c4e9-287">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="9c4e9-287">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="9c4e9-288">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-288">N/A</span></span>

<span data-ttu-id="9c4e9-289">Remarques :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-289">Notes:</span></span>

- <span data-ttu-id="9c4e9-290">La séparation d’une relation n’est pas valide ici, car les objets dépendants/enfants ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-290">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="9c4e9-291">La valeur par défaut pour les relations requises comme celle-ci est `Cascade` .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-291">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="9c4e9-292">L’utilisation de tout autre chose que la suppression en cascade pour les relations requises entraîne une exception lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-292">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="9c4e9-293">En règle générale, il s’agit d’un `DbUpdateException` car les dépendants/enfants ne sont pas chargés, et par conséquent, l’état non valide ne peut être détecté que par la base de données.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-293">Typically, this is a `DbUpdateException` because the dependents/children are not loaded, and hence the invalid state can only be detected by the database.</span></span> <span data-ttu-id="9c4e9-294">SaveChanges encapsule ensuite l’exception de base de données dans un `DbUpdateException` .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-294">SaveChanges then wraps the database exception in a `DbUpdateException`.</span></span>
  - <span data-ttu-id="9c4e9-295">`SetNull` est rejeté lors de la création de la base de données, car la colonne clé étrangère n’accepte pas la valeur null.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-295">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>

#### <a name="optional-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="9c4e9-296">Relation facultative avec les dépendants/enfants chargés</span><span class="sxs-lookup"><span data-stu-id="9c4e9-296">Optional relationship with dependents/children loaded</span></span>

| <span data-ttu-id="9c4e9-297">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="9c4e9-297">DeleteBehavior</span></span>    | <span data-ttu-id="9c4e9-298">En cas de suppression du principal/parent</span><span class="sxs-lookup"><span data-stu-id="9c4e9-298">On deleting principal/parent</span></span>             | <span data-ttu-id="9c4e9-299">Sur la séparation du principal/du parent</span><span class="sxs-lookup"><span data-stu-id="9c4e9-299">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="9c4e9-300">Cascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-300">Cascade</span></span>           | <span data-ttu-id="9c4e9-301">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-301">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="9c4e9-302">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-302">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="9c4e9-303">Restreindre</span><span class="sxs-lookup"><span data-stu-id="9c4e9-303">Restrict</span></span>          | <span data-ttu-id="9c4e9-304">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-304">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="9c4e9-305">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-305">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="9c4e9-306">NoAction</span><span class="sxs-lookup"><span data-stu-id="9c4e9-306">NoAction</span></span>          | <span data-ttu-id="9c4e9-307">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-307">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="9c4e9-308">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-308">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="9c4e9-309">SetNull</span><span class="sxs-lookup"><span data-stu-id="9c4e9-309">SetNull</span></span>           | <span data-ttu-id="9c4e9-310">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-310">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="9c4e9-311">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-311">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="9c4e9-312">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="9c4e9-312">ClientSetNull</span></span>     | <span data-ttu-id="9c4e9-313">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-313">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="9c4e9-314">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-314">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="9c4e9-315">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-315">ClientCascade</span></span>     | <span data-ttu-id="9c4e9-316">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-316">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="9c4e9-317">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-317">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="9c4e9-318">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="9c4e9-318">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="9c4e9-319">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="9c4e9-319">Dependent FKs set to null by EF Core</span></span>

<span data-ttu-id="9c4e9-320">Remarques :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-320">Notes:</span></span>

- <span data-ttu-id="9c4e9-321">La valeur par défaut pour les relations facultatives comme celle-ci est `ClientSetNull` .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-321">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="9c4e9-322">Les objets dépendants/enfants ne sont jamais supprimés, sauf si `Cascade` ou `ClientCascade` sont configurés.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-322">Dependents/children are never deleted unless `Cascade` or `ClientCascade` are configured.</span></span>
- <span data-ttu-id="9c4e9-323">Toutes les autres valeurs entraînent la définition de la valeur null de la clés étrangères dépendante par EF Core...</span><span class="sxs-lookup"><span data-stu-id="9c4e9-323">All other values cause the dependent FKs to be set to null by EF Core...</span></span>
  - <span data-ttu-id="9c4e9-324">... sauf `ClientNoAction` qui indique à EF Core de ne pas toucher aux clés étrangères des dépendants/enfants lorsque le principal/parent est supprimé.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-324">...except `ClientNoAction` which tells EF Core not to touch the foreign keys of dependents/children when the principal/parent is deleted.</span></span> <span data-ttu-id="9c4e9-325">Par conséquent, la base de données lève une exception, qui est encapsulée en tant que `DbUpdateException` par SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-325">The database therefore throws an exception, which is wrapped as a `DbUpdateException` by SaveChanges.</span></span>

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="9c4e9-326">Relation facultative avec les dépendants/enfants non chargés</span><span class="sxs-lookup"><span data-stu-id="9c4e9-326">Optional relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="9c4e9-327">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="9c4e9-327">DeleteBehavior</span></span>    | <span data-ttu-id="9c4e9-328">En cas de suppression du principal/parent</span><span class="sxs-lookup"><span data-stu-id="9c4e9-328">On deleting principal/parent</span></span>             | <span data-ttu-id="9c4e9-329">Sur la séparation du principal/du parent</span><span class="sxs-lookup"><span data-stu-id="9c4e9-329">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="9c4e9-330">Cascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-330">Cascade</span></span>           | <span data-ttu-id="9c4e9-331">Dépendants supprimés par la base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-331">Dependents deleted by database</span></span>           | <span data-ttu-id="9c4e9-332">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-332">N/A</span></span>
| <span data-ttu-id="9c4e9-333">Restreindre</span><span class="sxs-lookup"><span data-stu-id="9c4e9-333">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="9c4e9-334">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-334">N/A</span></span>
| <span data-ttu-id="9c4e9-335">NoAction</span><span class="sxs-lookup"><span data-stu-id="9c4e9-335">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="9c4e9-336">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-336">N/A</span></span>
| <span data-ttu-id="9c4e9-337">SetNull</span><span class="sxs-lookup"><span data-stu-id="9c4e9-337">SetNull</span></span>           | <span data-ttu-id="9c4e9-338">Clés étrangères dépendant défini sur NULL par la base de données</span><span class="sxs-lookup"><span data-stu-id="9c4e9-338">Dependent FKs set to null by database</span></span>    | <span data-ttu-id="9c4e9-339">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-339">N/A</span></span>
| <span data-ttu-id="9c4e9-340">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="9c4e9-340">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="9c4e9-341">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-341">N/A</span></span>
| <span data-ttu-id="9c4e9-342">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="9c4e9-342">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="9c4e9-343">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-343">N/A</span></span>
| <span data-ttu-id="9c4e9-344">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="9c4e9-344">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="9c4e9-345">N/A</span><span class="sxs-lookup"><span data-stu-id="9c4e9-345">N/A</span></span>

<span data-ttu-id="9c4e9-346">Remarques :</span><span class="sxs-lookup"><span data-stu-id="9c4e9-346">Notes:</span></span>

- <span data-ttu-id="9c4e9-347">La séparation d’une relation n’est pas valide ici, car les objets dépendants/enfants ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-347">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="9c4e9-348">La valeur par défaut pour les relations facultatives comme celle-ci est `ClientSetNull` .</span><span class="sxs-lookup"><span data-stu-id="9c4e9-348">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="9c4e9-349">Les dépendants/enfants doivent être chargés pour éviter une exception de base de données, sauf si la base de données a été configurée pour mettre en cascade des suppressions ou des valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="9c4e9-349">Dependents/children must be loaded to avoid a database exception unless the database has been configured to cascade either deletes or nulls.</span></span>
