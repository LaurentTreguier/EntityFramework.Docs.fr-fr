---
title: Nouveautés de EF Core 5,0
description: Vue d’ensemble des nouvelles fonctionnalités de EF Core 5,0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: ab6a49fa0b23aff83ee64446e498b7e94c632020
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023505"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="15ea4-103">Nouveautés de EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="15ea4-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="15ea4-104">La liste suivante répertorie les principales nouvelles fonctionnalités de EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="15ea4-104">The following list includes the major new features in EF Core 5.0.</span></span> <span data-ttu-id="15ea4-105">Pour obtenir la liste complète des problèmes dans la version, consultez notre [suivi](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0)des problèmes.</span><span class="sxs-lookup"><span data-stu-id="15ea4-105">For the full list of issues in the release, see our [issue tracker](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).</span></span>

<span data-ttu-id="15ea4-106">En tant que version majeure, EF Core 5,0 contient également plusieurs [modifications avec rupture](xref:core/what-is-new/ef-core-5.0/breaking-changes), qui sont des améliorations d’API ou des changements de comportement qui peuvent avoir un impact négatif sur les applications existantes.</span><span class="sxs-lookup"><span data-stu-id="15ea4-106">As a major release, EF Core 5.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), which are API improvements or behavioral changes that may have negative impact on existing applications.</span></span>

## <a name="many-to-many"></a><span data-ttu-id="15ea4-107">Plusieurs à plusieurs</span><span class="sxs-lookup"><span data-stu-id="15ea4-107">Many-to-many</span></span>

<span data-ttu-id="15ea4-108">EF Core 5,0 prend en charge les relations plusieurs-à-plusieurs sans mapper explicitement la table de jointure.</span><span class="sxs-lookup"><span data-stu-id="15ea4-108">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="15ea4-109">Par exemple, considérez les types d’entités suivants :</span><span class="sxs-lookup"><span data-stu-id="15ea4-109">For example, consider these entity types:</span></span>

```csharp
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

<span data-ttu-id="15ea4-110">EF Core 5,0 reconnaît qu’il s’agit d’une relation plusieurs-à-plusieurs par Convention, et crée automatiquement une `PostTag` table de jointure dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="15ea4-110">EF Core 5.0 recognizes this as a many-to-many relationship by convention, and automatically creates a `PostTag` join table in the database.</span></span> <span data-ttu-id="15ea4-111">Les données peuvent être interrogées et mises à jour sans référence explicite à la table de jointure, ce qui simplifie considérablement le code.</span><span class="sxs-lookup"><span data-stu-id="15ea4-111">Data can be queried and updated without explicitly referencing the join table, considerably simplifying code.</span></span> <span data-ttu-id="15ea4-112">La table de jointure peut encore être personnalisée et interrogée explicitement si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="15ea4-112">The join table can still be customized and queried explicitly if needed.</span></span>

<span data-ttu-id="15ea4-113">Pour plus d’informations, [consultez la documentation complète sur plusieurs à plusieurs](xref:core/modeling/relationships#many-to-many).</span><span class="sxs-lookup"><span data-stu-id="15ea4-113">For further information, [see the full documentation on many-to-many](xref:core/modeling/relationships#many-to-many).</span></span>

## <a name="split-queries"></a><span data-ttu-id="15ea4-114">Fractionner des requêtes</span><span class="sxs-lookup"><span data-stu-id="15ea4-114">Split queries</span></span>

<span data-ttu-id="15ea4-115">À compter de EF Core 3,0, EF Core génère toujours une requête SQL unique pour chaque requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="15ea4-115">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="15ea4-116">Cela garantit la cohérence des données retournées dans les contraintes du mode de transaction en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="15ea4-116">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="15ea4-117">Toutefois, cela peut devenir très lent lorsque la requête utilise `Include` ou une projection pour rétablir plusieurs collections associées.</span><span class="sxs-lookup"><span data-stu-id="15ea4-117">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="15ea4-118">EF Core 5,0 autorise désormais une seule requête LINQ, y compris les collections associées, à fractionner en plusieurs requêtes SQL.</span><span class="sxs-lookup"><span data-stu-id="15ea4-118">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="15ea4-119">Cela peut améliorer considérablement les performances, mais peut entraîner une incohérence dans les résultats retournés si les données changent entre les deux requêtes.</span><span class="sxs-lookup"><span data-stu-id="15ea4-119">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="15ea4-120">Les transactions sérialisables ou d’instantané peuvent être utilisées pour atténuer ce problème et obtenir une cohérence avec les requêtes fractionnées, mais cela peut réduire les coûts de performances et la différence comportementale.</span><span class="sxs-lookup"><span data-stu-id="15ea4-120">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

<span data-ttu-id="15ea4-121">Par exemple, considérez une requête qui extrait deux niveaux de collections associées à l’aide de `Include` :</span><span class="sxs-lookup"><span data-stu-id="15ea4-121">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

<span data-ttu-id="15ea4-122">Par défaut, EF Core génère le code SQL suivant lorsque vous utilisez le fournisseur SQLite :</span><span class="sxs-lookup"><span data-stu-id="15ea4-122">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

<span data-ttu-id="15ea4-123">Avec les requêtes de fractionnement, le code SQL suivant est généré à la place :</span><span class="sxs-lookup"><span data-stu-id="15ea4-123">With split queries, the following SQL is generated instead:</span></span>

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

<span data-ttu-id="15ea4-124">Les requêtes Split peuvent être activées en plaçant le nouvel `AsSplitQuery` opérateur n’importe où dans votre requête LINQ, ou globalement dans le de votre modèle `OnConfiguring` .</span><span class="sxs-lookup"><span data-stu-id="15ea4-124">Split queries can be enabled by placing the new `AsSplitQuery` operator anywhere in your LINQ query, or globally in your model's `OnConfiguring`.</span></span> <span data-ttu-id="15ea4-125">Pour plus d’informations, [consultez la documentation complète sur les requêtes de fractionnement](xref:core/querying/single-split-queries).</span><span class="sxs-lookup"><span data-stu-id="15ea4-125">For further information, [see the full documentation on split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="simple-logging-and-improved-diagnostics"></a><span data-ttu-id="15ea4-126">Journalisation simple et diagnostics améliorés</span><span class="sxs-lookup"><span data-stu-id="15ea4-126">Simple logging and improved diagnostics</span></span>

<span data-ttu-id="15ea4-127">EF Core 5,0 introduit une méthode simple pour configurer la journalisation via la nouvelle `LogTo` méthode.</span><span class="sxs-lookup"><span data-stu-id="15ea4-127">EF Core 5.0 introduces a simple way to set up logging via the new `LogTo` method.</span></span> <span data-ttu-id="15ea4-128">Les messages de journalisation seront écrits dans la console, y compris tous les éléments SQL générés par EF Core :</span><span class="sxs-lookup"><span data-stu-id="15ea4-128">The following will cause logging messages to be written to the console, including all SQL generated by EF Core:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

<span data-ttu-id="15ea4-129">En outre, il est désormais possible d’appeler `ToQueryString` sur n’importe quelle requête LINQ, en récupérant le SQL que la requête exécuterait :</span><span class="sxs-lookup"><span data-stu-id="15ea4-129">In addition, it is now possible to call `ToQueryString` on any LINQ query, retrieving the SQL that the query would execute:</span></span>

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

<span data-ttu-id="15ea4-130">Enfin, différents types de EF Core ont été associés à une `DebugView` propriété améliorée qui fournit une vue détaillée des éléments internes.</span><span class="sxs-lookup"><span data-stu-id="15ea4-130">Finally, various EF Core types have been fitted with an enhanced `DebugView` property which provides a detailed view into the internals.</span></span> <span data-ttu-id="15ea4-131">Par exemple, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> peut être consulté pour voir exactement les entités qui font l’objet d’un suivi dans un moment donné.</span><span class="sxs-lookup"><span data-stu-id="15ea4-131">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> can be consulted to see exactly which entities are being tracked in a given moment.</span></span>

<span data-ttu-id="15ea4-132">Pour plus d’informations, [consultez la documentation sur la journalisation et l’interception](xref:core/logging-events-diagnostics/index).</span><span class="sxs-lookup"><span data-stu-id="15ea4-132">For further information, [see the documentation on logging and interception](xref:core/logging-events-diagnostics/index).</span></span>

## <a name="filtered-include"></a><span data-ttu-id="15ea4-133">Include filtré</span><span class="sxs-lookup"><span data-stu-id="15ea4-133">Filtered include</span></span>

<span data-ttu-id="15ea4-134">La `Include` méthode prend désormais en charge le filtrage des entités incluses :</span><span class="sxs-lookup"><span data-stu-id="15ea4-134">The `Include` method now supports filtering of the entities included:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="15ea4-135">Cette requête renverra les blogs avec chaque publication associée, mais uniquement lorsque le titre de publication contient « fromage ».</span><span class="sxs-lookup"><span data-stu-id="15ea4-135">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="15ea4-136">Pour plus d’informations, [consultez la documentation complète sur les includes filtrées](xref:core/querying/related-data/eager#filtered-include).</span><span class="sxs-lookup"><span data-stu-id="15ea4-136">For further information, [see the full documentation on filtered include](xref:core/querying/related-data/eager#filtered-include).</span></span>

## <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="15ea4-137">Mappage table par type (TPT)</span><span class="sxs-lookup"><span data-stu-id="15ea4-137">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="15ea4-138">Par défaut, EF Core mappe une hiérarchie d’héritage de types .NET à une seule table de base de données.</span><span class="sxs-lookup"><span data-stu-id="15ea4-138">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="15ea4-139">C’est ce que l’on appelle le mappage TPH (table par hiérarchie).</span><span class="sxs-lookup"><span data-stu-id="15ea4-139">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="15ea4-140">EF Core 5,0 permet également de mapper chaque type .NET dans une hiérarchie d’héritage à une autre table de base de données. connu sous le nom de mappage table par type (TPT).</span><span class="sxs-lookup"><span data-stu-id="15ea4-140">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="15ea4-141">Par exemple, considérez ce modèle avec une hiérarchie mappée :</span><span class="sxs-lookup"><span data-stu-id="15ea4-141">For example, consider this model with a mapped hierarchy:</span></span>

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Cat : Animal
{
    public string EducationLevel { get; set; }
}

public class Dog : Animal
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="15ea4-142">Avec TPT, une table de base de données est créée pour chaque type dans la hiérarchie :</span><span class="sxs-lookup"><span data-stu-id="15ea4-142">With TPT, a database table is created for each type in the hierarchy:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);
```

<span data-ttu-id="15ea4-143">Pour plus d’informations, [consultez la documentation complète sur TPT](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="15ea4-143">For further information, [see the full documentation on TPT](xref:core/modeling/inheritance).</span></span>

## <a name="flexible-entity-mapping"></a><span data-ttu-id="15ea4-144">Mappage d’entité flexible</span><span class="sxs-lookup"><span data-stu-id="15ea4-144">Flexible entity mapping</span></span>

<span data-ttu-id="15ea4-145">Les types d’entités sont couramment mappés à des tables ou à des vues de telle sorte que EF Core récupère le contenu de la table ou de la vue lors de l’interrogation de ce type.</span><span class="sxs-lookup"><span data-stu-id="15ea4-145">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="15ea4-146">EF Core 5,0 ajoute des options de mappage supplémentaires, où une entité peut être mappée à une requête SQL (appelée « définition de requête ») ou à une fonction table (TVF) :</span><span class="sxs-lookup"><span data-stu-id="15ea4-146">EF Core 5.0 adds additional mapping options, where an entity can be mapped to a SQL query (called a "defining query"), or to a table-valued function (TVF):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");

    modelBuilder.Entity<Blog>().ToFunction("BlogsReturningFunction");
}
```

<span data-ttu-id="15ea4-147">Les fonctions table peuvent également être mappées à une méthode .NET plutôt qu’à un DbSet, ce qui permet de passer des paramètres ; le mappage peut être configuré avec <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A> .</span><span class="sxs-lookup"><span data-stu-id="15ea4-147">Table-valued functions can also be mapped to a .NET method rather than to a DbSet, allowing parameters to be passed; the mapping can be set up with <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A>.</span></span>

<span data-ttu-id="15ea4-148">Enfin, il est désormais possible de mapper une entité à une vue lors de l’interrogation (ou à une fonction ou à la définition d’une requête), mais à une table lors de la mise à jour :</span><span class="sxs-lookup"><span data-stu-id="15ea4-148">Finally, it is now possible to map an entity to a view when querying (or to a function or defining query), but to a table when updating:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a><span data-ttu-id="15ea4-149">Types d’entité et conteneurs de propriétés de type partagé</span><span class="sxs-lookup"><span data-stu-id="15ea4-149">Shared-type entity types and property bags</span></span>

<span data-ttu-id="15ea4-150">EF Core 5,0 permet au même type CLR d’être mappé à plusieurs types d’entité différents ; ces types sont appelés types d’entité de type Shared.</span><span class="sxs-lookup"><span data-stu-id="15ea4-150">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types; such types are known as shared-type entity types.</span></span> <span data-ttu-id="15ea4-151">Bien qu’un type CLR puisse être utilisé avec cette fonctionnalité, .NET `Dictionary` offre un cas d’utilisation particulièrement attrayant, que nous appelons « conteneurs de propriétés » :</span><span class="sxs-lookup"><span data-stu-id="15ea4-151">While any CLR type can be used with this feature, .NET `Dictionary` offers a particularly compelling use-case which we call "property bags":</span></span>

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<decimal>("Price");
        });
    }
}
```

<span data-ttu-id="15ea4-152">Ces entités peuvent ensuite être interrogées et mises à jour de la même façon que les types d’entités normaux avec leur propre type CLR dédié.</span><span class="sxs-lookup"><span data-stu-id="15ea4-152">These entities can then be queried and updated just like normal entity types with their own, dedicated CLR type.</span></span> <span data-ttu-id="15ea4-153">Vous trouverez plus d’informations dans la documentation sur les [conteneurs de propriétés](xref:core/modeling/shadow-properties).</span><span class="sxs-lookup"><span data-stu-id="15ea4-153">More information can be found in the documentation on [property bags](xref:core/modeling/shadow-properties).</span></span>

## <a name="required-11-dependents"></a><span data-ttu-id="15ea4-154">Dépendants 1:1 requis</span><span class="sxs-lookup"><span data-stu-id="15ea4-154">Required 1:1 dependents</span></span>

<span data-ttu-id="15ea4-155">Dans EF Core 3,1, la terminaison dépendante d’une relation un-à-un était toujours considérée comme facultative.</span><span class="sxs-lookup"><span data-stu-id="15ea4-155">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="15ea4-156">Cela était surtout le cas lors de l’utilisation d’entités détenues, car toutes les colonnes de l’entité appartenant ont été créées en tant que valeurs NULL dans la base de données, même si elles ont été configurées comme requises dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="15ea4-156">This was most apparent when using owned entities, as all the owned entity's column were created as nullable in the database, even if they were configured as required in the model.</span></span>

<span data-ttu-id="15ea4-157">Dans EF Core 5,0, une navigation vers une entité détenue peut être configurée comme un dépendant obligatoire.</span><span class="sxs-lookup"><span data-stu-id="15ea4-157">In EF Core 5.0, a navigation to an owned entity can be configured as as a required dependent.</span></span> <span data-ttu-id="15ea4-158">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="15ea4-158">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();
    });
}
```

## <a name="dbcontextfactory"></a><span data-ttu-id="15ea4-159">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="15ea4-159">DbContextFactory</span></span>

<span data-ttu-id="15ea4-160">EF Core 5,0 introduit `AddDbContextFactory` et `AddPooledDbContextFactory` pour inscrire une fabrique pour la création d’instances de DbContext dans le conteneur d’injection de dépendances (D.I.) de l’application. cela peut être utile lorsque le code d’application doit créer et supprimer manuellement des instances de contexte.</span><span class="sxs-lookup"><span data-stu-id="15ea4-160">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container; this can be useful when application code needs to create and dispose context instances manually.</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="15ea4-161">À ce stade, les services d’application tels que les contrôleurs de ASP.NET Core peuvent ensuite être injectés avec `IDbContextFactory<TContext>` et l’utiliser pour instancier des instances de contexte :</span><span class="sxs-lookup"><span data-stu-id="15ea4-161">At this point, application services such as ASP.NET Core controllers can then be injected with `IDbContextFactory<TContext>`, and use it to instantiate context instances:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
        => _contextFactory = contextFactory;

    public void DoSomeThing()
    {
        using (var context = _contextFactory.CreateDbContext())
        {
            // ...
        }
    }
}
```

<span data-ttu-id="15ea4-162">Pour plus d’informations, [consultez la documentation complète sur DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).</span><span class="sxs-lookup"><span data-stu-id="15ea4-162">For further information, [see the full documentation on DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).</span></span>

## <a name="sqlite-table-rebuilds"></a><span data-ttu-id="15ea4-163">Régénérations de la table SQLite</span><span class="sxs-lookup"><span data-stu-id="15ea4-163">SQLite table rebuilds</span></span>

<span data-ttu-id="15ea4-164">Par rapport aux autres bases de données, SQLite est relativement limité dans ses fonctionnalités de manipulation de schéma. par exemple, la suppression d’une colonne d’une table existante n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="15ea4-164">Compared to other databases, SQLite is relatively limited in its schema manipulation capabilities; for example, dropping a column from an existing table is not supported.</span></span> <span data-ttu-id="15ea4-165">EF Core 5,0 contournent ces limitations en créant automatiquement une nouvelle table, en copiant les données de l’ancienne table, en supprimant l’ancienne table et en renommant la nouvelle.</span><span class="sxs-lookup"><span data-stu-id="15ea4-165">EF Core 5.0 works around these limitations by automatically creating a new table, copying the data from the old table, dropping the old table and renaming the new one.</span></span> <span data-ttu-id="15ea4-166">Cette « reconstruit » la table et permet d’appliquer en toute sécurité les opérations de migration précédemment non prises en charge.</span><span class="sxs-lookup"><span data-stu-id="15ea4-166">This "rebuilds" the table, and allows previously unsupported migration operations to be safely applied.</span></span>

<span data-ttu-id="15ea4-167">Pour plus d’informations sur les opérations de migration qui sont désormais prises en charge via les reconstructions de table, [consultez cette page de documentation](xref:core/providers/sqlite/limitations#migrations-limitations).</span><span class="sxs-lookup"><span data-stu-id="15ea4-167">For details on which migration operations are now supported via table rebuilds, [see this documentation page](xref:core/providers/sqlite/limitations#migrations-limitations).</span></span>

## <a name="database-collations"></a><span data-ttu-id="15ea4-168">Classements de base de données</span><span class="sxs-lookup"><span data-stu-id="15ea4-168">Database collations</span></span>

<span data-ttu-id="15ea4-169">EF Core 5,0 introduit la prise en charge de la spécification des classements de texte au niveau de la base de données, de la colonne ou de la requête.</span><span class="sxs-lookup"><span data-stu-id="15ea4-169">EF Core 5.0 introduces support for specifying text collations at the database, column or query level.</span></span> <span data-ttu-id="15ea4-170">Cela permet de configurer le respect de la casse et d’autres aspects textuels de manière à ce qu’ils soient flexibles et ne compromettent pas les performances des requêtes.</span><span class="sxs-lookup"><span data-stu-id="15ea4-170">This allows case sensitivity and other textual aspects to be configured in a way that is both flexible and does not compromise query performance.</span></span>

<span data-ttu-id="15ea4-171">Par exemple, le code suivant configure la `Name` colonne pour qu’elle respecte la casse sur SQL Server, et tous les index créés sur la colonne fonctionneront en conséquence :</span><span class="sxs-lookup"><span data-stu-id="15ea4-171">For example, the following will configure the `Name` column to be case-sensitive on SQL Server, and any indexes created on the column will function accordingly:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

<span data-ttu-id="15ea4-172">Pour plus d’informations, [consultez la documentation complète sur les classements et le respect de la casse](xref:core/miscellaneous/collations-and-case-sensitivity).</span><span class="sxs-lookup"><span data-stu-id="15ea4-172">For further information, [see the full documentation on collations and case sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>

## <a name="event-counters"></a><span data-ttu-id="15ea4-173">Compteurs d’événements</span><span class="sxs-lookup"><span data-stu-id="15ea4-173">Event counters</span></span>

<span data-ttu-id="15ea4-174">EF Core 5,0 expose des [compteurs d’événements](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) qui peuvent être utilisés pour suivre les performances de votre application et repérer différentes anomalies.</span><span class="sxs-lookup"><span data-stu-id="15ea4-174">EF Core 5.0 exposes [event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) which can be used to track your application's performance and spot various anomalies.</span></span> <span data-ttu-id="15ea4-175">Connectez-vous simplement à un processus exécutant EF avec l’outil [dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) :</span><span class="sxs-lookup"><span data-stu-id="15ea4-175">Simply attach to a process running EF with the [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) tool:</span></span>

```console
> dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496

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

<span data-ttu-id="15ea4-176">Pour plus d’informations, [consultez la documentation complète sur les compteurs d’événements](xref:core/logging-events-diagnostics/event-counters).</span><span class="sxs-lookup"><span data-stu-id="15ea4-176">For further information, [see the full documentation on event counters](xref:core/logging-events-diagnostics/event-counters).</span></span>

## <a name="other-features"></a><span data-ttu-id="15ea4-177">Autres fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="15ea4-177">Other features</span></span>

### <a name="model-building"></a><span data-ttu-id="15ea4-178">Génération de modèles</span><span class="sxs-lookup"><span data-stu-id="15ea4-178">Model building</span></span>

* <span data-ttu-id="15ea4-179">Les API de création de modèle ont été introduites pour faciliter la configuration des [comparateurs de valeurs](xref:core/modeling/value-comparers).</span><span class="sxs-lookup"><span data-stu-id="15ea4-179">Model building APIs have been introduced for easier configuration of [value comparers](xref:core/modeling/value-comparers).</span></span>
* <span data-ttu-id="15ea4-180">Les colonnes calculées peuvent désormais être configurées comme [ *stockées* ou *virtuelles*](xref:core/modeling/generated-properties#computed-columns).</span><span class="sxs-lookup"><span data-stu-id="15ea4-180">Computed columns can now be configured as [*stored* or *virtual*](xref:core/modeling/generated-properties#computed-columns).</span></span>
* <span data-ttu-id="15ea4-181">La précision et l’échelle peuvent désormais être configurées [par le biais de l’API Fluent](xref:core/modeling/entity-properties#precision-and-scale).</span><span class="sxs-lookup"><span data-stu-id="15ea4-181">Precision and scale can now be configured [via the Fluent API](xref:core/modeling/entity-properties#precision-and-scale).</span></span>
* <span data-ttu-id="15ea4-182">De nouvelles API de création de modèle ont été introduites pour les [Propriétés de navigation](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="15ea4-182">New model building APIs have been introduced for [navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>
* <span data-ttu-id="15ea4-183">De nouvelles API de création de modèle ont été introduites pour les champs, comme les propriétés.</span><span class="sxs-lookup"><span data-stu-id="15ea4-183">New model building APIs have been introduced for fields, similar to properties.</span></span>
* <span data-ttu-id="15ea4-184">Les types .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) et [IPAddress](/dotnet/api/system.net.ipaddress) peuvent désormais être mappés à des colonnes de chaîne de base de données.</span><span class="sxs-lookup"><span data-stu-id="15ea4-184">The .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) and [IPAddress](/dotnet/api/system.net.ipaddress) types can now be mapped to database string columns.</span></span>
* <span data-ttu-id="15ea4-185">Un champ de stockage peut désormais être configuré par [le biais du nouvel `[BackingField]` attribut](xref:core/modeling/backing-field).</span><span class="sxs-lookup"><span data-stu-id="15ea4-185">A backing field can now be configured via [the new `[BackingField]` attribute](xref:core/modeling/backing-field).</span></span>
* <span data-ttu-id="15ea4-186">Les champs de stockage Nullable sont désormais autorisés, ce qui offre une meilleure prise en charge des valeurs par défaut générées par le magasin, où la valeur par défaut du CLR n’est pas une bonne valeur de sentinelle (notable `bool` ).</span><span class="sxs-lookup"><span data-stu-id="15ea4-186">Nullable backing fields are now allowed, providing better support for store-generated defaults where the CLR default isn't a good sentinel value (notable `bool`).</span></span>
* <span data-ttu-id="15ea4-187">Un nouvel `[Index]` attribut peut être utilisé sur un type d’entité pour spécifier un index au lieu d’utiliser l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="15ea4-187">A new `[Index]` attribute can be used on an entity type to specify an index, instead of using the Fluent API.</span></span>
* <span data-ttu-id="15ea4-188">Un nouvel `[Keyless]` attribut peut être utilisé pour configurer un type d’entité [comme n’ayant aucune clé](xref:core/modeling/keyless-entity-types).</span><span class="sxs-lookup"><span data-stu-id="15ea4-188">A new `[Keyless]` attribute can be used to configure an entity type [as having no key](xref:core/modeling/keyless-entity-types).</span></span>
* <span data-ttu-id="15ea4-189">Par défaut, [EF Core considère maintenant les discriminateurs comme *terminés*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), ce qui signifie qu’il s’attend à ne jamais voir les valeurs de discriminateur non configurées par l’application dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="15ea4-189">By default, [EF Core now regards discriminators as *complete*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), meaning that it expects to never see discriminator values not configured by the application in the model.</span></span> <span data-ttu-id="15ea4-190">Cela permet d’améliorer les performances et peut être désactivé si votre colonne de discriminateur peut contenir des valeurs inconnues.</span><span class="sxs-lookup"><span data-stu-id="15ea4-190">This allows for some performance improvements, and can be disabled if your discriminator column might hold unknown values.</span></span>

### <a name="query"></a><span data-ttu-id="15ea4-191">Query</span><span class="sxs-lookup"><span data-stu-id="15ea4-191">Query</span></span>

* <span data-ttu-id="15ea4-192">Les exceptions d’échec de la traduction de requête contiennent désormais des raisons plus explicites concernant les raisons de l’échec, afin de vous aider à identifier le problème.</span><span class="sxs-lookup"><span data-stu-id="15ea4-192">Query translation failure exceptions now contain more explicit reasons about the reasons for the failure, to help pinpoint the issue.</span></span>
* <span data-ttu-id="15ea4-193">Les requêtes sans suivi peuvent désormais effectuer la [résolution d’identité](xref:core/querying/tracking#identity-resolution), ce qui évite le renvoi de plusieurs instances d’entité pour le même objet de base de données.</span><span class="sxs-lookup"><span data-stu-id="15ea4-193">No-tracking queries can now perform [identity resolution](xref:core/querying/tracking#identity-resolution), avoiding multiple entity instances being returned for the same database object.</span></span>
* <span data-ttu-id="15ea4-194">Ajout de la prise en charge de GroupBy avec des agrégats conditionnels (par exemple `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))` ,).</span><span class="sxs-lookup"><span data-stu-id="15ea4-194">Added support for GroupBy with conditional aggregates (e.g. `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`).</span></span>
* <span data-ttu-id="15ea4-195">Ajout de la prise en charge de la traduction de l’opérateur distinct sur les éléments de groupe avant Aggregate.</span><span class="sxs-lookup"><span data-stu-id="15ea4-195">Added support for translating the Distinct operator over group elements before aggregate.</span></span>
* <span data-ttu-id="15ea4-196">Traduction de [`Reverse`](/dotnet/api/system.linq.queryable.reverse) .</span><span class="sxs-lookup"><span data-stu-id="15ea4-196">Translation of [`Reverse`](/dotnet/api/system.linq.queryable.reverse).</span></span>
* <span data-ttu-id="15ea4-197">Traduction améliorée `DateTime` pour SQL Server (par exemple [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A) , [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A) ).</span><span class="sxs-lookup"><span data-stu-id="15ea4-197">Improved translation around `DateTime` for SQL Server (e.g. [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A), [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A)).</span></span>
* <span data-ttu-id="15ea4-198">Traduction de nouvelles méthodes sur des tableaux d’octets (par exemple [`Contains`](/dotnet/api/system.linq.enumerable.contains) ,, [`Length`](/dotnet/api/system.array.length) [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal) ).</span><span class="sxs-lookup"><span data-stu-id="15ea4-198">Translation of new methods on byte arrays (e.g. [`Contains`](/dotnet/api/system.linq.enumerable.contains), [`Length`](/dotnet/api/system.array.length), [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)).</span></span>
* <span data-ttu-id="15ea4-199">Traduction de certains opérateurs de bits supplémentaires, tels que le complément à deux.</span><span class="sxs-lookup"><span data-stu-id="15ea4-199">Translation of some additional bitwise operators, such as two's complement.</span></span>
* <span data-ttu-id="15ea4-200">Traduction de `FirstOrDefault` sur des chaînes.</span><span class="sxs-lookup"><span data-stu-id="15ea4-200">Translation of `FirstOrDefault` over strings.</span></span>
* <span data-ttu-id="15ea4-201">Amélioration de la traduction des requêtes autour de la sémantique null, ce qui se traduit par des requêtes plus fortes et plus efficaces.</span><span class="sxs-lookup"><span data-stu-id="15ea4-201">Improved query translation around null semantics, resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="15ea4-202">Les fonctions mappées par l’utilisateur peuvent désormais être annotées pour contrôler la propagation de la valeur null, ce qui se traduit par des requêtes plus strictes et plus efficaces.</span><span class="sxs-lookup"><span data-stu-id="15ea4-202">User-mapped functions can now be annotated to control null propagation, again resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="15ea4-203">SQL qui contient des blocs CASE est désormais beaucoup plus concis.</span><span class="sxs-lookup"><span data-stu-id="15ea4-203">SQL containing CASE blocks is now considerably more concise.</span></span>
* <span data-ttu-id="15ea4-204">La [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) fonction SQL Server peut désormais être appelée dans des requêtes via la nouvelle [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) méthode.</span><span class="sxs-lookup"><span data-stu-id="15ea4-204">The SQL Server [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) function can now be called in queries via the new [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) method.</span></span>
* <span data-ttu-id="15ea4-205">`EnableDetailedErrors` Ajoute [des détails supplémentaires aux exceptions](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).</span><span class="sxs-lookup"><span data-stu-id="15ea4-205">`EnableDetailedErrors` adds [additional details to exceptions](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).</span></span>

### <a name="saving"></a><span data-ttu-id="15ea4-206">Enregistrement</span><span class="sxs-lookup"><span data-stu-id="15ea4-206">Saving</span></span>

* <span data-ttu-id="15ea4-207">[Interception](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) d’SaveChanges et [événements](xref:core/logging-events-diagnostics/events).</span><span class="sxs-lookup"><span data-stu-id="15ea4-207">SaveChanges [interception](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) and [events](xref:core/logging-events-diagnostics/events).</span></span>
* <span data-ttu-id="15ea4-208">Des API ont été introduites pour le contrôle des points d’enregistrement de [transaction](xref:core/saving/transactions#savepoints).</span><span class="sxs-lookup"><span data-stu-id="15ea4-208">APIs have been introduced for controlling [transaction savepoints](xref:core/saving/transactions#savepoints).</span></span> <span data-ttu-id="15ea4-209">En outre, EF Core crée automatiquement un point de `SaveChanges` sauvegarde lorsque est appelé et une transaction est déjà en cours, puis le restaure en cas de défaillance.</span><span class="sxs-lookup"><span data-stu-id="15ea4-209">In addition, EF Core will automatically create a savepoint when `SaveChanges` is called and a transaction is already in progress, and roll back to it in case of failure.</span></span>
* <span data-ttu-id="15ea4-210">Un ID de transaction peut être défini explicitement par l’application, ce qui facilite la corrélation des événements de transaction dans la journalisation et ailleurs.</span><span class="sxs-lookup"><span data-stu-id="15ea4-210">A transaction ID can be explicitly set by the application, allowing for easier correlation of transaction events in logging and elsewhere.</span></span>
* <span data-ttu-id="15ea4-211">La taille de lot maximale par défaut pour SQL Server a été remplacée par 42 en fonction de l’analyse des performances de traitement par lot.</span><span class="sxs-lookup"><span data-stu-id="15ea4-211">The default maximum batch size for SQL Server has been changed to 42 based on an analysis of batching performance.</span></span>

### <a name="migrations-and-scaffolding"></a><span data-ttu-id="15ea4-212">Migrations et génération de modèles automatique</span><span class="sxs-lookup"><span data-stu-id="15ea4-212">Migrations and scaffolding</span></span>

* <span data-ttu-id="15ea4-213">Les tables peuvent maintenant être [exclues des migrations](xref:core/modeling/entity-types#excluding-from-migrations).</span><span class="sxs-lookup"><span data-stu-id="15ea4-213">Tables can now be [excluded from migrations](xref:core/modeling/entity-types#excluding-from-migrations).</span></span>
* <span data-ttu-id="15ea4-214">Une nouvelle [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) commande affiche maintenant les migrations qui n’ont pas encore été appliquées à la base de données ( [`Get-Migration`](xref:core/cli/powershell#get-migration) fait de même dans la console Package Management).</span><span class="sxs-lookup"><span data-stu-id="15ea4-214">A new [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) command now shows which migrations have not yet been applied to the database ([`Get-Migration`](xref:core/cli/powershell#get-migration) does the same in the Package Management Console).</span></span>
* <span data-ttu-id="15ea4-215">Les scripts de migration contiennent désormais des instructions de transaction, le cas échéant, afin d’améliorer les cas de figure où l’application de migration échoue.</span><span class="sxs-lookup"><span data-stu-id="15ea4-215">Migrations scripts now contain transaction statements where appropriate to improve handling cases where migration application fails.</span></span>
* <span data-ttu-id="15ea4-216">Les colonnes pour les classes de base non mappées sont désormais ordonnées après d’autres colonnes pour les types d’entités mappés.</span><span class="sxs-lookup"><span data-stu-id="15ea4-216">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="15ea4-217">Notez que cela affecte uniquement les tables nouvellement créées. l’ordre des colonnes pour les tables existantes reste inchangé.</span><span class="sxs-lookup"><span data-stu-id="15ea4-217">Note this only impacts newly created tables; the column order for existing tables remains unchanged.</span></span>
* <span data-ttu-id="15ea4-218">La génération de la migration peut désormais être consciente si la migration générée est idempotent et si la sortie est exécutée immédiatement ou générée en tant que script.</span><span class="sxs-lookup"><span data-stu-id="15ea4-218">Migration generation can now be aware if the migration being generated is idempotent, and whether the output will be executed immediately or generated as a script.</span></span>
* <span data-ttu-id="15ea4-219">De nouveaux paramètres de ligne de commande ont été ajoutés pour spécifier des espaces de noms dans les [migrations](xref:core/managing-schemas/migrations/index#namespaces) et la [génération de modèles](xref:core/managing-schemas/scaffolding#directories-and-namespaces)automatique.</span><span class="sxs-lookup"><span data-stu-id="15ea4-219">New command-line parameters have been added for specifying namespaces in [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [scaffolding](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span></span>
* <span data-ttu-id="15ea4-220">La commande [de mise à jour de la base de données dotnet EF](xref:core/cli/dotnet#dotnet-ef-database-update) accepte désormais un nouveau `--connection` paramètre pour spécifier la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="15ea4-220">The [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) command now accepts a new `--connection` parameter for specifying the connection string.</span></span>
* <span data-ttu-id="15ea4-221">La génération de modèles automatique des bases de données existantes singularise maintenant les noms de table, de sorte que les tables nommées `People` et `Addresses` seront automatiquement structurées en types d’entité appelés `Person` et `Address` .</span><span class="sxs-lookup"><span data-stu-id="15ea4-221">Scaffolding existing databases now singularizes table names, so tables named `People` and `Addresses` will be scaffolded to entity types called `Person` and `Address`.</span></span> <span data-ttu-id="15ea4-222">Les [noms de bases de données d’origine peuvent toujours être conservés](xref:core/managing-schemas/scaffolding#preserving-names).</span><span class="sxs-lookup"><span data-stu-id="15ea4-222">[Original database names can still be preserved](xref:core/managing-schemas/scaffolding#preserving-names).</span></span>
* <span data-ttu-id="15ea4-223">La nouvelle [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) option peut indiquer EF Core à exclure `OnModelConfiguring` lors de la génération de modèles automatique d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="15ea4-223">The new [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) option can instruct EF Core to exclude `OnModelConfiguring` when scaffolding a model.</span></span>

### <a name="cosmos"></a><span data-ttu-id="15ea4-224">Cosmos</span><span class="sxs-lookup"><span data-stu-id="15ea4-224">Cosmos</span></span>

* <span data-ttu-id="15ea4-225">Les [paramètres de connexion Cosmos](xref:core/providers/cosmos/index#cosmos-options) ont été développés.</span><span class="sxs-lookup"><span data-stu-id="15ea4-225">[Cosmos connection settings](xref:core/providers/cosmos/index#cosmos-options) have been expanded.</span></span>
* <span data-ttu-id="15ea4-226">L’accès concurrentiel optimiste est maintenant [pris en charge sur Cosmos via l’utilisation d’ETags](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).</span><span class="sxs-lookup"><span data-stu-id="15ea4-226">Optimistic concurrency is now [supported on Cosmos via the use of ETags](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).</span></span>
* <span data-ttu-id="15ea4-227">La nouvelle `WithPartitionKey` méthode permet d’inclure la [clé de partition](xref:core/providers/cosmos/index#partition-keys) Cosmos à la fois dans le modèle et dans les requêtes.</span><span class="sxs-lookup"><span data-stu-id="15ea4-227">The new `WithPartitionKey` method allows the Cosmos [partition key](xref:core/providers/cosmos/index#partition-keys) to be included both in the model and in queries.</span></span>
* <span data-ttu-id="15ea4-228">Les méthodes [`Contains`](/dotnet/api/system.string.contains) de chaîne [`StartsWith`](/dotnet/api/system.string.startswith) et [`EndsWith`](/dotnet/api/system.string.endswith) sont maintenant traduites pour Cosmos.</span><span class="sxs-lookup"><span data-stu-id="15ea4-228">The string methods [`Contains`](/dotnet/api/system.string.contains), [`StartsWith`](/dotnet/api/system.string.startswith) and [`EndsWith`](/dotnet/api/system.string.endswith) are now translated for Cosmos.</span></span>
* <span data-ttu-id="15ea4-229">L' `is` opérateur C# est maintenant traduit sur Cosmos.</span><span class="sxs-lookup"><span data-stu-id="15ea4-229">The C# `is` operator is now translated on Cosmos.</span></span>

### <a name="sqlite"></a><span data-ttu-id="15ea4-230">SQLite</span><span class="sxs-lookup"><span data-stu-id="15ea4-230">Sqlite</span></span>

* <span data-ttu-id="15ea4-231">Les colonnes calculées sont maintenant prises en charge.</span><span class="sxs-lookup"><span data-stu-id="15ea4-231">Computed columns are now supported.</span></span>
* <span data-ttu-id="15ea4-232">La récupération de données binaires et de chaîne avec GetBytes, GetChars et GetTextReader est désormais plus efficace grâce à l’utilisation de SqliteBlob et de flux.</span><span class="sxs-lookup"><span data-stu-id="15ea4-232">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="15ea4-233">L’initialisation de SqliteConnection est désormais différée.</span><span class="sxs-lookup"><span data-stu-id="15ea4-233">Initialization of SqliteConnection is now lazy.</span></span>

### <a name="other"></a><span data-ttu-id="15ea4-234">Autres</span><span class="sxs-lookup"><span data-stu-id="15ea4-234">Other</span></span>

* <span data-ttu-id="15ea4-235">Vous pouvez générer des proxys de suivi des modifications qui implémentent automatiquement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) et [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span><span class="sxs-lookup"><span data-stu-id="15ea4-235">Change-tracking proxies can be generated that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="15ea4-236">Cela constitue une autre approche pour le suivi des modifications qui n’analyse pas les modifications quand `SaveChanges` est appelé.</span><span class="sxs-lookup"><span data-stu-id="15ea4-236">This provides an alternative approach to change-tracking that doesn't scan for changes when `SaveChanges` is called.</span></span>
* <span data-ttu-id="15ea4-237">Une <xref:System.Data.Common.DbConnection> chaîne de connexion ou peut maintenant être modifiée sur un DbContext déjà initialisé.</span><span class="sxs-lookup"><span data-stu-id="15ea4-237">A <xref:System.Data.Common.DbConnection> or connection string can now be changed on an already-initialized DbContext.</span></span>
* <span data-ttu-id="15ea4-238">La nouvelle <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> méthode n ° 0 Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) efface le DbContext de toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="15ea4-238">The new <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.0#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) method clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="15ea4-239">Cela ne doit généralement pas être nécessaire lors de l’utilisation de la méthode recommandée pour créer une instance de contexte de courte durée pour chaque unité de travail.</span><span class="sxs-lookup"><span data-stu-id="15ea4-239">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="15ea4-240">Toutefois, s’il est nécessaire de réinitialiser l’état d’une instance de DbContext, l’utilisation de la nouvelle `Clear()` méthode est plus efficace et plus fiable que le détachement en masse de toutes les entités.</span><span class="sxs-lookup"><span data-stu-id="15ea4-240">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more efficient and robust than mass-detaching all entities.</span></span>
* <span data-ttu-id="15ea4-241">Les outils en ligne de commande EF Core configurent désormais automatiquement les `ASPNETCORE_ENVIRONMENT`  `DOTNET_ENVIRONMENT` variables d’environnement et en « développement ».</span><span class="sxs-lookup"><span data-stu-id="15ea4-241">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="15ea4-242">Cela permet d’utiliser l’hôte générique en fonction de l’expérience de ASP.NET Core pendant le développement.</span><span class="sxs-lookup"><span data-stu-id="15ea4-242">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span>
* <span data-ttu-id="15ea4-243">Les arguments de ligne de commande personnalisés peuvent être transmis dans <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601> , ce qui permet aux applications de contrôler la façon dont le contexte est créé et initialisé.</span><span class="sxs-lookup"><span data-stu-id="15ea4-243">Custom command-line arguments can be flowed into <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601>, allowing applications to control how the context is created and initialized.</span></span>
* <span data-ttu-id="15ea4-244">Le facteur de remplissage d’index peut désormais être [configuré sur SQL Server](xref:core/providers/sql-server/indexes#fill-factor).</span><span class="sxs-lookup"><span data-stu-id="15ea4-244">The index fill factor can now be [configured on SQL Server](xref:core/providers/sql-server/indexes#fill-factor).</span></span>
* <span data-ttu-id="15ea4-245">La nouvelle <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> propriété peut être utilisée pour distinguer les cas où vous utilisez un fournisseur relationnel et un fournisseur de non-relation (par exemple, InMemory).</span><span class="sxs-lookup"><span data-stu-id="15ea4-245">The new <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> property can be used to distinguish when using a relational provider and a non-relation provider (such as InMemory).</span></span>
