---
title: Modification des clés étrangères et des navigations-EF Core
description: Comment modifier les relations entre les entités en manipulant les clés étrangères et les navigations
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: b1ebe77ed29291beeef3708b603db026c38bbbec
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983610"
---
# <a name="changing-foreign-keys-and-navigations"></a>Modification des clés étrangères et des navigations

## <a name="overview-of-foreign-keys-and-navigations"></a>Vue d’ensemble des clés étrangères et des navigations

Les relations dans un modèle de Entity Framework Core (EF Core) sont représentées à l’aide de clés étrangères (clés étrangères). Un FK est constitué d’une ou de plusieurs propriétés sur l’entité dépendante ou enfant dans la relation. Cette entité dépendante/enfant est associée à une entité principale/parente lorsque les valeurs des propriétés de clé étrangère sur le dépendant/enfant correspondent aux valeurs des propriétés de remplacement ou de clé primaire (PK) sur le principal/parent.

Les clés étrangères sont un bon moyen de stocker et manipuler les relations dans la base de données, mais elles ne sont pas très conviviales lorsque vous utilisez plusieurs entités associées dans le code de l’application. Par conséquent, la plupart des modèles de EF Core couchent également des « navigations » sur la représentation FK. Les navigations forment des références/.NET C# entre des instances d’entité qui reflètent les associations trouvées en faisant correspondre les valeurs de clés étrangères aux valeurs de clés primaires ou secondaires.

Les navigations peuvent être utilisées sur les deux côtés de la relation, sur un seul côté, ou pas du tout, laissant uniquement la propriété FK. La propriété FK peut être masquée en en faisant une [propriété Shadow](xref:core/modeling/shadow-properties). Pour plus d’informations sur les relations de modélisation, consultez [relations](xref:core/modeling/relationships) .

> [!TIP]
> Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris. Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations).

### <a name="example-model"></a>Exemple de modèle

Le modèle suivant contient quatre types d’entité avec des relations entre eux. Les commentaires dans le code indiquent les propriétés qui sont des clés étrangères, des clés primaires et des navigations.

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

Les trois relations dans ce modèle sont les suivantes :

- Chaque blog peut avoir de nombreuses publications (un-à-plusieurs) :
  - `Blog` est le principal/le parent.
  - `Post` est le dépendant/enfant. Elle contient la propriété FK `Post.BlogId` , dont la valeur doit correspondre à la `Blog.Id` valeur de clé primaire du blog associé.
  - `Post.Blog` est une navigation de référence à partir d’un billet vers le blog associé. `Post.Blog` est la navigation inverse de `Blog.Posts` .
  - `Blog.Posts` est une collection de navigation d’un blog vers toutes les publications associées. `Blog.Posts` est la navigation inverse de `Post.Blog` .
- Chaque blog peut avoir une ou plusieurs ressources (un-à-un) :
  - `Blog` est le principal/le parent.
  - `BlogAssets` est le dépendant/enfant. Elle contient la propriété FK `BlogAssets.BlogId` , dont la valeur doit correspondre à la `Blog.Id` valeur de clé primaire du blog associé.
  - `BlogAssets.Blog` est une référence pour la navigation entre les ressources et le blog associé. `BlogAssets.Blog` est la navigation inverse de `Blog.Assets` .
  - `Blog.Assets` est une navigation de référence à partir du blog vers les ressources associées. `Blog.Assets` est la navigation inverse de `BlogAssets.Blog` .
- Chaque billet peut avoir plusieurs balises et chaque balise peut avoir de nombreuses publications (plusieurs-à-plusieurs) :
  - Les relations plusieurs-à-plusieurs représentent une couche supplémentaire sur les relations 2 1-à-plusieurs. Les relations plusieurs-à-plusieurs sont décrites plus loin dans ce document.
  - `Post.Tags` est une collection qui fait partie d’une publication à toutes les balises associées. `Post.Tags` est la navigation inverse de `Tag.Posts` .
  - `Tag.Posts` est une collection de collections à partir d’une balise vers toutes les publications associées. `Tag.Posts` est la navigation inverse de `Post.Tags` .

Pour plus d’informations sur la façon de modéliser et de configurer des relations, consultez [relations](xref:core/modeling/relationships) .

## <a name="relationship-fixup"></a>Correction de la relation

EF Core maintient les navigations en alignement avec les valeurs de clés étrangères et vice versa. Autrement dit, si une valeur de clé étrangère change de sorte qu’elle fait désormais référence à une autre entité principale/parente, les navigations sont mises à jour pour refléter cette modification. De même, si une navigation est modifiée, les valeurs de clé étrangère des entités impliquées sont mises à jour pour refléter cette modification. C’est ce que l’on appelle la « correction de relation ».

### <a name="fixup-by-query"></a>Correction par requête

La correction se produit en premier lorsque les entités sont interrogées à partir de la base de données. La base de données comporte uniquement des valeurs de clés étrangères. par conséquent, lorsque EF Core crée une instance d’entité à partir de la base de données, elle utilise les valeurs de clé étrangère pour définir des navigations de référence et ajouter des entités aux navigations de collection, le cas échéant. Par exemple, considérez une requête pour les blogs et les publications et les ressources associées :

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

Pour chaque blog, EF Core commence par créer une `Blog` instance. Ensuite, à mesure que chaque publication est chargée à partir de la base de données, sa `Post.Blog` navigation de référence est configurée pour pointer vers le blog associé. De même, la publication est ajoutée à la navigation dans la `Blog.Posts` collection. La même chose se produit avec `BlogAssets` , sauf dans ce cas, les deux navigations sont des références. La `Blog.Assets` navigation est définie pour pointer vers l’instance de ressources, et la `BlogAsserts.Blog` navigation est définie pour pointer vers l’instance de blog.

En examinant la [vue de débogage change tracker](xref:core/change-tracking/debug-views) après cette requête, vous voyez deux blogs, chacun avec une ressource et deux publications faisant l’objet d’un suivi :

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

La vue déboguer affiche à la fois les valeurs de clés et les navigations. Les navigations sont affichées à l’aide des valeurs de clé primaire des entités associées. Par exemple, `Posts: [{Id: 1}, {Id: 2}]` dans la sortie ci-dessus indique que la navigation dans la `Blog.Posts` collection contient deux publications associées avec les clés primaires 1 et 2, respectivement. De même, pour chaque publication associée au premier blog, la `Blog: {Id: 1}` ligne indique que la `Post.Blog` navigation fait référence au blog avec la clé primaire 1.

### <a name="fixup-to-locally-tracked-entities"></a>Correction des entités suivies localement

La correction de la relation se produit également entre les entités retournées à partir d’une requête de suivi et les entités déjà suivies par DbContext. Par exemple, envisagez d’exécuter trois requêtes distinctes pour les blogs, les publications et les ressources :

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
En regardant à nouveau dans les vues de débogage, après la première requête, seuls les deux blogs sont suivis :

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

Les `Blog.Assets` navigations de référence sont NULL et les `Blog.Posts` navigations de collection sont vides, car aucune entité associée n’est actuellement suivie par le contexte.

Après la deuxième requête, les `Blogs.Assets` navigations de référence ont été corrigées pour pointer vers les instances qui viennent d’être suivies `BlogAsset` . De même, les `BlogAssets.Blog` navigations de référence sont définies de manière à pointer vers l’instance déjà suivie appropriée `Blog` .

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

Enfin, après la troisième requête, les `Blog.Posts` navigations dans les collections contiennent désormais toutes les publications associées, et les `Post.Blog` références pointent vers l' `Blog` instance appropriée :

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

Il s’agit du même état final que celui obtenu avec la requête unique d’origine, dans la mesure où EF Core les navigations ont été corrigées à mesure que les entités étaient suivies, même en provenance de plusieurs requêtes différentes.

> [!NOTE]
> La correction ne provoque jamais le retour de plus de données de la base de données. Il connecte uniquement les entités qui sont déjà retournées par la requête ou déjà suivies par DbContext. Pour plus d’informations sur la gestion des doublons lors de la sérialisation [d’entités, consultez résolution d’identité dans EF Core](xref:core/change-tracking/identity-resolution) .

## <a name="changing-relationships-using-navigations"></a>Modification des relations à l’aide de navigations

Le moyen le plus simple de modifier la relation entre deux entités consiste à manipuler une navigation, tout en laissant EF Core pour corriger les valeurs de navigation inverse et FK de manière appropriée. Pour ce faire :

- Ajout ou suppression d’une entité dans une navigation de collection.
- Modification d’une navigation de référence pour pointer vers une autre entité ou définition de la valeur null.

### <a name="adding-or-removing-from-collection-navigations"></a>Ajout ou suppression de navigation dans les collections

Par exemple, nous allons déplacer l’une des publications du blog Visual Studio vers le blog .NET. Cela nécessite le chargement préalable des blogs et des publications, puis le déplacement de la publication à partir de la collection de navigation sur un blog vers la collection de navigation sur l’autre blog :

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
> Un appel à <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> est nécessaire ici, car l’accès à la vue de débogage n’entraîne pas la [détection automatique des modifications](xref:core/change-tracking/change-detection).

Il s’agit de l’affichage débogage imprimé après l’exécution du code ci-dessus :

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

La `Blog.Posts` navigation sur le blog .net comporte désormais trois publications ( `Posts: [{Id: 1}, {Id: 2}, {Id: 3}]` ). De même, la `Blog.Posts` navigation sur le blog Visual Studio n’a qu’une seule publication ( `Posts: [{Id: 4}]` ). Cela est attendu, car le code a modifié explicitement ces collections.

Plus intéressant, même si le code n’a pas modifié explicitement la `Post.Blog` navigation, il a été résolu pour pointer vers le blog Visual Studio ( `Blog: {Id: 1}` ). En outre, la `Post.BlogId` valeur de clé étrangère a été mise à jour pour correspondre à la valeur de clé primaire du blog .net. Cette modification apportée à la valeur FK dans est alors rendue persistante dans la base de données lorsque SaveChanges est appelé :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a>Modification des navigations de référence

Dans l’exemple précédent, une publication a été déplacée d’un blog à un autre en modifiant la navigation dans la collection des publications sur chaque blog. La même chose peut être obtenue en modifiant la `Post.Blog` navigation de référence pour qu’elle pointe vers le nouveau blog. Par exemple :

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

L’affichage débogage après cette modification est _exactement_ identique à celui de l’exemple précédent. En effet, EF Core a détecté la modification de navigation de référence, puis a corrigé les navigations de collection et la valeur FK pour les faire correspondre.

## <a name="changing-relationships-using-foreign-key-values"></a>Modification des relations à l’aide de valeurs de clés étrangères

Dans la section précédente, les relations étaient manipulées par des navigations laissant les valeurs de clé étrangère être mises à jour automatiquement. Il s’agit de la méthode recommandée pour manipuler les relations dans EF Core. Toutefois, il est également possible de manipuler les valeurs FK directement. Par exemple, nous pouvons déplacer un billet d’un blog à un autre en modifiant la `Post.BlogId` valeur de clé étrangère :

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

Notez que cela est très similaire à la modification de la navigation de référence, comme indiqué dans l’exemple précédent.

La vue déboguer après cette modification est à nouveau _exactement la même_ que celle des deux exemples précédents. En effet, EF Core a détecté la modification de la valeur FK, puis a corrigé les navigations de référence et de collection pour les faire correspondre.

> [!TIP]
> N’écrivez pas de code pour manipuler toutes les navigations et valeurs FK chaque fois qu’une relation change. Ce code est plus complexe et doit garantir des modifications cohérentes aux clés étrangères et aux navigations dans tous les cas. Si possible, manipulez simplement une seule navigation, ou peut-être les deux. Si nécessaire, manipulez simplement les valeurs FK. Évitez de manipuler à la fois les navigations et les valeurs FK.

## <a name="fixup-for-added-or-deleted-entities"></a>Correction pour les entités ajoutées ou supprimées

### <a name="adding-to-a-collection-navigation"></a>Ajouter à une navigation dans une collection

EF Core effectue les actions suivantes lorsqu’il [détecte](xref:core/change-tracking/change-detection) qu’une nouvelle entité dépendante/enfant a été ajoutée à une navigation dans une collection :

- Si l’entité n’est pas suivie, elle est suivie. (En général, l’entité est dans l' `Added` État. Toutefois, si le type d’entité est configuré pour utiliser des clés générées et que la valeur de clé primaire est définie, l’entité est suivie dans l' `Unchanged` État.)
- Si l’entité est associée à un autre principal/parent, cette relation est interrompue.
- L’entité est associée au principal/parent qui possède la navigation dans la collection.
- Les navigations et les valeurs de clé étrangère sont fixes pour toutes les entités impliquées.

En fonction de cela, nous pouvons voir que pour déplacer un billet d’un blog à un autre, nous n’avons pas besoin de le supprimer de l’ancienne navigation de la collection avant de l’ajouter au nouveau. Par conséquent, le code de l’exemple ci-dessus peut être modifié à partir de :

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

Par :

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

EF Core constate que la publication a été ajoutée à un nouveau blog et la supprime automatiquement de la collection sur le premier blog.

### <a name="removing-from-a-collection-navigation"></a>Supprimer d’une collection de navigation

La suppression d’une entité dépendante/enfant de la collection de navigation du principal/parent provoque le dépassement de la relation à ce principal/parent. Ce qui se passe ensuite dépend du fait que la relation est facultative ou obligatoire.

#### <a name="optional-relationships"></a>Relations facultatives

Par défaut pour les relations facultatives, la valeur de clé étrangère est définie sur null. Cela signifie que le dépendant/enfant n’est plus associé à _un_ principal/parent. Par exemple, nous allons charger un blog et publier, puis supprimer l’une des publications de la `Blog.Posts` barre de navigation de la collection :

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

Si vous examinez la [vue Debug change Tracking](xref:core/change-tracking/debug-views) après cette modification, vous voyez que :

- La `Post.BlogId` valeur FK a été définie sur null ( `BlogId: <null> FK Modified Originally 1` )
- La `Post.Blog` navigation de référence a été définie sur null ( `Blog: <null>` )
- La publication a été supprimée de la navigation dans la `Blog.Posts` collection ( `Posts: [{Id: 1}]` )

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

Notez que la publication n’est _pas_ marquée comme `Deleted` . Elle est marquée comme `Modified` afin que la valeur FK dans la base de données soit définie sur Null lorsque SaveChanges est appelé.

#### <a name="required-relationships"></a>Relations requises

La définition de la valeur FK sur null n’est pas autorisée (et n’est généralement pas possible) pour les relations requises. Par conséquent, le fait de découper une relation requise signifie que l’entité dépendante/enfant doit être reapparentée à un nouveau principal/parent, ou supprimée de la base de données lorsque SaveChanges est appelé pour éviter une violation de contrainte référentielle. C’est ce que l’on appelle la « suppression des orphelins » et est le comportement par défaut dans EF Core pour les relations requises.

Par exemple, nous allons modifier la relation entre le blog et les publications, puis exécuter le même code que dans l’exemple précédent :

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

Si vous examinez l’affichage débogage après cette modification, vous voyez que :

- La publication a été marquée comme `Deleted` telle qu’elle sera supprimée de la base de données lorsque SaveChanges est appelé.
- La `Post.Blog` navigation de référence a été définie sur null ( `Blog: <null>` ).
- La publication a été supprimée de la navigation dans la `Blog.Posts` collection ( `Posts: [{Id: 1}]` ).

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

Notez que le `Post.BlogId` reste inchangé, car pour une relation obligatoire, il ne peut pas avoir la valeur null.

L’appel de SaveChanges entraîne la suppression de la publication orpheline :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a>Supprimer le minutage des orphelins et le nouveau parenté

Par défaut, le marquage des orphelins `Deleted` se produit dès que le changement de relation est [détecté](xref:core/change-tracking/change-detection). Toutefois, ce processus peut être retardé jusqu’à ce que SaveChanges soit réellement appelé. Cela peut être utile pour éviter d’effectuer des orphelins d’entités qui ont été supprimées d’un principal/parent, mais qui seront à nouveau apparentés à un nouveau principal/parent avant l’appel de SaveChanges. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> est utilisé pour définir ce minutage. Par exemple :

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

Après la suppression de la publication de la première collection, l’objet n’est pas marqué comme `Deleted` dans l’exemple précédent. Au lieu de cela, EF Core effectue le suivi de la gravité de la relation, _même s’il s’agit d’une relation obligatoire_. (La valeur FK est considérée comme NULL par EF Core même s’il ne peut pas vraiment être null, car le type n’accepte pas la valeur null. C’est ce que l’on appelle « null conceptuel ».)

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

L’appel de SaveChanges à ce stade entraînerait la suppression de la publication orpheline. Toutefois, si tel est le cas dans l’exemple ci-dessus, la publication est associée à un nouveau blog avant l’appel de SaveChanges, puis elle est redéfinie de manière appropriée sur ce nouveau blog et n’est plus considérée comme orpheline :

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

SaveChanges appelée à ce stade met à jour la publication dans la base de données au lieu de la supprimer.

Il est également possible de désactiver la suppression automatique des orphelins. Une exception se produit si SaveChanges est appelé alors qu’un orphelin est suivi. Par exemple, le code suivant :

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

Lèvera cette exception :

> System. InvalidOperationException : l’association entre les entités’blog’et’poster’avec la valeur de clé' {BlogId : 1} 'a été interrompue, mais la relation est marquée comme obligatoire ou est implicitement requise, car la clé étrangère n’accepte pas les valeurs NULL. Si l’entité dépendante/enfant doit être supprimée lorsqu’une relation requise est rompue, configurez la relation pour qu’elle utilise des suppressions en cascade.

La suppression des orphelins, ainsi que des suppressions en cascade, peut être forcée à tout moment en appelant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> . La combinaison de ce paramètre avec la définition de l’option supprimer le minutage orphelin pour `Never` garantit que les orphelins ne sont jamais supprimés, à moins que EF Core soit explicitement invité à le faire.

### <a name="changing-a-reference-navigation"></a>Modification d’une navigation de référence

La modification de la navigation de référence d’une relation un-à-plusieurs a le même effet que la modification de la navigation de la collection à l’autre extrémité de la relation. L’affectation de la valeur null à la navigation de référence de dependent/Child équivaut à supprimer l’entité de la navigation dans la collection du principal/parent. Toutes les modifications de correction et de base de données sont effectuées comme décrit dans la section précédente, y compris la création de l’entité orpheline si la relation est requise.

#### <a name="optional-one-to-one-relationships"></a>Relations un-à-un facultatives

Pour les relations un-à-un, la modification d’une navigation de référence provoque la gravité d’une relation précédente. Pour les relations facultatives, cela signifie que la valeur FK sur le dépendant/enfant précédemment associé est définie sur null. Par exemple :

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

La vue déboguer avant d’appeler SaveChanges indique que les nouvelles ressources ont remplacé les ressources existantes, qui sont désormais marquées comme `Modified` avec une `BlogAssets.BlogId` valeur FK NULL :

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

Il en résulte une mise à jour et une insertion lorsque SaveChanges est appelé :

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

#### <a name="required-one-to-one-relationships"></a>Relations un-à-un requises

L’exécution du même code que dans l’exemple précédent, mais cette fois avec une relation un-à-un obligatoire, indique que le précédemment associé `BlogAssets` est désormais marqué comme `Deleted` , car il devient orphelin lorsque le nouveau devient `BlogAssets` son emplacement :

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

Cela entraîne ensuite la suppression d’un et l’insertion lorsque SaveChanges est appelé :

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

Le minutage du marquage des orphelins comme supprimés peut être modifié de la même façon que pour les navigations dans les collections et a les mêmes effets.

### <a name="deleting-an-entity"></a>Suppression d’une entité

#### <a name="optional-relationships"></a>Relations facultatives

Lorsqu’une entité est marquée comme `Deleted` , par exemple en appelant <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> , les références à l’entité supprimée sont supprimées des navigations d’autres entités. Pour les relations facultatives, les valeurs FK dans les entités dépendantes sont définies sur null.

Par exemple, nous allons marquer le blog Visual Studio comme `Deleted` suit :

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

Si vous examinez l' [affichage de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) avant d’appeler SaveChanges, vous voyez :

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

Notez que :

- Le blog est marqué comme `Deleted` .
- Les ressources associées au blog supprimé ont une valeur FK null ( `BlogId: <null> FK Modified Originally 2` ) et une navigation de référence null ( `Blog: <null>` )
- Chaque publication associée au blog supprimé a une valeur FK null ( `BlogId: <null> FK Modified Originally 2` ) et une navigation de référence null ( `Blog: <null>` )

#### <a name="required-relationships"></a>Relations requises

Le comportement de correction pour les relations requises est le même que pour les relations facultatives, à ceci près que les entités dépendantes/enfants sont marquées comme `Deleted` étant donné qu’elles ne peuvent pas exister sans principal/parent et doivent être supprimées de la base de données lorsque SaveChanges est appelé pour éviter une exception de contrainte référentielle. C’est ce que l’on appelle la « suppression en cascade » et est le comportement par défaut dans EF Core pour les relations requises. Par exemple, l’exécution du même code que dans l’exemple précédent, mais avec une relation obligatoire, entraîne l’affichage de débogage suivant avant l’appel de SaveChanges :

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

Comme prévu, les dépendants/enfants sont maintenant marqués comme `Deleted` . Toutefois, Notez que les navigations sur les entités supprimées n’ont _pas_ changé. Cela peut paraître étrange, mais elle évite de broyer complètement un graphique supprimé d’entités en effaçant toutes les navigations. Autrement dit, le blog, la ressource et les publications forment toujours un graphique d’entités même après avoir été supprimé. Il est ainsi beaucoup plus facile de supprimer un graphique d’entités que le cas dans EF6 où le graphique a été broyé.

#### <a name="cascade-delete-timing-and-re-parenting"></a>Suppression en cascade du minutage et du nouveau parentage

Par défaut, la suppression en cascade se produit dès que le parent/principal est marqué comme `Deleted` . Il s’agit de la même chose que pour la suppression des orphelins, comme décrit précédemment. Comme pour la suppression des orphelins, ce processus peut être retardé jusqu’à ce que SaveChanges soit appelé, voire totalement désactivé, en le définissant de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> manière appropriée. Cela est utile de la même façon que pour la suppression des orphelins, y compris pour le nouveau parent des enfants/dépendants après la suppression d’un principal/parent.

Les suppressions en cascade, ainsi que la suppression des orphelins, peuvent être forcées à tout moment en appelant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> . La combinaison de ce paramètre avec la définition du minutage de la suppression en cascade pour `Never` garantir que les suppressions en cascade ne se produiront jamais, à moins que EF Core soit explicitement invité à le faire.

> [!TIP]
> La suppression en cascade et la suppression des orphelins sont étroitement liées. Les deux entraînent la suppression d’entités dépendantes/enfants lorsque la relation avec le principal/parent requis est interrompue. Pour la suppression en cascade, ce problème se produit car le principal/le parent est lui-même supprimé. Pour les orphelins, l’entité principale/parente existe toujours, mais n’est plus liée aux entités dépendantes/enfants.

## <a name="many-to-many-relationships"></a>Relations Plusieurs-à-plusieurs

Les relations plusieurs-à-plusieurs dans EF Core sont implémentées à l’aide d’une entité de jointure. Chaque côté de la relation plusieurs-à-plusieurs est lié à cette entité de jointure avec une relation un-à-plusieurs. Avant le EF Core 5,0, cette entité de jointure devait être explicitement définie et mappée. À compter de EF Core 5,0, il peut être créé implicitement et masqué. Toutefois, dans les deux cas, le comportement sous-jacent est le même. Nous allons tout d’abord examiner ce comportement sous-jacent pour comprendre comment fonctionne le suivi des relations plusieurs-à-plusieurs.

### <a name="how-many-to-many-relationships-work"></a>Fonctionnement des relations plusieurs-à-plusieurs

Considérez cette EF Core modèle qui crée une relation plusieurs-à-plusieurs entre des publications et des balises à l’aide d’un type d’entité de jointure défini explicitement :

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

Notez que le `PostTag` type d’entité de jointure contient deux propriétés de clé étrangère. Dans ce modèle, pour une publication à associer à une balise, il doit y avoir une entité de jointure PostTag où la valeur de clé `PostTag.PostId` étrangère correspond à la `Post.Id` valeur de clé primaire, et où la valeur de clé `PostTag.TagId` étrangère correspond à la `Tag.Id` valeur de clé primaire. Par exemple :

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

Si vous examinez la [vue de débogage change tracker](xref:core/change-tracking/debug-views) après l’exécution de ce code, vous voyez que la publication et la balise sont liées par la nouvelle `PostTag` entité de jointure :

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

Notez que les navigations de la collection sur `Post` et `Tag` ont été corrigées, de même que les navigations de référence `PostTag` . Ces relations peuvent être manipulées par des navigations au lieu de valeurs FK, comme dans tous les exemples précédents. Par exemple, le code ci-dessus peut être modifié pour ajouter la relation en définissant les navigations de référence sur l’entité de jointure :

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

Cela donne exactement la même modification à clés étrangères et aux navigations que dans l’exemple précédent.

### <a name="skip-navigations"></a>Ignorer les navigations

> [!NOTE]
> Les navigations ignorées ont été introduites dans EF Core 5,0.

La manipulation manuelle de la table de jointure peut être lourde. À compter de EF Core 5,0, les relations plusieurs-à-plusieurs peuvent être manipulées directement à l’aide de navigations spéciales de collection qui « ignorent » l’entité de jointure. Par exemple, deux navigations à ignorer peuvent être ajoutées au modèle ci-dessus. une de la publication aux balises, et l’autre de la balise à Posts :

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

Cette relation plusieurs-à-plusieurs requiert la configuration suivante pour s’assurer que les navigations ignorées et les navigations normales sont toutes utilisées pour la même relation plusieurs-à-plusieurs :

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

Pour plus d’informations sur le mappage des relations plusieurs-à-plusieurs, consultez [relations](xref:core/modeling/relationships) .

Ignorer les navigations et se comporter comme des navigations de collections normales. Toutefois, la façon dont elles fonctionnent avec les valeurs de clés étrangères est différente. Nous allons associer un billet à une balise, mais cette fois-ci à l’aide d’une navigation à ignorer :

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

Notez que ce code n’utilise pas l’entité de jointure. Au lieu de cela, il suffit d’ajouter une entité à une collection de navigation de la même façon que s’il s’agissait d’une relation un-à-plusieurs. La vue de débogage résultante est essentiellement la même que précédemment :

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

Notez qu’une instance de l' `PostTag` entité de jointure a été créée automatiquement avec les valeurs FK définies sur les valeurs de clé primaire de la balise et de la publication qui sont maintenant associées. Toutes les navigations de référence et de collection normales ont été résolues pour correspondre à ces valeurs FK. En outre, étant donné que ce modèle contient des navigations ignorées, celles-ci ont également été corrigées. Plus précisément, même si nous avons ajouté la balise à la `Post.Tags` navigation ignorée, la `Tag.Posts` navigation par omission inverse de l’autre côté de cette relation a également été résolue pour contenir la publication associée.

Il est important de noter que les relations plusieurs-à-plusieurs sous-jacentes peuvent toujours être manipulées directement même lorsque les navigations ignorées ont été superposées en premier. Par exemple, la balise et la publication peuvent être associées comme nous l’avons fait avant d’introduire des navigations ignorées :

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

Ou utiliser les valeurs FK :

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

Cela entraînera toujours la correction correcte des navigations ignorées, ce qui entraîne la sortie de la même vue de débogage que dans l’exemple précédent.

### <a name="skip-navigations-only"></a>Ignorer les navigations uniquement

Dans la section précédente, nous avons ajouté ignorer les navigations _, en plus de_ la définition complète des deux relations un-à-plusieurs sous-jacentes. Cela est utile pour illustrer ce qui se passe aux valeurs FK, mais il est souvent inutile. Au lieu de cela, la relation plusieurs-à-plusieurs peut être définie en utilisant _uniquement des navigations ignorées_. C’est ainsi que la relation plusieurs-à-plusieurs est définie dans le modèle, tout en haut de ce document. À l’aide de ce modèle, nous pouvons à nouveau associer un billet et une balise en ajoutant un billet à la `Tag.Posts` navigation ignorée (ou, alternativement, en ajoutant une balise à la `Post.Tags` navigation ignorée) :

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

Si vous examinez l’affichage débogage après avoir effectué cette modification, vous constatez que EF Core a créé une instance de `Dictionary<string, object>` pour représenter l’entité de jointure. Cette entité de jointure contient `PostsId` `TagsId` à la fois les propriétés de clé étrangère et celles qui ont été définies pour correspondre aux valeurs de clé primaire de la publication et de la balise associées.

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

Pour plus d’informations sur les entités de jointure implicite et l’utilisation des types d’entités, consultez [relations](xref:core/modeling/relationships) `Dictionary<string, object>` .

> [!IMPORTANT]
> Le type CLR utilisé pour les types d’entité de jointure par convention peut changer dans les versions ultérieures pour améliorer les performances. Ne dépendez pas du type de jointure `Dictionary<string, object>` , sauf si cette configuration a été explicitement configurée.

### <a name="join-entities-with-payloads"></a>Joindre des entités avec des charges utiles

Jusqu’à présent, tous les exemples ont utilisé un type d’entité de jointure (explicite ou implicite) qui contient uniquement les deux propriétés de clé étrangère nécessaires pour la relation plusieurs-à-plusieurs. Aucune de ces valeurs FK n’a besoin d’être explicitement définie par l’application lors de la manipulation des relations, car leurs valeurs proviennent des propriétés de clé primaire des entités associées. Cela permet à EF Core de créer des instances de l’entité de jointure sans données manquantes.

#### <a name="payloads-with-generated-values"></a>Charges utiles avec des valeurs générées

EF Core prend en charge l’ajout de propriétés supplémentaires au type d’entité de jointure. C’est ce que l’on appelle donner à l’entité de jointure une « charge utile ». Par exemple, nous allons ajouter `TaggedOn` la propriété à l' `PostTag` entité de jointure :

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

Cette propriété de charge utile n’est pas définie quand EF Core crée une instance d’entité de jointure. La façon la plus courante de traiter ce problème consiste à utiliser des propriétés de charge utile avec des valeurs générées automatiquement. Par exemple, la `TaggedOn` propriété peut être configurée pour utiliser un horodateur généré par le magasin lorsque chaque nouvelle entité est insérée :

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

Une publication peut désormais être marquée de la même façon qu’avant :

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

Si vous examinez l' [affichage de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) après avoir appelé SaveChanges, la propriété de charge utile est correctement définie :

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

#### <a name="explicitly-setting-payload-values"></a>Définition explicite des valeurs de charge utile

Après l’exemple précédent, nous allons ajouter une propriété de charge utile qui n’utilise pas de valeur générée automatiquement :

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

Une publication peut désormais être marquée de la même façon qu’avant, et l’entité de jointure est toujours créée automatiquement. Cette entité est ensuite accessible à l’aide de l’un des mécanismes décrits dans [accès aux entités suivies](xref:core/change-tracking/entity-entries). Par exemple, le code ci-dessous utilise <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> pour accéder à l’instance d’entité de jointure :

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

Une fois l’entité de jointure localisée, elle peut être manipulée de manière normale (dans cet exemple, pour définir la `TaggedBy` propriété de charge utile avant d’appeler SaveChanges).

> [!NOTE]
> Notez qu’un appel à <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> est requis ici pour permettre à EF Core de détecter la modification de la propriété de navigation et de créer l’instance de l’entité de jointure avant que `Find` ne soit utilisé. Pour plus d’informations, consultez Modification de la [détection et des notifications](xref:core/change-tracking/change-detection) .

L’entité de jointure peut également être créée explicitement pour associer un billet à une balise. Par exemple :

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

Enfin, une autre façon de définir des données de charge utile consiste à remplacer <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ou à utiliser l' <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> événement pour traiter les entités avant de mettre à jour la base de données. Par exemple :

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
