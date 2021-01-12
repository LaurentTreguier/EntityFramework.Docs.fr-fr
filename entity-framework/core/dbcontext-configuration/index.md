---
title: Durée de vie de DbContext, configuration et initialisation-EF Core
description: Modèles pour la création et la gestion des instances DbContext avec ou sans injection de dépendances
author: ajcvickers
ms.date: 11/07/2020
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 10ed474df2c4c52e61083d9d671909be02cd8cef
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129029"
---
# <a name="dbcontext-lifetime-configuration-and-initialization"></a><span data-ttu-id="c5b5c-103">Durée de vie de DbContext, configuration et initialisation</span><span class="sxs-lookup"><span data-stu-id="c5b5c-103">DbContext Lifetime, Configuration, and Initialization</span></span>

<span data-ttu-id="c5b5c-104">Cet article présente les modèles de base pour l’initialisation et la configuration d’une <xref:Microsoft.EntityFrameworkCore.DbContext> instance de.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-104">This article shows basic patterns for initialization and configuration of a <xref:Microsoft.EntityFrameworkCore.DbContext> instance.</span></span>

## <a name="the-dbcontext-lifetime"></a><span data-ttu-id="c5b5c-105">Durée de vie de DbContext</span><span class="sxs-lookup"><span data-stu-id="c5b5c-105">The DbContext lifetime</span></span>

<span data-ttu-id="c5b5c-106">La durée de vie d’un `DbContext` commence lorsque l’instance est créée et se termine lorsque l’instance est [supprimée](/dotnet/standard/garbage-collection/unmanaged).</span><span class="sxs-lookup"><span data-stu-id="c5b5c-106">The lifetime of a `DbContext` begins when the instance is created and ends when the instance is [disposed](/dotnet/standard/garbage-collection/unmanaged).</span></span> <span data-ttu-id="c5b5c-107">Une `DbContext` instance est conçue pour être utilisée pour une _seule_ [unité de travail](https://www.martinfowler.com/eaaCatalog/unitOfWork.html).</span><span class="sxs-lookup"><span data-stu-id="c5b5c-107">A `DbContext` instance is designed to be used for a _single_ [unit-of-work](https://www.martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="c5b5c-108">Cela signifie que la durée de vie d’une `DbContext` instance est généralement très brève.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-108">This means that the lifetime of a `DbContext` instance is usually very short.</span></span>

> [!TIP]
> <span data-ttu-id="c5b5c-109">Pour citer Martin Fowler à partir du lien ci-dessus, «une unité de travail effectue le suivi de tout ce que vous faites pendant une transaction commerciale qui peut affecter la base de données.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-109">To quote Martin Fowler from the link above, "A Unit of Work keeps track of everything you do during a business transaction that can affect the database.</span></span> <span data-ttu-id="c5b5c-110">Lorsque vous avez terminé, il détermine tout ce qui doit être fait pour modifier la base de données à la suite de votre travail.»</span><span class="sxs-lookup"><span data-stu-id="c5b5c-110">When you're done, it figures out everything that needs to be done to alter the database as a result of your work."</span></span>

<span data-ttu-id="c5b5c-111">Une unité de travail standard lors de l’utilisation de Entity Framework Core (EF Core) implique :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-111">A typical unit-of-work when using Entity Framework Core (EF Core) involves:</span></span>

- <span data-ttu-id="c5b5c-112">Création d’une `DbContext` instance</span><span class="sxs-lookup"><span data-stu-id="c5b5c-112">Creation of a `DbContext` instance</span></span>
- <span data-ttu-id="c5b5c-113">Suivi des instances d’entité par le contexte.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-113">Tracking of entity instances by the context.</span></span> <span data-ttu-id="c5b5c-114">Les entités sont suivies par</span><span class="sxs-lookup"><span data-stu-id="c5b5c-114">Entities become tracked by</span></span>
  - <span data-ttu-id="c5b5c-115">[Retourné à partir d’une requête](xref:core/querying/tracking)</span><span class="sxs-lookup"><span data-stu-id="c5b5c-115">Being [returned from a query](xref:core/querying/tracking)</span></span>
  - <span data-ttu-id="c5b5c-116">[Ajouté ou attaché au contexte](xref:core/saving/disconnected-entities)</span><span class="sxs-lookup"><span data-stu-id="c5b5c-116">Being [added or attached to the context](xref:core/saving/disconnected-entities)</span></span>
- <span data-ttu-id="c5b5c-117">Les entités suivies sont modifiées en fonction des besoins pour implémenter la règle d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-117">Changes are made to the tracked entities as needed to implement the business rule</span></span>
- <span data-ttu-id="c5b5c-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ou <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> is called.</span></span> <span data-ttu-id="c5b5c-119">EF Core détecte les modifications apportées et les écrit dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-119">EF Core detects the changes made and writes them to the database.</span></span>
- <span data-ttu-id="c5b5c-120">L' `DbContext` instance est supprimée</span><span class="sxs-lookup"><span data-stu-id="c5b5c-120">The `DbContext` instance is disposed</span></span>

> [!IMPORTANT]
>
> - <span data-ttu-id="c5b5c-121">Il est très important de supprimer le <xref:Microsoft.EntityFrameworkCore.DbContext> après l’utilisation.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-121">It is very important to dispose the <xref:Microsoft.EntityFrameworkCore.DbContext> after use.</span></span> <span data-ttu-id="c5b5c-122">Cela garantit que toutes les ressources non managées sont libérées et que tous les événements ou autres hooks sont désinscrits afin d’éviter les fuites de mémoire au cas où l’instance resterait référencée.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-122">This ensures both that any unmanaged resources are freed, and that any events or other hooks are unregistered so as to prevent memory leaks in case the instance remains referenced.</span></span>
> - <span data-ttu-id="c5b5c-123">[DbContext n’est **pas thread-safe**](#avoiding-dbcontext-threading-issues).</span><span class="sxs-lookup"><span data-stu-id="c5b5c-123">[DbContext is **not thread-safe**](#avoiding-dbcontext-threading-issues).</span></span> <span data-ttu-id="c5b5c-124">Ne partagez pas de contextes entre les threads.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-124">Do not share contexts between threads.</span></span> <span data-ttu-id="c5b5c-125">Veillez à [attendre](/dotnet/csharp/language-reference/operators/await) tous les appels asynchrones avant de continuer à utiliser l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-125">Make sure to [await](/dotnet/csharp/language-reference/operators/await) all async calls before continuing to use the context instance.</span></span>
> - <span data-ttu-id="c5b5c-126">Une <xref:System.InvalidOperationException> exception levée par EF Core code peut mettre le contexte dans un État irrécupérable.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-126">An <xref:System.InvalidOperationException> thrown by EF Core code can put the context into an unrecoverable state.</span></span> <span data-ttu-id="c5b5c-127">Ces exceptions indiquent une erreur de programme et ne sont pas conçues pour être récupérées à partir de.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-127">Such exceptions indicate a program error and are not designed to be recovered from.</span></span>

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a><span data-ttu-id="c5b5c-128">DbContext dans l’injection de dépendances pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5b5c-128">DbContext in dependency injection for ASP.NET Core</span></span>

<span data-ttu-id="c5b5c-129">Dans de nombreuses applications Web, chaque requête HTTP correspond à une unité de travail unique.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-129">In many web applications, each HTTP request corresponds to a single unit-of-work.</span></span> <span data-ttu-id="c5b5c-130">Cela permet de lier la durée de vie du contexte à celle de la demande d’une bonne valeur par défaut pour les applications Web.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-130">This makes tying the context lifetime to that of the request a good default for web applications.</span></span>

<span data-ttu-id="c5b5c-131">ASP.NET Core applications sont [configurées à l’aide de l’injection de dépendances](/aspnet/core/fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="c5b5c-131">ASP.NET Core applications are [configured using dependency injection](/aspnet/core/fundamentals/startup).</span></span> <span data-ttu-id="c5b5c-132">EF Core peuvent être ajoutés à cette configuration à l’aide de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> dans la [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) méthode de `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-132">EF Core can be added to this configuration using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> in the [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) method of `Startup.cs`.</span></span> <span data-ttu-id="c5b5c-133">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-133">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();

            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

<span data-ttu-id="c5b5c-134">Cet exemple inscrit une sous- `DbContext` classe appelée `ApplicationDbContext` en tant que service étendu dans le fournisseur de services d’application ASP.net Core (également appelé</span><span class="sxs-lookup"><span data-stu-id="c5b5c-134">This example registers a `DbContext` subclass called `ApplicationDbContext` as a scoped service in the ASP.NET Core application service provider (a.k.a.</span></span> <span data-ttu-id="c5b5c-135">conteneur d’injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="c5b5c-135">the dependency injection container).</span></span> <span data-ttu-id="c5b5c-136">Le contexte est configuré pour utiliser le fournisseur de base de données SQL Server et lira la chaîne de connexion à partir de la configuration de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-136">The context is configured to use the SQL Server database provider and will read the connection string from ASP.NET Core configuration.</span></span> <span data-ttu-id="c5b5c-137">Cela n’a généralement pas _d’importance dans_ `ConfigureServices` l’appel à `AddDbContext` .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-137">It typically does not matter _where_ in `ConfigureServices` the call to `AddDbContext` is made.</span></span>

<span data-ttu-id="c5b5c-138">La `ApplicationDbContext` classe doit exposer un constructeur public avec un `DbContextOptions<ApplicationDbContext>` paramètre.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-138">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="c5b5c-139">C’est ainsi que la configuration contextuelle de `AddDbContext` est transmise au `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-139">This is how context configuration from `AddDbContext` is passed to the `DbContext`.</span></span> <span data-ttu-id="c5b5c-140">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-140">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="c5b5c-141">`ApplicationDbContext` peut ensuite être utilisé dans les contrôleurs de ASP.NET Core ou d’autres services via l’injection de constructeur.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-141">`ApplicationDbContext` can then be used in ASP.NET Core controllers or other services through constructor injection.</span></span> <span data-ttu-id="c5b5c-142">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-142">For example:</span></span>

<!--
    public class MyController
    {
        private readonly ApplicationDbContext _context;

        public MyController(ApplicationDbContext context)
        {
            _context = context;
        }
    }
-->
[!code-csharp[MyController](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Controllers/MyController.cs?name=MyController)]

<span data-ttu-id="c5b5c-143">Le résultat final est une `ApplicationDbContext` instance créée pour chaque demande et transmise au contrôleur pour exécuter une unité de travail avant d’être supprimée à la fin de la demande.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-143">The final result is an `ApplicationDbContext` instance created for each request and passed to the controller to perform a unit-of-work before being disposed when the request ends.</span></span>

<span data-ttu-id="c5b5c-144">Pour en savoir plus sur les options de configuration, consultez cet article.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-144">Read further in this article to learn more about configuration options.</span></span> <span data-ttu-id="c5b5c-145">En outre, consultez [démarrage de l’application dans ASP.net Core](/aspnet/core/fundamentals/startup) et [injection de dépendances dans ASP.net Core](/aspnet/core/fundamentals/dependency-injection) pour plus d’informations sur la configuration et l’injection de dépendances dans ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-145">In addition, see [App startup in ASP.NET Core](/aspnet/core/fundamentals/startup) and [Dependency injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) for more information on configuration and dependency injection in ASP.NET Core.</span></span>

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a><span data-ttu-id="c5b5c-146">Initialisation de DbContext simple avec’New'</span><span class="sxs-lookup"><span data-stu-id="c5b5c-146">Simple DbContext initialization with 'new'</span></span>

<span data-ttu-id="c5b5c-147">`DbContext` les instances peuvent être construites dans le sens .NET normal, par exemple avec `new` en C#.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-147">`DbContext` instances can be constructed in the normal .NET way, for example with `new` in C#.</span></span> <span data-ttu-id="c5b5c-148">La configuration peut être effectuée en substituant la `OnConfiguring` méthode ou en passant des options au constructeur.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-148">Configuration can be performed by overriding the `OnConfiguring` method, or by passing options to the constructor.</span></span> <span data-ttu-id="c5b5c-149">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-149">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="c5b5c-150">Ce modèle facilite également la réussite de la configuration comme la chaîne de connexion par le biais du `DbContext` constructeur.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-150">This pattern also makes it easy to pass configuration like the connection string via the `DbContext` constructor.</span></span> <span data-ttu-id="c5b5c-151">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-151">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        private readonly string _connectionString;

        public ApplicationDbContext(string connectionString)
        {
            _connectionString = connectionString;
        }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(_connectionString);
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNewAndArgs/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="c5b5c-152">`DbContextOptionsBuilder`Vous pouvez également utiliser pour créer un `DbContextOptions` objet qui est ensuite passé au `DbContext` constructeur.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-152">Alternately, `DbContextOptionsBuilder` can be used to create a `DbContextOptions` object that is then passed to the `DbContext` constructor.</span></span> <span data-ttu-id="c5b5c-153">Cela permet `DbContext` à un configuré pour l’injection de dépendances d’être également construit explicitement.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-153">This allows a `DbContext` configured for dependency injection to also be constructed explicitly.</span></span> <span data-ttu-id="c5b5c-154">Par exemple, lors de l’utilisation `ApplicationDbContext` de défini pour ASP.net Core Web Apps ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-154">For example, when using `ApplicationDbContext` defined for ASP.NET Core web apps above:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="c5b5c-155">Le `DbContextOptions` peut être créé et le constructeur peut être appelé explicitement :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-155">The `DbContextOptions` can be created and the constructor can be called explicitly:</span></span>

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a><span data-ttu-id="c5b5c-156">Utilisation d’une fabrique DbContext (par exemple, pour éblouissant)</span><span class="sxs-lookup"><span data-stu-id="c5b5c-156">Using a DbContext factory (e.g. for Blazor)</span></span>

<span data-ttu-id="c5b5c-157">Certains types d’applications (par exemple, [ASP.net Core éblouissant](/aspnet/core/blazor/)) utilisent l’injection de dépendances, mais ne créent pas une étendue de service qui s’aligne sur la `DbContext` durée de vie souhaitée.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-157">Some application types (e.g. [ASP.NET Core Blazor](/aspnet/core/blazor/)) use dependency injection but do not create a service scope that aligns with the desired `DbContext` lifetime.</span></span> <span data-ttu-id="c5b5c-158">Même dans ce cas, l’application devra peut-être effectuer plusieurs unités de travail au sein de cette étendue.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-158">Even where such an alignment does exist, the application may need to perform multiple units-of-work within this scope.</span></span> <span data-ttu-id="c5b5c-159">Par exemple, plusieurs unités de travail au sein d’une même requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-159">For example, multiple units-of-work within a single HTTP request.</span></span>

<span data-ttu-id="c5b5c-160">Dans ces cas-là, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> peut être utilisé pour inscrire une fabrique pour la création d' `DbContext` instances de.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-160">In these cases, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> can be used to register a factory for creation of `DbContext` instances.</span></span> <span data-ttu-id="c5b5c-161">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-161">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

<span data-ttu-id="c5b5c-162">La `ApplicationDbContext` classe doit exposer un constructeur public avec un `DbContextOptions<ApplicationDbContext>` paramètre.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-162">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="c5b5c-163">Il s’agit du même modèle que celui utilisé dans la section ASP.NET Core traditionnelle ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-163">This is the same pattern as used in the traditional ASP.NET Core section above.</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="c5b5c-164">La `DbContextFactory` fabrique peut ensuite être utilisée dans d’autres services via l’injection de constructeur.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-164">The `DbContextFactory` factory can then be used in other services through constructor injection.</span></span> <span data-ttu-id="c5b5c-165">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-165">For example:</span></span>

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

<span data-ttu-id="c5b5c-166">La fabrique injectée peut ensuite être utilisée pour construire des instances DbContext dans le code de service.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-166">The injected factory can then be used to construct DbContext instances in the service code.</span></span> <span data-ttu-id="c5b5c-167">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-167">For example:</span></span>

<!--
        public void DoSomething()
        {
            using (var context = _contextFactory.CreateDbContext())
            {
                // ...
            }
        }
-->
[!code-csharp[DoSomething](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=DoSomething)]

<span data-ttu-id="c5b5c-168">Notez que les `DbContext` instances créées de cette façon **ne sont pas** gérées par le fournisseur de services de l’application et doivent donc être supprimées par l’application.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-168">Notice that the `DbContext` instances created in this way are **not** managed by the application's service provider and therefore must be disposed by the application.</span></span>

<span data-ttu-id="c5b5c-169">Pour plus d’informations sur l’utilisation de EF Core avec éblouissant, consultez [ASP.net Core serveur éblouissant avec Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-169">See [ASP.NET Core Blazor Server with Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) for more information on using EF Core with Blazor.</span></span>

## <a name="dbcontextoptions"></a><span data-ttu-id="c5b5c-170">DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="c5b5c-170">DbContextOptions</span></span>

<span data-ttu-id="c5b5c-171">Le point de départ de toutes les `DbContext` configurations est <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-171">The starting point for all `DbContext` configuration is <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="c5b5c-172">Il existe trois façons d’utiliser ce générateur :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-172">There are three ways to get this builder:</span></span>

- <span data-ttu-id="c5b5c-173">Dans `AddDbContext` et méthodes associées</span><span class="sxs-lookup"><span data-stu-id="c5b5c-173">In `AddDbContext` and related methods</span></span>
- <span data-ttu-id="c5b5c-174">Dans `OnConfiguring`</span><span class="sxs-lookup"><span data-stu-id="c5b5c-174">In `OnConfiguring`</span></span>
- <span data-ttu-id="c5b5c-175">Construit explicitement avec `new`</span><span class="sxs-lookup"><span data-stu-id="c5b5c-175">Constructed explicitly with `new`</span></span>

<span data-ttu-id="c5b5c-176">Des exemples de chacun d’entre eux sont présentés dans les sections précédentes.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-176">Examples of each of these are shown in the preceding sections.</span></span> <span data-ttu-id="c5b5c-177">La même configuration peut être appliquée, quel que soit l’emplacement d’où le générateur provient.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-177">The same configuration can be applied regardless of where the builder comes from.</span></span> <span data-ttu-id="c5b5c-178">En outre, `OnConfiguring` est toujours appelé, quelle que soit la façon dont le contexte est construit.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-178">In addition, `OnConfiguring` is always called regardless of how the context is constructed.</span></span> <span data-ttu-id="c5b5c-179">Cela `OnConfiguring` peut être utilisé pour effectuer une configuration supplémentaire même lorsque `AddDbContext` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-179">This means `OnConfiguring` can be used to perform additional configuration even when `AddDbContext` is being used.</span></span>

### <a name="configuring-the-database-provider"></a><span data-ttu-id="c5b5c-180">Configuration du fournisseur de base de données</span><span class="sxs-lookup"><span data-stu-id="c5b5c-180">Configuring the database provider</span></span>

<span data-ttu-id="c5b5c-181">Chaque `DbContext` instance doit être configurée pour utiliser un et un seul fournisseur de base de données.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-181">Each `DbContext` instance must be configured to use one and only one database provider.</span></span> <span data-ttu-id="c5b5c-182">(Les différentes instances d’un `DbContext` sous-type peuvent être utilisées avec différents fournisseurs de bases de données, mais une seule instance ne doit en utiliser qu’une seule.) Un fournisseur de base de données est configuré à l’aide d’un `Use*` appel spécifique.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-182">(Different instances of a `DbContext` subtype can be used with different database providers, but a single instance must only use one.) A database provider is configured using a specific `Use*`" call.</span></span> <span data-ttu-id="c5b5c-183">Par exemple, pour utiliser le fournisseur de base de données SQL Server :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-183">For example, to use the SQL Server database provider:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="c5b5c-184">Ces `Use*` méthodes sont des méthodes d’extension implémentées par le fournisseur de base de données.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-184">These `Use*`" methods are extension methods implemented by the database provider.</span></span> <span data-ttu-id="c5b5c-185">Cela signifie que le package NuGet du fournisseur de base de données doit être installé pour permettre l’utilisation de la méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-185">This means that the database provider NuGet package must be installed before the extension method can be used.</span></span>

> [!TIP]
> <span data-ttu-id="c5b5c-186">EF Core fournisseurs de bases de données utilisent de manière intensive les [méthodes d’extension](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods).</span><span class="sxs-lookup"><span data-stu-id="c5b5c-186">EF Core database providers make extensive use of [extension methods](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods).</span></span> <span data-ttu-id="c5b5c-187">Si le compilateur indique qu’une méthode est introuvable, vérifiez que le package NuGet du fournisseur est installé et que vous avez `using Microsoft.EntityFrameworkCore;` dans votre code.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-187">If the compiler indicates that a method cannot be found, then make sure that the provider's NuGet package is installed and that you have `using Microsoft.EntityFrameworkCore;` in your code.</span></span>

<span data-ttu-id="c5b5c-188">Le tableau suivant contient des exemples pour les fournisseurs de bases de données courants.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-188">The following table contains examples for common database providers.</span></span>

| <span data-ttu-id="c5b5c-189">Système de base de données</span><span class="sxs-lookup"><span data-stu-id="c5b5c-189">Database system</span></span>              | <span data-ttu-id="c5b5c-190">Exemple de configuration</span><span class="sxs-lookup"><span data-stu-id="c5b5c-190">Example configuration</span></span>                         | <span data-ttu-id="c5b5c-191">Package NuGet</span><span class="sxs-lookup"><span data-stu-id="c5b5c-191">NuGet package</span></span>
|:-----------------------------|-----------------------------------------------|--------------
| <span data-ttu-id="c5b5c-192">SQL Server ou SQL Azure</span><span class="sxs-lookup"><span data-stu-id="c5b5c-192">SQL Server or Azure SQL</span></span>      | `.UseSqlServer(connectionString)`             | [<span data-ttu-id="c5b5c-193">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="c5b5c-193">Microsoft.EntityFrameworkCore.SqlServer</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| <span data-ttu-id="c5b5c-194">Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="c5b5c-194">Azure Cosmos DB</span></span>              | `.UseCosmos(connectionString, databaseName)`  | [<span data-ttu-id="c5b5c-195">Microsoft.EntityFrameworkCore.Cosmos</span><span class="sxs-lookup"><span data-stu-id="c5b5c-195">Microsoft.EntityFrameworkCore.Cosmos</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| <span data-ttu-id="c5b5c-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="c5b5c-196">SQLite</span></span>                       | `.UseSqlite(connectionString)`                | [<span data-ttu-id="c5b5c-197">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="c5b5c-197">Microsoft.EntityFrameworkCore.Sqlite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| <span data-ttu-id="c5b5c-198">Fournisseur en mémoire EF Core</span><span class="sxs-lookup"><span data-stu-id="c5b5c-198">EF Core in-memory database</span></span>   | `.UseInMemoryDatabase(databaseName)`          | [<span data-ttu-id="c5b5c-199">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="c5b5c-199">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| <span data-ttu-id="c5b5c-200">PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="c5b5c-200">PostgreSQL\*</span></span>                  | `.UseNpgsql(connectionString)`                | [<span data-ttu-id="c5b5c-201">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="c5b5c-201">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| <span data-ttu-id="c5b5c-202">MySQL/MariaDB \*</span><span class="sxs-lookup"><span data-stu-id="c5b5c-202">MySQL/MariaDB\*</span></span>               | `.UseMySql((connectionString)`                | [<span data-ttu-id="c5b5c-203">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="c5b5c-203">Pomelo.EntityFrameworkCore.MySql</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| <span data-ttu-id="c5b5c-204">Oracle\*</span><span class="sxs-lookup"><span data-stu-id="c5b5c-204">Oracle\*</span></span>                      | `.UseOracle(connectionString)`                | [<span data-ttu-id="c5b5c-205">Oracle.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="c5b5c-205">Oracle.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

<span data-ttu-id="c5b5c-206">\* Ces fournisseurs de bases de données ne sont pas fournis par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-206">\*These database providers are not shipped by Microsoft.</span></span> <span data-ttu-id="c5b5c-207">Pour plus d’informations sur les fournisseurs de base de données, consultez [fournisseurs de](xref:core/providers/index) bases de données.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-207">See [Database Providers](xref:core/providers/index) for more information about database providers.</span></span>

> [!WARNING]
> <span data-ttu-id="c5b5c-208">La base de données en mémoire EF Core n’est pas conçue pour une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-208">The EF Core in-memory database is not designed for production use.</span></span> <span data-ttu-id="c5b5c-209">En outre, il n’est peut-être pas le meilleur choix pour les tests.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-209">In addition, it may not be the best choice even for testing.</span></span> <span data-ttu-id="c5b5c-210">Pour plus d’informations, consultez [test du code qui utilise EF Core](xref:core/testing/index) .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-210">See [Testing Code That Uses EF Core](xref:core/testing/index) for more information.</span></span>

<span data-ttu-id="c5b5c-211">Consultez [chaînes de connexion](xref:core/miscellaneous/connection-strings) pour plus d’informations sur l’utilisation de chaînes de connexion avec EF Core.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-211">See [Connection Strings](xref:core/miscellaneous/connection-strings) for more information on using connection strings with EF Core.</span></span>

<span data-ttu-id="c5b5c-212">Une configuration facultative spécifique au fournisseur de base de données est effectuée dans un générateur supplémentaire spécifique au fournisseur.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-212">Optional configuration specific to the database provider is performed in an additional provider-specific builder.</span></span> <span data-ttu-id="c5b5c-213">Par exemple, l’utilisation <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> de pour configurer les nouvelles tentatives de résilience des connexions lors de la connexion à Azure SQL :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-213">For example, using <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> to configure retries for connection resiliency when connecting to Azure SQL:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .UseSqlServer(
                    @"Server=(localdb)\mssqllocaldb;Database=Test",
                    providerOptions =>
                        {
                            providerOptions.EnableRetryOnFailure();
                        });
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalProviderConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

> [!TIP]
> <span data-ttu-id="c5b5c-214">Le même fournisseur de base de données est utilisé pour SQL Server et Azure SQL.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-214">The same database provider is used for SQL Server and Azure SQL.</span></span> <span data-ttu-id="c5b5c-215">Toutefois, il est recommandé d’utiliser la [résilience des connexions](xref:core/miscellaneous/connection-resiliency) lors de la connexion à SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-215">However, it is recommended that [connection resiliency](xref:core/miscellaneous/connection-resiliency) be used when connecting to SQL Azure.</span></span>

<span data-ttu-id="c5b5c-216">Pour plus d’informations sur la configuration spécifique au fournisseur, consultez [fournisseurs de bases de données](xref:core/providers/index) .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-216">See [Database Providers](xref:core/providers/index) for more information on provider-specific configuration.</span></span>

### <a name="other-dbcontext-configuration"></a><span data-ttu-id="c5b5c-217">Autre configuration de DbContext</span><span class="sxs-lookup"><span data-stu-id="c5b5c-217">Other DbContext configuration</span></span>

<span data-ttu-id="c5b5c-218">`DbContext`Une autre configuration peut être chaînée avant ou après (il n’y a aucune différence) avec l' `Use*` appel.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-218">Other `DbContext` configuration can be chained either before or after (it makes no difference which) the `Use*` call.</span></span> <span data-ttu-id="c5b5c-219">Par exemple, pour activer la journalisation des données sensibles :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-219">For example, to turn on sensitive-data logging:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .EnableSensitiveDataLogging()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="c5b5c-220">Le tableau suivant contient des exemples de méthodes courantes appelées sur `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-220">The following table contains examples of common methods called on `DbContextOptionsBuilder`.</span></span>

| <span data-ttu-id="c5b5c-221">Méthode DbContextOptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="c5b5c-221">DbContextOptionsBuilder method</span></span>                                                             | <span data-ttu-id="c5b5c-222">Résultat</span><span class="sxs-lookup"><span data-stu-id="c5b5c-222">What it does</span></span>                                                | <span data-ttu-id="c5b5c-223">En savoir plus</span><span class="sxs-lookup"><span data-stu-id="c5b5c-223">Learn more</span></span>
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | <span data-ttu-id="c5b5c-224">Définit le comportement de suivi par défaut pour les requêtes</span><span class="sxs-lookup"><span data-stu-id="c5b5c-224">Sets the default tracking behavior for queries</span></span>              | [<span data-ttu-id="c5b5c-225">Comportement du suivi des requêtes</span><span class="sxs-lookup"><span data-stu-id="c5b5c-225">Query Tracking Behavior</span></span>](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | <span data-ttu-id="c5b5c-226">Un moyen simple d’obtenir des journaux de EF Core (EF Core 5,0 et versions ultérieures)</span><span class="sxs-lookup"><span data-stu-id="c5b5c-226">A simple way to get EF Core logs (EF Core 5.0 and later)</span></span>    | [<span data-ttu-id="c5b5c-227">Journalisation, événements et diagnostics</span><span class="sxs-lookup"><span data-stu-id="c5b5c-227">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | <span data-ttu-id="c5b5c-228">Inscrit une `Microsoft.Extensions.Logging` fabrique</span><span class="sxs-lookup"><span data-stu-id="c5b5c-228">Registers an `Microsoft.Extensions.Logging` factory</span></span>         | [<span data-ttu-id="c5b5c-229">Journalisation, événements et diagnostics</span><span class="sxs-lookup"><span data-stu-id="c5b5c-229">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | <span data-ttu-id="c5b5c-230">Comprend les données d’application dans les exceptions et la journalisation</span><span class="sxs-lookup"><span data-stu-id="c5b5c-230">Includes application data in exceptions and logging</span></span>         | [<span data-ttu-id="c5b5c-231">Journalisation, événements et diagnostics</span><span class="sxs-lookup"><span data-stu-id="c5b5c-231">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | <span data-ttu-id="c5b5c-232">Erreurs de requête plus détaillées (au détriment des performances)</span><span class="sxs-lookup"><span data-stu-id="c5b5c-232">More detailed query errors (at the expense of performance)</span></span>  | [<span data-ttu-id="c5b5c-233">Journalisation, événements et diagnostics</span><span class="sxs-lookup"><span data-stu-id="c5b5c-233">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | <span data-ttu-id="c5b5c-234">Ignorer ou lever pour les avertissements et autres événements</span><span class="sxs-lookup"><span data-stu-id="c5b5c-234">Ignore or throw for warnings and other events</span></span>               | [<span data-ttu-id="c5b5c-235">Journalisation, événements et diagnostics</span><span class="sxs-lookup"><span data-stu-id="c5b5c-235">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | <span data-ttu-id="c5b5c-236">Inscrit EF Core intercepteurs</span><span class="sxs-lookup"><span data-stu-id="c5b5c-236">Registers EF Core interceptors</span></span>                              | [<span data-ttu-id="c5b5c-237">Journalisation, événements et diagnostics</span><span class="sxs-lookup"><span data-stu-id="c5b5c-237">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | <span data-ttu-id="c5b5c-238">Utiliser des proxies dynamiques pour le chargement différé</span><span class="sxs-lookup"><span data-stu-id="c5b5c-238">Use dynamic proxies for lazy-loading</span></span>                        | [<span data-ttu-id="c5b5c-239">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="c5b5c-239">Lazy Loading</span></span>](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | <span data-ttu-id="c5b5c-240">Utiliser des proxies dynamiques pour le suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="c5b5c-240">Use dynamic proxies for change-tracking</span></span>                     | <span data-ttu-id="c5b5c-241">Bientôt disponible...</span><span class="sxs-lookup"><span data-stu-id="c5b5c-241">Coming soon...</span></span>

> [!NOTE]
> <span data-ttu-id="c5b5c-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> et <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> sont des méthodes d’extension du package NuGet [Microsoft. EntityFrameworkCore. proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> and <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> are extension methods from the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package.</span></span> <span data-ttu-id="c5b5c-243">Ce type de «. L’appel de UseSomething () est la méthode recommandée pour configurer et/ou utiliser des extensions de EF Core contenues dans d’autres packages.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-243">This kind of ".UseSomething()" call is the recommended way to configure and/or use EF Core extensions contained in other packages.</span></span>

### <a name="dbcontextoptions-verses-dbcontextoptionstcontext"></a><span data-ttu-id="c5b5c-244">`DbContextOptions` contre `DbContextOptions<TContext>`</span><span class="sxs-lookup"><span data-stu-id="c5b5c-244">`DbContextOptions` verses `DbContextOptions<TContext>`</span></span>

<span data-ttu-id="c5b5c-245">La plupart des `DbContext` sous-classes qui acceptent un `DbContextOptions` doivent utiliser la variation [générique](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-245">Most `DbContext` subclasses that accept a `DbContextOptions` should use the [generic](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` variation.</span></span> <span data-ttu-id="c5b5c-246">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-246">For example:</span></span>

<!--
    public sealed class SealedApplicationDbContext : DbContext
    {
        public SealedApplicationDbContext(DbContextOptions<SealedApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[SealedApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=SealedApplicationDbContext)]

<span data-ttu-id="c5b5c-247">Cela permet de s’assurer que les options correctes pour le `DbContext` sous-type spécifique sont résolues à partir de l’injection de dépendances, même lorsque plusieurs `DbContext` sous-types sont inscrits.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-247">This ensures that the correct options for the specific `DbContext` subtype are resolved from dependency injection, even when multiple `DbContext` subtypes are registered.</span></span>

> [!TIP]
> <span data-ttu-id="c5b5c-248">Votre DbContext n’a pas besoin d’être sealed, mais la fermeture est recommandée pour les classes qui ne sont pas destinées à être héritées de.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-248">Your DbContext does not need to be sealed, but sealing is best practice to do so for classes not designed to be inherited from.</span></span>

<span data-ttu-id="c5b5c-249">Toutefois, si le `DbContext` sous-type est lui-même destiné à être hérité de, il doit exposer un constructeur protégé qui prend un non générique `DbContextOptions` .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-249">However, if the `DbContext` subtype is itself intended to be inherited from, then it should expose a protected constructor taking a non-generic `DbContextOptions`.</span></span> <span data-ttu-id="c5b5c-250">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-250">For example:</span></span>

<!--
    public abstract class ApplicationDbContextBase : DbContext
    {
        protected ApplicationDbContextBase(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContextBase](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContextBase)]

<span data-ttu-id="c5b5c-251">Cela permet à plusieurs sous-classes concrètes d’appeler ce constructeur de base à l’aide de leurs différentes `DbContextOptions<TContext>` instances génériques.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-251">This allows multiple concrete subclasses to call this base constructor using their different generic `DbContextOptions<TContext>` instances.</span></span> <span data-ttu-id="c5b5c-252">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-252">For example:</span></span>

<!--
    public sealed class ApplicationDbContext1 : ApplicationDbContextBase
    {
        public ApplicationDbContext1(DbContextOptions<ApplicationDbContext1> contextOptions)
            : base(contextOptions)
        {
        }
    }

    public sealed class ApplicationDbContext2 : ApplicationDbContextBase
    {
        public ApplicationDbContext2(DbContextOptions<ApplicationDbContext2> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext12](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext12)]

<span data-ttu-id="c5b5c-253">Notez qu’il s’agit exactement du même modèle que lorsqu’il hérite `DbContext` directement de.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-253">Notice that this is exactly the same pattern as when inheriting from `DbContext` directly.</span></span> <span data-ttu-id="c5b5c-254">Autrement dit, le `DbContext` constructeur lui-même accepte un non générique `DbContextOptions` pour cette raison.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-254">That is, the `DbContext` constructor itself accepts a non-generic `DbContextOptions` for this reason.</span></span>

<span data-ttu-id="c5b5c-255">Une `DbContext` sous-classe destinée à être instanciée et héritée de doit exposer les deux formes de constructeur.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-255">A `DbContext` subclass intended to be both instantiated and inherited from should expose both forms of constructor.</span></span> <span data-ttu-id="c5b5c-256">Exemple :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-256">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }

        protected ApplicationDbContext(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext)]

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="c5b5c-257">Configuration de DbContext au moment du design</span><span class="sxs-lookup"><span data-stu-id="c5b5c-257">Design-time DbContext configuration</span></span>

<span data-ttu-id="c5b5c-258">EF Core des outils au moment du design, tels que ceux des [migrations EF Core](xref:core/managing-schemas/migrations/index) , doivent être en mesure de découvrir et de créer une instance de travail d’un type afin de `DbContext` rassembler des détails sur les types d’entité de l’application et la façon dont ils sont mappés à un schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-258">EF Core design-time tools such as those for [EF Core migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="c5b5c-259">Ce processus peut être automatique tant que l’outil peut facilement créer le de façon à ce qu' `DbContext` il soit configuré de façon similaire à la façon dont il est configuré au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-259">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="c5b5c-260">Alors que tout modèle qui fournit les informations de configuration nécessaires au `DbContext` peut fonctionner au moment de l’exécution, les outils qui nécessitent l’utilisation d’un `DbContext` au moment du design peuvent uniquement fonctionner avec un nombre limité de modèles.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-260">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="c5b5c-261">Elles sont traitées plus en détail dans la section [création de contexte au moment du design](xref:core/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="c5b5c-261">These are covered in more detail in [Design-Time Context Creation](xref:core/cli/dbcontext-creation).</span></span>

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="c5b5c-262">Éviter les problèmes de thread DbContext</span><span class="sxs-lookup"><span data-stu-id="c5b5c-262">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="c5b5c-263">Entity Framework Core ne prend pas en charge l’exécution de plusieurs opérations parallèles sur la même `DbContext` instance.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-263">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="c5b5c-264">Cela comprend à la fois l’exécution parallèle des requêtes Async et toute utilisation simultanée explicite à partir de plusieurs threads.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-264">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="c5b5c-265">Par conséquent, toujours `await` Async appelle immédiatement, ou utilisez `DbContext` des instances distinctes pour les opérations qui s’exécutent en parallèle.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-265">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="c5b5c-266">Lorsque EF Core détecte une tentative d’utilisation d’une `DbContext` instance simultanément, vous voyez un `InvalidOperationException` avec un message semblable à celui-ci :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-266">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="c5b5c-267">Une deuxième opération a démarré sur ce contexte avant la fin d’une opération précédente.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-267">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="c5b5c-268">Cela est généralement dû à différents threads utilisant la même instance de DbContext, mais il n’est pas garanti que les membres d’instance soient thread-safe.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-268">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="c5b5c-269">Lorsque l’accès simultané n’est pas détecté, cela peut entraîner un comportement non défini, des blocages d’application et des données endommagées.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-269">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="c5b5c-270">Il existe des erreurs courantes qui peuvent entraîner par inadvertance un accès simultané sur la même `DbContext` instance :</span><span class="sxs-lookup"><span data-stu-id="c5b5c-270">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="asynchronous-operation-pitfalls"></a><span data-ttu-id="c5b5c-271">Pièges de l’opération asynchrone</span><span class="sxs-lookup"><span data-stu-id="c5b5c-271">Asynchronous operation pitfalls</span></span>

<span data-ttu-id="c5b5c-272">Les méthodes asynchrones permettent à EF Core de lancer des opérations qui accèdent à la base de données de façon non bloquante.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-272">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="c5b5c-273">Toutefois, si un appelant n’attend pas la fin de l’une de ces méthodes et poursuit l’exécution d’autres opérations sur le `DbContext` , l’état du `DbContext` peut être, (et très probablement) endommagé.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-273">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="c5b5c-274">Attendez toujours EF Core les méthodes asynchrones immédiatement.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-274">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a><span data-ttu-id="c5b5c-275">Partage implicite d’instances de DbContext via une injection de dépendance</span><span class="sxs-lookup"><span data-stu-id="c5b5c-275">Implicitly sharing DbContext instances via dependency injection</span></span>

<span data-ttu-id="c5b5c-276">La [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) méthode d’extension inscrit les `DbContext` types avec une [durée de vie limitée](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) par défaut.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-276">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="c5b5c-277">Cela ne pose pas de problèmes d’accès simultané dans la plupart des applications ASP.NET Core, car il n’existe qu’un seul thread qui exécute chaque demande client à un moment donné, et parce que chaque demande obtient une étendue d’injection de dépendances distincte (et par conséquent une `DbContext` instance distincte).</span><span class="sxs-lookup"><span data-stu-id="c5b5c-277">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="c5b5c-278">Pour le modèle d’hébergement de serveur éblouissant, une demande logique est utilisée pour gérer le circuit utilisateur éblouissant, et par conséquent, une seule instance DbContext étendue est disponible par circuit utilisateur si l’étendue d’injection par défaut est utilisée.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-278">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="c5b5c-279">Tout code qui exécute explicitement plusieurs threads en parallèle doit s’assurer que les `DbContext` instances ne sont jamais accessibles simultanément.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-279">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="c5b5c-280">À l’aide de l’injection de dépendances, cela peut être obtenu en inscrivant le contexte comme étant étendu, en créant des portées (à l’aide de `IServiceScopeFactory` ) pour chaque thread ou en inscrivant le `DbContext` comme transitoire (à l’aide de la surcharge de `AddDbContext` qui accepte un `ServiceLifetime` paramètre).</span><span class="sxs-lookup"><span data-stu-id="c5b5c-280">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="c5b5c-281">Plus de lectures</span><span class="sxs-lookup"><span data-stu-id="c5b5c-281">More reading</span></span>

- <span data-ttu-id="c5b5c-282">Lisez [injection de dépendances](/aspnet/core/fundamentals/dependency-injection) pour en savoir plus sur l’utilisation de di.</span><span class="sxs-lookup"><span data-stu-id="c5b5c-282">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="c5b5c-283">Pour plus d’informations, consultez le [test](xref:core/testing/index) .</span><span class="sxs-lookup"><span data-stu-id="c5b5c-283">Read [Testing](xref:core/testing/index) for more information.</span></span>
