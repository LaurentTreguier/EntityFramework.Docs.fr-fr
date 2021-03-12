---
title: 'Suppression en cascade : EF Core'
description: Configuration des comportements en cascade déclenchés lorsqu’une entité est supprimée ou endommagée de son principal/parent
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 6e897be6b4f5e6550d9ed3590445df60bef4fb3c
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023612"
---
# <a name="cascade-delete"></a><span data-ttu-id="736ba-103">Suppression en cascade</span><span class="sxs-lookup"><span data-stu-id="736ba-103">Cascade Delete</span></span>

<span data-ttu-id="736ba-104">Entity Framework Core (EF Core) représente des relations à l’aide de clés étrangères.</span><span class="sxs-lookup"><span data-stu-id="736ba-104">Entity Framework Core (EF Core) represents relationships using foreign keys.</span></span> <span data-ttu-id="736ba-105">Une entité avec une clé étrangère est l’entité enfant ou dépendante dans la relation.</span><span class="sxs-lookup"><span data-stu-id="736ba-105">An entity with a foreign key is the child or dependent entity in the relationship.</span></span> <span data-ttu-id="736ba-106">La valeur de clé étrangère de cette entité doit correspondre à la valeur de clé primaire (ou à une autre valeur de clé) de l’entité principale/parente associée.</span><span class="sxs-lookup"><span data-stu-id="736ba-106">This entity's foreign key value must match the primary key value (or an alternate key value) of the related principal/parent entity.</span></span>

<span data-ttu-id="736ba-107">Si l’entité principale/parente est supprimée, les valeurs de clé étrangère des objets dépendants/enfants ne correspondent plus à la clé primaire ou secondaire d' _un_ principal/parent.</span><span class="sxs-lookup"><span data-stu-id="736ba-107">If the principal/parent entity is deleted, then the foreign key values of the dependents/children will no longer match the primary or alternate key of _any_ principal/parent.</span></span> <span data-ttu-id="736ba-108">Il s’agit d’un État non valide et entraîne une violation de contrainte référentielle dans la plupart des bases de données.</span><span class="sxs-lookup"><span data-stu-id="736ba-108">This is an invalid state, and will cause a referential constraint violation in most databases.</span></span>

<span data-ttu-id="736ba-109">Il existe deux options pour éviter cette violation de contrainte référentielle :</span><span class="sxs-lookup"><span data-stu-id="736ba-109">There are two options to avoid this referential constraint violation:</span></span>

1. <span data-ttu-id="736ba-110">Définir les valeurs FK sur null</span><span class="sxs-lookup"><span data-stu-id="736ba-110">Set the FK values to null</span></span>
2. <span data-ttu-id="736ba-111">Supprimer également les entités dépendantes/enfants</span><span class="sxs-lookup"><span data-stu-id="736ba-111">Also delete the dependent/child entities</span></span>

<span data-ttu-id="736ba-112">La première option dans valide uniquement pour les relations facultatives où la propriété de clé étrangère (et la colonne de base de données à laquelle elle est mappée) doit être Nullable.</span><span class="sxs-lookup"><span data-stu-id="736ba-112">The first option in only valid for optional relationships where the foreign key property (and the database column to which it is mapped) must be nullable.</span></span>

<span data-ttu-id="736ba-113">La deuxième option est valide pour tout type de relation et est connue sous le nom de « suppression en cascade ».</span><span class="sxs-lookup"><span data-stu-id="736ba-113">The second option is valid for any kind of relationship and is known as "cascade delete".</span></span>

> [!TIP]
> <span data-ttu-id="736ba-114">Ce document décrit les suppressions en cascade (et la suppression des orphelins) du point de vue de la mise à jour de la base de données.</span><span class="sxs-lookup"><span data-stu-id="736ba-114">This document describes cascade deletes (and deleting orphans) from the perspective of updating the database.</span></span> <span data-ttu-id="736ba-115">Cela permet d’utiliser de façon intensive les concepts introduits dans [change Tracking dans EF Core](xref:core/change-tracking/index) et de [modifier les clés étrangères et les navigations](xref:core/change-tracking/relationship-changes).</span><span class="sxs-lookup"><span data-stu-id="736ba-115">It makes heavy use of concepts introduced in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="736ba-116">Veillez à bien comprendre ces concepts avant de traiter le matériel ici.</span><span class="sxs-lookup"><span data-stu-id="736ba-116">Make sure to fully understand these concepts before tackling the material here.</span></span>

> [!TIP]  
> <span data-ttu-id="736ba-117">Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/CascadeDeletes).</span><span class="sxs-lookup"><span data-stu-id="736ba-117">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/CascadeDeletes).</span></span>

## <a name="when-cascading-behaviors-happen"></a><span data-ttu-id="736ba-118">Quand des comportements en cascade se produisent</span><span class="sxs-lookup"><span data-stu-id="736ba-118">When cascading behaviors happen</span></span>

<span data-ttu-id="736ba-119">Des suppressions en cascade sont nécessaires lorsqu’une entité dépendante/enfant ne peut plus être associée à son principal/parent actuel.</span><span class="sxs-lookup"><span data-stu-id="736ba-119">Cascading deletes are needed when a dependent/child entity can no longer be associated with its current principal/parent.</span></span> <span data-ttu-id="736ba-120">Cela peut se produire en raison de la suppression du principal/parent, ou lorsque le principal/parent existe toujours, mais que le dépendant/enfant n’est plus associé.</span><span class="sxs-lookup"><span data-stu-id="736ba-120">This can happen because the principal/parent is deleted, or it can happen when the principal/parent still exists but the dependent/child is no longer associated with it.</span></span>

### <a name="deleting-a-principalparent"></a><span data-ttu-id="736ba-121">Suppression d’un principal/parent</span><span class="sxs-lookup"><span data-stu-id="736ba-121">Deleting a principal/parent</span></span>

<span data-ttu-id="736ba-122">Considérons ce modèle simple `Blog` , où est le principal/le parent dans une relation avec `Post` , qui est le dépendant/enfant.</span><span class="sxs-lookup"><span data-stu-id="736ba-122">Consider this simple model where `Blog` is the principal/parent in a relationship with `Post`, which is the dependent/child.</span></span> <span data-ttu-id="736ba-123">`Post.BlogId` est une propriété de clé étrangère, dont la valeur doit correspondre `Post.Id` à la clé primaire de la publication à laquelle le blog appartient.</span><span class="sxs-lookup"><span data-stu-id="736ba-123">`Post.BlogId` is a foreign key property, the value of which must match the `Post.Id` primary key of the post to which the blog belongs.</span></span>

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

<span data-ttu-id="736ba-124">Par Convention, cette relation est configurée en tant que obligatoire, car la `Post.BlogId` propriété de clé étrangère n’accepte pas les valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="736ba-124">By convention, this relationship is configured as a required, since the `Post.BlogId` foreign key property is non-nullable.</span></span> <span data-ttu-id="736ba-125">Les relations requises sont configurées pour utiliser des suppressions en cascade par défaut.</span><span class="sxs-lookup"><span data-stu-id="736ba-125">Required relationships are configured to use cascade deletes by default.</span></span> <span data-ttu-id="736ba-126">Pour plus d’informations sur les relations de modélisation, consultez [relations](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="736ba-126">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships.</span></span>

<span data-ttu-id="736ba-127">Lors de la suppression d’un blog, toutes les publications sont supprimées en cascade.</span><span class="sxs-lookup"><span data-stu-id="736ba-127">When deleting a blog, all posts are cascade deleted.</span></span> <span data-ttu-id="736ba-128">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="736ba-128">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

<span data-ttu-id="736ba-129">SaveChanges génère le code SQL suivant, à l’aide de SQL Server comme exemple :</span><span class="sxs-lookup"><span data-stu-id="736ba-129">SaveChanges generates the following SQL, using SQL Server as an example:</span></span>

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

### <a name="severing-a-relationship"></a><span data-ttu-id="736ba-130">Séparation d’une relation</span><span class="sxs-lookup"><span data-stu-id="736ba-130">Severing a relationship</span></span>

<span data-ttu-id="736ba-131">Au lieu de supprimer le blog, nous pourrions à présent rompre la relation entre chaque publication et son blog.</span><span class="sxs-lookup"><span data-stu-id="736ba-131">Rather than deleting the blog, we could instead sever the relationship between each post and its blog.</span></span> <span data-ttu-id="736ba-132">Pour ce faire, vous pouvez définir la navigation `Post.Blog` de référence sur null pour chaque publication :</span><span class="sxs-lookup"><span data-stu-id="736ba-132">This can be done by setting the reference navigation `Post.Blog` to null for each post:</span></span>

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

<span data-ttu-id="736ba-133">La relation peut également être interrompue en supprimant chaque publication du `Blog.Posts` volet de navigation de la collection :</span><span class="sxs-lookup"><span data-stu-id="736ba-133">The relationship can also be severed by removing each post from the `Blog.Posts` collection navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

<span data-ttu-id="736ba-134">Dans les deux cas, le résultat est le même : le blog n’est pas supprimé, mais les publications qui ne sont plus associées à un blog sont supprimées :</span><span class="sxs-lookup"><span data-stu-id="736ba-134">In either case the result is the same: the blog is not deleted, but the posts that are no longer associated with any blog are deleted:</span></span>

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

<span data-ttu-id="736ba-135">La suppression d’entités qui ne sont plus associées à un principal/dépendant est connue sous le nom de « suppression des orphelins ».</span><span class="sxs-lookup"><span data-stu-id="736ba-135">Deleting entities that are no longer associated with any principal/dependent is known as "deleting orphans".</span></span>

> [!TIP]
> <span data-ttu-id="736ba-136">La suppression en cascade et la suppression des orphelins sont étroitement liées.</span><span class="sxs-lookup"><span data-stu-id="736ba-136">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="736ba-137">Les deux entraînent la suppression d’entités dépendantes/enfants lorsque la relation avec le principal/parent requis est interrompue.</span><span class="sxs-lookup"><span data-stu-id="736ba-137">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="736ba-138">Pour la suppression en cascade, ce problème se produit car le principal/le parent est lui-même supprimé.</span><span class="sxs-lookup"><span data-stu-id="736ba-138">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="736ba-139">Pour les orphelins, l’entité principale/parente existe toujours, mais n’est plus liée aux entités dépendantes/enfants.</span><span class="sxs-lookup"><span data-stu-id="736ba-139">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>  

## <a name="where-cascading-behaviors-happen"></a><span data-ttu-id="736ba-140">Où se produisent les comportements en cascade</span><span class="sxs-lookup"><span data-stu-id="736ba-140">Where cascading behaviors happen</span></span>

<span data-ttu-id="736ba-141">Les comportements en cascade peuvent être appliqués aux éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="736ba-141">Cascading behaviors can be applied to:</span></span>

- <span data-ttu-id="736ba-142">Entités suivies par le actuel <xref:Microsoft.EntityFrameworkCore.DbContext></span><span class="sxs-lookup"><span data-stu-id="736ba-142">Entities tracked by the current <xref:Microsoft.EntityFrameworkCore.DbContext></span></span>
- <span data-ttu-id="736ba-143">Entités de la base de données qui n’ont pas été chargées dans le contexte</span><span class="sxs-lookup"><span data-stu-id="736ba-143">Entities in the database that have not been loaded into the context</span></span>

### <a name="cascade-delete-of-tracked-entities"></a><span data-ttu-id="736ba-144">Suppression en cascade des entités suivies</span><span class="sxs-lookup"><span data-stu-id="736ba-144">Cascade delete of tracked entities</span></span>

<span data-ttu-id="736ba-145">EF Core applique toujours les comportements en cascade configurés aux entités suivies.</span><span class="sxs-lookup"><span data-stu-id="736ba-145">EF Core always applies configured cascading behaviors to tracked entities.</span></span> <span data-ttu-id="736ba-146">Cela signifie que si l’application charge toutes les entités dépendantes/enfants pertinentes dans DbContext, comme indiqué dans les exemples ci-dessus, les comportements en cascade sont correctement appliqués, quelle que soit la façon dont la base de données est configurée.</span><span class="sxs-lookup"><span data-stu-id="736ba-146">This means that if the application loads all relevant dependent/child entities into the DbContext, as is shown in the examples above, then cascading behaviors will be correctly applied regardless of how the database is configured.</span></span>

> [!TIP]
> <span data-ttu-id="736ba-147">Le minutage exact du moment où les comportements en cascade se produisent pour les entités suivies peut être contrôlé à l’aide <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> de et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="736ba-147">The exact timing of when cascading behaviors happen to tracked entities can be controlled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType>.</span></span> <span data-ttu-id="736ba-148">Pour plus d’informations [, consultez Modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) .</span><span class="sxs-lookup"><span data-stu-id="736ba-148">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

### <a name="cascade-delete-in-the-database"></a><span data-ttu-id="736ba-149">Suppression en cascade dans la base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-149">Cascade delete in the database</span></span>

<span data-ttu-id="736ba-150">De nombreux systèmes de base de données proposent également des comportements en cascade qui sont déclenchés lorsqu’une entité est supprimée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="736ba-150">Many database systems also offer cascading behaviors that are triggered when an entity is deleted in the database.</span></span> <span data-ttu-id="736ba-151">EF Core configure ces comportements en fonction du comportement de suppression en cascade dans le modèle EF Core quand une base de données est créée à l’aide de <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> ou EF Core migrations.</span><span class="sxs-lookup"><span data-stu-id="736ba-151">EF Core configures these behaviors based on the cascade delete behavior in the EF Core model when a database is created using <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> or EF Core migrations.</span></span> <span data-ttu-id="736ba-152">Par exemple, à l’aide du modèle ci-dessus, le tableau suivant est créé pour les publications quand vous utilisez SQL Server :</span><span class="sxs-lookup"><span data-stu-id="736ba-152">For example, using the model above, the following table is created for posts when using SQL Server:</span></span>

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

<span data-ttu-id="736ba-153">Notez que la contrainte de clé étrangère définissant la relation entre les blogs et les publications est configurée avec `ON DELETE CASCADE` .</span><span class="sxs-lookup"><span data-stu-id="736ba-153">Notice that the foreign key constraint defining the relationship between blogs and posts is configured with `ON DELETE CASCADE`.</span></span>

<span data-ttu-id="736ba-154">Si nous savons que la base de données est configurée de la même manière, nous pouvons supprimer un blog _sans charger au préalable les publications_ et la base de données s’occupera de la suppression de toutes les publications associées à ce blog.</span><span class="sxs-lookup"><span data-stu-id="736ba-154">If we know that the database is configured like this, then we can delete a blog _without first loading posts_ and the database will take care of deleting all the posts that were related to that blog.</span></span> <span data-ttu-id="736ba-155">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="736ba-155">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

<span data-ttu-id="736ba-156">Notez qu’il n’existe aucune `Include` pour les publications, donc elles ne sont pas chargées.</span><span class="sxs-lookup"><span data-stu-id="736ba-156">Notice that there is no `Include` for posts, so they are not loaded.</span></span> <span data-ttu-id="736ba-157">Dans ce cas, SaveChanges supprimera uniquement le blog, car il s’agit de la seule entité faisant l’objet d’un suivi :</span><span class="sxs-lookup"><span data-stu-id="736ba-157">SaveChanges in this case will delete just the blog, since that's the only entity being tracked:</span></span>

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="736ba-158">Cela entraînerait une exception si la contrainte de clé étrangère dans la base de données n’est pas configurée pour les suppressions en cascade.</span><span class="sxs-lookup"><span data-stu-id="736ba-158">This would result in an exception if the foreign key constraint in the database is not configured for cascade deletes.</span></span> <span data-ttu-id="736ba-159">Toutefois, dans ce cas, les publications sont supprimées par la base de données, car elle a été configurée avec le `ON DELETE CASCADE` moment où elle a été créée.</span><span class="sxs-lookup"><span data-stu-id="736ba-159">However, in this case the posts are deleted by the database because it has been configured with `ON DELETE CASCADE` when it was created.</span></span>

> [!NOTE]
> <span data-ttu-id="736ba-160">En règle générale, les bases de données n’ont aucun moyen de supprimer automatiquement des orphelins.</span><span class="sxs-lookup"><span data-stu-id="736ba-160">Databases don't typically have any way to automatically delete orphans.</span></span> <span data-ttu-id="736ba-161">Cela est dû au fait que EF Core représente des relations utilisant des navigations aussi bien que des clés étrangères, les bases de données ont uniquement des clés étrangères et aucune navigation.</span><span class="sxs-lookup"><span data-stu-id="736ba-161">This is because while EF Core represents relationships using navigations as well of foreign keys, databases have only foreign keys and no navigations.</span></span> <span data-ttu-id="736ba-162">Cela signifie qu’il n’est généralement pas possible de rompre une relation sans charger les deux côtés dans le DbContext.</span><span class="sxs-lookup"><span data-stu-id="736ba-162">This means that it is usually not possible to sever a relationship without loading both sides into the DbContext.</span></span>

> [!NOTE]
> <span data-ttu-id="736ba-163">La base de données en mémoire EF Core ne prend pas actuellement en charge les suppressions en cascade dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="736ba-163">The EF Core in-memory database does not currently support cascade deletes in the database.</span></span>

> [!WARNING]
> <span data-ttu-id="736ba-164">Ne configurez pas la suppression en cascade dans la base de données lors de la suppression réversible d’entités.</span><span class="sxs-lookup"><span data-stu-id="736ba-164">Do not configure cascade delete in the database when soft-deleting entities.</span></span> <span data-ttu-id="736ba-165">Cela peut entraîner la suppression accidentelle des entités plutôt que la suppression réversible.</span><span class="sxs-lookup"><span data-stu-id="736ba-165">This may cause entities to be accidentally really deleted instead of soft-deleted.</span></span>

### <a name="database-cascade-limitations"></a><span data-ttu-id="736ba-166">Limitations de cascade de base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-166">Database cascade limitations</span></span>

<span data-ttu-id="736ba-167">Certaines bases de données, plus particulièrement SQL Server, ont des limitations sur les comportements en cascade qui forment des cycles.</span><span class="sxs-lookup"><span data-stu-id="736ba-167">Some databases, most notably SQL Server, have limitations on the cascade behaviors that form cycles.</span></span> <span data-ttu-id="736ba-168">Prenons l’exemple du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="736ba-168">For example, consider the following model:</span></span>

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

<span data-ttu-id="736ba-169">Ce modèle a trois relations, toutes obligatoires et par conséquent configurées pour une suppression en cascade par Convention :</span><span class="sxs-lookup"><span data-stu-id="736ba-169">This model has three relationships, all required and therefore configured to cascade delete by convention:</span></span>

- <span data-ttu-id="736ba-170">La suppression d’un blog entraîne la suppression en cascade de toutes les publications associées</span><span class="sxs-lookup"><span data-stu-id="736ba-170">Deleting a blog will cascade delete all the related posts</span></span>
- <span data-ttu-id="736ba-171">La suppression de l’auteur des publications entraîne la suppression en cascade des publications créées</span><span class="sxs-lookup"><span data-stu-id="736ba-171">Deleting the author of posts will cause the authored posts to be cascade deleted</span></span>
- <span data-ttu-id="736ba-172">Le fait de supprimer le propriétaire d’un blog entraînera la suppression en cascade du blog</span><span class="sxs-lookup"><span data-stu-id="736ba-172">Deleting the owner of a blog will cause the blog to be cascade deleted</span></span>

<span data-ttu-id="736ba-173">Tout cela est raisonnable (si un bit draconiennes dans les stratégies de gestion des blogs !), mais la tentative de création d’une base de données SQL Server avec ces cascades configurés entraîne l’exception suivante :</span><span class="sxs-lookup"><span data-stu-id="736ba-173">This is all reasonable (if a bit draconian in blog management policies!) but attempting to create a SQL Server database with these cascades configured results in the following exception:</span></span>

> <span data-ttu-id="736ba-174">Microsoft. Data. SqlClient. SqlException (0x80131904) : l’introduction de la contrainte de clé étrangère « FK_Posts_Person_AuthorId » sur la table « Posts » peut entraîner des cycles ou plusieurs chemins d’accès en cascade.</span><span class="sxs-lookup"><span data-stu-id="736ba-174">Microsoft.Data.SqlClient.SqlException (0x80131904): Introducing FOREIGN KEY constraint 'FK_Posts_Person_AuthorId' on table 'Posts' may cause cycles or multiple cascade paths.</span></span> <span data-ttu-id="736ba-175">Spécifiez ON DELETE NO ACTION ou ON UPDATE NO ACTION, ou modifiez d'autres contraintes FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="736ba-175">Specify ON DELETE NO ACTION or ON UPDATE NO ACTION, or modify other FOREIGN KEY constraints.</span></span>

<span data-ttu-id="736ba-176">Il existe deux façons de gérer cette situation :</span><span class="sxs-lookup"><span data-stu-id="736ba-176">There are two ways to handle this situation:</span></span>

1. <span data-ttu-id="736ba-177">Modifiez une ou plusieurs des relations pour ne pas supprimer en cascade.</span><span class="sxs-lookup"><span data-stu-id="736ba-177">Change one or more of the relationships to not cascade delete.</span></span>
2. <span data-ttu-id="736ba-178">Configurez la base de données sans une ou plusieurs de ces suppressions en cascade, puis vérifiez que toutes les entités dépendantes sont chargées afin que EF Core puisse effectuer le comportement en cascade.</span><span class="sxs-lookup"><span data-stu-id="736ba-178">Configure the database without one or more of these cascade deletes, then ensure all dependent entities are loaded so that EF Core can perform the cascading behavior.</span></span>

<span data-ttu-id="736ba-179">En adoptant la première approche de notre exemple, nous pourrions rendre la relation blog-owner facultative, en lui attribuant une propriété de clé étrangère Nullable :</span><span class="sxs-lookup"><span data-stu-id="736ba-179">Taking the first approach with our example, we could make the blog-owner relationship optional by giving it a nullable foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="736ba-180">Une relation facultative permet au blog d’exister sans propriétaire, ce qui signifie que la suppression en cascade ne sera plus configurée par défaut.</span><span class="sxs-lookup"><span data-stu-id="736ba-180">An optional relationship allows the blog to exist without an owner, which means cascade delete will no longer be configured by default.</span></span> <span data-ttu-id="736ba-181">Cela signifie qu’il n’y a plus de cycle dans les actions en cascade et que la base de données peut être créée sans erreur sur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="736ba-181">This means there is no longer a cycle in cascading actions, and the database can be created without error on SQL Server.</span></span>

<span data-ttu-id="736ba-182">En adoptant plutôt la deuxième approche, nous pouvons conserver la relation de propriétaire de blog requise et configurée pour la suppression en cascade, mais faire en sorte que cette configuration s’applique uniquement aux entités suivies, et non à la base de données :</span><span class="sxs-lookup"><span data-stu-id="736ba-182">Taking the second approach instead, we can keep the blog-owner relationship required and configured for cascade delete, but make this configuration only apply to tracked entities, not the database:</span></span>

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

<span data-ttu-id="736ba-183">À présent, que se passe-t-il si nous chargeons une personne et le blog qu’elle possède, puis que vous supprimez la personne ?</span><span class="sxs-lookup"><span data-stu-id="736ba-183">Now what happens if we load both a person and the blog they own, then delete the person?</span></span>

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

<span data-ttu-id="736ba-184">EF Core met en cascade la suppression du propriétaire afin que le blog soit également supprimé :</span><span class="sxs-lookup"><span data-stu-id="736ba-184">EF Core will cascade the delete of the owner so that the blog is also deleted:</span></span>

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

<span data-ttu-id="736ba-185">Toutefois, si le blog n’est pas chargé lors de la suppression du propriétaire :</span><span class="sxs-lookup"><span data-stu-id="736ba-185">However, if the blog is not loaded when the owner is deleted:</span></span>

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

<span data-ttu-id="736ba-186">Une exception est alors levée en raison de la violation de la contrainte de clé étrangère dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="736ba-186">Then an exception will be thrown due to violation of the foreign key constraint in the database:</span></span>

> <span data-ttu-id="736ba-187">Microsoft. Data. SqlClient. SqlException : l’instruction DELETE est en conflit avec la contrainte de référence « FK_Blogs_People_OwnerId ».</span><span class="sxs-lookup"><span data-stu-id="736ba-187">Microsoft.Data.SqlClient.SqlException: The DELETE statement conflicted with the REFERENCE constraint "FK_Blogs_People_OwnerId".</span></span> <span data-ttu-id="736ba-188">Le conflit s’est produit dans la base de données « Scratch », table «dbo. Blogs», colonne « OwnerId ».</span><span class="sxs-lookup"><span data-stu-id="736ba-188">The conflict occurred in database "Scratch", table "dbo.Blogs", column 'OwnerId'.</span></span>
<span data-ttu-id="736ba-189">L'instruction a été arrêtée.</span><span class="sxs-lookup"><span data-stu-id="736ba-189">The statement has been terminated.</span></span>

## <a name="cascading-nulls"></a><span data-ttu-id="736ba-190">Valeurs NULL en cascade</span><span class="sxs-lookup"><span data-stu-id="736ba-190">Cascading nulls</span></span>

<span data-ttu-id="736ba-191">Les relations facultatives ont des propriétés de clé étrangère Nullable mappées à des colonnes de base de données Nullable.</span><span class="sxs-lookup"><span data-stu-id="736ba-191">Optional relationships have nullable foreign key properties mapped to nullable database columns.</span></span> <span data-ttu-id="736ba-192">Cela signifie que la valeur de clé étrangère peut être définie sur Null lorsque le principal/parent actuel est supprimé ou est élevé du dépendant/enfant.</span><span class="sxs-lookup"><span data-stu-id="736ba-192">This means that the foreign key value can be set to null when the current principal/parent is deleted or is severed from the dependent/child.</span></span>

<span data-ttu-id="736ba-193">Revenons à l’exemple à partir de [lorsque des comportements en cascade se produisent](#when-cascading-behaviors-happen), mais cette fois avec une relation facultative représentée par une `Post.BlogId` propriété de clé étrangère Nullable :</span><span class="sxs-lookup"><span data-stu-id="736ba-193">Let's look again at the examples from [When cascading behaviors happen](#when-cascading-behaviors-happen), but this time with an optional relationship represented by a nullable `Post.BlogId` foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="736ba-194">Cette propriété de clé étrangère sera définie sur null pour chaque publication lors de la suppression de son blog associé.</span><span class="sxs-lookup"><span data-stu-id="736ba-194">This foreign key property will be set to null for each post when its related blog is deleted.</span></span> <span data-ttu-id="736ba-195">Par exemple, ce code, qui est le même que précédemment :</span><span class="sxs-lookup"><span data-stu-id="736ba-195">For example, this code, which is the same as before:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

<span data-ttu-id="736ba-196">Entraîne désormais les mises à jour de la base de données suivantes lorsque SaveChanges est appelé :</span><span class="sxs-lookup"><span data-stu-id="736ba-196">Will now result in the following database updates when SaveChanges is called:</span></span>

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

<span data-ttu-id="736ba-197">De même, si la relation est rompue à l’aide de l’un des exemples ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="736ba-197">Likewise, if the relationship is severed using either of the examples from above:</span></span>

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

<span data-ttu-id="736ba-198">Ou :</span><span class="sxs-lookup"><span data-stu-id="736ba-198">Or:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

<span data-ttu-id="736ba-199">Les publications sont ensuite mises à jour avec des valeurs de clé étrangère NULL lorsque SaveChanges est appelé :</span><span class="sxs-lookup"><span data-stu-id="736ba-199">Then the posts are updated with null foreign key values when SaveChanges is called:</span></span>

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

<span data-ttu-id="736ba-200">Pour plus d’informations sur la façon dont EF Core gère les clés étrangères et les navigations, consultez [modification des clés étrangères](xref:core/change-tracking/relationship-changes) et des navigations, car leurs valeurs sont modifiées.</span><span class="sxs-lookup"><span data-stu-id="736ba-200">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on how EF Core manages foreign keys and navigations as their values are changed.</span></span>

> [!NOTE]
> <span data-ttu-id="736ba-201">La correction des relations comme celle-ci a été le comportement par défaut de Entity Framework depuis la première version de 2008.</span><span class="sxs-lookup"><span data-stu-id="736ba-201">The fixup of relationships like this has been the default behavior of Entity Framework since the first version in 2008.</span></span> <span data-ttu-id="736ba-202">Avant de EF Core il n’avait pas de nom et n’était pas possible de le modifier.</span><span class="sxs-lookup"><span data-stu-id="736ba-202">Prior to EF Core it didn't have a name and was not possible to change.</span></span> <span data-ttu-id="736ba-203">Il est maintenant connu comme `ClientSetNull` décrit dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="736ba-203">It is now known as `ClientSetNull` as described in the next section.</span></span>

<span data-ttu-id="736ba-204">Les bases de données peuvent également être configurées pour mettre en cascade des valeurs NULL comme ceci lorsqu’un principal/parent dans une relation facultative est supprimé.</span><span class="sxs-lookup"><span data-stu-id="736ba-204">Databases can also be configured to cascade nulls like this when a principal/parent in an optional relationship is deleted.</span></span> <span data-ttu-id="736ba-205">Toutefois, cela est bien moins courant que d’utiliser des suppressions en cascade dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="736ba-205">However, this is much less common than using cascading deletes in the database.</span></span> <span data-ttu-id="736ba-206">L’utilisation des suppressions en cascade et des valeurs NULL en cascade dans la base de données en même temps entraîne presque toujours des cycles de relation lors de l’utilisation de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="736ba-206">Using cascading deletes and cascading nulls in the database at the same time will almost always result in relationship cycles when using SQL Server.</span></span> <span data-ttu-id="736ba-207">Consultez la section suivante pour plus d’informations sur la configuration des valeurs NULL en cascade.</span><span class="sxs-lookup"><span data-stu-id="736ba-207">See the next section for more information on configuring cascading nulls.</span></span>

## <a name="configuring-cascading-behaviors"></a><span data-ttu-id="736ba-208">Configuration des comportements en cascade</span><span class="sxs-lookup"><span data-stu-id="736ba-208">Configuring cascading behaviors</span></span>

> [!TIP]
> <span data-ttu-id="736ba-209">Veillez à lire les sections ci-dessus avant de vous y trouver.</span><span class="sxs-lookup"><span data-stu-id="736ba-209">Be sure to read sections above before coming here.</span></span> <span data-ttu-id="736ba-210">Les options de configuration n’auront probablement pas de sens si la matière précédente n’est pas comprise.</span><span class="sxs-lookup"><span data-stu-id="736ba-210">The configuration options will likely not make sense if the preceding material is not understood.</span></span>

<span data-ttu-id="736ba-211">Les comportements en cascade sont configurés par relation à l’aide <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> de la méthode dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="736ba-211">Cascade behaviors are configured per relationship using the <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> method in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="736ba-212">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="736ba-212">For example:</span></span>

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

<span data-ttu-id="736ba-213">Pour plus d’informations sur la configuration des relations entre les types d’entités, consultez [relations](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="736ba-213">See [Relationships](xref:core/modeling/relationships) for more information on configuring relationships between entity types.</span></span>

<span data-ttu-id="736ba-214">`OnDelete` accepte une valeur de l’enum, qui est d’une certaine confusion <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> .</span><span class="sxs-lookup"><span data-stu-id="736ba-214">`OnDelete` accepts a value from the, admittedly confusing, <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> enum.</span></span> <span data-ttu-id="736ba-215">Cet enum définit le comportement de EF Core sur les entités suivies, ainsi que la configuration de la suppression en cascade dans la base de données quand EF est utilisé pour créer le schéma.</span><span class="sxs-lookup"><span data-stu-id="736ba-215">This enum defines both the behavior of EF Core on tracked entities, and the configuration of cascade delete in the database when EF is used to create the schema.</span></span>

### <a name="impact-on-database-schema"></a><span data-ttu-id="736ba-216">Impact sur le schéma de base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-216">Impact on database schema</span></span>

<span data-ttu-id="736ba-217">Le tableau suivant indique le résultat de chaque `OnDelete` valeur sur la contrainte de clé étrangère créée par EF Core migrations ou <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> .</span><span class="sxs-lookup"><span data-stu-id="736ba-217">The following table shows the result of each `OnDelete` value on the foreign key constraint created by EF Core migrations or <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A>.</span></span>

| <span data-ttu-id="736ba-218">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="736ba-218">DeleteBehavior</span></span>        | <span data-ttu-id="736ba-219">Impact sur le schéma de base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-219">Impact on database schema</span></span>
|:----------------------|--------------------------
| <span data-ttu-id="736ba-220">Cascade</span><span class="sxs-lookup"><span data-stu-id="736ba-220">Cascade</span></span>               | <span data-ttu-id="736ba-221">ON DELETE CASCADE</span><span class="sxs-lookup"><span data-stu-id="736ba-221">ON DELETE CASCADE</span></span>
| <span data-ttu-id="736ba-222">Restreindre</span><span class="sxs-lookup"><span data-stu-id="736ba-222">Restrict</span></span>              | <span data-ttu-id="736ba-223">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="736ba-223">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="736ba-224">NoAction</span><span class="sxs-lookup"><span data-stu-id="736ba-224">NoAction</span></span>              | <span data-ttu-id="736ba-225">base de données par défaut</span><span class="sxs-lookup"><span data-stu-id="736ba-225">database default</span></span>
| <span data-ttu-id="736ba-226">SetNull</span><span class="sxs-lookup"><span data-stu-id="736ba-226">SetNull</span></span>               | <span data-ttu-id="736ba-227">ON DELETE SET NULL</span><span class="sxs-lookup"><span data-stu-id="736ba-227">ON DELETE SET NULL</span></span>
| <span data-ttu-id="736ba-228">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="736ba-228">ClientSetNull</span></span>         | <span data-ttu-id="736ba-229">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="736ba-229">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="736ba-230">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="736ba-230">ClientCascade</span></span>         | <span data-ttu-id="736ba-231">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="736ba-231">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="736ba-232">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="736ba-232">ClientNoAction</span></span>        | <span data-ttu-id="736ba-233">base de données par défaut</span><span class="sxs-lookup"><span data-stu-id="736ba-233">database default</span></span>

> [!NOTE]
> <span data-ttu-id="736ba-234">Ce tableau est confus et nous prévoyons de le revoir dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="736ba-234">This table is confusing and we plan to revisit this in a future release.</span></span> <span data-ttu-id="736ba-235">Consultez [#21252 du problème GitHub](https://github.com/dotnet/efcore/issues/21252).</span><span class="sxs-lookup"><span data-stu-id="736ba-235">See [GitHub Issue #21252](https://github.com/dotnet/efcore/issues/21252).</span></span>

<span data-ttu-id="736ba-236">Les comportements de `ON DELETE NO ACTION` et `ON DELETE RESTRICT` dans les bases de données relationnelles sont généralement identiques ou très similaires.</span><span class="sxs-lookup"><span data-stu-id="736ba-236">The behaviors of `ON DELETE NO ACTION` and `ON DELETE RESTRICT` in relational databases are typically either identical or very similar.</span></span> <span data-ttu-id="736ba-237">Malgré ce que `NO ACTION` peut impliquer, ces deux options entraînent l’application de contraintes référentielles.</span><span class="sxs-lookup"><span data-stu-id="736ba-237">Despite what `NO ACTION` may imply, both of these options cause referential constraints to be enforced.</span></span> <span data-ttu-id="736ba-238">La différence, le cas échéant, réside dans le _moment où_ la base de données vérifie les contraintes.</span><span class="sxs-lookup"><span data-stu-id="736ba-238">The difference, when there is one, is _when_ the database checks the constraints.</span></span>  <span data-ttu-id="736ba-239">Consultez la documentation de votre base de données pour obtenir les différences spécifiques entre `ON DELETE NO ACTION` et `ON DELETE RESTRICT` sur votre système de base de données.</span><span class="sxs-lookup"><span data-stu-id="736ba-239">Check your database documentation for the specific differences between `ON DELETE NO ACTION` and `ON DELETE RESTRICT` on your database system.</span></span>

<span data-ttu-id="736ba-240">Les seules valeurs qui provoqueront des comportements en cascade sur la base de données sont `Cascade` et `SetNull` .</span><span class="sxs-lookup"><span data-stu-id="736ba-240">The only values that will cause cascading behaviors on the database are `Cascade` and `SetNull`.</span></span> <span data-ttu-id="736ba-241">Toutes les autres valeurs configurent la base de données pour qu’elle n’effectue aucune modification en cascade.</span><span class="sxs-lookup"><span data-stu-id="736ba-241">All other values will configure the database to not cascade any changes.</span></span>

### <a name="impact-on-savechanges-behavior"></a><span data-ttu-id="736ba-242">Impact sur le comportement d’SaveChanges</span><span class="sxs-lookup"><span data-stu-id="736ba-242">Impact on SaveChanges behavior</span></span>

<span data-ttu-id="736ba-243">Les tableaux des sections suivantes couvrent ce qui arrive aux entités dépendantes/enfants lorsque le principal/parent est supprimé, ou sa relation avec les entités dépendantes/enfants est interrompue.</span><span class="sxs-lookup"><span data-stu-id="736ba-243">The tables in the following sections cover what happens to dependent/child entities when the principal/parent is deleted, or its relationship to the dependent/child entities is severed.</span></span> <span data-ttu-id="736ba-244">Chaque table couvre l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="736ba-244">Each table covers one of:</span></span>

- <span data-ttu-id="736ba-245">Relations facultatives (FK Nullable) et obligatoires (FK non Nullable)</span><span class="sxs-lookup"><span data-stu-id="736ba-245">Optional (nullable FK) and required (non-nullable FK) relationships</span></span>
- <span data-ttu-id="736ba-246">Lorsque les objets dépendants/enfants sont chargés et suivis par DbContext et lorsqu’ils existent uniquement dans la base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-246">When dependents/children are loaded and tracked by the DbContext and when they exist only in the database</span></span>

#### <a name="required-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="736ba-247">Relation requise avec les dépendants/enfants chargés</span><span class="sxs-lookup"><span data-stu-id="736ba-247">Required relationship with dependents/children loaded</span></span>

| <span data-ttu-id="736ba-248">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="736ba-248">DeleteBehavior</span></span>    | <span data-ttu-id="736ba-249">En cas de suppression du principal/parent</span><span class="sxs-lookup"><span data-stu-id="736ba-249">On deleting principal/parent</span></span>             | <span data-ttu-id="736ba-250">Sur la séparation du principal/du parent</span><span class="sxs-lookup"><span data-stu-id="736ba-250">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="736ba-251">Cascade</span><span class="sxs-lookup"><span data-stu-id="736ba-251">Cascade</span></span>           | <span data-ttu-id="736ba-252">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-252">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="736ba-253">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-253">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="736ba-254">Restreindre</span><span class="sxs-lookup"><span data-stu-id="736ba-254">Restrict</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="736ba-255">NoAction</span><span class="sxs-lookup"><span data-stu-id="736ba-255">NoAction</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="736ba-256">SetNull</span><span class="sxs-lookup"><span data-stu-id="736ba-256">SetNull</span></span>           | <span data-ttu-id="736ba-257">`SqlException` sur la création de la base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-257">`SqlException` on creating database</span></span>      | <span data-ttu-id="736ba-258">`SqlException` sur la création de la base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-258">`SqlException` on creating database</span></span>
| <span data-ttu-id="736ba-259">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="736ba-259">ClientSetNull</span></span>     | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="736ba-260">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="736ba-260">ClientCascade</span></span>     | <span data-ttu-id="736ba-261">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-261">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="736ba-262">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-262">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="736ba-263">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="736ba-263">ClientNoAction</span></span>    | `DbUpdateException`                      | `InvalidOperationException`

<span data-ttu-id="736ba-264">Remarques :</span><span class="sxs-lookup"><span data-stu-id="736ba-264">Notes:</span></span>

- <span data-ttu-id="736ba-265">La valeur par défaut pour les relations requises comme celle-ci est `Cascade` .</span><span class="sxs-lookup"><span data-stu-id="736ba-265">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="736ba-266">L’utilisation de tout autre chose que la suppression en cascade pour les relations requises entraîne une exception lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="736ba-266">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="736ba-267">En règle générale, il s’agit d’un `InvalidOperationException` EF Core, car l’état non valide est détecté dans les enfants/dépendants chargés.</span><span class="sxs-lookup"><span data-stu-id="736ba-267">Typically, this is an `InvalidOperationException` from EF Core since the invalid state is detected in the loaded children/dependents.</span></span>
  - <span data-ttu-id="736ba-268">`ClientNoAction` Force EF Core à ne pas vérifier les dépendants de correction avant de les envoyer à la base de données. par conséquent, dans ce cas, la base de données lève une exception, qui est ensuite encapsulée dans un `DbUpdateException` par SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="736ba-268">`ClientNoAction` forces EF Core to not check fixup dependents before sending them to the database, so in this case the database throws an exception, which is then wrapped in a `DbUpdateException` by SaveChanges.</span></span>
  - <span data-ttu-id="736ba-269">`SetNull` est rejeté lors de la création de la base de données, car la colonne clé étrangère n’accepte pas la valeur null.</span><span class="sxs-lookup"><span data-stu-id="736ba-269">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>
- <span data-ttu-id="736ba-270">Étant donné que les dépendants/enfants sont chargés, ils sont toujours supprimés par EF Core et ne sont jamais laissés à la suppression de la base de données.</span><span class="sxs-lookup"><span data-stu-id="736ba-270">Since dependents/children are loaded, they are always deleted by EF Core, and never left for the database to delete.</span></span>

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="736ba-271">Relation requise avec les dépendants/enfants non chargés</span><span class="sxs-lookup"><span data-stu-id="736ba-271">Required relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="736ba-272">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="736ba-272">DeleteBehavior</span></span>    | <span data-ttu-id="736ba-273">En cas de suppression du principal/parent</span><span class="sxs-lookup"><span data-stu-id="736ba-273">On deleting principal/parent</span></span>             | <span data-ttu-id="736ba-274">Sur la séparation du principal/du parent</span><span class="sxs-lookup"><span data-stu-id="736ba-274">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="736ba-275">Cascade</span><span class="sxs-lookup"><span data-stu-id="736ba-275">Cascade</span></span>           | <span data-ttu-id="736ba-276">Dépendants supprimés par la base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-276">Dependents deleted by database</span></span>           | <span data-ttu-id="736ba-277">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-277">N/A</span></span>
| <span data-ttu-id="736ba-278">Restreindre</span><span class="sxs-lookup"><span data-stu-id="736ba-278">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="736ba-279">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-279">N/A</span></span>
| <span data-ttu-id="736ba-280">NoAction</span><span class="sxs-lookup"><span data-stu-id="736ba-280">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="736ba-281">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-281">N/A</span></span>
| <span data-ttu-id="736ba-282">SetNull</span><span class="sxs-lookup"><span data-stu-id="736ba-282">SetNull</span></span>           | <span data-ttu-id="736ba-283">`SqlException` sur la création de la base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-283">`SqlException` on creating database</span></span>      | <span data-ttu-id="736ba-284">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-284">N/A</span></span>
| <span data-ttu-id="736ba-285">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="736ba-285">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="736ba-286">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-286">N/A</span></span>
| <span data-ttu-id="736ba-287">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="736ba-287">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="736ba-288">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-288">N/A</span></span>
| <span data-ttu-id="736ba-289">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="736ba-289">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="736ba-290">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-290">N/A</span></span>

<span data-ttu-id="736ba-291">Remarques :</span><span class="sxs-lookup"><span data-stu-id="736ba-291">Notes:</span></span>

- <span data-ttu-id="736ba-292">La séparation d’une relation n’est pas valide ici, car les objets dépendants/enfants ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="736ba-292">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="736ba-293">La valeur par défaut pour les relations requises comme celle-ci est `Cascade` .</span><span class="sxs-lookup"><span data-stu-id="736ba-293">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="736ba-294">L’utilisation de tout autre chose que la suppression en cascade pour les relations requises entraîne une exception lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="736ba-294">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="736ba-295">En règle générale, il s’agit d’un `DbUpdateException` car les dépendants/enfants ne sont pas chargés, et par conséquent, l’état non valide ne peut être détecté que par la base de données.</span><span class="sxs-lookup"><span data-stu-id="736ba-295">Typically, this is a `DbUpdateException` because the dependents/children are not loaded, and hence the invalid state can only be detected by the database.</span></span> <span data-ttu-id="736ba-296">SaveChanges encapsule ensuite l’exception de base de données dans un `DbUpdateException` .</span><span class="sxs-lookup"><span data-stu-id="736ba-296">SaveChanges then wraps the database exception in a `DbUpdateException`.</span></span>
  - <span data-ttu-id="736ba-297">`SetNull` est rejeté lors de la création de la base de données, car la colonne clé étrangère n’accepte pas la valeur null.</span><span class="sxs-lookup"><span data-stu-id="736ba-297">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>

#### <a name="optional-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="736ba-298">Relation facultative avec les dépendants/enfants chargés</span><span class="sxs-lookup"><span data-stu-id="736ba-298">Optional relationship with dependents/children loaded</span></span>

| <span data-ttu-id="736ba-299">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="736ba-299">DeleteBehavior</span></span>    | <span data-ttu-id="736ba-300">En cas de suppression du principal/parent</span><span class="sxs-lookup"><span data-stu-id="736ba-300">On deleting principal/parent</span></span>             | <span data-ttu-id="736ba-301">Sur la séparation du principal/du parent</span><span class="sxs-lookup"><span data-stu-id="736ba-301">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="736ba-302">Cascade</span><span class="sxs-lookup"><span data-stu-id="736ba-302">Cascade</span></span>           | <span data-ttu-id="736ba-303">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-303">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="736ba-304">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-304">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="736ba-305">Restreindre</span><span class="sxs-lookup"><span data-stu-id="736ba-305">Restrict</span></span>          | <span data-ttu-id="736ba-306">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-306">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="736ba-307">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-307">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="736ba-308">NoAction</span><span class="sxs-lookup"><span data-stu-id="736ba-308">NoAction</span></span>          | <span data-ttu-id="736ba-309">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-309">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="736ba-310">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-310">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="736ba-311">SetNull</span><span class="sxs-lookup"><span data-stu-id="736ba-311">SetNull</span></span>           | <span data-ttu-id="736ba-312">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-312">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="736ba-313">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-313">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="736ba-314">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="736ba-314">ClientSetNull</span></span>     | <span data-ttu-id="736ba-315">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-315">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="736ba-316">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-316">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="736ba-317">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="736ba-317">ClientCascade</span></span>     | <span data-ttu-id="736ba-318">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-318">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="736ba-319">Dépendants supprimés par EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-319">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="736ba-320">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="736ba-320">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="736ba-321">Le clés étrangères dépendant a la valeur null en EF Core</span><span class="sxs-lookup"><span data-stu-id="736ba-321">Dependent FKs set to null by EF Core</span></span>

<span data-ttu-id="736ba-322">Remarques :</span><span class="sxs-lookup"><span data-stu-id="736ba-322">Notes:</span></span>

- <span data-ttu-id="736ba-323">La valeur par défaut pour les relations facultatives comme celle-ci est `ClientSetNull` .</span><span class="sxs-lookup"><span data-stu-id="736ba-323">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="736ba-324">Les objets dépendants/enfants ne sont jamais supprimés, sauf si `Cascade` ou `ClientCascade` sont configurés.</span><span class="sxs-lookup"><span data-stu-id="736ba-324">Dependents/children are never deleted unless `Cascade` or `ClientCascade` are configured.</span></span>
- <span data-ttu-id="736ba-325">Toutes les autres valeurs entraînent la définition de la valeur null de la clés étrangères dépendante par EF Core...</span><span class="sxs-lookup"><span data-stu-id="736ba-325">All other values cause the dependent FKs to be set to null by EF Core...</span></span>
  - <span data-ttu-id="736ba-326">... sauf `ClientNoAction` qui indique à EF Core de ne pas toucher aux clés étrangères des dépendants/enfants lorsque le principal/parent est supprimé.</span><span class="sxs-lookup"><span data-stu-id="736ba-326">...except `ClientNoAction` which tells EF Core not to touch the foreign keys of dependents/children when the principal/parent is deleted.</span></span> <span data-ttu-id="736ba-327">Par conséquent, la base de données lève une exception, qui est encapsulée en tant que `DbUpdateException` par SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="736ba-327">The database therefore throws an exception, which is wrapped as a `DbUpdateException` by SaveChanges.</span></span>

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="736ba-328">Relation facultative avec les dépendants/enfants non chargés</span><span class="sxs-lookup"><span data-stu-id="736ba-328">Optional relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="736ba-329">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="736ba-329">DeleteBehavior</span></span>    | <span data-ttu-id="736ba-330">En cas de suppression du principal/parent</span><span class="sxs-lookup"><span data-stu-id="736ba-330">On deleting principal/parent</span></span>             | <span data-ttu-id="736ba-331">Sur la séparation du principal/du parent</span><span class="sxs-lookup"><span data-stu-id="736ba-331">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="736ba-332">Cascade</span><span class="sxs-lookup"><span data-stu-id="736ba-332">Cascade</span></span>           | <span data-ttu-id="736ba-333">Dépendants supprimés par la base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-333">Dependents deleted by database</span></span>           | <span data-ttu-id="736ba-334">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-334">N/A</span></span>
| <span data-ttu-id="736ba-335">Restreindre</span><span class="sxs-lookup"><span data-stu-id="736ba-335">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="736ba-336">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-336">N/A</span></span>
| <span data-ttu-id="736ba-337">NoAction</span><span class="sxs-lookup"><span data-stu-id="736ba-337">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="736ba-338">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-338">N/A</span></span>
| <span data-ttu-id="736ba-339">SetNull</span><span class="sxs-lookup"><span data-stu-id="736ba-339">SetNull</span></span>           | <span data-ttu-id="736ba-340">Clés étrangères dépendant défini sur NULL par la base de données</span><span class="sxs-lookup"><span data-stu-id="736ba-340">Dependent FKs set to null by database</span></span>    | <span data-ttu-id="736ba-341">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-341">N/A</span></span>
| <span data-ttu-id="736ba-342">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="736ba-342">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="736ba-343">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-343">N/A</span></span>
| <span data-ttu-id="736ba-344">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="736ba-344">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="736ba-345">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-345">N/A</span></span>
| <span data-ttu-id="736ba-346">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="736ba-346">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="736ba-347">N/A</span><span class="sxs-lookup"><span data-stu-id="736ba-347">N/A</span></span>

<span data-ttu-id="736ba-348">Remarques :</span><span class="sxs-lookup"><span data-stu-id="736ba-348">Notes:</span></span>

- <span data-ttu-id="736ba-349">La séparation d’une relation n’est pas valide ici, car les objets dépendants/enfants ne sont pas chargés.</span><span class="sxs-lookup"><span data-stu-id="736ba-349">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="736ba-350">La valeur par défaut pour les relations facultatives comme celle-ci est `ClientSetNull` .</span><span class="sxs-lookup"><span data-stu-id="736ba-350">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="736ba-351">Les dépendants/enfants doivent être chargés pour éviter une exception de base de données, sauf si la base de données a été configurée pour mettre en cascade des suppressions ou des valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="736ba-351">Dependents/children must be loaded to avoid a database exception unless the database has been configured to cascade either deletes or nulls.</span></span>
