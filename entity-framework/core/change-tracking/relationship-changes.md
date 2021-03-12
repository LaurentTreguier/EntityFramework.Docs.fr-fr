---
title: Modification des clés étrangères et des navigations-EF Core
description: Comment modifier les relations entre les entités en manipulant les clés étrangères et les navigations
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: 5d3a68a40ed8bd112c19bdaed466071b03e30a66
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024405"
---
# <a name="changing-foreign-keys-and-navigations"></a><span data-ttu-id="39a2c-103">Modification des clés étrangères et des navigations</span><span class="sxs-lookup"><span data-stu-id="39a2c-103">Changing Foreign Keys and Navigations</span></span>

## <a name="overview-of-foreign-keys-and-navigations"></a><span data-ttu-id="39a2c-104">Vue d’ensemble des clés étrangères et des navigations</span><span class="sxs-lookup"><span data-stu-id="39a2c-104">Overview of foreign keys and navigations</span></span>

<span data-ttu-id="39a2c-105">Les relations dans un modèle de Entity Framework Core (EF Core) sont représentées à l’aide de clés étrangères (clés étrangères).</span><span class="sxs-lookup"><span data-stu-id="39a2c-105">Relationships in an Entity Framework Core (EF Core) model are represented using foreign keys (FKs).</span></span> <span data-ttu-id="39a2c-106">Un FK est constitué d’une ou de plusieurs propriétés sur l’entité dépendante ou enfant dans la relation.</span><span class="sxs-lookup"><span data-stu-id="39a2c-106">An FK consists of one or more properties on the dependent or child entity in the relationship.</span></span> <span data-ttu-id="39a2c-107">Cette entité dépendante/enfant est associée à une entité principale/parente lorsque les valeurs des propriétés de clé étrangère sur le dépendant/enfant correspondent aux valeurs des propriétés de remplacement ou de clé primaire (PK) sur le principal/parent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-107">This dependent/child entity is associated with a given principal/parent entity when the values of the foreign key properties on the dependent/child match the values of the alternate or primary key (PK) properties on the principal/parent.</span></span>

<span data-ttu-id="39a2c-108">Les clés étrangères sont un bon moyen de stocker et manipuler les relations dans la base de données, mais elles ne sont pas très conviviales lorsque vous utilisez plusieurs entités associées dans le code de l’application.</span><span class="sxs-lookup"><span data-stu-id="39a2c-108">Foreign keys are a good way to store and manipulate relationships in the database, but are not very friendly when working with multiple related entities in application code.</span></span> <span data-ttu-id="39a2c-109">Par conséquent, la plupart des modèles de EF Core couchent également des « navigations » sur la représentation FK.</span><span class="sxs-lookup"><span data-stu-id="39a2c-109">Therefore, most EF Core models also layer "navigations" over the FK representation.</span></span> <span data-ttu-id="39a2c-110">Les navigations forment des références/.NET C# entre des instances d’entité qui reflètent les associations trouvées en faisant correspondre les valeurs de clés étrangères aux valeurs de clés primaires ou secondaires.</span><span class="sxs-lookup"><span data-stu-id="39a2c-110">Navigations form C#/.NET references between entity instances that reflect the associations found by matching foreign key values to primary or alternate key values.</span></span>

<span data-ttu-id="39a2c-111">Les navigations peuvent être utilisées sur les deux côtés de la relation, sur un seul côté, ou pas du tout, laissant uniquement la propriété FK.</span><span class="sxs-lookup"><span data-stu-id="39a2c-111">Navigations can be used on both sides of the relationship, on one side only, or not at all, leaving only the FK property.</span></span> <span data-ttu-id="39a2c-112">La propriété FK peut être masquée en en faisant une [propriété Shadow](xref:core/modeling/shadow-properties).</span><span class="sxs-lookup"><span data-stu-id="39a2c-112">The FK property can be hidden by making it a [shadow property](xref:core/modeling/shadow-properties).</span></span> <span data-ttu-id="39a2c-113">Pour plus d’informations sur les relations de modélisation, consultez [relations](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="39a2c-113">See [Relationships](xref:core/modeling/relationships) for more information on modelling relationships.</span></span>

> [!TIP]
> <span data-ttu-id="39a2c-114">Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris.</span><span class="sxs-lookup"><span data-stu-id="39a2c-114">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="39a2c-115">Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="39a2c-115">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="39a2c-116">Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangingFKsAndNavigations).</span><span class="sxs-lookup"><span data-stu-id="39a2c-116">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangingFKsAndNavigations).</span></span>

### <a name="example-model"></a><span data-ttu-id="39a2c-117">Exemple de modèle</span><span class="sxs-lookup"><span data-stu-id="39a2c-117">Example model</span></span>

<span data-ttu-id="39a2c-118">Le modèle suivant contient quatre types d’entité avec des relations entre eux.</span><span class="sxs-lookup"><span data-stu-id="39a2c-118">The following model contains four entity types with relationships between them.</span></span> <span data-ttu-id="39a2c-119">Les commentaires dans le code indiquent les propriétés qui sont des clés étrangères, des clés primaires et des navigations.</span><span class="sxs-lookup"><span data-stu-id="39a2c-119">The comments in the code indicate which properties are foreign keys, primary keys, and navigations.</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public int Id { get; set; } // Primary key
    public byte[] Banner { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation

    public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
}

public class Tag
{
    public int Id { get; set; } // Primary key
    public string Text { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Model)]

<span data-ttu-id="39a2c-120">Les trois relations dans ce modèle sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="39a2c-120">The three relationships in this model are:</span></span>

- <span data-ttu-id="39a2c-121">Chaque blog peut avoir de nombreuses publications (un-à-plusieurs) :</span><span class="sxs-lookup"><span data-stu-id="39a2c-121">Each blog can have many posts (one-to-many):</span></span>
  - <span data-ttu-id="39a2c-122">`Blog` est le principal/le parent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-122">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="39a2c-123">`Post` est le dépendant/enfant.</span><span class="sxs-lookup"><span data-stu-id="39a2c-123">`Post` is the dependent/child.</span></span> <span data-ttu-id="39a2c-124">Elle contient la propriété FK `Post.BlogId` , dont la valeur doit correspondre à la `Blog.Id` valeur de clé primaire du blog associé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-124">It contains the FK property `Post.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="39a2c-125">`Post.Blog` est une navigation de référence à partir d’un billet vers le blog associé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-125">`Post.Blog` is a reference navigation from a post to the associated blog.</span></span> <span data-ttu-id="39a2c-126">`Post.Blog` est la navigation inverse de `Blog.Posts` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-126">`Post.Blog` is the inverse navigation for `Blog.Posts`.</span></span>
  - <span data-ttu-id="39a2c-127">`Blog.Posts` est une collection de navigation d’un blog vers toutes les publications associées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-127">`Blog.Posts` is a collection navigation from a blog to all the associated posts.</span></span> <span data-ttu-id="39a2c-128">`Blog.Posts` est la navigation inverse de `Post.Blog` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-128">`Blog.Posts` is the inverse navigation for `Post.Blog`.</span></span>
- <span data-ttu-id="39a2c-129">Chaque blog peut avoir une ou plusieurs ressources (un-à-un) :</span><span class="sxs-lookup"><span data-stu-id="39a2c-129">Each blog can have one assets (one-to-one):</span></span>
  - <span data-ttu-id="39a2c-130">`Blog` est le principal/le parent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-130">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="39a2c-131">`BlogAssets` est le dépendant/enfant.</span><span class="sxs-lookup"><span data-stu-id="39a2c-131">`BlogAssets` is the dependent/child.</span></span> <span data-ttu-id="39a2c-132">Elle contient la propriété FK `BlogAssets.BlogId` , dont la valeur doit correspondre à la `Blog.Id` valeur de clé primaire du blog associé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-132">It contains the FK property `BlogAssets.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="39a2c-133">`BlogAssets.Blog` est une référence pour la navigation entre les ressources et le blog associé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-133">`BlogAssets.Blog` is a reference navigation from the assets to the associated blog.</span></span> <span data-ttu-id="39a2c-134">`BlogAssets.Blog` est la navigation inverse de `Blog.Assets` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-134">`BlogAssets.Blog` is the inverse navigation for `Blog.Assets`.</span></span>
  - <span data-ttu-id="39a2c-135">`Blog.Assets` est une navigation de référence à partir du blog vers les ressources associées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-135">`Blog.Assets` is a reference navigation from the blog to the associated assets.</span></span> <span data-ttu-id="39a2c-136">`Blog.Assets` est la navigation inverse de `BlogAssets.Blog` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-136">`Blog.Assets` is the inverse navigation for `BlogAssets.Blog`.</span></span>
- <span data-ttu-id="39a2c-137">Chaque billet peut avoir plusieurs balises et chaque balise peut avoir de nombreuses publications (plusieurs-à-plusieurs) :</span><span class="sxs-lookup"><span data-stu-id="39a2c-137">Each post can have many tags and each tag can have many posts (many-to-many):</span></span>
  - <span data-ttu-id="39a2c-138">Les relations plusieurs-à-plusieurs représentent une couche supplémentaire sur les relations 2 1-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="39a2c-138">Many-to-many relationships are a further layer over two one-to-many relationships.</span></span> <span data-ttu-id="39a2c-139">Les relations plusieurs-à-plusieurs sont décrites plus loin dans ce document.</span><span class="sxs-lookup"><span data-stu-id="39a2c-139">Many-to-many relationships are covered later in this document.</span></span>
  - <span data-ttu-id="39a2c-140">`Post.Tags` est une collection qui fait partie d’une publication à toutes les balises associées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-140">`Post.Tags` is a collection navigation from a post to all the associated tags.</span></span> <span data-ttu-id="39a2c-141">`Post.Tags` est la navigation inverse de `Tag.Posts` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-141">`Post.Tags` is the inverse navigation for `Tag.Posts`.</span></span>
  - <span data-ttu-id="39a2c-142">`Tag.Posts` est une collection de collections à partir d’une balise vers toutes les publications associées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-142">`Tag.Posts` is a collection navigation from a tag to all the associated posts.</span></span> <span data-ttu-id="39a2c-143">`Tag.Posts` est la navigation inverse de `Post.Tags` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-143">`Tag.Posts` is the inverse navigation for `Post.Tags`.</span></span>

<span data-ttu-id="39a2c-144">Pour plus d’informations sur la façon de modéliser et de configurer des relations, consultez [relations](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="39a2c-144">See [Relationships](xref:core/modeling/relationships) for more information on how to model and configure relationships.</span></span>

## <a name="relationship-fixup"></a><span data-ttu-id="39a2c-145">Correction de la relation</span><span class="sxs-lookup"><span data-stu-id="39a2c-145">Relationship fixup</span></span>

<span data-ttu-id="39a2c-146">EF Core maintient les navigations en alignement avec les valeurs de clés étrangères et vice versa.</span><span class="sxs-lookup"><span data-stu-id="39a2c-146">EF Core keeps navigations in alignment with foreign key values and vice versa.</span></span> <span data-ttu-id="39a2c-147">Autrement dit, si une valeur de clé étrangère change de sorte qu’elle fait désormais référence à une autre entité principale/parente, les navigations sont mises à jour pour refléter cette modification.</span><span class="sxs-lookup"><span data-stu-id="39a2c-147">That is, if a foreign key value changes such that it now refers to a different principal/parent entity, then the navigations are updated to reflect this change.</span></span> <span data-ttu-id="39a2c-148">De même, si une navigation est modifiée, les valeurs de clé étrangère des entités impliquées sont mises à jour pour refléter cette modification.</span><span class="sxs-lookup"><span data-stu-id="39a2c-148">Likewise, if a navigation is changed, then the foreign key values of the entities involved are updated to reflect this change.</span></span> <span data-ttu-id="39a2c-149">C’est ce que l’on appelle la « correction de relation ».</span><span class="sxs-lookup"><span data-stu-id="39a2c-149">This is called "relationship fixup".</span></span>

### <a name="fixup-by-query"></a><span data-ttu-id="39a2c-150">Correction par requête</span><span class="sxs-lookup"><span data-stu-id="39a2c-150">Fixup by query</span></span>

<span data-ttu-id="39a2c-151">La correction se produit en premier lorsque les entités sont interrogées à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="39a2c-151">Fixup first occurs when entities are queried from the database.</span></span> <span data-ttu-id="39a2c-152">La base de données comporte uniquement des valeurs de clés étrangères. par conséquent, lorsque EF Core crée une instance d’entité à partir de la base de données, elle utilise les valeurs de clé étrangère pour définir des navigations de référence et ajouter des entités aux navigations de collection, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="39a2c-152">The database has only foreign key values, so when EF Core creates an entity instance from the database it uses the foreign key values to set reference navigations and add entities to collection navigations as appropriate.</span></span> <span data-ttu-id="39a2c-153">Par exemple, considérez une requête pour les blogs et les publications et les ressources associées :</span><span class="sxs-lookup"><span data-stu-id="39a2c-153">For example, consider a query for blogs and its associated posts and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

<span data-ttu-id="39a2c-154">Pour chaque blog, EF Core commence par créer une `Blog` instance.</span><span class="sxs-lookup"><span data-stu-id="39a2c-154">For each blog, EF Core will first create a `Blog` instance.</span></span> <span data-ttu-id="39a2c-155">Ensuite, à mesure que chaque publication est chargée à partir de la base de données, sa `Post.Blog` navigation de référence est configurée pour pointer vers le blog associé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-155">Then, as each post is loaded from the database its `Post.Blog` reference navigation is set to point to the associated blog.</span></span> <span data-ttu-id="39a2c-156">De même, la publication est ajoutée à la navigation dans la `Blog.Posts` collection.</span><span class="sxs-lookup"><span data-stu-id="39a2c-156">Likewise, the post is added to the `Blog.Posts` collection navigation.</span></span> <span data-ttu-id="39a2c-157">La même chose se produit avec `BlogAssets` , sauf dans ce cas, les deux navigations sont des références.</span><span class="sxs-lookup"><span data-stu-id="39a2c-157">The same thing happens with `BlogAssets`, except in this case both navigations are references.</span></span> <span data-ttu-id="39a2c-158">La `Blog.Assets` navigation est définie pour pointer vers l’instance de ressources, et la `BlogAsserts.Blog` navigation est définie pour pointer vers l’instance de blog.</span><span class="sxs-lookup"><span data-stu-id="39a2c-158">The `Blog.Assets` navigation is set to point to the assets instance, and the `BlogAsserts.Blog` navigation is set to point to the blog instance.</span></span>

<span data-ttu-id="39a2c-159">En examinant la [vue de débogage change tracker](xref:core/change-tracking/debug-views) après cette requête, vous voyez deux blogs, chacun avec une ressource et deux publications faisant l’objet d’un suivi :</span><span class="sxs-lookup"><span data-stu-id="39a2c-159">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after this query shows two blogs, each with one assets and two posts being tracked:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="39a2c-160">La vue déboguer affiche à la fois les valeurs de clés et les navigations.</span><span class="sxs-lookup"><span data-stu-id="39a2c-160">The debug view shows both key values and navigations.</span></span> <span data-ttu-id="39a2c-161">Les navigations sont affichées à l’aide des valeurs de clé primaire des entités associées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-161">Navigations are shown using the primary key values of the related entities.</span></span> <span data-ttu-id="39a2c-162">Par exemple, `Posts: [{Id: 1}, {Id: 2}]` dans la sortie ci-dessus indique que la navigation dans la `Blog.Posts` collection contient deux publications associées avec les clés primaires 1 et 2, respectivement.</span><span class="sxs-lookup"><span data-stu-id="39a2c-162">For example, `Posts: [{Id: 1}, {Id: 2}]` in the output above indicates that the `Blog.Posts` collection navigation contains two related posts with primary keys 1 and 2 respectively.</span></span> <span data-ttu-id="39a2c-163">De même, pour chaque publication associée au premier blog, la `Blog: {Id: 1}` ligne indique que la `Post.Blog` navigation fait référence au blog avec la clé primaire 1.</span><span class="sxs-lookup"><span data-stu-id="39a2c-163">Similarly, for each post associated with the first blog, the `Blog: {Id: 1}` line indicates that the `Post.Blog` navigation references the Blog with primary key 1.</span></span>

### <a name="fixup-to-locally-tracked-entities"></a><span data-ttu-id="39a2c-164">Correction des entités suivies localement</span><span class="sxs-lookup"><span data-stu-id="39a2c-164">Fixup to locally tracked entities</span></span>

<span data-ttu-id="39a2c-165">La correction de la relation se produit également entre les entités retournées à partir d’une requête de suivi et les entités déjà suivies par DbContext.</span><span class="sxs-lookup"><span data-stu-id="39a2c-165">Relationship fixup also happens between entities returned from a tracking query and entities already tracked by the DbContext.</span></span> <span data-ttu-id="39a2c-166">Par exemple, envisagez d’exécuter trois requêtes distinctes pour les blogs, les publications et les ressources :</span><span class="sxs-lookup"><span data-stu-id="39a2c-166">For example, consider executing three separate queries for blogs, posts, and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_2)]
<span data-ttu-id="39a2c-167">En regardant à nouveau dans les vues de débogage, après la première requête, seuls les deux blogs sont suivis :</span><span class="sxs-lookup"><span data-stu-id="39a2c-167">Looking again at the debug views, after the first query only the two blogs are tracked:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: []
```

<span data-ttu-id="39a2c-168">Les `Blog.Assets` navigations de référence sont NULL et les `Blog.Posts` navigations de collection sont vides, car aucune entité associée n’est actuellement suivie par le contexte.</span><span class="sxs-lookup"><span data-stu-id="39a2c-168">The `Blog.Assets` reference navigations are null, and the `Blog.Posts` collection navigations are empty because no associated entities are currently being tracked by the context.</span></span>

<span data-ttu-id="39a2c-169">Après la deuxième requête, les `Blogs.Assets` navigations de référence ont été corrigées pour pointer vers les instances qui viennent d’être suivies `BlogAsset` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-169">After the second query, the `Blogs.Assets` reference navigations have been fixed up to point to the newly tracked `BlogAsset` instances.</span></span> <span data-ttu-id="39a2c-170">De même, les `BlogAssets.Blog` navigations de référence sont définies de manière à pointer vers l’instance déjà suivie appropriée `Blog` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-170">Likewise, the `BlogAssets.Blog` reference navigations are set to point to the appropriate already tracked `Blog` instance.</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: []
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
```

<span data-ttu-id="39a2c-171">Enfin, après la troisième requête, les `Blog.Posts` navigations dans les collections contiennent désormais toutes les publications associées, et les `Post.Blog` références pointent vers l' `Blog` instance appropriée :</span><span class="sxs-lookup"><span data-stu-id="39a2c-171">Finally, after the third query, the `Blog.Posts` collection navigations now contain all related posts, and the `Post.Blog` references point to the appropriate `Blog` instance:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="39a2c-172">Il s’agit du même état final que celui obtenu avec la requête unique d’origine, dans la mesure où EF Core les navigations ont été corrigées à mesure que les entités étaient suivies, même en provenance de plusieurs requêtes différentes.</span><span class="sxs-lookup"><span data-stu-id="39a2c-172">This is the same end-state as was achieved with the original single query, since EF Core fixed up navigations as entities were tracked, even when coming from multiple different queries.</span></span>

> [!NOTE]
> <span data-ttu-id="39a2c-173">La correction ne provoque jamais le retour de plus de données de la base de données.</span><span class="sxs-lookup"><span data-stu-id="39a2c-173">Fixup never causes more data to be returned from the database.</span></span> <span data-ttu-id="39a2c-174">Il connecte uniquement les entités qui sont déjà retournées par la requête ou déjà suivies par DbContext.</span><span class="sxs-lookup"><span data-stu-id="39a2c-174">It only connects entities that are already returned by the query or already tracked by the DbContext.</span></span> <span data-ttu-id="39a2c-175">Pour plus d’informations sur la gestion des doublons lors de la sérialisation [d’entités, consultez résolution d’identité dans EF Core](xref:core/change-tracking/identity-resolution) .</span><span class="sxs-lookup"><span data-stu-id="39a2c-175">See [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution) for information about handling duplicates when serializing entities.</span></span>

## <a name="changing-relationships-using-navigations"></a><span data-ttu-id="39a2c-176">Modification des relations à l’aide de navigations</span><span class="sxs-lookup"><span data-stu-id="39a2c-176">Changing relationships using navigations</span></span>

<span data-ttu-id="39a2c-177">Le moyen le plus simple de modifier la relation entre deux entités consiste à manipuler une navigation, tout en laissant EF Core pour corriger les valeurs de navigation inverse et FK de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="39a2c-177">The easiest way to change the relationship between two entities is by manipulating a navigation, while leaving EF Core to fixup the inverse navigation and FK values appropriately.</span></span> <span data-ttu-id="39a2c-178">Pour ce faire :</span><span class="sxs-lookup"><span data-stu-id="39a2c-178">This can be done by:</span></span>

- <span data-ttu-id="39a2c-179">Ajout ou suppression d’une entité dans une navigation de collection.</span><span class="sxs-lookup"><span data-stu-id="39a2c-179">Adding or removing an entity from a collection navigation.</span></span>
- <span data-ttu-id="39a2c-180">Modification d’une navigation de référence pour pointer vers une autre entité ou définition de la valeur null.</span><span class="sxs-lookup"><span data-stu-id="39a2c-180">Changing a reference navigation to point to a different entity, or setting it to null.</span></span>

### <a name="adding-or-removing-from-collection-navigations"></a><span data-ttu-id="39a2c-181">Ajout ou suppression de navigation dans les collections</span><span class="sxs-lookup"><span data-stu-id="39a2c-181">Adding or removing from collection navigations</span></span>

<span data-ttu-id="39a2c-182">Par exemple, nous allons déplacer l’une des publications du blog Visual Studio vers le blog .NET.</span><span class="sxs-lookup"><span data-stu-id="39a2c-182">For example, let's move one of the posts from the Visual Studio blog to the .NET blog.</span></span> <span data-ttu-id="39a2c-183">Cela nécessite le chargement préalable des blogs et des publications, puis le déplacement de la publication à partir de la collection de navigation sur un blog vers la collection de navigation sur l’autre blog :</span><span class="sxs-lookup"><span data-stu-id="39a2c-183">This requires first loading the blogs and posts, and then moving the post from the navigation collection on one blog to the navigation collection on the other blog:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");
        var vsBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == "Visual Studio Blog");

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Changing_relationships_using_navigations_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_1)]

> [!TIP]
> <span data-ttu-id="39a2c-184">Un appel à <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> est nécessaire ici, car l’accès à la vue de débogage n’entraîne pas la [détection automatique des modifications](xref:core/change-tracking/change-detection).</span><span class="sxs-lookup"><span data-stu-id="39a2c-184">A call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is needed here because accessing the debug view does not cause [automatic detection of changes](xref:core/change-tracking/change-detection).</span></span>

<span data-ttu-id="39a2c-185">Il s’agit de l’affichage débogage imprimé après l’exécution du code ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="39a2c-185">This is the debug view printed after running the code above:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: [{Id: 4}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="39a2c-186">La `Blog.Posts` navigation sur le blog .net comporte désormais trois publications ( `Posts: [{Id: 1}, {Id: 2}, {Id: 3}]` ).</span><span class="sxs-lookup"><span data-stu-id="39a2c-186">The `Blog.Posts` navigation on the .NET Blog now has three posts (`Posts: [{Id: 1}, {Id: 2}, {Id: 3}]`).</span></span> <span data-ttu-id="39a2c-187">De même, la `Blog.Posts` navigation sur le blog Visual Studio n’a qu’une seule publication ( `Posts: [{Id: 4}]` ).</span><span class="sxs-lookup"><span data-stu-id="39a2c-187">Likewise, the `Blog.Posts` navigation on the Visual Studio blog only has one post (`Posts: [{Id: 4}]`).</span></span> <span data-ttu-id="39a2c-188">Cela est attendu, car le code a modifié explicitement ces collections.</span><span class="sxs-lookup"><span data-stu-id="39a2c-188">This is to be expected since the code explicitly changed these collections.</span></span>

<span data-ttu-id="39a2c-189">Plus intéressant, même si le code n’a pas modifié explicitement la `Post.Blog` navigation, il a été résolu pour pointer vers le blog Visual Studio ( `Blog: {Id: 1}` ).</span><span class="sxs-lookup"><span data-stu-id="39a2c-189">More interestingly, even though the code did not explicitly change the `Post.Blog` navigation, it has been fixed-up to point to the Visual Studio blog (`Blog: {Id: 1}`).</span></span> <span data-ttu-id="39a2c-190">En outre, la `Post.BlogId` valeur de clé étrangère a été mise à jour pour correspondre à la valeur de clé primaire du blog .net.</span><span class="sxs-lookup"><span data-stu-id="39a2c-190">Also, the `Post.BlogId` foreign key value has been updated to match the primary key value of the .NET blog.</span></span> <span data-ttu-id="39a2c-191">Cette modification apportée à la valeur FK dans est alors rendue persistante dans la base de données lorsque SaveChanges est appelé :</span><span class="sxs-lookup"><span data-stu-id="39a2c-191">This change to the FK value in then persisted to the database when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a><span data-ttu-id="39a2c-192">Modification des navigations de référence</span><span class="sxs-lookup"><span data-stu-id="39a2c-192">Changing reference navigations</span></span>

<span data-ttu-id="39a2c-193">Dans l’exemple précédent, une publication a été déplacée d’un blog à un autre en modifiant la navigation dans la collection des publications sur chaque blog.</span><span class="sxs-lookup"><span data-stu-id="39a2c-193">In the previous example, a post was moved from one blog to another by manipulating the collection navigation of posts on each blog.</span></span> <span data-ttu-id="39a2c-194">La même chose peut être obtenue en modifiant la `Post.Blog` navigation de référence pour qu’elle pointe vers le nouveau blog.</span><span class="sxs-lookup"><span data-stu-id="39a2c-194">The same thing can be achieved by instead changing the `Post.Blog` reference navigation to point to the new blog.</span></span> <span data-ttu-id="39a2c-195">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="39a2c-195">For example:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

<span data-ttu-id="39a2c-196">L’affichage débogage après cette modification est _exactement_ identique à celui de l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-196">The debug view after this change is _exactly the same_ as it was in the previous example.</span></span> <span data-ttu-id="39a2c-197">En effet, EF Core a détecté la modification de navigation de référence, puis a corrigé les navigations de collection et la valeur FK pour les faire correspondre.</span><span class="sxs-lookup"><span data-stu-id="39a2c-197">This because EF Core detected the reference navigation change and then fixed up the collection navigations and FK value to match.</span></span>

## <a name="changing-relationships-using-foreign-key-values"></a><span data-ttu-id="39a2c-198">Modification des relations à l’aide de valeurs de clés étrangères</span><span class="sxs-lookup"><span data-stu-id="39a2c-198">Changing relationships using foreign key values</span></span>

<span data-ttu-id="39a2c-199">Dans la section précédente, les relations étaient manipulées par des navigations laissant les valeurs de clé étrangère être mises à jour automatiquement.</span><span class="sxs-lookup"><span data-stu-id="39a2c-199">In the previous section, relationships were manipulated by navigations leaving foreign key values to be updated automatically.</span></span> <span data-ttu-id="39a2c-200">Il s’agit de la méthode recommandée pour manipuler les relations dans EF Core.</span><span class="sxs-lookup"><span data-stu-id="39a2c-200">This is the recommended way to manipulate relationships in EF Core.</span></span> <span data-ttu-id="39a2c-201">Toutefois, il est également possible de manipuler les valeurs FK directement.</span><span class="sxs-lookup"><span data-stu-id="39a2c-201">However, it is also possible to manipulate FK values directly.</span></span> <span data-ttu-id="39a2c-202">Par exemple, nous pouvons déplacer un billet d’un blog à un autre en modifiant la `Post.BlogId` valeur de clé étrangère :</span><span class="sxs-lookup"><span data-stu-id="39a2c-202">For example, we can move a post from one blog to another by changing the `Post.BlogId` foreign key value:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

<span data-ttu-id="39a2c-203">Notez que cela est très similaire à la modification de la navigation de référence, comme indiqué dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-203">Notice how this is very similar to changing the reference navigation, as shown in the previous example.</span></span>

<span data-ttu-id="39a2c-204">La vue déboguer après cette modification est à nouveau _exactement la même_ que celle des deux exemples précédents.</span><span class="sxs-lookup"><span data-stu-id="39a2c-204">The debug view after this change is again _exactly the same_ as was the case for the previous two examples.</span></span> <span data-ttu-id="39a2c-205">En effet, EF Core a détecté la modification de la valeur FK, puis a corrigé les navigations de référence et de collection pour les faire correspondre.</span><span class="sxs-lookup"><span data-stu-id="39a2c-205">This because EF Core detected the FK value change and then fixed up both the reference and collection navigations to match.</span></span>

> [!TIP]
> <span data-ttu-id="39a2c-206">N’écrivez pas de code pour manipuler toutes les navigations et valeurs FK chaque fois qu’une relation change.</span><span class="sxs-lookup"><span data-stu-id="39a2c-206">Do not write code to manipulate all navigations and FK values each time a relationship changes.</span></span> <span data-ttu-id="39a2c-207">Ce code est plus complexe et doit garantir des modifications cohérentes aux clés étrangères et aux navigations dans tous les cas.</span><span class="sxs-lookup"><span data-stu-id="39a2c-207">Such code is more complicated and must ensure consistent changes to foreign keys and navigations in every case.</span></span> <span data-ttu-id="39a2c-208">Si possible, manipulez simplement une seule navigation, ou peut-être les deux.</span><span class="sxs-lookup"><span data-stu-id="39a2c-208">If possible, just manipulate a single navigation, or maybe both navigations.</span></span> <span data-ttu-id="39a2c-209">Si nécessaire, manipulez simplement les valeurs FK.</span><span class="sxs-lookup"><span data-stu-id="39a2c-209">If needed, just manipulate FK values.</span></span> <span data-ttu-id="39a2c-210">Évitez de manipuler à la fois les navigations et les valeurs FK.</span><span class="sxs-lookup"><span data-stu-id="39a2c-210">Avoid manipulating both navigations and FK values.</span></span>

## <a name="fixup-for-added-or-deleted-entities"></a><span data-ttu-id="39a2c-211">Correction pour les entités ajoutées ou supprimées</span><span class="sxs-lookup"><span data-stu-id="39a2c-211">Fixup for added or deleted entities</span></span>

### <a name="adding-to-a-collection-navigation"></a><span data-ttu-id="39a2c-212">Ajouter à une navigation dans une collection</span><span class="sxs-lookup"><span data-stu-id="39a2c-212">Adding to a collection navigation</span></span>

<span data-ttu-id="39a2c-213">EF Core effectue les actions suivantes lorsqu’il [détecte](xref:core/change-tracking/change-detection) qu’une nouvelle entité dépendante/enfant a été ajoutée à une navigation dans une collection :</span><span class="sxs-lookup"><span data-stu-id="39a2c-213">EF Core performs the following actions when it [detects](xref:core/change-tracking/change-detection) that a new dependent/child entity has been added to a collection navigation:</span></span>

- <span data-ttu-id="39a2c-214">Si l’entité n’est pas suivie, elle est suivie.</span><span class="sxs-lookup"><span data-stu-id="39a2c-214">If the entity is not tracked, then it is tracked.</span></span> <span data-ttu-id="39a2c-215">(En général, l’entité est dans l' `Added` État.</span><span class="sxs-lookup"><span data-stu-id="39a2c-215">(The entity will usually be in the `Added` state.</span></span> <span data-ttu-id="39a2c-216">Toutefois, si le type d’entité est configuré pour utiliser des clés générées et que la valeur de clé primaire est définie, l’entité est suivie dans l' `Unchanged` État.)</span><span class="sxs-lookup"><span data-stu-id="39a2c-216">However, if the entity type is configured to use generated keys and the primary key value is set, then the entity is tracked in the `Unchanged` state.)</span></span>
- <span data-ttu-id="39a2c-217">Si l’entité est associée à un autre principal/parent, cette relation est interrompue.</span><span class="sxs-lookup"><span data-stu-id="39a2c-217">If the entity is associated with a different principal/parent, then that relationship is severed.</span></span>
- <span data-ttu-id="39a2c-218">L’entité est associée au principal/parent qui possède la navigation dans la collection.</span><span class="sxs-lookup"><span data-stu-id="39a2c-218">The entity becomes associated with the principal/parent that owns the collection navigation.</span></span>
- <span data-ttu-id="39a2c-219">Les navigations et les valeurs de clé étrangère sont fixes pour toutes les entités impliquées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-219">Navigations and foreign key values are fixed up for all entities involved.</span></span>

<span data-ttu-id="39a2c-220">En fonction de cela, nous pouvons voir que pour déplacer un billet d’un blog à un autre, nous n’avons pas besoin de le supprimer de l’ancienne navigation de la collection avant de l’ajouter au nouveau.</span><span class="sxs-lookup"><span data-stu-id="39a2c-220">Based on this we can see that to move a post from one blog to another we don't actually need to remove it from the old collection navigation before adding it to the new.</span></span> <span data-ttu-id="39a2c-221">Par conséquent, le code de l’exemple ci-dessus peut être modifié à partir de :</span><span class="sxs-lookup"><span data-stu-id="39a2c-221">So the code from the example above can be changed from:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

<span data-ttu-id="39a2c-222">Par :</span><span class="sxs-lookup"><span data-stu-id="39a2c-222">To:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

<span data-ttu-id="39a2c-223">EF Core constate que la publication a été ajoutée à un nouveau blog et la supprime automatiquement de la collection sur le premier blog.</span><span class="sxs-lookup"><span data-stu-id="39a2c-223">EF Core sees that the post has been added to a new blog and automatically removes it from the collection on the first blog.</span></span>

### <a name="removing-from-a-collection-navigation"></a><span data-ttu-id="39a2c-224">Supprimer d’une collection de navigation</span><span class="sxs-lookup"><span data-stu-id="39a2c-224">Removing from a collection navigation</span></span>

<span data-ttu-id="39a2c-225">La suppression d’une entité dépendante/enfant de la collection de navigation du principal/parent provoque le dépassement de la relation à ce principal/parent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-225">Removing a dependent/child entity from the collection navigation of the principal/parent causes severing of the relationship to that principal/parent.</span></span> <span data-ttu-id="39a2c-226">Ce qui se passe ensuite dépend du fait que la relation est facultative ou obligatoire.</span><span class="sxs-lookup"><span data-stu-id="39a2c-226">What happens next depends on whether the relationship is optional or required.</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="39a2c-227">Relations facultatives</span><span class="sxs-lookup"><span data-stu-id="39a2c-227">Optional relationships</span></span>

<span data-ttu-id="39a2c-228">Par défaut pour les relations facultatives, la valeur de clé étrangère est définie sur null.</span><span class="sxs-lookup"><span data-stu-id="39a2c-228">By default for optional relationships, the foreign key value is set to null.</span></span> <span data-ttu-id="39a2c-229">Cela signifie que le dépendant/enfant n’est plus associé à _un_ principal/parent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-229">This means that the dependent/child is no longer associated with _any_ principal/parent.</span></span> <span data-ttu-id="39a2c-230">Par exemple, nous allons charger un blog et publier, puis supprimer l’une des publications de la `Blog.Posts` barre de navigation de la collection :</span><span class="sxs-lookup"><span data-stu-id="39a2c-230">For example, let's load a blog and posts and then remove one of the posts from the `Blog.Posts` collection navigation:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

<span data-ttu-id="39a2c-231">Si vous examinez la [vue Debug change Tracking](xref:core/change-tracking/debug-views) après cette modification, vous voyez que :</span><span class="sxs-lookup"><span data-stu-id="39a2c-231">Looking at the [change tracking debug view](xref:core/change-tracking/debug-views) after this change shows that:</span></span>

- <span data-ttu-id="39a2c-232">La `Post.BlogId` valeur FK a été définie sur null ( `BlogId: <null> FK Modified Originally 1` )</span><span class="sxs-lookup"><span data-stu-id="39a2c-232">The `Post.BlogId` FK has been set to null (`BlogId: <null> FK Modified Originally 1`)</span></span>
- <span data-ttu-id="39a2c-233">La `Post.Blog` navigation de référence a été définie sur null ( `Blog: <null>` )</span><span class="sxs-lookup"><span data-stu-id="39a2c-233">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`)</span></span>
- <span data-ttu-id="39a2c-234">La publication a été supprimée de la navigation dans la `Blog.Posts` collection ( `Posts: [{Id: 1}]` )</span><span class="sxs-lookup"><span data-stu-id="39a2c-234">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`)</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="39a2c-235">Notez que la publication n’est _pas_ marquée comme `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-235">Notice that the post is _not_ marked as `Deleted`.</span></span> <span data-ttu-id="39a2c-236">Elle est marquée comme `Modified` afin que la valeur FK dans la base de données soit définie sur Null lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-236">It is marked as `Modified` so that the FK value in the database will be set to null when SaveChanges is called.</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="39a2c-237">Relations requises</span><span class="sxs-lookup"><span data-stu-id="39a2c-237">Required relationships</span></span>

<span data-ttu-id="39a2c-238">La définition de la valeur FK sur null n’est pas autorisée (et n’est généralement pas possible) pour les relations requises.</span><span class="sxs-lookup"><span data-stu-id="39a2c-238">Setting the FK value to null is not allowed (and is usually not possible) for required relationships.</span></span> <span data-ttu-id="39a2c-239">Par conséquent, le fait de découper une relation requise signifie que l’entité dépendante/enfant doit être reapparentée à un nouveau principal/parent, ou supprimée de la base de données lorsque SaveChanges est appelé pour éviter une violation de contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="39a2c-239">Therefore, severing a required relationship means that the dependent/child entity must be either re-parented to a new principal/parent, or removed from the database when SaveChanges is called to avoid a referential constraint violation.</span></span> <span data-ttu-id="39a2c-240">C’est ce que l’on appelle la « suppression des orphelins » et est le comportement par défaut dans EF Core pour les relations requises.</span><span class="sxs-lookup"><span data-stu-id="39a2c-240">This is known as "deleting orphans", and is the default behavior in EF Core for required relationships.</span></span>

<span data-ttu-id="39a2c-241">Par exemple, nous allons modifier la relation entre le blog et les publications, puis exécuter le même code que dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="39a2c-241">For example, let's change the relationship between blog and posts to be required and then run the same code as in the previous example:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

<span data-ttu-id="39a2c-242">Si vous examinez l’affichage débogage après cette modification, vous voyez que :</span><span class="sxs-lookup"><span data-stu-id="39a2c-242">Looking at the debug view after this change shows that:</span></span>

- <span data-ttu-id="39a2c-243">La publication a été marquée comme `Deleted` telle qu’elle sera supprimée de la base de données lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-243">The post has been marked as `Deleted` such that it will be deleted from the database when SaveChanges is called.</span></span>
- <span data-ttu-id="39a2c-244">La `Post.Blog` navigation de référence a été définie sur null ( `Blog: <null>` ).</span><span class="sxs-lookup"><span data-stu-id="39a2c-244">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`).</span></span>
- <span data-ttu-id="39a2c-245">La publication a été supprimée de la navigation dans la `Blog.Posts` collection ( `Posts: [{Id: 1}]` ).</span><span class="sxs-lookup"><span data-stu-id="39a2c-245">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`).</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="39a2c-246">Notez que le `Post.BlogId` reste inchangé, car pour une relation obligatoire, il ne peut pas avoir la valeur null.</span><span class="sxs-lookup"><span data-stu-id="39a2c-246">Notice that the `Post.BlogId` remains unchanged since for a required relationship it cannot be set to null.</span></span>

<span data-ttu-id="39a2c-247">L’appel de SaveChanges entraîne la suppression de la publication orpheline :</span><span class="sxs-lookup"><span data-stu-id="39a2c-247">Calling SaveChanges results in the orphaned post being deleted:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a><span data-ttu-id="39a2c-248">Supprimer le minutage des orphelins et le nouveau parenté</span><span class="sxs-lookup"><span data-stu-id="39a2c-248">Delete orphans timing and re-parenting</span></span>

<span data-ttu-id="39a2c-249">Par défaut, le marquage des orphelins `Deleted` se produit dès que le changement de relation est [détecté](xref:core/change-tracking/change-detection).</span><span class="sxs-lookup"><span data-stu-id="39a2c-249">By default, marking orphans as `Deleted` happens as soon as the relationship change is [detected](xref:core/change-tracking/change-detection).</span></span> <span data-ttu-id="39a2c-250">Toutefois, ce processus peut être retardé jusqu’à ce que SaveChanges soit réellement appelé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-250">However, this process can be delayed until SaveChanges is actually called.</span></span> <span data-ttu-id="39a2c-251">Cela peut être utile pour éviter d’effectuer des orphelins d’entités qui ont été supprimées d’un principal/parent, mais qui seront à nouveau apparentés à un nouveau principal/parent avant l’appel de SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="39a2c-251">This can be useful to avoid making orphans of entities that have been removed from one principal/parent, but will be re-parented with a new principal/parent before SaveChanges is called.</span></span> <span data-ttu-id="39a2c-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> est utilisé pour définir ce minutage.</span><span class="sxs-lookup"><span data-stu-id="39a2c-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> is used to set this timing.</span></span> <span data-ttu-id="39a2c-253">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="39a2c-253">For example:</span></span>

<!--
        context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_5)]

<span data-ttu-id="39a2c-254">Après la suppression de la publication de la première collection, l’objet n’est pas marqué comme `Deleted` dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-254">After removing the post from the first collection the object is not marked as `Deleted` as it was in the previous example.</span></span> <span data-ttu-id="39a2c-255">Au lieu de cela, EF Core effectue le suivi de la gravité de la relation, _même s’il s’agit d’une relation obligatoire_.</span><span class="sxs-lookup"><span data-stu-id="39a2c-255">Instead, EF Core is tracking that the relationship is severed _even though this is a required relationship_.</span></span> <span data-ttu-id="39a2c-256">(La valeur FK est considérée comme NULL par EF Core même s’il ne peut pas vraiment être null, car le type n’accepte pas la valeur null.</span><span class="sxs-lookup"><span data-stu-id="39a2c-256">(The FK value is considered null by EF Core even though it cannot really be null because the type is not nullable.</span></span> <span data-ttu-id="39a2c-257">C’est ce que l’on appelle « null conceptuel ».)</span><span class="sxs-lookup"><span data-stu-id="39a2c-257">This is known as a "conceptual null".)</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="39a2c-258">L’appel de SaveChanges à ce stade entraînerait la suppression de la publication orpheline.</span><span class="sxs-lookup"><span data-stu-id="39a2c-258">Calling SaveChanges at this time would result in the orphaned post being deleted.</span></span> <span data-ttu-id="39a2c-259">Toutefois, si tel est le cas dans l’exemple ci-dessus, la publication est associée à un nouveau blog avant l’appel de SaveChanges, puis elle est redéfinie de manière appropriée sur ce nouveau blog et n’est plus considérée comme orpheline :</span><span class="sxs-lookup"><span data-stu-id="39a2c-259">However, if as in the example above, post is associated with a new blog before SaveChanges is called, then it will be fixed up appropriately to that new blog and is no longer considered an orphan:</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

<span data-ttu-id="39a2c-260">SaveChanges appelée à ce stade met à jour la publication dans la base de données au lieu de la supprimer.</span><span class="sxs-lookup"><span data-stu-id="39a2c-260">SaveChanges called at this point will update the post in the database rather than deleting it.</span></span>

<span data-ttu-id="39a2c-261">Il est également possible de désactiver la suppression automatique des orphelins.</span><span class="sxs-lookup"><span data-stu-id="39a2c-261">It is also possible to turn off automatic deletion of orphans.</span></span> <span data-ttu-id="39a2c-262">Une exception se produit si SaveChanges est appelé alors qu’un orphelin est suivi.</span><span class="sxs-lookup"><span data-stu-id="39a2c-262">This will result in an exception if SaveChanges is called while an orphan is being tracked.</span></span> <span data-ttu-id="39a2c-263">Par exemple, le code suivant :</span><span class="sxs-lookup"><span data-stu-id="39a2c-263">For example, this code:</span></span>

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

<span data-ttu-id="39a2c-264">Lèvera cette exception :</span><span class="sxs-lookup"><span data-stu-id="39a2c-264">Will throw this exception:</span></span>

> <span data-ttu-id="39a2c-265">System. InvalidOperationException : l’association entre les entités’blog’et’poster’avec la valeur de clé' {BlogId : 1} 'a été interrompue, mais la relation est marquée comme obligatoire ou est implicitement requise, car la clé étrangère n’accepte pas les valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="39a2c-265">System.InvalidOperationException: The association between entities 'Blog' and 'Post' with the key value '{BlogId: 1}' has been severed, but the relationship is either marked as required or is implicitly required because the foreign key is not nullable.</span></span> <span data-ttu-id="39a2c-266">Si l’entité dépendante/enfant doit être supprimée lorsqu’une relation requise est rompue, configurez la relation pour qu’elle utilise des suppressions en cascade.</span><span class="sxs-lookup"><span data-stu-id="39a2c-266">If the dependent/child entity should be deleted when a required relationship is severed, configure the relationship to use cascade deletes.</span></span>

<span data-ttu-id="39a2c-267">La suppression des orphelins, ainsi que des suppressions en cascade, peut être forcée à tout moment en appelant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="39a2c-267">Deletion of orphans, as well as cascade deletes, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="39a2c-268">La combinaison de ce paramètre avec la définition de l’option supprimer le minutage orphelin pour `Never` garantit que les orphelins ne sont jamais supprimés, à moins que EF Core soit explicitement invité à le faire.</span><span class="sxs-lookup"><span data-stu-id="39a2c-268">Combining this with setting the delete orphan timing to `Never` will ensure orphans are never deleted unless EF Core is explicitly instructed to do so.</span></span>

### <a name="changing-a-reference-navigation"></a><span data-ttu-id="39a2c-269">Modification d’une navigation de référence</span><span class="sxs-lookup"><span data-stu-id="39a2c-269">Changing a reference navigation</span></span>

<span data-ttu-id="39a2c-270">La modification de la navigation de référence d’une relation un-à-plusieurs a le même effet que la modification de la navigation de la collection à l’autre extrémité de la relation.</span><span class="sxs-lookup"><span data-stu-id="39a2c-270">Changing the reference navigation of a one-to-many relationship has the same effect as changing the collection navigation on the other end of the relationship.</span></span> <span data-ttu-id="39a2c-271">L’affectation de la valeur null à la navigation de référence de dependent/Child équivaut à supprimer l’entité de la navigation dans la collection du principal/parent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-271">Setting the reference navigation of dependent/child to null is equivalent to removing the entity from the collection navigation of the principal/parent.</span></span> <span data-ttu-id="39a2c-272">Toutes les modifications de correction et de base de données sont effectuées comme décrit dans la section précédente, y compris la création de l’entité orpheline si la relation est requise.</span><span class="sxs-lookup"><span data-stu-id="39a2c-272">All fixup and database changes happen as described in the previous section, including making the entity an orphan if the relationship is required.</span></span>

#### <a name="optional-one-to-one-relationships"></a><span data-ttu-id="39a2c-273">Relations un-à-un facultatives</span><span class="sxs-lookup"><span data-stu-id="39a2c-273">Optional one-to-one relationships</span></span>

<span data-ttu-id="39a2c-274">Pour les relations un-à-un, la modification d’une navigation de référence provoque la gravité d’une relation précédente.</span><span class="sxs-lookup"><span data-stu-id="39a2c-274">For one-to-one relationships, changing a reference navigation causes any previous relationship to be severed.</span></span> <span data-ttu-id="39a2c-275">Pour les relations facultatives, cela signifie que la valeur FK sur le dépendant/enfant précédemment associé est définie sur null.</span><span class="sxs-lookup"><span data-stu-id="39a2c-275">For optional relationships, this means that the FK value on the previously related dependent/child is set to null.</span></span> <span data-ttu-id="39a2c-276">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="39a2c-276">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

<span data-ttu-id="39a2c-277">La vue déboguer avant d’appeler SaveChanges indique que les nouvelles ressources ont remplacé les ressources existantes, qui sont désormais marquées comme `Modified` avec une `BlogAssets.BlogId` valeur FK NULL :</span><span class="sxs-lookup"><span data-stu-id="39a2c-277">The debug view before calling SaveChanges shows that the new assets has replaced the existing assets, which is now marked as `Modified` with a null `BlogAssets.BlogId` FK value:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482629}
  Posts: []
BlogAssets {Id: -2147482629} Added
  Id: -2147482629 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Modified
  Id: 1 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 1
  Blog: <null>
```

<span data-ttu-id="39a2c-278">Il en résulte une mise à jour et une insertion lorsque SaveChanges est appelé :</span><span class="sxs-lookup"><span data-stu-id="39a2c-278">This results in an update and an insert when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Assets" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2=NULL, @p3='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p2, @p3);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

#### <a name="required-one-to-one-relationships"></a><span data-ttu-id="39a2c-279">Relations un-à-un requises</span><span class="sxs-lookup"><span data-stu-id="39a2c-279">Required one-to-one relationships</span></span>

<span data-ttu-id="39a2c-280">L’exécution du même code que dans l’exemple précédent, mais cette fois avec une relation un-à-un obligatoire, indique que le précédemment associé `BlogAssets` est désormais marqué comme `Deleted` , car il devient orphelin lorsque le nouveau devient `BlogAssets` son emplacement :</span><span class="sxs-lookup"><span data-stu-id="39a2c-280">Running the same code as in the previous example, but this time with a required one-to-one relationship, shows that the previously associated `BlogAssets` is now marked as `Deleted`, since it becomes an orphan when the new `BlogAssets` takes its place:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482639}
  Posts: []
BlogAssets {Id: -2147482639} Added
  Id: -2147482639 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Deleted
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: <null>
```

<span data-ttu-id="39a2c-281">Cela entraîne ensuite la suppression d’un et l’insertion lorsque SaveChanges est appelé :</span><span class="sxs-lookup"><span data-stu-id="39a2c-281">This then results in an delete an and insert when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Assets"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1=NULL, @p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p1, @p2);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="39a2c-282">Le minutage du marquage des orphelins comme supprimés peut être modifié de la même façon que pour les navigations dans les collections et a les mêmes effets.</span><span class="sxs-lookup"><span data-stu-id="39a2c-282">The timing of marking orphans as deleted can be changed in the same way as shown for collection navigations and has the same effects.</span></span>

### <a name="deleting-an-entity"></a><span data-ttu-id="39a2c-283">Suppression d’une entité</span><span class="sxs-lookup"><span data-stu-id="39a2c-283">Deleting an entity</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="39a2c-284">Relations facultatives</span><span class="sxs-lookup"><span data-stu-id="39a2c-284">Optional relationships</span></span>

<span data-ttu-id="39a2c-285">Lorsqu’une entité est marquée comme `Deleted` , par exemple en appelant <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> , les références à l’entité supprimée sont supprimées des navigations d’autres entités.</span><span class="sxs-lookup"><span data-stu-id="39a2c-285">When an entity is marked as `Deleted`, for example by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, then references to the deleted entity are removed from the navigations of other entities.</span></span> <span data-ttu-id="39a2c-286">Pour les relations facultatives, les valeurs FK dans les entités dépendantes sont définies sur null.</span><span class="sxs-lookup"><span data-stu-id="39a2c-286">For optional relationships, the FK values in dependent entities are set to null.</span></span>

<span data-ttu-id="39a2c-287">Par exemple, nous allons marquer le blog Visual Studio comme `Deleted` suit :</span><span class="sxs-lookup"><span data-stu-id="39a2c-287">For example, let's mark the Visual Studio blog as `Deleted`:</span></span>

<!--
        using var context = new BlogsContext();

        var vsBlog = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .Single(e => e.Name == "Visual Studio Blog");

        context.Remove(vsBlog);

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Deleting_an_entity_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Deleting_an_entity_1)]

<span data-ttu-id="39a2c-288">Si vous examinez l' [affichage de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) avant d’appeler SaveChanges, vous voyez :</span><span class="sxs-lookup"><span data-stu-id="39a2c-288">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows:</span></span>

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Modified
  Id: 2 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 2
  Blog: <null>
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
Post {Id: 4} Modified
  Id: 4 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="39a2c-289">Notez que :</span><span class="sxs-lookup"><span data-stu-id="39a2c-289">Notice that:</span></span>

- <span data-ttu-id="39a2c-290">Le blog est marqué comme `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-290">The blog is marked as `Deleted`.</span></span>
- <span data-ttu-id="39a2c-291">Les ressources associées au blog supprimé ont une valeur FK null ( `BlogId: <null> FK Modified Originally 2` ) et une navigation de référence null ( `Blog: <null>` )</span><span class="sxs-lookup"><span data-stu-id="39a2c-291">The assets related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>
- <span data-ttu-id="39a2c-292">Chaque publication associée au blog supprimé a une valeur FK null ( `BlogId: <null> FK Modified Originally 2` ) et une navigation de référence null ( `Blog: <null>` )</span><span class="sxs-lookup"><span data-stu-id="39a2c-292">Each post related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="39a2c-293">Relations requises</span><span class="sxs-lookup"><span data-stu-id="39a2c-293">Required relationships</span></span>

<span data-ttu-id="39a2c-294">Le comportement de correction pour les relations requises est le même que pour les relations facultatives, à ceci près que les entités dépendantes/enfants sont marquées comme `Deleted` étant donné qu’elles ne peuvent pas exister sans principal/parent et doivent être supprimées de la base de données lorsque SaveChanges est appelé pour éviter une exception de contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="39a2c-294">The fixup behavior for required relationships is the same as for optional relationships except that the dependent/child entities are marked as `Deleted` since they cannot exist without a principal/parent and must be removed from the database when SaveChanges is called to avoid a referential constraint exception.</span></span> <span data-ttu-id="39a2c-295">C’est ce que l’on appelle la « suppression en cascade » et est le comportement par défaut dans EF Core pour les relations requises.</span><span class="sxs-lookup"><span data-stu-id="39a2c-295">This is known as "cascade delete", and is the default behavior in EF Core for required relationships.</span></span> <span data-ttu-id="39a2c-296">Par exemple, l’exécution du même code que dans l’exemple précédent, mais avec une relation obligatoire, entraîne l’affichage de débogage suivant avant l’appel de SaveChanges :</span><span class="sxs-lookup"><span data-stu-id="39a2c-296">For example, running the same code as in the previous example but with a required relationship results in the following debug view before SaveChanges is called:</span></span>

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Deleted
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 3} Deleted
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="39a2c-297">Comme prévu, les dépendants/enfants sont maintenant marqués comme `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-297">As expected, the dependents/children are now marked as `Deleted`.</span></span> <span data-ttu-id="39a2c-298">Toutefois, Notez que les navigations sur les entités supprimées n’ont _pas_ changé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-298">However, notice that the navigations on the deleted entities have _not_ changed.</span></span> <span data-ttu-id="39a2c-299">Cela peut paraître étrange, mais elle évite de broyer complètement un graphique supprimé d’entités en effaçant toutes les navigations.</span><span class="sxs-lookup"><span data-stu-id="39a2c-299">This may seem strange, but it avoids completely shredding a deleted graph of entities by clearing all navigations.</span></span> <span data-ttu-id="39a2c-300">Autrement dit, le blog, la ressource et les publications forment toujours un graphique d’entités même après avoir été supprimé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-300">That is, the blog, asset, and posts still form a graph of entities even after having been deleted.</span></span> <span data-ttu-id="39a2c-301">Il est ainsi beaucoup plus facile de supprimer un graphique d’entités que le cas dans EF6 où le graphique a été broyé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-301">This makes it much easier to un-delete a graph of entities than was the case in EF6 where the graph was shredded.</span></span>

#### <a name="cascade-delete-timing-and-re-parenting"></a><span data-ttu-id="39a2c-302">Suppression en cascade du minutage et du nouveau parentage</span><span class="sxs-lookup"><span data-stu-id="39a2c-302">Cascade delete timing and re-parenting</span></span>

<span data-ttu-id="39a2c-303">Par défaut, la suppression en cascade se produit dès que le parent/principal est marqué comme `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-303">By default, cascade delete happens as soon as the parent/principal is marked as `Deleted`.</span></span> <span data-ttu-id="39a2c-304">Il s’agit de la même chose que pour la suppression des orphelins, comme décrit précédemment.</span><span class="sxs-lookup"><span data-stu-id="39a2c-304">This is the same as for deleting orphans, as described previously.</span></span> <span data-ttu-id="39a2c-305">Comme pour la suppression des orphelins, ce processus peut être retardé jusqu’à ce que SaveChanges soit appelé, voire totalement désactivé, en le définissant de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="39a2c-305">As with deleting orphans, this process can be delayed until SaveChanges is called, or even disabled entirely, by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> appropriately.</span></span> <span data-ttu-id="39a2c-306">Cela est utile de la même façon que pour la suppression des orphelins, y compris pour le nouveau parent des enfants/dépendants après la suppression d’un principal/parent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-306">This is useful in the same way as it is for deleting orphans, including for re-parenting children/dependents after deletion of a principal/parent.</span></span>

<span data-ttu-id="39a2c-307">Les suppressions en cascade, ainsi que la suppression des orphelins, peuvent être forcées à tout moment en appelant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="39a2c-307">Cascade deletes, as well as deleting orphans, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="39a2c-308">La combinaison de ce paramètre avec la définition du minutage de la suppression en cascade pour `Never` garantir que les suppressions en cascade ne se produiront jamais, à moins que EF Core soit explicitement invité à le faire.</span><span class="sxs-lookup"><span data-stu-id="39a2c-308">Combining this with setting the cascade delete timing to `Never` will ensure cascade deletes never happen unless EF Core is explicitly instructed to do so.</span></span>

> [!TIP]
> <span data-ttu-id="39a2c-309">La suppression en cascade et la suppression des orphelins sont étroitement liées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-309">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="39a2c-310">Les deux entraînent la suppression d’entités dépendantes/enfants lorsque la relation avec le principal/parent requis est interrompue.</span><span class="sxs-lookup"><span data-stu-id="39a2c-310">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="39a2c-311">Pour la suppression en cascade, ce problème se produit car le principal/le parent est lui-même supprimé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-311">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="39a2c-312">Pour les orphelins, l’entité principale/parente existe toujours, mais n’est plus liée aux entités dépendantes/enfants.</span><span class="sxs-lookup"><span data-stu-id="39a2c-312">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>

## <a name="many-to-many-relationships"></a><span data-ttu-id="39a2c-313">Relations Plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="39a2c-313">Many-to-many relationships</span></span>

<span data-ttu-id="39a2c-314">Les relations plusieurs-à-plusieurs dans EF Core sont implémentées à l’aide d’une entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="39a2c-314">Many-to-many relationships in EF Core are implemented using a join entity.</span></span> <span data-ttu-id="39a2c-315">Chaque côté de la relation plusieurs-à-plusieurs est lié à cette entité de jointure avec une relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="39a2c-315">Each side the many-to-many relationship is related to this join entity with a one-to-many relationship.</span></span> <span data-ttu-id="39a2c-316">Avant le EF Core 5,0, cette entité de jointure devait être explicitement définie et mappée.</span><span class="sxs-lookup"><span data-stu-id="39a2c-316">Before EF Core 5.0, this join entity had to explicitly defined and mapped.</span></span> <span data-ttu-id="39a2c-317">À compter de EF Core 5,0, il peut être créé implicitement et masqué.</span><span class="sxs-lookup"><span data-stu-id="39a2c-317">Starting with EF Core 5.0, it can be created implicitly and hidden.</span></span> <span data-ttu-id="39a2c-318">Toutefois, dans les deux cas, le comportement sous-jacent est le même.</span><span class="sxs-lookup"><span data-stu-id="39a2c-318">However, in both cases the underlying behavior is the same.</span></span> <span data-ttu-id="39a2c-319">Nous allons tout d’abord examiner ce comportement sous-jacent pour comprendre comment fonctionne le suivi des relations plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="39a2c-319">We will look at this underlying behavior first to understand how tracking of many-to-many relationships works.</span></span>

### <a name="how-many-to-many-relationships-work"></a><span data-ttu-id="39a2c-320">Fonctionnement des relations plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="39a2c-320">How many-to-many relationships work</span></span>

<span data-ttu-id="39a2c-321">Considérez cette EF Core modèle qui crée une relation plusieurs-à-plusieurs entre des publications et des balises à l’aide d’un type d’entité de jointure défini explicitement :</span><span class="sxs-lookup"><span data-stu-id="39a2c-321">Consider this EF Core model that creates a many-to-many relationship between posts and tags using an explicitly defined join entity type:</span></span>

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
    -->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Model)]

<span data-ttu-id="39a2c-322">Notez que le `PostTag` type d’entité de jointure contient deux propriétés de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="39a2c-322">Notice that the `PostTag` join entity type contains two foreign key properties.</span></span> <span data-ttu-id="39a2c-323">Dans ce modèle, pour une publication à associer à une balise, il doit y avoir une entité de jointure PostTag où la valeur de clé `PostTag.PostId` étrangère correspond à la `Post.Id` valeur de clé primaire, et où la valeur de clé `PostTag.TagId` étrangère correspond à la `Tag.Id` valeur de clé primaire.</span><span class="sxs-lookup"><span data-stu-id="39a2c-323">In this model, for a post to be related to a tag, there must be a PostTag join entity where the `PostTag.PostId` foreign key value matches the `Post.Id` primary key value, and where the `PostTag.TagId` foreign key value matches the `Tag.Id` primary key value.</span></span> <span data-ttu-id="39a2c-324">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="39a2c-324">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

<span data-ttu-id="39a2c-325">Si vous examinez la [vue de débogage change tracker](xref:core/change-tracking/debug-views) après l’exécution de ce code, vous voyez que la publication et la balise sont liées par la nouvelle `PostTag` entité de jointure :</span><span class="sxs-lookup"><span data-stu-id="39a2c-325">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after running this code shows that the post and tag are related by the new `PostTag` join entity:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
```

<span data-ttu-id="39a2c-326">Notez que les navigations de la collection sur `Post` et `Tag` ont été corrigées, de même que les navigations de référence `PostTag` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-326">Notice that the collection navigations on `Post` and `Tag` have been fixed up, as have the reference navigations on `PostTag`.</span></span> <span data-ttu-id="39a2c-327">Ces relations peuvent être manipulées par des navigations au lieu de valeurs FK, comme dans tous les exemples précédents.</span><span class="sxs-lookup"><span data-stu-id="39a2c-327">These relationships can be manipulated by navigations instead of FK values, just as in all the preceding examples.</span></span> <span data-ttu-id="39a2c-328">Par exemple, le code ci-dessus peut être modifié pour ajouter la relation en définissant les navigations de référence sur l’entité de jointure :</span><span class="sxs-lookup"><span data-stu-id="39a2c-328">For example, the code above can be modified to add the relationship by setting the reference navigations on the join entity:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

<span data-ttu-id="39a2c-329">Cela donne exactement la même modification à clés étrangères et aux navigations que dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-329">This results in exactly the same change to FKs and navigations as in the previous example.</span></span>

### <a name="skip-navigations"></a><span data-ttu-id="39a2c-330">Ignorer les navigations</span><span class="sxs-lookup"><span data-stu-id="39a2c-330">Skip navigations</span></span>

> [!NOTE]
> <span data-ttu-id="39a2c-331">Les navigations ignorées ont été introduites dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="39a2c-331">Skip navigations were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="39a2c-332">La manipulation manuelle de la table de jointure peut être lourde.</span><span class="sxs-lookup"><span data-stu-id="39a2c-332">Manipulating the join table manually can be cumbersome.</span></span> <span data-ttu-id="39a2c-333">À compter de EF Core 5,0, les relations plusieurs-à-plusieurs peuvent être manipulées directement à l’aide de navigations spéciales de collection qui « ignorent » l’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="39a2c-333">Starting with EF Core 5.0, many-to-many relationships can be manipulated directly using special collection navigations that "skip over" the join entity.</span></span> <span data-ttu-id="39a2c-334">Par exemple, deux navigations à ignorer peuvent être ajoutées au modèle ci-dessus. une de la publication aux balises, et l’autre de la balise à Posts :</span><span class="sxs-lookup"><span data-stu-id="39a2c-334">For example, two skip navigations can be added to the model above; one from Post to Tags, and the other from Tag to Posts:</span></span>

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Model)]

<span data-ttu-id="39a2c-335">Cette relation plusieurs-à-plusieurs requiert la configuration suivante pour s’assurer que les navigations ignorées et les navigations normales sont toutes utilisées pour la même relation plusieurs-à-plusieurs :</span><span class="sxs-lookup"><span data-stu-id="39a2c-335">This many-to-many relationship requires the following configuration to ensure the skip navigations and normal navigations are all used for the same many-to-many relationship:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne(t => t.Tag).WithMany(p => p.PostTags),
                    j => j.HasOne(t => t.Post).WithMany(p => p.PostTags));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=OnModelCreating)]

<span data-ttu-id="39a2c-336">Pour plus d’informations sur le mappage des relations plusieurs-à-plusieurs, consultez [relations](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="39a2c-336">See [Relationships](xref:core/modeling/relationships) for more information on mapping many-to-many relationships.</span></span>

<span data-ttu-id="39a2c-337">Ignorer les navigations et se comporter comme des navigations de collections normales.</span><span class="sxs-lookup"><span data-stu-id="39a2c-337">Skip navigations look and behave like normal collection navigations.</span></span> <span data-ttu-id="39a2c-338">Toutefois, la façon dont elles fonctionnent avec les valeurs de clés étrangères est différente.</span><span class="sxs-lookup"><span data-stu-id="39a2c-338">However, the way they work with foreign key values is different.</span></span> <span data-ttu-id="39a2c-339">Nous allons associer un billet à une balise, mais cette fois-ci à l’aide d’une navigation à ignorer :</span><span class="sxs-lookup"><span data-stu-id="39a2c-339">Let's associate a post with a tag, but this time using a skip navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

<span data-ttu-id="39a2c-340">Notez que ce code n’utilise pas l’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="39a2c-340">Notice that this code doesn't use the join entity.</span></span> <span data-ttu-id="39a2c-341">Au lieu de cela, il suffit d’ajouter une entité à une collection de navigation de la même façon que s’il s’agissait d’une relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="39a2c-341">It instead just adds an entity to a navigation collection in the same way as would be done if this were a one-to-many relationship.</span></span> <span data-ttu-id="39a2c-342">La vue de débogage résultante est essentiellement la même que précédemment :</span><span class="sxs-lookup"><span data-stu-id="39a2c-342">The resulting debug view is essentially the same as before:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
  Posts: [{Id: 3}]
```

<span data-ttu-id="39a2c-343">Notez qu’une instance de l' `PostTag` entité de jointure a été créée automatiquement avec les valeurs FK définies sur les valeurs de clé primaire de la balise et de la publication qui sont maintenant associées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-343">Notice that an instance of the `PostTag` join entity was created automatically with FK values set to the PK values of the tag and post that are now associated.</span></span> <span data-ttu-id="39a2c-344">Toutes les navigations de référence et de collection normales ont été résolues pour correspondre à ces valeurs FK.</span><span class="sxs-lookup"><span data-stu-id="39a2c-344">All the normal reference and collection navigations have been fixed up to match these FK values.</span></span> <span data-ttu-id="39a2c-345">En outre, étant donné que ce modèle contient des navigations ignorées, celles-ci ont également été corrigées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-345">Also, since this model contains skip navigations, these have also been fixed up.</span></span> <span data-ttu-id="39a2c-346">Plus précisément, même si nous avons ajouté la balise à la `Post.Tags` navigation ignorée, la `Tag.Posts` navigation par omission inverse de l’autre côté de cette relation a également été résolue pour contenir la publication associée.</span><span class="sxs-lookup"><span data-stu-id="39a2c-346">Specifically, even though we added the tag to the `Post.Tags` skip navigation, the `Tag.Posts` inverse skip navigation on the other side of this relationship has also been fixed up to contain the associated post.</span></span>

<span data-ttu-id="39a2c-347">Il est important de noter que les relations plusieurs-à-plusieurs sous-jacentes peuvent toujours être manipulées directement même lorsque les navigations ignorées ont été superposées en premier.</span><span class="sxs-lookup"><span data-stu-id="39a2c-347">It is worth noting that the underlying many-to-many relationships can still be manipulated directly even when skip navigations have been layered on top.</span></span> <span data-ttu-id="39a2c-348">Par exemple, la balise et la publication peuvent être associées comme nous l’avons fait avant d’introduire des navigations ignorées :</span><span class="sxs-lookup"><span data-stu-id="39a2c-348">For example, the tag and Post could be associated as we did before introducing skip navigations:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

<span data-ttu-id="39a2c-349">Ou utiliser les valeurs FK :</span><span class="sxs-lookup"><span data-stu-id="39a2c-349">Or using FK values:</span></span>

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

<span data-ttu-id="39a2c-350">Cela entraînera toujours la correction correcte des navigations ignorées, ce qui entraîne la sortie de la même vue de débogage que dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="39a2c-350">This will still result in the skip navigations being fixed up correctly, resulting in the same debug view output as in the previous example.</span></span>

### <a name="skip-navigations-only"></a><span data-ttu-id="39a2c-351">Ignorer les navigations uniquement</span><span class="sxs-lookup"><span data-stu-id="39a2c-351">Skip navigations only</span></span>

<span data-ttu-id="39a2c-352">Dans la section précédente, nous avons ajouté ignorer les navigations _, en plus de_ la définition complète des deux relations un-à-plusieurs sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="39a2c-352">In the previous section we added skip navigations _in addition to_ fully defining the two underlying one-to-many relationships.</span></span> <span data-ttu-id="39a2c-353">Cela est utile pour illustrer ce qui se passe aux valeurs FK, mais il est souvent inutile.</span><span class="sxs-lookup"><span data-stu-id="39a2c-353">This is useful to illustrate what happens to FK values, but is often unnecessary.</span></span> <span data-ttu-id="39a2c-354">Au lieu de cela, la relation plusieurs-à-plusieurs peut être définie en utilisant _uniquement des navigations ignorées_.</span><span class="sxs-lookup"><span data-stu-id="39a2c-354">Instead, the many-to-many relationship can be defined using _only skip navigations_.</span></span> <span data-ttu-id="39a2c-355">C’est ainsi que la relation plusieurs-à-plusieurs est définie dans le modèle, tout en haut de ce document.</span><span class="sxs-lookup"><span data-stu-id="39a2c-355">This is how the many-to-many relationship is defined in the model at the very top of this document.</span></span> <span data-ttu-id="39a2c-356">À l’aide de ce modèle, nous pouvons à nouveau associer un billet et une balise en ajoutant un billet à la `Tag.Posts` navigation ignorée (ou, alternativement, en ajoutant une balise à la `Post.Tags` navigation ignorée) :</span><span class="sxs-lookup"><span data-stu-id="39a2c-356">Using this model, we can again associate a Post and a Tag by adding a post to the `Tag.Posts` skip navigation (or, alternately, adding a tag to the `Post.Tags` skip navigation):</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

<span data-ttu-id="39a2c-357">Si vous examinez l’affichage débogage après avoir effectué cette modification, vous constatez que EF Core a créé une instance de `Dictionary<string, object>` pour représenter l’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="39a2c-357">Looking at the debug view after making this change reveals that EF Core has created an instance of `Dictionary<string, object>` to represent the join entity.</span></span> <span data-ttu-id="39a2c-358">Cette entité de jointure contient `PostsId` `TagsId` à la fois les propriétés de clé étrangère et celles qui ont été définies pour correspondre aux valeurs de clé primaire de la publication et de la balise associées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-358">This join entity contains both `PostsId` and `TagsId` foreign key properties which have been set to match the PK values of the post and tag that are associated.</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 1} Added
  PostsId: 3 PK FK
  TagsId: 1 PK FK
```

<span data-ttu-id="39a2c-359">Pour plus d’informations sur les entités de jointure implicite et l’utilisation des types d’entités, consultez [relations](xref:core/modeling/relationships) `Dictionary<string, object>` .</span><span class="sxs-lookup"><span data-stu-id="39a2c-359">See [Relationships](xref:core/modeling/relationships) for more information about implicit join entities and the use of `Dictionary<string, object>` entity types.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="39a2c-360">Le type CLR utilisé pour les types d’entité de jointure par convention peut changer dans les versions ultérieures pour améliorer les performances.</span><span class="sxs-lookup"><span data-stu-id="39a2c-360">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="39a2c-361">Ne dépendez pas du type de jointure `Dictionary<string, object>` , sauf si cette configuration a été explicitement configurée.</span><span class="sxs-lookup"><span data-stu-id="39a2c-361">Do not depend on the join type being `Dictionary<string, object>` unless this has been explicitly configured.</span></span>

### <a name="join-entities-with-payloads"></a><span data-ttu-id="39a2c-362">Joindre des entités avec des charges utiles</span><span class="sxs-lookup"><span data-stu-id="39a2c-362">Join entities with payloads</span></span>

<span data-ttu-id="39a2c-363">Jusqu’à présent, tous les exemples ont utilisé un type d’entité de jointure (explicite ou implicite) qui contient uniquement les deux propriétés de clé étrangère nécessaires pour la relation plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="39a2c-363">So far all the examples have used a join entity type (whether explicit or implicit) that contains only the two foreign key properties needed for the many-to-many relationship.</span></span> <span data-ttu-id="39a2c-364">Aucune de ces valeurs FK n’a besoin d’être explicitement définie par l’application lors de la manipulation des relations, car leurs valeurs proviennent des propriétés de clé primaire des entités associées.</span><span class="sxs-lookup"><span data-stu-id="39a2c-364">Neither of these FK values need to be explicitly set by the application when manipulating relationships because their values come from the primary key properties of the related entities.</span></span> <span data-ttu-id="39a2c-365">Cela permet à EF Core de créer des instances de l’entité de jointure sans données manquantes.</span><span class="sxs-lookup"><span data-stu-id="39a2c-365">This allows EF Core to create instances of the join entity without missing data.</span></span>

#### <a name="payloads-with-generated-values"></a><span data-ttu-id="39a2c-366">Charges utiles avec des valeurs générées</span><span class="sxs-lookup"><span data-stu-id="39a2c-366">Payloads with generated values</span></span>

<span data-ttu-id="39a2c-367">EF Core prend en charge l’ajout de propriétés supplémentaires au type d’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="39a2c-367">EF Core supports adding additional properties to the join entity type.</span></span> <span data-ttu-id="39a2c-368">C’est ce que l’on appelle donner à l’entité de jointure une « charge utile ».</span><span class="sxs-lookup"><span data-stu-id="39a2c-368">This is known as giving the join entity a "payload".</span></span> <span data-ttu-id="39a2c-369">Par exemple, nous allons ajouter `TaggedOn` la propriété à l' `PostTag` entité de jointure :</span><span class="sxs-lookup"><span data-stu-id="39a2c-369">For example, let's add `TaggedOn` property to the `PostTag` join entity:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

<span data-ttu-id="39a2c-370">Cette propriété de charge utile n’est pas définie quand EF Core crée une instance d’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="39a2c-370">This payload property will not be set when EF Core creates a join entity instance.</span></span> <span data-ttu-id="39a2c-371">La façon la plus courante de traiter ce problème consiste à utiliser des propriétés de charge utile avec des valeurs générées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="39a2c-371">The most common way to deal with this is to use payload properties with automatically generated values.</span></span> <span data-ttu-id="39a2c-372">Par exemple, la `TaggedOn` propriété peut être configurée pour utiliser un horodateur généré par le magasin lorsque chaque nouvelle entité est insérée :</span><span class="sxs-lookup"><span data-stu-id="39a2c-372">For example, the `TaggedOn` property can be configured to use a store-generated timestamp when each new entity is inserted:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany(),
                    j => j.Property(e => e.TaggedOn).HasDefaultValueSql("CURRENT_TIMESTAMP"));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=OnModelCreating)]

<span data-ttu-id="39a2c-373">Une publication peut désormais être marquée de la même façon qu’avant :</span><span class="sxs-lookup"><span data-stu-id="39a2c-373">A post can now be tagged in the same way as before:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

<span data-ttu-id="39a2c-374">Si vous examinez l' [affichage de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) après avoir appelé SaveChanges, la propriété de charge utile est correctement définie :</span><span class="sxs-lookup"><span data-stu-id="39a2c-374">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after calling SaveChanges shows that the payload property has been set appropriately:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Unchanged
  PostId: 3 PK FK
  TagId: 1 PK FK
  TaggedOn: '12/29/2020 8:13:21 PM'
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
```

#### <a name="explicitly-setting-payload-values"></a><span data-ttu-id="39a2c-375">Définition explicite des valeurs de charge utile</span><span class="sxs-lookup"><span data-stu-id="39a2c-375">Explicitly setting payload values</span></span>

<span data-ttu-id="39a2c-376">Après l’exemple précédent, nous allons ajouter une propriété de charge utile qui n’utilise pas de valeur générée automatiquement :</span><span class="sxs-lookup"><span data-stu-id="39a2c-376">Following on from the previous example, let's add a payload property that does not use an automatically generated value:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public DateTime TaggedOn { get; set; } // Auto-generated payload property
        public string TaggedBy { get; set; } // Not-generated payload property
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Model)]

<span data-ttu-id="39a2c-377">Une publication peut désormais être marquée de la même façon qu’avant, et l’entité de jointure est toujours créée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="39a2c-377">A post can now be tagged in the same way as before, and the join entity will still be created automatically.</span></span> <span data-ttu-id="39a2c-378">Cette entité est ensuite accessible à l’aide de l’un des mécanismes décrits dans [accès aux entités suivies](xref:core/change-tracking/entity-entries).</span><span class="sxs-lookup"><span data-stu-id="39a2c-378">This entity can then be accessed using one of the mechanisms described in [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="39a2c-379">Par exemple, le code ci-dessous utilise <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> pour accéder à l’instance d’entité de jointure :</span><span class="sxs-lookup"><span data-stu-id="39a2c-379">For example, the code below uses <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> to access the join entity instance:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();

            var joinEntity = context.Set<PostTag>().Find(post.Id, tag.Id);

            joinEntity.TaggedBy = "ajcvickers";

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_8](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_8)]

<span data-ttu-id="39a2c-380">Une fois l’entité de jointure localisée, elle peut être manipulée de manière normale (dans cet exemple, pour définir la `TaggedBy` propriété de charge utile avant d’appeler SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="39a2c-380">Once the join entity has been located it can be manipulated in the normal way--in this example, to set the `TaggedBy` payload property before calling SaveChanges.</span></span>

> [!NOTE]
> <span data-ttu-id="39a2c-381">Notez qu’un appel à <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> est requis ici pour permettre à EF Core de détecter la modification de la propriété de navigation et de créer l’instance de l’entité de jointure avant que `Find` ne soit utilisé.</span><span class="sxs-lookup"><span data-stu-id="39a2c-381">Note that a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is required here to give EF Core a chance to detect the navigation property change and create the join entity instance before `Find` is used.</span></span> <span data-ttu-id="39a2c-382">Pour plus d’informations, consultez Modification de la [détection et des notifications](xref:core/change-tracking/change-detection) .</span><span class="sxs-lookup"><span data-stu-id="39a2c-382">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information.</span></span>

<span data-ttu-id="39a2c-383">L’entité de jointure peut également être créée explicitement pour associer un billet à une balise.</span><span class="sxs-lookup"><span data-stu-id="39a2c-383">Alternately, the join entity can be created explicitly to associate a post with a tag.</span></span> <span data-ttu-id="39a2c-384">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="39a2c-384">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag()
            {
                PostId = post.Id,
                TagId = tag.Id,
                TaggedBy = "ajcvickers"
            });

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_9](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_9)]

<span data-ttu-id="39a2c-385">Enfin, une autre façon de définir des données de charge utile consiste à remplacer <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ou à utiliser l' <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> événement pour traiter les entités avant de mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="39a2c-385">Finally, another way to set payload data is by either overriding <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or using the <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> event to process entities before updating the database.</span></span> <span data-ttu-id="39a2c-386">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="39a2c-386">For example:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>())
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                }
            }

            return base.SaveChanges();
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=SaveChanges)]
