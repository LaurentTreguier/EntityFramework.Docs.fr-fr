---
title: Change Tracking-EF Core
description: Vue d’ensemble du suivi des modifications pour EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: ccd14947cf53e3f8a38d2e0ddf2601b29e845775
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024444"
---
# <a name="change-tracking-in-ef-core"></a><span data-ttu-id="488b6-103">Change Tracking dans EF Core</span><span class="sxs-lookup"><span data-stu-id="488b6-103">Change Tracking in EF Core</span></span>

<span data-ttu-id="488b6-104">Chaque <xref:Microsoft.EntityFrameworkCore.DbContext> instance suit les modifications apportées aux entités.</span><span class="sxs-lookup"><span data-stu-id="488b6-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="488b6-105">Ces entités suivies entraînent à leur tour les modifications apportées à la base de données lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="488b6-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="488b6-106">Ce document présente une vue d’ensemble du suivi des modifications de Entity Framework Core (EF Core) et de son lien avec les requêtes et les mises à jour.</span><span class="sxs-lookup"><span data-stu-id="488b6-106">This document presents an overview of Entity Framework Core (EF Core) change tracking and how it relates to queries and updates.</span></span>

> [!TIP]
> <span data-ttu-id="488b6-107">Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span><span class="sxs-lookup"><span data-stu-id="488b6-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="488b6-108">Par souci de simplicité, ce document utilise et référence des méthodes synchrones, par exemple, <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> plutôt que leurs équivalents Async, tels que <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="488b6-108">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather than their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="488b6-109">L’appel et l’attente de la méthode Async peuvent être substitués sauf indication contraire.</span><span class="sxs-lookup"><span data-stu-id="488b6-109">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="how-to-track-entities"></a><span data-ttu-id="488b6-110">Comment suivre les entités</span><span class="sxs-lookup"><span data-stu-id="488b6-110">How to track entities</span></span>

<span data-ttu-id="488b6-111">Les instances d’entité sont suivies quand elles sont :</span><span class="sxs-lookup"><span data-stu-id="488b6-111">Entity instances become tracked when they are:</span></span>

- <span data-ttu-id="488b6-112">Retourné à partir d’une requête exécutée sur la base de données</span><span class="sxs-lookup"><span data-stu-id="488b6-112">Returned from a query executed against the database</span></span>
- <span data-ttu-id="488b6-113">Explicitement attaché à DbContext par des `Add` `Attach` `Update` méthodes similaires,, ou</span><span class="sxs-lookup"><span data-stu-id="488b6-113">Explicitly attached to the DbContext by `Add`, `Attach`, `Update`, or similar methods</span></span>
- <span data-ttu-id="488b6-114">Détecté comme de nouvelles entités connectées à des entités suivies existantes</span><span class="sxs-lookup"><span data-stu-id="488b6-114">Detected as new entities connected to existing tracked entities</span></span>

<span data-ttu-id="488b6-115">Les instances d’entité ne sont plus suivies quand :</span><span class="sxs-lookup"><span data-stu-id="488b6-115">Entity instances are no longer tracked when:</span></span>

- <span data-ttu-id="488b6-116">DbContext est supprimé</span><span class="sxs-lookup"><span data-stu-id="488b6-116">The DbContext is disposed</span></span>
- <span data-ttu-id="488b6-117">Le dispositif de suivi des modifications est effacé (EF Core 5,0 et versions ultérieures)</span><span class="sxs-lookup"><span data-stu-id="488b6-117">The change tracker is cleared (EF Core 5.0 and later)</span></span>
- <span data-ttu-id="488b6-118">Les entités sont détachées explicitement</span><span class="sxs-lookup"><span data-stu-id="488b6-118">The entities are explicitly detached</span></span>

<span data-ttu-id="488b6-119">DbContext est conçu pour représenter une unité de travail à courte durée de vie, comme décrit dans [initialisation et configuration de DbContext](xref:core/dbcontext-configuration/index).</span><span class="sxs-lookup"><span data-stu-id="488b6-119">DbContext is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="488b6-120">Cela signifie que la suppression de DbContext est _le moyen normal_ d’arrêter le suivi des entités.</span><span class="sxs-lookup"><span data-stu-id="488b6-120">This means that disposing the DbContext is _the normal way_ to stop tracking entities.</span></span> <span data-ttu-id="488b6-121">En d’autres termes, la durée de vie d’un DbContext doit être :</span><span class="sxs-lookup"><span data-stu-id="488b6-121">In other words, the lifetime of a DbContext should be:</span></span>

1. <span data-ttu-id="488b6-122">Créer l’instance DbContext</span><span class="sxs-lookup"><span data-stu-id="488b6-122">Create the DbContext instance</span></span>
2. <span data-ttu-id="488b6-123">Suivre certaines entités</span><span class="sxs-lookup"><span data-stu-id="488b6-123">Track some entities</span></span>
3. <span data-ttu-id="488b6-124">Apporter des modifications aux entités</span><span class="sxs-lookup"><span data-stu-id="488b6-124">Make some changes to the entities</span></span>
4. <span data-ttu-id="488b6-125">Appeler SaveChanges pour mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="488b6-125">Call SaveChanges to update the database</span></span>
5. <span data-ttu-id="488b6-126">Supprimer l’instance DbContext</span><span class="sxs-lookup"><span data-stu-id="488b6-126">Dispose the DbContext instance</span></span>

> [!TIP]
> <span data-ttu-id="488b6-127">Il n’est pas nécessaire d’effacer le dispositif de suivi des modifications ou de détacher explicitement les instances d’entité lorsque vous adoptez cette approche.</span><span class="sxs-lookup"><span data-stu-id="488b6-127">It is not necessary to clear the change tracker or explicitly detach entity instances when taking this approach.</span></span> <span data-ttu-id="488b6-128">Toutefois, si vous avez besoin de détacher des entités, l’appel de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> est plus efficace que le détachement d’entités un par un.</span><span class="sxs-lookup"><span data-stu-id="488b6-128">However, if you do need to detach entities, then calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> is more efficient than detaching entities one-by-one.</span></span>

## <a name="entity-states"></a><span data-ttu-id="488b6-129">États d’entité</span><span class="sxs-lookup"><span data-stu-id="488b6-129">Entity states</span></span>

<span data-ttu-id="488b6-130">Chaque entité est associée à un donné <xref:Microsoft.EntityFrameworkCore.EntityState> :</span><span class="sxs-lookup"><span data-stu-id="488b6-130">Every entity is associated with a given <xref:Microsoft.EntityFrameworkCore.EntityState>:</span></span>

- <span data-ttu-id="488b6-131">`Detached` les entités ne sont pas suivies par le <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="488b6-131">`Detached` entities are not being tracked by the <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
- <span data-ttu-id="488b6-132">`Added` les entités sont nouvelles et n’ont pas encore été insérées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="488b6-132">`Added` entities are new and have not yet been inserted into the database.</span></span> <span data-ttu-id="488b6-133">Cela signifie qu’ils seront insérés lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="488b6-133">This means they will be inserted when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>
- <span data-ttu-id="488b6-134">`Unchanged` les entités n’ont _pas_ été modifiées depuis la requête de la base de données.</span><span class="sxs-lookup"><span data-stu-id="488b6-134">`Unchanged` entities have _not_ been changed since they were queried from the database.</span></span> <span data-ttu-id="488b6-135">Toutes les entités retournées par les requêtes sont initialement dans cet État.</span><span class="sxs-lookup"><span data-stu-id="488b6-135">All entities returned from queries are initially in this state.</span></span>
- <span data-ttu-id="488b6-136">`Modified` les entités ont été modifiées depuis la requête de la base de données.</span><span class="sxs-lookup"><span data-stu-id="488b6-136">`Modified` entities have been changed since they were queried from the database.</span></span> <span data-ttu-id="488b6-137">Cela signifie qu’ils seront mis à jour lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="488b6-137">This means they will be updated when SaveChanges is called.</span></span>
- <span data-ttu-id="488b6-138">`Deleted` les entités existent dans la base de données, mais sont marquées comme devant être supprimées lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="488b6-138">`Deleted` entities exist in the database, but are marked to be deleted when SaveChanges is called.</span></span>

<span data-ttu-id="488b6-139">EF Core effectue le suivi des modifications au niveau de la propriété.</span><span class="sxs-lookup"><span data-stu-id="488b6-139">EF Core tracks changes at the property level.</span></span> <span data-ttu-id="488b6-140">Par exemple, si une seule valeur de propriété est modifiée, une mise à jour de base de données modifie uniquement cette valeur.</span><span class="sxs-lookup"><span data-stu-id="488b6-140">For example, if only a single property value is modified, then a database update will change only that value.</span></span> <span data-ttu-id="488b6-141">Toutefois, les propriétés ne peuvent être marquées comme modifiées que lorsque l’entité elle-même est dans l’état modifié.</span><span class="sxs-lookup"><span data-stu-id="488b6-141">However, properties can only be marked as modified when the entity itself is in the Modified state.</span></span> <span data-ttu-id="488b6-142">(Ou, d’un autre point de vue, l’état modifié signifie qu’au moins une valeur de propriété a été marquée comme modifiée.)</span><span class="sxs-lookup"><span data-stu-id="488b6-142">(Or, from an alternate perspective, the Modified state means that at least one property value has been marked as modified.)</span></span>

<span data-ttu-id="488b6-143">Le tableau suivant récapitule les différents États :</span><span class="sxs-lookup"><span data-stu-id="488b6-143">The following table summarizes the different states:</span></span>

| <span data-ttu-id="488b6-144">État des entités</span><span class="sxs-lookup"><span data-stu-id="488b6-144">Entity state</span></span>     | <span data-ttu-id="488b6-145">Suivi par DbContext</span><span class="sxs-lookup"><span data-stu-id="488b6-145">Tracked by DbContext</span></span> | <span data-ttu-id="488b6-146">Existe dans la base de données</span><span class="sxs-lookup"><span data-stu-id="488b6-146">Exists in database</span></span> | <span data-ttu-id="488b6-147">Propriétés modifiées</span><span class="sxs-lookup"><span data-stu-id="488b6-147">Properties modified</span></span> | <span data-ttu-id="488b6-148">Action sur SaveChanges</span><span class="sxs-lookup"><span data-stu-id="488b6-148">Action on SaveChanges</span></span>
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | <span data-ttu-id="488b6-149">Non</span><span class="sxs-lookup"><span data-stu-id="488b6-149">No</span></span>                   | -                  | -                   | -
| `Added`          | <span data-ttu-id="488b6-150">Oui</span><span class="sxs-lookup"><span data-stu-id="488b6-150">Yes</span></span>                  | <span data-ttu-id="488b6-151">Non</span><span class="sxs-lookup"><span data-stu-id="488b6-151">No</span></span>                 | -                   | <span data-ttu-id="488b6-152">Insérer</span><span class="sxs-lookup"><span data-stu-id="488b6-152">Insert</span></span>
| `Unchanged`      | <span data-ttu-id="488b6-153">Oui</span><span class="sxs-lookup"><span data-stu-id="488b6-153">Yes</span></span>                  | <span data-ttu-id="488b6-154">Oui</span><span class="sxs-lookup"><span data-stu-id="488b6-154">Yes</span></span>                | <span data-ttu-id="488b6-155">Non</span><span class="sxs-lookup"><span data-stu-id="488b6-155">No</span></span>                  | -
| `Modified`       | <span data-ttu-id="488b6-156">Oui</span><span class="sxs-lookup"><span data-stu-id="488b6-156">Yes</span></span>                  | <span data-ttu-id="488b6-157">Oui</span><span class="sxs-lookup"><span data-stu-id="488b6-157">Yes</span></span>                | <span data-ttu-id="488b6-158">Oui</span><span class="sxs-lookup"><span data-stu-id="488b6-158">Yes</span></span>                 | <span data-ttu-id="488b6-159">Update</span><span class="sxs-lookup"><span data-stu-id="488b6-159">Update</span></span>
| `Deleted`        | <span data-ttu-id="488b6-160">Oui</span><span class="sxs-lookup"><span data-stu-id="488b6-160">Yes</span></span>                  | <span data-ttu-id="488b6-161">Oui</span><span class="sxs-lookup"><span data-stu-id="488b6-161">Yes</span></span>                | -                   | <span data-ttu-id="488b6-162">Supprimer</span><span class="sxs-lookup"><span data-stu-id="488b6-162">Delete</span></span>

> [!NOTE]
> <span data-ttu-id="488b6-163">Ce texte utilise des termes de base de données relationnelle pour plus de clarté.</span><span class="sxs-lookup"><span data-stu-id="488b6-163">This text uses relational database terms for clarity.</span></span> <span data-ttu-id="488b6-164">Les bases de données NoSQL prennent généralement en charge des opérations similaires, mais éventuellement avec des noms différents.</span><span class="sxs-lookup"><span data-stu-id="488b6-164">NoSQL databases typically support similar operations but possibly with different names.</span></span> <span data-ttu-id="488b6-165">Pour plus d’informations, consultez la documentation de votre fournisseur de base de données.</span><span class="sxs-lookup"><span data-stu-id="488b6-165">Consult your database provider documentation for more information.</span></span>

## <a name="tracking-from-queries"></a><span data-ttu-id="488b6-166">Suivi à partir de requêtes</span><span class="sxs-lookup"><span data-stu-id="488b6-166">Tracking from queries</span></span>

<span data-ttu-id="488b6-167">EF Core le suivi des modifications fonctionne mieux quand la même <xref:Microsoft.EntityFrameworkCore.DbContext> instance est utilisée pour interroger des entités et les mettre à jour en appelant <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> .</span><span class="sxs-lookup"><span data-stu-id="488b6-167">EF Core change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="488b6-168">En effet, EF Core effectue automatiquement le suivi de l’état des entités interrogées, puis détecte toutes les modifications apportées à ces entités lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="488b6-168">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span>

<span data-ttu-id="488b6-169">Cette approche présente plusieurs avantages par rapport au [suivi explicite des instances d’entité](xref:core/change-tracking/explicit-tracking):</span><span class="sxs-lookup"><span data-stu-id="488b6-169">This approach has several advantages over [explicitly tracking entity instances](xref:core/change-tracking/explicit-tracking):</span></span>

- <span data-ttu-id="488b6-170">Elle est simple.</span><span class="sxs-lookup"><span data-stu-id="488b6-170">It is simple.</span></span> <span data-ttu-id="488b6-171">Les États d’entité doivent rarement être manipulés explicitement--EF Core prend en charge les changements d’État.</span><span class="sxs-lookup"><span data-stu-id="488b6-171">Entity states rarely need to be manipulated explicitly--EF Core takes care of state changes.</span></span>
- <span data-ttu-id="488b6-172">Les mises à jour sont limitées uniquement aux valeurs qui ont été réellement modifiées.</span><span class="sxs-lookup"><span data-stu-id="488b6-172">Updates are limited to only those values that have actually changed.</span></span>
- <span data-ttu-id="488b6-173">Les valeurs des [Propriétés Shadow](xref:core/modeling/shadow-properties) sont conservées et utilisées en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="488b6-173">The values of [shadow properties](xref:core/modeling/shadow-properties) are preserved and used as needed.</span></span> <span data-ttu-id="488b6-174">Cela s’avère particulièrement utile lorsque les clés étrangères sont stockées dans un état d’ombre.</span><span class="sxs-lookup"><span data-stu-id="488b6-174">This is especially relevant when foreign keys are stored in shadow state.</span></span>
- <span data-ttu-id="488b6-175">Les valeurs d’origine des propriétés sont conservées automatiquement et utilisées pour des mises à jour efficaces.</span><span class="sxs-lookup"><span data-stu-id="488b6-175">The original values of properties are preserved automatically and used for efficient updates.</span></span>

## <a name="simple-query-and-update"></a><span data-ttu-id="488b6-176">Requête et mise à jour simples</span><span class="sxs-lookup"><span data-stu-id="488b6-176">Simple query and update</span></span>

<span data-ttu-id="488b6-177">Par exemple, considérez un modèle de blog/billet simple :</span><span class="sxs-lookup"><span data-stu-id="488b6-177">For example, consider a simple blog/posts model:</span></span>

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

<span data-ttu-id="488b6-178">Nous pouvons utiliser ce modèle pour rechercher des blogs et des publications, puis mettre à jour la base de données :</span><span class="sxs-lookup"><span data-stu-id="488b6-178">We can use this model to query for blogs and posts and then make some updates to the database:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

<span data-ttu-id="488b6-179">L’appel de SaveChanges produit les mises à jour de base de données suivantes, à l’aide de SQLite comme exemple de base de données :</span><span class="sxs-lookup"><span data-stu-id="488b6-179">Calling SaveChanges results in the following database updates, using SQLite as an example database:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

<span data-ttu-id="488b6-180">La [vue de débogage du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications est un excellent moyen de visualiser les entités qui font l’objet d’un suivi et leurs États.</span><span class="sxs-lookup"><span data-stu-id="488b6-180">The [change tracker debug view](xref:core/change-tracking/debug-views) is a great way visualize which entities are being tracked and what their states are.</span></span> <span data-ttu-id="488b6-181">Par exemple, insérez le code suivant dans l’exemple ci-dessus avant d’appeler SaveChanges :</span><span class="sxs-lookup"><span data-stu-id="488b6-181">For example, inserting the following code into the sample above before calling SaveChanges:</span></span>

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

<span data-ttu-id="488b6-182">Génère la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="488b6-182">Generates the following output:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="488b6-183">Notez spécifiquement :</span><span class="sxs-lookup"><span data-stu-id="488b6-183">Notice specifically:</span></span>

- <span data-ttu-id="488b6-184">La `Blog.Name` propriété est marquée comme modifiée ( `Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'` ), ce qui entraîne l’état du blog `Modified` .</span><span class="sxs-lookup"><span data-stu-id="488b6-184">The `Blog.Name` property is marked as modified (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`), and this results in the blog being in the `Modified` state.</span></span>
- <span data-ttu-id="488b6-185">La `Post.Title` propriété de la publication 2 est marquée comme modifiée ( `Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'` ), ce qui entraîne l’état de cette publication `Modified` .</span><span class="sxs-lookup"><span data-stu-id="488b6-185">The `Post.Title` property of post 2 is marked as modified (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`), and this results in this post being in the `Modified` state.</span></span>
- <span data-ttu-id="488b6-186">Les autres valeurs de propriété de publication 2 n’ont pas changé et ne sont donc pas marquées comme modifiées.</span><span class="sxs-lookup"><span data-stu-id="488b6-186">The other property values of post 2 have not changed and are therefore not marked as modified.</span></span> <span data-ttu-id="488b6-187">C’est pourquoi ces valeurs ne sont pas incluses dans la mise à jour de la base de données.</span><span class="sxs-lookup"><span data-stu-id="488b6-187">This is why these values are not included in the database update.</span></span>
- <span data-ttu-id="488b6-188">L’autre publication n’a pas été modifiée de quelque manière que ce soit.</span><span class="sxs-lookup"><span data-stu-id="488b6-188">The other post was not modified in any way.</span></span> <span data-ttu-id="488b6-189">C’est pourquoi il est toujours dans l' `Unchanged` État et n’est pas inclus dans la mise à jour de la base de données.</span><span class="sxs-lookup"><span data-stu-id="488b6-189">This is why it is still in the `Unchanged` state and is not included in the database update.</span></span>

## <a name="query-then-insert-update-and-delete"></a><span data-ttu-id="488b6-190">Requête, insérer, mettre à jour et supprimer</span><span class="sxs-lookup"><span data-stu-id="488b6-190">Query then insert, update, and delete</span></span>

<span data-ttu-id="488b6-191">Les mises à jour telles que celles de l’exemple précédent peuvent être combinées avec des insertions et des suppressions dans la même unité de travail.</span><span class="sxs-lookup"><span data-stu-id="488b6-191">Updates like those in the previous example can be combined with inserts and deletes in the same unit-of-work.</span></span> <span data-ttu-id="488b6-192">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="488b6-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

<span data-ttu-id="488b6-193">Dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="488b6-193">In this example:</span></span>

- <span data-ttu-id="488b6-194">Un blog et les publications associées sont interrogés à partir de la base de données et suivis</span><span class="sxs-lookup"><span data-stu-id="488b6-194">A blog and related posts are queried from the database and tracked</span></span>
- <span data-ttu-id="488b6-195">La `Blog.Name` propriété a été modifiée.</span><span class="sxs-lookup"><span data-stu-id="488b6-195">The `Blog.Name` property is changed</span></span>
- <span data-ttu-id="488b6-196">Une nouvelle publication est ajoutée à la collection de publications existantes pour le blog</span><span class="sxs-lookup"><span data-stu-id="488b6-196">A new post is added to the collection of existing posts for the blog</span></span>
- <span data-ttu-id="488b6-197">Une publication existante est marquée pour suppression en appelant <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="488b6-197">An existing post is marked for deletion by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="488b6-198">Si vous revoyez la [vue debug du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications avant d’appeler SaveChanges, vous voyez comment EF Core effectue le suivi de ces modifications :</span><span class="sxs-lookup"><span data-stu-id="488b6-198">Looking again at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows how EF Core is tracking these changes:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="488b6-199">Notez que :</span><span class="sxs-lookup"><span data-stu-id="488b6-199">Notice that:</span></span>

- <span data-ttu-id="488b6-200">Le blog est marqué comme `Modified` .</span><span class="sxs-lookup"><span data-stu-id="488b6-200">The blog is marked as `Modified`.</span></span> <span data-ttu-id="488b6-201">Cette opération génère une mise à jour de la base de données.</span><span class="sxs-lookup"><span data-stu-id="488b6-201">This will generate a database update.</span></span>
- <span data-ttu-id="488b6-202">La publication 2 est marquée comme `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="488b6-202">Post 2 is marked as `Deleted`.</span></span> <span data-ttu-id="488b6-203">Cela génère une suppression de base de données.</span><span class="sxs-lookup"><span data-stu-id="488b6-203">This will generate a database delete.</span></span>
- <span data-ttu-id="488b6-204">Une nouvelle publication avec un ID temporaire est associée au blog 1 et est marquée comme `Added` .</span><span class="sxs-lookup"><span data-stu-id="488b6-204">A new post with a temporary ID is associated with blog 1 and is marked as `Added`.</span></span> <span data-ttu-id="488b6-205">Cela génère une insertion de base de données.</span><span class="sxs-lookup"><span data-stu-id="488b6-205">This will generate a database insert.</span></span>

<span data-ttu-id="488b6-206">Cela se traduit par les commandes de base de données suivantes (à l’aide de SQLite) lorsque SaveChanges est appelé :</span><span class="sxs-lookup"><span data-stu-id="488b6-206">This results in the following database commands (using SQLite) when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="488b6-207">Pour plus d’informations sur l’insertion et la suppression d’entités, consultez [suivi explicite des entités](xref:core/change-tracking/explicit-tracking) .</span><span class="sxs-lookup"><span data-stu-id="488b6-207">See [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking) for more information on inserting and deleting entities.</span></span> <span data-ttu-id="488b6-208">Consultez [modification de la détection et des notifications](xref:core/change-tracking/change-detection) pour plus d’informations sur la façon dont EF Core détecte automatiquement les modifications comme suit.</span><span class="sxs-lookup"><span data-stu-id="488b6-208">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information on how EF Core automatically detects changes like this.</span></span>

> [!TIP]
> <span data-ttu-id="488b6-209">Appelez <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> pour déterminer si des modifications ont été apportées pour que SaveChanges fasse des mises à jour de la base de données.</span><span class="sxs-lookup"><span data-stu-id="488b6-209">Call <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> to determine whether any changes have been made that will cause SaveChanges to make updates to the database.</span></span> <span data-ttu-id="488b6-210">Si HasChanges retourne false, alors SaveChanges sera une absence d’opération.</span><span class="sxs-lookup"><span data-stu-id="488b6-210">If HasChanges return false, then SaveChanges will be a no-op.</span></span>
