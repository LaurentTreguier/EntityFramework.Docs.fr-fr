---
title: Débogage du dispositif de suivi des modifications-EF Core
description: Utilisation du DebugView ChangeTracker et des messages du journal pour déboguer le suivi des modifications EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/debug-views
ms.openlocfilehash: cda81728beb5ffbc8604c191e84b3553a7343ec0
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023170"
---
# <a name="change-tracker-debugging"></a>Débogage du dispositif de suivi des modifications

Le dispositif de suivi des modifications de Entity Framework Core (EF Core) génère deux types de sortie pour faciliter le débogage :

- Le <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> fournit une vue explicite de toutes les entités faisant l’objet d’un suivi.
- Les messages de journal au niveau du débogage sont générés lorsque le dispositif de suivi des modifications détecte l’État et corrige les relations

> [!TIP]
> Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris. Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackerDebugging).

## <a name="change-tracker-debug-view"></a>Modifier l’affichage de débogage du dispositif de suivi

Vous pouvez accéder à la vue de débogage du dispositif de suivi des modifications dans le débogueur de votre IDE. Par exemple, avec Visual Studio :

![Accès à la vue de débogage du dispositif de suivi des modifications à partir du débogueur Visual Studio](_static/debug-view.png)

Elle est également accessible directement à partir du code, par exemple pour envoyer la vue de débogage à la console :

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.ShortView);
-->
[!code-csharp[Change_tracker_debug_view_1b](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1b)]

L’affichage débogage a une forme abrégée et une forme longue. La forme abrégée affiche les entités suivies, leur état et leurs valeurs de clés. La forme longue comprend également l’ensemble des valeurs de propriété et de navigation, ainsi que l’État.

### <a name="the-short-view"></a>Vue abrégée

Examinons un exemple de vue de débogage à l’aide du modèle indiqué à la fin de ce document. Tout d’abord, nous allons suivre certaines entités et les placer dans des États différents, de sorte que nous disposons de bonnes données de suivi des modifications pour afficher :

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts).ThenInclude(e => e.Tags)
            .Include(e => e.Assets)
            .ToList();

        // Mark something Added
        blogs[0].Posts.Add(
            new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many new features and..."
            });

        // Mark something Deleted
        blogs[1].Posts.Remove(blogs[1].Posts[1]);

        // Make something Modified
        blogs[0].Name = ".NET Blog (All new!)";

        context.ChangeTracker.DetectChanges();
-->
[!code-csharp[Change_tracker_debug_view_1a](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1a)]

L’impression de la vue brève à ce stade, comme indiqué ci-dessus, entraîne la sortie suivante :

```output
Blog {Id: 1} Modified AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}
Blog {Id: 2} Unchanged AK {AssetsId: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged FK {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged FK {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
Post {Id: -2147482643} Added FK {BlogId: 1}
Post {Id: 1} Unchanged FK {BlogId: 1}
Post {Id: 2} Unchanged FK {BlogId: 1}
Post {Id: 3} Unchanged FK {BlogId: 2}
Post {Id: 4} Deleted FK {BlogId: 2}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged FK {PostsId: 1} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged FK {PostsId: 1} FK {TagsId: 3}
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged FK {PostsId: 2} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged FK {PostsId: 3} FK {TagsId: 2}
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted FK {PostsId: 4} FK {TagsId: 2}
Tag {Id: 1} Unchanged
Tag {Id: 2} Unchanged
Tag {Id: 3} Unchanged
```

Remarques :

- Chaque entité suivie est listée avec sa valeur de clé primaire (PK). Par exemple : `Blog {Id: 1}`.
- Si l’entité est un type d’entité de type partagé, son type CLR est également indiqué. Par exemple : `PostTag (Dictionary<string, object>)`.
- Le <xref:Microsoft.EntityFrameworkCore.EntityState> s’affiche ensuite. Il peut s’agir de `Unchanged` , `Added` , `Modified` ou `Deleted` .
- Les valeurs des autres clés (AKs) sont affichées ci-dessous. Par exemple : `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.
- Enfin, les valeurs de n’importe quelle clé étrangère (clés étrangères) sont affichées. Par exemple : `FK {PostsId: 4} FK {TagsId: 2}`.

### <a name="the-long-view"></a>Vue longue

La vue longue peut être envoyée à la console de la même façon que la vue abrégée :

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracker_debug_view_1c](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1c)]

La sortie pour le même État que le petit affichage ci-dessus est la suivante :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK
  Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'
  Assets: {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  AssetsId: '3a54b880-2b9d-486b-9403-dc2e52d36d65' AK
  Name: 'Visual Studio Blog'
  Assets: {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
  Posts: [{Id: 3}]
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged
  Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK
  Banner: <null>
  Blog: {Id: 2}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged
  Id: 'ed727978-1ffe-4709-baee-73913e8e44a0' PK FK
  Banner: <null>
  Blog: {Id: 1}
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many new fe...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
  Tags: []
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: [{Id: 1}, {Id: 3}]
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: [{Id: 1}]
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: [{Id: 2}]
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: [{Id: 2}]
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged
  PostsId: 1 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged
  PostsId: 1 PK FK
  TagsId: 3 PK FK
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged
  PostsId: 2 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged
  PostsId: 3 PK FK
  TagsId: 2 PK FK
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted
  PostsId: 4 PK FK
  TagsId: 2 PK FK
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 1}, {Id: 2}]
Tag {Id: 2} Unchanged
  Id: 2 PK
  Text: 'Visual Studio'
  Posts: [{Id: 3}, {Id: 4}]
Tag {Id: 3} Unchanged
  Id: 3 PK
  Text: 'EF Core'
  Posts: [{Id: 1}]
```

Chaque entité suivie et son état s’affichent comme avant. Toutefois, l’affichage long affiche également les valeurs de propriété et de navigation.

#### <a name="property-values"></a>Valeurs de propriétés

Pour chaque propriété, l’affichage long indique si la propriété fait partie d’une clé primaire (PK), d’une clé secondaire (AK) ou d’une clé étrangère (FK). Par exemple :

- `Blog.Id` est une propriété de clé primaire : `Id: 1 PK`
- `Blog.AssetsId` est une propriété de clé secondaire : `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`
- `Post.BlogId` est une propriété de clé étrangère : `BlogId: 2 FK`
- `BlogAssets.Id` est une clé primaire et une propriété de clé étrangère : `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`

Les valeurs de propriété qui ont été modifiées sont marquées comme telles, et la valeur d’origine de la propriété est également indiquée. Par exemple : `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.

Enfin, `Added` les entités avec des valeurs de clé temporaires indiquent que la valeur est temporaire. Par exemple : `Id: -2147482643 PK Temporary`.

#### <a name="navigation-values"></a>Valeurs de navigation

Les valeurs de navigation sont affichées à l’aide des valeurs de clé primaire des entités référencées par les navigations. Par exemple, dans la sortie ci-dessus, le billet 3 est lié au blog 2. Cela signifie que la `Post.Blog` navigation pointe vers l' `Blog` instance avec l’ID 2. Cela s’affiche sous la forme `Blog: {Id: 2}` .

La même chose se produit pour les navigations de collection, sauf que dans ce cas, il peut y avoir plusieurs entités associées. Par exemple, la navigation dans la collection `Blog.Posts` contient trois entités, avec les valeurs de clé 1, 2 et-2147482643, respectivement. Cela s’affiche sous la forme `[{Id: 1}, {Id: 2}, {Id: -2147482643}]` .

## <a name="change-tracker-logging"></a>Journalisation du suivi des modifications

Le dispositif de suivi des modifications consigne les messages à `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> chaque fois qu’il [détecte des modifications de propriété ou de navigation](xref:core/change-tracking/debug-views). Par exemple, lorsque <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> est appelé dans le code en haut de ce document et que la [journalisation du débogage est activée](xref:core/logging-events-diagnostics/index), les journaux suivants sont générés :

```output
dbug: 12/30/2020 13:52:44.815 CoreEventId.DetectChangesStarting[10800] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges starting for 'BlogsContext'.
dbug: 12/30/2020 13:52:44.818 CoreEventId.PropertyChangeDetected[10802] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The unchanged property 'Blog.Name' was detected as changed from '.NET Blog' to '.NET Blog (All new!)' and will be marked as modified for entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.820 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Blog' entity with key '{Id: 1}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.821 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      1 entities were added and 0 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.ValueGenerated[10808] (Microsoft.EntityFrameworkCore.ChangeTracking)
      'BlogsContext' generated temporary value '-2147482638' for the property 'Id.Post'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.StartedTracking[10806] (Microsoft.EntityFrameworkCore.ChangeTracking)
      Context 'BlogsContext' started tracking 'Post' entity with key '{Id: -2147482638}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CascadeDeleteOrphan[10003] (Microsoft.EntityFrameworkCore.Update)
      An entity of type 'Post' with key '{Id: 4}' changed to 'Deleted' state due to severed required relationship to its parent entity of type 'Blog'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Modified' to 'Deleted'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.CascadeDelete[10002] (Microsoft.EntityFrameworkCore.Update)
      A cascade state change of an entity of type 'PostTag' with key '{PostsId: 4, TagsId: 2}' to 'Deleted' occurred due to the deletion of its parent entity of type 'Post' with key '{Id: 4}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'PostTag' entity with key '{PostsId: 4, TagsId: 2}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Deleted'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.DetectChangesCompleted[10801] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges completed for 'BlogsContext'.
```

Le tableau suivant récapitule les messages de journalisation du dispositif de suivi des modifications :

| ID de l’événement                                                                                                               | Description
|:-----------------------------------------------------------------------------------------------------------------------|----
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesStarting?displayProperty=nameWithType>        | <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> est en cours de démarrage
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesCompleted?displayProperty=nameWithType>       | <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> est terminée
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.PropertyChangeDetected?displayProperty=nameWithType>       | Une valeur de propriété normale a changé
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ForeignKeyChangeDetected?displayProperty=nameWithType>     | Une valeur de propriété de clé étrangère a été modifiée
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.CollectionChangeDetected?displayProperty=nameWithType>     | Des entités associées ont été ajoutées ou supprimées dans une navigation sans saut de collection.
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ReferenceChangeDetected?displayProperty=nameWithType>      | Une navigation de référence a été modifiée pour pointer vers une autre entité, ou a la valeur null
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StartedTracking?displayProperty=nameWithType>              | EF Core démarré le suivi d’une entité
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StateChanged?displayProperty=nameWithType>                 | Le <xref:Microsoft.EntityFrameworkCore.EntityState> d’une entité a changé
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ValueGenerated?displayProperty=nameWithType>               | Une valeur a été générée pour une propriété
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.SkipCollectionChangeDetected?displayProperty=nameWithType> | Des entités associées ont été ajoutées ou supprimées dans une navigation de collection ignorée.

## <a name="the-model"></a>Le modèle

Le modèle utilisé pour les exemples ci-dessus contient les types d’entités suivants :

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public Guid AssetsId { get; set; } // Alternate key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public Guid Id { get; set; } // Primary key and foreign key
    public byte[] Banner { get; set; }

    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; } // Foreign key
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
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Model)]

Le modèle est principalement configuré par Convention, avec seulement quelques lignes dans OnModelCreating :

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Blog>()
            .Property(e => e.AssetsId)
            .ValueGeneratedOnAdd();

        modelBuilder
            .Entity<BlogAssets>()
            .HasOne(e => e.Blog)
            .WithOne(e => e.Assets)
            .HasForeignKey<BlogAssets>(e => e.Id)
            .HasPrincipalKey<Blog>(e => e.AssetsId);
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=OnModelCreating)]
