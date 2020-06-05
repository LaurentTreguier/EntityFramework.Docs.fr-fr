---
title: Nouveautés de EF Core 5,0
description: Vue d’ensemble des nouvelles fonctionnalités de EF Core 5,0
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 45d851a4b08a26dda0c24e20c79f42964fa4fae4
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418937"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="cbb99-103">Nouveautés de EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="cbb99-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="cbb99-104">EF Core 5,0 est actuellement en cours de développement.</span><span class="sxs-lookup"><span data-stu-id="cbb99-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="cbb99-105">Cette page contient une vue d’ensemble des changements intéressants introduits dans chaque version préliminaire.</span><span class="sxs-lookup"><span data-stu-id="cbb99-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="cbb99-106">Cette page ne duplique pas le [plan pour EF Core 5,0](plan.md).</span><span class="sxs-lookup"><span data-stu-id="cbb99-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="cbb99-107">Le plan décrit les thèmes globaux pour EF Core 5,0, y compris tout ce que nous envisageons d’inclure avant d’expédier la version finale.</span><span class="sxs-lookup"><span data-stu-id="cbb99-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="cbb99-108">Nous ajouterons des liens à partir d’ici à la documentation officielle au fur et à mesure de sa publication.</span><span class="sxs-lookup"><span data-stu-id="cbb99-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-5"></a><span data-ttu-id="cbb99-109">Préversion 5</span><span class="sxs-lookup"><span data-stu-id="cbb99-109">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="cbb99-110">Classements de base de données</span><span class="sxs-lookup"><span data-stu-id="cbb99-110">Database collations</span></span>

<span data-ttu-id="cbb99-111">Le classement par défaut d’une base de données peut désormais être spécifié dans le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="cbb99-111">The default collation for a database can now be specified in the EF model.</span></span>
<span data-ttu-id="cbb99-112">Cela passera à des migrations générées pour définir le classement lors de la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="cbb99-112">This will flow through to generated migrations to set the collation when the database is created.</span></span>
<span data-ttu-id="cbb99-113">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-113">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="cbb99-114">Les migrations génèrent ensuite les éléments suivants pour créer la base de données sur SQL Server :</span><span class="sxs-lookup"><span data-stu-id="cbb99-114">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="cbb99-115">Le classement à utiliser pour des colonnes de base de données spécifiques peut également être spécifié.</span><span class="sxs-lookup"><span data-stu-id="cbb99-115">The collation to use for specific database columns can also be specified.</span></span>
<span data-ttu-id="cbb99-116">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-116">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="cbb99-117">Pour ceux qui n’utilisent pas de migrations, les classements sont à présent rétroconçus de la base de données lors de la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="cbb99-117">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="cbb99-118">Enfin, le `EF.Functions.Collate()` autorise les requêtes ad hoc utilisant des classements différents.</span><span class="sxs-lookup"><span data-stu-id="cbb99-118">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span>
<span data-ttu-id="cbb99-119">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-119">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="cbb99-120">La requête suivante est générée pour SQL Server :</span><span class="sxs-lookup"><span data-stu-id="cbb99-120">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="cbb99-121">Notez que les classements ad hoc doivent être utilisés avec précaution, car ils peuvent avoir un impact négatif sur les performances de la base de données.</span><span class="sxs-lookup"><span data-stu-id="cbb99-121">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="cbb99-122">La documentation est suivie d’un problème [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="cbb99-122">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="cbb99-123">Transmettre les arguments dans IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="cbb99-123">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="cbb99-124">Les arguments sont à présent transmis à partir de la ligne de commande dans la `CreateDbContext` méthode de [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="cbb99-124">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="cbb99-125">Par exemple, pour indiquer qu’il s’agit d’une build dev, un argument personnalisé (par exemple, `dev` ) peut être passé sur la ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="cbb99-125">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="cbb99-126">Cet argument est ensuite transmis à la fabrique, où il peut être utilisé pour contrôler la façon dont le contexte est créé et initialisé.</span><span class="sxs-lookup"><span data-stu-id="cbb99-126">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span>
<span data-ttu-id="cbb99-127">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-127">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="cbb99-128">La documentation est suivie d’un problème [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="cbb99-128">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="cbb99-129">Requêtes de suivi sans suivi de la résolution d’identité</span><span class="sxs-lookup"><span data-stu-id="cbb99-129">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="cbb99-130">Les requêtes de non-suivi peuvent maintenant être configurées pour effectuer la résolution d’identité.</span><span class="sxs-lookup"><span data-stu-id="cbb99-130">No-tracking queries can now be configured to perform identity resolution.</span></span>
<span data-ttu-id="cbb99-131">Par exemple, la requête suivante crée une nouvelle instance de blog pour chaque publication, même si chaque blog a la même clé primaire.</span><span class="sxs-lookup"><span data-stu-id="cbb99-131">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="cbb99-132">Toutefois, au détriment d’un peu plus lentement et en utilisant toujours plus de mémoire, cette requête peut être modifiée pour garantir qu’une seule instance de blog est créée :</span><span class="sxs-lookup"><span data-stu-id="cbb99-132">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="cbb99-133">Notez que cela n’est utile que pour les requêtes sans suivi, car toutes les requêtes de suivi présentent déjà ce comportement.</span><span class="sxs-lookup"><span data-stu-id="cbb99-133">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="cbb99-134">En outre, la syntaxe de l’API suivante `PerformIdentityResolution` sera modifiée.</span><span class="sxs-lookup"><span data-stu-id="cbb99-134">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span>
<span data-ttu-id="cbb99-135">Consultez [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="cbb99-135">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="cbb99-136">La documentation est suivie d’un problème [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="cbb99-136">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="cbb99-137">Colonnes calculées (persistantes) stockées</span><span class="sxs-lookup"><span data-stu-id="cbb99-137">Stored (persisted) computed columns</span></span>

<span data-ttu-id="cbb99-138">La plupart des bases de données autorisent le stockage des valeurs de colonne calculées après le calcul.</span><span class="sxs-lookup"><span data-stu-id="cbb99-138">Most databases allow computed column values to be stored after computation.</span></span>
<span data-ttu-id="cbb99-139">Bien que cela prenne de l’espace disque, la colonne calculée n’est calculée qu’une seule fois lors de la mise à jour, et non chaque fois que sa valeur est récupérée.</span><span class="sxs-lookup"><span data-stu-id="cbb99-139">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span>
<span data-ttu-id="cbb99-140">Cela permet également d’indexer la colonne pour certaines bases de données.</span><span class="sxs-lookup"><span data-stu-id="cbb99-140">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="cbb99-141">EF Core 5,0 permet de configurer les colonnes calculées comme stockées.</span><span class="sxs-lookup"><span data-stu-id="cbb99-141">EF Core 5.0 allows computed columns to be configured as stored.</span></span>
<span data-ttu-id="cbb99-142">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-142">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="cbb99-143">SQLite colonnes calculées</span><span class="sxs-lookup"><span data-stu-id="cbb99-143">SQLite computed columns</span></span>

<span data-ttu-id="cbb99-144">EF Core prend désormais en charge les colonnes calculées dans les bases de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="cbb99-144">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="cbb99-145">Preview 4</span><span class="sxs-lookup"><span data-stu-id="cbb99-145">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="cbb99-146">Configurer la précision/l’échelle de la base de données dans le modèle</span><span class="sxs-lookup"><span data-stu-id="cbb99-146">Configure database precision/scale in model</span></span>

<span data-ttu-id="cbb99-147">La précision et l’échelle d’une propriété peuvent désormais être spécifiées à l’aide du générateur de modèles.</span><span class="sxs-lookup"><span data-stu-id="cbb99-147">Precision and scale for a property can now be specified using the model builder.</span></span>
<span data-ttu-id="cbb99-148">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-148">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="cbb99-149">La précision et l’échelle peuvent toujours être définies par le biais du type de base de données complet, par exemple « Decimal (16, 4) ».</span><span class="sxs-lookup"><span data-stu-id="cbb99-149">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="cbb99-150">La documentation est suivie d’un problème [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="cbb99-150">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="cbb99-151">Spécifier SQL Server facteur de remplissage d’index</span><span class="sxs-lookup"><span data-stu-id="cbb99-151">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="cbb99-152">Le facteur de remplissage peut désormais être spécifié lors de la création d’un index sur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="cbb99-152">The fill factor can now be specified when creating an index on SQL Server.</span></span>
<span data-ttu-id="cbb99-153">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-153">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="cbb99-154">Preview 3</span><span class="sxs-lookup"><span data-stu-id="cbb99-154">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="cbb99-155">Include filtré</span><span class="sxs-lookup"><span data-stu-id="cbb99-155">Filtered Include</span></span>

<span data-ttu-id="cbb99-156">La méthode Include prend désormais en charge le filtrage des entités incluses.</span><span class="sxs-lookup"><span data-stu-id="cbb99-156">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="cbb99-157">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-157">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="cbb99-158">Cette requête renverra les blogs avec chaque publication associée, mais uniquement lorsque le titre de publication contient « fromage ».</span><span class="sxs-lookup"><span data-stu-id="cbb99-158">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="cbb99-159">Skip et Take peuvent également être utilisés pour réduire le nombre d’entités incluses.</span><span class="sxs-lookup"><span data-stu-id="cbb99-159">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="cbb99-160">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-160">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="cbb99-161">Cette requête renverra les blogs contenant au maximum cinq publications, incluses sur chaque blog.</span><span class="sxs-lookup"><span data-stu-id="cbb99-161">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="cbb99-162">Pour plus d’informations, consultez la [documentation include](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="cbb99-162">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="cbb99-163">Nouvelle API ModelBuilder pour les propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="cbb99-163">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="cbb99-164">Les propriétés de navigation sont principalement configurées lors de la [définition de relations](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="cbb99-164">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="cbb99-165">Toutefois, la nouvelle `Navigation` méthode peut être utilisée dans les cas où les propriétés de navigation nécessitent une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="cbb99-165">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="cbb99-166">Par exemple, pour définir un champ de stockage pour la navigation lorsque le champ est introuvable par Convention :</span><span class="sxs-lookup"><span data-stu-id="cbb99-166">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="cbb99-167">Notez que l' `Navigation` API ne remplace pas la configuration de la relation.</span><span class="sxs-lookup"><span data-stu-id="cbb99-167">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="cbb99-168">Au lieu de cela, il permet une configuration supplémentaire des propriétés de navigation dans les relations déjà découvertes ou définies.</span><span class="sxs-lookup"><span data-stu-id="cbb99-168">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="cbb99-169">Consultez la [documentation](xref:core/modeling/relationships#configuring-navigation-properties)sur la configuration des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="cbb99-169">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="cbb99-170">Nouveaux paramètres de ligne de commande pour les espaces de noms et les chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="cbb99-170">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="cbb99-171">Les migrations et la génération de modèles automatique permettent désormais de spécifier des espaces de noms sur la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="cbb99-171">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="cbb99-172">Par exemple, pour rétroconcevoir une base de données en plaçant le contexte et les classes de modèle dans différents espaces de noms :</span><span class="sxs-lookup"><span data-stu-id="cbb99-172">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="cbb99-173">Pour plus d’informations, consultez la documentation sur les [migrations](xref:core/managing-schemas/migrations/index#namespaces) et l' [ingénierie à rebours](xref:core/managing-schemas/scaffolding#directories-and-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="cbb99-173">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="cbb99-174">En outre, une chaîne de connexion peut désormais être passée à la `database-update` commande :</span><span class="sxs-lookup"><span data-stu-id="cbb99-174">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="cbb99-175">Pour plus d’informations, consultez la documentation sur les [Outils](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .</span><span class="sxs-lookup"><span data-stu-id="cbb99-175">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="cbb99-176">Des paramètres équivalents ont également été ajoutés aux commandes PowerShell utilisées dans la console du gestionnaire de package VS.</span><span class="sxs-lookup"><span data-stu-id="cbb99-176">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="cbb99-177">EnableDetailedErrors a retourné</span><span class="sxs-lookup"><span data-stu-id="cbb99-177">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="cbb99-178">Pour des raisons de performances, EF n’effectue pas de vérifications null supplémentaires lors de la lecture des valeurs de la base de données.</span><span class="sxs-lookup"><span data-stu-id="cbb99-178">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="cbb99-179">Cela peut entraîner des exceptions qui sont difficiles à la cause première lorsqu’une valeur Null inattendue est rencontrée.</span><span class="sxs-lookup"><span data-stu-id="cbb99-179">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="cbb99-180">L’utilisation de `EnableDetailedErrors` permet d’ajouter une vérification de valeur null supplémentaire aux requêtes de sorte que, pour une faible surcharge de performances, ces erreurs sont plus faciles à suivre à une cause racine.</span><span class="sxs-lookup"><span data-stu-id="cbb99-180">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="cbb99-181">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-181">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="cbb99-182">La documentation est suivie d’un problème [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="cbb99-182">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="cbb99-183">Clés de partition Cosmos</span><span class="sxs-lookup"><span data-stu-id="cbb99-183">Cosmos partition keys</span></span>

<span data-ttu-id="cbb99-184">La clé de partition à utiliser pour une requête donnée peut désormais être spécifiée dans la requête.</span><span class="sxs-lookup"><span data-stu-id="cbb99-184">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="cbb99-185">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-185">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="cbb99-186">La documentation est suivie d’un problème [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="cbb99-186">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="cbb99-187">Prise en charge de la fonction DATALENGTH SQL Server</span><span class="sxs-lookup"><span data-stu-id="cbb99-187">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="cbb99-188">Vous pouvez y accéder à l’aide de la nouvelle `EF.Functions.DataLength` méthode.</span><span class="sxs-lookup"><span data-stu-id="cbb99-188">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="cbb99-189">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-189">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="cbb99-190">Préversion 2</span><span class="sxs-lookup"><span data-stu-id="cbb99-190">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="cbb99-191">Utiliser un attribut C# pour spécifier un champ de stockage de propriété</span><span class="sxs-lookup"><span data-stu-id="cbb99-191">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="cbb99-192">Un attribut C# peut maintenant être utilisé pour spécifier le champ de stockage d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="cbb99-192">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="cbb99-193">Cet attribut permet à EF Core d’écrire et de lire toujours dans le champ de stockage, ce qui se produit normalement, même si le champ de stockage ne peut pas être trouvé automatiquement.</span><span class="sxs-lookup"><span data-stu-id="cbb99-193">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="cbb99-194">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-194">For example:</span></span>

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

<span data-ttu-id="cbb99-195">La documentation est suivie d’un problème [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="cbb99-195">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="cbb99-196">Terminer le mappage de discriminateur</span><span class="sxs-lookup"><span data-stu-id="cbb99-196">Complete discriminator mapping</span></span>

<span data-ttu-id="cbb99-197">EF Core utilise une colonne de discriminateur pour le [mappage TPH d’une hiérarchie d’héritage](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="cbb99-197">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="cbb99-198">Certaines améliorations des performances sont possibles tant que EF Core connaît toutes les valeurs possibles pour le discriminateur.</span><span class="sxs-lookup"><span data-stu-id="cbb99-198">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="cbb99-199">EF Core 5,0 implémente désormais ces améliorations.</span><span class="sxs-lookup"><span data-stu-id="cbb99-199">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="cbb99-200">Par exemple, les versions précédentes de EF Core génèrent toujours ce SQL pour une requête qui retourne tous les types dans une hiérarchie :</span><span class="sxs-lookup"><span data-stu-id="cbb99-200">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="cbb99-201">EF Core 5,0 génère désormais les éléments suivants lorsqu’un mappage de discriminateur complet est configuré :</span><span class="sxs-lookup"><span data-stu-id="cbb99-201">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="cbb99-202">Il s’agit du comportement par défaut à partir de Preview 3.</span><span class="sxs-lookup"><span data-stu-id="cbb99-202">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="cbb99-203">Améliorations des performances dans Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="cbb99-203">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="cbb99-204">Nous avons apporté deux améliorations des performances pour SQLIte :</span><span class="sxs-lookup"><span data-stu-id="cbb99-204">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="cbb99-205">La récupération de données binaires et de chaîne avec GetBytes, GetChars et GetTextReader est désormais plus efficace grâce à l’utilisation de SqliteBlob et de flux.</span><span class="sxs-lookup"><span data-stu-id="cbb99-205">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="cbb99-206">L’initialisation de SqliteConnection est désormais différée.</span><span class="sxs-lookup"><span data-stu-id="cbb99-206">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="cbb99-207">Ces améliorations se trouvent dans le fournisseur Microsoft. Data. sqlite de ADO.NET et améliorent donc également les performances en dehors de EF Core.</span><span class="sxs-lookup"><span data-stu-id="cbb99-207">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="cbb99-208">Preview 1</span><span class="sxs-lookup"><span data-stu-id="cbb99-208">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="cbb99-209">Journalisation simple</span><span class="sxs-lookup"><span data-stu-id="cbb99-209">Simple logging</span></span>

<span data-ttu-id="cbb99-210">Cette fonctionnalité ajoute des fonctionnalités similaires à `Database.Log` dans EF6.</span><span class="sxs-lookup"><span data-stu-id="cbb99-210">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="cbb99-211">Autrement dit, il offre un moyen simple d’obtenir des journaux à partir de EF Core sans avoir besoin de configurer tout type d’infrastructure de journalisation externe.</span><span class="sxs-lookup"><span data-stu-id="cbb99-211">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="cbb99-212">La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="cbb99-212">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="cbb99-213">Une documentation supplémentaire est suivie par le [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)du problème.</span><span class="sxs-lookup"><span data-stu-id="cbb99-213">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="cbb99-214">Moyen simple d’utiliser le SQL généré</span><span class="sxs-lookup"><span data-stu-id="cbb99-214">Simple way to get generated SQL</span></span>

<span data-ttu-id="cbb99-215">EF Core 5,0 introduit la `ToQueryString` méthode d’extension, qui retourne le SQL que EF Core générera lors de l’exécution d’une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="cbb99-215">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="cbb99-216">La documentation préliminaire est incluse dans l' [État EF Weekly pour le 9 janvier 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="cbb99-216">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="cbb99-217">Une documentation supplémentaire est suivie par le [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)du problème.</span><span class="sxs-lookup"><span data-stu-id="cbb99-217">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="cbb99-218">Utiliser un attribut C# pour indiquer qu’une entité n’a pas de clé</span><span class="sxs-lookup"><span data-stu-id="cbb99-218">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="cbb99-219">Un type d’entité peut désormais être configuré comme n’ayant aucune clé à l’aide de la nouvelle `KeylessAttribute` .</span><span class="sxs-lookup"><span data-stu-id="cbb99-219">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="cbb99-220">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-220">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="cbb99-221">La documentation est suivie d’un problème [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="cbb99-221">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="cbb99-222">La connexion ou la chaîne de connexion peut être modifiée lors de l’DbContext initialisé</span><span class="sxs-lookup"><span data-stu-id="cbb99-222">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="cbb99-223">Il est désormais plus facile de créer une instance DbContext sans connexion ou chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="cbb99-223">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="cbb99-224">En outre, la connexion ou la chaîne de connexion peut désormais être mutée sur l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="cbb99-224">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="cbb99-225">Cette fonctionnalité permet à la même instance de contexte de se connecter de manière dynamique à différentes bases de données.</span><span class="sxs-lookup"><span data-stu-id="cbb99-225">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="cbb99-226">La documentation est suivie d’un problème [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="cbb99-226">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="cbb99-227">Proxys de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="cbb99-227">Change-tracking proxies</span></span>

<span data-ttu-id="cbb99-228">EF Core pouvez désormais générer des proxies d’exécution qui implémentent automatiquement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) et [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="cbb99-228">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="cbb99-229">Celles-ci rerapportent ensuite les modifications de valeur sur les propriétés d’entité directement à EF Core, évitant ainsi la nécessité d’analyser les modifications.</span><span class="sxs-lookup"><span data-stu-id="cbb99-229">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="cbb99-230">Toutefois, les proxies sont fournis avec leur propre ensemble de limitations, donc ils ne le sont pas pour tout le monde.</span><span class="sxs-lookup"><span data-stu-id="cbb99-230">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="cbb99-231">La documentation est suivie d’un problème [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="cbb99-231">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="cbb99-232">Vues de débogage améliorées</span><span class="sxs-lookup"><span data-stu-id="cbb99-232">Enhanced debug views</span></span>

<span data-ttu-id="cbb99-233">Les vues de débogage sont un moyen simple de consulter les éléments internes d’EF Core lors du débogage des problèmes.</span><span class="sxs-lookup"><span data-stu-id="cbb99-233">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="cbb99-234">Une vue de débogage a été implémentée pour le modèle il y a quelque temps.</span><span class="sxs-lookup"><span data-stu-id="cbb99-234">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="cbb99-235">Pour EF Core 5,0, nous avons facilité la lecture et l’ajout d’une nouvelle vue de débogage pour les entités suivies dans le gestionnaire d’État.</span><span class="sxs-lookup"><span data-stu-id="cbb99-235">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="cbb99-236">La documentation préliminaire est incluse dans l' [État EF Weekly pour le 12 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="cbb99-236">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="cbb99-237">Une documentation supplémentaire est suivie par le [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)du problème.</span><span class="sxs-lookup"><span data-stu-id="cbb99-237">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="cbb99-238">Amélioration de la gestion de la sémantique null de la base de données</span><span class="sxs-lookup"><span data-stu-id="cbb99-238">Improved handling of database null semantics</span></span>

<span data-ttu-id="cbb99-239">En général, les bases de données relationnelles traitent la valeur NULL comme une valeur inconnue et, par conséquent, n’est pas égale à une autre valeur NULL.</span><span class="sxs-lookup"><span data-stu-id="cbb99-239">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="cbb99-240">Alors que C# traite NULL comme une valeur définie, qui compare la valeur à toute autre valeur null.</span><span class="sxs-lookup"><span data-stu-id="cbb99-240">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="cbb99-241">EF Core par défaut traduit les requêtes afin qu’elles utilisent la sémantique null C#.</span><span class="sxs-lookup"><span data-stu-id="cbb99-241">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="cbb99-242">EF Core 5,0 améliore considérablement l’efficacité de ces traductions.</span><span class="sxs-lookup"><span data-stu-id="cbb99-242">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="cbb99-243">La documentation est suivie d’un problème [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="cbb99-243">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="cbb99-244">Propriétés de l’indexeur</span><span class="sxs-lookup"><span data-stu-id="cbb99-244">Indexer properties</span></span>

<span data-ttu-id="cbb99-245">EF Core 5,0 prend en charge le mappage des propriétés de l’indexeur C#.</span><span class="sxs-lookup"><span data-stu-id="cbb99-245">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="cbb99-246">Ces propriétés permettent aux entités d’agir en tant que conteneurs de propriétés où les colonnes sont mappées à des propriétés nommées dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="cbb99-246">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="cbb99-247">La documentation est suivie d’un problème [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="cbb99-247">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="cbb99-248">Génération de contraintes de validation pour les mappages d’énumération</span><span class="sxs-lookup"><span data-stu-id="cbb99-248">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="cbb99-249">EF Core migrations 5,0 peut désormais générer des contraintes de validation pour les mappages de propriété d’énumération.</span><span class="sxs-lookup"><span data-stu-id="cbb99-249">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="cbb99-250">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-250">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="cbb99-251">La documentation est suivie d’un problème [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="cbb99-251">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="cbb99-252">IsRelational</span><span class="sxs-lookup"><span data-stu-id="cbb99-252">IsRelational</span></span>

<span data-ttu-id="cbb99-253">Une nouvelle `IsRelational` méthode a été ajoutée en plus des `IsSqlServer` , `IsSqlite` , et existants `IsInMemory` .</span><span class="sxs-lookup"><span data-stu-id="cbb99-253">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="cbb99-254">Cette méthode peut être utilisée pour tester si DbContext utilise un fournisseur de base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="cbb99-254">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="cbb99-255">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-255">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="cbb99-256">La documentation est suivie d’un problème [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="cbb99-256">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="cbb99-257">Accès concurrentiel optimiste Cosmos avec ETags</span><span class="sxs-lookup"><span data-stu-id="cbb99-257">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="cbb99-258">Le fournisseur de base de données Azure Cosmos DB prend désormais en charge l’accès concurrentiel optimiste à l’aide d’ETags.</span><span class="sxs-lookup"><span data-stu-id="cbb99-258">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="cbb99-259">Utilisez le générateur de modèles de OnModelCreating pour configurer un ETag :</span><span class="sxs-lookup"><span data-stu-id="cbb99-259">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="cbb99-260">SaveChanges lèvera ensuite un en cas de `DbUpdateConcurrencyException` conflit d’accès concurrentiel, qui [peut être géré](https://docs.microsoft.com/ef/core/saving/concurrency) pour implémenter les nouvelles tentatives, etc.</span><span class="sxs-lookup"><span data-stu-id="cbb99-260">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="cbb99-261">La documentation est suivie d’un problème [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="cbb99-261">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="cbb99-262">Traductions de requêtes pour d’autres constructions DateTime</span><span class="sxs-lookup"><span data-stu-id="cbb99-262">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="cbb99-263">Les requêtes contenant la nouvelle construction DateTime sont maintenant traduites.</span><span class="sxs-lookup"><span data-stu-id="cbb99-263">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="cbb99-264">En outre, les fonctions de SQL Server suivantes sont maintenant mappées :</span><span class="sxs-lookup"><span data-stu-id="cbb99-264">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="cbb99-265">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="cbb99-265">DateDiffWeek</span></span>
* <span data-ttu-id="cbb99-266">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="cbb99-266">DateFromParts</span></span>

<span data-ttu-id="cbb99-267">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-267">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="cbb99-268">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="cbb99-268">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="cbb99-269">Traductions de requêtes pour d’autres constructions de tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="cbb99-269">Query translations for more byte array constructs</span></span>

<span data-ttu-id="cbb99-270">Les requêtes utilisant les propriétés Contains, Length, SequenceEqual, etc. sur Byte [] sont désormais traduites en SQL.</span><span class="sxs-lookup"><span data-stu-id="cbb99-270">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="cbb99-271">La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="cbb99-271">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="cbb99-272">Une documentation supplémentaire est suivie par le [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)du problème.</span><span class="sxs-lookup"><span data-stu-id="cbb99-272">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="cbb99-273">Traduction de requête pour l’inverse</span><span class="sxs-lookup"><span data-stu-id="cbb99-273">Query translation for Reverse</span></span>

<span data-ttu-id="cbb99-274">Les requêtes utilisant `Reverse` sont désormais traduites.</span><span class="sxs-lookup"><span data-stu-id="cbb99-274">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="cbb99-275">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-275">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="cbb99-276">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="cbb99-276">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="cbb99-277">Traduction des requêtes pour les opérateurs au niveau du bit</span><span class="sxs-lookup"><span data-stu-id="cbb99-277">Query translation for bitwise operators</span></span>

<span data-ttu-id="cbb99-278">Les requêtes utilisant des opérateurs de bits sont maintenant traduites dans d’autres cas, par exemple :</span><span class="sxs-lookup"><span data-stu-id="cbb99-278">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="cbb99-279">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="cbb99-279">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="cbb99-280">Traduction des requêtes pour les chaînes sur Cosmos</span><span class="sxs-lookup"><span data-stu-id="cbb99-280">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="cbb99-281">Les requêtes qui utilisent les méthodes de chaîne Contains, StartsWith et EndsWith sont désormais traduites lors de l’utilisation du fournisseur Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="cbb99-281">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="cbb99-282">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="cbb99-282">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
