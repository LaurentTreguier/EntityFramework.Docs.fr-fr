---
title: Change Tracking-EF Core
description: Vue d’ensemble du suivi des modifications pour EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: 8cfa4590af07ec1715eb48ec0c7acb3426b6a6b4
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983259"
---
# <a name="change-tracking-in-ef-core"></a>Change Tracking dans EF Core

Chaque <xref:Microsoft.EntityFrameworkCore.DbContext> instance suit les modifications apportées aux entités. Ces entités suivies entraînent à leur tour les modifications apportées à la base de données lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé.

Ce document présente une vue d’ensemble du suivi des modifications de Entity Framework Core (EF Core) et de son lien avec les requêtes et les mises à jour.

> [!TIP]
> Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).

> [!TIP]
> Par souci de simplicité, ce document utilise et référence des méthodes synchrones, par exemple <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> plutôt que leurs équivalents Async, tels que <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> . L’appel et l’attente de la méthode Async peuvent être substitués sauf indication contraire.

## <a name="how-to-track-entities"></a>Comment suivre les entités

Les instances d’entité sont suivies quand elles sont :

- Retourné à partir d’une requête exécutée sur la base de données
- Explicitement attaché à DbContext par des `Add` `Attach` `Update` méthodes similaires,, ou
- Détecté comme de nouvelles entités connectées à des entités suivies existantes

Les instances d’entité ne sont plus suivies quand :

- DbContext est supprimé
- Le dispositif de suivi des modifications est effacé (EF Core 5,0 et versions ultérieures)
- Les entités sont détachées explicitement

DbContext est conçu pour représenter une unité de travail à courte durée de vie, comme décrit dans [initialisation et configuration de DbContext](xref:core/dbcontext-configuration/index). Cela signifie que la suppression de DbContext est _le moyen normal_ d’arrêter le suivi des entités. En d’autres termes, la durée de vie d’un DbContext doit être :

1. Créer l’instance DbContext
2. Suivre certaines entités
3. Apporter des modifications aux entités
4. Appeler SaveChanges pour mettre à jour la base de données
5. Supprimer l’instance DbContext

> [!TIP]
> Il n’est pas nécessaire d’effacer le dispositif de suivi des modifications ou de détacher explicitement les instances d’entité lorsque vous adoptez cette approche. Toutefois, si vous avez besoin de détacher des entités, l’appel de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> est plus efficace que le détachement d’entités un par un.

## <a name="entity-states"></a>États d’entité

Chaque entité est associée à un donné <xref:Microsoft.EntityFrameworkCore.EntityState> :

- `Detached` les entités ne sont pas suivies par le <xref:Microsoft.EntityFrameworkCore.DbContext> .
- `Added` les entités sont nouvelles et n’ont pas encore été insérées dans la base de données. Cela signifie qu’ils seront insérés lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé.
- `Unchanged` les entités n’ont _pas_ été modifiées depuis la requête de la base de données. Toutes les entités retournées par les requêtes sont initialement dans cet État.
- `Modified` les entités ont été modifiées depuis la requête de la base de données. Cela signifie qu’ils seront mis à jour lorsque SaveChanges est appelé.
- `Deleted` les entités existent dans la base de données, mais sont marquées comme devant être supprimées lorsque SaveChanges est appelé.

EF Core effectue le suivi des modifications au niveau de la propriété. Par exemple, si une seule valeur de propriété est modifiée, une mise à jour de base de données modifie uniquement cette valeur. Toutefois, les propriétés ne peuvent être marquées comme modifiées que lorsque l’entité elle-même est dans l’état modifié. (Ou, d’un autre point de vue, l’état modifié signifie qu’au moins une valeur de propriété a été marquée comme modifiée.)

Le tableau suivant récapitule les différents États :

| État des entités     | Suivi par DbContext | Existe dans la base de données | Propriétés modifiées | Action sur SaveChanges
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | Non                   | -                  | -                   | -
| `Added`          | Oui                  | Non                 | -                   | Insérer
| `Unchanged`      | Oui                  | Oui                | Non                  | -
| `Modified`       | Oui                  | Oui                | Oui                 | Update
| `Deleted`        | Oui                  | Oui                | -                   | Supprimer

> [!NOTE]
> Ce texte utilise des termes de base de données relationnelle pour plus de clarté. Les bases de données NoSQL prennent généralement en charge des opérations similaires, mais éventuellement avec des noms différents. Pour plus d’informations, consultez la documentation de votre fournisseur de base de données.

## <a name="tracking-from-queries"></a>Suivi à partir de requêtes

EF Core le suivi des modifications fonctionne mieux quand la même <xref:Microsoft.EntityFrameworkCore.DbContext> instance est utilisée pour interroger des entités et les mettre à jour en appelant <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . En effet, EF Core effectue automatiquement le suivi de l’état des entités interrogées, puis détecte toutes les modifications apportées à ces entités lorsque SaveChanges est appelé.

Cette approche présente plusieurs avantages par rapport au [suivi explicite des instances d’entité](xref:core/change-tracking/explicit-tracking):

- Elle est simple. Les États d’entité doivent rarement être manipulés explicitement--EF Core prend en charge les changements d’État.
- Les mises à jour sont limitées uniquement aux valeurs qui ont été réellement modifiées.
- Les valeurs des [Propriétés Shadow](xref:core/modeling/shadow-properties) sont conservées et utilisées en fonction des besoins. Cela s’avère particulièrement utile lorsque les clés étrangères sont stockées dans un état d’ombre.
- Les valeurs d’origine des propriétés sont conservées automatiquement et utilisées pour des mises à jour efficaces.

## <a name="simple-query-and-update"></a>Requête et mise à jour simples

Par exemple, considérez un modèle de blog/billet simple :

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

Nous pouvons utiliser ce modèle pour rechercher des blogs et des publications, puis mettre à jour la base de données :

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

L’appel de SaveChanges produit les mises à jour de base de données suivantes, à l’aide de SQLite comme exemple de base de données :

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

La [vue de débogage du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications est un excellent moyen de visualiser les entités qui font l’objet d’un suivi et leurs États. Par exemple, insérez le code suivant dans l’exemple ci-dessus avant d’appeler SaveChanges :

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

Génère la sortie suivante :

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

Notez spécifiquement :

- La `Blog.Name` propriété est marquée comme modifiée ( `Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'` ), ce qui entraîne l’état du blog `Modified` .
- La `Post.Title` propriété de la publication 2 est marquée comme modifiée ( `Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'` ), ce qui entraîne l’état de cette publication `Modified` .
- Les autres valeurs de propriété de publication 2 n’ont pas changé et ne sont donc pas marquées comme modifiées. C’est pourquoi ces valeurs ne sont pas incluses dans la mise à jour de la base de données.
- L’autre publication n’a pas été modifiée de quelque manière que ce soit. C’est pourquoi il est toujours dans l' `Unchanged` État et n’est pas inclus dans la mise à jour de la base de données.

## <a name="query-then-insert-update-and-delete"></a>Requête, insérer, mettre à jour et supprimer

Les mises à jour telles que celles de l’exemple précédent peuvent être combinées avec des insertions et des suppressions dans la même unité de travail. Par exemple :

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

Dans cet exemple :

- Un blog et les publications associées sont interrogés à partir de la base de données et suivis
- La `Blog.Name` propriété a été modifiée.
- Une nouvelle publication est ajoutée à la collection de publications existantes pour le blog
- Une publication existante est marquée pour suppression en appelant <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>

Si vous revoyez la [vue debug du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications avant d’appeler SaveChanges, vous voyez comment EF Core effectue le suivi de ces modifications :

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

Notez que :

- Le blog est marqué comme `Modified` . Cette opération génère une mise à jour de la base de données.
- La publication 2 est marquée comme `Deleted` . Cela génère une suppression de base de données.
- Une nouvelle publication avec un ID temporaire est associée au blog 1 et est marquée comme `Added` . Cela génère une insertion de base de données.

Cela se traduit par les commandes de base de données suivantes (à l’aide de SQLite) lorsque SaveChanges est appelé :

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

Pour plus d’informations sur l’insertion et la suppression d’entités, consultez [suivi explicite des entités](xref:core/change-tracking/explicit-tracking) . Consultez [modification de la détection et des notifications](xref:core/change-tracking/change-detection) pour plus d’informations sur la façon dont EF Core détecte automatiquement les modifications comme suit.

> [!TIP]
> Appelez <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> pour déterminer si des modifications ont été apportées pour que SaveChanges fasse des mises à jour de la base de données. Si HasChanges retourne false, alors SaveChanges sera une absence d’opération.
