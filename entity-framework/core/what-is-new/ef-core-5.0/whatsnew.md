---
title: Nouveautés de EF Core 5,0
description: Vue d’ensemble des nouvelles fonctionnalités de EF Core 5,0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 0605d021b46066c6af7b631c99e86c0e53caa8db
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070755"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="dd7de-103">Nouveautés de EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="dd7de-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="dd7de-104">Toutes les fonctionnalités prévues pour EF Core 5,0 sont maintenant terminées.</span><span class="sxs-lookup"><span data-stu-id="dd7de-104">All features planned for EF Core 5.0 have now been completed.</span></span> <span data-ttu-id="dd7de-105">Cette page contient une vue d’ensemble des changements intéressants introduits dans chaque version préliminaire.</span><span class="sxs-lookup"><span data-stu-id="dd7de-105">This page contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="dd7de-106">Cette page ne duplique pas le [plan pour EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="dd7de-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="dd7de-107">Le plan décrit les thèmes globaux pour EF Core 5,0, y compris tout ce que nous envisageons d’inclure avant d’expédier la version finale.</span><span class="sxs-lookup"><span data-stu-id="dd7de-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="rc1"></a><span data-ttu-id="dd7de-108">RC1</span><span class="sxs-lookup"><span data-stu-id="dd7de-108">RC1</span></span>

### <a name="many-to-many"></a><span data-ttu-id="dd7de-109">Plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="dd7de-109">Many-to-many</span></span>

<span data-ttu-id="dd7de-110">EF Core 5,0 prend en charge les relations plusieurs-à-plusieurs sans mapper explicitement la table de jointure.</span><span class="sxs-lookup"><span data-stu-id="dd7de-110">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="dd7de-111">Par exemple, considérez les types d’entités suivants :</span><span class="sxs-lookup"><span data-stu-id="dd7de-111">For example, consider these entity types:</span></span>

```C#
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Tag> Tags { get; set; }
}

public class Tag
{
    public int Id { get; set; }
    public string Text { get; set; }
    public ICollection<Post> Posts { get; set; }
}
```

<span data-ttu-id="dd7de-112">Notez que `Post` contient une collection de `Tags` et `Tag` contient une collection de `Posts` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-112">Notice that `Post` contains a collection of `Tags`, and `Tag` contains a collection of `Posts`.</span></span> <span data-ttu-id="dd7de-113">EF Core 5,0 reconnaît qu’il s’agit d’une relation plusieurs-à-plusieurs par Convention.</span><span class="sxs-lookup"><span data-stu-id="dd7de-113">EF Core 5.0 recognizes this as a many-to-many relationship by convention.</span></span> <span data-ttu-id="dd7de-114">Cela signifie qu’aucun code n’est requis dans `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="dd7de-114">This means no code is required in `OnModelCreating`:</span></span>

```C#
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="dd7de-115">Lorsque des migrations (ou `EnsureCreated` ) sont utilisées pour créer la base de données, EF Core crée automatiquement la table de jointure.</span><span class="sxs-lookup"><span data-stu-id="dd7de-115">When Migrations (or `EnsureCreated`) are used to create the database, EF Core will automatically create the join table.</span></span> <span data-ttu-id="dd7de-116">Par exemple, sur SQL Server pour ce modèle, EF Core génère :</span><span class="sxs-lookup"><span data-stu-id="dd7de-116">For example, on SQL Server for this model, EF Core generates:</span></span>

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id])
);

CREATE TABLE [Tag] (
    [Id] int NOT NULL IDENTITY,
    [Text] nvarchar(max) NULL,
    CONSTRAINT [PK_Tag] PRIMARY KEY ([Id])
);

CREATE TABLE [PostTag] (
    [PostsId] int NOT NULL,
    [TagsId] int NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([Id]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tag_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tag] ([Id]) ON DELETE CASCADE
);

CREATE INDEX [IX_PostTag_TagsId] ON [PostTag] ([TagsId]);
```

<span data-ttu-id="dd7de-117">La création et `Blog` l’Association `Post` d’entités et les résultats des mises à jour de la table de jointure se produisent automatiquement.</span><span class="sxs-lookup"><span data-stu-id="dd7de-117">Creating and associating `Blog` and `Post` entities results in join table updates happening automatically.</span></span> <span data-ttu-id="dd7de-118">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-118">For example:</span></span>

```C#
var beginnerTag = new Tag {Text = "Beginner"};
var advancedTag = new Tag {Text = "Advanced"};
var efCoreTag = new Tag {Text = "EF Core"};

context.AddRange(
    new Post {Name = "EF Core 101", Tags = new List<Tag> {beginnerTag, efCoreTag}},
    new Post {Name = "Writing an EF database provider", Tags = new List<Tag> {advancedTag, efCoreTag}},
    new Post {Name = "Savepoints in EF Core", Tags = new List<Tag> {beginnerTag, efCoreTag}});

context.SaveChanges();
```

<span data-ttu-id="dd7de-119">Une fois les billets et les balises insérés, EF crée automatiquement des lignes dans la table de jointure.</span><span class="sxs-lookup"><span data-stu-id="dd7de-119">After inserting the Posts and Tags, EF will then automatically create rows in the join table.</span></span> <span data-ttu-id="dd7de-120">Par exemple, sur SQL Server :</span><span class="sxs-lookup"><span data-stu-id="dd7de-120">For example, on SQL Server:</span></span>

```sql
SET NOCOUNT ON;
INSERT INTO [PostTag] ([PostsId], [TagsId])
VALUES (@p6, @p7),
(@p8, @p9),
(@p10, @p11),
(@p12, @p13),
(@p14, @p15),
(@p16, @p17);
```

<span data-ttu-id="dd7de-121">Pour les requêtes, l’inclusion et d’autres opérations de requête fonctionnent comme pour toute autre relation.</span><span class="sxs-lookup"><span data-stu-id="dd7de-121">For queries, Include and other query operations work just like for any other relationship.</span></span> <span data-ttu-id="dd7de-122">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-122">For example:</span></span>

```C#
foreach (var post in context.Posts.Include(e => e.Tags))
{
    Console.Write($"Post \"{post.Name}\" has tags");

    foreach (var tag in post.Tags)
    {
        Console.Write($" '{tag.Text}'");
    }
}
```

<span data-ttu-id="dd7de-123">Le SQL généré utilise automatiquement la table de jointure pour rétablir toutes les balises associées :</span><span class="sxs-lookup"><span data-stu-id="dd7de-123">The SQL generated uses the join table automatically to bring back all related Tags:</span></span>

```sql
SELECT [p].[Id], [p].[Name], [t0].[PostsId], [t0].[TagsId], [t0].[Id], [t0].[Text]
FROM [Posts] AS [p]
LEFT JOIN (
    SELECT [p0].[PostsId], [p0].[TagsId], [t].[Id], [t].[Text]
    FROM [PostTag] AS [p0]
    INNER JOIN [Tag] AS [t] ON [p0].[TagsId] = [t].[Id]
) AS [t0] ON [p].[Id] = [t0].[PostsId]
ORDER BY [p].[Id], [t0].[PostsId], [t0].[TagsId], [t0].[Id]
```

<span data-ttu-id="dd7de-124">Contrairement à EF6, EF Core permet une personnalisation complète de la table de jointure.</span><span class="sxs-lookup"><span data-stu-id="dd7de-124">Unlike EF6, EF Core allows full customization of the join table.</span></span> <span data-ttu-id="dd7de-125">Par exemple, le code ci-dessous configure une relation plusieurs-à-plusieurs qui a également des navigations vers l’entité de jointure, et dans laquelle l’entité de jointure contient une propriété de charge utile :</span><span class="sxs-lookup"><span data-stu-id="dd7de-125">For example, the code below configures a many-to-many relationship that also has navigations to the join entity, and in which the join entity contains a payload property:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Community>()
        .HasMany(e => e.Members)
        .WithMany(e => e.Memberships)
        .UsingEntity<PersonCommunity>(
            b => b.HasOne(e => e.Member).WithMany().HasForeignKey(e => e.MembersId),
            b => b.HasOne(e => e.Membership).WithMany().HasForeignKey(e => e.MembershipsId))
        .Property(e => e.MemberSince).HasDefaultValueSql("CURRENT_TIMESTAMP");
}
```

### <a name="map-entity-types-to-queries"></a><span data-ttu-id="dd7de-126">Mapper les types d’entités aux requêtes</span><span class="sxs-lookup"><span data-stu-id="dd7de-126">Map entity types to queries</span></span>

<span data-ttu-id="dd7de-127">Les types d’entités sont couramment mappés à des tables ou à des vues de telle sorte que EF Core récupère le contenu de la table ou de la vue lors de l’interrogation de ce type.</span><span class="sxs-lookup"><span data-stu-id="dd7de-127">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="dd7de-128">EF Core 5,0 permet à un type d’entité d’être mappé à une « définition de requête ».</span><span class="sxs-lookup"><span data-stu-id="dd7de-128">EF Core 5.0 allows an entity type to mapped to a "defining query".</span></span> <span data-ttu-id="dd7de-129">(Ceci était partiellement pris en charge dans les versions précédentes, mais a été considérablement amélioré et sa syntaxe est différente dans EF Core 5,0.)</span><span class="sxs-lookup"><span data-stu-id="dd7de-129">(This was partially supported in previous versions, but is much improved and has different syntax in EF Core 5.0.)</span></span>

<span data-ttu-id="dd7de-130">Prenons l’exemple de deux tables : l’un avec les publications modernes ; l’autre avec les publications héritées.</span><span class="sxs-lookup"><span data-stu-id="dd7de-130">For example, consider two tables; one with modern posts; the other with legacy posts.</span></span> <span data-ttu-id="dd7de-131">Le tableau des publications modernes contient des colonnes supplémentaires, mais dans le cadre de notre application, nous souhaitons que les publications moderne et héritée TP soient combinées et mappées à un type d’entité avec toutes les propriétés nécessaires :</span><span class="sxs-lookup"><span data-stu-id="dd7de-131">The modern posts table has some additional columns, but for the purpose of our application we want both modern and legacy posts tp be combined and mapped to an entity type with all necessary properties:</span></span>

```c#
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Category { get; set; }
    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

<span data-ttu-id="dd7de-132">Dans EF Core 5,0, `ToSqlQuery` peut être utilisé pour mapper ce type d’entité à une requête qui extrait et combine des lignes des deux tables :</span><span class="sxs-lookup"><span data-stu-id="dd7de-132">In EF Core 5.0, `ToSqlQuery` can be used to map this entity type to a query that pulls and combines rows from both tables:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

<span data-ttu-id="dd7de-133">Notez que la `legacy_posts` table n’a pas de `Category` colonne, donc nous synthétisons plutôt une valeur par défaut pour toutes les publications héritées.</span><span class="sxs-lookup"><span data-stu-id="dd7de-133">Notice that the `legacy_posts` table does not have a `Category` column, so we instead synthesize a default value for all legacy posts.</span></span>

<span data-ttu-id="dd7de-134">Ce type d’entité peut ensuite être utilisé de manière normale pour les requêtes LINQ.</span><span class="sxs-lookup"><span data-stu-id="dd7de-134">This entity type can then be used in the normal way for LINQ queries.</span></span> <span data-ttu-id="dd7de-135">Par exemple,</span><span class="sxs-lookup"><span data-stu-id="dd7de-135">For example.</span></span> <span data-ttu-id="dd7de-136">requête LINQ :</span><span class="sxs-lookup"><span data-stu-id="dd7de-136">the LINQ query:</span></span>

```c#
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

<span data-ttu-id="dd7de-137">Génère le code SQL suivant sur SQLite :</span><span class="sxs-lookup"><span data-stu-id="dd7de-137">Generates the following SQL on SQLite:</span></span>

```sql
SELECT "p"."Id", "p"."BlogId", "p"."Category", "p"."Name"
FROM (
    SELECT Id, Name, Category, BlogId FROM posts
    UNION ALL
    SELECT Id, Name, "Legacy", BlogId from legacy_posts
) AS "p"
INNER JOIN "Blogs" AS "b" ON "p"."BlogId" = "b"."Id"
WHERE ('Unicorn' = '') OR (instr("b"."Name", 'Unicorn') > 0)
```

<span data-ttu-id="dd7de-138">Notez que la requête configurée pour le type d’entité est utilisée comme base pour composer la requête LINQ complète.</span><span class="sxs-lookup"><span data-stu-id="dd7de-138">Notice that the query configured for the entity type is used as a starting for composing the full LINQ query.</span></span>

### <a name="event-counters"></a><span data-ttu-id="dd7de-139">Compteurs d’événements</span><span class="sxs-lookup"><span data-stu-id="dd7de-139">Event counters</span></span>

<span data-ttu-id="dd7de-140">Les [compteurs d’événements .net](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) permettent d’exposer efficacement les métriques de performances à partir d’une application.</span><span class="sxs-lookup"><span data-stu-id="dd7de-140">[.NET event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) are a way to efficiently expose performance metrics from an application.</span></span> <span data-ttu-id="dd7de-141">EF Core 5,0 comprend des compteurs d’événements sous la `Microsoft.EntityFrameworkCore` catégorie.</span><span class="sxs-lookup"><span data-stu-id="dd7de-141">EF Core 5.0 includes event counters under the `Microsoft.EntityFrameworkCore` category.</span></span> <span data-ttu-id="dd7de-142">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-142">For example:</span></span>

```
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

<span data-ttu-id="dd7de-143">Cela indique aux compteurs dotnet de commencer à collecter des événements de EF Core pour le processus 49496.</span><span class="sxs-lookup"><span data-stu-id="dd7de-143">This tells dotnet counters to start collecting EF Core events for process 49496.</span></span> <span data-ttu-id="dd7de-144">Cela génère une sortie similaire à celle-ci dans la console :</span><span class="sxs-lookup"><span data-stu-id="dd7de-144">This generates output like this in the console:</span></span>

```
[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                     1,755
    Queries (Total)                                            98,402
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             1
```

### <a name="property-bags"></a><span data-ttu-id="dd7de-145">Conteneurs de propriétés</span><span class="sxs-lookup"><span data-stu-id="dd7de-145">Property bags</span></span>

<span data-ttu-id="dd7de-146">EF Core 5,0 permet au même type CLR d’être mappé à plusieurs types d’entité différents.</span><span class="sxs-lookup"><span data-stu-id="dd7de-146">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types.</span></span> <span data-ttu-id="dd7de-147">Ces types sont appelés types d’entité de type Shared.</span><span class="sxs-lookup"><span data-stu-id="dd7de-147">Such types are known as shared-type entity types.</span></span> <span data-ttu-id="dd7de-148">Cette fonctionnalité combinée aux propriétés d’indexeur (incluses dans Preview 1) permet d’utiliser des conteneurs de propriétés comme type d’entité.</span><span class="sxs-lookup"><span data-stu-id="dd7de-148">This feature combined with indexer properties (included in preview 1) allows property bags to be used as entity type.</span></span>

<span data-ttu-id="dd7de-149">Par exemple, le DbContext ci-dessous configure le type BCL `Dictionary<string, object>` en tant que type d’entité de type partagé pour les produits et les catégories.</span><span class="sxs-lookup"><span data-stu-id="dd7de-149">For example, the DbContext below configures the BCL type `Dictionary<string, object>` as a shared-type entity type for both products and categories.</span></span>

```c#
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");
    public DbSet<Dictionary<string, object>> Categories => Set<Dictionary<string, object>>("Category");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Category", b =>
        {
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
        });

        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<decimal>("Price");
            b.IndexerProperty<int?>("CategoryId");

            b.HasOne("Category", null).WithMany();
        });
    }
}
```

<span data-ttu-id="dd7de-150">Les objets dictionary (« conteneurs de propriétés ») peuvent maintenant être ajoutés au contexte en tant qu’instances d’entité et enregistrés.</span><span class="sxs-lookup"><span data-stu-id="dd7de-150">Dictionary objects ("property bags") can now be added to the context as entity instances and saved.</span></span> <span data-ttu-id="dd7de-151">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-151">For example:</span></span>

```c#
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

<span data-ttu-id="dd7de-152">Ces entités peuvent ensuite être interrogées et mises à jour de manière normale :</span><span class="sxs-lookup"><span data-stu-id="dd7de-152">These entities can then be queried and updated in the normal way:</span></span>

```c#
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a><span data-ttu-id="dd7de-153">Interception d’SaveChanges et événements</span><span class="sxs-lookup"><span data-stu-id="dd7de-153">SaveChanges interception and events</span></span>

<span data-ttu-id="dd7de-154">EF Core 5,0 introduit les événements .NET et un intercepteur de EF Core déclenché lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="dd7de-154">EF Core 5.0 introduces both .NET events and an EF Core interceptor triggered when SaveChanges is called.</span></span>

<span data-ttu-id="dd7de-155">Les événements sont simples à utiliser. par exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-155">The events are simple to use; for example:</span></span>

```c#
context.SavingChanges += (sender, args) =>
{
    Console.WriteLine($"Saving changes for {((DbContext)sender).Database.GetConnectionString()}");
};

context.SavedChanges += (sender, args) =>
{
    Console.WriteLine($"Saved {args.EntitiesSavedCount} changes for {((DbContext)sender).Database.GetConnectionString()}");
};
```

<span data-ttu-id="dd7de-156">Notez que :</span><span class="sxs-lookup"><span data-stu-id="dd7de-156">Notice that:</span></span>
* <span data-ttu-id="dd7de-157">L’expéditeur de l’événement est l' `DbContext` instance</span><span class="sxs-lookup"><span data-stu-id="dd7de-157">The event sender is the `DbContext` instance</span></span>
* <span data-ttu-id="dd7de-158">Les arguments de l' `SavedChanges` événement contiennent le nombre d’entités enregistrées dans la base de données</span><span class="sxs-lookup"><span data-stu-id="dd7de-158">The args for the `SavedChanges` event contains the number of entities saved to the database</span></span>

<span data-ttu-id="dd7de-159">L’intercepteur est défini par `ISaveChangesInterceptor` , mais il est souvent convienient d’hériter de `SaveChangesInterceptor` pour éviter d’implémenter chaque méthode.</span><span class="sxs-lookup"><span data-stu-id="dd7de-159">The interceptor is defined by `ISaveChangesInterceptor`, but it is often convienient to inherit from `SaveChangesInterceptor` to avoid implementing every method.</span></span> <span data-ttu-id="dd7de-160">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-160">For example:</span></span>

```c#
public class MySaveChangesInterceptor : SaveChangesInterceptor
{
    public override InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        Console.WriteLine($"Saving changes for {eventData.Context.Database.GetConnectionString()}");

        return result;
    }

    public override ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        Console.WriteLine($"Saving changes asynchronously for {eventData.Context.Database.GetConnectionString()}");

        return new ValueTask<InterceptionResult<int>>(result);
    }
}
```

<span data-ttu-id="dd7de-161">Notez que :</span><span class="sxs-lookup"><span data-stu-id="dd7de-161">Notice that:</span></span>
* <span data-ttu-id="dd7de-162">L’intercepteur a à la fois des méthodes synchrones et asynchrones.</span><span class="sxs-lookup"><span data-stu-id="dd7de-162">The interceptor has both sync and async methods.</span></span> <span data-ttu-id="dd7de-163">Cela peut être utile si vous devez effectuer des e/s asynchrones, telles que l’écriture sur un serveur d’audit.</span><span class="sxs-lookup"><span data-stu-id="dd7de-163">This can be useful if you need to perform async I/O, such as writing to an audit server.</span></span>
* <span data-ttu-id="dd7de-164">L’intercepteur permet d’ignorer SaveChanges à l’aide du `InterceptionResult` mécanisme commun à tous les intercepteurs.</span><span class="sxs-lookup"><span data-stu-id="dd7de-164">The interceptor allows SaveChanges to be skipped using the `InterceptionResult` mechanism common to all interceptors.</span></span>

<span data-ttu-id="dd7de-165">L’inconvénient des intercepteurs est qu’ils doivent être inscrits sur le DbContext lorsqu’il est en cours de construction.</span><span class="sxs-lookup"><span data-stu-id="dd7de-165">The downside of interceptors is that they must be registered on the DbContext when it is being constructed.</span></span> <span data-ttu-id="dd7de-166">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-166">For example:</span></span>

```c#
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .AddInterceptors(new MySaveChangesInterceptor())
            .UseSqlite("Data Source = test.db");
```

<span data-ttu-id="dd7de-167">En revanche, les événements peuvent être enregistrés sur l’instance DbContext à tout moment.</span><span class="sxs-lookup"><span data-stu-id="dd7de-167">In contrast, the events can be registered on the DbContext instance at any time.</span></span>

### <a name="exclude-tables-from-migrations"></a><span data-ttu-id="dd7de-168">Exclure des tables des migrations</span><span class="sxs-lookup"><span data-stu-id="dd7de-168">Exclude tables from migrations</span></span>

<span data-ttu-id="dd7de-169">Il est parfois utile d’avoir un seul type d’entité mappé dans plusieurs DbContexts.</span><span class="sxs-lookup"><span data-stu-id="dd7de-169">It is sometimes useful to have a single entity type mapped in multiple DbContexts.</span></span> <span data-ttu-id="dd7de-170">Cela est particulièrement vrai lorsque vous utilisez des [contextes délimités](https://www.martinfowler.com/bliki/BoundedContext.html), pour lesquels il est courant d’avoir un type DbContext différent pour chaque contexte délimité.</span><span class="sxs-lookup"><span data-stu-id="dd7de-170">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different DbContext type for each bounded context.</span></span>

<span data-ttu-id="dd7de-171">Par exemple, un `User` type peut être nécessaire à la fois par un contexte d’autorisation et un contexte de création de rapports.</span><span class="sxs-lookup"><span data-stu-id="dd7de-171">For example, a `User` type may be needed by both an authorization context and a reporting context.</span></span> <span data-ttu-id="dd7de-172">Si une modification est apportée au `User` type, les migrations pour les deux DbContexts tentent de mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-172">If a change is made to the `User` type, then migrations for both DbContexts will attempt to update the database.</span></span> <span data-ttu-id="dd7de-173">Pour éviter cela, le modèle de l’un des contextes peut être configuré pour exclure la table de ses migrations.</span><span class="sxs-lookup"><span data-stu-id="dd7de-173">To prevent this, the model for one of the contexts can be configured to exclude the table from its migrations.</span></span>

<span data-ttu-id="dd7de-174">Dans le code ci-dessous, le `AuthorizationContext` génère des migrations pour les modifications apportées à la `Users` table, mais ce n' `ReportingContext` est pas le cas, ce qui empêche les migrations d’entrer en conflit.</span><span class="sxs-lookup"><span data-stu-id="dd7de-174">In the code below, the `AuthorizationContext` will generate migrations for changes to the `Users` table, but the `ReportingContext` will not, preventing the migrations from clashing.</span></span>

```C#
public class AuthorizationContext : DbContext
{
    public DbSet<User> Users { get; set; }
}

public class ReportingContext : DbContext
{
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>().ToTable("Users", t => t.ExcludeFromMigrations());
    }
}
```

### <a name="required-11-dependents"></a><span data-ttu-id="dd7de-175">Dépendants 1:1 requis</span><span class="sxs-lookup"><span data-stu-id="dd7de-175">Required 1:1 dependents</span></span>

<span data-ttu-id="dd7de-176">Dans EF Core 3,1, la terminaison dépendante d’une relation un-à-un était toujours considérée comme facultative.</span><span class="sxs-lookup"><span data-stu-id="dd7de-176">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="dd7de-177">Cela était le plus évident lors de l’utilisation d’entités détenues.</span><span class="sxs-lookup"><span data-stu-id="dd7de-177">This was most apparent when using owned entities.</span></span> <span data-ttu-id="dd7de-178">Par exemple, considérez le modèle et la configuration suivants :</span><span class="sxs-lookup"><span data-stu-id="dd7de-178">For example, consider the following model and configuration:</span></span>

```c#
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }

    public Address HomeAddress { get; set; }
    public Address WorkAddress { get; set; }
}

public class Address
{
    public string Line1 { get; set; }
    public string Line2 { get; set; }
    public string City { get; set; }
    public string Region { get; set; }
    public string Country { get; set; }
    public string Postcode { get; set; }
}
```

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

<span data-ttu-id="dd7de-179">Cela entraîne des migrations créant la table suivante pour SQLite :</span><span class="sxs-lookup"><span data-stu-id="dd7de-179">This results in Migrations creating the following table for SQLite:</span></span>

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NULL,
    "HomeAddress_Region" TEXT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

<span data-ttu-id="dd7de-180">Notez que toutes les colonnes acceptent la valeur null, même si certaines des `HomeAddress` propriétés ont été configurées selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="dd7de-180">Notice that all the columns are nullable, even though some of the `HomeAddress` properties have been configured as required.</span></span> <span data-ttu-id="dd7de-181">En outre, lors de l’interrogation d’un `Person` , si toutes les colonnes pour l’adresse personnelle ou professionnelle ont la valeur null, EF Core laisse les `HomeAddress` Propriétés and/or `WorkAddress` comme null, au lieu de définir une instance vide de `Address` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-181">Also, when querying for a `Person`, if all the columns for either the home or work address are null, then EF Core will leave the `HomeAddress` and/or `WorkAddress` properties as null, rather than setting an empty instance of `Address`.</span></span>

<span data-ttu-id="dd7de-182">Dans EF Core 5,0, la `HomeAddress` navigation peut désormais être configurée comme un dépendant obligatoire.</span><span class="sxs-lookup"><span data-stu-id="dd7de-182">In EF Core 5.0, the `HomeAddress` navigation can now be configured as as a required dependent.</span></span> <span data-ttu-id="dd7de-183">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-183">For example:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

<span data-ttu-id="dd7de-184">La table créée par les migrations inclue désormais des colonnes non Nullable pour les propriétés requises du dépendant requis :</span><span class="sxs-lookup"><span data-stu-id="dd7de-184">The table created by Migrations will now included non-nullable columns for the required properties of the required dependent:</span></span>

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NOT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NOT NULL,
    "HomeAddress_Region" TEXT NOT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NOT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

<span data-ttu-id="dd7de-185">En outre, EF Core lève désormais une exception en cas de tentative d’enregistrement d’un propriétaire qui a un dépendant obligatoire null.</span><span class="sxs-lookup"><span data-stu-id="dd7de-185">In addition, EF Core will now throw an exception if an attempt is made to save an owner which has a null required dependent.</span></span> <span data-ttu-id="dd7de-186">Dans cet exemple, EF Core lève une exception quand vous tentez d’enregistrer un `Person` avec une valeur null `HomeAddress` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-186">In this example, EF Core will throw when attempting to save a `Person` with a null `HomeAddress`.</span></span>

<span data-ttu-id="dd7de-187">Enfin, EF Core créera toujours une instance d’un dépendant obligatoire, même si toutes les colonnes de la dépendance requise ont des valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="dd7de-187">Finally, EF Core will still create an instance of a required dependent even when all the columns for the required dependent have null values.</span></span>

### <a name="options-for-migration-generation"></a><span data-ttu-id="dd7de-188">Options de génération de la migration</span><span class="sxs-lookup"><span data-stu-id="dd7de-188">Options for migration generation</span></span>

<span data-ttu-id="dd7de-189">EF Core 5,0 offre un contrôle accru de la génération des migrations à des fins différentes.</span><span class="sxs-lookup"><span data-stu-id="dd7de-189">EF Core 5.0 introduces greater control over generation of migrations for different purposes.</span></span> <span data-ttu-id="dd7de-190">Cela comprend la possibilité de :</span><span class="sxs-lookup"><span data-stu-id="dd7de-190">This includes the ability to:</span></span>

* <span data-ttu-id="dd7de-191">Savoir si la migration est en cours de génération pour un script ou pour une exécution immédiate</span><span class="sxs-lookup"><span data-stu-id="dd7de-191">Know if the migration is being generated for a script or for immediate execution</span></span>
* <span data-ttu-id="dd7de-192">Savoir si un script idempotent est en cours de génération</span><span class="sxs-lookup"><span data-stu-id="dd7de-192">Know if an idempotent script is being generated</span></span>
* <span data-ttu-id="dd7de-193">Sachez si le script doit exclure les instructions de transaction (voir les _scripts de migration avec les transactions_ ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="dd7de-193">Know if the script should exclude transaction statements (See _Migrations scripts with transactions_ below.)</span></span>

<span data-ttu-id="dd7de-194">Ce comportement est spécifié par un `MigrationsSqlGenerationOptions` enum, qui peut désormais être passé à `IMigrator.GenerateScript` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-194">This behavior is specified by an the `MigrationsSqlGenerationOptions` enum, which can now be passed to `IMigrator.GenerateScript`.</span></span>

<span data-ttu-id="dd7de-195">Ce travail inclut également une meilleure génération de scripts idempotent avec des appels à `EXEC` sur SQL Server lorsque cela est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="dd7de-195">Also included in this work is better generation of idempotent scripts with calls to `EXEC` on SQL Server when needed.</span></span> <span data-ttu-id="dd7de-196">Ce travail permet également d’apporter des améliorations similaires aux scripts générés par d’autres fournisseurs de bases de données, notamment PostgreSQL.</span><span class="sxs-lookup"><span data-stu-id="dd7de-196">This work also enables similar improvements to the scripts generated by other database providers, including PostgreSQL.</span></span>

### <a name="migrations-scripts-with-transactions"></a><span data-ttu-id="dd7de-197">Migration de scripts avec transactions</span><span class="sxs-lookup"><span data-stu-id="dd7de-197">Migrations scripts with transactions</span></span>

<span data-ttu-id="dd7de-198">Les scripts SQL générés à partir des migrations contiennent désormais des instructions pour commencer et valider des transactions en fonction de la migration.</span><span class="sxs-lookup"><span data-stu-id="dd7de-198">SQL scripts generated from migrations now contain statements to begin and commit transactions as appropriate for the migration.</span></span> <span data-ttu-id="dd7de-199">Par exemple, le script de migration ci-dessous a été généré à partir de deux migrations.</span><span class="sxs-lookup"><span data-stu-id="dd7de-199">For example, the migration script below was generated from two migrations.</span></span> <span data-ttu-id="dd7de-200">Notez que chaque migration est maintenant appliquée à l’intérieur d’une transaction.</span><span class="sxs-lookup"><span data-stu-id="dd7de-200">Notice that each migration is now applied inside a transaction.</span></span>

```sql
BEGIN TRANSACTION;
GO

CREATE TABLE [Groups] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Groups] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Members] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    [GroupId] int NULL,
    CONSTRAINT [PK_Members] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Members_Groups_GroupId] FOREIGN KEY ([GroupId]) REFERENCES [Groups] ([Id]) ON DELETE NO ACTION
);
GO

CREATE INDEX [IX_Members_GroupId] ON [Members] ([GroupId]);
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910194835_One', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
GO

BEGIN TRANSACTION;
GO

EXEC sp_rename N'[Groups].[Name]', N'GroupName', N'COLUMN';
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910195234_Two', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
```

<span data-ttu-id="dd7de-201">Comme mentionné dans la section précédente, cette utilisation des transactions peut être désactivée si les transactions doivent être gérées différemment.</span><span class="sxs-lookup"><span data-stu-id="dd7de-201">As mentioned in the previous section, this use of transactions can be disabled if transactions need to be handled differently.</span></span>

### <a name="see-pending-migrations"></a><span data-ttu-id="dd7de-202">Voir migrations en attente</span><span class="sxs-lookup"><span data-stu-id="dd7de-202">See pending migrations</span></span>

<span data-ttu-id="dd7de-203">Cette fonctionnalité a été ajoutée par la communauté [@Psypher9](https://github.com/Psypher9) .</span><span class="sxs-lookup"><span data-stu-id="dd7de-203">This feature was contributed from the community by [@Psypher9](https://github.com/Psypher9).</span></span> <span data-ttu-id="dd7de-204">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="dd7de-204">Many thanks for the contribution!</span></span>

<span data-ttu-id="dd7de-205">La `dotnet ef migrations list` commande affiche maintenant les migrations qui n’ont pas encore été appliquées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-205">The `dotnet ef migrations list` command now shows which migrations have not yet been applied to the database.</span></span> <span data-ttu-id="dd7de-206">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-206">For example:</span></span>

```
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

<span data-ttu-id="dd7de-207">En outre, il existe désormais une `Get-Migration` commande pour la console du gestionnaire de package avec la même fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="dd7de-207">In addition, there is now a `Get-Migration` command for the Package Manager Console with the same functionality.</span></span>

### <a name="modelbuilder-api-for-value-comparers"></a><span data-ttu-id="dd7de-208">API ModelBuilder pour les comparateurs de valeurs</span><span class="sxs-lookup"><span data-stu-id="dd7de-208">ModelBuilder API for value comparers</span></span>

<span data-ttu-id="dd7de-209">Les propriétés de EF Core pour les types mutables personnalisés [requièrent un comparateur de valeur](xref:core/modeling/value-comparers) pour que les modifications de propriété soient détectées correctement.</span><span class="sxs-lookup"><span data-stu-id="dd7de-209">EF Core properties for custom mutable types [require a value comparer](xref:core/modeling/value-comparers) for property changes to be detected correctly.</span></span> <span data-ttu-id="dd7de-210">Cela peut maintenant être spécifié dans le cadre de la configuration de la conversion de valeur pour le type.</span><span class="sxs-lookup"><span data-stu-id="dd7de-210">This can now be specified as part of configuring the value conversion for the type.</span></span> <span data-ttu-id="dd7de-211">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-211">For example:</span></span>

```c#
modelBuilder
    .Entity<EntityType>()
    .Property(e => e.MyProperty)
    .HasConversion(
        v => JsonSerializer.Serialize(v, null),
        v => JsonSerializer.Deserialize<List<int>>(v, null),
        new ValueComparer<List<int>>(
            (c1, c2) => c1.SequenceEqual(c2),
            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
            c => c.ToList()));
```

### <a name="entityentry-trygetvalue-methods"></a><span data-ttu-id="dd7de-212">EntityEntry, méthodes TryGetValue</span><span class="sxs-lookup"><span data-stu-id="dd7de-212">EntityEntry TryGetValue methods</span></span>

<span data-ttu-id="dd7de-213">Cette fonctionnalité a été ajoutée par la communauté [@m4ss1m0g](https://github.com/m4ss1m0g) .</span><span class="sxs-lookup"><span data-stu-id="dd7de-213">This feature was contributed from the community by [@m4ss1m0g](https://github.com/m4ss1m0g).</span></span> <span data-ttu-id="dd7de-214">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="dd7de-214">Many thanks for the contribution!</span></span>

<span data-ttu-id="dd7de-215">Une `TryGetValue` méthode a été ajoutée à `EntityEntry.CurrentValues` et `EntityEntry.OriginalValues` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-215">A `TryGetValue` method has been added to `EntityEntry.CurrentValues` and `EntityEntry.OriginalValues`.</span></span> <span data-ttu-id="dd7de-216">Cela permet de demander la valeur d’une propriété sans vérifier d’abord si la propriété est mappée dans le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="dd7de-216">This allows the value of a property to be requested without first checking if the property is mapped in the EF model.</span></span> <span data-ttu-id="dd7de-217">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-217">For example:</span></span>

```c#
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a><span data-ttu-id="dd7de-218">Taille de lot maximale par défaut pour SQL Server</span><span class="sxs-lookup"><span data-stu-id="dd7de-218">Default max batch size for SQL Server</span></span>

<span data-ttu-id="dd7de-219">À compter de EF Core 5,0, la taille de lot maximale par défaut pour SaveChanges sur SQL Server est désormais 42.</span><span class="sxs-lookup"><span data-stu-id="dd7de-219">Starting with EF Core 5.0, the default maximum batch size for SaveChanges on SQL Server is now 42.</span></span> <span data-ttu-id="dd7de-220">Comme c’est bien connu, il s’agit également de la réponse à la question ultime de vie, de l’univers et de tout.</span><span class="sxs-lookup"><span data-stu-id="dd7de-220">As is well known, this is also the answer to the Ultimate Question of Life, the Universe, and Everything.</span></span> <span data-ttu-id="dd7de-221">Toutefois, il s’agit probablement d’une coïncidence, puisque la valeur a été obtenue par l' [analyse des performances de traitement par lot](https://github.com/dotnet/efcore/issues/9270).</span><span class="sxs-lookup"><span data-stu-id="dd7de-221">However, this is probably a coincidence, since the value was obtained through [analysis of batching performance](https://github.com/dotnet/efcore/issues/9270).</span></span> <span data-ttu-id="dd7de-222">Nous ne pensons pas que nous avons découvert une forme de la question finale, bien qu’il semble quelque peu plausible que la terre a été créée pour comprendre pourquoi SQL Server fonctionne comme elle.</span><span class="sxs-lookup"><span data-stu-id="dd7de-222">We do not believe that we have discovered a form of the Ultimate Question, although it does seem somewhat plausible that the Earth was created to understand why SQL Server works the way it does.</span></span>

### <a name="default-environment-to-development"></a><span data-ttu-id="dd7de-223">Développement de l’environnement par défaut</span><span class="sxs-lookup"><span data-stu-id="dd7de-223">Default environment to Development</span></span>

<span data-ttu-id="dd7de-224">Les outils en ligne de commande EF Core configurent désormais automatiquement les `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` variables d’environnement et en « développement ».</span><span class="sxs-lookup"><span data-stu-id="dd7de-224">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="dd7de-225">Cela permet d’utiliser l’hôte générique en fonction de l’expérience de ASP.NET Core pendant le développement.</span><span class="sxs-lookup"><span data-stu-id="dd7de-225">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span> <span data-ttu-id="dd7de-226">Consultez [#19903](https://github.com/dotnet/efcore/issues/19903).</span><span class="sxs-lookup"><span data-stu-id="dd7de-226">See [#19903](https://github.com/dotnet/efcore/issues/19903).</span></span>

### <a name="better-migrations-column-ordering"></a><span data-ttu-id="dd7de-227">Meilleur classement des colonnes de migrations</span><span class="sxs-lookup"><span data-stu-id="dd7de-227">Better migrations column ordering</span></span>

<span data-ttu-id="dd7de-228">Les colonnes pour les classes de base non mappées sont désormais ordonnées après d’autres colonnes pour les types d’entités mappés.</span><span class="sxs-lookup"><span data-stu-id="dd7de-228">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="dd7de-229">Notez que cela affecte uniquement les tables nouvellement créées.</span><span class="sxs-lookup"><span data-stu-id="dd7de-229">Note this only impacts newly created tables.</span></span> <span data-ttu-id="dd7de-230">L’ordre des colonnes pour les tables existantes reste inchangé.</span><span class="sxs-lookup"><span data-stu-id="dd7de-230">The column order for existing tables remains unchanged.</span></span> <span data-ttu-id="dd7de-231">Consultez [#11314](https://github.com/dotnet/efcore/issues/11314).</span><span class="sxs-lookup"><span data-stu-id="dd7de-231">See [#11314](https://github.com/dotnet/efcore/issues/11314).</span></span>

### <a name="query-improvements"></a><span data-ttu-id="dd7de-232">Améliorations des requêtes</span><span class="sxs-lookup"><span data-stu-id="dd7de-232">Query improvements</span></span>

<span data-ttu-id="dd7de-233">EF Core 5,0 RC1 contient des améliorations supplémentaires de la traduction des requêtes :</span><span class="sxs-lookup"><span data-stu-id="dd7de-233">EF Core 5.0 RC1 contains some additional query translation improvements:</span></span>

* <span data-ttu-id="dd7de-234">Traduction de `is` sur Cosmos--voir [#16391](https://github.com/dotnet/efcore/issues/16391)</span><span class="sxs-lookup"><span data-stu-id="dd7de-234">Translation of `is` on Cosmos--see [#16391](https://github.com/dotnet/efcore/issues/16391)</span></span>
* <span data-ttu-id="dd7de-235">Les fonctions mappées par l’utilisateur peuvent désormais être annotées pour contrôler la propagation de la valeur null, voir [#19609](https://github.com/dotnet/efcore/issues/19609)</span><span class="sxs-lookup"><span data-stu-id="dd7de-235">User-mapped functions can now be annotated to control null propagation--see [#19609](https://github.com/dotnet/efcore/issues/19609)</span></span>
* <span data-ttu-id="dd7de-236">Prise en charge de la traduction de GroupBy avec des agrégats conditionnels--voir [#11711](https://github.com/dotnet/efcore/issues/11711)</span><span class="sxs-lookup"><span data-stu-id="dd7de-236">Support for translation of GroupBy with conditional aggregates--see [#11711](https://github.com/dotnet/efcore/issues/11711)</span></span>
* <span data-ttu-id="dd7de-237">Traduction d’un opérateur distinct sur un élément Group avant Aggregate--voir [#17376](https://github.com/dotnet/efcore/issues/17376)</span><span class="sxs-lookup"><span data-stu-id="dd7de-237">Translation of Distinct operator over group element before aggregate--see [#17376](https://github.com/dotnet/efcore/issues/17376)</span></span>

### <a name="model-building-for-fields"></a><span data-ttu-id="dd7de-238">Génération de modèles pour les champs</span><span class="sxs-lookup"><span data-stu-id="dd7de-238">Model building for fields</span></span>

<span data-ttu-id="dd7de-239">Enfin, pour RC1, EF Core autorise désormais l’utilisation des méthodes lambda dans ModelBuilder pour les champs, ainsi que pour les propriétés.</span><span class="sxs-lookup"><span data-stu-id="dd7de-239">Finally for RC1, EF Core now allows use of the lambda methods in the ModelBuilder for fields as well as properties.</span></span> <span data-ttu-id="dd7de-240">Par exemple, si vous êtes réticent aux propriétés pour une raison quelconque et décidez d’utiliser des champs publics, ces champs peuvent maintenant être mappés à l’aide des générateurs lambda :</span><span class="sxs-lookup"><span data-stu-id="dd7de-240">For example, if you are averse to properties for some reason and decide to use public fields, then these fields can now be mapped using the lambda builders:</span></span>

```c#
public class Post
{
    public int Id;
    public string Name;
    public string Category;
    public int BlogId;
    public Blog Blog;
}

public class Blog
{
    public int Id;
    public string Name;
    public ICollection<Post> Posts;
}
```

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
    });

    modelBuilder.Entity<Post>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
        b.Property(e => e.Category);
        b.Property(e => e.BlogId);
        b.HasOne(e => e.Blog).WithMany(e => e.Posts);
    });
}
```

<span data-ttu-id="dd7de-241">Bien que cela soit maintenant possible, nous ne vous recommandons pas de le faire.</span><span class="sxs-lookup"><span data-stu-id="dd7de-241">While this is now possible, we are certainly not recommending that you do this.</span></span> <span data-ttu-id="dd7de-242">Notez également que cela n’ajoute pas de fonctionnalités de mappage de champs supplémentaires à EF Core, mais autorise uniquement l’utilisation des méthodes lambda au lieu d’exiger toujours les méthodes de chaîne.</span><span class="sxs-lookup"><span data-stu-id="dd7de-242">Also, note that this does not add any additional field mapping capabilities to EF Core, it only allows the lambda methods to be used instead of always requiring the string methods.</span></span> <span data-ttu-id="dd7de-243">Cela est rarement utile dans la mesure où les champs sont rarement publics.</span><span class="sxs-lookup"><span data-stu-id="dd7de-243">This is seldom useful since fields are rarely public.</span></span>

## <a name="preview-8"></a><span data-ttu-id="dd7de-244">Version préliminaire 8</span><span class="sxs-lookup"><span data-stu-id="dd7de-244">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="dd7de-245">Mappage table par type (TPT)</span><span class="sxs-lookup"><span data-stu-id="dd7de-245">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="dd7de-246">Par défaut, EF Core mappe une hiérarchie d’héritage de types .NET à une seule table de base de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-246">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="dd7de-247">C’est ce que l’on appelle le mappage TPH (table par hiérarchie).</span><span class="sxs-lookup"><span data-stu-id="dd7de-247">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="dd7de-248">EF Core 5,0 permet également de mapper chaque type .NET dans une hiérarchie d’héritage à une autre table de base de données. connu sous le nom de mappage table par type (TPT).</span><span class="sxs-lookup"><span data-stu-id="dd7de-248">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="dd7de-249">Par exemple, considérez ce modèle avec une hiérarchie mappée :</span><span class="sxs-lookup"><span data-stu-id="dd7de-249">For example, consider this model with a mapped hierarchy:</span></span>

```c#
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EducationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="dd7de-250">Par défaut, EF Core mappe ce à une table unique :</span><span class="sxs-lookup"><span data-stu-id="dd7de-250">By default, EF Core will map this to a single table:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

<span data-ttu-id="dd7de-251">Toutefois, le mappage de chaque type d’entité à une autre table génère à la place une table par type :</span><span class="sxs-lookup"><span data-stu-id="dd7de-251">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

<span data-ttu-id="dd7de-252">Notez que la création de contraintes de clé étrangère indiquées ci-dessus a été ajoutée après la création de branche du code pour Preview 8.</span><span class="sxs-lookup"><span data-stu-id="dd7de-252">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="dd7de-253">Les types d’entités peuvent être mappés à des tables différentes à l’aide des attributs de mappage :</span><span class="sxs-lookup"><span data-stu-id="dd7de-253">Entity types can be mapped to different tables using mapping attributes:</span></span>

```c#
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="dd7de-254">Ou à l’aide de la `ModelBuilder` Configuration :</span><span class="sxs-lookup"><span data-stu-id="dd7de-254">Or using `ModelBuilder` configuration:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="dd7de-255">La documentation est suivie d’un problème [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span><span class="sxs-lookup"><span data-stu-id="dd7de-255">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="dd7de-256">Migrations : reconstruire les tables SQLite</span><span class="sxs-lookup"><span data-stu-id="dd7de-256">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="dd7de-257">Par rapport à d’autres bases de données, SQLite est relativement limité dans ses fonctionnalités de manipulation de schéma.</span><span class="sxs-lookup"><span data-stu-id="dd7de-257">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="dd7de-258">Par exemple, la suppression d’une colonne d’une table existante nécessite la suppression et la recréation de la table entière.</span><span class="sxs-lookup"><span data-stu-id="dd7de-258">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="dd7de-259">EF Core migrations 5,0 prend désormais en charge la reconstruction automatique de la table pour les modifications de schéma qui l’exigent.</span><span class="sxs-lookup"><span data-stu-id="dd7de-259">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="dd7de-260">Par exemple, imaginez que nous disposons d’une `Unicorns` table créée pour un `Unicorn` type d’entité :</span><span class="sxs-lookup"><span data-stu-id="dd7de-260">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

```c#
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

<span data-ttu-id="dd7de-261">Nous avons ensuite appris que le stockage de l’âge d’une licorne est considéré comme très impropre. nous allons donc supprimer cette propriété, ajouter une nouvelle migration et mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-261">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="dd7de-262">Cette mise à jour échoue lors de l’utilisation de EF Core 3,1, car la colonne ne peut pas être supprimée.</span><span class="sxs-lookup"><span data-stu-id="dd7de-262">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="dd7de-263">Dans EF Core 5,0, les migrations recréent à la place la table :</span><span class="sxs-lookup"><span data-stu-id="dd7de-263">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

<span data-ttu-id="dd7de-264">Notez que :</span><span class="sxs-lookup"><span data-stu-id="dd7de-264">Notice that:</span></span>
* <span data-ttu-id="dd7de-265">Une table temporaire est créée avec le schéma souhaité pour la nouvelle table</span><span class="sxs-lookup"><span data-stu-id="dd7de-265">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="dd7de-266">Les données sont copiées à partir de la table actuelle dans la table temporaire</span><span class="sxs-lookup"><span data-stu-id="dd7de-266">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="dd7de-267">Mise hors tension de la clé étrangère</span><span class="sxs-lookup"><span data-stu-id="dd7de-267">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="dd7de-268">La table actuelle est supprimée</span><span class="sxs-lookup"><span data-stu-id="dd7de-268">The current table is dropped</span></span>
* <span data-ttu-id="dd7de-269">La table temporaire est renommée en une nouvelle table</span><span class="sxs-lookup"><span data-stu-id="dd7de-269">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="dd7de-270">La documentation est suivie d’un problème [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span><span class="sxs-lookup"><span data-stu-id="dd7de-270">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="dd7de-271">Fonctions table</span><span class="sxs-lookup"><span data-stu-id="dd7de-271">Table-valued functions</span></span>

<span data-ttu-id="dd7de-272">Cette fonctionnalité a été ajoutée par la communauté [@pmiddleton](https://github.com/pmiddleton) .</span><span class="sxs-lookup"><span data-stu-id="dd7de-272">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="dd7de-273">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="dd7de-273">Many thanks for the contribution!</span></span>

<span data-ttu-id="dd7de-274">EF Core 5,0 offre une prise en charge de première classe pour le mappage des méthodes .NET aux fonctions table (TVF).</span><span class="sxs-lookup"><span data-stu-id="dd7de-274">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="dd7de-275">Ces fonctions peuvent ensuite être utilisées dans les requêtes LINQ où la composition supplémentaire sur les résultats de la fonction sera également traduite en SQL.</span><span class="sxs-lookup"><span data-stu-id="dd7de-275">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="dd7de-276">Par exemple, considérez cette TVF définie dans une base de données SQL Server :</span><span class="sxs-lookup"><span data-stu-id="dd7de-276">For example, consider this TVF defined in a SQL Server database:</span></span>

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

<span data-ttu-id="dd7de-277">Le modèle de EF Core nécessite deux types d’entités pour utiliser cette TVF :</span><span class="sxs-lookup"><span data-stu-id="dd7de-277">The EF Core model requires two entity types to use this TVF:</span></span>
* <span data-ttu-id="dd7de-278">`Employee`Type qui est mappé à la table Employees de manière normale</span><span class="sxs-lookup"><span data-stu-id="dd7de-278">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="dd7de-279">`Report`Type qui correspond à la forme retournée par la fonction TVF</span><span class="sxs-lookup"><span data-stu-id="dd7de-279">A `Report` type that matches the shape returned by the TVF</span></span>

```c#
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```c#
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

<span data-ttu-id="dd7de-280">Ces types doivent être inclus dans le modèle de EF Core :</span><span class="sxs-lookup"><span data-stu-id="dd7de-280">These types must be included in the EF Core model:</span></span>

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="dd7de-281">Notez que n' `Report` a pas de clé primaire et doit donc être configuré en tant que tel.</span><span class="sxs-lookup"><span data-stu-id="dd7de-281">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="dd7de-282">Enfin, une méthode .NET doit être mappée à la TVF dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-282">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="dd7de-283">Cette méthode peut être définie sur DbContext à l’aide de la nouvelle `FromExpression` méthode :</span><span class="sxs-lookup"><span data-stu-id="dd7de-283">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="dd7de-284">Cette méthode utilise un paramètre et un type de retour qui correspondent à la fonction TVF définie ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="dd7de-284">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="dd7de-285">La méthode est ensuite ajoutée au modèle EF Core dans OnModelCreating :</span><span class="sxs-lookup"><span data-stu-id="dd7de-285">The method is then added to the EF Core model in OnModelCreating:</span></span>

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="dd7de-286">(L’utilisation d’une expression lambda est un moyen simple de passer le `MethodInfo` à EF Core.</span><span class="sxs-lookup"><span data-stu-id="dd7de-286">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="dd7de-287">Les arguments passés à la méthode sont ignorés.)</span><span class="sxs-lookup"><span data-stu-id="dd7de-287">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="dd7de-288">Nous pouvons maintenant écrire des requêtes qui appellent `GetReports` et composent les résultats.</span><span class="sxs-lookup"><span data-stu-id="dd7de-288">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="dd7de-289">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-289">For example:</span></span>

```c#
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

<span data-ttu-id="dd7de-290">Sur SQL Server, cela se traduit par :</span><span class="sxs-lookup"><span data-stu-id="dd7de-290">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="dd7de-291">Notez que le SQL est enraciné dans la `Employees` table, appelle `GetReports` , puis ajoute une clause WHERE supplémentaire sur les résultats de la fonction.</span><span class="sxs-lookup"><span data-stu-id="dd7de-291">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="dd7de-292">Mappage de requête/mise à jour flexible</span><span class="sxs-lookup"><span data-stu-id="dd7de-292">Flexible query/update mapping</span></span>

<span data-ttu-id="dd7de-293">EF Core 5,0 permet de mapper le même type d’entité à des objets de base de données différents.</span><span class="sxs-lookup"><span data-stu-id="dd7de-293">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="dd7de-294">Ces objets peuvent être des tables, des vues ou des fonctions.</span><span class="sxs-lookup"><span data-stu-id="dd7de-294">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="dd7de-295">Par exemple, un type d’entité peut être mappé à une vue de base de données et à une table de base de données :</span><span class="sxs-lookup"><span data-stu-id="dd7de-295">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="dd7de-296">Par défaut, EF Core effectue une requête à partir de la vue et envoie des mises à jour à la table.</span><span class="sxs-lookup"><span data-stu-id="dd7de-296">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="dd7de-297">Par exemple, l’exécution du code suivant :</span><span class="sxs-lookup"><span data-stu-id="dd7de-297">For example, executing the following code:</span></span>

```c#
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="dd7de-298">Génère une requête sur la vue, puis une mise à jour de la table :</span><span class="sxs-lookup"><span data-stu-id="dd7de-298">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="dd7de-299">Configuration de la requête Split au niveau du contexte</span><span class="sxs-lookup"><span data-stu-id="dd7de-299">Context-wide split-query configuration</span></span>

<span data-ttu-id="dd7de-300">Les requêtes de fractionnement (voir ci-dessous) peuvent désormais être configurées par défaut pour toute requête exécutée par DbContext.</span><span class="sxs-lookup"><span data-stu-id="dd7de-300">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="dd7de-301">Cette configuration n’est disponible que pour les fournisseurs relationnels et doit donc être spécifiée dans le cadre de la `UseProvider` Configuration.</span><span class="sxs-lookup"><span data-stu-id="dd7de-301">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="dd7de-302">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-302">For example:</span></span>

```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="dd7de-303">La documentation est suivie d’un problème [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="dd7de-303">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="dd7de-304">Mappage PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="dd7de-304">PhysicalAddress mapping</span></span>

<span data-ttu-id="dd7de-305">Cette fonctionnalité a été ajoutée par la communauté [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="dd7de-305">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="dd7de-306">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="dd7de-306">Many thanks for the contribution!</span></span>

<span data-ttu-id="dd7de-307">La classe .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) standard est désormais automatiquement mappée à une colonne de type chaîne pour les bases de données qui n’ont pas encore de prise en charge native.</span><span class="sxs-lookup"><span data-stu-id="dd7de-307">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="dd7de-308">Pour plus d’informations, consultez les exemples `IPAddress` ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="dd7de-308">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="dd7de-309">Version préliminaire 7</span><span class="sxs-lookup"><span data-stu-id="dd7de-309">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="dd7de-310">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="dd7de-310">DbContextFactory</span></span>

<span data-ttu-id="dd7de-311">EF Core 5,0 introduit `AddDbContextFactory` et `AddPooledDbContextFactory` pour inscrire une fabrique pour la création d’instances de DbContext dans le conteneur d’injection de dépendances (D.I.) de l’application.</span><span class="sxs-lookup"><span data-stu-id="dd7de-311">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="dd7de-312">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-312">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="dd7de-313">Les services d’application tels que les contrôleurs de ASP.NET Core peuvent ensuite dépendre `IDbContextFactory<TContext>` du constructeur de service.</span><span class="sxs-lookup"><span data-stu-id="dd7de-313">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="dd7de-314">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-314">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="dd7de-315">Les instances de DbContext peuvent ensuite être créées et utilisées en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="dd7de-315">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="dd7de-316">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-316">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="dd7de-317">Notez que les instances de DbContext créées de cette façon _ne sont pas_ gérées par le fournisseur de services de l’application et doivent donc être supprimées par l’application.</span><span class="sxs-lookup"><span data-stu-id="dd7de-317">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="dd7de-318">Ce découplage est très utile pour les applications éblouissantes, où l’utilisation de `IDbContextFactory` est recommandée, mais peut également être utile dans d’autres scénarios.</span><span class="sxs-lookup"><span data-stu-id="dd7de-318">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="dd7de-319">Les instances de DbContext peuvent être regroupées en appelant `AddPooledDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-319">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="dd7de-320">Ce regroupement fonctionne de la même façon que pour `AddDbContextPool` et présente également les mêmes limitations.</span><span class="sxs-lookup"><span data-stu-id="dd7de-320">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="dd7de-321">La documentation est suivie d’un problème [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span><span class="sxs-lookup"><span data-stu-id="dd7de-321">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="dd7de-322">Réinitialiser l’état de DbContext</span><span class="sxs-lookup"><span data-stu-id="dd7de-322">Reset DbContext state</span></span>

<span data-ttu-id="dd7de-323">EF Core 5,0 introduit `ChangeTracker.Clear()` , qui efface le DbContext de toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="dd7de-323">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="dd7de-324">Cela ne doit généralement pas être nécessaire lors de l’utilisation de la méthode recommandée pour créer une instance de contexte de courte durée pour chaque unité de travail.</span><span class="sxs-lookup"><span data-stu-id="dd7de-324">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="dd7de-325">Toutefois, s’il est nécessaire de réinitialiser l’état d’une instance de DbContext, l’utilisation de la nouvelle `Clear()` méthode est plus performante et fiable que le détachement en masse de toutes les entités.</span><span class="sxs-lookup"><span data-stu-id="dd7de-325">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="dd7de-326">La documentation est suivie d’un problème [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span><span class="sxs-lookup"><span data-stu-id="dd7de-326">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="dd7de-327">Nouveau modèle pour les valeurs par défaut générées par le magasin</span><span class="sxs-lookup"><span data-stu-id="dd7de-327">New pattern for store-generated defaults</span></span>

<span data-ttu-id="dd7de-328">EF Core permet de définir une valeur explicite pour une colonne qui peut également avoir une contrainte de valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="dd7de-328">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="dd7de-329">EF Core utilise la valeur CLR par défaut de type de propriété comme sentinelle pour ce. Si la valeur n’est pas la valeur CLR par défaut, elle est insérée, sinon la valeur par défaut de la base de données est utilisée.</span><span class="sxs-lookup"><span data-stu-id="dd7de-329">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="dd7de-330">Cela crée des problèmes pour les types où la valeur CLR par défaut n’est pas une bonne sentinelle, notamment les `bool` Propriétés.</span><span class="sxs-lookup"><span data-stu-id="dd7de-330">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="dd7de-331">EF Core 5,0 autorise désormais la valeur null dans le champ de stockage pour les cas comme celui-ci.</span><span class="sxs-lookup"><span data-stu-id="dd7de-331">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="dd7de-332">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-332">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="dd7de-333">Notez que le champ de stockage accepte la valeur null, mais pas la propriété exposée publiquement.</span><span class="sxs-lookup"><span data-stu-id="dd7de-333">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="dd7de-334">Cela permet à la valeur de sentinelle de ne pas avoir `null` d’impact sur la surface publique du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="dd7de-334">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="dd7de-335">Dans ce cas, si le `IsValid` n’est jamais défini, la valeur par défaut de la base de données est utilisée, car le champ de stockage conserve la valeur null.</span><span class="sxs-lookup"><span data-stu-id="dd7de-335">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="dd7de-336">Si `true` ou `false` sont définis, cette valeur est enregistrée explicitement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-336">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="dd7de-337">La documentation est suivie d’un problème [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span><span class="sxs-lookup"><span data-stu-id="dd7de-337">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="dd7de-338">Clés de partition Cosmos</span><span class="sxs-lookup"><span data-stu-id="dd7de-338">Cosmos partition keys</span></span>

<span data-ttu-id="dd7de-339">EF Core permet d’inclure la clé de partition Cosmos dans le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="dd7de-339">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="dd7de-340">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-340">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="dd7de-341">À partir de Preview 7, la clé de partition est incluse dans le PK du type d’entité et est utilisée pour améliorer les performances dans certaines requêtes.</span><span class="sxs-lookup"><span data-stu-id="dd7de-341">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="dd7de-342">La documentation est suivie d’un problème [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="dd7de-342">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="dd7de-343">Configuration de Cosmos</span><span class="sxs-lookup"><span data-stu-id="dd7de-343">Cosmos configuration</span></span>

<span data-ttu-id="dd7de-344">EF Core 5,0 améliore la configuration des connexions Cosmos et Cosmos.</span><span class="sxs-lookup"><span data-stu-id="dd7de-344">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="dd7de-345">Auparavant, EF Core nécessitait la spécification du point de terminaison et de la clé explicitement lors de la connexion à une base de données Cosmos.</span><span class="sxs-lookup"><span data-stu-id="dd7de-345">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="dd7de-346">EF Core 5,0 autorise l’utilisation d’une chaîne de connexion à la place.</span><span class="sxs-lookup"><span data-stu-id="dd7de-346">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="dd7de-347">En outre, EF Core 5,0 permet de définir explicitement l’instance WebProxy.</span><span class="sxs-lookup"><span data-stu-id="dd7de-347">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="dd7de-348">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-348">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="dd7de-349">De nombreuses autres valeurs de délai d’attente, limites, etc. peuvent également être configurées.</span><span class="sxs-lookup"><span data-stu-id="dd7de-349">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="dd7de-350">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-350">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="dd7de-351">Enfin, le mode de connexion par défaut est désormais `ConnectionMode.Gateway` , ce qui est généralement plus compatible.</span><span class="sxs-lookup"><span data-stu-id="dd7de-351">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="dd7de-352">La documentation est suivie d’un problème [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="dd7de-352">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="dd7de-353">Génération de modèles automatique-DbContext Now singularise</span><span class="sxs-lookup"><span data-stu-id="dd7de-353">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="dd7de-354">Auparavant, lors de la génération de modèles automatique d’une base de données existante, EF Core créera des noms de types d’entité qui correspondent aux noms de table dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-354">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="dd7de-355">Par exemple, les tables `People` et ont `Addresses` abouti à des types d’entités nommés `People` et `Addresses` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-355">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="dd7de-356">Dans les versions précédentes, ce comportement était configurable par l’inscription d’un service de pluralisation.</span><span class="sxs-lookup"><span data-stu-id="dd7de-356">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="dd7de-357">Désormais, dans EF Core 5,0, le package [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) est utilisé comme un service de pluralisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="dd7de-357">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="dd7de-358">Cela signifie que `People` les tables et `Addresses` sont maintenant rétroconçues dans les types d’entités nommés `Person` et `Address` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-358">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="dd7de-359">Points</span><span class="sxs-lookup"><span data-stu-id="dd7de-359">Savepoints</span></span>

<span data-ttu-id="dd7de-360">EF Core prend désormais en charge les [points](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) de contrôle pour un plus grand contrôle sur les transactions qui exécutent plusieurs opérations.</span><span class="sxs-lookup"><span data-stu-id="dd7de-360">EF Core now supports [savepoints](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="dd7de-361">Les points de sauvegarde peuvent être créés, libérés et restaurés manuellement.</span><span class="sxs-lookup"><span data-stu-id="dd7de-361">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="dd7de-362">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-362">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="dd7de-363">En outre, les EF Core sont désormais restaurés jusqu’au dernier point de sauvegarde en cas d’échec de l’exécution `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-363">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="dd7de-364">Cela permet de faire une nouvelle tentative d’SaveChanges sans retenter l’intégralité de la transaction.</span><span class="sxs-lookup"><span data-stu-id="dd7de-364">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="dd7de-365">La documentation est suivie d’un problème [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span><span class="sxs-lookup"><span data-stu-id="dd7de-365">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="dd7de-366">Préversion 6</span><span class="sxs-lookup"><span data-stu-id="dd7de-366">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="dd7de-367">Fractionner les requêtes pour les regroupements associés</span><span class="sxs-lookup"><span data-stu-id="dd7de-367">Split queries for related collections</span></span>

<span data-ttu-id="dd7de-368">À compter de EF Core 3,0, EF Core génère toujours une requête SQL unique pour chaque requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="dd7de-368">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="dd7de-369">Cela garantit la cohérence des données retournées dans les contraintes du mode de transaction en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="dd7de-369">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="dd7de-370">Toutefois, cela peut devenir très lent lorsque la requête utilise `Include` ou une projection pour rétablir plusieurs collections associées.</span><span class="sxs-lookup"><span data-stu-id="dd7de-370">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="dd7de-371">EF Core 5,0 autorise désormais une seule requête LINQ, y compris les collections associées, à fractionner en plusieurs requêtes SQL.</span><span class="sxs-lookup"><span data-stu-id="dd7de-371">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="dd7de-372">Cela peut améliorer considérablement les performances, mais peut entraîner une incohérence dans les résultats retournés si les données changent entre les deux requêtes.</span><span class="sxs-lookup"><span data-stu-id="dd7de-372">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="dd7de-373">Les transactions sérialisables ou d’instantané peuvent être utilisées pour atténuer ce problème et obtenir une cohérence avec les requêtes fractionnées, mais cela peut réduire les coûts de performances et la différence comportementale.</span><span class="sxs-lookup"><span data-stu-id="dd7de-373">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="dd7de-374">Fractionner des requêtes avec include</span><span class="sxs-lookup"><span data-stu-id="dd7de-374">Split queries with Include</span></span>

<span data-ttu-id="dd7de-375">Par exemple, considérez une requête qui extrait deux niveaux de collections associées à l’aide de `Include` :</span><span class="sxs-lookup"><span data-stu-id="dd7de-375">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="dd7de-376">Par défaut, EF Core génère le code SQL suivant lorsque vous utilisez le fournisseur SQLite :</span><span class="sxs-lookup"><span data-stu-id="dd7de-376">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="dd7de-377">La nouvelle `AsSplitQuery` API peut être utilisée pour modifier ce comportement.</span><span class="sxs-lookup"><span data-stu-id="dd7de-377">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="dd7de-378">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-378">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="dd7de-379">AsSplitQuery est disponible pour tous les fournisseurs de bases de données relationnelles et peut être utilisée n’importe où dans la requête, tout comme AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="dd7de-379">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="dd7de-380">EF Core générera maintenant les trois requêtes SQL suivantes :</span><span class="sxs-lookup"><span data-stu-id="dd7de-380">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="dd7de-381">Toutes les opérations sur la racine de la requête sont prises en charge.</span><span class="sxs-lookup"><span data-stu-id="dd7de-381">All operations on the query root are supported.</span></span> <span data-ttu-id="dd7de-382">Cela comprend les opérations OrderBy/Skip/Take, Join, FirstOrDefault et des résultats uniques de sélection.</span><span class="sxs-lookup"><span data-stu-id="dd7de-382">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="dd7de-383">Notez que les inclusions filtrées avec OrderBy/Skip/Take ne sont pas prises en charge dans Preview 6, mais sont disponibles dans les builds quotidiennes et seront incluses dans Preview 7.</span><span class="sxs-lookup"><span data-stu-id="dd7de-383">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="dd7de-384">Fractionner des requêtes avec des projections de collection</span><span class="sxs-lookup"><span data-stu-id="dd7de-384">Split queries with collection projections</span></span>

<span data-ttu-id="dd7de-385">`AsSplitQuery` peut également être utilisé lorsque les collections sont chargées dans des projections.</span><span class="sxs-lookup"><span data-stu-id="dd7de-385">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="dd7de-386">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-386">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="dd7de-387">Cette requête LINQ génère les deux requêtes SQL suivantes lors de l’utilisation du fournisseur SQLite :</span><span class="sxs-lookup"><span data-stu-id="dd7de-387">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="dd7de-388">Notez que seule la matérialisation de la collection est prise en charge.</span><span class="sxs-lookup"><span data-stu-id="dd7de-388">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="dd7de-389">Les compositions ultérieures `e.Albums` dans le cas ci-dessus n’aboutissent pas à une requête Split.</span><span class="sxs-lookup"><span data-stu-id="dd7de-389">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="dd7de-390">Les améliorations apportées à cette zone sont suivies par [#21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="dd7de-390">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="dd7de-391">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="dd7de-391">IndexAttribute</span></span>

<span data-ttu-id="dd7de-392">Le nouveau IndexAttribute peut être placé sur un type d’entité pour spécifier un index pour une colonne unique.</span><span class="sxs-lookup"><span data-stu-id="dd7de-392">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="dd7de-393">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-393">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="dd7de-394">Par SQL Server, les migrations génèrent alors le code SQL suivant :</span><span class="sxs-lookup"><span data-stu-id="dd7de-394">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="dd7de-395">IndexAttribute peut également être utilisé pour spécifier un index couvrant plusieurs colonnes.</span><span class="sxs-lookup"><span data-stu-id="dd7de-395">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="dd7de-396">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-396">For example:</span></span>

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="dd7de-397">Pour SQL Server, les résultats sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="dd7de-397">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="dd7de-398">La documentation est suivie d’un problème [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="dd7de-398">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="dd7de-399">Amélioration des exceptions de traduction des requêtes</span><span class="sxs-lookup"><span data-stu-id="dd7de-399">Improved query translation exceptions</span></span>

<span data-ttu-id="dd7de-400">Nous continuons d’améliorer les messages d’exception générés en cas d’échec de la traduction de la requête.</span><span class="sxs-lookup"><span data-stu-id="dd7de-400">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="dd7de-401">Par exemple, cette requête utilise la propriété non mappée `IsSigned` :</span><span class="sxs-lookup"><span data-stu-id="dd7de-401">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="dd7de-402">EF Core lèvera l’exception suivante indiquant que la conversion a échoué, car `IsSigned` n’est pas mappé :</span><span class="sxs-lookup"><span data-stu-id="dd7de-402">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="dd7de-403">Exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="dd7de-403">Unhandled exception.</span></span> <span data-ttu-id="dd7de-404">System. InvalidOperationException : expression LINQ’DbSet <Artist> (). Où (a => a. IsSigned) 'n’a pas pu être traduit.</span><span class="sxs-lookup"><span data-stu-id="dd7de-404">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="dd7de-405">Informations supplémentaires : la traduction du membre « IsSigned » sur le type d’entité « Artist » a échoué.</span><span class="sxs-lookup"><span data-stu-id="dd7de-405">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="dd7de-406">Éventuellement, le membre spécifié n’est pas mappé.</span><span class="sxs-lookup"><span data-stu-id="dd7de-406">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="dd7de-407">Réécrivez la requête dans un formulaire qui peut être traduit ou basculez explicitement vers l’évaluation du client en insérant un appel à AsEnumerable (), AsAsyncEnumerable (), ToList () ou ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="dd7de-407">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="dd7de-408">Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2101038 (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="dd7de-408">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="dd7de-409">De même, de meilleurs messages d’exception sont désormais générés lorsque vous tentez de traduire des comparaisons de chaînes avec la sémantique dépendante de la culture.</span><span class="sxs-lookup"><span data-stu-id="dd7de-409">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="dd7de-410">Par exemple, cette requête tente d’utiliser `StringComparison.CurrentCulture` :</span><span class="sxs-lookup"><span data-stu-id="dd7de-410">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="dd7de-411">EF Core lèvera à présent l’exception suivante :</span><span class="sxs-lookup"><span data-stu-id="dd7de-411">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="dd7de-412">Exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="dd7de-412">Unhandled exception.</span></span> <span data-ttu-id="dd7de-413">System. InvalidOperationException : expression LINQ’DbSet <Artist> (). Où (a => a. Name. Equals (valeur : « les licornes », comparisonType : CurrentCulture))» n’a pas pu être traduit.</span><span class="sxs-lookup"><span data-stu-id="dd7de-413">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="dd7de-414">Informations supplémentaires : traduction de « String ». La méthode Equals qui accepte l’argument’StringComparison’n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="dd7de-414">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="dd7de-415">Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2129535 (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="dd7de-415">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="dd7de-416">Réécrivez la requête dans un formulaire qui peut être traduit ou basculez explicitement vers l’évaluation du client en insérant un appel à AsEnumerable (), AsAsyncEnumerable (), ToList () ou ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="dd7de-416">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="dd7de-417">Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2101038 (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="dd7de-417">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="dd7de-418">Spécifier l’ID de la transaction</span><span class="sxs-lookup"><span data-stu-id="dd7de-418">Specify transaction ID</span></span>

<span data-ttu-id="dd7de-419">Cette fonctionnalité a été ajoutée par la communauté [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="dd7de-419">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="dd7de-420">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="dd7de-420">Many thanks for the contribution!</span></span>

<span data-ttu-id="dd7de-421">EF Core expose un ID de transaction pour la corrélation des transactions entre les appels.</span><span class="sxs-lookup"><span data-stu-id="dd7de-421">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="dd7de-422">Cet ID est généralement défini par EF Core lorsqu’une transaction est démarrée.</span><span class="sxs-lookup"><span data-stu-id="dd7de-422">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="dd7de-423">Si l’application démarre plutôt la transaction, cette fonctionnalité permet à l’application de définir explicitement l’ID de transaction afin qu’elle soit correctement corrélée partout où elle est utilisée.</span><span class="sxs-lookup"><span data-stu-id="dd7de-423">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="dd7de-424">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-424">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="dd7de-425">Mappage IPAddress</span><span class="sxs-lookup"><span data-stu-id="dd7de-425">IPAddress mapping</span></span>

<span data-ttu-id="dd7de-426">Cette fonctionnalité a été ajoutée par la communauté [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="dd7de-426">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="dd7de-427">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="dd7de-427">Many thanks for the contribution!</span></span>

<span data-ttu-id="dd7de-428">La classe .NET [IPAddress](/dotnet/api/system.net.ipaddress) standard est désormais automatiquement mappée à une colonne de type chaîne pour les bases de données qui n’ont pas encore de prise en charge native.</span><span class="sxs-lookup"><span data-stu-id="dd7de-428">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="dd7de-429">Par exemple, envisagez de mapper ce type d’entité :</span><span class="sxs-lookup"><span data-stu-id="dd7de-429">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="dd7de-430">Sur SQL Server, les migrations créent le tableau suivant :</span><span class="sxs-lookup"><span data-stu-id="dd7de-430">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="dd7de-431">Les entités peuvent ensuite être ajoutées de manière normale :</span><span class="sxs-lookup"><span data-stu-id="dd7de-431">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="dd7de-432">Et le SQL qui en résulte insère l’adresse IPv4 ou IPv6 normalisée :</span><span class="sxs-lookup"><span data-stu-id="dd7de-432">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="dd7de-433">Exclure OnConfiguring lors de la génération de modèles automatique</span><span class="sxs-lookup"><span data-stu-id="dd7de-433">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="dd7de-434">Lorsqu’un DbContext est généré à partir d’une base de données existante, EF Core par défaut crée une surcharge OnConfiguring avec une chaîne de connexion afin que le contexte soit immédiatement utilisable.</span><span class="sxs-lookup"><span data-stu-id="dd7de-434">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="dd7de-435">Toutefois, cela n’est pas utile si vous avez déjà une classe partielle avec OnConfiguring, ou si vous configurez le contexte d’une autre façon.</span><span class="sxs-lookup"><span data-stu-id="dd7de-435">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="dd7de-436">Pour résoudre ce risque, les commandes de génération de modèles automatique peuvent maintenant être demandées d’omettre la génération de OnConfiguring.</span><span class="sxs-lookup"><span data-stu-id="dd7de-436">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="dd7de-437">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-437">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="dd7de-438">Ou dans la console du gestionnaire de package :</span><span class="sxs-lookup"><span data-stu-id="dd7de-438">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="dd7de-439">Notez que nous vous recommandons [d’utiliser une chaîne de connexion nommée et un stockage sécurisé comme des secrets d’utilisateur](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="dd7de-439">Note that we recommend using [a named connection string and secure storage like User Secrets](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="dd7de-440">Traductions pour FirstOrDefault sur les chaînes</span><span class="sxs-lookup"><span data-stu-id="dd7de-440">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="dd7de-441">Cette fonctionnalité a été ajoutée par la communauté [@dvoreckyaa](https://github.com/dvoreckyaa) .</span><span class="sxs-lookup"><span data-stu-id="dd7de-441">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="dd7de-442">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="dd7de-442">Many thanks for the contribution!</span></span>

<span data-ttu-id="dd7de-443">Les opérateurs FirstOrDefault et similaires pour les caractères dans les chaînes sont maintenant traduits.</span><span class="sxs-lookup"><span data-stu-id="dd7de-443">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="dd7de-444">Par exemple, cette requête LINQ :</span><span class="sxs-lookup"><span data-stu-id="dd7de-444">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="dd7de-445">Sera traduit dans le code SQL suivant lors de l’utilisation de SQL Server :</span><span class="sxs-lookup"><span data-stu-id="dd7de-445">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="dd7de-446">Simplifier les blocs de casse</span><span class="sxs-lookup"><span data-stu-id="dd7de-446">Simplify case blocks</span></span>

<span data-ttu-id="dd7de-447">EF Core génère désormais de meilleures requêtes avec des blocs de cas.</span><span class="sxs-lookup"><span data-stu-id="dd7de-447">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="dd7de-448">Par exemple, cette requête LINQ :</span><span class="sxs-lookup"><span data-stu-id="dd7de-448">For example, this LINQ query:</span></span>

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="dd7de-449">Était sur SQL Server traduite formellement en :</span><span class="sxs-lookup"><span data-stu-id="dd7de-449">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

<span data-ttu-id="dd7de-450">Mais est maintenant traduit par :</span><span class="sxs-lookup"><span data-stu-id="dd7de-450">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="dd7de-451">Préversion 5</span><span class="sxs-lookup"><span data-stu-id="dd7de-451">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="dd7de-452">Classements de base de données</span><span class="sxs-lookup"><span data-stu-id="dd7de-452">Database collations</span></span>

<span data-ttu-id="dd7de-453">Le classement par défaut d’une base de données peut désormais être spécifié dans le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="dd7de-453">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="dd7de-454">Cela passera à des migrations générées pour définir le classement lors de la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-454">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="dd7de-455">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-455">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="dd7de-456">Les migrations génèrent ensuite les éléments suivants pour créer la base de données sur SQL Server :</span><span class="sxs-lookup"><span data-stu-id="dd7de-456">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="dd7de-457">Le classement à utiliser pour des colonnes de base de données spécifiques peut également être spécifié.</span><span class="sxs-lookup"><span data-stu-id="dd7de-457">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="dd7de-458">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-458">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="dd7de-459">Pour ceux qui n’utilisent pas de migrations, les classements sont à présent rétroconçus de la base de données lors de la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="dd7de-459">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="dd7de-460">Enfin, le `EF.Functions.Collate()` autorise les requêtes ad hoc utilisant des classements différents.</span><span class="sxs-lookup"><span data-stu-id="dd7de-460">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="dd7de-461">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-461">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="dd7de-462">La requête suivante est générée pour SQL Server :</span><span class="sxs-lookup"><span data-stu-id="dd7de-462">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="dd7de-463">Notez que les classements ad hoc doivent être utilisés avec précaution, car ils peuvent avoir un impact négatif sur les performances de la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-463">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="dd7de-464">La documentation est suivie d’un problème [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="dd7de-464">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="dd7de-465">Transmettre les arguments dans IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="dd7de-465">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="dd7de-466">Les arguments sont à présent transmis à partir de la ligne de commande dans la `CreateDbContext` méthode de [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span><span class="sxs-lookup"><span data-stu-id="dd7de-466">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span></span> <span data-ttu-id="dd7de-467">Par exemple, pour indiquer qu’il s’agit d’une build dev, un argument personnalisé (par exemple, `dev` ) peut être passé sur la ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="dd7de-467">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="dd7de-468">Cet argument est ensuite transmis à la fabrique, où il peut être utilisé pour contrôler la façon dont le contexte est créé et initialisé.</span><span class="sxs-lookup"><span data-stu-id="dd7de-468">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="dd7de-469">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-469">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="dd7de-470">La documentation est suivie d’un problème [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="dd7de-470">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="dd7de-471">Requêtes de suivi sans suivi de la résolution d’identité</span><span class="sxs-lookup"><span data-stu-id="dd7de-471">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="dd7de-472">Les requêtes de non-suivi peuvent maintenant être configurées pour effectuer la résolution d’identité.</span><span class="sxs-lookup"><span data-stu-id="dd7de-472">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="dd7de-473">Par exemple, la requête suivante crée une nouvelle instance de blog pour chaque publication, même si chaque blog a la même clé primaire.</span><span class="sxs-lookup"><span data-stu-id="dd7de-473">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="dd7de-474">Toutefois, au détriment d’un peu plus lentement et en utilisant toujours plus de mémoire, cette requête peut être modifiée pour garantir qu’une seule instance de blog est créée :</span><span class="sxs-lookup"><span data-stu-id="dd7de-474">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="dd7de-475">Notez que cela n’est utile que pour les requêtes sans suivi, car toutes les requêtes de suivi présentent déjà ce comportement.</span><span class="sxs-lookup"><span data-stu-id="dd7de-475">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="dd7de-476">En outre, la syntaxe de l’API suivante `PerformIdentityResolution` sera modifiée.</span><span class="sxs-lookup"><span data-stu-id="dd7de-476">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="dd7de-477">Consultez [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="dd7de-477">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="dd7de-478">La documentation est suivie d’un problème [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="dd7de-478">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="dd7de-479">Colonnes calculées (persistantes) stockées</span><span class="sxs-lookup"><span data-stu-id="dd7de-479">Stored (persisted) computed columns</span></span>

<span data-ttu-id="dd7de-480">La plupart des bases de données autorisent le stockage des valeurs de colonne calculées après le calcul.</span><span class="sxs-lookup"><span data-stu-id="dd7de-480">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="dd7de-481">Bien que cela prenne de l’espace disque, la colonne calculée n’est calculée qu’une seule fois lors de la mise à jour, et non chaque fois que sa valeur est récupérée.</span><span class="sxs-lookup"><span data-stu-id="dd7de-481">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="dd7de-482">Cela permet également d’indexer la colonne pour certaines bases de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-482">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="dd7de-483">EF Core 5,0 permet de configurer les colonnes calculées comme stockées.</span><span class="sxs-lookup"><span data-stu-id="dd7de-483">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="dd7de-484">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-484">For example:</span></span>

```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="dd7de-485">SQLite colonnes calculées</span><span class="sxs-lookup"><span data-stu-id="dd7de-485">SQLite computed columns</span></span>

<span data-ttu-id="dd7de-486">EF Core prend désormais en charge les colonnes calculées dans les bases de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="dd7de-486">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="dd7de-487">Preview 4</span><span class="sxs-lookup"><span data-stu-id="dd7de-487">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="dd7de-488">Configurer la précision/l’échelle de la base de données dans le modèle</span><span class="sxs-lookup"><span data-stu-id="dd7de-488">Configure database precision/scale in model</span></span>

<span data-ttu-id="dd7de-489">La précision et l’échelle d’une propriété peuvent désormais être spécifiées à l’aide du générateur de modèles.</span><span class="sxs-lookup"><span data-stu-id="dd7de-489">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="dd7de-490">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-490">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="dd7de-491">La précision et l’échelle peuvent toujours être définies par le biais du type de base de données complet, par exemple « Decimal (16, 4) ».</span><span class="sxs-lookup"><span data-stu-id="dd7de-491">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="dd7de-492">La documentation est suivie d’un problème [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="dd7de-492">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="dd7de-493">Spécifier SQL Server facteur de remplissage d’index</span><span class="sxs-lookup"><span data-stu-id="dd7de-493">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="dd7de-494">Le facteur de remplissage peut désormais être spécifié lors de la création d’un index sur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="dd7de-494">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="dd7de-495">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-495">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="dd7de-496">Preview 3</span><span class="sxs-lookup"><span data-stu-id="dd7de-496">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="dd7de-497">Include filtré</span><span class="sxs-lookup"><span data-stu-id="dd7de-497">Filtered Include</span></span>

<span data-ttu-id="dd7de-498">La méthode Include prend désormais en charge le filtrage des entités incluses.</span><span class="sxs-lookup"><span data-stu-id="dd7de-498">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="dd7de-499">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-499">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="dd7de-500">Cette requête renverra les blogs avec chaque publication associée, mais uniquement lorsque le titre de publication contient « fromage ».</span><span class="sxs-lookup"><span data-stu-id="dd7de-500">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="dd7de-501">Skip et Take peuvent également être utilisés pour réduire le nombre d’entités incluses.</span><span class="sxs-lookup"><span data-stu-id="dd7de-501">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="dd7de-502">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-502">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="dd7de-503">Cette requête renverra les blogs contenant au maximum cinq publications, incluses sur chaque blog.</span><span class="sxs-lookup"><span data-stu-id="dd7de-503">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="dd7de-504">Pour plus d’informations, consultez la [documentation include](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="dd7de-504">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="dd7de-505">Nouvelle API ModelBuilder pour les propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="dd7de-505">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="dd7de-506">Les propriétés de navigation sont principalement configurées lors de la [définition de relations](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="dd7de-506">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="dd7de-507">Toutefois, la nouvelle `Navigation` méthode peut être utilisée dans les cas où les propriétés de navigation nécessitent une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="dd7de-507">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="dd7de-508">Par exemple, pour définir un champ de stockage pour la navigation lorsque le champ est introuvable par Convention :</span><span class="sxs-lookup"><span data-stu-id="dd7de-508">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="dd7de-509">Notez que l' `Navigation` API ne remplace pas la configuration de la relation.</span><span class="sxs-lookup"><span data-stu-id="dd7de-509">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="dd7de-510">Au lieu de cela, il permet une configuration supplémentaire des propriétés de navigation dans les relations déjà découvertes ou définies.</span><span class="sxs-lookup"><span data-stu-id="dd7de-510">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="dd7de-511">Consultez la [documentation](xref:core/modeling/relationships#configuring-navigation-properties)sur la configuration des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="dd7de-511">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="dd7de-512">Nouveaux paramètres de ligne de commande pour les espaces de noms et les chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="dd7de-512">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="dd7de-513">Les migrations et la génération de modèles automatique permettent désormais de spécifier des espaces de noms sur la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="dd7de-513">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="dd7de-514">Par exemple, pour rétroconcevoir une base de données en plaçant le contexte et les classes de modèle dans différents espaces de noms :</span><span class="sxs-lookup"><span data-stu-id="dd7de-514">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="dd7de-515">Pour plus d’informations, consultez la documentation sur les [migrations](xref:core/managing-schemas/migrations/index#namespaces) et l' [ingénierie à rebours](xref:core/managing-schemas/scaffolding#directories-and-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="dd7de-515">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="dd7de-516">En outre, une chaîne de connexion peut désormais être passée à la `database-update` commande :</span><span class="sxs-lookup"><span data-stu-id="dd7de-516">Also, a connection string can now be passed to the `database-update` command:</span></span>

```dotnetcli
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="dd7de-517">Pour plus d’informations, consultez la documentation sur les [Outils](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .</span><span class="sxs-lookup"><span data-stu-id="dd7de-517">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="dd7de-518">Des paramètres équivalents ont également été ajoutés aux commandes PowerShell utilisées dans la console du gestionnaire de package VS.</span><span class="sxs-lookup"><span data-stu-id="dd7de-518">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="dd7de-519">EnableDetailedErrors a retourné</span><span class="sxs-lookup"><span data-stu-id="dd7de-519">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="dd7de-520">Pour des raisons de performances, EF n’effectue pas de vérifications null supplémentaires lors de la lecture des valeurs de la base de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-520">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="dd7de-521">Cela peut entraîner des exceptions qui sont difficiles à la cause première lorsqu’une valeur Null inattendue est rencontrée.</span><span class="sxs-lookup"><span data-stu-id="dd7de-521">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="dd7de-522">L’utilisation de `EnableDetailedErrors` permet d’ajouter une vérification de valeur null supplémentaire aux requêtes de sorte que, pour une faible surcharge de performances, ces erreurs sont plus faciles à suivre à une cause racine.</span><span class="sxs-lookup"><span data-stu-id="dd7de-522">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="dd7de-523">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-523">For example:</span></span>

```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="dd7de-524">La documentation est suivie d’un problème [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="dd7de-524">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="dd7de-525">Clés de partition Cosmos</span><span class="sxs-lookup"><span data-stu-id="dd7de-525">Cosmos partition keys</span></span>

<span data-ttu-id="dd7de-526">La clé de partition à utiliser pour une requête donnée peut désormais être spécifiée dans la requête.</span><span class="sxs-lookup"><span data-stu-id="dd7de-526">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="dd7de-527">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-527">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="dd7de-528">La documentation est suivie d’un problème [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="dd7de-528">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="dd7de-529">Prise en charge de la fonction DATALENGTH SQL Server</span><span class="sxs-lookup"><span data-stu-id="dd7de-529">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="dd7de-530">Vous pouvez y accéder à l’aide de la nouvelle `EF.Functions.DataLength` méthode.</span><span class="sxs-lookup"><span data-stu-id="dd7de-530">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="dd7de-531">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-531">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="dd7de-532">Préversion 2</span><span class="sxs-lookup"><span data-stu-id="dd7de-532">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="dd7de-533">Utiliser un attribut C# pour spécifier un champ de stockage de propriété</span><span class="sxs-lookup"><span data-stu-id="dd7de-533">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="dd7de-534">Un attribut C# peut maintenant être utilisé pour spécifier le champ de stockage d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="dd7de-534">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="dd7de-535">Cet attribut permet à EF Core d’écrire et de lire toujours dans le champ de stockage, ce qui se produit normalement, même si le champ de stockage ne peut pas être trouvé automatiquement.</span><span class="sxs-lookup"><span data-stu-id="dd7de-535">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="dd7de-536">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-536">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="dd7de-537">La documentation est suivie d’un problème [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="dd7de-537">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="dd7de-538">Terminer le mappage de discriminateur</span><span class="sxs-lookup"><span data-stu-id="dd7de-538">Complete discriminator mapping</span></span>

<span data-ttu-id="dd7de-539">EF Core utilise une colonne de discriminateur pour le [mappage TPH d’une hiérarchie d’héritage](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="dd7de-539">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](xref:core/modeling/inheritance).</span></span> <span data-ttu-id="dd7de-540">Certaines améliorations des performances sont possibles tant que EF Core connaît toutes les valeurs possibles pour le discriminateur.</span><span class="sxs-lookup"><span data-stu-id="dd7de-540">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="dd7de-541">EF Core 5,0 implémente désormais ces améliorations.</span><span class="sxs-lookup"><span data-stu-id="dd7de-541">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="dd7de-542">Par exemple, les versions précédentes de EF Core génèrent toujours ce SQL pour une requête qui retourne tous les types dans une hiérarchie :</span><span class="sxs-lookup"><span data-stu-id="dd7de-542">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="dd7de-543">EF Core 5,0 génère désormais les éléments suivants lorsqu’un mappage de discriminateur complet est configuré :</span><span class="sxs-lookup"><span data-stu-id="dd7de-543">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="dd7de-544">Il s’agit du comportement par défaut à partir de Preview 3.</span><span class="sxs-lookup"><span data-stu-id="dd7de-544">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="dd7de-545">Améliorations des performances dans Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="dd7de-545">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="dd7de-546">Nous avons apporté deux améliorations des performances pour SQLIte :</span><span class="sxs-lookup"><span data-stu-id="dd7de-546">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="dd7de-547">La récupération de données binaires et de chaîne avec GetBytes, GetChars et GetTextReader est désormais plus efficace grâce à l’utilisation de SqliteBlob et de flux.</span><span class="sxs-lookup"><span data-stu-id="dd7de-547">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="dd7de-548">L’initialisation de SqliteConnection est désormais différée.</span><span class="sxs-lookup"><span data-stu-id="dd7de-548">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="dd7de-549">Ces améliorations se trouvent dans le fournisseur Microsoft. Data. sqlite de ADO.NET et améliorent donc également les performances en dehors de EF Core.</span><span class="sxs-lookup"><span data-stu-id="dd7de-549">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="dd7de-550">Preview 1</span><span class="sxs-lookup"><span data-stu-id="dd7de-550">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="dd7de-551">Journalisation simple</span><span class="sxs-lookup"><span data-stu-id="dd7de-551">Simple logging</span></span>

<span data-ttu-id="dd7de-552">Cette fonctionnalité ajoute des fonctionnalités similaires à `Database.Log` dans EF6.</span><span class="sxs-lookup"><span data-stu-id="dd7de-552">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="dd7de-553">Autrement dit, il offre un moyen simple d’obtenir des journaux à partir de EF Core sans avoir besoin de configurer tout type d’infrastructure de journalisation externe.</span><span class="sxs-lookup"><span data-stu-id="dd7de-553">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="dd7de-554">La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="dd7de-554">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="dd7de-555">Une documentation supplémentaire est suivie par le [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)du problème.</span><span class="sxs-lookup"><span data-stu-id="dd7de-555">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="dd7de-556">Moyen simple d’utiliser le SQL généré</span><span class="sxs-lookup"><span data-stu-id="dd7de-556">Simple way to get generated SQL</span></span>

<span data-ttu-id="dd7de-557">EF Core 5,0 introduit la `ToQueryString` méthode d’extension, qui retourne le SQL que EF Core générera lors de l’exécution d’une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="dd7de-557">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="dd7de-558">La documentation préliminaire est incluse dans l' [État EF Weekly pour le 9 janvier 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="dd7de-558">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="dd7de-559">Une documentation supplémentaire est suivie par le [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)du problème.</span><span class="sxs-lookup"><span data-stu-id="dd7de-559">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="dd7de-560">Utiliser un attribut C# pour indiquer qu’une entité n’a pas de clé</span><span class="sxs-lookup"><span data-stu-id="dd7de-560">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="dd7de-561">Un type d’entité peut désormais être configuré comme n’ayant aucune clé à l’aide de la nouvelle `KeylessAttribute` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-561">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="dd7de-562">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-562">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="dd7de-563">La documentation est suivie d’un problème [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="dd7de-563">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="dd7de-564">La connexion ou la chaîne de connexion peut être modifiée lors de l’DbContext initialisé</span><span class="sxs-lookup"><span data-stu-id="dd7de-564">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="dd7de-565">Il est désormais plus facile de créer une instance DbContext sans connexion ou chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="dd7de-565">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="dd7de-566">En outre, la connexion ou la chaîne de connexion peut désormais être mutée sur l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="dd7de-566">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="dd7de-567">Cette fonctionnalité permet à la même instance de contexte de se connecter de manière dynamique à différentes bases de données.</span><span class="sxs-lookup"><span data-stu-id="dd7de-567">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="dd7de-568">La documentation est suivie d’un problème [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="dd7de-568">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="dd7de-569">Proxys de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="dd7de-569">Change-tracking proxies</span></span>

<span data-ttu-id="dd7de-570">EF Core pouvez désormais générer des proxies d’exécution qui implémentent automatiquement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) et [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span><span class="sxs-lookup"><span data-stu-id="dd7de-570">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="dd7de-571">Celles-ci rerapportent ensuite les modifications de valeur sur les propriétés d’entité directement à EF Core, évitant ainsi la nécessité d’analyser les modifications.</span><span class="sxs-lookup"><span data-stu-id="dd7de-571">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="dd7de-572">Toutefois, les proxies sont fournis avec leur propre ensemble de limitations, donc ils ne le sont pas pour tout le monde.</span><span class="sxs-lookup"><span data-stu-id="dd7de-572">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="dd7de-573">La documentation est suivie d’un problème [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="dd7de-573">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="dd7de-574">Vues de débogage améliorées</span><span class="sxs-lookup"><span data-stu-id="dd7de-574">Enhanced debug views</span></span>

<span data-ttu-id="dd7de-575">Les vues de débogage sont un moyen simple de consulter les éléments internes d’EF Core lors du débogage des problèmes.</span><span class="sxs-lookup"><span data-stu-id="dd7de-575">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="dd7de-576">Une vue de débogage a été implémentée pour le modèle il y a quelque temps.</span><span class="sxs-lookup"><span data-stu-id="dd7de-576">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="dd7de-577">Pour EF Core 5,0, nous avons facilité la lecture et l’ajout d’une nouvelle vue de débogage pour les entités suivies dans le gestionnaire d’État.</span><span class="sxs-lookup"><span data-stu-id="dd7de-577">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="dd7de-578">La documentation préliminaire est incluse dans l' [État EF Weekly pour le 12 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="dd7de-578">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="dd7de-579">Une documentation supplémentaire est suivie par le [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)du problème.</span><span class="sxs-lookup"><span data-stu-id="dd7de-579">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="dd7de-580">Amélioration de la gestion de la sémantique null de la base de données</span><span class="sxs-lookup"><span data-stu-id="dd7de-580">Improved handling of database null semantics</span></span>

<span data-ttu-id="dd7de-581">En général, les bases de données relationnelles traitent la valeur NULL comme une valeur inconnue et, par conséquent, n’est pas égale à une autre valeur NULL.</span><span class="sxs-lookup"><span data-stu-id="dd7de-581">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="dd7de-582">Alors que C# traite NULL comme une valeur définie, qui compare la valeur à toute autre valeur null.</span><span class="sxs-lookup"><span data-stu-id="dd7de-582">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="dd7de-583">EF Core par défaut traduit les requêtes afin qu’elles utilisent la sémantique null C#.</span><span class="sxs-lookup"><span data-stu-id="dd7de-583">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="dd7de-584">EF Core 5,0 améliore considérablement l’efficacité de ces traductions.</span><span class="sxs-lookup"><span data-stu-id="dd7de-584">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="dd7de-585">La documentation est suivie d’un problème [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="dd7de-585">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="dd7de-586">Propriétés de l’indexeur</span><span class="sxs-lookup"><span data-stu-id="dd7de-586">Indexer properties</span></span>

<span data-ttu-id="dd7de-587">EF Core 5,0 prend en charge le mappage des propriétés de l’indexeur C#.</span><span class="sxs-lookup"><span data-stu-id="dd7de-587">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="dd7de-588">Ces propriétés permettent aux entités d’agir en tant que conteneurs de propriétés où les colonnes sont mappées à des propriétés nommées dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="dd7de-588">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="dd7de-589">La documentation est suivie d’un problème [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="dd7de-589">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="dd7de-590">Génération de contraintes de validation pour les mappages d’énumération</span><span class="sxs-lookup"><span data-stu-id="dd7de-590">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="dd7de-591">EF Core migrations 5,0 peut désormais générer des contraintes de validation pour les mappages de propriété d’énumération.</span><span class="sxs-lookup"><span data-stu-id="dd7de-591">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="dd7de-592">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-592">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="dd7de-593">La documentation est suivie d’un problème [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="dd7de-593">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="dd7de-594">IsRelational</span><span class="sxs-lookup"><span data-stu-id="dd7de-594">IsRelational</span></span>

<span data-ttu-id="dd7de-595">Une nouvelle `IsRelational` méthode a été ajoutée en plus des `IsSqlServer` , `IsSqlite` , et existants `IsInMemory` .</span><span class="sxs-lookup"><span data-stu-id="dd7de-595">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="dd7de-596">Cette méthode peut être utilisée pour tester si DbContext utilise un fournisseur de base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="dd7de-596">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="dd7de-597">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-597">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="dd7de-598">La documentation est suivie d’un problème [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="dd7de-598">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="dd7de-599">Accès concurrentiel optimiste Cosmos avec ETags</span><span class="sxs-lookup"><span data-stu-id="dd7de-599">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="dd7de-600">Le fournisseur de base de données Azure Cosmos DB prend désormais en charge l’accès concurrentiel optimiste à l’aide d’ETags.</span><span class="sxs-lookup"><span data-stu-id="dd7de-600">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="dd7de-601">Utilisez le générateur de modèles de OnModelCreating pour configurer un ETag :</span><span class="sxs-lookup"><span data-stu-id="dd7de-601">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="dd7de-602">SaveChanges lèvera ensuite un en cas de `DbUpdateConcurrencyException` conflit d’accès concurrentiel, qui [peut être géré](xref:core/saving/concurrency) pour implémenter les nouvelles tentatives, etc.</span><span class="sxs-lookup"><span data-stu-id="dd7de-602">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](xref:core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="dd7de-603">La documentation est suivie d’un problème [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="dd7de-603">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="dd7de-604">Traductions de requêtes pour d’autres constructions DateTime</span><span class="sxs-lookup"><span data-stu-id="dd7de-604">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="dd7de-605">Les requêtes contenant la nouvelle construction DateTime sont maintenant traduites.</span><span class="sxs-lookup"><span data-stu-id="dd7de-605">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="dd7de-606">En outre, les fonctions de SQL Server suivantes sont maintenant mappées :</span><span class="sxs-lookup"><span data-stu-id="dd7de-606">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="dd7de-607">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="dd7de-607">DateDiffWeek</span></span>
* <span data-ttu-id="dd7de-608">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="dd7de-608">DateFromParts</span></span>

<span data-ttu-id="dd7de-609">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-609">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="dd7de-610">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="dd7de-610">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="dd7de-611">Traductions de requêtes pour d’autres constructions de tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="dd7de-611">Query translations for more byte array constructs</span></span>

<span data-ttu-id="dd7de-612">Les requêtes utilisant les propriétés Contains, Length, SequenceEqual, etc. sur Byte [] sont désormais traduites en SQL.</span><span class="sxs-lookup"><span data-stu-id="dd7de-612">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="dd7de-613">La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="dd7de-613">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="dd7de-614">Une documentation supplémentaire est suivie par le [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)du problème.</span><span class="sxs-lookup"><span data-stu-id="dd7de-614">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="dd7de-615">Traduction de requête pour l’inverse</span><span class="sxs-lookup"><span data-stu-id="dd7de-615">Query translation for Reverse</span></span>

<span data-ttu-id="dd7de-616">Les requêtes utilisant `Reverse` sont désormais traduites.</span><span class="sxs-lookup"><span data-stu-id="dd7de-616">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="dd7de-617">Exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-617">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="dd7de-618">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="dd7de-618">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="dd7de-619">Traduction des requêtes pour les opérateurs au niveau du bit</span><span class="sxs-lookup"><span data-stu-id="dd7de-619">Query translation for bitwise operators</span></span>

<span data-ttu-id="dd7de-620">Les requêtes utilisant des opérateurs de bits sont maintenant traduites dans d’autres cas, par exemple :</span><span class="sxs-lookup"><span data-stu-id="dd7de-620">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="dd7de-621">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="dd7de-621">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="dd7de-622">Traduction des requêtes pour les chaînes sur Cosmos</span><span class="sxs-lookup"><span data-stu-id="dd7de-622">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="dd7de-623">Les requêtes qui utilisent les méthodes de chaîne Contains, StartsWith et EndsWith sont désormais traduites lors de l’utilisation du fournisseur Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="dd7de-623">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="dd7de-624">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="dd7de-624">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
