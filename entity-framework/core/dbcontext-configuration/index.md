---
title: Durée de vie de DbContext, configuration et initialisation-EF Core
description: Modèles pour la création et la gestion des instances DbContext avec ou sans injection de dépendances
author: ajcvickers
ms.date: 11/07/2020
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 5b3143edbcfb82312b8026fb09c96dac85427831
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983519"
---
# <a name="dbcontext-lifetime-configuration-and-initialization"></a>Durée de vie de DbContext, configuration et initialisation

Cet article présente les modèles de base pour l’initialisation et la configuration d’une <xref:Microsoft.EntityFrameworkCore.DbContext> instance de.

## <a name="the-dbcontext-lifetime"></a>Durée de vie de DbContext

La durée de vie d’un `DbContext` commence lorsque l’instance est créée et se termine lorsque l’instance est [supprimée](/dotnet/standard/garbage-collection/unmanaged). Une `DbContext` instance est conçue pour être utilisée pour une _seule_ [unité de travail](https://www.martinfowler.com/eaaCatalog/unitOfWork.html). Cela signifie que la durée de vie d’une `DbContext` instance est généralement très brève.

> [!TIP]
> Pour citer Martin Fowler à partir du lien ci-dessus, «une unité de travail effectue le suivi de tout ce que vous faites pendant une transaction commerciale qui peut affecter la base de données. Lorsque vous avez terminé, il détermine tout ce qui doit être fait pour modifier la base de données à la suite de votre travail.»

Une unité de travail standard lors de l’utilisation de Entity Framework Core (EF Core) implique :

- Création d’une `DbContext` instance
- Suivi des instances d’entité par le contexte. Les entités sont suivies par
  - [Retourné à partir d’une requête](xref:core/querying/tracking)
  - [Ajouté ou attaché au contexte](xref:core/saving/disconnected-entities)
- Les entités suivies sont modifiées en fonction des besoins pour implémenter la règle d’entreprise.
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ou <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> est appelé. EF Core détecte les modifications apportées et les écrit dans la base de données.
- L' `DbContext` instance est supprimée

> [!IMPORTANT]
>
> - Il est très important de supprimer le <xref:Microsoft.EntityFrameworkCore.DbContext> après l’utilisation. Cela garantit que toutes les ressources non managées sont libérées et que tous les événements ou autres hooks sont désinscrits afin d’éviter les fuites de mémoire au cas où l’instance resterait référencée.
> - [DbContext n’est **pas thread-safe**](#avoiding-dbcontext-threading-issues). Ne partagez pas de contextes entre les threads. Veillez à [attendre](/dotnet/csharp/language-reference/operators/await) tous les appels asynchrones avant de continuer à utiliser l’instance de contexte.
> - Une <xref:System.InvalidOperationException> exception levée par EF Core code peut mettre le contexte dans un État irrécupérable. Ces exceptions indiquent une erreur de programme et ne sont pas conçues pour être récupérées à partir de.

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a>DbContext dans l’injection de dépendances pour ASP.NET Core

Dans de nombreuses applications Web, chaque requête HTTP correspond à une unité de travail unique. Cela permet de lier la durée de vie du contexte à celle de la demande d’une bonne valeur par défaut pour les applications Web.

ASP.NET Core applications sont [configurées à l’aide de l’injection de dépendances](/aspnet/core/fundamentals/startup). EF Core peuvent être ajoutés à cette configuration à l’aide de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> dans la [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) méthode de `Startup.cs` . Par exemple :

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();

            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

Cet exemple inscrit une sous- `DbContext` classe appelée `ApplicationDbContext` en tant que service étendu dans le fournisseur de services d’application ASP.net Core (également appelé conteneur d’injection de dépendances). Le contexte est configuré pour utiliser le fournisseur de base de données SQL Server et lira la chaîne de connexion à partir de la configuration de ASP.NET Core. Cela n’a généralement pas _d’importance dans_ `ConfigureServices` l’appel à `AddDbContext` .

La `ApplicationDbContext` classe doit exposer un constructeur public avec un `DbContextOptions<ApplicationDbContext>` paramètre. C’est ainsi que la configuration contextuelle de `AddDbContext` est transmise au `DbContext` . Par exemple :

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

`ApplicationDbContext` peut ensuite être utilisé dans les contrôleurs de ASP.NET Core ou d’autres services via l’injection de constructeur. Par exemple :

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

Le résultat final est une `ApplicationDbContext` instance créée pour chaque demande et transmise au contrôleur pour exécuter une unité de travail avant d’être supprimée à la fin de la demande.

Pour en savoir plus sur les options de configuration, consultez cet article. En outre, consultez [démarrage de l’application dans ASP.net Core](/aspnet/core/fundamentals/startup) et [injection de dépendances dans ASP.net Core](/aspnet/core/fundamentals/dependency-injection) pour plus d’informations sur la configuration et l’injection de dépendances dans ASP.net core.

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a>Initialisation de DbContext simple avec’New'

`DbContext` les instances peuvent être construites dans le sens .NET normal, par exemple avec `new` en C#. La configuration peut être effectuée en substituant la `OnConfiguring` méthode ou en passant des options au constructeur. Par exemple :

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

Ce modèle facilite également la réussite de la configuration comme la chaîne de connexion par le biais du `DbContext` constructeur. Par exemple :

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

`DbContextOptionsBuilder`Vous pouvez également utiliser pour créer un `DbContextOptions` objet qui est ensuite passé au `DbContext` constructeur. Cela permet `DbContext` à un configuré pour l’injection de dépendances d’être également construit explicitement. Par exemple, lors de l’utilisation `ApplicationDbContext` de défini pour ASP.net Core Web Apps ci-dessus :

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

Le `DbContextOptions` peut être créé et le constructeur peut être appelé explicitement :

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a>Utilisation d’une fabrique DbContext (par exemple, pour éblouissant)

Certains types d’applications (par exemple, [ASP.net Core éblouissant](/aspnet/core/blazor/)) utilisent l’injection de dépendances, mais ne créent pas une étendue de service qui s’aligne sur la `DbContext` durée de vie souhaitée. Même dans ce cas, l’application devra peut-être effectuer plusieurs unités de travail au sein de cette étendue. Par exemple, plusieurs unités de travail au sein d’une même requête HTTP.

Dans ces cas-là, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> peut être utilisé pour inscrire une fabrique pour la création d' `DbContext` instances de. Par exemple :

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

La `ApplicationDbContext` classe doit exposer un constructeur public avec un `DbContextOptions<ApplicationDbContext>` paramètre. Il s’agit du même modèle que celui utilisé dans la section ASP.NET Core traditionnelle ci-dessus.

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

La `DbContextFactory` fabrique peut ensuite être utilisée dans d’autres services via l’injection de constructeur. Par exemple :

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

La fabrique injectée peut ensuite être utilisée pour construire des instances DbContext dans le code de service. Par exemple :

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

Notez que les `DbContext` instances créées de cette façon **ne sont pas** gérées par le fournisseur de services de l’application et doivent donc être supprimées par l’application.

Pour plus d’informations sur l’utilisation de EF Core avec éblouissant, consultez [ASP.net Core serveur éblouissant avec Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) .

## <a name="dbcontextoptions"></a>DbContextOptions

Le point de départ de toutes les `DbContext` configurations est <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> . Il existe trois façons d’utiliser ce générateur :

- Dans `AddDbContext` et méthodes associées
- Dans `OnConfiguring`
- Construit explicitement avec `new`

Des exemples de chacun d’entre eux sont présentés dans les sections précédentes. La même configuration peut être appliquée, quel que soit l’emplacement d’où le générateur provient. En outre, `OnConfiguring` est toujours appelé, quelle que soit la façon dont le contexte est construit. Cela `OnConfiguring` peut être utilisé pour effectuer une configuration supplémentaire même lorsque `AddDbContext` est utilisé.

### <a name="configuring-the-database-provider"></a>Configuration du fournisseur de base de données

Chaque `DbContext` instance doit être configurée pour utiliser un et un seul fournisseur de base de données. (Les différentes instances d’un `DbContext` sous-type peuvent être utilisées avec différents fournisseurs de bases de données, mais une seule instance ne doit en utiliser qu’une seule.) Un fournisseur de base de données est configuré à l’aide d’un `Use*` appel spécifique. Par exemple, pour utiliser le fournisseur de base de données SQL Server :

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

Ces `Use*` méthodes sont des méthodes d’extension implémentées par le fournisseur de base de données. Cela signifie que le package NuGet du fournisseur de base de données doit être installé pour permettre l’utilisation de la méthode d’extension.

> [!TIP]
> EF Core fournisseurs de bases de données utilisent de manière intensive les [méthodes d’extension](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods). Si le compilateur indique qu’une méthode est introuvable, vérifiez que le package NuGet du fournisseur est installé et que vous avez `using Microsoft.EntityFrameworkCore;` dans votre code.

Le tableau suivant contient des exemples pour les fournisseurs de bases de données courants.

| Système de base de données              | Exemple de configuration                         | Package NuGet
|:-----------------------------|-----------------------------------------------|--------------
| SQL Server ou SQL Azure      | `.UseSqlServer(connectionString)`             | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| Azure Cosmos DB              | `.UseCosmos(connectionString, databaseName)`  | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| SQLite                       | `.UseSqlite(connectionString)`                | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| Fournisseur en mémoire EF Core   | `.UseInMemoryDatabase(databaseName)`          | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| PostgreSQL                  | `.UseNpgsql(connectionString)`                | [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| MySQL/MariaDB *               | `.UseMySql((connectionString)`                | [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| Oracle*                      | `.UseOracle(connectionString)`                | [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

* Ces fournisseurs de bases de données ne sont pas fournis par Microsoft. Pour plus d’informations sur les fournisseurs de base de données, consultez [fournisseurs de](xref:core/providers/index) bases de données.

> [!WARNING]
> La base de données en mémoire EF Core n’est pas conçue pour une utilisation en production. En outre, il n’est peut-être pas le meilleur choix pour les tests. Pour plus d’informations, consultez [test du code qui utilise EF Core](xref:core/testing/index) .

Consultez [chaînes de connexion](xref:core/miscellaneous/connection-strings) pour plus d’informations sur l’utilisation de chaînes de connexion avec EF Core.

Une configuration facultative spécifique au fournisseur de base de données est effectuée dans un générateur supplémentaire spécifique au fournisseur. Par exemple, l’utilisation <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> de pour configurer les nouvelles tentatives de résilience des connexions lors de la connexion à Azure SQL :

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
> Le même fournisseur de base de données est utilisé pour SQL Server et Azure SQL. Toutefois, il est recommandé d’utiliser la [résilience des connexions](xref:core/miscellaneous/connection-resiliency) lors de la connexion à SQL Azure.

Pour plus d’informations sur la configuration spécifique au fournisseur, consultez [fournisseurs de bases de données](xref:core/providers/index) .

### <a name="other-dbcontext-configuration"></a>Autre configuration de DbContext

`DbContext`Une autre configuration peut être chaînée avant ou après (il n’y a aucune différence) avec l' `Use*` appel. Par exemple, pour activer la journalisation des données sensibles :

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

Le tableau suivant contient des exemples de méthodes courantes appelées sur `DbContextOptionsBuilder` .

| Méthode DbContextOptionsBuilder                                                             | Résultat                                                | En savoir plus
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | Définit le comportement de suivi par défaut pour les requêtes              | [Comportement du suivi des requêtes](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | Un moyen simple d’obtenir des journaux de EF Core (EF Core 5,0 et versions ultérieures)    | [Journalisation, événements et diagnostics](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | Inscrit une `Microsoft.Extensions.Logging` fabrique         | [Journalisation, événements et diagnostics](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | Comprend les données d’application dans les exceptions et la journalisation         | [Journalisation, événements et diagnostics](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | Erreurs de requête plus détaillées (au détriment des performances)  | [Journalisation, événements et diagnostics](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | Ignorer ou lever pour les avertissements et autres événements               | [Journalisation, événements et diagnostics](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | Inscrit EF Core intercepteurs                              | [Journalisation, événements et diagnostics](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | Utiliser des proxies dynamiques pour le chargement différé                        | [Chargement différé](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | Utiliser des proxies dynamiques pour le suivi des modifications                     | Bientôt disponible...

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> et <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> sont des méthodes d’extension du package NuGet [Microsoft. EntityFrameworkCore. proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) . Ce type de «. L’appel de UseSomething () est la méthode recommandée pour configurer et/ou utiliser des extensions de EF Core contenues dans d’autres packages.

### <a name="dbcontextoptions-versus-dbcontextoptionstcontext"></a>`DbContextOptions` alternative `DbContextOptions<TContext>`

La plupart des `DbContext` sous-classes qui acceptent un `DbContextOptions` doivent utiliser la variation [générique](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` . Par exemple :

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

Cela permet de s’assurer que les options correctes pour le `DbContext` sous-type spécifique sont résolues à partir de l’injection de dépendances, même lorsque plusieurs `DbContext` sous-types sont inscrits.

> [!TIP]
> Votre DbContext n’a pas besoin d’être sealed, mais la fermeture est recommandée pour les classes qui ne sont pas destinées à être héritées de.

Toutefois, si le `DbContext` sous-type est lui-même destiné à être hérité de, il doit exposer un constructeur protégé qui prend un non générique `DbContextOptions` . Par exemple :

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

Cela permet à plusieurs sous-classes concrètes d’appeler ce constructeur de base à l’aide de leurs différentes `DbContextOptions<TContext>` instances génériques. Par exemple :

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

Notez qu’il s’agit exactement du même modèle que lorsqu’il hérite `DbContext` directement de. Autrement dit, le `DbContext` constructeur lui-même accepte un non générique `DbContextOptions` pour cette raison.

Une `DbContext` sous-classe destinée à être instanciée et héritée de doit exposer les deux formes de constructeur. Par exemple :

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

## <a name="design-time-dbcontext-configuration"></a>Configuration de DbContext au moment du design

EF Core des outils au moment du design, tels que ceux des [migrations EF Core](xref:core/managing-schemas/migrations/index) , doivent être en mesure de découvrir et de créer une instance de travail d’un type afin de `DbContext` rassembler des détails sur les types d’entité de l’application et la façon dont ils sont mappés à un schéma de base de données. Ce processus peut être automatique tant que l’outil peut facilement créer le de façon à ce qu' `DbContext` il soit configuré de façon similaire à la façon dont il est configuré au moment de l’exécution.

Alors que tout modèle qui fournit les informations de configuration nécessaires au `DbContext` peut fonctionner au moment de l’exécution, les outils qui nécessitent l’utilisation d’un `DbContext` au moment du design peuvent uniquement fonctionner avec un nombre limité de modèles. Elles sont traitées plus en détail dans la section [création de contexte au moment du design](xref:core/cli/dbcontext-creation).

## <a name="avoiding-dbcontext-threading-issues"></a>Éviter les problèmes de thread DbContext

Entity Framework Core ne prend pas en charge l’exécution de plusieurs opérations parallèles sur la même `DbContext` instance. Cela comprend à la fois l’exécution parallèle des requêtes Async et toute utilisation simultanée explicite à partir de plusieurs threads. Par conséquent, toujours `await` Async appelle immédiatement, ou utilisez `DbContext` des instances distinctes pour les opérations qui s’exécutent en parallèle.

Lorsque EF Core détecte une tentative d’utilisation d’une `DbContext` instance simultanément, vous voyez un `InvalidOperationException` avec un message semblable à celui-ci :

> Une deuxième opération a démarré sur ce contexte avant la fin d’une opération précédente. Cela est généralement dû à différents threads utilisant la même instance de DbContext, mais il n’est pas garanti que les membres d’instance soient thread-safe.

Lorsque l’accès simultané n’est pas détecté, cela peut entraîner un comportement non défini, des blocages d’application et des données endommagées.

Il existe des erreurs courantes qui peuvent entraîner par inadvertance un accès simultané sur la même `DbContext` instance :

### <a name="asynchronous-operation-pitfalls"></a>Pièges de l’opération asynchrone

Les méthodes asynchrones permettent à EF Core de lancer des opérations qui accèdent à la base de données de façon non bloquante. Toutefois, si un appelant n’attend pas la fin de l’une de ces méthodes et poursuit l’exécution d’autres opérations sur le `DbContext` , l’état du `DbContext` peut être, (et très probablement) endommagé.

Attendez toujours EF Core les méthodes asynchrones immédiatement.

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a>Partage implicite d’instances de DbContext via une injection de dépendance

La [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) méthode d’extension inscrit les `DbContext` types avec une [durée de vie limitée](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) par défaut.

Cela ne pose pas de problèmes d’accès simultané dans la plupart des applications ASP.NET Core, car il n’existe qu’un seul thread qui exécute chaque demande client à un moment donné, et parce que chaque demande obtient une étendue d’injection de dépendances distincte (et par conséquent une `DbContext` instance distincte). Pour le modèle d’hébergement de serveur éblouissant, une demande logique est utilisée pour gérer le circuit utilisateur éblouissant, et par conséquent, une seule instance DbContext étendue est disponible par circuit utilisateur si l’étendue d’injection par défaut est utilisée.

Tout code qui exécute explicitement plusieurs threads en parallèle doit s’assurer que les `DbContext` instances ne sont jamais accessibles simultanément.

À l’aide de l’injection de dépendances, cela peut être obtenu en inscrivant le contexte comme étant étendu, en créant des portées (à l’aide de `IServiceScopeFactory` ) pour chaque thread ou en inscrivant le `DbContext` comme transitoire (à l’aide de la surcharge de `AddDbContext` qui accepte un `ServiceLifetime` paramètre).

## <a name="more-reading"></a>Plus de lectures

- Lisez [injection de dépendances](/aspnet/core/fundamentals/dependency-injection) pour en savoir plus sur l’utilisation de di.
- Pour plus d’informations, consultez le [test](xref:core/testing/index) .
