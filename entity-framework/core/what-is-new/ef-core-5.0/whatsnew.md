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
# <a name="whats-new-in-ef-core-50"></a>Nouveautés de EF Core 5,0

La liste suivante répertorie les principales nouvelles fonctionnalités de EF Core 5,0. Pour obtenir la liste complète des problèmes dans la version, consultez notre [suivi](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0)des problèmes.

En tant que version majeure, EF Core 5,0 contient également plusieurs [modifications avec rupture](xref:core/what-is-new/ef-core-5.0/breaking-changes), qui sont des améliorations d’API ou des changements de comportement qui peuvent avoir un impact négatif sur les applications existantes.

## <a name="many-to-many"></a>Plusieurs à plusieurs

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

EF Core 5,0 reconnaît qu’il s’agit d’une relation plusieurs-à-plusieurs par Convention, et crée automatiquement une `PostTag` table de jointure dans la base de données. Les données peuvent être interrogées et mises à jour sans référence explicite à la table de jointure, ce qui simplifie considérablement le code. La table de jointure peut encore être personnalisée et interrogée explicitement si nécessaire.

Pour plus d’informations, [consultez la documentation complète sur plusieurs à plusieurs](xref:core/modeling/relationships#many-to-many).

## <a name="split-queries"></a>Fractionner des requêtes

À compter de EF Core 3,0, EF Core génère toujours une requête SQL unique pour chaque requête LINQ. Cela garantit la cohérence des données retournées dans les contraintes du mode de transaction en cours d’utilisation. Toutefois, cela peut devenir très lent lorsque la requête utilise `Include` ou une projection pour rétablir plusieurs collections associées.

EF Core 5,0 autorise désormais une seule requête LINQ, y compris les collections associées, à fractionner en plusieurs requêtes SQL. Cela peut améliorer considérablement les performances, mais peut entraîner une incohérence dans les résultats retournés si les données changent entre les deux requêtes. Les transactions sérialisables ou d’instantané peuvent être utilisées pour atténuer ce problème et obtenir une cohérence avec les requêtes fractionnées, mais cela peut réduire les coûts de performances et la différence comportementale.

Par exemple, considérez une requête qui extrait deux niveaux de collections associées à l’aide de `Include` :

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

Par défaut, EF Core génère le code SQL suivant lorsque vous utilisez le fournisseur SQLite :

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

Avec les requêtes de fractionnement, le code SQL suivant est généré à la place :

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

Les requêtes Split peuvent être activées en plaçant le nouvel `AsSplitQuery` opérateur n’importe où dans votre requête LINQ, ou globalement dans le de votre modèle `OnConfiguring` . Pour plus d’informations, [consultez la documentation complète sur les requêtes de fractionnement](xref:core/querying/single-split-queries).

## <a name="simple-logging-and-improved-diagnostics"></a>Journalisation simple et diagnostics améliorés

EF Core 5,0 introduit une méthode simple pour configurer la journalisation via la nouvelle `LogTo` méthode. Les messages de journalisation seront écrits dans la console, y compris tous les éléments SQL générés par EF Core :

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

En outre, il est désormais possible d’appeler `ToQueryString` sur n’importe quelle requête LINQ, en récupérant le SQL que la requête exécuterait :

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

Enfin, différents types de EF Core ont été associés à une `DebugView` propriété améliorée qui fournit une vue détaillée des éléments internes. Par exemple, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> peut être consulté pour voir exactement les entités qui font l’objet d’un suivi dans un moment donné.

Pour plus d’informations, [consultez la documentation sur la journalisation et l’interception](xref:core/logging-events-diagnostics/index).

## <a name="filtered-include"></a>Include filtré

La `Include` méthode prend désormais en charge le filtrage des entités incluses :

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Cette requête renverra les blogs avec chaque publication associée, mais uniquement lorsque le titre de publication contient « fromage ».

Pour plus d’informations, [consultez la documentation complète sur les includes filtrées](xref:core/querying/related-data/eager#filtered-include).

## <a name="table-per-type-tpt-mapping"></a>Mappage table par type (TPT)

Par défaut, EF Core mappe une hiérarchie d’héritage de types .NET à une seule table de base de données. C’est ce que l’on appelle le mappage TPH (table par hiérarchie). EF Core 5,0 permet également de mapper chaque type .NET dans une hiérarchie d’héritage à une autre table de base de données. connu sous le nom de mappage table par type (TPT).

Par exemple, considérez ce modèle avec une hiérarchie mappée :

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

Avec TPT, une table de base de données est créée pour chaque type dans la hiérarchie :

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

Pour plus d’informations, [consultez la documentation complète sur TPT](xref:core/modeling/inheritance).

## <a name="flexible-entity-mapping"></a>Mappage d’entité flexible

Les types d’entités sont couramment mappés à des tables ou à des vues de telle sorte que EF Core récupère le contenu de la table ou de la vue lors de l’interrogation de ce type. EF Core 5,0 ajoute des options de mappage supplémentaires, où une entité peut être mappée à une requête SQL (appelée « définition de requête ») ou à une fonction table (TVF) :

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

Les fonctions table peuvent également être mappées à une méthode .NET plutôt qu’à un DbSet, ce qui permet de passer des paramètres ; le mappage peut être configuré avec <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A> .

Enfin, il est désormais possible de mapper une entité à une vue lors de l’interrogation (ou à une fonction ou à la définition d’une requête), mais à une table lors de la mise à jour :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a>Types d’entité et conteneurs de propriétés de type partagé

EF Core 5,0 permet au même type CLR d’être mappé à plusieurs types d’entité différents ; ces types sont appelés types d’entité de type Shared. Bien qu’un type CLR puisse être utilisé avec cette fonctionnalité, .NET `Dictionary` offre un cas d’utilisation particulièrement attrayant, que nous appelons « conteneurs de propriétés » :

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

Ces entités peuvent ensuite être interrogées et mises à jour de la même façon que les types d’entités normaux avec leur propre type CLR dédié. Vous trouverez plus d’informations dans la documentation sur les [conteneurs de propriétés](xref:core/modeling/shadow-properties).

## <a name="required-11-dependents"></a>Dépendants 1:1 requis

Dans EF Core 3,1, la terminaison dépendante d’une relation un-à-un était toujours considérée comme facultative. Cela était surtout le cas lors de l’utilisation d’entités détenues, car toutes les colonnes de l’entité appartenant ont été créées en tant que valeurs NULL dans la base de données, même si elles ont été configurées comme requises dans le modèle.

Dans EF Core 5,0, une navigation vers une entité détenue peut être configurée comme un dépendant obligatoire. Par exemple :

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

## <a name="dbcontextfactory"></a>DbContextFactory

EF Core 5,0 introduit `AddDbContextFactory` et `AddPooledDbContextFactory` pour inscrire une fabrique pour la création d’instances de DbContext dans le conteneur d’injection de dépendances (D.I.) de l’application. cela peut être utile lorsque le code d’application doit créer et supprimer manuellement des instances de contexte.

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

À ce stade, les services d’application tels que les contrôleurs de ASP.NET Core peuvent ensuite être injectés avec `IDbContextFactory<TContext>` et l’utiliser pour instancier des instances de contexte :

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

Pour plus d’informations, [consultez la documentation complète sur DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).

## <a name="sqlite-table-rebuilds"></a>Régénérations de la table SQLite

Par rapport aux autres bases de données, SQLite est relativement limité dans ses fonctionnalités de manipulation de schéma. par exemple, la suppression d’une colonne d’une table existante n’est pas prise en charge. EF Core 5,0 contournent ces limitations en créant automatiquement une nouvelle table, en copiant les données de l’ancienne table, en supprimant l’ancienne table et en renommant la nouvelle. Cette « reconstruit » la table et permet d’appliquer en toute sécurité les opérations de migration précédemment non prises en charge.

Pour plus d’informations sur les opérations de migration qui sont désormais prises en charge via les reconstructions de table, [consultez cette page de documentation](xref:core/providers/sqlite/limitations#migrations-limitations).

## <a name="database-collations"></a>Classements de base de données

EF Core 5,0 introduit la prise en charge de la spécification des classements de texte au niveau de la base de données, de la colonne ou de la requête. Cela permet de configurer le respect de la casse et d’autres aspects textuels de manière à ce qu’ils soient flexibles et ne compromettent pas les performances des requêtes.

Par exemple, le code suivant configure la `Name` colonne pour qu’elle respecte la casse sur SQL Server, et tous les index créés sur la colonne fonctionneront en conséquence :

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

Pour plus d’informations, [consultez la documentation complète sur les classements et le respect de la casse](xref:core/miscellaneous/collations-and-case-sensitivity).

## <a name="event-counters"></a>Compteurs d’événements

EF Core 5,0 expose des [compteurs d’événements](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) qui peuvent être utilisés pour suivre les performances de votre application et repérer différentes anomalies. Connectez-vous simplement à un processus exécutant EF avec l’outil [dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) :

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

Pour plus d’informations, [consultez la documentation complète sur les compteurs d’événements](xref:core/logging-events-diagnostics/event-counters).

## <a name="other-features"></a>Autres fonctionnalités

### <a name="model-building"></a>Génération de modèles

* Les API de création de modèle ont été introduites pour faciliter la configuration des [comparateurs de valeurs](xref:core/modeling/value-comparers).
* Les colonnes calculées peuvent désormais être configurées comme [ *stockées* ou *virtuelles*](xref:core/modeling/generated-properties#computed-columns).
* La précision et l’échelle peuvent désormais être configurées [par le biais de l’API Fluent](xref:core/modeling/entity-properties#precision-and-scale).
* De nouvelles API de création de modèle ont été introduites pour les [Propriétés de navigation](xref:core/modeling/relationships#configuring-navigation-properties).
* De nouvelles API de création de modèle ont été introduites pour les champs, comme les propriétés.
* Les types .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) et [IPAddress](/dotnet/api/system.net.ipaddress) peuvent désormais être mappés à des colonnes de chaîne de base de données.
* Un champ de stockage peut désormais être configuré par [le biais du nouvel `[BackingField]` attribut](xref:core/modeling/backing-field).
* Les champs de stockage Nullable sont désormais autorisés, ce qui offre une meilleure prise en charge des valeurs par défaut générées par le magasin, où la valeur par défaut du CLR n’est pas une bonne valeur de sentinelle (notable `bool` ).
* Un nouvel `[Index]` attribut peut être utilisé sur un type d’entité pour spécifier un index au lieu d’utiliser l’API Fluent.
* Un nouvel `[Keyless]` attribut peut être utilisé pour configurer un type d’entité [comme n’ayant aucune clé](xref:core/modeling/keyless-entity-types).
* Par défaut, [EF Core considère maintenant les discriminateurs comme *terminés*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), ce qui signifie qu’il s’attend à ne jamais voir les valeurs de discriminateur non configurées par l’application dans le modèle. Cela permet d’améliorer les performances et peut être désactivé si votre colonne de discriminateur peut contenir des valeurs inconnues.

### <a name="query"></a>Query

* Les exceptions d’échec de la traduction de requête contiennent désormais des raisons plus explicites concernant les raisons de l’échec, afin de vous aider à identifier le problème.
* Les requêtes sans suivi peuvent désormais effectuer la [résolution d’identité](xref:core/querying/tracking#identity-resolution), ce qui évite le renvoi de plusieurs instances d’entité pour le même objet de base de données.
* Ajout de la prise en charge de GroupBy avec des agrégats conditionnels (par exemple `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))` ,).
* Ajout de la prise en charge de la traduction de l’opérateur distinct sur les éléments de groupe avant Aggregate.
* Traduction de [`Reverse`](/dotnet/api/system.linq.queryable.reverse) .
* Traduction améliorée `DateTime` pour SQL Server (par exemple [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A) , [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A) ).
* Traduction de nouvelles méthodes sur des tableaux d’octets (par exemple [`Contains`](/dotnet/api/system.linq.enumerable.contains) ,, [`Length`](/dotnet/api/system.array.length) [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal) ).
* Traduction de certains opérateurs de bits supplémentaires, tels que le complément à deux.
* Traduction de `FirstOrDefault` sur des chaînes.
* Amélioration de la traduction des requêtes autour de la sémantique null, ce qui se traduit par des requêtes plus fortes et plus efficaces.
* Les fonctions mappées par l’utilisateur peuvent désormais être annotées pour contrôler la propagation de la valeur null, ce qui se traduit par des requêtes plus strictes et plus efficaces.
* SQL qui contient des blocs CASE est désormais beaucoup plus concis.
* La [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) fonction SQL Server peut désormais être appelée dans des requêtes via la nouvelle [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) méthode.
* `EnableDetailedErrors` Ajoute [des détails supplémentaires aux exceptions](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).

### <a name="saving"></a>Enregistrement

* [Interception](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) d’SaveChanges et [événements](xref:core/logging-events-diagnostics/events).
* Des API ont été introduites pour le contrôle des points d’enregistrement de [transaction](xref:core/saving/transactions#savepoints). En outre, EF Core crée automatiquement un point de `SaveChanges` sauvegarde lorsque est appelé et une transaction est déjà en cours, puis le restaure en cas de défaillance.
* Un ID de transaction peut être défini explicitement par l’application, ce qui facilite la corrélation des événements de transaction dans la journalisation et ailleurs.
* La taille de lot maximale par défaut pour SQL Server a été remplacée par 42 en fonction de l’analyse des performances de traitement par lot.

### <a name="migrations-and-scaffolding"></a>Migrations et génération de modèles automatique

* Les tables peuvent maintenant être [exclues des migrations](xref:core/modeling/entity-types#excluding-from-migrations).
* Une nouvelle [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) commande affiche maintenant les migrations qui n’ont pas encore été appliquées à la base de données ( [`Get-Migration`](xref:core/cli/powershell#get-migration) fait de même dans la console Package Management).
* Les scripts de migration contiennent désormais des instructions de transaction, le cas échéant, afin d’améliorer les cas de figure où l’application de migration échoue.
* Les colonnes pour les classes de base non mappées sont désormais ordonnées après d’autres colonnes pour les types d’entités mappés. Notez que cela affecte uniquement les tables nouvellement créées. l’ordre des colonnes pour les tables existantes reste inchangé.
* La génération de la migration peut désormais être consciente si la migration générée est idempotent et si la sortie est exécutée immédiatement ou générée en tant que script.
* De nouveaux paramètres de ligne de commande ont été ajoutés pour spécifier des espaces de noms dans les [migrations](xref:core/managing-schemas/migrations/index#namespaces) et la [génération de modèles](xref:core/managing-schemas/scaffolding#directories-and-namespaces)automatique.
* La commande [de mise à jour de la base de données dotnet EF](xref:core/cli/dotnet#dotnet-ef-database-update) accepte désormais un nouveau `--connection` paramètre pour spécifier la chaîne de connexion.
* La génération de modèles automatique des bases de données existantes singularise maintenant les noms de table, de sorte que les tables nommées `People` et `Addresses` seront automatiquement structurées en types d’entité appelés `Person` et `Address` . Les [noms de bases de données d’origine peuvent toujours être conservés](xref:core/managing-schemas/scaffolding#preserving-names).
* La nouvelle [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) option peut indiquer EF Core à exclure `OnModelConfiguring` lors de la génération de modèles automatique d’un modèle.

### <a name="cosmos"></a>Cosmos

* Les [paramètres de connexion Cosmos](xref:core/providers/cosmos/index#cosmos-options) ont été développés.
* L’accès concurrentiel optimiste est maintenant [pris en charge sur Cosmos via l’utilisation d’ETags](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).
* La nouvelle `WithPartitionKey` méthode permet d’inclure la [clé de partition](xref:core/providers/cosmos/index#partition-keys) Cosmos à la fois dans le modèle et dans les requêtes.
* Les méthodes [`Contains`](/dotnet/api/system.string.contains) de chaîne [`StartsWith`](/dotnet/api/system.string.startswith) et [`EndsWith`](/dotnet/api/system.string.endswith) sont maintenant traduites pour Cosmos.
* L' `is` opérateur C# est maintenant traduit sur Cosmos.

### <a name="sqlite"></a>SQLite

* Les colonnes calculées sont maintenant prises en charge.
* La récupération de données binaires et de chaîne avec GetBytes, GetChars et GetTextReader est désormais plus efficace grâce à l’utilisation de SqliteBlob et de flux.
* L’initialisation de SqliteConnection est désormais différée.

### <a name="other"></a>Autres

* Vous pouvez générer des proxys de suivi des modifications qui implémentent automatiquement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) et [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged). Cela constitue une autre approche pour le suivi des modifications qui n’analyse pas les modifications quand `SaveChanges` est appelé.
* Une <xref:System.Data.Common.DbConnection> chaîne de connexion ou peut maintenant être modifiée sur un DbContext déjà initialisé.
* La nouvelle <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> méthode n ° 0 Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) efface le DbContext de toutes les entités suivies. Cela ne doit généralement pas être nécessaire lors de l’utilisation de la méthode recommandée pour créer une instance de contexte de courte durée pour chaque unité de travail. Toutefois, s’il est nécessaire de réinitialiser l’état d’une instance de DbContext, l’utilisation de la nouvelle `Clear()` méthode est plus efficace et plus fiable que le détachement en masse de toutes les entités.
* Les outils en ligne de commande EF Core configurent désormais automatiquement les `ASPNETCORE_ENVIRONMENT`  `DOTNET_ENVIRONMENT` variables d’environnement et en « développement ». Cela permet d’utiliser l’hôte générique en fonction de l’expérience de ASP.NET Core pendant le développement.
* Les arguments de ligne de commande personnalisés peuvent être transmis dans <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601> , ce qui permet aux applications de contrôler la façon dont le contexte est créé et initialisé.
* Le facteur de remplissage d’index peut désormais être [configuré sur SQL Server](xref:core/providers/sql-server/indexes#fill-factor).
* La nouvelle <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> propriété peut être utilisée pour distinguer les cas où vous utilisez un fournisseur relationnel et un fournisseur de non-relation (par exemple, InMemory).
