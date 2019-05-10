---
title: Configuration d’un DbContext - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 316d363d4a1b8a909efc1c32b492280c0d16cb4e
ms.sourcegitcommit: 960e42a01b3a2f76da82e074f64f52252a8afecc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405207"
---
# <a name="configuring-a-dbcontext"></a>Configuration d’un DbContext

Cet article présente les modèles de base pour la configuration un `DbContext` via un `DbContextOptions` pour se connecter à une base de données à l’aide d’un fournisseur EF Core spécifique et les comportements facultatifs.

## <a name="design-time-dbcontext-configuration"></a>Configuration de DbContext au moment du design

EF Core au moment du design des outils tels que [migrations](xref:core/managing-schemas/migrations/index) doivent être en mesure de détecter et de créer une instance de l’utilisation d’un `DbContext` type afin de collecter des informations détaillées sur les types d’entité de l’application et comment ils sont mappés à un schéma de base de données. Ce processus peut être automatique, que l’outil peut facilement créer le `DbContext` de sorte qu’il sera configuré de la même façon à la façon dont elle est configurée au moment de l’exécution.

Bien que n’importe quel modèle qui fournit les informations de configuration nécessaires à la `DbContext` peut fonctionner au moment de l’exécution, les outils qui requièrent l’utilisation un `DbContext` au moment du design ne fonctionnent qu’avec un nombre limité de modèles. Elles sont traitées plus en détail dans le [la création du contexte au moment du Design](xref:core/miscellaneous/cli/dbcontext-creation) section.

## <a name="configuring-dbcontextoptions"></a>Configuration de DbContextOptions

`DbContext` doit avoir une instance de `DbContextOptions` afin d’effectuer des tâches. Le `DbContextOptions` instance comporte des informations de configuration telles que :

- Le fournisseur de base de données à utiliser, généralement sélectionné en appelant une méthode comme `UseSqlServer` ou `UseSqlite`. Ces méthodes d’extension requièrent le package de fournisseur correspondant, tel que `Microsoft.EntityFrameworkCore.SqlServer` ou `Microsoft.EntityFrameworkCore.Sqlite`. Les méthodes sont définies dans le `Microsoft.EntityFrameworkCore` espace de noms.
- Toute chaîne de connexion nécessaires ou d’un identificateur de l’instance de la base de données, généralement passé en tant qu’argument à la méthode de sélection de fournisseur mentionnée ci-dessus
- Tous les sélecteurs comportement facultatif au niveau du fournisseur, généralement également chaînés à l’intérieur de l’appel à la méthode de sélection du fournisseur
- N’importe quel sélecteurs de comportement générales EF Core, généralement chaînées après ou avant la méthode de sélection du fournisseur

L’exemple suivant configure le `DbContextOptions` pour utiliser le fournisseur SQL Server, une connexion contenue dans le `connectionString` variable, un délai d’expiration au niveau du fournisseur de commande et un sélecteur de comportement d’EF Core qui effectue toutes les requêtes exécutées dans le `DbContext` [NoTracking](xref:core/querying/tracking#no-tracking-queries) par défaut :

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> Méthodes de sélecteur de fournisseur et d’autres méthodes de sélecteur de comportement mentionnés ci-dessus sont des méthodes d’extension sur `DbContextOptions` ou classes d’option spécifique au fournisseur. Pour accéder à ces méthodes d’extension que vous devrez peut-être disposer d’un espace de noms (généralement `Microsoft.EntityFrameworkCore`) dans l’étendue et inclure les dépendances de package supplémentaires dans le projet.

Le `DbContextOptions` peuvent être fournis à la `DbContext` en substituant le `OnConfiguring` méthode ou en externe via un argument de constructeur.

Si les deux sont utilisés, `OnConfiguring` est appliqué en dernier et peuvent remplacer des options fournies à l’argument de constructeur.

### <a name="constructor-argument"></a>Argument du constructeur

Code de contexte avec le constructeur :

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
> Le constructeur de base de DbContext accepte également la version non générique de `DbContextOptions`, mais à l’aide de la version non générique n’est pas recommandé pour les applications avec plusieurs types de contexte.

Code d’application d’initialiser à partir de l’argument du constructeur :

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

Code de contexte avec `OnConfiguring`:

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

Code d’application pour initialiser un `DbContext` qui utilise `OnConfiguring`:

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> Cette approche ne se prête pas au test, à moins que les tests ciblent la base de données complète.

### <a name="using-dbcontext-with-dependency-injection"></a>À l’aide de DbContext avec l’injection de dépendance

EF Core prend en charge à l’aide de `DbContext` avec un conteneur d’injection de dépendance. Votre type DbContext peut être ajouté au conteneur de service en utilisant le `AddDbContext<TContext>` (méthode).

`AddDbContext<TContext>` rendra à la fois votre type DbContext, `TContext`et le correspondantes `DbContextOptions<TContext>` disponibles pour l’injection de code à partir du conteneur de service.

Consultez [lire plus](#more-reading) ci-dessous pour plus d’informations sur l’injection de dépendances.

Ajout de la `Dbcontext` pour l’injection de dépendances :

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

Cela nécessite l’ajout d’un [argument du constructeur](#constructor-argument) à votre type DbContext qui accepte `DbContextOptions<TContext>`.

Code de contexte :

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

Code d’application (dans ASP.NET Core) :

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

Code d’application (à l’aide de ServiceProvider directement, moins fréquent) :

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```
## <a name="avoiding-dbcontext-threading-issues"></a>Éviter les problèmes liés aux threads de DbContext

Entity Framework Core ne prend pas en charge plusieurs opérations parallèles en cours d’exécution sur le même `DbContext` instance. Cela inclut l’exécution parallèle de requêtes d’async et toute utilisation explicite simultanée à partir de plusieurs threads. Par conséquent, toujours `await` async appelle immédiatement, ou utiliser distinct `DbContext` instances pour les opérations qui s’exécutent en parallèle.

Quand EF Core détecte une tentative d’utiliser un `DbContext` instance simultanément, vous verrez un `InvalidOperationException` avec un message comme celui-ci : 

> Une deuxième opération démarré sur ce contexte avant une opération précédente s’est terminée. Cela est généralement dû à différents threads à l’aide de la même instance de DbContext, toutefois les membres d’instance ne sont pas garantis pour être thread-safe.

Lors de l’accès simultané n’est pas détecté, il peut entraîner un comportement non défini, les blocages d’application et une altération des données.

Il existe des erreurs courantes qui peuvent inadvernetly cause des accès simultané sur le même `DbContext` instance :

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>Oubli à attendre la fin d’une opération asynchrone avant de commencer toute autre opération sur le même DbContext

Les méthodes asynchrones permettent EF Core lancer des opérations qui accèdent à la base de données de façon non bloquante. Mais si un appelant n’attend pas la fin d’une des méthodes suivantes, puis passe à effectuer d’autres opérations sur le `DbContext`, l’état de la `DbContext` peut être (et sera très probablement) endommagé. 

Toujours attendre les méthodes asynchrones EF Core immédiatement.  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>Partage implicitement des instances de DbContext entre plusieurs threads par le biais de l’injection de dépendances

Le [ `AddDbContext` ](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) enregistre la méthode d’extension `DbContext` types avec un [durée de vie délimitée](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) par défaut. 

Cela est protégée contre les problèmes d’accès simultané dans les applications ASP.NET Core, car il y n'a qu’un seul thread à un moment donné, l’exécution de chaque demande du client, et étant donné que chaque requête obtient une étendue de l’injection de dépendance distinct (et par conséquent une `DbContext` instance).

Toutefois, tout code qui exécute explicitement plusieurs threads en parallèle devez vous assurer de que `DbContext` instances ne sont pas jamais accesed simultanément.

À l’aide de l’injection de dépendances, cela est possible en inscrire soit le contexte comme étendues et de création des étendues (à l’aide de `IServiceScopeFactory`) pour chaque thread ou en inscrivant le `DbContext` comme provisoire (à l’aide de la surcharge de `AddDbContext` qui accepte un `ServiceLifetime` paramètre).

## <a name="more-reading"></a>Lire plus

* Lecture [bien démarrer avec ASP.NET Core](../get-started/aspnetcore/index.md) pour plus d’informations sur l’utilisation d’EF avec ASP.NET Core.
* Lecture [l’Injection de dépendances](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) pour en savoir plus sur l’utilisation de l’injection de dépendances.
* Lecture [test](testing/index.md) pour plus d’informations.
