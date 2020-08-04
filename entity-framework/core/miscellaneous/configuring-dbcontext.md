---
title: Configuration d’un DbContext-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 9614449f6ead393b514f42b718b4cae5f97dfc98
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526418"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="935e6-102">Configuration d’un DbContext</span><span class="sxs-lookup"><span data-stu-id="935e6-102">Configuring a DbContext</span></span>

<span data-ttu-id="935e6-103">Cet article présente des modèles de base pour configurer un `DbContext` via un `DbContextOptions` pour se connecter à une base de données à l’aide d’un fournisseur de EF Core spécifique et de comportements facultatifs.</span><span class="sxs-lookup"><span data-stu-id="935e6-103">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="935e6-104">Configuration de DbContext au moment du design</span><span class="sxs-lookup"><span data-stu-id="935e6-104">Design-time DbContext configuration</span></span>

<span data-ttu-id="935e6-105">EF Core des outils au moment du design, tels que les [migrations](xref:core/managing-schemas/migrations/index) , doivent être en mesure de découvrir et de créer une instance de travail d’un `DbContext` type afin de rassembler des détails sur les types d’entité de l’application et la façon dont ils sont mappés à un schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="935e6-105">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="935e6-106">Ce processus peut être automatique tant que l’outil peut facilement créer le de façon à ce qu' `DbContext` il soit configuré de façon similaire à la façon dont il est configuré au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="935e6-106">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="935e6-107">Alors que tout modèle qui fournit les informations de configuration nécessaires au `DbContext` peut fonctionner au moment de l’exécution, les outils qui nécessitent l’utilisation d’un `DbContext` au moment du design peuvent uniquement fonctionner avec un nombre limité de modèles.</span><span class="sxs-lookup"><span data-stu-id="935e6-107">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="935e6-108">Celles-ci sont traitées plus en détail dans la section [création de contexte au moment du design](xref:core/miscellaneous/cli/dbcontext-creation) .</span><span class="sxs-lookup"><span data-stu-id="935e6-108">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="935e6-109">Configuration de DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="935e6-109">Configuring DbContextOptions</span></span>

<span data-ttu-id="935e6-110">`DbContext`doit avoir une instance de afin `DbContextOptions` d’effectuer n’importe quel travail.</span><span class="sxs-lookup"><span data-stu-id="935e6-110">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="935e6-111">L' `DbContextOptions` instance contient des informations de configuration telles que :</span><span class="sxs-lookup"><span data-stu-id="935e6-111">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="935e6-112">Fournisseur de base de données à utiliser, généralement sélectionné en appelant une méthode telle que `UseSqlServer` ou `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="935e6-112">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="935e6-113">Ces méthodes d’extension requièrent le package du fournisseur correspondant, tel que `Microsoft.EntityFrameworkCore.SqlServer` ou `Microsoft.EntityFrameworkCore.Sqlite` .</span><span class="sxs-lookup"><span data-stu-id="935e6-113">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="935e6-114">Les méthodes sont définies dans l' `Microsoft.EntityFrameworkCore` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="935e6-114">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="935e6-115">Toute chaîne de connexion ou tout identificateur nécessaire de l’instance de base de données, généralement passé comme argument à la méthode de sélection du fournisseur mentionnée ci-dessus</span><span class="sxs-lookup"><span data-stu-id="935e6-115">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="935e6-116">Tous les sélecteurs de comportements facultatifs au niveau du fournisseur, généralement chaînés à l’intérieur de l’appel à la méthode de sélection du fournisseur</span><span class="sxs-lookup"><span data-stu-id="935e6-116">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="935e6-117">Tous les sélecteurs de comportements EF Core généraux, en général chaînés après ou avant la méthode du sélecteur du fournisseur</span><span class="sxs-lookup"><span data-stu-id="935e6-117">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="935e6-118">L’exemple suivant configure `DbContextOptions` pour utiliser le fournisseur SQL Server, une connexion contenue dans la `connectionString` variable, un délai d’attente de commande au niveau du fournisseur et un sélecteur de comportement EF Core qui effectue toutes les requêtes exécutées dans le `DbContext` [sans suivi](xref:core/querying/tracking#no-tracking-queries) par défaut :</span><span class="sxs-lookup"><span data-stu-id="935e6-118">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> <span data-ttu-id="935e6-119">Les méthodes de sélection de fournisseur et d’autres méthodes de sélecteur de comportements mentionnées ci-dessus sont des méthodes d’extension sur les `DbContextOptions` classes d’options spécifiques au fournisseur ou.</span><span class="sxs-lookup"><span data-stu-id="935e6-119">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="935e6-120">Pour pouvoir accéder à ces méthodes d’extension, vous devrez peut-être disposer d’un espace de noms (en général `Microsoft.EntityFrameworkCore` ) dans la portée et inclure des dépendances de package supplémentaires dans le projet.</span><span class="sxs-lookup"><span data-stu-id="935e6-120">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="935e6-121">`DbContextOptions`Peut être fourni à `DbContext` en substituant la `OnConfiguring` méthode ou en externe via un argument de constructeur.</span><span class="sxs-lookup"><span data-stu-id="935e6-121">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="935e6-122">Si les deux sont utilisés, `OnConfiguring` est appliqué en dernier et peut remplacer les options fournies à l’argument du constructeur.</span><span class="sxs-lookup"><span data-stu-id="935e6-122">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="935e6-123">Argument de constructeur</span><span class="sxs-lookup"><span data-stu-id="935e6-123">Constructor argument</span></span>

<span data-ttu-id="935e6-124">Votre constructeur peut simplement accepter un `DbContextOptions` comme suit :</span><span class="sxs-lookup"><span data-stu-id="935e6-124">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]  
> <span data-ttu-id="935e6-125">Le constructeur de base de DbContext accepte également la version non générique de `DbContextOptions` , mais l’utilisation de la version non générique n’est pas recommandée pour les applications avec plusieurs types de contexte.</span><span class="sxs-lookup"><span data-stu-id="935e6-125">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="935e6-126">Votre application peut maintenant passer le `DbContextOptions` lors de l’instanciation d’un contexte, comme suit :</span><span class="sxs-lookup"><span data-stu-id="935e6-126">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="935e6-127">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="935e6-127">OnConfiguring</span></span>

<span data-ttu-id="935e6-128">Vous pouvez également initialiser le `DbContextOptions` dans le contexte lui-même.</span><span class="sxs-lookup"><span data-stu-id="935e6-128">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="935e6-129">Bien que vous puissiez utiliser cette technique pour la configuration de base, vous devrez généralement obtenir certains détails de configuration de l’extérieur, par exemple une chaîne de connexion de base de données.</span><span class="sxs-lookup"><span data-stu-id="935e6-129">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="935e6-130">Pour ce faire, vous pouvez utiliser une API de configuration ou tout autre moyen.</span><span class="sxs-lookup"><span data-stu-id="935e6-130">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="935e6-131">Pour initialiser `DbContextOptions` dans le contexte, substituez la `OnConfiguring` méthode et appelez les méthodes sur le fourni `DbContextOptionsBuilder` :</span><span class="sxs-lookup"><span data-stu-id="935e6-131">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

<span data-ttu-id="935e6-132">Une application peut simplement instancier ce type de contexte sans passer quoi que ce soit à son constructeur :</span><span class="sxs-lookup"><span data-stu-id="935e6-132">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="935e6-133">Cette approche ne se prête pas au test, à moins que les tests ciblent la base de données complète.</span><span class="sxs-lookup"><span data-stu-id="935e6-133">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="935e6-134">Utilisation de DbContext avec l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="935e6-134">Using DbContext with dependency injection</span></span>

<span data-ttu-id="935e6-135">EF Core prend en charge l’utilisation `DbContext` de avec un conteneur d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="935e6-135">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="935e6-136">Votre type DbContext peut être ajouté au conteneur de service à l’aide de la `AddDbContext<TContext>` méthode.</span><span class="sxs-lookup"><span data-stu-id="935e6-136">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="935e6-137">`AddDbContext<TContext>`rendra à la fois votre type DbContext, `TContext` , et le correspondant `DbContextOptions<TContext>` disponible pour l’injection à partir du conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="935e6-137">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="935e6-138">Pour plus d’informations sur l’injection de dépendances, voir [plus de lectures](#more-reading) ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="935e6-138">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="935e6-139">Ajout `DbContext` de à l’injection de dépendances :</span><span class="sxs-lookup"><span data-stu-id="935e6-139">Adding the `DbContext` to dependency injection:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="935e6-140">Cela nécessite l’ajout d’un [argument de constructeur](#constructor-argument) à votre type DbContext qui accepte `DbContextOptions<TContext>` .</span><span class="sxs-lookup"><span data-stu-id="935e6-140">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="935e6-141">Code de contexte :</span><span class="sxs-lookup"><span data-stu-id="935e6-141">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="935e6-142">Code d’application (dans ASP.NET Core) :</span><span class="sxs-lookup"><span data-stu-id="935e6-142">Application code (in ASP.NET Core):</span></span>

``` csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

<span data-ttu-id="935e6-143">Code d’application (à l’aide de ServiceProvider directement, moins courant) :</span><span class="sxs-lookup"><span data-stu-id="935e6-143">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="935e6-144">Éviter les problèmes de thread DbContext</span><span class="sxs-lookup"><span data-stu-id="935e6-144">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="935e6-145">Entity Framework Core ne prend pas en charge l’exécution de plusieurs opérations parallèles sur la même `DbContext` instance.</span><span class="sxs-lookup"><span data-stu-id="935e6-145">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="935e6-146">Cela comprend à la fois l’exécution parallèle des requêtes Async et toute utilisation simultanée explicite à partir de plusieurs threads.</span><span class="sxs-lookup"><span data-stu-id="935e6-146">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="935e6-147">Par conséquent, toujours `await` Async appelle immédiatement, ou utilisez `DbContext` des instances distinctes pour les opérations qui s’exécutent en parallèle.</span><span class="sxs-lookup"><span data-stu-id="935e6-147">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="935e6-148">Lorsque EF Core détecte une tentative d’utilisation d’une `DbContext` instance simultanément, vous voyez un `InvalidOperationException` avec un message semblable à celui-ci :</span><span class="sxs-lookup"><span data-stu-id="935e6-148">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="935e6-149">Une deuxième opération a démarré sur ce contexte avant la fin d’une opération précédente.</span><span class="sxs-lookup"><span data-stu-id="935e6-149">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="935e6-150">Cela est généralement dû à différents threads utilisant la même instance de DbContext, mais il n’est pas garanti que les membres d’instance soient thread-safe.</span><span class="sxs-lookup"><span data-stu-id="935e6-150">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="935e6-151">Lorsque l’accès simultané n’est pas détecté, cela peut entraîner un comportement non défini, des blocages d’application et des données endommagées.</span><span class="sxs-lookup"><span data-stu-id="935e6-151">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="935e6-152">Il existe des erreurs courantes qui peuvent entraîner par inadvertance un accès simultané sur la même `DbContext` instance :</span><span class="sxs-lookup"><span data-stu-id="935e6-152">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="935e6-153">Oubli de l’attente de la fin d’une opération asynchrone avant le démarrage d’une autre opération sur le même DbContext</span><span class="sxs-lookup"><span data-stu-id="935e6-153">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="935e6-154">Les méthodes asynchrones permettent à EF Core de lancer des opérations qui accèdent à la base de données de façon non bloquante.</span><span class="sxs-lookup"><span data-stu-id="935e6-154">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="935e6-155">Toutefois, si un appelant n’attend pas la fin de l’une de ces méthodes et poursuit l’exécution d’autres opérations sur le `DbContext` , l’état du `DbContext` peut être, (et très probablement) endommagé.</span><span class="sxs-lookup"><span data-stu-id="935e6-155">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="935e6-156">Attendez toujours EF Core les méthodes asynchrones immédiatement.</span><span class="sxs-lookup"><span data-stu-id="935e6-156">Always await EF Core asynchronous methods immediately.</span></span>  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="935e6-157">Partage implicite d’instances de DbContext sur plusieurs threads via l’injection de dépendances</span><span class="sxs-lookup"><span data-stu-id="935e6-157">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="935e6-158">La [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) méthode d’extension inscrit les `DbContext` types avec une [durée de vie limitée](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) par défaut.</span><span class="sxs-lookup"><span data-stu-id="935e6-158">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="935e6-159">Cela ne pose pas de problèmes d’accès simultané dans la plupart des applications ASP.NET Core, car il n’existe qu’un seul thread qui exécute chaque demande client à un moment donné, et parce que chaque demande obtient une étendue d’injection de dépendances distincte (et par conséquent une `DbContext` instance distincte).</span><span class="sxs-lookup"><span data-stu-id="935e6-159">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="935e6-160">Pour le modèle d’hébergement de serveur éblouissant, une demande logique est utilisée pour gérer le circuit utilisateur éblouissant, et par conséquent, une seule instance DbContext étendue est disponible par circuit utilisateur si l’étendue d’injection par défaut est utilisée.</span><span class="sxs-lookup"><span data-stu-id="935e6-160">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="935e6-161">Tout code qui exécute explicitement plusieurs threads en parallèle doit s’assurer que les `DbContext` instances ne sont jamais accessibles simultanément.</span><span class="sxs-lookup"><span data-stu-id="935e6-161">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="935e6-162">À l’aide de l’injection de dépendances, cela peut être obtenu en inscrivant le contexte comme étant étendu, en créant des portées (à l’aide de `IServiceScopeFactory` ) pour chaque thread ou en inscrivant le `DbContext` comme transitoire (à l’aide de la surcharge de `AddDbContext` qui accepte un `ServiceLifetime` paramètre).</span><span class="sxs-lookup"><span data-stu-id="935e6-162">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="935e6-163">Plus de lectures</span><span class="sxs-lookup"><span data-stu-id="935e6-163">More reading</span></span>

- <span data-ttu-id="935e6-164">Lisez [injection de dépendances](/aspnet/core/fundamentals/dependency-injection) pour en savoir plus sur l’utilisation de di.</span><span class="sxs-lookup"><span data-stu-id="935e6-164">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="935e6-165">Pour plus d’informations, consultez le [test](testing/index.md) .</span><span class="sxs-lookup"><span data-stu-id="935e6-165">Read [Testing](testing/index.md) for more information.</span></span>
