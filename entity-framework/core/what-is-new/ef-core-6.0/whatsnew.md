---
title: Nouveautés de EF Core 6,0
description: Vue d’ensemble des nouvelles fonctionnalités de EF Core 6,0
author: ajcvickers
ms.date: 03/08/2021
uid: core/what-is-new/ef-core-6.0/whatsnew
ms.openlocfilehash: 15ab49f60d8831c60e599d8c06b3700aace74bda
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023521"
---
# <a name="whats-new-in-ef-core-60"></a>Nouveautés de EF Core 6,0

EF Core 6,0 est actuellement en cours de développement. Elle contient une vue d’ensemble des modifications intéressantes introduites dans chaque aperçu.

Cette page ne duplique pas le [plan pour EF Core 6,0](xref:core/what-is-new/ef-core-6.0/plan). Le plan décrit les thèmes globaux pour EF Core 6,0, y compris tout ce que nous envisageons d’inclure avant d’expédier la version finale.

> [!TIP]
> Vous pouvez exécuter et déboguer dans tous les exemples Preview 1 ci-dessous en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).

## <a name="ef-core-60-preview-2"></a>EF Core 6,0 Preview 2

### <a name="preserve-synchronization-context-in-savechangesasync"></a>Conserver le contexte de synchronisation dans SaveChangesAsync

GitHub problème : [#23971](https://github.com/dotnet/efcore/issues/23971).

Nous avons [modifié le code EF Core dans la version 5,0](https://github.com/dotnet/efcore/issues/10164) afin de lui affecter la valeur <xref:System.Threading.Tasks.Task.ConfigureAwait%2A?displayProperty=nameWithType> à `false` tous les endroits où nous avons du `await` code asynchrone. Il s’agit généralement d’un meilleur choix pour l’utilisation de EF Core. Toutefois, <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> est un cas particulier, car EF Core définit les valeurs générées dans les entités suivies une fois l’opération de base de données asynchrone terminée. Ces modifications peuvent ensuite déclencher des notifications qui, par exemple, doivent s’exécuter sur le U.I. thread. Par conséquent, nous rétablissons cette modification dans EF Core 6,0 pour la <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> méthode uniquement.

### <a name="translate-stringconcat-with-multiple-arguments"></a>Traduire String. Concat avec plusieurs arguments

GitHub problème : [#23859](https://github.com/dotnet/efcore/issues/23859). Cette fonctionnalité a été fournie par [@wmeints](https://github.com/wmeints) .

À compter de EF Core 6,0, les appels à <xref:System.String.Concat%2A?displayProperty=nameWithType> avec plusieurs arguments sont désormais traduits en SQL. Par exemple, la requête suivante :

<!--
        var shards = context.Shards
            .Where(e => string.Concat(e.Token1, e.Token2, e.Token3) != e.TokensProcessed).ToList();
-->
[!code-csharp[StringConcat](../../../../samples/core/Miscellaneous/NewInEFCore6/StringConcatSample.cs?name=StringConcat)]

Sera traduit dans le code SQL suivant lors de l’utilisation de SQL Server :

```sql
SELECT [s].[Id], [s].[Token1], [s].[Token2], [s].[Token3], [s].[TokensProcessed]
FROM [Shards] AS [s]
WHERE ((COALESCE([s].[Token1], N'') + (COALESCE([s].[Token2], N'') + COALESCE([s].[Token3], N''))) <> [s].[TokensProcessed]) OR [s].[TokensProcessed] IS NULL
```

### <a name="smoother-integration-with-systemlinqasync"></a>Intégration plus lisse avec System. Linq. Async

GitHub problème : [#24041](https://github.com/dotnet/efcore/issues/24041).

Le package [System. Linq. Async](https://www.nuget.org/packages/System.Linq.Async/) ajoute un traitement LINQ asynchrone côté client. L’utilisation de ce package avec les versions précédentes de EF Core était lourde en raison d’un conflit d’espace de noms pour les méthodes LINQ asynchrones. Dans EF Core 6,0, nous avons tiré parti de la mise en correspondance des modèles C# pour <xref:System.Collections.Generic.IAsyncEnumerable%601> que la EF Core exposée <xref:Microsoft.EntityFrameworkCore.DbSet%601> n’ait pas besoin d’implémenter l’interface directement.

Notez que la plupart des applications n’ont pas besoin d’utiliser System. Linq. Async, car EF Core requêtes sont généralement entièrement traduites sur le serveur.

### <a name="more-flexible-free-text-search"></a>Recherche de texte libre plus flexible

GitHub problème : [#23921](https://github.com/dotnet/efcore/issues/23921).

Dans EF Core 6,0, nous avons assoupli les exigences de paramètres pour <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.FreeText(Microsoft.EntityFrameworkCore.DbFunctions,System.String,System.String)> et <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.Contains%2A> . Cela permet d’utiliser ces fonctions avec des colonnes binaires, ou avec des colonnes mappées à l’aide d’un convertisseur de valeur. Par exemple, considérez un type d’entité avec une `Name` propriété définie en tant qu’objet de valeur :

<!--
    public class Customer
    {
        public int Id { get; set; }

        public Name Name{ get; set; }
    }

    public class Name
    {
        public string First { get; set; }
        public string MiddleInitial { get; set; }
        public string Last { get; set; }
    }
-->
[!code-csharp[EntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=EntityType)]

Cette valeur est mappée au format JSON dans la base de données :

<!--
            modelBuilder.Entity<Customer>()
                .Property(e => e.Name)
                .HasConversion(
                    v => JsonSerializer.Serialize(v, null),
                    v => JsonSerializer.Deserialize<Name>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=ConfigureCompositeValueObject)]

Une requête peut maintenant être exécutée à l’aide `Contains` de ou `FreeText` même si le type de la propriété n’est `Name` pas `string` . Par exemple :

<!--
        var result = context.Customers.Where(e => EF.Functions.Contains(e.Name, "Martin")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=Query)]

Cela génère le code SQL suivant, lors de l’utilisation de SQL Server :

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE CONTAINS([c].[Name], N'Martin')
```

## <a name="ef-core-60-preview-1"></a>EF Core 6,0 Preview 1

### <a name="unicodeattribute"></a>UnicodeAttribute

GitHub problème : [#19794](https://github.com/dotnet/efcore/issues/19794). Cette fonctionnalité a été fournie par [@RaymondHuy](https://github.com/RaymondHuy) .

À compter de EF Core 6,0, une propriété de type chaîne peut désormais être mappée à une colonne non-Unicode à l’aide d’un attribut _de mappage sans spécifier directement le type de la base de données_. Par exemple, considérez un `Book` type d’entité avec une propriété pour le [numéro ISBN (International Standard Book Number)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) au format « ISBN 978-3-16-148410-0 » :

<!--
    public class Book
    {
        public int Id { get; set; }
        public string Title { get; set; }

        [Unicode(false)]
        [MaxLength(22)]
        public string Isbn { get; set; }
    }
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/UnicodeAttributeSample.cs?name=BookEntityType)]

Étant donné que les ISBNs ne peuvent pas contenir de caractères non-Unicode, l' `Unicode` attribut entraîne l’utilisation d’un type de chaîne non-Unicode. En outre, `MaxLength` est utilisé pour limiter la taille de la colonne de base de données. Par exemple, lors de l’utilisation de SQL Server, cela aboutit à une colonne de base de données `varchar(22)` :

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> EF Core mappe les propriétés de chaîne aux colonnes Unicode par défaut. `UnicodeAttribute` est ignoré lorsque le système de base de données prend en charge uniquement les types Unicode.

### <a name="precisionattribute"></a>PrecisionAttribute

GitHub problème : [#17914](https://github.com/dotnet/efcore/issues/17914). Cette fonctionnalité a été fournie par [@RaymondHuy](https://github.com/RaymondHuy) .

La précision et l’échelle d’une colonne de base de données peuvent désormais être configurées à l’aide d’attributs de mappage _sans spécifier directement le type de base de données_. Par exemple, considérez un `Product` type d’entité avec une propriété décimale `Price` :

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

EF Core mappera cette propriété à une colonne de base de données avec une précision de 10 et une échelle de 2. Par exemple, sur SQL Server :

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a>EntityTypeConfigurationAttribute

GitHub problème : [#23163](https://github.com/dotnet/efcore/issues/23163). Cette fonctionnalité a été fournie par [@KaloyanIT](https://github.com/KaloyanIT) .

<xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> les instances autorisent <xref:Microsoft.EntityFrameworkCore.ModelBuilder> la configuration de chaque type d’entité dans sa propre classe de configuration. Par exemple :

<!--
public class BookConfiguration : IEntityTypeConfiguration<Book>
{
    public void Configure(EntityTypeBuilder<Book> builder)
    {
        builder
            .Property(e => e.Isbn)
            .IsUnicode(false)
            .HasMaxLength(22);
    }
}
-->
[!code-csharp[BookConfiguration](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookConfiguration)]

Normalement, cette classe de configuration doit être instanciée et appelée dans à partir de <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> . Par exemple :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

À partir de EF Core 6,0, un `EntityTypeConfigurationAttribute` peut être placé sur le type d’entité afin que EF Core puisse trouver et utiliser la configuration appropriée. Par exemple :

<!--
[EntityTypeConfiguration(typeof(BookConfiguration))]
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
}
-->
[!code-csharp[BookEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=BookEntityType)]

Cet attribut signifie que EF Core utilise l’implémentation spécifiée `IEntityTypeConfiguration` chaque fois que le `Book` type d’entité est inclus dans un modèle. Le type d’entité est inclus dans un modèle à l’aide de l’un des mécanismes normaux. Par exemple, en créant une <xref:Microsoft.EntityFrameworkCore.DbSet%601> propriété pour le type d’entité :

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

Ou en l’inscrivant dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> `EntityTypeConfigurationAttribute` les types ne seront pas détectés automatiquement dans un assembly. Les types d’entités doivent être ajoutés au modèle avant que l’attribut ne soit découvert sur ce type d’entité.

### <a name="translate-tostring-on-sqlite"></a>Traduire ToString sur SQLite

GitHub problème : [#17223](https://github.com/dotnet/efcore/issues/17223). Cette fonctionnalité a été fournie par [@ralmsdeveloper](https://github.com/ralmsdeveloper) .

Les appels à <xref:System.Object.ToString> sont désormais traduits en SQL lors de l’utilisation du fournisseur de base de données SQLite. Cela peut être utile pour les recherches de texte impliquant des colonnes qui ne sont pas des chaînes. Par exemple, considérez un `User` type d’entité qui stocke les numéros de téléphone comme valeurs numériques :

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

`ToString` peut être utilisé pour convertir le nombre en une chaîne dans la base de données. Nous pouvons ensuite utiliser cette chaîne avec une fonction telle que `LIKE` pour rechercher des nombres qui correspondent à un modèle. Par exemple, pour rechercher tous les nombres contenant 555 :

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

Cela se traduit par le code SQL suivant lors de l’utilisation d’une base de données SQLite :

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

Notez que la traduction de <xref:System.Object.ToString> pour SQL Server est déjà prise en charge dans EF Core 5,0 et peut également être prise en charge par d’autres fournisseurs de bases de données.

### <a name="effunctionsrandom"></a>EF. Functions. Random

GitHub problème : [#16141](https://github.com/dotnet/efcore/issues/16141). Cette fonctionnalité a été fournie par [@RaymondHuy](https://github.com/RaymondHuy) .

`EF.Functions.Random` mappe à une fonction de base de données qui retourne un nombre Pseudo-aléatoire compris entre 0 et 1 exclusif. Les traductions ont été implémentées dans le EF Core référentiel pour SQL Server, SQLite et Cosmos. Par exemple, considérez un `User` type d’entité avec une `Popularity` propriété :

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

`Popularity` peut avoir des valeurs comprises entre 1 et 5 inclus. À l’aide de, `EF.Functions.Random` nous pouvons écrire une requête pour renvoyer tous les utilisateurs avec une popularité choisie de manière aléatoire :

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

Cela se traduit par le code SQL suivant lors de l’utilisation d’une base de données SQL Server :

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a>Prise en charge des colonnes éparses SQL Server

GitHub problème : [#8023](https://github.com/dotnet/efcore/issues/8023).

SQL Server [colonnes éparses](/sql/relational-databases/tables/use-sparse-columns) sont des colonnes ordinaires optimisées pour stocker des valeurs NULL. Cela peut être utile lors de l’utilisation du [mappage d’héritage TPH](xref:core/modeling/inheritance) où les propriétés d’un sous-type rarement utilisé entraînent des valeurs de colonne null pour la plupart des lignes de la table. Par exemple, considérez une `ForumModerator` classe qui s’étend de `ForumUser` :

<!--
    public class ForumUser
    {
        public int Id { get; set; }
        public string Username { get; set; }
    }

    public class ForumModerator : ForumUser
    {
        public string ForumName { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=UserEntityType)]

Il peut y avoir des millions d’utilisateurs, avec seulement quelques modérateurs. Cela signifie que le mappage de `ForumName` As Sparse peut être pertinent ici. Cela peut maintenant être configuré à l’aide `IsSparse` de dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> . Par exemple :

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<ForumModerator>()
                .Property(e => e.ForumName)
                .IsSparse();
        }
-->
[!code-csharp[OnModelCreating](../../../../samples/core/Miscellaneous/NewInEFCore6/SparseColumnsSample.cs?name=OnModelCreating)]

EF Core migrations marque la colonne comme étant éparse. Par exemple :

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> Les colonnes éparses présentent des limitations. Veillez à lire la [documentation sur les colonnes éparses SQL Server](/sql/relational-databases/tables/use-sparse-columns) pour vous assurer que les colonnes éparses sont le bon choix pour votre scénario.

### <a name="in-memory-database-validate-required-properties-are-not-null"></a>Base de données en mémoire : les propriétés Validate required ne sont pas null

GitHub problème : [#10613](https://github.com/dotnet/efcore/issues/10613). Cette fonctionnalité a été fournie par [@fagnercarvalho](https://github.com/fagnercarvalho) .

La base de données en mémoire EF Core lèvera à présent une exception en cas de tentative d’enregistrement d’une valeur null pour une propriété marquée comme obligatoire. Par exemple, considérez un `User` type avec une `Username` propriété obligatoire :

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

Si vous tentez d’enregistrer une entité avec une valeur null `Username` , l’exception suivante se produit :

> Microsoft. EntityFrameworkCore. exception dbupdateexception : les propriétés requises « {'username'} » sont manquantes pour l’instance du type d’entité « User » avec la valeur de clé « {ID : 1} ».

Cette validation peut être désactivée si nécessaire. Par exemple :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .LogTo(Console.WriteLine, new[] { InMemoryEventId.ChangesSaved })
                .UseInMemoryDatabase("UserContextWithNullCheckingDisabled")
                .EnableNullabilityCheck(false);
        }
-->
[!code-csharp[OnConfiguring](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=OnConfiguring)]

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a>Traduction de SQL Server améliorée pour IsNullOrWhitespace

GitHub problème : [#22916](https://github.com/dotnet/efcore/issues/22916). Cette fonctionnalité a été fournie par [@Marusyk](https://github.com/Marusyk) .

Considérez la requête suivante :

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

Avant le EF Core 6,0, le code suivant a été converti en SQL Server :

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

Cette traduction a été améliorée pour EF Core 6,0 à :

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a>Les commentaires de base de données sont générés au format de commentaires de code

GitHub problème : [#19113](https://github.com/dotnet/efcore/issues/19113). Cette fonctionnalité a été fournie par [@ErikEJ](https://github.com/ErikEJ) .

Les commentaires sur les tables et les colonnes SQL sont désormais générés automatiquement dans les types d’entités créés lors de l' [ingénierie à rebours d’une EF Core modèle](xref:core/managing-schemas/scaffolding) à partir d’une base de données SQL Server existante. Par exemple :

```csharp
/// <summary>
/// The Blog table.
/// </summary>
public partial class Blog
{
    /// <summary>
    /// The primary key.
    /// </summary>
    [Key]
    public int Id { get; set; }
}
```

## <a name="microsoftdatasqlite-60-preview-1"></a>Microsoft. Data. sqlite 6,0 Preview 1

> [!TIP]
> Vous pouvez exécuter et déboguer dans tous les exemples Preview 1 ci-dessous en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).

### <a name="savepoints-api"></a>API d’enregistrement des points de enregistrement

GitHub problème : [#20228](https://github.com/dotnet/efcore/issues/20228).

Nous sommes en cours de normalisation sur [une API commune pour les points d’enregistrement dans les fournisseurs ADO.net](https://github.com/dotnet/runtime/issues/33397). Microsoft. Data. sqlite prend désormais en charge cette API, notamment :

- <xref:System.Data.Common.DbTransaction.Save(System.String)> pour créer un point d’enregistrement dans la transaction
- <xref:System.Data.Common.DbTransaction.Rollback(System.String)> pour restaurer un point de sauvegarde précédent
- <xref:System.Data.Common.DbTransaction.Release(System.String)> pour libérer un point d’enregistrement

L’utilisation d’un point de sauvegarde permet de restaurer une partie d’une transaction sans restaurer toute la transaction. Par exemple, le code ci-dessous :

- Crée une transaction
- Envoie une mise à jour à la base de données.
- Crée un point d’enregistrement
- Envoie une autre mise à jour à la base de données
- Restaure le point de sauvegarde précédent créé
- Valide la transaction

<!--
        using var connection = new SqliteConnection("DataSource=test.db");
        connection.Open();

        using var transaction = connection.BeginTransaction();

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'ajcvickers' WHERE Id = 1";
            command.ExecuteNonQuery();
        }

        transaction.Save("MySavepoint");

        using (var command = connection.CreateCommand())
        {
            command.CommandText = @"UPDATE Users SET Username = 'wfvickers' WHERE Id = 2";
            command.ExecuteNonQuery();
        }

        transaction.Rollback("MySavepoint");

        transaction.Commit();
-->
[!code-csharp[PerformUpdates](../../../../samples/core/Miscellaneous/NewInEFCore6/SqliteSamples.cs?name=PerformUpdates)]

Cela entraîne la validation de la première mise à jour dans la base de données, tandis que la deuxième mise à jour n’est pas validée, car le point de sauvegarde a été restauré avant la validation de la transaction.

### <a name="command-timeout-in-the-connection-string"></a>Délai d’expiration de la commande dans la chaîne de connexion

GitHub problème : [#22505](https://github.com/dotnet/efcore/issues/22505). Cette fonctionnalité a été fournie par [@nmichels](https://github.com/nmichels) .

Les fournisseurs ADO.NET prennent en charge deux délais d’attente distincts :

- Délai d’attente de la connexion, qui détermine la durée maximale d’attente lors de l’établissement d’une connexion à la base de données.
- Délai d’attente de la commande, qui détermine la durée maximale d’attente d’une commande pour s’exécuter.

Le délai d’expiration de la commande peut être défini à partir du code à l’aide de <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType> . De nombreux fournisseurs exposent désormais également ce délai d’attente de commande dans la chaîne de connexion. Microsoft. Data. sqlite suit cette tendance avec le `Command Timeout` mot clé de chaîne de connexion. Par exemple, `"Command Timeout=60;DataSource=test.db"` utilise 60 secondes comme délai d’expiration par défaut pour les commandes créées par la connexion.

> [!TIP]
> SQLite traite `Default Timeout` comme synonyme de `Command Timeout` et peut donc être utilisé à la place si préféré.
