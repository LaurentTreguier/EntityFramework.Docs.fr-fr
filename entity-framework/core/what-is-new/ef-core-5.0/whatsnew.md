---
title: Nouveautés de EF Core 5,0
description: Vue d’ensemble des nouvelles fonctionnalités de EF Core 5,0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 8fa45bf31cb5f1a7e35134f9513a40469719f8c2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065613"
---
# <a name="whats-new-in-ef-core-50"></a>Nouveautés de EF Core 5,0

Toutes les fonctionnalités prévues pour EF Core 5,0 sont maintenant terminées. Cette page contient une vue d’ensemble des changements intéressants introduits dans chaque version préliminaire.

Cette page ne duplique pas le [plan pour EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan). Le plan décrit les thèmes globaux pour EF Core 5,0, y compris tout ce que nous envisageons d’inclure avant d’expédier la version finale.

## <a name="rc1"></a>RC1

### <a name="many-to-many"></a>Plusieurs-à-plusieurs

EF Core 5,0 prend en charge les relations plusieurs-à-plusieurs sans mapper explicitement la table de jointure.

Par exemple, considérez les types d’entités suivants :

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

Notez que `Post` contient une collection de `Tags` et `Tag` contient une collection de `Posts` . EF Core 5,0 reconnaît qu’il s’agit d’une relation plusieurs-à-plusieurs par Convention. Cela signifie qu’aucun code n’est requis dans `OnModelCreating` :

```csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Blog> Blogs { get; set; }
}
```

Lorsque des migrations (ou `EnsureCreated` ) sont utilisées pour créer la base de données, EF Core crée automatiquement la table de jointure. Par exemple, sur SQL Server pour ce modèle, EF Core génère :

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

La création et `Blog` l’Association `Post` d’entités et les résultats des mises à jour de la table de jointure se produisent automatiquement. Par exemple :

```csharp
var beginnerTag = new Tag {Text = "Beginner"};
var advancedTag = new Tag {Text = "Advanced"};
var efCoreTag = new Tag {Text = "EF Core"};

context.AddRange(
    new Post {Name = "EF Core 101", Tags = new List<Tag> {beginnerTag, efCoreTag}},
    new Post {Name = "Writing an EF database provider", Tags = new List<Tag> {advancedTag, efCoreTag}},
    new Post {Name = "Savepoints in EF Core", Tags = new List<Tag> {beginnerTag, efCoreTag}});

context.SaveChanges();
```

Une fois les billets et les balises insérés, EF crée automatiquement des lignes dans la table de jointure. Par exemple, sur SQL Server :

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

Pour les requêtes, l’inclusion et d’autres opérations de requête fonctionnent comme pour toute autre relation. Par exemple :

```csharp
foreach (var post in context.Posts.Include(e => e.Tags))
{
    Console.Write($"Post \"{post.Name}\" has tags");

    foreach (var tag in post.Tags)
    {
        Console.Write($" '{tag.Text}'");
    }
}
```

Le SQL généré utilise automatiquement la table de jointure pour rétablir toutes les balises associées :

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

Contrairement à EF6, EF Core permet une personnalisation complète de la table de jointure. Par exemple, le code ci-dessous configure une relation plusieurs-à-plusieurs qui a également des navigations vers l’entité de jointure, et dans laquelle l’entité de jointure contient une propriété de charge utile :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Post>()
        .HasMany(p => p.Tags)
        .WithMany(p => p.Posts)
        .UsingEntity<PostTag>(
            j => j
                .HasOne(pt => pt.Tag)
                .WithMany()
                .HasForeignKey(pt => pt.TagId),
            j => j
                .HasOne(pt => pt.Post)
                .WithMany()
                .HasForeignKey(pt => pt.PostId),
            j =>
            {
                j.Property(pt => pt.PublicationDate).HasDefaultValueSql("CURRENT_TIMESTAMP");
                j.HasKey(t => new { t.PostId, t.TagId });
            });
}
```

### <a name="map-entity-types-to-queries"></a>Mapper les types d’entités aux requêtes

Les types d’entités sont couramment mappés à des tables ou à des vues de telle sorte que EF Core récupère le contenu de la table ou de la vue lors de l’interrogation de ce type. EF Core 5,0 permet à un type d’entité d’être mappé à une « définition de requête ». (Ceci était partiellement pris en charge dans les versions précédentes, mais a été considérablement amélioré et sa syntaxe est différente dans EF Core 5,0.)

Prenons l’exemple de deux tables : l’un avec les publications modernes ; l’autre avec les publications héritées. Le tableau des publications modernes contient des colonnes supplémentaires, mais pour les besoins de notre application, nous souhaitons que les publications moderne et héritée soient combinées et mappées à un type d’entité avec toutes les propriétés nécessaires :

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Category { get; set; }
    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

Dans EF Core 5,0, `ToSqlQuery` peut être utilisé pour mapper ce type d’entité à une requête qui extrait et combine des lignes des deux tables :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

Notez que la `legacy_posts` table n’a pas de `Category` colonne, donc nous synthétisons plutôt une valeur par défaut pour toutes les publications héritées.

Ce type d’entité peut ensuite être utilisé de manière normale pour les requêtes LINQ. Par exemple, requête LINQ :

```csharp
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

Génère le code SQL suivant sur SQLite :

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

Notez que la requête configurée pour le type d’entité est utilisée comme base pour composer la requête LINQ complète.

### <a name="event-counters"></a>Compteurs d’événements

Les [compteurs d’événements .net](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) permettent d’exposer efficacement les métriques de performances à partir d’une application. EF Core 5,0 comprend des compteurs d’événements sous la `Microsoft.EntityFrameworkCore` catégorie. Par exemple :

```
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

Cela indique aux compteurs dotnet de commencer à collecter des événements de EF Core pour le processus 49496. Cela génère une sortie similaire à celle-ci dans la console :

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

### <a name="property-bags"></a>Conteneurs de propriétés

EF Core 5,0 permet au même type CLR d’être mappé à plusieurs types d’entité différents. Ces types sont appelés types d’entité de type Shared. Cette fonctionnalité combinée aux propriétés d’indexeur (incluses dans Preview 1) permet d’utiliser des conteneurs de propriétés comme type d’entité.

Par exemple, le DbContext ci-dessous configure le type BCL `Dictionary<string, object>` en tant que type d’entité de type partagé pour les produits et les catégories.

```csharp
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

Les objets dictionary (« conteneurs de propriétés ») peuvent maintenant être ajoutés au contexte en tant qu’instances d’entité et enregistrés. Par exemple :

```csharp
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

Ces entités peuvent ensuite être interrogées et mises à jour de manière normale :

```csharp
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a>Interception d’SaveChanges et événements

EF Core 5,0 introduit les événements .NET et un intercepteur de EF Core déclenché lorsque SaveChanges est appelé.

Les événements sont simples à utiliser. par exemple :

```csharp
context.SavingChanges += (sender, args) =>
{
    Console.WriteLine($"Saving changes for {((DbContext)sender).Database.GetConnectionString()}");
};

context.SavedChanges += (sender, args) =>
{
    Console.WriteLine($"Saved {args.EntitiesSavedCount} changes for {((DbContext)sender).Database.GetConnectionString()}");
};
```

Notez que :
* L’expéditeur de l’événement est l' `DbContext` instance
* Les arguments de l' `SavedChanges` événement contiennent le nombre d’entités enregistrées dans la base de données

L’intercepteur est défini par `ISaveChangesInterceptor` , mais il est souvent convienient d’hériter de `SaveChangesInterceptor` pour éviter d’implémenter chaque méthode. Par exemple :

```csharp
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

Notez que :
* L’intercepteur a à la fois des méthodes synchrones et asynchrones. Cela peut être utile si vous devez effectuer des e/s asynchrones, telles que l’écriture sur un serveur d’audit.
* L’intercepteur permet d’ignorer SaveChanges à l’aide du `InterceptionResult` mécanisme commun à tous les intercepteurs.

L’inconvénient des intercepteurs est qu’ils doivent être inscrits sur le DbContext lorsqu’il est en cours de construction. Par exemple :

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .AddInterceptors(new MySaveChangesInterceptor())
        .UseSqlite("Data Source = test.db");
```

En revanche, les événements peuvent être enregistrés sur l’instance DbContext à tout moment.

### <a name="exclude-tables-from-migrations"></a>Exclure des tables des migrations

Il est parfois utile d’avoir un seul type d’entité mappé dans plusieurs DbContexts. Cela est particulièrement vrai lorsque vous utilisez des [contextes délimités](https://www.martinfowler.com/bliki/BoundedContext.html), pour lesquels il est courant d’avoir un type DbContext différent pour chaque contexte délimité.

Par exemple, un `User` type peut être nécessaire à la fois par un contexte d’autorisation et un contexte de création de rapports. Si une modification est apportée au `User` type, les migrations pour les deux DbContexts tentent de mettre à jour la base de données. Pour éviter cela, le modèle de l’un des contextes peut être configuré pour exclure la table de ses migrations.

Dans le code ci-dessous, le `AuthorizationContext` génère des migrations pour les modifications apportées à la `Users` table, mais ce n' `ReportingContext` est pas le cas, ce qui empêche les migrations d’entrer en conflit.

```csharp
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

### <a name="required-11-dependents"></a>Dépendants 1:1 requis

Dans EF Core 3,1, la terminaison dépendante d’une relation un-à-un était toujours considérée comme facultative. Cela était le plus évident lors de l’utilisation d’entités détenues. Par exemple, considérez le modèle et la configuration suivants :

```csharp
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

```csharp
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

Cela entraîne des migrations créant la table suivante pour SQLite :

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

Notez que toutes les colonnes acceptent la valeur null, même si certaines des `HomeAddress` propriétés ont été configurées selon les besoins. En outre, lors de l’interrogation d’un `Person` , si toutes les colonnes pour l’adresse personnelle ou professionnelle ont la valeur null, EF Core laisse les `HomeAddress` Propriétés and/or `WorkAddress` comme null, au lieu de définir une instance vide de `Address` .

Dans EF Core 5,0, la `HomeAddress` navigation peut désormais être configurée comme un dépendant obligatoire. Par exemple :

```csharp
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

La table créée par les migrations inclue désormais des colonnes non Nullable pour les propriétés requises du dépendant requis :

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

En outre, EF Core lève désormais une exception en cas de tentative d’enregistrement d’un propriétaire qui a un dépendant obligatoire null. Dans cet exemple, EF Core lève une exception quand vous tentez d’enregistrer un `Person` avec une valeur null `HomeAddress` .

Enfin, EF Core créera toujours une instance d’un dépendant obligatoire, même si toutes les colonnes de la dépendance requise ont des valeurs NULL.

### <a name="options-for-migration-generation"></a>Options de génération de la migration

EF Core 5,0 offre un contrôle accru de la génération des migrations à des fins différentes. Cela comprend la possibilité de :

* Savoir si la migration est en cours de génération pour un script ou pour une exécution immédiate
* Savoir si un script idempotent est en cours de génération
* Sachez si le script doit exclure les instructions de transaction (voir les _scripts de migration avec les transactions_ ci-dessous).

Ce comportement est spécifié par un `MigrationsSqlGenerationOptions` enum, qui peut désormais être passé à `IMigrator.GenerateScript` .

Ce travail inclut également une meilleure génération de scripts idempotent avec des appels à `EXEC` sur SQL Server lorsque cela est nécessaire. Ce travail permet également d’apporter des améliorations similaires aux scripts générés par d’autres fournisseurs de bases de données, notamment PostgreSQL.

### <a name="migrations-scripts-with-transactions"></a>Migration de scripts avec transactions

Les scripts SQL générés à partir des migrations contiennent désormais des instructions pour commencer et valider des transactions en fonction de la migration. Par exemple, le script de migration ci-dessous a été généré à partir de deux migrations. Notez que chaque migration est maintenant appliquée à l’intérieur d’une transaction.

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

Comme mentionné dans la section précédente, cette utilisation des transactions peut être désactivée si les transactions doivent être gérées différemment.

### <a name="see-pending-migrations"></a>Voir migrations en attente

Cette fonctionnalité a été ajoutée par la communauté [@Psypher9](https://github.com/Psypher9) . De nombreuses remerciements pour la contribution !

La `dotnet ef migrations list` commande affiche maintenant les migrations qui n’ont pas encore été appliquées à la base de données. Par exemple :

```
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

En outre, il existe désormais une `Get-Migration` commande pour la console du gestionnaire de package avec la même fonctionnalité.

### <a name="modelbuilder-api-for-value-comparers"></a>API ModelBuilder pour les comparateurs de valeurs

Les propriétés de EF Core pour les types mutables personnalisés [requièrent un comparateur de valeur](xref:core/modeling/value-comparers) pour que les modifications de propriété soient détectées correctement. Cela peut maintenant être spécifié dans le cadre de la configuration de la conversion de valeur pour le type. Par exemple :

```csharp
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

### <a name="entityentry-trygetvalue-methods"></a>EntityEntry, méthodes TryGetValue

Cette fonctionnalité a été ajoutée par la communauté [@m4ss1m0g](https://github.com/m4ss1m0g) . De nombreuses remerciements pour la contribution !

Une `TryGetValue` méthode a été ajoutée à `EntityEntry.CurrentValues` et `EntityEntry.OriginalValues` . Cela permet de demander la valeur d’une propriété sans vérifier d’abord si la propriété est mappée dans le modèle EF. Par exemple :

```csharp
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a>Taille de lot maximale par défaut pour SQL Server

À compter de EF Core 5,0, la taille de lot maximale par défaut pour SaveChanges sur SQL Server est désormais 42. Comme c’est bien connu, il s’agit également de la réponse à la question ultime de vie, de l’univers et de tout. Toutefois, il s’agit probablement d’une coïncidence, puisque la valeur a été obtenue par l' [analyse des performances de traitement par lot](https://github.com/dotnet/efcore/issues/9270). Nous ne pensons pas que nous avons découvert une forme de la question finale, bien qu’il semble quelque peu plausible que la terre a été créée pour comprendre pourquoi SQL Server fonctionne comme elle.

### <a name="default-environment-to-development"></a>Développement de l’environnement par défaut

Les outils en ligne de commande EF Core configurent désormais automatiquement les `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` variables d’environnement et en « développement ». Cela permet d’utiliser l’hôte générique en fonction de l’expérience de ASP.NET Core pendant le développement. Consultez [#19903](https://github.com/dotnet/efcore/issues/19903).

### <a name="better-migrations-column-ordering"></a>Meilleur classement des colonnes de migrations

Les colonnes pour les classes de base non mappées sont désormais ordonnées après d’autres colonnes pour les types d’entités mappés. Notez que cela affecte uniquement les tables nouvellement créées. L’ordre des colonnes pour les tables existantes reste inchangé. Consultez [#11314](https://github.com/dotnet/efcore/issues/11314).

### <a name="query-improvements"></a>Améliorations des requêtes

EF Core 5,0 RC1 contient des améliorations supplémentaires de la traduction des requêtes :

* Traduction de `is` sur Cosmos--voir [#16391](https://github.com/dotnet/efcore/issues/16391)
* Les fonctions mappées par l’utilisateur peuvent désormais être annotées pour contrôler la propagation de la valeur null, voir [#19609](https://github.com/dotnet/efcore/issues/19609)
* Prise en charge de la traduction de GroupBy avec des agrégats conditionnels--voir [#11711](https://github.com/dotnet/efcore/issues/11711)
* Traduction d’un opérateur distinct sur un élément Group avant Aggregate--voir [#17376](https://github.com/dotnet/efcore/issues/17376)

### <a name="model-building-for-fields"></a>Génération de modèles pour les champs

Enfin, pour RC1, EF Core autorise désormais l’utilisation des méthodes lambda dans ModelBuilder pour les champs, ainsi que pour les propriétés. Par exemple, si vous êtes réticent aux propriétés pour une raison quelconque et décidez d’utiliser des champs publics, ces champs peuvent maintenant être mappés à l’aide des générateurs lambda :

```csharp
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

```csharp
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

Bien que cela soit maintenant possible, nous ne vous recommandons pas de le faire. Notez également que cela n’ajoute pas de fonctionnalités de mappage de champs supplémentaires à EF Core, mais autorise uniquement l’utilisation des méthodes lambda au lieu d’exiger toujours les méthodes de chaîne. Cela est rarement utile dans la mesure où les champs sont rarement publics.

## <a name="preview-8"></a>Version préliminaire 8

### <a name="table-per-type-tpt-mapping"></a>Mappage table par type (TPT)

Par défaut, EF Core mappe une hiérarchie d’héritage de types .NET à une seule table de base de données. C’est ce que l’on appelle le mappage TPH (table par hiérarchie). EF Core 5,0 permet également de mapper chaque type .NET dans une hiérarchie d’héritage à une autre table de base de données. connu sous le nom de mappage table par type (TPT).

Par exemple, considérez ce modèle avec une hiérarchie mappée :

```csharp
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

Par défaut, EF Core mappe ce à une table unique :

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

Toutefois, le mappage de chaque type d’entité à une autre table génère à la place une table par type :

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

Notez que la création de contraintes de clé étrangère indiquées ci-dessus a été ajoutée après la création de branche du code pour Preview 8.

Les types d’entités peuvent être mappés à des tables différentes à l’aide des attributs de mappage :

```csharp
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

Ou à l’aide de la `ModelBuilder` Configuration :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

La documentation est suivie d’un problème [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).

### <a name="migrations-rebuild-sqlite-tables"></a>Migrations : reconstruire les tables SQLite

Par rapport à d’autres bases de données, SQLite est relativement limité dans ses fonctionnalités de manipulation de schéma. Par exemple, la suppression d’une colonne d’une table existante nécessite la suppression et la recréation de la table entière. EF Core migrations 5,0 prend désormais en charge la reconstruction automatique de la table pour les modifications de schéma qui l’exigent.

Par exemple, imaginez que nous disposons d’une `Unicorns` table créée pour un `Unicorn` type d’entité :

```csharp
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

Nous avons ensuite appris que le stockage de l’âge d’une licorne est considéré comme très impropre. nous allons donc supprimer cette propriété, ajouter une nouvelle migration et mettre à jour la base de données. Cette mise à jour échoue lors de l’utilisation de EF Core 3,1, car la colonne ne peut pas être supprimée. Dans EF Core 5,0, les migrations recréent à la place la table :

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

Notez que :
* Une table temporaire est créée avec le schéma souhaité pour la nouvelle table
* Les données sont copiées à partir de la table actuelle dans la table temporaire
* Mise hors tension de la clé étrangère
* La table actuelle est supprimée
* La table temporaire est renommée en une nouvelle table

La documentation est suivie d’un problème [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).

### <a name="table-valued-functions"></a>Fonctions table

Cette fonctionnalité a été ajoutée par la communauté [@pmiddleton](https://github.com/pmiddleton) . De nombreuses remerciements pour la contribution !

EF Core 5,0 offre une prise en charge de première classe pour le mappage des méthodes .NET aux fonctions table (TVF). Ces fonctions peuvent ensuite être utilisées dans les requêtes LINQ où la composition supplémentaire sur les résultats de la fonction sera également traduite en SQL.

Par exemple, considérez cette TVF définie dans une base de données SQL Server :

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

Le modèle de EF Core nécessite deux types d’entités pour utiliser cette TVF :
* `Employee`Type qui est mappé à la table Employees de manière normale
* `Report`Type qui correspond à la forme retournée par la fonction TVF

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```csharp
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

Ces types doivent être inclus dans le modèle de EF Core :

```csharp
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

Notez que n' `Report` a pas de clé primaire et doit donc être configuré en tant que tel.

Enfin, une méthode .NET doit être mappée à la TVF dans la base de données. Cette méthode peut être définie sur DbContext à l’aide de la nouvelle `FromExpression` méthode :

```csharp
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

Cette méthode utilise un paramètre et un type de retour qui correspondent à la fonction TVF définie ci-dessus. La méthode est ensuite ajoutée au modèle EF Core dans OnModelCreating :

```csharp
modelBuilder.HasDbFunction(() => GetReports(default));
```

(L’utilisation d’une expression lambda est un moyen simple de passer le `MethodInfo` à EF Core. Les arguments passés à la méthode sont ignorés.)

Nous pouvons maintenant écrire des requêtes qui appellent `GetReports` et composent les résultats. Par exemple :

```csharp
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

Sur SQL Server, cela se traduit par :

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

Notez que le SQL est enraciné dans la `Employees` table, appelle `GetReports` , puis ajoute une clause WHERE supplémentaire sur les résultats de la fonction.

### <a name="flexible-queryupdate-mapping"></a>Mappage de requête/mise à jour flexible

EF Core 5,0 permet de mapper le même type d’entité à des objets de base de données différents. Ces objets peuvent être des tables, des vues ou des fonctions.

Par exemple, un type d’entité peut être mappé à une vue de base de données et à une table de base de données :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

Par défaut, EF Core effectue une requête à partir de la vue et envoie des mises à jour à la table. Par exemple, l’exécution du code suivant :

```csharp
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

Génère une requête sur la vue, puis une mise à jour de la table :

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a>Configuration de la requête Split au niveau du contexte

Les requêtes de fractionnement (voir ci-dessous) peuvent désormais être configurées par défaut pour toute requête exécutée par DbContext. Cette configuration n’est disponible que pour les fournisseurs relationnels et doit donc être spécifiée dans le cadre de la `UseProvider` Configuration. Par exemple :

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

La documentation est suivie d’un problème [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).

### <a name="physicaladdress-mapping"></a>Mappage PhysicalAddress

Cette fonctionnalité a été ajoutée par la communauté [@ralmsdeveloper](https://github.com/ralmsdeveloper) . De nombreuses remerciements pour la contribution !

La classe .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) standard est désormais automatiquement mappée à une colonne de type chaîne pour les bases de données qui n’ont pas encore de prise en charge native. Pour plus d’informations, consultez les exemples `IPAddress` ci-dessous.

## <a name="preview-7"></a>Version préliminaire 7

### <a name="dbcontextfactory"></a>DbContextFactory

EF Core 5,0 introduit `AddDbContextFactory` et `AddPooledDbContextFactory` pour inscrire une fabrique pour la création d’instances de DbContext dans le conteneur d’injection de dépendances (D.I.) de l’application. Par exemple :

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

Les services d’application tels que les contrôleurs de ASP.NET Core peuvent ensuite dépendre `IDbContextFactory<TContext>` du constructeur de service. Par exemple :

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

Les instances de DbContext peuvent ensuite être créées et utilisées en fonction des besoins. Par exemple :

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

Notez que les instances de DbContext créées de cette façon _ne sont pas_ gérées par le fournisseur de services de l’application et doivent donc être supprimées par l’application. Ce découplage est très utile pour les applications éblouissantes, où l’utilisation de `IDbContextFactory` est recommandée, mais peut également être utile dans d’autres scénarios.

Les instances de DbContext peuvent être regroupées en appelant `AddPooledDbContextFactory` . Ce regroupement fonctionne de la même façon que pour `AddDbContextPool` et présente également les mêmes limitations.

La documentation est suivie d’un problème [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).

### <a name="reset-dbcontext-state"></a>Réinitialiser l’état de DbContext

EF Core 5,0 introduit `ChangeTracker.Clear()` , qui efface le DbContext de toutes les entités suivies. Cela ne doit généralement pas être nécessaire lors de l’utilisation de la méthode recommandée pour créer une instance de contexte de courte durée pour chaque unité de travail. Toutefois, s’il est nécessaire de réinitialiser l’état d’une instance de DbContext, l’utilisation de la nouvelle `Clear()` méthode est plus performante et fiable que le détachement en masse de toutes les entités.

La documentation est suivie d’un problème [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).

### <a name="new-pattern-for-store-generated-defaults"></a>Nouveau modèle pour les valeurs par défaut générées par le magasin

EF Core permet de définir une valeur explicite pour une colonne qui peut également avoir une contrainte de valeur par défaut. EF Core utilise la valeur CLR par défaut de type de propriété comme sentinelle pour ce. Si la valeur n’est pas la valeur CLR par défaut, elle est insérée, sinon la valeur par défaut de la base de données est utilisée.

Cela crée des problèmes pour les types où la valeur CLR par défaut n’est pas une bonne sentinelle, notamment les `bool` Propriétés. EF Core 5,0 autorise désormais la valeur null dans le champ de stockage pour les cas comme celui-ci. Par exemple :

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

Notez que le champ de stockage accepte la valeur null, mais pas la propriété exposée publiquement. Cela permet à la valeur de sentinelle de ne pas avoir `null` d’impact sur la surface publique du type d’entité. Dans ce cas, si le `IsValid` n’est jamais défini, la valeur par défaut de la base de données est utilisée, car le champ de stockage conserve la valeur null. Si `true` ou `false` sont définis, cette valeur est enregistrée explicitement dans la base de données.

La documentation est suivie d’un problème [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).

### <a name="cosmos-partition-keys"></a>Clés de partition Cosmos

EF Core permet d’inclure la clé de partition Cosmos dans le modèle EF. Par exemple :

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

À partir de Preview 7, la clé de partition est incluse dans le PK du type d’entité et est utilisée pour améliorer les performances dans certaines requêtes.

La documentation est suivie d’un problème [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).

### <a name="cosmos-configuration"></a>Configuration de Cosmos

EF Core 5,0 améliore la configuration des connexions Cosmos et Cosmos.

Auparavant, EF Core nécessitait la spécification du point de terminaison et de la clé explicitement lors de la connexion à une base de données Cosmos. EF Core 5,0 autorise l’utilisation d’une chaîne de connexion à la place. En outre, EF Core 5,0 permet de définir explicitement l’instance WebProxy. Par exemple :

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

De nombreuses autres valeurs de délai d’attente, limites, etc. peuvent également être configurées. Par exemple :

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

Enfin, le mode de connexion par défaut est désormais `ConnectionMode.Gateway` , ce qui est généralement plus compatible.

La documentation est suivie d’un problème [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).

### <a name="scaffold-dbcontext-now-singularizes"></a>Scaffold-DbContext maintenant singularise

Auparavant, lors de la génération de modèles automatique d’une base de données existante, EF Core créera des noms de types d’entité qui correspondent aux noms de table dans la base de données. Par exemple, les tables `People` et ont `Addresses` abouti à des types d’entités nommés `People` et `Addresses` .

Dans les versions précédentes, ce comportement était configurable par l’inscription d’un service de pluralisation. Désormais, dans EF Core 5,0, le package [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) est utilisé comme un service de pluralisation par défaut. Cela signifie que `People` les tables et `Addresses` sont maintenant rétroconçues dans les types d’entités nommés `Person` et `Address` .

### <a name="savepoints"></a>Points

EF Core prend désormais en charge les [points](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) de contrôle pour un plus grand contrôle sur les transactions qui exécutent plusieurs opérations.

Les points de sauvegarde peuvent être créés, libérés et restaurés manuellement. Par exemple :

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

En outre, les EF Core sont désormais restaurés jusqu’au dernier point de sauvegarde en cas d’échec de l’exécution `SaveChanges` . Cela permet de faire une nouvelle tentative d’SaveChanges sans retenter l’intégralité de la transaction.

La documentation est suivie d’un problème [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).

## <a name="preview-6"></a>Préversion 6

### <a name="split-queries-for-related-collections"></a>Fractionner les requêtes pour les regroupements associés

À compter de EF Core 3,0, EF Core génère toujours une requête SQL unique pour chaque requête LINQ. Cela garantit la cohérence des données retournées dans les contraintes du mode de transaction en cours d’utilisation. Toutefois, cela peut devenir très lent lorsque la requête utilise `Include` ou une projection pour rétablir plusieurs collections associées.

EF Core 5,0 autorise désormais une seule requête LINQ, y compris les collections associées, à fractionner en plusieurs requêtes SQL. Cela peut améliorer considérablement les performances, mais peut entraîner une incohérence dans les résultats retournés si les données changent entre les deux requêtes. Les transactions sérialisables ou d’instantané peuvent être utilisées pour atténuer ce problème et obtenir une cohérence avec les requêtes fractionnées, mais cela peut réduire les coûts de performances et la différence comportementale.

#### <a name="split-queries-with-include"></a>Fractionner des requêtes avec include

Par exemple, considérez une requête qui extrait deux niveaux de collections associées à l’aide de `Include` :

```csharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

Par défaut, EF Core génère le code SQL suivant lorsque vous utilisez le fournisseur SQLite :

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

La nouvelle `AsSplitQuery` API peut être utilisée pour modifier ce comportement. Par exemple :

```csharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

AsSplitQuery est disponible pour tous les fournisseurs de bases de données relationnelles et peut être utilisée n’importe où dans la requête, tout comme AsNoTracking. EF Core générera maintenant les trois requêtes SQL suivantes :

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

Toutes les opérations sur la racine de la requête sont prises en charge. Cela comprend les opérations OrderBy/Skip/Take, Join, FirstOrDefault et des résultats uniques de sélection.

Notez que les inclusions filtrées avec OrderBy/Skip/Take ne sont pas prises en charge dans Preview 6, mais sont disponibles dans les builds quotidiennes et seront incluses dans Preview 7.

#### <a name="split-queries-with-collection-projections"></a>Fractionner des requêtes avec des projections de collection

`AsSplitQuery` peut également être utilisé lorsque les collections sont chargées dans des projections. Par exemple :

```csharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

Cette requête LINQ génère les deux requêtes SQL suivantes lors de l’utilisation du fournisseur SQLite :

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

Notez que seule la matérialisation de la collection est prise en charge. Les compositions ultérieures `e.Albums` dans le cas ci-dessus n’aboutissent pas à une requête Split. Les améliorations apportées à cette zone sont suivies par [#21234](https://github.com/dotnet/efcore/issues/21234).

### <a name="indexattribute"></a>IndexAttribute

Le nouveau IndexAttribute peut être placé sur un type d’entité pour spécifier un index pour une colonne unique. Par exemple :

```csharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

Par SQL Server, les migrations génèrent alors le code SQL suivant :

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

IndexAttribute peut également être utilisé pour spécifier un index couvrant plusieurs colonnes. Par exemple :

```csharp
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

Pour SQL Server, les résultats sont les suivants :

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

La documentation est suivie d’un problème [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).

### <a name="improved-query-translation-exceptions"></a>Amélioration des exceptions de traduction des requêtes

Nous continuons d’améliorer les messages d’exception générés en cas d’échec de la traduction de la requête. Par exemple, cette requête utilise la propriété non mappée `IsSigned` :

```csharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core lèvera l’exception suivante indiquant que la conversion a échoué, car `IsSigned` n’est pas mappé :

> Exception non gérée. System. InvalidOperationException : expression LINQ’DbSet <Artist> (). Où (a => a. IsSigned) 'n’a pas pu être traduit. Informations supplémentaires : la traduction du membre « IsSigned » sur le type d’entité « Artist » a échoué. Éventuellement, le membre spécifié n’est pas mappé. Réécrivez la requête dans un formulaire qui peut être traduit ou basculez explicitement vers l’évaluation du client en insérant un appel à AsEnumerable (), AsAsyncEnumerable (), ToList () ou ToListAsync (). Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2101038 (éventuellement en anglais) pour plus d'informations.

De même, de meilleurs messages d’exception sont désormais générés lorsque vous tentez de traduire des comparaisons de chaînes avec la sémantique dépendante de la culture. Par exemple, cette requête tente d’utiliser `StringComparison.CurrentCulture` :

```csharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

EF Core lèvera à présent l’exception suivante :

> Exception non gérée. System. InvalidOperationException : expression LINQ’DbSet <Artist> (). Où (a => a. Name. Equals (valeur : « les licornes », comparisonType : CurrentCulture))» n’a pas pu être traduit. Informations supplémentaires : traduction de « String ». La méthode Equals qui accepte l’argument’StringComparison’n’est pas prise en charge. Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2129535 (éventuellement en anglais) pour plus d'informations. Réécrivez la requête dans un formulaire qui peut être traduit ou basculez explicitement vers l’évaluation du client en insérant un appel à AsEnumerable (), AsAsyncEnumerable (), ToList () ou ToListAsync (). Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2101038 (éventuellement en anglais) pour plus d'informations.

### <a name="specify-transaction-id"></a>Spécifier l’ID de la transaction

Cette fonctionnalité a été ajoutée par la communauté [@Marusyk](https://github.com/Marusyk) . De nombreuses remerciements pour la contribution !

EF Core expose un ID de transaction pour la corrélation des transactions entre les appels. Cet ID est généralement défini par EF Core lorsqu’une transaction est démarrée. Si l’application démarre plutôt la transaction, cette fonctionnalité permet à l’application de définir explicitement l’ID de transaction afin qu’elle soit correctement corrélée partout où elle est utilisée. Par exemple :

```csharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>Mappage IPAddress

Cette fonctionnalité a été ajoutée par la communauté [@ralmsdeveloper](https://github.com/ralmsdeveloper) . De nombreuses remerciements pour la contribution !

La classe .NET [IPAddress](/dotnet/api/system.net.ipaddress) standard est désormais automatiquement mappée à une colonne de type chaîne pour les bases de données qui n’ont pas encore de prise en charge native. Par exemple, envisagez de mapper ce type d’entité :

```csharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

Sur SQL Server, les migrations créent le tableau suivant :

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

Les entités peuvent ensuite être ajoutées de manière normale :

```csharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

Et le SQL qui en résulte insère l’adresse IPv4 ou IPv6 normalisée :

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a>Exclure OnConfiguring lors de la génération de modèles automatique

Lorsqu’un DbContext est généré à partir d’une base de données existante, EF Core par défaut crée une surcharge OnConfiguring avec une chaîne de connexion afin que le contexte soit immédiatement utilisable. Toutefois, cela n’est pas utile si vous avez déjà une classe partielle avec OnConfiguring, ou si vous configurez le contexte d’une autre façon.

Pour résoudre ce risque, les commandes de génération de modèles automatique peuvent maintenant être demandées d’omettre la génération de OnConfiguring. Par exemple :

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

Ou dans la console du gestionnaire de package :

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

Notez que nous vous recommandons [d’utiliser une chaîne de connexion nommée et un stockage sécurisé comme des secrets d’utilisateur](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).

### <a name="translations-for-firstordefault-on-strings"></a>Traductions pour FirstOrDefault sur les chaînes

Cette fonctionnalité a été ajoutée par la communauté [@dvoreckyaa](https://github.com/dvoreckyaa) . De nombreuses remerciements pour la contribution !

Les opérateurs FirstOrDefault et similaires pour les caractères dans les chaînes sont maintenant traduits. Par exemple, cette requête LINQ :

```csharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

Sera traduit dans le code SQL suivant lors de l’utilisation de SQL Server :

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a>Simplifier les blocs de casse

EF Core génère désormais de meilleures requêtes avec des blocs de cas. Par exemple, cette requête LINQ :

```csharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

Était sur SQL Server traduite formellement en :

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

Mais est maintenant traduit par :

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a>Préversion 5

### <a name="database-collations"></a>Classements de base de données

Le classement par défaut d’une base de données peut désormais être spécifié dans le modèle EF. Cela passera à des migrations générées pour définir le classement lors de la création de la base de données. Par exemple :

```csharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

Les migrations génèrent ensuite les éléments suivants pour créer la base de données sur SQL Server :

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

Le classement à utiliser pour des colonnes de base de données spécifiques peut également être spécifié. Par exemple :

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("German_PhoneBook_CI_AS");
```

Pour ceux qui n’utilisent pas de migrations, les classements sont à présent rétroconçus de la base de données lors de la génération de modèles automatique.

Enfin, le `EF.Functions.Collate()` autorise les requêtes ad hoc utilisant des classements différents. Par exemple :

```csharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

La requête suivante est générée pour SQL Server :

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

Notez que les classements ad hoc doivent être utilisés avec précaution, car ils peuvent avoir un impact négatif sur les performances de la base de données.

La documentation est suivie d’un problème [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a>Transmettre les arguments dans IDesignTimeDbContextFactory

Les arguments sont à présent transmis à partir de la ligne de commande dans la `CreateDbContext` méthode de [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1). Par exemple, pour indiquer qu’il s’agit d’une build dev, un argument personnalisé (par exemple, `dev` ) peut être passé sur la ligne de commande :

```
dotnet ef migrations add two --verbose --dev
```

Cet argument est ensuite transmis à la fabrique, où il peut être utilisé pour contrôler la façon dont le contexte est créé et initialisé. Par exemple :

```csharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

La documentation est suivie d’un problème [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).

### <a name="no-tracking-queries-with-identity-resolution"></a>Requêtes de suivi sans suivi de la résolution d’identité

Les requêtes de non-suivi peuvent maintenant être configurées pour effectuer la résolution d’identité. Par exemple, la requête suivante crée une nouvelle instance de blog pour chaque publication, même si chaque blog a la même clé primaire.

```csharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

Toutefois, au détriment d’un peu plus lentement et en utilisant toujours plus de mémoire, cette requête peut être modifiée pour garantir qu’une seule instance de blog est créée :

```csharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

Notez que cela n’est utile que pour les requêtes sans suivi, car toutes les requêtes de suivi présentent déjà ce comportement. En outre, la syntaxe de l’API suivante `PerformIdentityResolution` sera modifiée. Consultez [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).

La documentation est suivie d’un problème [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).

### <a name="stored-persisted-computed-columns"></a>Colonnes calculées (persistantes) stockées

La plupart des bases de données autorisent le stockage des valeurs de colonne calculées après le calcul. Bien que cela prenne de l’espace disque, la colonne calculée n’est calculée qu’une seule fois lors de la mise à jour, et non chaque fois que sa valeur est récupérée. Cela permet également d’indexer la colonne pour certaines bases de données.

EF Core 5,0 permet de configurer les colonnes calculées comme stockées. Par exemple :

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a>SQLite colonnes calculées

EF Core prend désormais en charge les colonnes calculées dans les bases de données SQLite.

## <a name="preview-4"></a>Preview 4

### <a name="configure-database-precisionscale-in-model"></a>Configurer la précision/l’échelle de la base de données dans le modèle

La précision et l’échelle d’une propriété peuvent désormais être spécifiées à l’aide du générateur de modèles. Par exemple :

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

La précision et l’échelle peuvent toujours être définies par le biais du type de base de données complet, par exemple « Decimal (16, 4) ».

La documentation est suivie d’un problème [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).

### <a name="specify-sql-server-index-fill-factor"></a>Spécifier SQL Server facteur de remplissage d’index

Le facteur de remplissage peut désormais être spécifié lors de la création d’un index sur SQL Server. Par exemple :

```csharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>Include filtré

La méthode Include prend désormais en charge le filtrage des entités incluses. Par exemple :

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Cette requête renverra les blogs avec chaque publication associée, mais uniquement lorsque le titre de publication contient « fromage ».

Skip et Take peuvent également être utilisés pour réduire le nombre d’entités incluses. Par exemple :

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
Cette requête renverra les blogs contenant au maximum cinq publications, incluses sur chaque blog.

Pour plus d’informations, consultez la [documentation include](xref:core/querying/related-data#filtered-include) .

### <a name="new-modelbuilder-api-for-navigation-properties"></a>Nouvelle API ModelBuilder pour les propriétés de navigation

Les propriétés de navigation sont principalement configurées lors de la [définition de relations](xref:core/modeling/relationships). Toutefois, la nouvelle `Navigation` méthode peut être utilisée dans les cas où les propriétés de navigation nécessitent une configuration supplémentaire. Par exemple, pour définir un champ de stockage pour la navigation lorsque le champ est introuvable par Convention :

```csharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

Notez que l' `Navigation` API ne remplace pas la configuration de la relation. Au lieu de cela, il permet une configuration supplémentaire des propriétés de navigation dans les relations déjà découvertes ou définies.

Consultez la [documentation](xref:core/modeling/relationships#configuring-navigation-properties)sur la configuration des propriétés de navigation.

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>Nouveaux paramètres de ligne de commande pour les espaces de noms et les chaînes de connexion

Les migrations et la génération de modèles automatique permettent désormais de spécifier des espaces de noms sur la ligne de commande. Par exemple, pour rétroconcevoir une base de données en plaçant le contexte et les classes de modèle dans différents espaces de noms :

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

Pour plus d’informations, consultez la documentation sur les [migrations](xref:core/managing-schemas/migrations/index#namespaces) et l' [ingénierie à rebours](xref:core/managing-schemas/scaffolding#directories-and-namespaces) .

---
En outre, une chaîne de connexion peut désormais être passée à la `database-update` commande :

```dotnetcli
dotnet ef database update --connection "connection string"
```

Pour plus d’informations, consultez la documentation sur les [Outils](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .

Des paramètres équivalents ont également été ajoutés aux commandes PowerShell utilisées dans la console du gestionnaire de package VS.

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors a retourné

Pour des raisons de performances, EF n’effectue pas de vérifications null supplémentaires lors de la lecture des valeurs de la base de données. Cela peut entraîner des exceptions qui sont difficiles à la cause première lorsqu’une valeur Null inattendue est rencontrée.

L’utilisation de `EnableDetailedErrors` permet d’ajouter une vérification de valeur null supplémentaire aux requêtes de sorte que, pour une faible surcharge de performances, ces erreurs sont plus faciles à suivre à une cause racine.

Par exemple :

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

La documentation est suivie d’un problème [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).

### <a name="cosmos-partition-keys"></a>Clés de partition Cosmos

La clé de partition à utiliser pour une requête donnée peut désormais être spécifiée dans la requête. Par exemple :

```csharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

La documentation est suivie d’un problème [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).

### <a name="support-for-the-sql-server-datalength-function"></a>Prise en charge de la fonction DATALENGTH SQL Server

Vous pouvez y accéder à l’aide de la nouvelle `EF.Functions.DataLength` méthode. Par exemple :

```csharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a>Préversion 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>Utiliser un attribut C# pour spécifier un champ de stockage de propriété

Un attribut C# peut maintenant être utilisé pour spécifier le champ de stockage d’une propriété. Cet attribut permet à EF Core d’écrire et de lire toujours dans le champ de stockage, ce qui se produit normalement, même si le champ de stockage ne peut pas être trouvé automatiquement. Par exemple :

```csharp
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

La documentation est suivie d’un problème [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).

### <a name="complete-discriminator-mapping"></a>Terminer le mappage de discriminateur

EF Core utilise une colonne de discriminateur pour le [mappage TPH d’une hiérarchie d’héritage](xref:core/modeling/inheritance). Certaines améliorations des performances sont possibles tant que EF Core connaît toutes les valeurs possibles pour le discriminateur. EF Core 5,0 implémente désormais ces améliorations.

Par exemple, les versions précédentes de EF Core génèrent toujours ce SQL pour une requête qui retourne tous les types dans une hiérarchie :

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

EF Core 5,0 génère désormais les éléments suivants lorsqu’un mappage de discriminateur complet est configuré :

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

Il s’agit du comportement par défaut à partir de Preview 3.

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Améliorations des performances dans Microsoft. Data. sqlite

Nous avons apporté deux améliorations des performances pour SQLIte :

* La récupération de données binaires et de chaîne avec GetBytes, GetChars et GetTextReader est désormais plus efficace grâce à l’utilisation de SqliteBlob et de flux.
* L’initialisation de SqliteConnection est désormais différée.

Ces améliorations se trouvent dans le fournisseur Microsoft. Data. sqlite de ADO.NET et améliorent donc également les performances en dehors de EF Core.

## <a name="preview-1"></a>Preview 1

### <a name="simple-logging"></a>Journalisation simple

Cette fonctionnalité ajoute des fonctionnalités similaires à `Database.Log` dans EF6. Autrement dit, il offre un moyen simple d’obtenir des journaux à partir de EF Core sans avoir besoin de configurer tout type d’infrastructure de journalisation externe.

La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

Une documentation supplémentaire est suivie par le [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)du problème.

### <a name="simple-way-to-get-generated-sql"></a>Moyen simple d’utiliser le SQL généré

EF Core 5,0 introduit la `ToQueryString` méthode d’extension, qui retourne le SQL que EF Core générera lors de l’exécution d’une requête LINQ.

La documentation préliminaire est incluse dans l' [État EF Weekly pour le 9 janvier 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).

Une documentation supplémentaire est suivie par le [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)du problème.

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>Utiliser un attribut C# pour indiquer qu’une entité n’a pas de clé

Un type d’entité peut désormais être configuré comme n’ayant aucune clé à l’aide de la nouvelle `KeylessAttribute` . Par exemple :

```csharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

La documentation est suivie d’un problème [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>La connexion ou la chaîne de connexion peut être modifiée lors de l’DbContext initialisé

Il est désormais plus facile de créer une instance DbContext sans connexion ou chaîne de connexion. En outre, la connexion ou la chaîne de connexion peut désormais être mutée sur l’instance de contexte. Cette fonctionnalité permet à la même instance de contexte de se connecter de manière dynamique à différentes bases de données.

La documentation est suivie d’un problème [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).

### <a name="change-tracking-proxies"></a>Proxys de suivi des modifications

EF Core pouvez désormais générer des proxies d’exécution qui implémentent automatiquement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) et [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged). Celles-ci rerapportent ensuite les modifications de valeur sur les propriétés d’entité directement à EF Core, évitant ainsi la nécessité d’analyser les modifications. Toutefois, les proxies sont fournis avec leur propre ensemble de limitations, donc ils ne le sont pas pour tout le monde.

La documentation est suivie d’un problème [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).

### <a name="enhanced-debug-views"></a>Vues de débogage améliorées

Les vues de débogage sont un moyen simple de consulter les éléments internes d’EF Core lors du débogage des problèmes. Une vue de débogage a été implémentée pour le modèle il y a quelque temps. Pour EF Core 5,0, nous avons facilité la lecture et l’ajout d’une nouvelle vue de débogage pour les entités suivies dans le gestionnaire d’État.

La documentation préliminaire est incluse dans l' [État EF Weekly pour le 12 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).

Une documentation supplémentaire est suivie par le [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)du problème.

### <a name="improved-handling-of-database-null-semantics"></a>Amélioration de la gestion de la sémantique null de la base de données

En général, les bases de données relationnelles traitent la valeur NULL comme une valeur inconnue et, par conséquent, n’est pas égale à une autre valeur NULL. Alors que C# traite NULL comme une valeur définie, qui compare la valeur à toute autre valeur null. EF Core par défaut traduit les requêtes afin qu’elles utilisent la sémantique null C#. EF Core 5,0 améliore considérablement l’efficacité de ces traductions.

La documentation est suivie d’un problème [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).

### <a name="indexer-properties"></a>Propriétés de l’indexeur

EF Core 5,0 prend en charge le mappage des propriétés de l’indexeur C#. Ces propriétés permettent aux entités d’agir en tant que conteneurs de propriétés où les colonnes sont mappées à des propriétés nommées dans le conteneur.

La documentation est suivie d’un problème [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).

### <a name="generation-of-check-constraints-for-enum-mappings"></a>Génération de contraintes de validation pour les mappages d’énumération

EF Core migrations 5,0 peut désormais générer des contraintes de validation pour les mappages de propriété d’énumération. Par exemple :

```sql
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

La documentation est suivie d’un problème [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).

### <a name="isrelational"></a>IsRelational

Une nouvelle `IsRelational` méthode a été ajoutée en plus des `IsSqlServer` , `IsSqlite` , et existants `IsInMemory` . Cette méthode peut être utilisée pour tester si DbContext utilise un fournisseur de base de données relationnelle. Par exemple :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

La documentation est suivie d’un problème [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).

### <a name="cosmos-optimistic-concurrency-with-etags"></a>Accès concurrentiel optimiste Cosmos avec ETags

Le fournisseur de base de données Azure Cosmos DB prend désormais en charge l’accès concurrentiel optimiste à l’aide d’ETags. Utilisez le générateur de modèles de OnModelCreating pour configurer un ETag :

```csharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

SaveChanges lèvera ensuite un en cas de `DbUpdateConcurrencyException` conflit d’accès concurrentiel, qui [peut être géré](xref:core/saving/concurrency) pour implémenter les nouvelles tentatives, etc.

La documentation est suivie d’un problème [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).

### <a name="query-translations-for-more-datetime-constructs"></a>Traductions de requêtes pour d’autres constructions DateTime

Les requêtes contenant la nouvelle construction DateTime sont maintenant traduites.

En outre, les fonctions de SQL Server suivantes sont maintenant mappées :

* DateDiffWeek
* DateFromParts

Par exemple :

```csharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translations-for-more-byte-array-constructs"></a>Traductions de requêtes pour d’autres constructions de tableau d’octets

Les requêtes utilisant les propriétés Contains, Length, SequenceEqual, etc. sur Byte [] sont désormais traduites en SQL.

La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

Une documentation supplémentaire est suivie par le [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)du problème.

### <a name="query-translation-for-reverse"></a>Traduction de requête pour l’inverse

Les requêtes utilisant `Reverse` sont désormais traduites. Par exemple :

```csharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-bitwise-operators"></a>Traduction des requêtes pour les opérateurs au niveau du bit

Les requêtes utilisant des opérateurs de bits sont maintenant traduites dans d’autres cas, par exemple :

```csharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-strings-on-cosmos"></a>Traduction des requêtes pour les chaînes sur Cosmos

Les requêtes qui utilisent les méthodes de chaîne Contains, StartsWith et EndsWith sont désormais traduites lors de l’utilisation du fournisseur Azure Cosmos DB.

La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).
