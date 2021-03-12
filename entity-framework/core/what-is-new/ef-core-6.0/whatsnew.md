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
# <a name="whats-new-in-ef-core-60"></a><span data-ttu-id="9768a-103">Nouveautés de EF Core 6,0</span><span class="sxs-lookup"><span data-stu-id="9768a-103">What's New in EF Core 6.0</span></span>

<span data-ttu-id="9768a-104">EF Core 6,0 est actuellement en cours de développement.</span><span class="sxs-lookup"><span data-stu-id="9768a-104">EF Core 6.0 is currently in development.</span></span> <span data-ttu-id="9768a-105">Elle contient une vue d’ensemble des modifications intéressantes introduites dans chaque aperçu.</span><span class="sxs-lookup"><span data-stu-id="9768a-105">This contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="9768a-106">Cette page ne duplique pas le [plan pour EF Core 6,0](xref:core/what-is-new/ef-core-6.0/plan).</span><span class="sxs-lookup"><span data-stu-id="9768a-106">This page does not duplicate the [plan for EF Core 6.0](xref:core/what-is-new/ef-core-6.0/plan).</span></span> <span data-ttu-id="9768a-107">Le plan décrit les thèmes globaux pour EF Core 6,0, y compris tout ce que nous envisageons d’inclure avant d’expédier la version finale.</span><span class="sxs-lookup"><span data-stu-id="9768a-107">The plan describes the overall themes for EF Core 6.0, including everything we are planning to include before shipping the final release.</span></span>

> [!TIP]
> <span data-ttu-id="9768a-108">Vous pouvez exécuter et déboguer dans tous les exemples Preview 1 ci-dessous en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).</span><span class="sxs-lookup"><span data-stu-id="9768a-108">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).</span></span>

## <a name="ef-core-60-preview-2"></a><span data-ttu-id="9768a-109">EF Core 6,0 Preview 2</span><span class="sxs-lookup"><span data-stu-id="9768a-109">EF Core 6.0 Preview 2</span></span>

### <a name="preserve-synchronization-context-in-savechangesasync"></a><span data-ttu-id="9768a-110">Conserver le contexte de synchronisation dans SaveChangesAsync</span><span class="sxs-lookup"><span data-stu-id="9768a-110">Preserve synchronization context in SaveChangesAsync</span></span>

<span data-ttu-id="9768a-111">GitHub problème : [#23971](https://github.com/dotnet/efcore/issues/23971).</span><span class="sxs-lookup"><span data-stu-id="9768a-111">GitHub Issue: [#23971](https://github.com/dotnet/efcore/issues/23971).</span></span>

<span data-ttu-id="9768a-112">Nous avons [modifié le code EF Core dans la version 5,0](https://github.com/dotnet/efcore/issues/10164) afin de lui affecter la valeur <xref:System.Threading.Tasks.Task.ConfigureAwait%2A?displayProperty=nameWithType> à `false` tous les endroits où nous avons du `await` code asynchrone.</span><span class="sxs-lookup"><span data-stu-id="9768a-112">We [changed the EF Core code in the 5.0 release](https://github.com/dotnet/efcore/issues/10164) to set <xref:System.Threading.Tasks.Task.ConfigureAwait%2A?displayProperty=nameWithType> to `false` in all places where we `await` async code.</span></span> <span data-ttu-id="9768a-113">Il s’agit généralement d’un meilleur choix pour l’utilisation de EF Core.</span><span class="sxs-lookup"><span data-stu-id="9768a-113">This is generally a better choice for EF Core usage.</span></span> <span data-ttu-id="9768a-114">Toutefois, <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> est un cas particulier, car EF Core définit les valeurs générées dans les entités suivies une fois l’opération de base de données asynchrone terminée.</span><span class="sxs-lookup"><span data-stu-id="9768a-114">However, <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> is a special case because EF Core will set generated values into tracked entities after the async database operation is complete.</span></span> <span data-ttu-id="9768a-115">Ces modifications peuvent ensuite déclencher des notifications qui, par exemple, doivent s’exécuter sur le U.I.</span><span class="sxs-lookup"><span data-stu-id="9768a-115">These changes may then trigger notifications which, for example, may have to run on the U.I.</span></span> <span data-ttu-id="9768a-116">thread.</span><span class="sxs-lookup"><span data-stu-id="9768a-116">thread.</span></span> <span data-ttu-id="9768a-117">Par conséquent, nous rétablissons cette modification dans EF Core 6,0 pour la <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> méthode uniquement.</span><span class="sxs-lookup"><span data-stu-id="9768a-117">Therefore, we are reverting this change in EF Core 6.0 for the <xref:System.Data.Entity.DbContext.SaveChangesAsync%2A> method only.</span></span>

### <a name="translate-stringconcat-with-multiple-arguments"></a><span data-ttu-id="9768a-118">Traduire String. Concat avec plusieurs arguments</span><span class="sxs-lookup"><span data-stu-id="9768a-118">Translate String.Concat with multiple arguments</span></span>

<span data-ttu-id="9768a-119">GitHub problème : [#23859](https://github.com/dotnet/efcore/issues/23859).</span><span class="sxs-lookup"><span data-stu-id="9768a-119">GitHub Issue: [#23859](https://github.com/dotnet/efcore/issues/23859).</span></span> <span data-ttu-id="9768a-120">Cette fonctionnalité a été fournie par [@wmeints](https://github.com/wmeints) .</span><span class="sxs-lookup"><span data-stu-id="9768a-120">This feature was contributed by [@wmeints](https://github.com/wmeints).</span></span>

<span data-ttu-id="9768a-121">À compter de EF Core 6,0, les appels à <xref:System.String.Concat%2A?displayProperty=nameWithType> avec plusieurs arguments sont désormais traduits en SQL.</span><span class="sxs-lookup"><span data-stu-id="9768a-121">Starting with EF Core 6.0, calls to <xref:System.String.Concat%2A?displayProperty=nameWithType> with multiple arguments are now translated to SQL.</span></span> <span data-ttu-id="9768a-122">Par exemple, la requête suivante :</span><span class="sxs-lookup"><span data-stu-id="9768a-122">For example, the following query:</span></span>

<!--
        var shards = context.Shards
            .Where(e => string.Concat(e.Token1, e.Token2, e.Token3) != e.TokensProcessed).ToList();
-->
[!code-csharp[StringConcat](../../../../samples/core/Miscellaneous/NewInEFCore6/StringConcatSample.cs?name=StringConcat)]

<span data-ttu-id="9768a-123">Sera traduit dans le code SQL suivant lors de l’utilisation de SQL Server :</span><span class="sxs-lookup"><span data-stu-id="9768a-123">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [s].[Id], [s].[Token1], [s].[Token2], [s].[Token3], [s].[TokensProcessed]
FROM [Shards] AS [s]
WHERE ((COALESCE([s].[Token1], N'') + (COALESCE([s].[Token2], N'') + COALESCE([s].[Token3], N''))) <> [s].[TokensProcessed]) OR [s].[TokensProcessed] IS NULL
```

### <a name="smoother-integration-with-systemlinqasync"></a><span data-ttu-id="9768a-124">Intégration plus lisse avec System. Linq. Async</span><span class="sxs-lookup"><span data-stu-id="9768a-124">Smoother integration with System.Linq.Async</span></span>

<span data-ttu-id="9768a-125">GitHub problème : [#24041](https://github.com/dotnet/efcore/issues/24041).</span><span class="sxs-lookup"><span data-stu-id="9768a-125">GitHub Issue: [#24041](https://github.com/dotnet/efcore/issues/24041).</span></span>

<span data-ttu-id="9768a-126">Le package [System. Linq. Async](https://www.nuget.org/packages/System.Linq.Async/) ajoute un traitement LINQ asynchrone côté client.</span><span class="sxs-lookup"><span data-stu-id="9768a-126">The [System.Linq.Async](https://www.nuget.org/packages/System.Linq.Async/) package adds client-side async LINQ processing.</span></span> <span data-ttu-id="9768a-127">L’utilisation de ce package avec les versions précédentes de EF Core était lourde en raison d’un conflit d’espace de noms pour les méthodes LINQ asynchrones.</span><span class="sxs-lookup"><span data-stu-id="9768a-127">Using this package with previous versions of EF Core was cumbersome due to a namespace clash for the async LINQ methods.</span></span> <span data-ttu-id="9768a-128">Dans EF Core 6,0, nous avons tiré parti de la mise en correspondance des modèles C# pour <xref:System.Collections.Generic.IAsyncEnumerable%601> que la EF Core exposée <xref:Microsoft.EntityFrameworkCore.DbSet%601> n’ait pas besoin d’implémenter l’interface directement.</span><span class="sxs-lookup"><span data-stu-id="9768a-128">In EF Core 6.0 we have taken advantage of C# pattern matching for <xref:System.Collections.Generic.IAsyncEnumerable%601> such that the exposed EF Core <xref:Microsoft.EntityFrameworkCore.DbSet%601> does not need to implement the interface directly.</span></span>

<span data-ttu-id="9768a-129">Notez que la plupart des applications n’ont pas besoin d’utiliser System. Linq. Async, car EF Core requêtes sont généralement entièrement traduites sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="9768a-129">Note that most applications do not need to use System.Linq.Async since EF Core queries are usually fully translated on the server.</span></span>

### <a name="more-flexible-free-text-search"></a><span data-ttu-id="9768a-130">Recherche de texte libre plus flexible</span><span class="sxs-lookup"><span data-stu-id="9768a-130">More flexible free-text search</span></span>

<span data-ttu-id="9768a-131">GitHub problème : [#23921](https://github.com/dotnet/efcore/issues/23921).</span><span class="sxs-lookup"><span data-stu-id="9768a-131">GitHub Issue: [#23921](https://github.com/dotnet/efcore/issues/23921).</span></span>

<span data-ttu-id="9768a-132">Dans EF Core 6,0, nous avons assoupli les exigences de paramètres pour <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.FreeText(Microsoft.EntityFrameworkCore.DbFunctions,System.String,System.String)> et <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.Contains%2A> .</span><span class="sxs-lookup"><span data-stu-id="9768a-132">In EF Core 6.0, we have relaxed the parameter requirements for <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.FreeText(Microsoft.EntityFrameworkCore.DbFunctions,System.String,System.String)> and <xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.Contains%2A>.</span></span> <span data-ttu-id="9768a-133">Cela permet d’utiliser ces fonctions avec des colonnes binaires, ou avec des colonnes mappées à l’aide d’un convertisseur de valeur.</span><span class="sxs-lookup"><span data-stu-id="9768a-133">This allows these functions to be used with binary columns, or with columns mapped using a value converter.</span></span> <span data-ttu-id="9768a-134">Par exemple, considérez un type d’entité avec une `Name` propriété définie en tant qu’objet de valeur :</span><span class="sxs-lookup"><span data-stu-id="9768a-134">For example, consider an entity type with a `Name` property defined as a value object:</span></span>

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

<span data-ttu-id="9768a-135">Cette valeur est mappée au format JSON dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="9768a-135">This is mapped to JSON in the database:</span></span>

<!--
            modelBuilder.Entity<Customer>()
                .Property(e => e.Name)
                .HasConversion(
                    v => JsonSerializer.Serialize(v, null),
                    v => JsonSerializer.Deserialize<Name>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=ConfigureCompositeValueObject)]

<span data-ttu-id="9768a-136">Une requête peut maintenant être exécutée à l’aide `Contains` de ou `FreeText` même si le type de la propriété n’est `Name` pas `string` .</span><span class="sxs-lookup"><span data-stu-id="9768a-136">A query can now be executed using `Contains` or `FreeText` even though the type of the property is `Name` not `string`.</span></span> <span data-ttu-id="9768a-137">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="9768a-137">For example:</span></span>

<!--
        var result = context.Customers.Where(e => EF.Functions.Contains(e.Name, "Martin")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ContainsFreeTextSample.cs?name=Query)]

<span data-ttu-id="9768a-138">Cela génère le code SQL suivant, lors de l’utilisation de SQL Server :</span><span class="sxs-lookup"><span data-stu-id="9768a-138">This generates the following SQL, when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE CONTAINS([c].[Name], N'Martin')
```

## <a name="ef-core-60-preview-1"></a><span data-ttu-id="9768a-139">EF Core 6,0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="9768a-139">EF Core 6.0 Preview 1</span></span>

### <a name="unicodeattribute"></a><span data-ttu-id="9768a-140">UnicodeAttribute</span><span class="sxs-lookup"><span data-stu-id="9768a-140">UnicodeAttribute</span></span>

<span data-ttu-id="9768a-141">GitHub problème : [#19794](https://github.com/dotnet/efcore/issues/19794).</span><span class="sxs-lookup"><span data-stu-id="9768a-141">GitHub Issue: [#19794](https://github.com/dotnet/efcore/issues/19794).</span></span> <span data-ttu-id="9768a-142">Cette fonctionnalité a été fournie par [@RaymondHuy](https://github.com/RaymondHuy) .</span><span class="sxs-lookup"><span data-stu-id="9768a-142">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="9768a-143">À compter de EF Core 6,0, une propriété de type chaîne peut désormais être mappée à une colonne non-Unicode à l’aide d’un attribut _de mappage sans spécifier directement le type de la base de données_.</span><span class="sxs-lookup"><span data-stu-id="9768a-143">Starting with EF Core 6.0, a string property can now be mapped to a non-Unicode column using a mapping attribute _without specifying the database type directly_.</span></span> <span data-ttu-id="9768a-144">Par exemple, considérez un `Book` type d’entité avec une propriété pour le [numéro ISBN (International Standard Book Number)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) au format « ISBN 978-3-16-148410-0 » :</span><span class="sxs-lookup"><span data-stu-id="9768a-144">For example, consider a `Book` entity type with a property for the [International Standard Book Number (ISBN)](https://en.wikipedia.org/wiki/International_Standard_Book_Number) in the form "ISBN 978-3-16-148410-0":</span></span>

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

<span data-ttu-id="9768a-145">Étant donné que les ISBNs ne peuvent pas contenir de caractères non-Unicode, l' `Unicode` attribut entraîne l’utilisation d’un type de chaîne non-Unicode.</span><span class="sxs-lookup"><span data-stu-id="9768a-145">Since ISBNs cannot contain any non-unicode characters, the `Unicode` attribute will cause a non-Unicode string type to be used.</span></span> <span data-ttu-id="9768a-146">En outre, `MaxLength` est utilisé pour limiter la taille de la colonne de base de données.</span><span class="sxs-lookup"><span data-stu-id="9768a-146">In addition, `MaxLength` is used to limit the size of the database column.</span></span> <span data-ttu-id="9768a-147">Par exemple, lors de l’utilisation de SQL Server, cela aboutit à une colonne de base de données `varchar(22)` :</span><span class="sxs-lookup"><span data-stu-id="9768a-147">For example, when using SQL Server, this results in a database column of `varchar(22)`:</span></span>

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="9768a-148">EF Core mappe les propriétés de chaîne aux colonnes Unicode par défaut.</span><span class="sxs-lookup"><span data-stu-id="9768a-148">EF Core maps string properties to Unicode columns by default.</span></span> <span data-ttu-id="9768a-149">`UnicodeAttribute` est ignoré lorsque le système de base de données prend en charge uniquement les types Unicode.</span><span class="sxs-lookup"><span data-stu-id="9768a-149">`UnicodeAttribute` is ignored when the database system supports only Unicode types.</span></span>

### <a name="precisionattribute"></a><span data-ttu-id="9768a-150">PrecisionAttribute</span><span class="sxs-lookup"><span data-stu-id="9768a-150">PrecisionAttribute</span></span>

<span data-ttu-id="9768a-151">GitHub problème : [#17914](https://github.com/dotnet/efcore/issues/17914).</span><span class="sxs-lookup"><span data-stu-id="9768a-151">GitHub Issue: [#17914](https://github.com/dotnet/efcore/issues/17914).</span></span> <span data-ttu-id="9768a-152">Cette fonctionnalité a été fournie par [@RaymondHuy](https://github.com/RaymondHuy) .</span><span class="sxs-lookup"><span data-stu-id="9768a-152">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="9768a-153">La précision et l’échelle d’une colonne de base de données peuvent désormais être configurées à l’aide d’attributs de mappage _sans spécifier directement le type de base de données_.</span><span class="sxs-lookup"><span data-stu-id="9768a-153">The precision and scale of a database column can now be configured using mapping attributes _without specifying the database type directly_.</span></span> <span data-ttu-id="9768a-154">Par exemple, considérez un `Product` type d’entité avec une propriété décimale `Price` :</span><span class="sxs-lookup"><span data-stu-id="9768a-154">For example, consider a `Product` entity type with a decimal `Price` property:</span></span>

<!--
    public class Product
    {
        public int Id { get; set; }

        [Precision(precision: 10, scale: 2)]
        public decimal Price { get; set; }
    }
-->
[!code-csharp[ProductEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/PrecisionAttributeSample.cs?name=ProductEntityType)]

<span data-ttu-id="9768a-155">EF Core mappera cette propriété à une colonne de base de données avec une précision de 10 et une échelle de 2.</span><span class="sxs-lookup"><span data-stu-id="9768a-155">EF Core will map this property to a database column with precision 10 and scale 2.</span></span> <span data-ttu-id="9768a-156">Par exemple, sur SQL Server :</span><span class="sxs-lookup"><span data-stu-id="9768a-156">For example, on SQL Server:</span></span>

```sql
CREATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### <a name="entitytypeconfigurationattribute"></a><span data-ttu-id="9768a-157">EntityTypeConfigurationAttribute</span><span class="sxs-lookup"><span data-stu-id="9768a-157">EntityTypeConfigurationAttribute</span></span>

<span data-ttu-id="9768a-158">GitHub problème : [#23163](https://github.com/dotnet/efcore/issues/23163).</span><span class="sxs-lookup"><span data-stu-id="9768a-158">GitHub Issue: [#23163](https://github.com/dotnet/efcore/issues/23163).</span></span> <span data-ttu-id="9768a-159">Cette fonctionnalité a été fournie par [@KaloyanIT](https://github.com/KaloyanIT) .</span><span class="sxs-lookup"><span data-stu-id="9768a-159">This feature was contributed by [@KaloyanIT](https://github.com/KaloyanIT).</span></span>

<span data-ttu-id="9768a-160"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> les instances autorisent <xref:Microsoft.EntityFrameworkCore.ModelBuilder> la configuration de chaque type d’entité dans sa propre classe de configuration.</span><span class="sxs-lookup"><span data-stu-id="9768a-160"><xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> instances allow <xref:Microsoft.EntityFrameworkCore.ModelBuilder> configuration for a each entity type to be contained in its own configuration class.</span></span> <span data-ttu-id="9768a-161">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="9768a-161">For example:</span></span>

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

<span data-ttu-id="9768a-162">Normalement, cette classe de configuration doit être instanciée et appelée dans à partir de <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="9768a-162">Normally, this configuration class must be instantiated and called into from <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="9768a-163">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="9768a-163">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    new BookConfiguration().Configure(modelBuilder.Entity<Book>());
}
```

<span data-ttu-id="9768a-164">À partir de EF Core 6,0, un `EntityTypeConfigurationAttribute` peut être placé sur le type d’entité afin que EF Core puisse trouver et utiliser la configuration appropriée.</span><span class="sxs-lookup"><span data-stu-id="9768a-164">Starting with EF Core 6.0, an `EntityTypeConfigurationAttribute` can be placed on the entity type such that EF Core can find and use appropriate configuration.</span></span> <span data-ttu-id="9768a-165">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="9768a-165">For example:</span></span>

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

<span data-ttu-id="9768a-166">Cet attribut signifie que EF Core utilise l’implémentation spécifiée `IEntityTypeConfiguration` chaque fois que le `Book` type d’entité est inclus dans un modèle.</span><span class="sxs-lookup"><span data-stu-id="9768a-166">This attribute means that EF Core will use the specified `IEntityTypeConfiguration` implementation whenever the `Book` entity type is included in a model.</span></span> <span data-ttu-id="9768a-167">Le type d’entité est inclus dans un modèle à l’aide de l’un des mécanismes normaux.</span><span class="sxs-lookup"><span data-stu-id="9768a-167">The entity type is included in a model using one of the normal mechanisms.</span></span> <span data-ttu-id="9768a-168">Par exemple, en créant une <xref:Microsoft.EntityFrameworkCore.DbSet%601> propriété pour le type d’entité :</span><span class="sxs-lookup"><span data-stu-id="9768a-168">For example, by creating a <xref:Microsoft.EntityFrameworkCore.DbSet%601> property for the entity type:</span></span>

<!--
public class BooksContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    //...
-->
[!code-csharp[DbContext](../../../../samples/core/Miscellaneous/NewInEFCore6/EntityTypeConfigurationAttributeSample.cs?name=DbContext)]

<span data-ttu-id="9768a-169">Ou en l’inscrivant dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> :</span><span class="sxs-lookup"><span data-stu-id="9768a-169">Or by registering it in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>();
}
```

> [!NOTE]
> <span data-ttu-id="9768a-170">`EntityTypeConfigurationAttribute` les types ne seront pas détectés automatiquement dans un assembly.</span><span class="sxs-lookup"><span data-stu-id="9768a-170">`EntityTypeConfigurationAttribute` types will not be automatically discovered in an assembly.</span></span> <span data-ttu-id="9768a-171">Les types d’entités doivent être ajoutés au modèle avant que l’attribut ne soit découvert sur ce type d’entité.</span><span class="sxs-lookup"><span data-stu-id="9768a-171">Entity types must be added to the model before the attribute will be discovered on that entity type.</span></span>

### <a name="translate-tostring-on-sqlite"></a><span data-ttu-id="9768a-172">Traduire ToString sur SQLite</span><span class="sxs-lookup"><span data-stu-id="9768a-172">Translate ToString on SQLite</span></span>

<span data-ttu-id="9768a-173">GitHub problème : [#17223](https://github.com/dotnet/efcore/issues/17223).</span><span class="sxs-lookup"><span data-stu-id="9768a-173">GitHub Issue: [#17223](https://github.com/dotnet/efcore/issues/17223).</span></span> <span data-ttu-id="9768a-174">Cette fonctionnalité a été fournie par [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="9768a-174">This feature was contributed by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span>

<span data-ttu-id="9768a-175">Les appels à <xref:System.Object.ToString> sont désormais traduits en SQL lors de l’utilisation du fournisseur de base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="9768a-175">Calls to <xref:System.Object.ToString> are now translated to SQL when using the SQLite database provider.</span></span> <span data-ttu-id="9768a-176">Cela peut être utile pour les recherches de texte impliquant des colonnes qui ne sont pas des chaînes.</span><span class="sxs-lookup"><span data-stu-id="9768a-176">This can be useful for text searches involving non-string columns.</span></span> <span data-ttu-id="9768a-177">Par exemple, considérez un `User` type d’entité qui stocke les numéros de téléphone comme valeurs numériques :</span><span class="sxs-lookup"><span data-stu-id="9768a-177">For example, consider a `User` entity type that stores phone numbers as numeric values:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public long PhoneNumber { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=UserEntityType)]

<span data-ttu-id="9768a-178">`ToString` peut être utilisé pour convertir le nombre en une chaîne dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9768a-178">`ToString` can be used to convert the number to a string in the database.</span></span> <span data-ttu-id="9768a-179">Nous pouvons ensuite utiliser cette chaîne avec une fonction telle que `LIKE` pour rechercher des nombres qui correspondent à un modèle.</span><span class="sxs-lookup"><span data-stu-id="9768a-179">We can then use this string with a function such as `LIKE` to find numbers that match a pattern.</span></span> <span data-ttu-id="9768a-180">Par exemple, pour rechercher tous les nombres contenant 555 :</span><span class="sxs-lookup"><span data-stu-id="9768a-180">For example, to find all numbers containing 555:</span></span>

<!--
var users = context.Users.Where(u => EF.Functions.Like(u.PhoneNumber.ToString(), "%555%")).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/ToStringTranslationSample.cs?name=Query)]

<span data-ttu-id="9768a-181">Cela se traduit par le code SQL suivant lors de l’utilisation d’une base de données SQLite :</span><span class="sxs-lookup"><span data-stu-id="9768a-181">This translates to the following SQL when using a SQLite database:</span></span>

```sql
SELECT COUNT(*)
FROM "Users" AS "u"
WHERE CAST("u"."PhoneNumber" AS TEXT) LIKE '%555%'
```

<span data-ttu-id="9768a-182">Notez que la traduction de <xref:System.Object.ToString> pour SQL Server est déjà prise en charge dans EF Core 5,0 et peut également être prise en charge par d’autres fournisseurs de bases de données.</span><span class="sxs-lookup"><span data-stu-id="9768a-182">Note that translation of <xref:System.Object.ToString> for SQL Server is already supported in EF Core 5.0, and may also be supported by other database providers.</span></span>

### <a name="effunctionsrandom"></a><span data-ttu-id="9768a-183">EF. Functions. Random</span><span class="sxs-lookup"><span data-stu-id="9768a-183">EF.Functions.Random</span></span>

<span data-ttu-id="9768a-184">GitHub problème : [#16141](https://github.com/dotnet/efcore/issues/16141).</span><span class="sxs-lookup"><span data-stu-id="9768a-184">GitHub Issue: [#16141](https://github.com/dotnet/efcore/issues/16141).</span></span> <span data-ttu-id="9768a-185">Cette fonctionnalité a été fournie par [@RaymondHuy](https://github.com/RaymondHuy) .</span><span class="sxs-lookup"><span data-stu-id="9768a-185">This feature was contributed by [@RaymondHuy](https://github.com/RaymondHuy).</span></span>

<span data-ttu-id="9768a-186">`EF.Functions.Random` mappe à une fonction de base de données qui retourne un nombre Pseudo-aléatoire compris entre 0 et 1 exclusif.</span><span class="sxs-lookup"><span data-stu-id="9768a-186">`EF.Functions.Random` maps to a database function returning a pseudo-random number between 0 and 1 exclusive.</span></span> <span data-ttu-id="9768a-187">Les traductions ont été implémentées dans le EF Core référentiel pour SQL Server, SQLite et Cosmos.</span><span class="sxs-lookup"><span data-stu-id="9768a-187">Translations have been implemented in the EF Core repo for SQL Server, SQLite, and Cosmos.</span></span> <span data-ttu-id="9768a-188">Par exemple, considérez un `User` type d’entité avec une `Popularity` propriété :</span><span class="sxs-lookup"><span data-stu-id="9768a-188">For example, consider a `User` entity type with a `Popularity` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }
        public string Username { get; set; }
        public int Popularity { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=UserEntityType)]

<span data-ttu-id="9768a-189">`Popularity` peut avoir des valeurs comprises entre 1 et 5 inclus.</span><span class="sxs-lookup"><span data-stu-id="9768a-189">`Popularity` can have values from 1 to 5 inclusive.</span></span> <span data-ttu-id="9768a-190">À l’aide de, `EF.Functions.Random` nous pouvons écrire une requête pour renvoyer tous les utilisateurs avec une popularité choisie de manière aléatoire :</span><span class="sxs-lookup"><span data-stu-id="9768a-190">Using `EF.Functions.Random` we can write a query to return all users with a randomly chosen popularity:</span></span>

<!--
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/RandomFunctionSample.cs?name=Query)]

<span data-ttu-id="9768a-191">Cela se traduit par le code SQL suivant lors de l’utilisation d’une base de données SQL Server :</span><span class="sxs-lookup"><span data-stu-id="9768a-191">This translates to the following SQL when using a SQL Server database:</span></span>

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

### <a name="support-for-sql-server-sparse-columns"></a><span data-ttu-id="9768a-192">Prise en charge des colonnes éparses SQL Server</span><span class="sxs-lookup"><span data-stu-id="9768a-192">Support for SQL Server sparse columns</span></span>

<span data-ttu-id="9768a-193">GitHub problème : [#8023](https://github.com/dotnet/efcore/issues/8023).</span><span class="sxs-lookup"><span data-stu-id="9768a-193">GitHub Issue: [#8023](https://github.com/dotnet/efcore/issues/8023).</span></span>

<span data-ttu-id="9768a-194">SQL Server [colonnes éparses](/sql/relational-databases/tables/use-sparse-columns) sont des colonnes ordinaires optimisées pour stocker des valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="9768a-194">SQL Server [sparse columns](/sql/relational-databases/tables/use-sparse-columns) are ordinary columns that are optimized to store null values.</span></span> <span data-ttu-id="9768a-195">Cela peut être utile lors de l’utilisation du [mappage d’héritage TPH](xref:core/modeling/inheritance) où les propriétés d’un sous-type rarement utilisé entraînent des valeurs de colonne null pour la plupart des lignes de la table.</span><span class="sxs-lookup"><span data-stu-id="9768a-195">This can be useful when using [TPH inheritance mapping](xref:core/modeling/inheritance) where properties of a rarely used subtype will result in null column values for most rows in the table.</span></span> <span data-ttu-id="9768a-196">Par exemple, considérez une `ForumModerator` classe qui s’étend de `ForumUser` :</span><span class="sxs-lookup"><span data-stu-id="9768a-196">For example, consider a `ForumModerator` class that extends from `ForumUser`:</span></span>

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

<span data-ttu-id="9768a-197">Il peut y avoir des millions d’utilisateurs, avec seulement quelques modérateurs.</span><span class="sxs-lookup"><span data-stu-id="9768a-197">There may be millions of users, with only a handful of these being moderators.</span></span> <span data-ttu-id="9768a-198">Cela signifie que le mappage de `ForumName` As Sparse peut être pertinent ici.</span><span class="sxs-lookup"><span data-stu-id="9768a-198">This means mapping the `ForumName` as sparse might make sense here.</span></span> <span data-ttu-id="9768a-199">Cela peut maintenant être configuré à l’aide `IsSparse` de dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="9768a-199">This can now be configured using `IsSparse` in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="9768a-200">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="9768a-200">For example:</span></span>

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

<span data-ttu-id="9768a-201">EF Core migrations marque la colonne comme étant éparse.</span><span class="sxs-lookup"><span data-stu-id="9768a-201">EF Core migrations will then mark the column as sparse.</span></span> <span data-ttu-id="9768a-202">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="9768a-202">For example:</span></span>

```sql
CREATE TABLE [ForumUser] (
    [Id] int NOT NULL IDENTITY,
    [Username] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [ForumName] nvarchar(max) SPARSE NULL,
    CONSTRAINT [PK_ForumUser] PRIMARY KEY ([Id]));
```

> [!NOTE]
> <span data-ttu-id="9768a-203">Les colonnes éparses présentent des limitations.</span><span class="sxs-lookup"><span data-stu-id="9768a-203">Sparse columns have limitations.</span></span> <span data-ttu-id="9768a-204">Veillez à lire la [documentation sur les colonnes éparses SQL Server](/sql/relational-databases/tables/use-sparse-columns) pour vous assurer que les colonnes éparses sont le bon choix pour votre scénario.</span><span class="sxs-lookup"><span data-stu-id="9768a-204">Make sure to read the [SQL Server sparse columns documentation](/sql/relational-databases/tables/use-sparse-columns) to ensure that sparse columns are the right choice for your scenario.</span></span>

### <a name="in-memory-database-validate-required-properties-are-not-null"></a><span data-ttu-id="9768a-205">Base de données en mémoire : les propriétés Validate required ne sont pas null</span><span class="sxs-lookup"><span data-stu-id="9768a-205">In-memory database: validate required properties are not null</span></span>

<span data-ttu-id="9768a-206">GitHub problème : [#10613](https://github.com/dotnet/efcore/issues/10613).</span><span class="sxs-lookup"><span data-stu-id="9768a-206">GitHub Issue: [#10613](https://github.com/dotnet/efcore/issues/10613).</span></span> <span data-ttu-id="9768a-207">Cette fonctionnalité a été fournie par [@fagnercarvalho](https://github.com/fagnercarvalho) .</span><span class="sxs-lookup"><span data-stu-id="9768a-207">This feature was contributed by [@fagnercarvalho](https://github.com/fagnercarvalho).</span></span>

<span data-ttu-id="9768a-208">La base de données en mémoire EF Core lèvera à présent une exception en cas de tentative d’enregistrement d’une valeur null pour une propriété marquée comme obligatoire.</span><span class="sxs-lookup"><span data-stu-id="9768a-208">The EF Core in-memory database will now throw an exception if an attempt is made to save a null value for a property marked as required.</span></span> <span data-ttu-id="9768a-209">Par exemple, considérez un `User` type avec une `Username` propriété obligatoire :</span><span class="sxs-lookup"><span data-stu-id="9768a-209">For example, consider a `User` type with a required `Username` property:</span></span>

<!--
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string Username { get; set; }
    }
-->
[!code-csharp[UserEntityType](../../../../samples/core/Miscellaneous/NewInEFCore6/InMemoryRequiredPropertiesSample.cs?name=UserEntityType)]

<span data-ttu-id="9768a-210">Si vous tentez d’enregistrer une entité avec une valeur null `Username` , l’exception suivante se produit :</span><span class="sxs-lookup"><span data-stu-id="9768a-210">Attempting to save an entity with a null `Username` will result in the following exception:</span></span>

> <span data-ttu-id="9768a-211">Microsoft. EntityFrameworkCore. exception dbupdateexception : les propriétés requises « {'username'} » sont manquantes pour l’instance du type d’entité « User » avec la valeur de clé « {ID : 1} ».</span><span class="sxs-lookup"><span data-stu-id="9768a-211">Microsoft.EntityFrameworkCore.DbUpdateException: Required properties '{'Username'}' are missing for the instance of entity type 'User' with the key value '{Id: 1}'.</span></span>

<span data-ttu-id="9768a-212">Cette validation peut être désactivée si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="9768a-212">This validation can be disabled if necessary.</span></span> <span data-ttu-id="9768a-213">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="9768a-213">For example:</span></span>

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

### <a name="improved-sql-server-translation-for-isnullorwhitespace"></a><span data-ttu-id="9768a-214">Traduction de SQL Server améliorée pour IsNullOrWhitespace</span><span class="sxs-lookup"><span data-stu-id="9768a-214">Improved SQL Server translation for IsNullOrWhitespace</span></span>

<span data-ttu-id="9768a-215">GitHub problème : [#22916](https://github.com/dotnet/efcore/issues/22916).</span><span class="sxs-lookup"><span data-stu-id="9768a-215">GitHub Issue: [#22916](https://github.com/dotnet/efcore/issues/22916).</span></span> <span data-ttu-id="9768a-216">Cette fonctionnalité a été fournie par [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="9768a-216">This feature was contributed by [@Marusyk](https://github.com/Marusyk).</span></span>

<span data-ttu-id="9768a-217">Considérez la requête suivante :</span><span class="sxs-lookup"><span data-stu-id="9768a-217">Consider the following query:</span></span>

<!--
        var users = context.Users.Where(
            e => string.IsNullOrWhiteSpace(e.FirstName)
                 || string.IsNullOrWhiteSpace(e.LastName)).ToList();
-->
[!code-csharp[Query](../../../../samples/core/Miscellaneous/NewInEFCore6/IsNullOrWhitespaceSample.cs?name=Query)]

<span data-ttu-id="9768a-218">Avant le EF Core 6,0, le code suivant a été converti en SQL Server :</span><span class="sxs-lookup"><span data-stu-id="9768a-218">Before EF Core 6.0, this was translated to the following on SQL Server:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR (LTRIM(RTRIM([u].[FirstName])) = N'')) OR ([u].[LastName] IS NULL OR (LTRIM(RTRIM([u].[LastName])) = N''))
```

<span data-ttu-id="9768a-219">Cette traduction a été améliorée pour EF Core 6,0 à :</span><span class="sxs-lookup"><span data-stu-id="9768a-219">This translation has been improved for EF Core 6.0 to:</span></span>

```sql
SELECT [u].[Id], [u].[FirstName], [u].[LastName]
FROM [Users] AS [u]
WHERE ([u].[FirstName] IS NULL OR ([u].[FirstName] = N'')) OR ([u].[LastName] IS NULL OR ([u].[LastName] = N''))
```

### <a name="database-comments-are-scaffolded-to-code-comments"></a><span data-ttu-id="9768a-220">Les commentaires de base de données sont générés au format de commentaires de code</span><span class="sxs-lookup"><span data-stu-id="9768a-220">Database comments are scaffolded to code comments</span></span>

<span data-ttu-id="9768a-221">GitHub problème : [#19113](https://github.com/dotnet/efcore/issues/19113).</span><span class="sxs-lookup"><span data-stu-id="9768a-221">GitHub Issue: [#19113](https://github.com/dotnet/efcore/issues/19113).</span></span> <span data-ttu-id="9768a-222">Cette fonctionnalité a été fournie par [@ErikEJ](https://github.com/ErikEJ) .</span><span class="sxs-lookup"><span data-stu-id="9768a-222">This feature was contributed by [@ErikEJ](https://github.com/ErikEJ).</span></span>

<span data-ttu-id="9768a-223">Les commentaires sur les tables et les colonnes SQL sont désormais générés automatiquement dans les types d’entités créés lors de l' [ingénierie à rebours d’une EF Core modèle](xref:core/managing-schemas/scaffolding) à partir d’une base de données SQL Server existante.</span><span class="sxs-lookup"><span data-stu-id="9768a-223">Comments on SQL tables and columns are now scaffolded into the entity types created when [reverse-engineering an EF Core model](xref:core/managing-schemas/scaffolding) from an existing SQL Server database.</span></span> <span data-ttu-id="9768a-224">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="9768a-224">For example:</span></span>

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

## <a name="microsoftdatasqlite-60-preview-1"></a><span data-ttu-id="9768a-225">Microsoft. Data. sqlite 6,0 Preview 1</span><span class="sxs-lookup"><span data-stu-id="9768a-225">Microsoft.Data.Sqlite 6.0 Preview 1</span></span>

> [!TIP]
> <span data-ttu-id="9768a-226">Vous pouvez exécuter et déboguer dans tous les exemples Preview 1 ci-dessous en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).</span><span class="sxs-lookup"><span data-stu-id="9768a-226">You can run and debug into all the preview 1 samples shown below by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/NewInEFCore6).</span></span>

### <a name="savepoints-api"></a><span data-ttu-id="9768a-227">API d’enregistrement des points de enregistrement</span><span class="sxs-lookup"><span data-stu-id="9768a-227">Savepoints API</span></span>

<span data-ttu-id="9768a-228">GitHub problème : [#20228](https://github.com/dotnet/efcore/issues/20228).</span><span class="sxs-lookup"><span data-stu-id="9768a-228">GitHub Issue: [#20228](https://github.com/dotnet/efcore/issues/20228).</span></span>

<span data-ttu-id="9768a-229">Nous sommes en cours de normalisation sur [une API commune pour les points d’enregistrement dans les fournisseurs ADO.net](https://github.com/dotnet/runtime/issues/33397).</span><span class="sxs-lookup"><span data-stu-id="9768a-229">We have been standardizing on [a common API for savepoints in ADO.NET providers](https://github.com/dotnet/runtime/issues/33397).</span></span> <span data-ttu-id="9768a-230">Microsoft. Data. sqlite prend désormais en charge cette API, notamment :</span><span class="sxs-lookup"><span data-stu-id="9768a-230">Microsoft.Data.Sqlite now supports this API, including:</span></span>

- <span data-ttu-id="9768a-231"><xref:System.Data.Common.DbTransaction.Save(System.String)> pour créer un point d’enregistrement dans la transaction</span><span class="sxs-lookup"><span data-stu-id="9768a-231"><xref:System.Data.Common.DbTransaction.Save(System.String)> to create a savepoint in the transaction</span></span>
- <span data-ttu-id="9768a-232"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> pour restaurer un point de sauvegarde précédent</span><span class="sxs-lookup"><span data-stu-id="9768a-232"><xref:System.Data.Common.DbTransaction.Rollback(System.String)> to roll back to a previous savepoint</span></span>
- <span data-ttu-id="9768a-233"><xref:System.Data.Common.DbTransaction.Release(System.String)> pour libérer un point d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="9768a-233"><xref:System.Data.Common.DbTransaction.Release(System.String)> to release a savepoint</span></span>

<span data-ttu-id="9768a-234">L’utilisation d’un point de sauvegarde permet de restaurer une partie d’une transaction sans restaurer toute la transaction.</span><span class="sxs-lookup"><span data-stu-id="9768a-234">Using a savepoint allows part of a transaction to be rolled back without rolling back the entire transaction.</span></span> <span data-ttu-id="9768a-235">Par exemple, le code ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="9768a-235">For example, the code below:</span></span>

- <span data-ttu-id="9768a-236">Crée une transaction</span><span class="sxs-lookup"><span data-stu-id="9768a-236">Creates a transaction</span></span>
- <span data-ttu-id="9768a-237">Envoie une mise à jour à la base de données.</span><span class="sxs-lookup"><span data-stu-id="9768a-237">Sends an update to the database</span></span>
- <span data-ttu-id="9768a-238">Crée un point d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="9768a-238">Creates a savepoint</span></span>
- <span data-ttu-id="9768a-239">Envoie une autre mise à jour à la base de données</span><span class="sxs-lookup"><span data-stu-id="9768a-239">Sends another update to the database</span></span>
- <span data-ttu-id="9768a-240">Restaure le point de sauvegarde précédent créé</span><span class="sxs-lookup"><span data-stu-id="9768a-240">Rolls back to the savepoint previous created</span></span>
- <span data-ttu-id="9768a-241">Valide la transaction</span><span class="sxs-lookup"><span data-stu-id="9768a-241">Commits the transaction</span></span>

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

<span data-ttu-id="9768a-242">Cela entraîne la validation de la première mise à jour dans la base de données, tandis que la deuxième mise à jour n’est pas validée, car le point de sauvegarde a été restauré avant la validation de la transaction.</span><span class="sxs-lookup"><span data-stu-id="9768a-242">This will result in the first update being committed to the database, while the second update is not committed since the savepoint was rolled back before committing the transaction.</span></span>

### <a name="command-timeout-in-the-connection-string"></a><span data-ttu-id="9768a-243">Délai d’expiration de la commande dans la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="9768a-243">Command timeout in the connection string</span></span>

<span data-ttu-id="9768a-244">GitHub problème : [#22505](https://github.com/dotnet/efcore/issues/22505).</span><span class="sxs-lookup"><span data-stu-id="9768a-244">GitHub Issue: [#22505](https://github.com/dotnet/efcore/issues/22505).</span></span> <span data-ttu-id="9768a-245">Cette fonctionnalité a été fournie par [@nmichels](https://github.com/nmichels) .</span><span class="sxs-lookup"><span data-stu-id="9768a-245">This feature was contributed by [@nmichels](https://github.com/nmichels).</span></span>

<span data-ttu-id="9768a-246">Les fournisseurs ADO.NET prennent en charge deux délais d’attente distincts :</span><span class="sxs-lookup"><span data-stu-id="9768a-246">ADO.NET providers support two distinct timeouts:</span></span>

- <span data-ttu-id="9768a-247">Délai d’attente de la connexion, qui détermine la durée maximale d’attente lors de l’établissement d’une connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="9768a-247">The connection timeout, which determines the maximum time to wait when making a connection to the database.</span></span>
- <span data-ttu-id="9768a-248">Délai d’attente de la commande, qui détermine la durée maximale d’attente d’une commande pour s’exécuter.</span><span class="sxs-lookup"><span data-stu-id="9768a-248">The command timeout, which determines the maximum time to wait for a command to complete executing.</span></span>

<span data-ttu-id="9768a-249">Le délai d’expiration de la commande peut être défini à partir du code à l’aide de <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="9768a-249">The command timeout can be set from code using <xref:System.Data.Common.DbCommand.CommandTimeout?displayProperty=nameWithType>.</span></span> <span data-ttu-id="9768a-250">De nombreux fournisseurs exposent désormais également ce délai d’attente de commande dans la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="9768a-250">Many providers are now also exposing this command timeout in the connection string.</span></span> <span data-ttu-id="9768a-251">Microsoft. Data. sqlite suit cette tendance avec le `Command Timeout` mot clé de chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="9768a-251">Microsoft.Data.Sqlite is following this trend with the `Command Timeout` connection string keyword.</span></span> <span data-ttu-id="9768a-252">Par exemple, `"Command Timeout=60;DataSource=test.db"` utilise 60 secondes comme délai d’expiration par défaut pour les commandes créées par la connexion.</span><span class="sxs-lookup"><span data-stu-id="9768a-252">For example, `"Command Timeout=60;DataSource=test.db"` will use 60 seconds as the default timeout for commands created by the connection.</span></span>

> [!TIP]
> <span data-ttu-id="9768a-253">SQLite traite `Default Timeout` comme synonyme de `Command Timeout` et peut donc être utilisé à la place si préféré.</span><span class="sxs-lookup"><span data-stu-id="9768a-253">Sqlite treats `Default Timeout` as a synonym for `Command Timeout` and so can be used instead if preferred.</span></span>
