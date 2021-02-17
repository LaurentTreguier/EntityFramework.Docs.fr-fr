---
title: Nouveautés de EF Core 6,0
description: Vue d’ensemble des nouvelles fonctionnalités de EF Core 6,0
author: ajcvickers
ms.date: 01/28/2021
uid: core/what-is-new/ef-core-6.0/whatsnew
ms.openlocfilehash: bcc2b3ce9047a2c6b5a89e99b96919914bcf42fe
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543196"
---
# <a name="whats-new-in-ef-core-60"></a><span data-ttu-id="a747a-103">Nouveautés de EF Core 6,0</span><span class="sxs-lookup"><span data-stu-id="a747a-103">What's New in EF Core 6.0</span></span>

<span data-ttu-id="a747a-104">EF Core 6,0 est actuellement en cours de développement.</span><span class="sxs-lookup"><span data-stu-id="a747a-104">EF Core 6.0 is currently in development.</span></span> <span data-ttu-id="a747a-105">Elle contient une vue d’ensemble des modifications intéressantes introduites dans chaque aperçu.</span><span class="sxs-lookup"><span data-stu-id="a747a-105">This contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="a747a-106">Cette page ne duplique pas le [plan pour EF Core 6,0](xref:core/what-is-new/ef-core-6.0/plan).</span><span class="sxs-lookup"><span data-stu-id="a747a-106">This page does not duplicate the [plan for EF Core 6.0](xref:core/what-is-new/ef-core-6.0/plan).</span></span> <span data-ttu-id="a747a-107">Le plan décrit les thèmes globaux pour EF Core 6,0, y compris tout ce que nous envisageons d’inclure avant d’expédier la version finale.</span><span class="sxs-lookup"><span data-stu-id="a747a-107">The plan describes the overall themes for EF Core 6.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="ef-core-60-preview-1"></a><span data-ttu-id="a747a-108">EF Core 6,0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="a747a-108">EF Core 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="a747a-109">Vous pouvez exécuter et déboguer dans tous les exemples Preview 1 ci-dessous en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span><span class="sxs-lookup"><span data-stu-id="a747a-109">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="unicodeattribute"></a><span data-ttu-id="a747a-110">UnicodeAttribute</span><span class="sxs-lookup"><span data-stu-id="a747a-110">UnicodeAttribute</span></span>

<span data-ttu-id="a747a-111">GitHub problème : [#19794](https://github.com/dotnet/efcore/issues/19794).</span><span class="sxs-lookup"><span data-stu-id="a747a-111">GitHub Issue: [#19794](https://github.com/dotnet/efcore/issues/19794).</span></span> <span data-ttu-id="a747a-112">Cette fonctionnalité a été fournie par [@RaymondHuy](https://github.com/RaymondHuy) .</span><span class="sxs-lookup"><span data-stu-id="a747a-112">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="a747a-113">À compter de EF Core 6,0, une propriété de type chaîne peut désormais être mappée à une colonne non-Unicode à l’aide d’un attribut _de mappage sans spécifier directement le type de la base de données_.</span><span class="sxs-lookup"><span data-stu-id="a747a-113">Starting with EF Core 6.0, a string property can now be mapped to a non-Unicode column using a mapping attribute _without specifying the database type directly_.</span></span> <span data-ttu-id="a747a-114">Par exemple, considérez un `Book` type d’entité avec une propriété pour le [numéro ISBN (International Standard Book Number)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) au format « ISBN 978-3-16-148410-0 » :</span><span class="sxs-lookup"><span data-stu-id="a747a-114">For example, consider a `Book` entity type with a property for the [International Standard Book Number (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) in the form "ISBN 978-3-16-148410-0":</span></span>

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

<span data-ttu-id="a747a-115">Étant donné que les ISBNs ne peuvent pas contenir de caractères non-Unicode, l' `Unicode` attribut entraîne l’utilisation d’un type de chaîne non-Unicode.</span><span class="sxs-lookup"><span data-stu-id="a747a-115">Since ISBNs cannot contain any non-unicode characters, the `Unicode` attribute will cause a non-Unicode string type to be used.</span></span> <span data-ttu-id="a747a-116">En outre, `MaxLength` est utilisé pour limiter la taille de la colonne de base de données.</span><span class="sxs-lookup"><span data-stu-id="a747a-116">In addition, `MaxLength` is used to limit the size of the database column.</span></span> <span data-ttu-id="a747a-117">Par exemple, lors de l’utilisation de SQL Server, cela aboutit à une colonne de base de données `varchar(22)` :</span><span class="sxs-lookup"><span data-stu-id="a747a-117">For example, when using SQL Server, this results in a database column of `varchar(22)`:</span></span>

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="a747a-118">EF Core mappe les propriétés de chaîne aux colonnes Unicode par défaut.</span><span class="sxs-lookup"><span data-stu-id="a747a-118">EF Core maps string properties to Unicode columns by default.</span></span> <span data-ttu-id="a747a-119">`UnicodeAttribute` est ignoré lorsque le système de base de données prend en charge uniquement les types Unicode.</span><span class="sxs-lookup"><span data-stu-id="a747a-119">`UnicodeAttribute` is ignored when the database system supports only Unicode types.</span></span>

### <a name="precisionattribute"></a><span data-ttu-id="a747a-120">PrecisionAttribute</span><span class="sxs-lookup"><span data-stu-id="a747a-120">PrecisionAttribute</span></span>

<span data-ttu-id="a747a-121">GitHub problème : [#17914](https://github.com/dotnet/efcore/issues/17914).</span><span class="sxs-lookup"><span data-stu-id="a747a-121">GitHub Issue: [#17914](https://github.com/dotnet/efcore/issues/17914).</span></span> <span data-ttu-id="a747a-122">Cette fonctionnalité a été fournie par [@RaymondHuy](https://github.com/RaymondHuy) .</span><span class="sxs-lookup"><span data-stu-id="a747a-122">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="a747a-123">La précision et l’échelle d’une colonne de base de données peuvent désormais être configurées à l’aide d’attributs de mappage _sans spécifier directement le type de base de données_.</span><span class="sxs-lookup"><span data-stu-id="a747a-123">The precision and scale of a database column can now be configured using mapping attributes _without specifying the database type directly_.</span></span> <span data-ttu-id="a747a-124">Par exemple, considérez un `Product` type d’entité avec une propriété décimale `Price` :</span><span class="sxs-lookup"><span data-stu-id="a747a-124">For example, consider a `Product` entity type with a decimal `Price` property:</span></span>

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

<span data-ttu-id="a747a-125">EF Core mappera cette propriété à une colonne de base de données avec une précision de 10 et une échelle de 2.</span><span class="sxs-lookup"><span data-stu-id="a747a-125">EF Core will map this property to a database column with precision 10 and scale 2.</span></span> <span data-ttu-id="a747a-126">Par exemple, sur SQL Server :</span><span class="sxs-lookup"><span data-stu-id="a747a-126">For example, on SQL Server:</span></span>

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a><span data-ttu-id="a747a-127">EntityTypeConfigurationAttribute</span><span class="sxs-lookup"><span data-stu-id="a747a-127">EntityTypeConfigurationAttribute</span></span>

<span data-ttu-id="a747a-128">GitHub problème : [#23163](https://github.com/dotnet/efcore/issues/23163).</span><span class="sxs-lookup"><span data-stu-id="a747a-128">GitHub Issue: [#23163](https://github.com/dotnet/efcore/issues/23163).</span></span> <span data-ttu-id="a747a-129">Cette fonctionnalité a été fournie par [@KaloyanIT](https://github.com/KaloyanIT) .</span><span class="sxs-lookup"><span data-stu-id="a747a-129">This feature was contributed by [@KaloyanIT](https://github.com/KaloyanIT).</span></span>

<span data-ttu-id="a747a-130"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> les instances autorisent <xref:Microsoft.EntityFrameworkCore.ModelBuilder> la configuration de chaque type d’entité dans sa propre classe de configuration.</span><span class="sxs-lookup"><span data-stu-id="a747a-130"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> instances allow <xref:Microsoft.EntityFrameworkCore.ModelBuilder> configuration for a each entity type to be contained in its own configuration class.</span></span> <span data-ttu-id="a747a-131">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a747a-131">For example:</span></span>

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

<span data-ttu-id="a747a-132">Normalement, cette classe de configuration doit être instanciée et appelée dans à partir de <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a747a-132">Normally, this configuration class must be instantiated and called into from <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a747a-133">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a747a-133">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

<span data-ttu-id="a747a-134">À partir de EF Core 6,0, un `EntityTypeConfigurationAttribute` peut être placé sur le type d’entité afin que EF Core puisse trouver et utiliser la configuration appropriée.</span><span class="sxs-lookup"><span data-stu-id="a747a-134">Starting with EF Core 6.0, an `EntityTypeConfigurationAttribute` can be placed on the entity type such that EF Core can find and use appropriate configuration.</span></span> <span data-ttu-id="a747a-135">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a747a-135">For example:</span></span>

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

<span data-ttu-id="a747a-136">Cet attribut signifie que EF Core utilise l’implémentation spécifiée `IEntityTypeConfiguration` chaque fois que le `Book` type d’entité est inclus dans un modèle.</span><span class="sxs-lookup"><span data-stu-id="a747a-136">This attribute means that EF Core will use the specified `IEntityTypeConfiguration` implementation whenever the `Book` entity type is included in a model.</span></span> <span data-ttu-id="a747a-137">Le type d’entité est inclus dans un modèle à l’aide de l’un des mécanismes normaux.</span><span class="sxs-lookup"><span data-stu-id="a747a-137">The entity type is included in a model using one of the normal mechanisms.</span></span> <span data-ttu-id="a747a-138">Par exemple, en créant une <xref:Microsoft.EntityFrameworkCore.DbSet%601> propriété pour le type d’entité :</span><span class="sxs-lookup"><span data-stu-id="a747a-138">For example, by creating a <xref:Microsoft.EntityFrameworkCore.DbSet%601> property for the entity type:</span></span>

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

<span data-ttu-id="a747a-139">Ou en l’inscrivant dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> :</span><span class="sxs-lookup"><span data-stu-id="a747a-139">Or by registering it in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> <span data-ttu-id="a747a-140">`EntityTypeConfigurationAttribute` les types ne seront pas détectés automatiquement dans un assembly.</span><span class="sxs-lookup"><span data-stu-id="a747a-140">`EntityTypeConfigurationAttribute` types will not be automatically discovered in an assembly.</span></span> <span data-ttu-id="a747a-141">Les types d’entités doivent être ajoutés au modèle avant que l’attribut ne soit découvert sur ce type d’entité.</span><span class="sxs-lookup"><span data-stu-id="a747a-141">Entity types must be added to the model before the attribute will be discovered on that entity type.</span></span>

### <a name="translate-tostring-on-sqlite"></a><span data-ttu-id="a747a-142">Traduire ToString sur SQLite</span><span class="sxs-lookup"><span data-stu-id="a747a-142">Translate ToString on SQLite</span></span>

<span data-ttu-id="a747a-143">GitHub problème : [#17223](https://github.com/dotnet/efcore/issues/17223).</span><span class="sxs-lookup"><span data-stu-id="a747a-143">GitHub Issue: [#17223](https://github.com/dotnet/efcore/issues/17223).</span></span> <span data-ttu-id="a747a-144">Cette fonctionnalité a été fournie par [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="a747a-144">This feature was contributed by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span>

<span data-ttu-id="a747a-145">Les appels à <xref:System.Object.ToString> sont désormais traduits en SQL lors de l’utilisation du fournisseur de base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="a747a-145">Calls to <xref:System.Object.ToString> are now translated to SQL when using the SQLite database provider.</span></span> <span data-ttu-id="a747a-146">Cela peut être utile pour les recherches de texte impliquant des colonnes qui ne sont pas des chaînes.</span><span class="sxs-lookup"><span data-stu-id="a747a-146">This can be useful for text searches involving non-string columns.</span></span> <span data-ttu-id="a747a-147">Par exemple, considérez un `User` type d’entité qui stocke les numéros de téléphone comme valeurs numériques :</span><span class="sxs-lookup"><span data-stu-id="a747a-147">For example, consider a `User` entity type that stores phone numbers as numeric values:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

<span data-ttu-id="a747a-148">`ToString` peut être utilisé pour convertir le nombre en une chaîne dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="a747a-148">`ToString` can be used to convert the number to a string in the database.</span></span> <span data-ttu-id="a747a-149">Nous pouvons ensuite utiliser cette chaîne avec une fonction telle que `LIKE` pour rechercher des nombres qui correspondent à un modèle.</span><span class="sxs-lookup"><span data-stu-id="a747a-149">We can then use this string with a function such as `LIKE` to find numbers that match a pattern.</span></span> <span data-ttu-id="a747a-150">Par exemple, pour rechercher tous les nombres contenant 555 :</span><span class="sxs-lookup"><span data-stu-id="a747a-150">For example, to find all numbers containing 555:</span></span>

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

<span data-ttu-id="a747a-151">Cela se traduit par le code SQL suivant lors de l’utilisation d’une base de données SQLite :</span><span class="sxs-lookup"><span data-stu-id="a747a-151">This translates to the following SQL when using a SQLite database:</span></span>

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

<span data-ttu-id="a747a-152">Notez que la traduction de <xref:System.Object.ToString> pour SQL Server est déjà prise en charge dans EF Core 5,0 et peut également être prise en charge par d’autres fournisseurs de bases de données.</span><span class="sxs-lookup"><span data-stu-id="a747a-152">Note that translation of <xref:System.Object.ToString> for SQL Server is already supported in EF Core 5.0, and may also be supported by other database providers.</span></span>

### <a name="effunctionsrandom"></a><span data-ttu-id="a747a-153">EF. Functions. Random</span><span class="sxs-lookup"><span data-stu-id="a747a-153">EF.Functions.Random</span></span>

<span data-ttu-id="a747a-154">GitHub problème : [#16141](https://github.com/dotnet/efcore/issues/16141).</span><span class="sxs-lookup"><span data-stu-id="a747a-154">GitHub Issue: [#16141](https://github.com/dotnet/efcore/issues/16141).</span></span> <span data-ttu-id="a747a-155">Cette fonctionnalité a été fournie par [@RaymondHuy](https://github.com/RaymondHuy) .</span><span class="sxs-lookup"><span data-stu-id="a747a-155">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="a747a-156">`EF.Functions.Random` mappe à une fonction de base de données qui retourne un nombre Pseudo-aléatoire compris entre 0 et 1 exclusif.</span><span class="sxs-lookup"><span data-stu-id="a747a-156">`EF.Functions.Random` maps to a database function returning a pseudo-random number between 0 and 1 exclusive.</span></span> <span data-ttu-id="a747a-157">Les traductions ont été implémentées dans le EF Core référentiel pour SQL Server, SQLite et Cosmos.</span><span class="sxs-lookup"><span data-stu-id="a747a-157">Translations have been implemented in the EF Core repo for SQL Server, SQLite, and Cosmos.</span></span> <span data-ttu-id="a747a-158">Par exemple, considérez un `User` type d’entité avec une `Popularity` propriété :</span><span class="sxs-lookup"><span data-stu-id="a747a-158">For example, consider a `User` entity type with a `Popularity` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

<span data-ttu-id="a747a-159">`Popularity` peut avoir des valeurs comprises entre 1 et 5 inclus.</span><span class="sxs-lookup"><span data-stu-id="a747a-159">`Popularity` can have values from 1 to 5 inclusive.</span></span> <span data-ttu-id="a747a-160">À l’aide de, `EF.Functions.Random` nous pouvons écrire une requête pour renvoyer tous les utilisateurs avec une popularité choisie de manière aléatoire :</span><span class="sxs-lookup"><span data-stu-id="a747a-160">Using `EF.Functions.Random` we can write a query to return all users with a randomly chosen popularity:</span></span>

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

<span data-ttu-id="a747a-161">Cela se traduit par le code SQL suivant lors de l’utilisation d’une base de données SQL Server :</span><span class="sxs-lookup"><span data-stu-id="a747a-161">This translates to the following SQL when using a SQL Server database:</span></span>

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a><span data-ttu-id="a747a-162">Prise en charge des colonnes éparses SQL Server</span><span class="sxs-lookup"><span data-stu-id="a747a-162">Support for SQL Server sparse columns</span></span>

<span data-ttu-id="a747a-163">GitHub problème : [#8023](https://github.com/dotnet/efcore/issues/8023).</span><span class="sxs-lookup"><span data-stu-id="a747a-163">GitHub Issue: [#8023](https://github.com/dotnet/efcore/issues/8023).</span></span>

<span data-ttu-id="a747a-164">SQL Server [colonnes éparses](/sql/relational-databases/tables/use-sparse-columns) sont des colonnes ordinaires optimisées pour stocker des valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="a747a-164">SQL Server [sparse columns](/sql/relational-databases/tables/use-sparse-columns) are ordinary columns that are optimized to store null values.</span></span> <span data-ttu-id="a747a-165">Cela peut être utile lors de l’utilisation du [mappage d’héritage TPH](xref:core/modeling/inheritance) où les propriétés d’un sous-type rarement utilisé entraînent des valeurs de colonne null pour la plupart des lignes de la table.</span><span class="sxs-lookup"><span data-stu-id="a747a-165">This can be useful when using [TPH inheritance mapping](xref:core/modeling/inheritance) where properties of a rarely used subtype will result in null column values for most rows in the table.</span></span> <span data-ttu-id="a747a-166">Par exemple, considérez une `ForumModerator` classe qui s’étend de `ForumUser` :</span><span class="sxs-lookup"><span data-stu-id="a747a-166">For example, consider a `ForumModerator` class that extends from `ForumUser`:</span></span>

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

<span data-ttu-id="a747a-167">Il peut y avoir des millions d’utilisateurs, avec seulement quelques modérateurs.</span><span class="sxs-lookup"><span data-stu-id="a747a-167">There may be millions of users, with only a handful of these being moderators.</span></span> <span data-ttu-id="a747a-168">Cela signifie que le mappage de `ForumName` As Sparse peut être pertinent ici.</span><span class="sxs-lookup"><span data-stu-id="a747a-168">This means mapping the `ForumName` as sparse might make sense here.</span></span> <span data-ttu-id="a747a-169">Cela peut maintenant être configuré à l’aide `IsSparse` de dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="a747a-169">This can now be configured using `IsSparse` in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="a747a-170">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a747a-170">For example:</span></span>

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

<span data-ttu-id="a747a-171">EF Core migrations marque la colonne comme étant éparse.</span><span class="sxs-lookup"><span data-stu-id="a747a-171">EF Core migrations will then mark the column as sparse.</span></span> <span data-ttu-id="a747a-172">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a747a-172">For example:</span></span>

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="a747a-173">Les colonnes éparses présentent des limitations.</span><span class="sxs-lookup"><span data-stu-id="a747a-173">Sparse columns have limitations.</span></span> <span data-ttu-id="a747a-174">Veillez à lire la [documentation sur les colonnes éparses SQL Server](/sql/relational-databases/tables/use-sparse-columns) pour vous assurer que les colonnes éparses sont le bon choix pour votre scénario.</span><span class="sxs-lookup"><span data-stu-id="a747a-174">Make sure to read the [SQL Server sparse columns documentation](/sql/relational-databases/tables/use-sparse-columns) to ensure that sparse columns are the right choice for your scenario.</span></span>

### <a name="in-memory-database-validate-required-properties-are-not-null"></a><span data-ttu-id="a747a-175">Base de données en mémoire : les propriétés Validate required ne sont pas null</span><span class="sxs-lookup"><span data-stu-id="a747a-175">In-memory database: validate required properties are not null</span></span>

<span data-ttu-id="a747a-176">GitHub problème : [#10613](https://github.com/dotnet/efcore/issues/10613).</span><span class="sxs-lookup"><span data-stu-id="a747a-176">GitHub Issue: [#10613](https://github.com/dotnet/efcore/issues/10613).</span></span> <span data-ttu-id="a747a-177">Cette fonctionnalité a été fournie par [@fagnercarvalho](https://github.com/fagnercarvalho) .</span><span class="sxs-lookup"><span data-stu-id="a747a-177">This feature was contributed by [@fagnercarvalho](https://github.com/fagnercarvalho).</span></span>

<span data-ttu-id="a747a-178">La base de données en mémoire EF Core lèvera à présent une exception en cas de tentative d’enregistrement d’une valeur null pour une propriété marquée comme obligatoire.</span><span class="sxs-lookup"><span data-stu-id="a747a-178">The EF Core in-memory database will now throw an exception if an attempt is made to save a null value for a property marked as required.</span></span> <span data-ttu-id="a747a-179">Par exemple, considérez un `User` type avec une `Username` propriété obligatoire :</span><span class="sxs-lookup"><span data-stu-id="a747a-179">For example, consider a `User` type with a required `Username` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

<span data-ttu-id="a747a-180">Si vous tentez d’enregistrer une entité avec une valeur null `Username` , l’exception suivante se produit :</span><span class="sxs-lookup"><span data-stu-id="a747a-180">Attempting to save an entity with a null `Username` will result in the following exception:</span></span>

> <span data-ttu-id="a747a-181">Microsoft. EntityFrameworkCore. exception dbupdateexception : les propriétés requises « {'username'} » sont manquantes pour l’instance du type d’entité « User » avec la valeur de clé « {ID : 1} ».</span><span class="sxs-lookup"><span data-stu-id="a747a-181">Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}'.</span></span>

<span data-ttu-id="a747a-182">Cette validation peut être désactivée si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="a747a-182">This validation can be disabled if necessary.</span></span> <span data-ttu-id="a747a-183">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a747a-183">For example:</span></span>

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

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a><span data-ttu-id="a747a-184">Traduction de SQL Server améliorée pour IsNullOrWhitespace</span><span class="sxs-lookup"><span data-stu-id="a747a-184">Improved SQL Server translation for IsNullOrWhitespace</span></span>

<span data-ttu-id="a747a-185">GitHub problème : [#22916](https://github.com/dotnet/efcore/issues/22916).</span><span class="sxs-lookup"><span data-stu-id="a747a-185">GitHub Issue: [#22916](https://github.com/dotnet/efcore/issues/22916).</span></span> <span data-ttu-id="a747a-186">Cette fonctionnalité a été fournie par [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="a747a-186">This feature was contributed by [@Marusyk](https://github.com/Marusyk).</span></span>

<span data-ttu-id="a747a-187">Considérez la requête suivante :</span><span class="sxs-lookup"><span data-stu-id="a747a-187">Consider the following query:</span></span>

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

<span data-ttu-id="a747a-188">Avant le EF Core 6,0, le code suivant a été converti en SQL Server :</span><span class="sxs-lookup"><span data-stu-id="a747a-188">Before EF Core 6.0, this was translated to the following on SQL Server:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

<span data-ttu-id="a747a-189">Cette traduction a été améliorée pour EF Core 6,0 à :</span><span class="sxs-lookup"><span data-stu-id="a747a-189">This translation has been improved for EF Core 6.0 to:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a><span data-ttu-id="a747a-190">Les commentaires de base de données sont générés au format de commentaires de code</span><span class="sxs-lookup"><span data-stu-id="a747a-190">Database comments are scaffolded to code comments</span></span>

<span data-ttu-id="a747a-191">GitHub problème : [#19113](https://github.com/dotnet/efcore/issues/19113).</span><span class="sxs-lookup"><span data-stu-id="a747a-191">GitHub Issue: [#19113](https://github.com/dotnet/efcore/issues/19113).</span></span> <span data-ttu-id="a747a-192">Cette fonctionnalité a été fournie par [@ErikEJ](https://github.com/ErikEJ) .</span><span class="sxs-lookup"><span data-stu-id="a747a-192">This feature was contributed by [@ErikEJ](https://github.com/ErikEJ).</span></span>

<span data-ttu-id="a747a-193">Les commentaires sur les tables et les colonnes SQL sont désormais générés automatiquement dans les types d’entités créés lors de l' [ingénierie à rebours d’une EF Core modèle](xref:core/managing-schemas/scaffolding) à partir d’une base de données SQL Server existante.</span><span class="sxs-lookup"><span data-stu-id="a747a-193">Comments on SQL tables and columns are now scaffolded into the entity types created when [reverse-engineering an EF Core model](xref:core/managing-schemas/scaffolding) from an existing SQL Server database.</span></span> <span data-ttu-id="a747a-194">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a747a-194">For example:</span></span>

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

## <a name="microsoftdatasqlite-60-preview-1"></a><span data-ttu-id="a747a-195">Microsoft. Data. sqlite 6,0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="a747a-195">Microsoft.Data.Sqlite 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="a747a-196">Vous pouvez exécuter et déboguer dans tous les exemples Preview 1 ci-dessous en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span><span class="sxs-lookup"><span data-stu-id="a747a-196">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="savepoints-api"></a><span data-ttu-id="a747a-197">API d’enregistrement des points de enregistrement</span><span class="sxs-lookup"><span data-stu-id="a747a-197">Savepoints API</span></span>

<span data-ttu-id="a747a-198">GitHub problème : [#20228](https://github.com/dotnet/efcore/issues/20228).</span><span class="sxs-lookup"><span data-stu-id="a747a-198">GitHub Issue: [#20228](https://github.com/dotnet/efcore/issues/20228).</span></span>

<span data-ttu-id="a747a-199">Nous sommes en cours de normalisation sur [une API commune pour les points d’enregistrement dans les fournisseurs ADO.net](https://github.com/dotnet/runtime/issues/33397).</span><span class="sxs-lookup"><span data-stu-id="a747a-199">We have been standardizing on [a common API for savepoints in ADO.NET providers](https://github.com/dotnet/runtime/issues/33397).</span></span> <span data-ttu-id="a747a-200">Microsoft. Data. sqlite prend désormais en charge cette API, notamment :</span><span class="sxs-lookup"><span data-stu-id="a747a-200">Microsoft.Data.Sqlite now supports this API, including:</span></span>

- <span data-ttu-id="a747a-201"><xref:System.Data.Common.DbTransaction.Save(System.String)> pour créer un point d’enregistrement dans la transaction</span><span class="sxs-lookup"><span data-stu-id="a747a-201"><xref:System.Data.Common.DbTransaction.Save(System.String)> to create a savepoint in the transaction</span></span>
- <span data-ttu-id="a747a-202"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> pour restaurer un point de sauvegarde précédent</span><span class="sxs-lookup"><span data-stu-id="a747a-202"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> to roll back to a previous savepoint</span></span>
- <span data-ttu-id="a747a-203"><xref:System.Data.Common.DbTransaction.Release(System.String)> pour libérer un point d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="a747a-203"><xref:System.Data.Common.DbTransaction.Release(System.String)> to release a savepoint</span></span>

<span data-ttu-id="a747a-204">L’utilisation d’un point de sauvegarde permet de restaurer une partie d’une transaction sans restaurer toute la transaction.</span><span class="sxs-lookup"><span data-stu-id="a747a-204">Using a savepoint allows part of a transaction to be rolled back without rolling back the entire transaction.</span></span> <span data-ttu-id="a747a-205">Par exemple, le code ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="a747a-205">For example, the code below:</span></span>

- <span data-ttu-id="a747a-206">Crée une transaction</span><span class="sxs-lookup"><span data-stu-id="a747a-206">Creates a transaction</span></span>
- <span data-ttu-id="a747a-207">Envoie une mise à jour à la base de données.</span><span class="sxs-lookup"><span data-stu-id="a747a-207">Sends an update to the database</span></span>
- <span data-ttu-id="a747a-208">Crée un point d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="a747a-208">Creates a savepoint</span></span>
- <span data-ttu-id="a747a-209">Envoie une autre mise à jour à la base de données</span><span class="sxs-lookup"><span data-stu-id="a747a-209">Sends another update to the database</span></span>
- <span data-ttu-id="a747a-210">Restaure le point de sauvegarde précédent créé</span><span class="sxs-lookup"><span data-stu-id="a747a-210">Rolls back to the savepoint previous created</span></span>
- <span data-ttu-id="a747a-211">Valide la transaction</span><span class="sxs-lookup"><span data-stu-id="a747a-211">Commits the transaction</span></span>

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

<span data-ttu-id="a747a-212">Cela entraîne la validation de la première mise à jour dans la base de données, tandis que la deuxième mise à jour n’est pas validée, car le point de sauvegarde a été restauré avant la validation de la transaction.</span><span class="sxs-lookup"><span data-stu-id="a747a-212">This will result in the first update being committed to the database, while the second update is not committed since the savepoint was rolled back before committing the transaction.</span></span>

### <a name="command-timeout-in-the-connection-string"></a><span data-ttu-id="a747a-213">Délai d’expiration de la commande dans la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="a747a-213">Command timeout in the connection string</span></span>

<span data-ttu-id="a747a-214">GitHub problème : [#22505](https://github.com/dotnet/efcore/issues/22505).</span><span class="sxs-lookup"><span data-stu-id="a747a-214">GitHub Issue: [#22505](https://github.com/dotnet/efcore/issues/22505).</span></span> <span data-ttu-id="a747a-215">Cette fonctionnalité a été fournie par [@nmichels](https://github.com/nmichels) .</span><span class="sxs-lookup"><span data-stu-id="a747a-215">This feature was contributed by [@nmichels](https://github.com/nmichels).</span></span>

<span data-ttu-id="a747a-216">Les fournisseurs ADO.NET prennent en charge deux délais d’attente distincts :</span><span class="sxs-lookup"><span data-stu-id="a747a-216">ADO.NET providers support two distinct timeouts:</span></span>

- <span data-ttu-id="a747a-217">Délai d’attente de la connexion, qui détermine la durée maximale d’attente lors de l’établissement d’une connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="a747a-217">The connection timeout, which determines the maximum time to wait when making a connection to the database.</span></span>
- <span data-ttu-id="a747a-218">Délai d’attente de la commande, qui détermine la durée maximale d’attente d’une commande pour s’exécuter.</span><span class="sxs-lookup"><span data-stu-id="a747a-218">The command timeout, which determines the maximum time to wait for a command to complete executing.</span></span>

<span data-ttu-id="a747a-219">Le délai d’expiration de la commande peut être défini à partir du code à l’aide de <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a747a-219">The command timeout can be set from code using <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a747a-220">De nombreux fournisseurs exposent désormais également ce délai d’attente de commande dans la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="a747a-220">Many providers are now also exposing this command timeout in the connection string.</span></span> <span data-ttu-id="a747a-221">Microsoft. Data. sqlite suit cette tendance avec le `Command Timeout` mot clé de chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="a747a-221">Microsoft.Data.Sqlite is following this trend with the `Command Timeout` connection string keyword.</span></span> <span data-ttu-id="a747a-222">Par exemple, `"Command Timeout=60;DataSource=test.db"` utilise 60 secondes comme délai d’expiration par défaut pour les commandes créées par la connexion.</span><span class="sxs-lookup"><span data-stu-id="a747a-222">For example, `"Command Timeout=60;DataSource=test.db"` will use 60 seconds as the default timeout for commands created by the connection.</span></span>

> [!TIP]
> <span data-ttu-id="a747a-223">SQLite traite `Default Timeout` comme synonyme de `Command Timeout` et peut donc être utilisé à la place si préféré.</span><span class="sxs-lookup"><span data-stu-id="a747a-223">Sqlite treats `Default Timeout` as a synonym for `Command Timeout` and so can be used instead if preferred.</span></span>
