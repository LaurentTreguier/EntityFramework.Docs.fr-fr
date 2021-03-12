---
title: Suivi explicite des entités-EF Core
description: Suivi explicite d’entités avec DbContext à l’aide de l’ajout, de l’attachement, de la mise à jour et de la suppression
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: a0e51db4a70338e725ed40caa57a7f02245f0707
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024470"
---
# <a name="explicitly-tracking-entities"></a>Suivi explicite des entités

Chaque <xref:Microsoft.EntityFrameworkCore.DbContext> instance suit les modifications apportées aux entités. Ces entités suivies entraînent à leur tour les modifications apportées à la base de données lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé.

Le suivi des modifications de Entity Framework Core (EF Core) fonctionne mieux lorsque la même <xref:Microsoft.EntityFrameworkCore.DbContext> instance est utilisée pour interroger des entités et les mettre à jour en appelant <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . En effet, EF Core effectue automatiquement le suivi de l’état des entités interrogées, puis détecte toutes les modifications apportées à ces entités lorsque SaveChanges est appelé. Cette approche est décrite dans [change Tracking dans EF Core](xref:core/change-tracking/index).

> [!TIP]
> Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris. Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeTrackingInEFCore).

> [!TIP]
> Par souci de simplicité, ce document utilise et référence des méthodes synchrones, par exemple, <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> plutôt que leurs équivalents Async, tels que <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> . L’appel et l’attente de la méthode Async peuvent être substitués sauf indication contraire.

## <a name="introduction"></a>Introduction

Les entités peuvent être explicitement « attachées » à un de <xref:Microsoft.EntityFrameworkCore.DbContext> sorte que le contexte effectue le suivi de ces entités. Cela s’avère particulièrement utile dans les cas suivants :

1. Création de nouvelles entités qui seront insérées dans la base de données.
2. Rattachement des entités déconnectées qui ont été précédemment interrogées par une _autre_ instance DbContext.

La plupart d’entre elles sont nécessaires à la plupart des applications, et sont principales gérées par les <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> méthodes.

La seconde est uniquement requise par les applications qui modifient des entités ou leurs relations **_alors que les entités ne sont pas suivies_**. Par exemple, une application Web peut envoyer des entités au client Web où l’utilisateur apporte des modifications et envoie les entités en arrière. Ces entités sont désignées comme étant « déconnectées », car elles ont été interrogées à l’origine à partir d’un DbContext, mais elles ont ensuite été déconnectées de ce contexte lorsqu’elles sont envoyées au client.

L’application Web doit à présent rattacher ces entités afin qu’elles soient à nouveau suivies et indiquer les modifications apportées afin de <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> pouvoir effectuer les mises à jour appropriées de la base de données. Cela est principalement géré par les <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> méthodes et.

> [!TIP]
> Il n’est normalement pas nécessaire d’attacher des entités à la _même instance DbContext_ à partir de laquelle elles ont été interrogées. N’effectuez pas régulièrement une requête de non-suivi, puis attachez les entités retournées au même contexte. Cette opération est plus lente que l’utilisation d’une requête de suivi et peut également entraîner des problèmes tels que des valeurs de propriété Shadow manquantes, ce qui rend plus difficile la récupération.

### <a name="generated-versus-explicit-key-values"></a>Valeurs de clés générées et explicites

Par défaut, les propriétés de [clé](xref:core/modeling/keys) de type entier et GUID sont configurées pour utiliser des [valeurs de clés générées automatiquement](xref:core/modeling/generated-properties). Cela présente un **avantage majeur pour le suivi des modifications : une valeur de clé non définie indique que l’entité est « New »**. Par « nouveau », nous entendons qu’elle n’a pas encore été insérée dans la base de données.

Deux modèles sont utilisés dans les sections suivantes. La première est configurée de façon à **ne pas** utiliser les valeurs de clés générées :

<!--
    public class Blog
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Model)]

Les valeurs de clé non générées (c’est-à-dire explicitement définies) sont affichées en premier dans chaque exemple, car tout est très explicite et facile à suivre. Elle est ensuite suivie d’un exemple dans lequel les valeurs de clé générées sont utilisées :

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

Notez que les propriétés de clé de ce modèle n’ont pas besoin de configuration supplémentaire ici, car l’utilisation de valeurs de clé générées est la [valeur par défaut pour les clés d’entiers simples](xref:core/modeling/generated-properties).

## <a name="inserting-new-entities"></a>Insertion de nouvelles entités

### <a name="explicit-key-values"></a>Valeurs de clés explicites

Une entité doit être suivie dans l' `Added` État à insérer par <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . Les entités sont généralement placées dans l’état Added en appelant l’une des <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> méthodes,,, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> ou équivalentes sur <xref:Microsoft.EntityFrameworkCore.DbSet%601> .

> [!TIP]
> Ces méthodes fonctionnent toutes de la même manière dans le contexte du suivi des modifications. Pour plus d’informations, consultez [fonctionnalités de change Tracking supplémentaires](xref:core/change-tracking/miscellaneous) .

Par exemple, pour démarrer le suivi d’un nouveau blog :

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

L’inspection de l' [affichage de débogage du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications après cet appel indique que le contexte effectue le suivi de la nouvelle entité dans l' `Added` État :

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

Toutefois, les méthodes Add ne fonctionnent pas simplement sur une entité individuelle. Ils commencent en fait le suivi d’un _graphique complet d’entités associées_, en les plaçant tout à l' `Added` État. Par exemple, pour insérer un nouveau blog et associer les nouvelles publications :

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_2)]

Le contexte effectue maintenant le suivi de toutes ces entités comme `Added` suit :

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Added
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Added
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Notez que des valeurs explicites ont été définies pour les `Id` Propriétés de clé dans les exemples ci-dessus. Cela est dû au fait que le modèle ici a été configuré pour utiliser des valeurs de clé définies explicitement, plutôt que des valeurs de clés générées automatiquement. Lorsque vous n’utilisez pas de clés générées, les propriétés de clé doivent être explicitement définies _avant_ d’appeler `Add` . Ces valeurs de clés sont ensuite insérées lorsque SaveChanges est appelé. Par exemple, lors de l’utilisation de SQLite :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Id", "Name")
VALUES (@p0, @p1);

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String), @p3='1' (DbType = String), @p4='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p5='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p2, @p3, @p4, @p5);

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String), @p1='1' (DbType = String), @p2='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p3='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2, @p3);
```

Toutes ces entités sont suivies dans l' `Unchanged` État après l’exécution d’SaveChanges, puisque ces entités existent maintenant dans la base de données :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

### <a name="generated-key-values"></a>Valeurs de clés générées

Comme indiqué ci-dessus, les [Propriétés de clé](xref:core/modeling/keys) de type entier et GUID sont configurées pour utiliser par défaut des [valeurs de clés générées automatiquement](xref:core/modeling/generated-properties) . Cela signifie que l’application _ne doit pas définir explicitement une valeur de clé_. Par exemple, pour insérer un nouveau blog et publier tous les éléments avec des valeurs de clés générées :

<!--
            context.Add(
                new Blog
                {
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Inserting_new_entities_3)]

Comme avec les valeurs de clé explicites, le contexte effectue le suivi de toutes ces entités comme `Added` suit :

```output
Blog {Id: -2147482644} Added
  Id: -2147482644 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -2147482637}, {Id: -2147482636}]
Post {Id: -2147482637} Added
  Id: -2147482637 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -2147482644}
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: -2147482644}
```

Notez que dans ce cas, les [valeurs de clé temporaires](xref:core/change-tracking/miscellaneous#temporary-values) ont été générées pour chaque entité. Ces valeurs sont utilisées par EF Core jusqu’à ce que SaveChanges soit appelé, à partir de laquelle les valeurs de clés réelles sont lues à partir de la base de données. Par exemple, lors de l’utilisation de SQLite :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Name")
VALUES (@p0);
SELECT "Id"
FROM "Blogs"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p2='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p3='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p1, @p2, @p3);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Une fois SaveChanges terminé, toutes les entités ont été mises à jour avec leurs valeurs de clés réelles et sont suivies dans l' `Unchanged` État, car elles correspondent désormais à l’État dans la base de données :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Il s’agit exactement du même état final que l’exemple précédent qui utilisait des valeurs de clés explicites.

> [!TIP]
> Une valeur de clé explicite peut encore être définie même en cas d’utilisation de valeurs de clé générées. EF Core essaiera ensuite d’insérer à l’aide de cette valeur de clé. Certaines configurations de base de données, y compris les SQL Server avec des colonnes d’identité, ne prennent pas en charge les insertions et lèvent ([consultez ces documents pour une solution de contournement](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)).

## <a name="attaching-existing-entities"></a>Attachement d’entités existantes

### <a name="explicit-key-values"></a>Valeurs de clés explicites

Les entités retournées par les requêtes sont suivies dans l' `Unchanged` État. L' `Unchanged` État signifie que l’entité n’a pas été modifiée depuis qu’elle a été interrogée. Une entité déconnectée, peut-être retournée par un client Web dans une requête HTTP, peut être placée dans cet État à l’aide de <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> , de <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> ou des méthodes équivalentes sur <xref:Microsoft.EntityFrameworkCore.DbSet%601> . Par exemple, pour démarrer le suivi d’un blog existant :

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Attaching_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_1)]

> [!NOTE]
> Les exemples ci-après illustrent la création d’entités explicitement avec `new` pour des raisons de simplicité. Normalement, les instances d’entité sont issues d’une autre source, telle que la désérialisation à partir d’un client, ou la création à partir de données dans une requête HTTP.

L’inspection de l' [affichage de débogage du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications après cet appel montre que l’entité est suivie dans l' `Unchanged` État :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

Tout comme `Add` , `Attach` définit en fait un graphique entier des entités connectées à l' `Unchanged` État. Par exemple, pour joindre un blog existant et les publications existantes associées :

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_2)]

Le contexte effectue maintenant le suivi de toutes ces entités comme `Unchanged` suit :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

L’appel de SaveChanges à ce stade n’aura aucun effet. Toutes les entités sont marquées comme `Unchanged` , donc rien à mettre à jour dans la base de données.

### <a name="generated-key-values"></a>Valeurs de clés générées

Comme indiqué ci-dessus, les [Propriétés de clé](xref:core/modeling/keys) de type entier et GUID sont configurées pour utiliser par défaut des [valeurs de clés générées automatiquement](xref:core/modeling/generated-properties) . Cela présente un avantage majeur lorsque vous travaillez avec des entités déconnectées : une valeur de clé non définie indique que l’entité n’a pas encore été insérée dans la base de données. Cela permet au dispositif de suivi des modifications de détecter automatiquement les nouvelles entités et de les placer dans l' `Added` État. Par exemple, envisagez d’attacher ce graphique à un blog et à des billets :

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Attaching_existing_entities_3)]

Le blog a une valeur de clé de 1, ce qui indique qu’il existe déjà dans la base de données. Deux des publications ont également des valeurs de clés définies, contrairement à la troisième. EF Core constatera que cette valeur de clé est égale à 0, la valeur CLR par défaut pour un entier. Il en résulte EF Core le marquage de la nouvelle entité au `Added` lieu de `Unchanged` :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482636}]
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
```

L’appel de SaveChanges à ce stade ne fait rien avec les `Unchanged` entités, mais insère la nouvelle entité dans la base de données. Par exemple, lors de l’utilisation de SQLite :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Le point important à noter ici est qu’avec les valeurs de clé générées, EF Core est en mesure de **distinguer automatiquement les nouvelles entités existantes dans un graphique déconnecté**. En résumé, lorsque vous utilisez des clés générées, EF Core insèrera toujours une entité lorsque cette entité n’a pas de valeur de clé définie.

## <a name="updating-existing-entities"></a>Mise à jour des entités existantes

### <a name="explicit-key-values"></a>Valeurs de clés explicites

<xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> , et les méthodes équivalentes sur <xref:Microsoft.EntityFrameworkCore.DbSet%601> se comportent exactement comme les `Attach` méthodes décrites ci-dessus, sauf que les entités sont placées dans le `Modfied` au lieu de l' `Unchanged` État. Par exemple, pour démarrer le suivi d’un blog existant, `Modified` procédez comme suit :

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

L’inspection de l' [affichage de débogage du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications après cet appel indique que le contexte effectue le suivi de cette entité dans l' `Modified` État :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

Tout comme avec `Add` et `Attach` , `Update` marque en fait _un graphique complet_ d’entités associées comme `Modified` . Par exemple, pour joindre un blog existant et les publications existantes associées comme `Modified` suit :

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_2)]

Le contexte effectue maintenant le suivi de toutes ces entités comme `Modified` suit :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

L’appel de SaveChanges à ce stade entraîne l’envoi de mises à jour à la base de données pour toutes ces entités. Par exemple, lors de l’utilisation de SQLite :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();
```

### <a name="generated-key-values"></a>Valeurs de clés générées

Comme avec `Attach` , les valeurs de clé générées présentent le même avantage majeur pour `Update` : une valeur de clé non définie indique que l’entité est nouvelle et n’a pas encore été insérée dans la base de données. Comme avec `Attach` , cela permet à DbContext de détecter automatiquement les nouvelles entités et de les placer dans l' `Added` État. Par exemple, envisagez d’appeler `Update` avec ce graphique d’un blog et des billets :

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Updating_existing_entities_3)]

Comme dans l' `Attach` exemple, la publication sans valeur de clé est détectée comme nouvelle et définie sur l' `Added` État. Les autres entités sont marquées comme `Modified` suit :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482633}]
Post {Id: -2147482633} Added
  Id: -2147482633 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

`SaveChanges`Si vous appelez à ce stade, les mises à jour sont envoyées à la base de données pour toutes les entités existantes, tandis que la nouvelle entité est insérée. Par exemple, lors de l’utilisation de SQLite :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Il s’agit d’un moyen très simple de générer des mises à jour et des insertions à partir d’un graphique déconnecté. Toutefois, les mises à jour ou les insertions sont envoyées à la base de données pour chaque propriété de chaque entité suivie, même si certaines valeurs de propriété n’ont pas été modifiées. Ne soyez pas trop effrayé. pour de nombreuses applications avec de petits graphiques, il peut s’agir d’un moyen simple et pragmatique de générer des mises à jour. Cela dit, d’autres modèles plus complexes peuvent parfois aboutir à des mises à jour plus efficaces, comme décrit dans [résolution d’identité dans EF Core](xref:core/change-tracking/identity-resolution).

## <a name="deleting-existing-entities"></a>Suppression d’entités existantes

Pour qu’une entité soit supprimée par SaveChanges, elle doit être suivie dans l' `Deleted` État. Les entités sont généralement placées dans l' `Deleted` État en appelant l’une des <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> méthodes, ou équivalentes sur <xref:Microsoft.EntityFrameworkCore.DbSet%601> . Par exemple, pour marquer une publication existante comme `Deleted` :

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

L’inspection de l' [affichage de débogage du dispositif](xref:core/change-tracking/debug-views) de suivi des modifications après cet appel indique que le contexte effectue le suivi de l’entité dans l' `Deleted` État :

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

Cette entité sera supprimée lorsque SaveChanges est appelé. Par exemple, lors de l’utilisation de SQLite :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

Une fois l’opération SaveChanges terminée, l’entité supprimée est détachée du DbContext, car elle n’existe plus dans la base de données. La vue de débogage est donc vide, car aucune entité n’est suivie.

## <a name="deleting-dependentchild-entities"></a>Suppression d’entités dépendantes/enfants

La suppression d’entités dépendantes/enfants d’un graphique est plus simple que la suppression d’entités principal/parent. Pour plus d’informations, consultez la section suivante et [modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) .

Il est rare d’appeler `Remove` sur une entité créée avec `new` . En outre, `Add` contrairement `Attach` `Update` à et, il est rare d’appeler `Remove` sur une entité qui n’est pas déjà suivie dans `Unchanged` l' `Modified` État ou. Au lieu de cela, il est courant d’effectuer le suivi d’une seule entité ou d’un graphique d’entités associées, puis `Remove` d’appeler sur les entités qui doivent être supprimées. Ce graphique d’entités suivies est généralement créé par :

1. Exécution d’une requête pour les entités
2. À l’aide des `Attach` `Update` méthodes ou sur un graphique d’entités déconnectées, comme décrit dans les sections précédentes.

Par exemple, le code de la section précédente est plus susceptible d’obtenir une publication à partir d’un client, puis d’effectuer une opération similaire à ce qui suit :

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

Cela se comporte exactement de la même façon que dans l’exemple précédent, puisque l’appel `Remove` de sur une entité non suivie provoque l’attachement préalable de celui-ci, puis il est marqué comme `Deleted` .

Dans des exemples plus réalistes, un graphique d’entités est d’abord attaché, puis certaines de ces entités sont marquées comme supprimées. Par exemple :

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

Toutes les entités sont marquées comme `Unchanged` , à l’exception de celle sur laquelle `Remove` a été appelé :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
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

Cette entité sera supprimée lorsque SaveChanges est appelé. Par exemple, lors de l’utilisation de SQLite :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

Une fois l’opération SaveChanges terminée, l’entité supprimée est détachée du DbContext, car elle n’existe plus dans la base de données. Les autres entités restent à l' `Unchanged` État :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
```

## <a name="deleting-principalparent-entities"></a>Suppression des entités principal/parent

Chaque relation qui connecte deux types d’entité a un principal ou un end parent, ainsi qu’une terminaison dépendante ou enfant. L’entité dépendante/enfant est celle avec la propriété de clé étrangère. Dans une relation un-à-plusieurs, le principal/le parent se trouve du côté « un », et le dépendant/enfant se trouve du côté « plusieurs ». Pour plus d’informations, consultez [relations](xref:core/modeling/relationships) .

Dans les exemples précédents, nous avons supprimé une publication, qui est une entité dépendante/enfant dans le blog, qui publie une relation un-à-plusieurs. Cela est relativement simple, car la suppression d’une entité dépendante/enfant n’a aucun impact sur les autres entités. En revanche, la suppression d’une entité principale/parente doit également avoir un impact sur les entités dépendantes/enfants. Si vous ne le faites pas, une valeur de clé étrangère fait référence à une valeur de clé primaire qui n’existe plus. Il s’agit d’un état de modèle non valide et génère une erreur de contrainte référentielle dans la plupart des bases de données.

Cet état de modèle non valide peut être géré de deux manières :

1. Attribution de la valeur null à ces valeurs. Cela indique que les éléments dépendants/enfants ne sont plus associés à un principal/parent. Il s’agit de la valeur par défaut pour les relations facultatives où la clé étrangère doit être Nullable. L’affectation de la valeur null à ce paramètre n’est pas valide pour les relations requises, où la clé étrangère est généralement non Nullable.
2. Suppression des objets dépendants/enfants. Il s’agit de la valeur par défaut pour les relations requises et est également valide pour les relations facultatives.

Pour plus d’informations sur le suivi des modifications et les relations, consultez [modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) .

### <a name="optional-relationships"></a>Relations facultatives

La `Post.BlogId` propriété de clé étrangère accepte la valeur null dans le modèle que nous utilisons. Cela signifie que la relation est facultative et, par conséquent, le comportement par défaut de EF Core consiste à définir des `BlogId` Propriétés de clé étrangère sur Null lorsque le blog est supprimé. Par exemple :

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

L’inspection de l' [affichage de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) après l’appel à `Remove` montre que, comme prévu, le blog est désormais marqué comme `Deleted` suit :

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

Il est plus intéressant de voir que toutes les publications associées sont désormais marquées comme `Modified` . Cela est dû au fait que la propriété de clé étrangère dans chaque entité a été définie sur null. L’appel de SaveChanges met à jour la valeur de clé étrangère pour chaque publication sur la valeur null dans la base de données avant de supprimer le blog :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p2;
SELECT changes();
```

Une fois l’opération SaveChanges terminée, l’entité supprimée est détachée du DbContext, car elle n’existe plus dans la base de données. Les autres entités sont désormais marquées comme `Unchanged` avec des valeurs de clé étrangère null, qui correspondent à l’état de la base de données :

```output
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: <null> FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: <null> FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

### <a name="required-relationships"></a>Relations requises

Si la `Post.BlogId` propriété de clé étrangère n’accepte pas les valeurs NULL, la relation entre les blogs et les publications devient « obligatoire ». Dans ce cas, EF Core supprime par défaut les entités dépendantes/enfants lorsque le principal/parent est supprimé. Par exemple, la suppression d’un blog avec des publications associées comme dans l’exemple précédent :

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

L’inspection de l' [affichage de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) après l’appel à `Remove` indique que, comme prévu, le blog est de nouveau marqué comme `Deleted` suit :

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Deleted
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

Dans ce cas, il est plus intéressant de voir que toutes les publications associées ont également été marquées comme `Deleted` . Si vous appelez SaveChanges, le blog et toutes les publications associées sont supprimés de la base de données :

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p1;
```

Une fois SaveChanges terminé, toutes les entités supprimées sont détachées du DbContext puisqu’elles n’existent plus dans la base de données. La sortie de la vue de débogage est donc vide.

> [!NOTE]
> Ce document ne présente que la surface de l’utilisation des relations dans EF Core. Pour plus d’informations sur les relations de modélisation et sur la [modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes) , consultez [relations](xref:core/modeling/relationships) . pour plus d’informations sur la mise à jour et la suppression d’entités dépendantes/enfants lors de l’appel de SaveChanges.

## <a name="custom-tracking-with-trackgraph"></a>Suivi personnalisé avec TrackGraph

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> fonctionne comme `Add` `Attach` et, `Update` à ceci près qu’il génère un rappel pour chaque instance d’entité avant de le suivre. Cela permet d’utiliser une logique personnalisée pour déterminer comment suivre les entités individuelles dans un graphique.

Par exemple, considérez la règle utilisée par EF Core lors du suivi des entités ayant des valeurs de clé générées : si la valeur de clé est égale à zéro, l’entité est nouvelle et doit être insérée. Nous allons étendre cette règle pour indiquer si la valeur de clé est négative, puis l’entité doit être supprimée. Cela nous permet de modifier les valeurs de clé primaire dans les entités d’un graphique déconnecté pour marquer les entités supprimées :

<!--
            blog.Posts.Add(
                new Post
                {
                    Title = "Announcing .NET 5.0",
                    Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                }
            );

            var toDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            toDelete.Id = -toDelete.Id;
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1a](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1a)]

Ce graphique déconnecté peut ensuite être suivi à l’aide de TrackGraph :

<!--
        public static void UpdateBlog(Blog blog)
        {
            using var context = new BlogsContext();

            context.ChangeTracker.TrackGraph(
                blog, node =>
                    {
                        var propertyEntry = node.Entry.Property("Id");
                        var keyValue = (int)propertyEntry.CurrentValue;

                        if (keyValue == 0)
                        {
                            node.Entry.State = EntityState.Added;
                        }
                        else if (keyValue < 0)
                        {
                            propertyEntry.CurrentValue = -keyValue;
                            node.Entry.State = EntityState.Deleted;
                        }
                        else
                        {
                            node.Entry.State = EntityState.Modified;
                        }

                        Console.WriteLine($"Tracking {node.Entry.Metadata.DisplayName()} with key value {keyValue} as {node.Entry.State}");

                    });

            context.SaveChanges();
        }
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1b](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1b)]

Pour chaque entité du graphique, le code ci-dessus vérifie la valeur de clé primaire _avant le suivi de l’entité_. Pour les valeurs de clé non définies (zéro), le code fait ce que EF Coreait normalement. Autrement dit, si la clé n’est pas définie, l’entité est marquée comme `Added` . Si la clé est définie et que la valeur n’est pas négative, l’entité est marquée comme `Modified` . Toutefois, si une valeur de clé négative est trouvée, sa valeur réelle, non négative, est restaurée et l’entité est suivie comme `Deleted` .

La sortie de l’exécution de ce code est la suivante :

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> Par souci de simplicité, ce code suppose que chaque entité a une propriété de clé primaire entière appelée `Id` . Cela peut être codifié dans une interface ou une classe de base abstraite. Sinon, la ou les propriétés de clé primaire peuvent être obtenues à partir des <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> métadonnées afin que ce code fonctionne avec n’importe quel type d’entité.

TrackGraph a deux surcharges. Dans la surcharge simple utilisée ci-dessus, EF Core détermine quand cesser de traverser le graphique. Plus précisément, il cesse de visiter les nouvelles entités associées à partir d’une entité donnée quand cette entité est déjà suivie, ou lorsque le rappel ne commence pas à effectuer le suivi de l’entité.

La surcharge avancée, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> , a un rappel qui retourne une valeur booléenne. Si le rappel retourne la valeur false, alors le parcours du graphique s’arrête. sinon, il continue. Vous devez veiller à éviter les boucles infinies lors de l’utilisation de cette surcharge.

La surcharge avancée autorise également la fourniture de l’État à TrackGraph et cet État est ensuite passé à chaque rappel.
