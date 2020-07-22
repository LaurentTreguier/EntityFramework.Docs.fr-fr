---
title: Nouveautés de EF Core 5,0
description: Vue d’ensemble des nouvelles fonctionnalités de EF Core 5,0
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: d42b2811d07516e9febedbc51fcb206000d38371
ms.sourcegitcommit: 51148929e3889c48227d96c95c4e310d53a3d2c9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86873381"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="a166b-103">Nouveautés de EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="a166b-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="a166b-104">EF Core 5,0 est actuellement en cours de développement.</span><span class="sxs-lookup"><span data-stu-id="a166b-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="a166b-105">Cette page contient une vue d’ensemble des changements intéressants introduits dans chaque version préliminaire.</span><span class="sxs-lookup"><span data-stu-id="a166b-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="a166b-106">Cette page ne duplique pas le [plan pour EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="a166b-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="a166b-107">Le plan décrit les thèmes globaux pour EF Core 5,0, y compris tout ce que nous envisageons d’inclure avant d’expédier la version finale.</span><span class="sxs-lookup"><span data-stu-id="a166b-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="a166b-108">Nous ajouterons des liens à partir d’ici à la documentation officielle au fur et à mesure de sa publication.</span><span class="sxs-lookup"><span data-stu-id="a166b-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-7"></a><span data-ttu-id="a166b-109">Version préliminaire 7</span><span class="sxs-lookup"><span data-stu-id="a166b-109">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="a166b-110">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="a166b-110">DbContextFactory</span></span>

<span data-ttu-id="a166b-111">EF Core 5,0 introduit `AddDbContextFactory` et `AddPooledDbContextFactory` pour inscrire une fabrique pour la création d’instances de DbContext dans le conteneur d’injection de dépendances (D.I.) de l’application.</span><span class="sxs-lookup"><span data-stu-id="a166b-111">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="a166b-112">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-112">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="a166b-113">Les services d’application tels que les contrôleurs de ASP.NET Core peuvent ensuite dépendre `IDbContextFactory<TContext>` du constructeur de service.</span><span class="sxs-lookup"><span data-stu-id="a166b-113">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="a166b-114">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-114">For example:</span></span>

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

<span data-ttu-id="a166b-115">Les instances de DbContext peuvent ensuite être créées et utilisées en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="a166b-115">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="a166b-116">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-116">For example:</span></span>

```csharp
public void DoSomehing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...            
    }
}
```

<span data-ttu-id="a166b-117">Notez que les instances de DbContext créées de cette façon _ne sont pas_ gérées par le fournisseur de services de l’application et doivent donc être supprimées par l’application.</span><span class="sxs-lookup"><span data-stu-id="a166b-117">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="a166b-118">Ce découplage est très utile pour les applications éblouissantes, où l’utilisation de `IDbContextFactory` est recommandée, mais peut également être utile dans d’autres scénarios.</span><span class="sxs-lookup"><span data-stu-id="a166b-118">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="a166b-119">Les instances de DbContext peuvent être regroupées en appelant `AddPooledDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="a166b-119">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="a166b-120">Ce regroupement fonctionne de la même façon que pour `AddDbContextPool` et présente également les mêmes limitations.</span><span class="sxs-lookup"><span data-stu-id="a166b-120">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="a166b-121">La documentation est suivie d’un problème [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span><span class="sxs-lookup"><span data-stu-id="a166b-121">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="a166b-122">Réinitialiser l’état de DbContext</span><span class="sxs-lookup"><span data-stu-id="a166b-122">Reset DbContext state</span></span>

<span data-ttu-id="a166b-123">EF Core 5,0 introduit `ChangeTracker.Clear()` , qui efface le DbContext de toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="a166b-123">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="a166b-124">Cela ne doit généralement pas être nécessaire lors de l’utilisation de la méthode recommandée pour créer une instance de contexte de courte durée pour chaque unité de travail.</span><span class="sxs-lookup"><span data-stu-id="a166b-124">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="a166b-125">Toutefois, s’il est nécessaire de réinitialiser l’état d’une instance de DbContext, l’utilisation de la nouvelle `Clear()` méthode est plus performante et fiable que le détachement en masse de toutes les entités.</span><span class="sxs-lookup"><span data-stu-id="a166b-125">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>  

<span data-ttu-id="a166b-126">La documentation est suivie d’un problème [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span><span class="sxs-lookup"><span data-stu-id="a166b-126">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="a166b-127">Nouveau modèle pour les valeurs par défaut générées par le magasin</span><span class="sxs-lookup"><span data-stu-id="a166b-127">New pattern for store-generated defaults</span></span>

<span data-ttu-id="a166b-128">EF Core permet de définir une valeur explicite pour une colonne qui peut également avoir une contrainte de valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="a166b-128">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="a166b-129">EF Core utilise la valeur CLR par défaut de type de propriété comme sentinelle pour ce. Si la valeur n’est pas la valeur CLR par défaut, elle est insérée, sinon la valeur par défaut de la base de données est utilisée.</span><span class="sxs-lookup"><span data-stu-id="a166b-129">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="a166b-130">Cela crée des problèmes pour les types où la valeur CLR par défaut n’est pas une bonne sentinelle, notamment les `bool` Propriétés.</span><span class="sxs-lookup"><span data-stu-id="a166b-130">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="a166b-131">EF Core 5,0 autorise désormais la valeur null dans le champ de stockage pour les cas comme celui-ci.</span><span class="sxs-lookup"><span data-stu-id="a166b-131">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="a166b-132">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-132">For example:</span></span>

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

<span data-ttu-id="a166b-133">Notez que le champ de stockage accepte la valeur null, mais pas la propriété exposée publiquement.</span><span class="sxs-lookup"><span data-stu-id="a166b-133">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="a166b-134">Cela permet à la valeur de sentinelle de ne pas avoir `null` d’impact sur la surface publique du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="a166b-134">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="a166b-135">Dans ce cas, si le `IsValid` n’est jamais défini, la valeur par défaut de la base de données est utilisée, car le champ de stockage conserve la valeur null.</span><span class="sxs-lookup"><span data-stu-id="a166b-135">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="a166b-136">Si `true` ou `false` sont définis, cette valeur est enregistrée explicitement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="a166b-136">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="a166b-137">La documentation est suivie d’un problème [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span><span class="sxs-lookup"><span data-stu-id="a166b-137">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="a166b-138">Clés de partition Cosmos</span><span class="sxs-lookup"><span data-stu-id="a166b-138">Cosmos partition keys</span></span>

<span data-ttu-id="a166b-139">EF Core permet d’inclure la clé de partition Cosmos dans le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="a166b-139">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="a166b-140">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-140">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="a166b-141">À partir de Preview 7, la clé de partition est incluse dans le PK du type d’entité et est utilisée pour améliorer les performances dans certaines requêtes.</span><span class="sxs-lookup"><span data-stu-id="a166b-141">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="a166b-142">La documentation est suivie d’un problème [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="a166b-142">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="a166b-143">Configuration de Cosmos</span><span class="sxs-lookup"><span data-stu-id="a166b-143">Cosmos configuration</span></span>

<span data-ttu-id="a166b-144">EF Core 5,0 améliore la configuration des connexions Cosmos et Cosmos.</span><span class="sxs-lookup"><span data-stu-id="a166b-144">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="a166b-145">Auparavant, EF Core nécessitait la spécification du point de terminaison et de la clé explicitement lors de la connexion à une base de données Cosmos.</span><span class="sxs-lookup"><span data-stu-id="a166b-145">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="a166b-146">EF Core 5,0 autorise l’utilisation d’une chaîne de connexion à la place.</span><span class="sxs-lookup"><span data-stu-id="a166b-146">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="a166b-147">En outre, EF Core 5,0 permet de définir explicitement l’instance WebProxy.</span><span class="sxs-lookup"><span data-stu-id="a166b-147">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="a166b-148">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-148">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="a166b-149">De nombreuses autres valeurs de délai d’attente, limites, etc. peuvent également être configurées.</span><span class="sxs-lookup"><span data-stu-id="a166b-149">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="a166b-150">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-150">For example:</span></span>

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

<span data-ttu-id="a166b-151">Enfin, le mode de connexion par défaut est désormais `ConnectionMode.Gateway` , ce qui est généralement plus compatible.</span><span class="sxs-lookup"><span data-stu-id="a166b-151">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="a166b-152">La documentation est suivie d’un problème [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="a166b-152">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="a166b-153">Génération de modèles automatique-DbContext Now singularise</span><span class="sxs-lookup"><span data-stu-id="a166b-153">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="a166b-154">Auparavant, lors de la génération de modèles automatique d’une base de données existante, EF Core créera des noms de types d’entité qui correspondent aux noms de table dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="a166b-154">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="a166b-155">Par exemple, les tables `People` et ont `Addresses` abouti à des types d’entités nommés `People` et `Addresses` .</span><span class="sxs-lookup"><span data-stu-id="a166b-155">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="a166b-156">Dans les versions précédentes, ce comportement était configurable par l’inscription d’un service de pluralisation.</span><span class="sxs-lookup"><span data-stu-id="a166b-156">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="a166b-157">Désormais, dans EF Core 5,0, le package [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) est utilisé comme un service de pluralisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="a166b-157">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="a166b-158">Cela signifie que `People` les tables et `Addresses` sont maintenant rétroconçues dans les types d’entités nommés `Person` et `Address` .</span><span class="sxs-lookup"><span data-stu-id="a166b-158">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="a166b-159">Points</span><span class="sxs-lookup"><span data-stu-id="a166b-159">Savepoints</span></span>

<span data-ttu-id="a166b-160">EF Core prend désormais en charge les [points](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) de contrôle pour un plus grand contrôle sur les transactions qui exécutent plusieurs opérations.</span><span class="sxs-lookup"><span data-stu-id="a166b-160">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="a166b-161">Les points de sauvegarde peuvent être créés, libérés et restaurés manuellement.</span><span class="sxs-lookup"><span data-stu-id="a166b-161">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="a166b-162">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-162">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint"); 
```

<span data-ttu-id="a166b-163">En outre, les EF Core sont désormais restaurés jusqu’au dernier point de sauvegarde en cas d’échec de l’exécution `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="a166b-163">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="a166b-164">Cela permet de faire une nouvelle tentative d’SaveChanges sans retenter l’intégralité de la transaction.</span><span class="sxs-lookup"><span data-stu-id="a166b-164">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="a166b-165">La documentation est suivie d’un problème [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span><span class="sxs-lookup"><span data-stu-id="a166b-165">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="a166b-166">Préversion 6</span><span class="sxs-lookup"><span data-stu-id="a166b-166">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="a166b-167">Fractionner les requêtes pour les regroupements associés</span><span class="sxs-lookup"><span data-stu-id="a166b-167">Split queries for related collections</span></span>

<span data-ttu-id="a166b-168">À compter de EF Core 3,0, EF Core génère toujours une requête SQL unique pour chaque requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="a166b-168">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="a166b-169">Cela garantit la cohérence des données retournées dans les contraintes du mode de transaction en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="a166b-169">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="a166b-170">Toutefois, cela peut devenir très lent lorsque la requête utilise `Include` ou une projection pour rétablir plusieurs collections associées.</span><span class="sxs-lookup"><span data-stu-id="a166b-170">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="a166b-171">EF Core 5,0 autorise désormais une seule requête LINQ, y compris les collections associées, à fractionner en plusieurs requêtes SQL.</span><span class="sxs-lookup"><span data-stu-id="a166b-171">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="a166b-172">Cela peut améliorer considérablement les performances, mais peut entraîner une incohérence dans les résultats retournés si les données changent entre les deux requêtes.</span><span class="sxs-lookup"><span data-stu-id="a166b-172">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="a166b-173">Les transactions sérialisables ou d’instantané peuvent être utilisées pour atténuer ce problème et obtenir une cohérence avec les requêtes fractionnées, mais cela peut réduire les coûts de performances et la différence comportementale.</span><span class="sxs-lookup"><span data-stu-id="a166b-173">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="a166b-174">Fractionner des requêtes avec include</span><span class="sxs-lookup"><span data-stu-id="a166b-174">Split queries with Include</span></span>

<span data-ttu-id="a166b-175">Par exemple, considérez une requête qui extrait deux niveaux de collections associées à l’aide de `Include` :</span><span class="sxs-lookup"><span data-stu-id="a166b-175">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="a166b-176">Par défaut, EF Core génère le code SQL suivant lorsque vous utilisez le fournisseur SQLite :</span><span class="sxs-lookup"><span data-stu-id="a166b-176">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="a166b-177">La nouvelle `AsSplitQuery` API peut être utilisée pour modifier ce comportement.</span><span class="sxs-lookup"><span data-stu-id="a166b-177">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="a166b-178">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-178">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="a166b-179">AsSplitQuery est disponible pour tous les fournisseurs de bases de données relationnelles et peut être utilisée n’importe où dans la requête, tout comme AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="a166b-179">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="a166b-180">EF Core générera maintenant les trois requêtes SQL suivantes :</span><span class="sxs-lookup"><span data-stu-id="a166b-180">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="a166b-181">Toutes les opérations sur la racine de la requête sont prises en charge.</span><span class="sxs-lookup"><span data-stu-id="a166b-181">All operations on the query root are supported.</span></span> <span data-ttu-id="a166b-182">Cela comprend les opérations OrderBy/Skip/Take, Join, FirstOrDefault et des résultats uniques de sélection.</span><span class="sxs-lookup"><span data-stu-id="a166b-182">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="a166b-183">Notez que les inclusions filtrées avec OrderBy/Skip/Take ne sont pas prises en charge dans Preview 6, mais sont disponibles dans les builds quotidiennes et seront incluses dans Preview 7.</span><span class="sxs-lookup"><span data-stu-id="a166b-183">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="a166b-184">Fractionner des requêtes avec des projections de collection</span><span class="sxs-lookup"><span data-stu-id="a166b-184">Split queries with collection projections</span></span>

<span data-ttu-id="a166b-185">`AsSplitQuery`peut également être utilisé lorsque les collections sont chargées dans des projections.</span><span class="sxs-lookup"><span data-stu-id="a166b-185">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="a166b-186">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-186">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="a166b-187">Cette requête LINQ génère les deux requêtes SQL suivantes lors de l’utilisation du fournisseur SQLite :</span><span class="sxs-lookup"><span data-stu-id="a166b-187">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="a166b-188">Notez que seule la matérialisation de la collection est prise en charge.</span><span class="sxs-lookup"><span data-stu-id="a166b-188">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="a166b-189">Les compositions ultérieures `e.Albums` dans le cas ci-dessus n’aboutissent pas à une requête Split.</span><span class="sxs-lookup"><span data-stu-id="a166b-189">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="a166b-190">Les améliorations apportées à cette zone sont suivies par [#21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="a166b-190">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="a166b-191">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="a166b-191">IndexAttribute</span></span>

<span data-ttu-id="a166b-192">Le nouveau IndexAttribute peut être placé sur un type d’entité pour spécifier un index pour une colonne unique.</span><span class="sxs-lookup"><span data-stu-id="a166b-192">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="a166b-193">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-193">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="a166b-194">Par SQL Server, les migrations génèrent alors le code SQL suivant :</span><span class="sxs-lookup"><span data-stu-id="a166b-194">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="a166b-195">IndexAttribute peut également être utilisé pour spécifier un index couvrant plusieurs colonnes.</span><span class="sxs-lookup"><span data-stu-id="a166b-195">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="a166b-196">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-196">For example:</span></span>

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

<span data-ttu-id="a166b-197">Pour SQL Server, les résultats sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="a166b-197">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="a166b-198">La documentation est suivie d’un problème [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="a166b-198">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="a166b-199">Amélioration des exceptions de traduction des requêtes</span><span class="sxs-lookup"><span data-stu-id="a166b-199">Improved query translation exceptions</span></span>

<span data-ttu-id="a166b-200">Nous continuons d’améliorer les messages d’exception générés en cas d’échec de la traduction de la requête.</span><span class="sxs-lookup"><span data-stu-id="a166b-200">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="a166b-201">Par exemple, cette requête utilise la propriété non mappée `IsSigned` :</span><span class="sxs-lookup"><span data-stu-id="a166b-201">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="a166b-202">EF Core lèvera l’exception suivante indiquant que la conversion a échoué, car `IsSigned` n’est pas mappé :</span><span class="sxs-lookup"><span data-stu-id="a166b-202">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="a166b-203">Exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="a166b-203">Unhandled exception.</span></span> <span data-ttu-id="a166b-204">System. InvalidOperationException : expression LINQ’DbSet <Artist> (). Où (a => a. IsSigned) 'n’a pas pu être traduit.</span><span class="sxs-lookup"><span data-stu-id="a166b-204">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="a166b-205">Informations supplémentaires : la traduction du membre « IsSigned » sur le type d’entité « Artist » a échoué.</span><span class="sxs-lookup"><span data-stu-id="a166b-205">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="a166b-206">Éventuellement, le membre spécifié n’est pas mappé.</span><span class="sxs-lookup"><span data-stu-id="a166b-206">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="a166b-207">Réécrivez la requête dans un formulaire qui peut être traduit ou basculez explicitement vers l’évaluation du client en insérant un appel à AsEnumerable (), AsAsyncEnumerable (), ToList () ou ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="a166b-207">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="a166b-208">Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2101038 (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="a166b-208">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="a166b-209">De même, de meilleurs messages d’exception sont désormais générés lorsque vous tentez de traduire des comparaisons de chaînes avec la sémantique dépendante de la culture.</span><span class="sxs-lookup"><span data-stu-id="a166b-209">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="a166b-210">Par exemple, cette requête tente d’utiliser `StringComparison.CurrentCulture` :</span><span class="sxs-lookup"><span data-stu-id="a166b-210">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="a166b-211">EF Core lèvera à présent l’exception suivante :</span><span class="sxs-lookup"><span data-stu-id="a166b-211">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="a166b-212">Exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="a166b-212">Unhandled exception.</span></span> <span data-ttu-id="a166b-213">System. InvalidOperationException : expression LINQ’DbSet <Artist> (). Où (a => a. Name. Equals (valeur : « les licornes », comparisonType : CurrentCulture))» n’a pas pu être traduit.</span><span class="sxs-lookup"><span data-stu-id="a166b-213">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="a166b-214">Informations supplémentaires : traduction de « String ». La méthode Equals qui accepte l’argument’StringComparison’n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="a166b-214">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="a166b-215">Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2129535 (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="a166b-215">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="a166b-216">Réécrivez la requête dans un formulaire qui peut être traduit ou basculez explicitement vers l’évaluation du client en insérant un appel à AsEnumerable (), AsAsyncEnumerable (), ToList () ou ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="a166b-216">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="a166b-217">Consultez la rubrique https://go.microsoft.com/fwlink/?linkid=2101038 (éventuellement en anglais) pour plus d'informations.</span><span class="sxs-lookup"><span data-stu-id="a166b-217">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="a166b-218">Spécifier l’ID de la transaction</span><span class="sxs-lookup"><span data-stu-id="a166b-218">Specify transaction ID</span></span>

<span data-ttu-id="a166b-219">Cette fonctionnalité a été ajoutée par la communauté [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="a166b-219">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="a166b-220">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="a166b-220">Many thanks for the contribution!</span></span>

<span data-ttu-id="a166b-221">EF Core expose un ID de transaction pour la corrélation des transactions entre les appels.</span><span class="sxs-lookup"><span data-stu-id="a166b-221">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="a166b-222">Cet ID est généralement défini par EF Core lorsqu’une transaction est démarrée.</span><span class="sxs-lookup"><span data-stu-id="a166b-222">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="a166b-223">Si l’application démarre plutôt la transaction, cette fonctionnalité permet à l’application de définir explicitement l’ID de transaction afin qu’elle soit correctement corrélée partout où elle est utilisée.</span><span class="sxs-lookup"><span data-stu-id="a166b-223">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="a166b-224">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-224">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="a166b-225">Mappage IPAddress</span><span class="sxs-lookup"><span data-stu-id="a166b-225">IPAddress mapping</span></span>

<span data-ttu-id="a166b-226">Cette fonctionnalité a été ajoutée par la communauté [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="a166b-226">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="a166b-227">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="a166b-227">Many thanks for the contribution!</span></span>

<span data-ttu-id="a166b-228">La classe .NET [IPAddress](/dotnet/api/system.net.ipaddress) standard est désormais automatiquement mappée à une colonne de type chaîne pour les bases de données qui n’ont pas encore de prise en charge native.</span><span class="sxs-lookup"><span data-stu-id="a166b-228">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="a166b-229">Par exemple, envisagez de mapper ce type d’entité :</span><span class="sxs-lookup"><span data-stu-id="a166b-229">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="a166b-230">Sur SQL Server, les migrations créent le tableau suivant :</span><span class="sxs-lookup"><span data-stu-id="a166b-230">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="a166b-231">Les entités peuvent ensuite être ajoutées de manière normale :</span><span class="sxs-lookup"><span data-stu-id="a166b-231">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="a166b-232">Et le SQL qui en résulte insère l’adresse IPv4 ou IPv6 normalisée :</span><span class="sxs-lookup"><span data-stu-id="a166b-232">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="a166b-233">Exclure OnConfiguring lors de la génération de modèles automatique</span><span class="sxs-lookup"><span data-stu-id="a166b-233">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="a166b-234">Lorsqu’un DbContext est généré à partir d’une base de données existante, EF Core par défaut crée une surcharge OnConfiguring avec une chaîne de connexion afin que le contexte soit immédiatement utilisable.</span><span class="sxs-lookup"><span data-stu-id="a166b-234">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="a166b-235">Toutefois, cela n’est pas utile si vous avez déjà une classe partielle avec OnConfiguring, ou si vous configurez le contexte d’une autre façon.</span><span class="sxs-lookup"><span data-stu-id="a166b-235">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="a166b-236">Pour résoudre ce risque, les commandes de génération de modèles automatique peuvent maintenant être demandées d’omettre la génération de OnConfiguring.</span><span class="sxs-lookup"><span data-stu-id="a166b-236">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="a166b-237">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-237">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="a166b-238">Ou dans la console du gestionnaire de package :</span><span class="sxs-lookup"><span data-stu-id="a166b-238">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="a166b-239">Notez que nous vous recommandons [d’utiliser une chaîne de connexion nommée et un stockage sécurisé comme des secrets d’utilisateur](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="a166b-239">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="a166b-240">Traductions pour FirstOrDefault sur les chaînes</span><span class="sxs-lookup"><span data-stu-id="a166b-240">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="a166b-241">Cette fonctionnalité a été ajoutée par la communauté [@dvoreckyaa](https://github.com/dvoreckyaa) .</span><span class="sxs-lookup"><span data-stu-id="a166b-241">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="a166b-242">De nombreuses remerciements pour la contribution !</span><span class="sxs-lookup"><span data-stu-id="a166b-242">Many thanks for the contribution!</span></span>

<span data-ttu-id="a166b-243">Les opérateurs FirstOrDefault et similaires pour les caractères dans les chaînes sont maintenant traduits.</span><span class="sxs-lookup"><span data-stu-id="a166b-243">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="a166b-244">Par exemple, cette requête LINQ :</span><span class="sxs-lookup"><span data-stu-id="a166b-244">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="a166b-245">Sera traduit dans le code SQL suivant lors de l’utilisation de SQL Server :</span><span class="sxs-lookup"><span data-stu-id="a166b-245">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="a166b-246">Simplifier les blocs de casse</span><span class="sxs-lookup"><span data-stu-id="a166b-246">Simplify case blocks</span></span>

<span data-ttu-id="a166b-247">EF Core génère désormais de meilleures requêtes avec des blocs de cas.</span><span class="sxs-lookup"><span data-stu-id="a166b-247">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="a166b-248">Par exemple, cette requête LINQ :</span><span class="sxs-lookup"><span data-stu-id="a166b-248">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="a166b-249">Était sur SQL Server traduite formellement en :</span><span class="sxs-lookup"><span data-stu-id="a166b-249">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="a166b-250">Mais est maintenant traduit par :</span><span class="sxs-lookup"><span data-stu-id="a166b-250">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="a166b-251">Préversion 5</span><span class="sxs-lookup"><span data-stu-id="a166b-251">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="a166b-252">Classements de base de données</span><span class="sxs-lookup"><span data-stu-id="a166b-252">Database collations</span></span>

<span data-ttu-id="a166b-253">Le classement par défaut d’une base de données peut désormais être spécifié dans le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="a166b-253">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="a166b-254">Cela passera à des migrations générées pour définir le classement lors de la création de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a166b-254">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="a166b-255">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-255">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="a166b-256">Les migrations génèrent ensuite les éléments suivants pour créer la base de données sur SQL Server :</span><span class="sxs-lookup"><span data-stu-id="a166b-256">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="a166b-257">Le classement à utiliser pour des colonnes de base de données spécifiques peut également être spécifié.</span><span class="sxs-lookup"><span data-stu-id="a166b-257">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="a166b-258">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-258">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="a166b-259">Pour ceux qui n’utilisent pas de migrations, les classements sont à présent rétroconçus de la base de données lors de la génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="a166b-259">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="a166b-260">Enfin, le `EF.Functions.Collate()` autorise les requêtes ad hoc utilisant des classements différents.</span><span class="sxs-lookup"><span data-stu-id="a166b-260">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="a166b-261">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-261">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="a166b-262">La requête suivante est générée pour SQL Server :</span><span class="sxs-lookup"><span data-stu-id="a166b-262">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="a166b-263">Notez que les classements ad hoc doivent être utilisés avec précaution, car ils peuvent avoir un impact négatif sur les performances de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a166b-263">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="a166b-264">La documentation est suivie d’un problème [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="a166b-264">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="a166b-265">Transmettre les arguments dans IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="a166b-265">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="a166b-266">Les arguments sont à présent transmis à partir de la ligne de commande dans la `CreateDbContext` méthode de [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="a166b-266">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="a166b-267">Par exemple, pour indiquer qu’il s’agit d’une build dev, un argument personnalisé (par exemple, `dev` ) peut être passé sur la ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="a166b-267">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="a166b-268">Cet argument est ensuite transmis à la fabrique, où il peut être utilisé pour contrôler la façon dont le contexte est créé et initialisé.</span><span class="sxs-lookup"><span data-stu-id="a166b-268">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="a166b-269">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-269">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="a166b-270">La documentation est suivie d’un problème [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="a166b-270">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="a166b-271">Requêtes de suivi sans suivi de la résolution d’identité</span><span class="sxs-lookup"><span data-stu-id="a166b-271">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="a166b-272">Les requêtes de non-suivi peuvent maintenant être configurées pour effectuer la résolution d’identité.</span><span class="sxs-lookup"><span data-stu-id="a166b-272">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="a166b-273">Par exemple, la requête suivante crée une nouvelle instance de blog pour chaque publication, même si chaque blog a la même clé primaire.</span><span class="sxs-lookup"><span data-stu-id="a166b-273">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="a166b-274">Toutefois, au détriment d’un peu plus lentement et en utilisant toujours plus de mémoire, cette requête peut être modifiée pour garantir qu’une seule instance de blog est créée :</span><span class="sxs-lookup"><span data-stu-id="a166b-274">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="a166b-275">Notez que cela n’est utile que pour les requêtes sans suivi, car toutes les requêtes de suivi présentent déjà ce comportement.</span><span class="sxs-lookup"><span data-stu-id="a166b-275">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="a166b-276">En outre, la syntaxe de l’API suivante `PerformIdentityResolution` sera modifiée.</span><span class="sxs-lookup"><span data-stu-id="a166b-276">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="a166b-277">Consultez [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="a166b-277">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="a166b-278">La documentation est suivie d’un problème [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="a166b-278">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="a166b-279">Colonnes calculées (persistantes) stockées</span><span class="sxs-lookup"><span data-stu-id="a166b-279">Stored (persisted) computed columns</span></span>

<span data-ttu-id="a166b-280">La plupart des bases de données autorisent le stockage des valeurs de colonne calculées après le calcul.</span><span class="sxs-lookup"><span data-stu-id="a166b-280">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="a166b-281">Bien que cela prenne de l’espace disque, la colonne calculée n’est calculée qu’une seule fois lors de la mise à jour, et non chaque fois que sa valeur est récupérée.</span><span class="sxs-lookup"><span data-stu-id="a166b-281">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="a166b-282">Cela permet également d’indexer la colonne pour certaines bases de données.</span><span class="sxs-lookup"><span data-stu-id="a166b-282">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="a166b-283">EF Core 5,0 permet de configurer les colonnes calculées comme stockées.</span><span class="sxs-lookup"><span data-stu-id="a166b-283">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="a166b-284">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-284">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="a166b-285">SQLite colonnes calculées</span><span class="sxs-lookup"><span data-stu-id="a166b-285">SQLite computed columns</span></span>

<span data-ttu-id="a166b-286">EF Core prend désormais en charge les colonnes calculées dans les bases de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="a166b-286">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="a166b-287">Preview 4</span><span class="sxs-lookup"><span data-stu-id="a166b-287">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="a166b-288">Configurer la précision/l’échelle de la base de données dans le modèle</span><span class="sxs-lookup"><span data-stu-id="a166b-288">Configure database precision/scale in model</span></span>

<span data-ttu-id="a166b-289">La précision et l’échelle d’une propriété peuvent désormais être spécifiées à l’aide du générateur de modèles.</span><span class="sxs-lookup"><span data-stu-id="a166b-289">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="a166b-290">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-290">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="a166b-291">La précision et l’échelle peuvent toujours être définies par le biais du type de base de données complet, par exemple « Decimal (16, 4) ».</span><span class="sxs-lookup"><span data-stu-id="a166b-291">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="a166b-292">La documentation est suivie d’un problème [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="a166b-292">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="a166b-293">Spécifier SQL Server facteur de remplissage d’index</span><span class="sxs-lookup"><span data-stu-id="a166b-293">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="a166b-294">Le facteur de remplissage peut désormais être spécifié lors de la création d’un index sur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a166b-294">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="a166b-295">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-295">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="a166b-296">Preview 3</span><span class="sxs-lookup"><span data-stu-id="a166b-296">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="a166b-297">Include filtré</span><span class="sxs-lookup"><span data-stu-id="a166b-297">Filtered Include</span></span>

<span data-ttu-id="a166b-298">La méthode Include prend désormais en charge le filtrage des entités incluses.</span><span class="sxs-lookup"><span data-stu-id="a166b-298">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="a166b-299">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-299">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="a166b-300">Cette requête renverra les blogs avec chaque publication associée, mais uniquement lorsque le titre de publication contient « fromage ».</span><span class="sxs-lookup"><span data-stu-id="a166b-300">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="a166b-301">Skip et Take peuvent également être utilisés pour réduire le nombre d’entités incluses.</span><span class="sxs-lookup"><span data-stu-id="a166b-301">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="a166b-302">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-302">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="a166b-303">Cette requête renverra les blogs contenant au maximum cinq publications, incluses sur chaque blog.</span><span class="sxs-lookup"><span data-stu-id="a166b-303">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="a166b-304">Pour plus d’informations, consultez la [documentation include](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="a166b-304">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="a166b-305">Nouvelle API ModelBuilder pour les propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="a166b-305">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="a166b-306">Les propriétés de navigation sont principalement configurées lors de la [définition de relations](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="a166b-306">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="a166b-307">Toutefois, la nouvelle `Navigation` méthode peut être utilisée dans les cas où les propriétés de navigation nécessitent une configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="a166b-307">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="a166b-308">Par exemple, pour définir un champ de stockage pour la navigation lorsque le champ est introuvable par Convention :</span><span class="sxs-lookup"><span data-stu-id="a166b-308">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="a166b-309">Notez que l' `Navigation` API ne remplace pas la configuration de la relation.</span><span class="sxs-lookup"><span data-stu-id="a166b-309">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="a166b-310">Au lieu de cela, il permet une configuration supplémentaire des propriétés de navigation dans les relations déjà découvertes ou définies.</span><span class="sxs-lookup"><span data-stu-id="a166b-310">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="a166b-311">Consultez la [documentation](xref:core/modeling/relationships#configuring-navigation-properties)sur la configuration des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="a166b-311">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="a166b-312">Nouveaux paramètres de ligne de commande pour les espaces de noms et les chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="a166b-312">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="a166b-313">Les migrations et la génération de modèles automatique permettent désormais de spécifier des espaces de noms sur la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="a166b-313">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="a166b-314">Par exemple, pour rétroconcevoir une base de données en plaçant le contexte et les classes de modèle dans différents espaces de noms :</span><span class="sxs-lookup"><span data-stu-id="a166b-314">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="a166b-315">Pour plus d’informations, consultez la documentation sur les [migrations](xref:core/managing-schemas/migrations/index#namespaces) et l' [ingénierie à rebours](xref:core/managing-schemas/scaffolding#directories-and-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="a166b-315">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="a166b-316">En outre, une chaîne de connexion peut désormais être passée à la `database-update` commande :</span><span class="sxs-lookup"><span data-stu-id="a166b-316">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="a166b-317">Pour plus d’informations, consultez la documentation sur les [Outils](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .</span><span class="sxs-lookup"><span data-stu-id="a166b-317">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="a166b-318">Des paramètres équivalents ont également été ajoutés aux commandes PowerShell utilisées dans la console du gestionnaire de package VS.</span><span class="sxs-lookup"><span data-stu-id="a166b-318">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="a166b-319">EnableDetailedErrors a retourné</span><span class="sxs-lookup"><span data-stu-id="a166b-319">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="a166b-320">Pour des raisons de performances, EF n’effectue pas de vérifications null supplémentaires lors de la lecture des valeurs de la base de données.</span><span class="sxs-lookup"><span data-stu-id="a166b-320">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="a166b-321">Cela peut entraîner des exceptions qui sont difficiles à la cause première lorsqu’une valeur Null inattendue est rencontrée.</span><span class="sxs-lookup"><span data-stu-id="a166b-321">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="a166b-322">L’utilisation de `EnableDetailedErrors` permet d’ajouter une vérification de valeur null supplémentaire aux requêtes de sorte que, pour une faible surcharge de performances, ces erreurs sont plus faciles à suivre à une cause racine.</span><span class="sxs-lookup"><span data-stu-id="a166b-322">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="a166b-323">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-323">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="a166b-324">La documentation est suivie d’un problème [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="a166b-324">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="a166b-325">Clés de partition Cosmos</span><span class="sxs-lookup"><span data-stu-id="a166b-325">Cosmos partition keys</span></span>

<span data-ttu-id="a166b-326">La clé de partition à utiliser pour une requête donnée peut désormais être spécifiée dans la requête.</span><span class="sxs-lookup"><span data-stu-id="a166b-326">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="a166b-327">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-327">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="a166b-328">La documentation est suivie d’un problème [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="a166b-328">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="a166b-329">Prise en charge de la fonction DATALENGTH SQL Server</span><span class="sxs-lookup"><span data-stu-id="a166b-329">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="a166b-330">Vous pouvez y accéder à l’aide de la nouvelle `EF.Functions.DataLength` méthode.</span><span class="sxs-lookup"><span data-stu-id="a166b-330">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="a166b-331">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-331">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="a166b-332">Préversion 2</span><span class="sxs-lookup"><span data-stu-id="a166b-332">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="a166b-333">Utiliser un attribut C# pour spécifier un champ de stockage de propriété</span><span class="sxs-lookup"><span data-stu-id="a166b-333">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="a166b-334">Un attribut C# peut maintenant être utilisé pour spécifier le champ de stockage d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="a166b-334">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="a166b-335">Cet attribut permet à EF Core d’écrire et de lire toujours dans le champ de stockage, ce qui se produit normalement, même si le champ de stockage ne peut pas être trouvé automatiquement.</span><span class="sxs-lookup"><span data-stu-id="a166b-335">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="a166b-336">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-336">For example:</span></span>

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

<span data-ttu-id="a166b-337">La documentation est suivie d’un problème [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="a166b-337">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="a166b-338">Terminer le mappage de discriminateur</span><span class="sxs-lookup"><span data-stu-id="a166b-338">Complete discriminator mapping</span></span>

<span data-ttu-id="a166b-339">EF Core utilise une colonne de discriminateur pour le [mappage TPH d’une hiérarchie d’héritage](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="a166b-339">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="a166b-340">Certaines améliorations des performances sont possibles tant que EF Core connaît toutes les valeurs possibles pour le discriminateur.</span><span class="sxs-lookup"><span data-stu-id="a166b-340">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="a166b-341">EF Core 5,0 implémente désormais ces améliorations.</span><span class="sxs-lookup"><span data-stu-id="a166b-341">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="a166b-342">Par exemple, les versions précédentes de EF Core génèrent toujours ce SQL pour une requête qui retourne tous les types dans une hiérarchie :</span><span class="sxs-lookup"><span data-stu-id="a166b-342">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="a166b-343">EF Core 5,0 génère désormais les éléments suivants lorsqu’un mappage de discriminateur complet est configuré :</span><span class="sxs-lookup"><span data-stu-id="a166b-343">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="a166b-344">Il s’agit du comportement par défaut à partir de Preview 3.</span><span class="sxs-lookup"><span data-stu-id="a166b-344">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="a166b-345">Améliorations des performances dans Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="a166b-345">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="a166b-346">Nous avons apporté deux améliorations des performances pour SQLIte :</span><span class="sxs-lookup"><span data-stu-id="a166b-346">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="a166b-347">La récupération de données binaires et de chaîne avec GetBytes, GetChars et GetTextReader est désormais plus efficace grâce à l’utilisation de SqliteBlob et de flux.</span><span class="sxs-lookup"><span data-stu-id="a166b-347">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="a166b-348">L’initialisation de SqliteConnection est désormais différée.</span><span class="sxs-lookup"><span data-stu-id="a166b-348">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="a166b-349">Ces améliorations se trouvent dans le fournisseur Microsoft. Data. sqlite de ADO.NET et améliorent donc également les performances en dehors de EF Core.</span><span class="sxs-lookup"><span data-stu-id="a166b-349">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="a166b-350">Preview 1</span><span class="sxs-lookup"><span data-stu-id="a166b-350">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="a166b-351">Journalisation simple</span><span class="sxs-lookup"><span data-stu-id="a166b-351">Simple logging</span></span>

<span data-ttu-id="a166b-352">Cette fonctionnalité ajoute des fonctionnalités similaires à `Database.Log` dans EF6.</span><span class="sxs-lookup"><span data-stu-id="a166b-352">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="a166b-353">Autrement dit, il offre un moyen simple d’obtenir des journaux à partir de EF Core sans avoir besoin de configurer tout type d’infrastructure de journalisation externe.</span><span class="sxs-lookup"><span data-stu-id="a166b-353">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="a166b-354">La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="a166b-354">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="a166b-355">Une documentation supplémentaire est suivie par le [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)du problème.</span><span class="sxs-lookup"><span data-stu-id="a166b-355">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="a166b-356">Moyen simple d’utiliser le SQL généré</span><span class="sxs-lookup"><span data-stu-id="a166b-356">Simple way to get generated SQL</span></span>

<span data-ttu-id="a166b-357">EF Core 5,0 introduit la `ToQueryString` méthode d’extension, qui retourne le SQL que EF Core générera lors de l’exécution d’une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="a166b-357">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="a166b-358">La documentation préliminaire est incluse dans l' [État EF Weekly pour le 9 janvier 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="a166b-358">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="a166b-359">Une documentation supplémentaire est suivie par le [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)du problème.</span><span class="sxs-lookup"><span data-stu-id="a166b-359">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="a166b-360">Utiliser un attribut C# pour indiquer qu’une entité n’a pas de clé</span><span class="sxs-lookup"><span data-stu-id="a166b-360">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="a166b-361">Un type d’entité peut désormais être configuré comme n’ayant aucune clé à l’aide de la nouvelle `KeylessAttribute` .</span><span class="sxs-lookup"><span data-stu-id="a166b-361">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="a166b-362">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-362">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="a166b-363">La documentation est suivie d’un problème [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="a166b-363">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="a166b-364">La connexion ou la chaîne de connexion peut être modifiée lors de l’DbContext initialisé</span><span class="sxs-lookup"><span data-stu-id="a166b-364">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="a166b-365">Il est désormais plus facile de créer une instance DbContext sans connexion ou chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="a166b-365">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="a166b-366">En outre, la connexion ou la chaîne de connexion peut désormais être mutée sur l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="a166b-366">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="a166b-367">Cette fonctionnalité permet à la même instance de contexte de se connecter de manière dynamique à différentes bases de données.</span><span class="sxs-lookup"><span data-stu-id="a166b-367">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="a166b-368">La documentation est suivie d’un problème [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="a166b-368">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="a166b-369">Proxys de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="a166b-369">Change-tracking proxies</span></span>

<span data-ttu-id="a166b-370">EF Core pouvez désormais générer des proxies d’exécution qui implémentent automatiquement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) et [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="a166b-370">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="a166b-371">Celles-ci rerapportent ensuite les modifications de valeur sur les propriétés d’entité directement à EF Core, évitant ainsi la nécessité d’analyser les modifications.</span><span class="sxs-lookup"><span data-stu-id="a166b-371">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="a166b-372">Toutefois, les proxies sont fournis avec leur propre ensemble de limitations, donc ils ne le sont pas pour tout le monde.</span><span class="sxs-lookup"><span data-stu-id="a166b-372">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="a166b-373">La documentation est suivie d’un problème [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="a166b-373">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="a166b-374">Vues de débogage améliorées</span><span class="sxs-lookup"><span data-stu-id="a166b-374">Enhanced debug views</span></span>

<span data-ttu-id="a166b-375">Les vues de débogage sont un moyen simple de consulter les éléments internes d’EF Core lors du débogage des problèmes.</span><span class="sxs-lookup"><span data-stu-id="a166b-375">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="a166b-376">Une vue de débogage a été implémentée pour le modèle il y a quelque temps.</span><span class="sxs-lookup"><span data-stu-id="a166b-376">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="a166b-377">Pour EF Core 5,0, nous avons facilité la lecture et l’ajout d’une nouvelle vue de débogage pour les entités suivies dans le gestionnaire d’État.</span><span class="sxs-lookup"><span data-stu-id="a166b-377">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="a166b-378">La documentation préliminaire est incluse dans l' [État EF Weekly pour le 12 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="a166b-378">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="a166b-379">Une documentation supplémentaire est suivie par le [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)du problème.</span><span class="sxs-lookup"><span data-stu-id="a166b-379">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="a166b-380">Amélioration de la gestion de la sémantique null de la base de données</span><span class="sxs-lookup"><span data-stu-id="a166b-380">Improved handling of database null semantics</span></span>

<span data-ttu-id="a166b-381">En général, les bases de données relationnelles traitent la valeur NULL comme une valeur inconnue et, par conséquent, n’est pas égale à une autre valeur NULL.</span><span class="sxs-lookup"><span data-stu-id="a166b-381">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="a166b-382">Alors que C# traite NULL comme une valeur définie, qui compare la valeur à toute autre valeur null.</span><span class="sxs-lookup"><span data-stu-id="a166b-382">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="a166b-383">EF Core par défaut traduit les requêtes afin qu’elles utilisent la sémantique null C#.</span><span class="sxs-lookup"><span data-stu-id="a166b-383">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="a166b-384">EF Core 5,0 améliore considérablement l’efficacité de ces traductions.</span><span class="sxs-lookup"><span data-stu-id="a166b-384">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="a166b-385">La documentation est suivie d’un problème [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="a166b-385">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="a166b-386">Propriétés de l’indexeur</span><span class="sxs-lookup"><span data-stu-id="a166b-386">Indexer properties</span></span>

<span data-ttu-id="a166b-387">EF Core 5,0 prend en charge le mappage des propriétés de l’indexeur C#.</span><span class="sxs-lookup"><span data-stu-id="a166b-387">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="a166b-388">Ces propriétés permettent aux entités d’agir en tant que conteneurs de propriétés où les colonnes sont mappées à des propriétés nommées dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="a166b-388">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="a166b-389">La documentation est suivie d’un problème [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="a166b-389">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="a166b-390">Génération de contraintes de validation pour les mappages d’énumération</span><span class="sxs-lookup"><span data-stu-id="a166b-390">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="a166b-391">EF Core migrations 5,0 peut désormais générer des contraintes de validation pour les mappages de propriété d’énumération.</span><span class="sxs-lookup"><span data-stu-id="a166b-391">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="a166b-392">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-392">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="a166b-393">La documentation est suivie d’un problème [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="a166b-393">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="a166b-394">IsRelational</span><span class="sxs-lookup"><span data-stu-id="a166b-394">IsRelational</span></span>

<span data-ttu-id="a166b-395">Une nouvelle `IsRelational` méthode a été ajoutée en plus des `IsSqlServer` , `IsSqlite` , et existants `IsInMemory` .</span><span class="sxs-lookup"><span data-stu-id="a166b-395">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="a166b-396">Cette méthode peut être utilisée pour tester si DbContext utilise un fournisseur de base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="a166b-396">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="a166b-397">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-397">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="a166b-398">La documentation est suivie d’un problème [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="a166b-398">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="a166b-399">Accès concurrentiel optimiste Cosmos avec ETags</span><span class="sxs-lookup"><span data-stu-id="a166b-399">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="a166b-400">Le fournisseur de base de données Azure Cosmos DB prend désormais en charge l’accès concurrentiel optimiste à l’aide d’ETags.</span><span class="sxs-lookup"><span data-stu-id="a166b-400">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="a166b-401">Utilisez le générateur de modèles de OnModelCreating pour configurer un ETag :</span><span class="sxs-lookup"><span data-stu-id="a166b-401">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="a166b-402">SaveChanges lèvera ensuite un en cas de `DbUpdateConcurrencyException` conflit d’accès concurrentiel, qui [peut être géré](https://docs.microsoft.com/ef/core/saving/concurrency) pour implémenter les nouvelles tentatives, etc.</span><span class="sxs-lookup"><span data-stu-id="a166b-402">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="a166b-403">La documentation est suivie d’un problème [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="a166b-403">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="a166b-404">Traductions de requêtes pour d’autres constructions DateTime</span><span class="sxs-lookup"><span data-stu-id="a166b-404">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="a166b-405">Les requêtes contenant la nouvelle construction DateTime sont maintenant traduites.</span><span class="sxs-lookup"><span data-stu-id="a166b-405">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="a166b-406">En outre, les fonctions de SQL Server suivantes sont maintenant mappées :</span><span class="sxs-lookup"><span data-stu-id="a166b-406">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="a166b-407">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="a166b-407">DateDiffWeek</span></span>
* <span data-ttu-id="a166b-408">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="a166b-408">DateFromParts</span></span>

<span data-ttu-id="a166b-409">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-409">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="a166b-410">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="a166b-410">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="a166b-411">Traductions de requêtes pour d’autres constructions de tableau d’octets</span><span class="sxs-lookup"><span data-stu-id="a166b-411">Query translations for more byte array constructs</span></span>

<span data-ttu-id="a166b-412">Les requêtes utilisant les propriétés Contains, Length, SequenceEqual, etc. sur Byte [] sont désormais traduites en SQL.</span><span class="sxs-lookup"><span data-stu-id="a166b-412">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="a166b-413">La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="a166b-413">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="a166b-414">Une documentation supplémentaire est suivie par le [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)du problème.</span><span class="sxs-lookup"><span data-stu-id="a166b-414">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="a166b-415">Traduction de requête pour l’inverse</span><span class="sxs-lookup"><span data-stu-id="a166b-415">Query translation for Reverse</span></span>

<span data-ttu-id="a166b-416">Les requêtes utilisant `Reverse` sont désormais traduites.</span><span class="sxs-lookup"><span data-stu-id="a166b-416">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="a166b-417">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-417">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="a166b-418">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="a166b-418">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="a166b-419">Traduction des requêtes pour les opérateurs au niveau du bit</span><span class="sxs-lookup"><span data-stu-id="a166b-419">Query translation for bitwise operators</span></span>

<span data-ttu-id="a166b-420">Les requêtes utilisant des opérateurs de bits sont maintenant traduites dans d’autres cas, par exemple :</span><span class="sxs-lookup"><span data-stu-id="a166b-420">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="a166b-421">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="a166b-421">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="a166b-422">Traduction des requêtes pour les chaînes sur Cosmos</span><span class="sxs-lookup"><span data-stu-id="a166b-422">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="a166b-423">Les requêtes qui utilisent les méthodes de chaîne Contains, StartsWith et EndsWith sont désormais traduites lors de l’utilisation du fournisseur Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="a166b-423">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="a166b-424">La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="a166b-424">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
