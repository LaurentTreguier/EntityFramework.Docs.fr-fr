---
title: Nouveautés de EF Core 5,0
description: Vue d’ensemble des nouvelles fonctionnalités de EF Core 5,0
author: ajcvickers
ms.date: 03/30/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: c902988920e3b1a6039808fe0658fc19dee2728a
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103072"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="768f5-103">Nouveautés de EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="768f5-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="768f5-104">EF Core 5,0 est actuellement en cours de développement.</span><span class="sxs-lookup"><span data-stu-id="768f5-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="768f5-105">Cette page contient une vue d’ensemble des changements intéressants introduits dans chaque version préliminaire.</span><span class="sxs-lookup"><span data-stu-id="768f5-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="768f5-106">Cette page ne duplique pas le [plan pour EF Core 5,0](plan.md).</span><span class="sxs-lookup"><span data-stu-id="768f5-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="768f5-107">Le plan décrit les thèmes globaux pour EF Core 5,0, y compris tout ce que nous envisageons d’inclure avant d’expédier la version finale.</span><span class="sxs-lookup"><span data-stu-id="768f5-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="768f5-108">Nous ajouterons des liens à partir d’ici à la documentation officielle au fur et à mesure de sa publication.</span><span class="sxs-lookup"><span data-stu-id="768f5-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-3"></a><span data-ttu-id="768f5-109">Preview 3</span><span class="sxs-lookup"><span data-stu-id="768f5-109">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="768f5-110">Include filtré</span><span class="sxs-lookup"><span data-stu-id="768f5-110">Filtered Include</span></span>

<span data-ttu-id="768f5-111">La méthode Include prend désormais en charge le filtrage des entités incluses.</span><span class="sxs-lookup"><span data-stu-id="768f5-111">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="768f5-112">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-112">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="768f5-113">Cette requête renverra les blogs avec chaque publication associée, mais uniquement lorsque le titre de publication contient « fromage ».</span><span class="sxs-lookup"><span data-stu-id="768f5-113">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="768f5-114">Skip et Take peuvent également être utilisés pour réduire le nombre d’entités incluses.</span><span class="sxs-lookup"><span data-stu-id="768f5-114">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="768f5-115">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-115">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="768f5-116">Cette requête renverra les blogs contenant au maximum cinq publications, incluses sur chaque blog.</span><span class="sxs-lookup"><span data-stu-id="768f5-116">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="768f5-117">Pour plus d’informations, consultez la [documentation include](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="768f5-117">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="768f5-118">Nouvelle API ModelBuilder pour les propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="768f5-118">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="768f5-119">Les propriétés de navigation sont principalement configurées lors de la [définition de relations](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="768f5-119">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="768f5-120">Toutefois, la nouvelle `Navigation` méthode peut être utilisée dans les cas où les propriétés de navigation nécessitent une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="768f5-120">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="768f5-121">Par exemple, pour définir un champ de stockage pour la navigation lorsque le champ est introuvable par Convention :</span><span class="sxs-lookup"><span data-stu-id="768f5-121">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="768f5-122">Notez que l' `Navigation` API ne remplace pas la configuration de la relation.</span><span class="sxs-lookup"><span data-stu-id="768f5-122">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="768f5-123">Au lieu de cela, il permet une configuration supplémentaire des propriétés de navigation dans les relations déjà découvertes ou définies.</span><span class="sxs-lookup"><span data-stu-id="768f5-123">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="768f5-124">La documentation est suivie d’un problème [#2302](https://github.com/dotnet/EntityFramework.Docs/issues/2302).</span><span class="sxs-lookup"><span data-stu-id="768f5-124">Documentation is tracked by issue [#2302](https://github.com/dotnet/EntityFramework.Docs/issues/2302).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="768f5-125">Nouveaux paramètres de ligne de commande pour les espaces de noms et les chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="768f5-125">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="768f5-126">Les migrations et la génération de modèles automatique permettent désormais de spécifier des espaces de noms sur la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="768f5-126">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="768f5-127">Par exemple, pour rétroconcevoir une base de données en plaçant le contexte et les classes de modèle dans différents espaces de noms :</span><span class="sxs-lookup"><span data-stu-id="768f5-127">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="768f5-128">En outre, une chaîne de connexion peut désormais être passée `database-update` à la commande :</span><span class="sxs-lookup"><span data-stu-id="768f5-128">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="768f5-129">Des paramètres équivalents ont également été ajoutés aux commandes PowerShell utilisées dans la console du gestionnaire de package VS.</span><span class="sxs-lookup"><span data-stu-id="768f5-129">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

<span data-ttu-id="768f5-130">La documentation est suivie d’un problème [#2303](https://github.com/dotnet/EntityFramework.Docs/issues/2303).</span><span class="sxs-lookup"><span data-stu-id="768f5-130">Documentation is tracked by issue [#2303](https://github.com/dotnet/EntityFramework.Docs/issues/2303).</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="768f5-131">EnableDetailedErrors a retourné</span><span class="sxs-lookup"><span data-stu-id="768f5-131">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="768f5-132">Pour des raisons de performances, EF n’effectue pas de vérifications null supplémentaires lors de la lecture des valeurs de la base de données.</span><span class="sxs-lookup"><span data-stu-id="768f5-132">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="768f5-133">Cela peut entraîner des exceptions qui sont difficiles à la cause première lorsqu’une valeur Null inattendue est rencontrée.</span><span class="sxs-lookup"><span data-stu-id="768f5-133">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="768f5-134">L' `EnableDetailedErrors` utilisation de permet d’ajouter une vérification de valeur null supplémentaire aux requêtes de sorte que, pour une faible surcharge de performances, ces erreurs sont plus faciles à suivre à une cause racine.</span><span class="sxs-lookup"><span data-stu-id="768f5-134">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="768f5-135">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-135">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="768f5-136">La documentation est suivie d’un problème [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="768f5-136">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="768f5-137">Clés de partition Cosmos</span><span class="sxs-lookup"><span data-stu-id="768f5-137">Cosmos partition keys</span></span>

<span data-ttu-id="768f5-138">La clé de partition à utiliser pour une requête donnée peut désormais être spécifiée dans la requête.</span><span class="sxs-lookup"><span data-stu-id="768f5-138">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="768f5-139">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-139">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="768f5-140">La documentation est suivie d’un problème [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="768f5-140">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="768f5-141">Prise en charge de la fonction DATALENGTH SQL Server</span><span class="sxs-lookup"><span data-stu-id="768f5-141">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="768f5-142">Vous pouvez y accéder à l’aide `EF.Functions.DataLength` de la nouvelle méthode.</span><span class="sxs-lookup"><span data-stu-id="768f5-142">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="768f5-143">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-143">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="768f5-144">Préversion 2</span><span class="sxs-lookup"><span data-stu-id="768f5-144">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="768f5-145">Utiliser un attribut C# pour spécifier un champ de stockage de propriété</span><span class="sxs-lookup"><span data-stu-id="768f5-145">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="768f5-146">Un attribut C# peut maintenant être utilisé pour spécifier le champ de stockage d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="768f5-146">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="768f5-147">Cet attribut permet à EF Core d’écrire et de lire toujours dans le champ de stockage, ce qui se produit normalement, même si le champ de stockage ne peut pas être trouvé automatiquement.</span><span class="sxs-lookup"><span data-stu-id="768f5-147">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="768f5-148">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-148">For example:</span></span>

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

<span data-ttu-id="768f5-149">La documentation est suivie d’un problème [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="768f5-149">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="768f5-150">Terminer le mappage de discriminateur</span><span class="sxs-lookup"><span data-stu-id="768f5-150">Complete discriminator mapping</span></span>

<span data-ttu-id="768f5-151">EF Core utilise une colonne de discriminateur pour le [mappage TPH d’une hiérarchie d’héritage](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="768f5-151">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="768f5-152">Certaines améliorations des performances sont possibles tant que EF Core connaît toutes les valeurs possibles pour le discriminateur.</span><span class="sxs-lookup"><span data-stu-id="768f5-152">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="768f5-153">EF Core 5,0 implémente désormais ces améliorations.</span><span class="sxs-lookup"><span data-stu-id="768f5-153">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="768f5-154">Par exemple, les versions précédentes de EF Core génèrent toujours ce SQL pour une requête qui retourne tous les types dans une hiérarchie :</span><span class="sxs-lookup"><span data-stu-id="768f5-154">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="768f5-155">EF Core 5,0 génère désormais les éléments suivants lorsqu’un mappage de discriminateur complet est configuré :</span><span class="sxs-lookup"><span data-stu-id="768f5-155">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="768f5-156">Il s’agit du comportement par défaut à partir de Preview 3.</span><span class="sxs-lookup"><span data-stu-id="768f5-156">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="768f5-157">Améliorations des performances dans Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="768f5-157">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="768f5-158">Nous avons apporté deux améliorations des performances pour SQLIte :</span><span class="sxs-lookup"><span data-stu-id="768f5-158">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="768f5-159">La récupération de données binaires et de chaîne avec GetBytes, GetChars et GetTextReader est désormais plus efficace grâce à l’utilisation de SqliteBlob et de flux.</span><span class="sxs-lookup"><span data-stu-id="768f5-159">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="768f5-160">L’initialisation de SqliteConnection est désormais différée.</span><span class="sxs-lookup"><span data-stu-id="768f5-160">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="768f5-161">Ces améliorations se trouvent dans le fournisseur Microsoft. Data. sqlite de ADO.NET et améliorent donc également les performances en dehors de EF Core.</span><span class="sxs-lookup"><span data-stu-id="768f5-161">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="768f5-162">Preview 1</span><span class="sxs-lookup"><span data-stu-id="768f5-162">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="768f5-163">Journalisation simple</span><span class="sxs-lookup"><span data-stu-id="768f5-163">Simple logging</span></span>

<span data-ttu-id="768f5-164">Cette fonctionnalité ajoute des fonctionnalités similaires `Database.Log` à dans EF6.</span><span class="sxs-lookup"><span data-stu-id="768f5-164">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="768f5-165">Autrement dit, il offre un moyen simple d’obtenir des journaux à partir de EF Core sans avoir besoin de configurer tout type d’infrastructure de journalisation externe.</span><span class="sxs-lookup"><span data-stu-id="768f5-165">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="768f5-166">La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="768f5-166">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="768f5-167">Une documentation supplémentaire est suivie par le [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)du problème.</span><span class="sxs-lookup"><span data-stu-id="768f5-167">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="768f5-168">Moyen simple d’utiliser le SQL généré</span><span class="sxs-lookup"><span data-stu-id="768f5-168">Simple way to get generated SQL</span></span>

<span data-ttu-id="768f5-169">EF Core 5,0 introduit la `ToQueryString` méthode d’extension, qui retourne le SQL que EF Core générera lors de l’exécution d’une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="768f5-169">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="768f5-170">La documentation préliminaire est incluse dans l' [État EF Weekly pour le 9 janvier 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="768f5-170">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="768f5-171">Une documentation supplémentaire est suivie par le [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)du problème.</span><span class="sxs-lookup"><span data-stu-id="768f5-171">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="768f5-172">Utiliser un attribut C# pour indiquer qu’une entité n’a pas de clé</span><span class="sxs-lookup"><span data-stu-id="768f5-172">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="768f5-173">Un type d’entité peut désormais être configuré comme n’ayant aucune clé à `KeylessAttribute`l’aide de la nouvelle.</span><span class="sxs-lookup"><span data-stu-id="768f5-173">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="768f5-174">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-174">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="768f5-175">La documentation est suivie d’un problème [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="768f5-175">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="768f5-176">La connexion ou la chaîne de connexion peut être modifiée lors de l’DbContext initialisé</span><span class="sxs-lookup"><span data-stu-id="768f5-176">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="768f5-177">Il est désormais plus facile de créer une instance DbContext sans connexion ou chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="768f5-177">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="768f5-178">En outre, la connexion ou la chaîne de connexion peut désormais être mutée sur l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="768f5-178">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="768f5-179">Cette fonctionnalité permet à la même instance de contexte de se connecter de manière dynamique à différentes bases de données.</span><span class="sxs-lookup"><span data-stu-id="768f5-179">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="768f5-180">La documentation est suivie d’un problème [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="768f5-180">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="768f5-181">Proxys de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="768f5-181">Change-tracking proxies</span></span>

<span data-ttu-id="768f5-182">EF Core pouvez désormais générer des proxies d’exécution qui implémentent automatiquement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) et [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="768f5-182">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="768f5-183">Celles-ci rerapportent ensuite les modifications de valeur sur les propriétés d’entité directement à EF Core, évitant ainsi la nécessité d’analyser les modifications.</span><span class="sxs-lookup"><span data-stu-id="768f5-183">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="768f5-184">Toutefois, les proxies sont fournis avec leur propre ensemble de limitations, donc ils ne le sont pas pour tout le monde.</span><span class="sxs-lookup"><span data-stu-id="768f5-184">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="768f5-185">La documentation est suivie d’un problème [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="768f5-185">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="768f5-186">Vues de débogage améliorées</span><span class="sxs-lookup"><span data-stu-id="768f5-186">Enhanced debug views</span></span>

<span data-ttu-id="768f5-187">Les vues de débogage sont un moyen simple de consulter les éléments internes d’EF Core lors du débogage des problèmes.</span><span class="sxs-lookup"><span data-stu-id="768f5-187">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="768f5-188">Une vue de débogage a été implémentée pour le modèle il y a quelque temps.</span><span class="sxs-lookup"><span data-stu-id="768f5-188">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="768f5-189">Pour EF Core 5,0, nous avons facilité la lecture et l’ajout d’une nouvelle vue de débogage pour les entités suivies dans le gestionnaire d’État.</span><span class="sxs-lookup"><span data-stu-id="768f5-189">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="768f5-190">La documentation préliminaire est incluse dans l' [État EF Weekly pour le 12 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="768f5-190">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="768f5-191">Une documentation supplémentaire est suivie par le [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)du problème.</span><span class="sxs-lookup"><span data-stu-id="768f5-191">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="768f5-192">Amélioration de la gestion de la sémantique null de la base de données</span><span class="sxs-lookup"><span data-stu-id="768f5-192">Improved handling of database null semantics</span></span>

<span data-ttu-id="768f5-193">En général, les bases de données relationnelles traitent la valeur NULL comme une valeur inconnue et, par conséquent, n’est pas égale à une autre valeur NULL.</span><span class="sxs-lookup"><span data-stu-id="768f5-193">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="768f5-194">Alors que C# traite NULL comme une valeur définie, qui compare la valeur à toute autre valeur null.</span><span class="sxs-lookup"><span data-stu-id="768f5-194">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="768f5-195">EF Core par défaut traduit les requêtes afin qu’elles utilisent la sémantique null C#.</span><span class="sxs-lookup"><span data-stu-id="768f5-195">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="768f5-196">EF Core 5,0 améliore considérablement l’efficacité de ces traductions.</span><span class="sxs-lookup"><span data-stu-id="768f5-196">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="768f5-197">La documentation est suivie d’un problème [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="768f5-197">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="768f5-198">Propriétés de l’indexeur</span><span class="sxs-lookup"><span data-stu-id="768f5-198">Indexer properties</span></span>

<span data-ttu-id="768f5-199">EF Core 5,0 prend en charge le mappage des propriétés de l’indexeur C#.</span><span class="sxs-lookup"><span data-stu-id="768f5-199">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="768f5-200">Ces propriétés permettent aux entités d’agir en tant que conteneurs de propriétés où les colonnes sont mappées à des propriétés nommées dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="768f5-200">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="768f5-201">La documentation est suivie d’un problème [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="768f5-201">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="768f5-202">Génération de contraintes de validation pour les mappages d’énumération</span><span class="sxs-lookup"><span data-stu-id="768f5-202">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="768f5-203">EF Core migrations 5,0 peut désormais générer des contraintes de validation pour les mappages de propriété d’énumération.</span><span class="sxs-lookup"><span data-stu-id="768f5-203">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="768f5-204">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-204">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="768f5-205">La documentation est suivie d’un problème [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="768f5-205">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="768f5-206">IsRelational</span><span class="sxs-lookup"><span data-stu-id="768f5-206">IsRelational</span></span>

<span data-ttu-id="768f5-207">Une nouvelle `IsRelational` méthode a été ajoutée en plus des, `IsSqlServer` `IsSqlite`, et `IsInMemory`existants.</span><span class="sxs-lookup"><span data-stu-id="768f5-207">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="768f5-208">Cette méthode peut être utilisée pour tester si DbContext utilise un fournisseur de base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="768f5-208">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="768f5-209">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-209">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="768f5-210">La documentation est suivie d’un problème [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="768f5-210">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="768f5-211">Accès concurrentiel optimiste Cosmos avec ETags</span><span class="sxs-lookup"><span data-stu-id="768f5-211">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="768f5-212">Le fournisseur de base de données Azure Cosmos DB prend désormais en charge l’accès concurrentiel optimiste à l’aide d’ETags.</span><span class="sxs-lookup"><span data-stu-id="768f5-212">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="768f5-213">Utilisez le générateur de modèles de OnModelCreating pour configurer un ETag :</span><span class="sxs-lookup"><span data-stu-id="768f5-213">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="768f5-214">SaveChanges lèvera ensuite un `DbUpdateConcurrencyException` en cas de conflit d’accès concurrentiel, qui [peut être géré](https://docs.microsoft.com/ef/core/saving/concurrency) pour implémenter les nouvelles tentatives, etc.</span><span class="sxs-lookup"><span data-stu-id="768f5-214">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="768f5-215">La documentation est suivie d’un problème [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="768f5-215">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="768f5-216">Traductions de requêtes pour d’autres constructions DateTime</span><span class="sxs-lookup"><span data-stu-id="768f5-216">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="768f5-217">Les requêtes contenant la nouvelle construction DateTime sont maintenant traduites.</span><span class="sxs-lookup"><span data-stu-id="768f5-217">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="768f5-218">En outre, les fonctions de SQL Server suivantes sont maintenant mappées :</span><span class="sxs-lookup"><span data-stu-id="768f5-218">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="768f5-219">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="768f5-219">DateDiffWeek</span></span>
* <span data-ttu-id="768f5-220">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="768f5-220">DateFromParts</span></span>

<span data-ttu-id="768f5-221">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-221">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="768f5-222">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="768f5-222">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="768f5-223">Traductions de requêtes pour d’autres constructions de tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="768f5-223">Query translations for more byte array constructs</span></span>

<span data-ttu-id="768f5-224">Les requêtes utilisant les propriétés Contains, Length, SequenceEqual, etc. sur Byte [] sont désormais traduites en SQL.</span><span class="sxs-lookup"><span data-stu-id="768f5-224">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="768f5-225">La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="768f5-225">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="768f5-226">Une documentation supplémentaire est suivie par le [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)du problème.</span><span class="sxs-lookup"><span data-stu-id="768f5-226">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="768f5-227">Traduction de requête pour l’inverse</span><span class="sxs-lookup"><span data-stu-id="768f5-227">Query translation for Reverse</span></span>

<span data-ttu-id="768f5-228">Les requêtes `Reverse` utilisant sont désormais traduites.</span><span class="sxs-lookup"><span data-stu-id="768f5-228">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="768f5-229">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-229">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="768f5-230">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="768f5-230">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="768f5-231">Traduction des requêtes pour les opérateurs au niveau du bit</span><span class="sxs-lookup"><span data-stu-id="768f5-231">Query translation for bitwise operators</span></span>

<span data-ttu-id="768f5-232">Les requêtes utilisant des opérateurs de bits sont maintenant traduites dans d’autres cas, par exemple :</span><span class="sxs-lookup"><span data-stu-id="768f5-232">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="768f5-233">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="768f5-233">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="768f5-234">Traduction des requêtes pour les chaînes sur Cosmos</span><span class="sxs-lookup"><span data-stu-id="768f5-234">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="768f5-235">Les requêtes qui utilisent les méthodes de chaîne Contains, StartsWith et EndsWith sont désormais traduites lors de l’utilisation du fournisseur Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="768f5-235">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="768f5-236">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="768f5-236">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
