---
title: Utilisation de Microsoft. extensions. Logging-EF Core
description: Journalisation à partir de EF Core à l’aide de Microsoft. extensions. Logging dans ASP.NET Core et d’autres types d’applications
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/extensions-logging
ms.openlocfilehash: 3732bda0ef29b05672a2dfb83405da802f899191
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431534"
---
# <a name="using-microsoftextensionslogging-in-ef-core"></a><span data-ttu-id="c7aea-103">Utilisation de Microsoft. extensions. Logging dans EF Core</span><span class="sxs-lookup"><span data-stu-id="c7aea-103">Using Microsoft.Extensions.Logging in EF Core</span></span>

<span data-ttu-id="c7aea-104">[Microsoft. extensions. Logging](/dotnet/core/extensions/logging) est un mécanisme de journalisation extensible avec des fournisseurs de plug-ins pour de nombreux systèmes de journalisation courants.</span><span class="sxs-lookup"><span data-stu-id="c7aea-104">[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) is an extensible logging mechanism with plug-in providers for many common logging systems.</span></span> <span data-ttu-id="c7aea-105">Les plug-ins fournis par Microsoft (par exemple, [Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) et les plug-ins tiers (par exemple, [Serilog. extensions. Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) sont disponibles sous forme de packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="c7aea-105">Both Microsoft-supplied plug-ins (e.g [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) and third-party plug-ins (e.g. [Serilog.Extensions.Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) are available as NuGet packages.</span></span>

<span data-ttu-id="c7aea-106">Entity Framework Core (EF Core) s’intègre entièrement à `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="c7aea-106">Entity Framework Core (EF Core) fully integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="c7aea-107">Toutefois, envisagez d’utiliser la [journalisation simple](xref:core/logging-events-diagnostics/simple-logging) pour un enregistrement plus simple, en particulier pour les applications qui n’utilisent pas l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="c7aea-107">However, consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) for a simpler way to log, especially for applications that don't use dependency injection.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="c7aea-108">applications ASP.NET Core ;</span><span class="sxs-lookup"><span data-stu-id="c7aea-108">ASP.NET Core applications</span></span>

<span data-ttu-id="c7aea-109">`Microsoft.Extensions.Logging` est [utilisé par défaut dans les applications ASP.net Core](/aspnet/core/fundamentals/logging).</span><span class="sxs-lookup"><span data-stu-id="c7aea-109">`Microsoft.Extensions.Logging` is [used by default in ASP.NET Core applications](/aspnet/core/fundamentals/logging).</span></span> <span data-ttu-id="c7aea-110">Appel <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> de ou <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> .</span><span class="sxs-lookup"><span data-stu-id="c7aea-110">Calling <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A>.</span></span>

## <a name="other-application-types"></a><span data-ttu-id="c7aea-111">Autres types d’applications</span><span class="sxs-lookup"><span data-stu-id="c7aea-111">Other application types</span></span>

<span data-ttu-id="c7aea-112">D’autres types d’applications peuvent utiliser [GenericHost](/dotnet/core/extensions/generic-host) pour récupérer les mêmes modèles d’injection de dépendances que ceux utilisés dans ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="c7aea-112">Other application types can use the [GenericHost](/dotnet/core/extensions/generic-host) to get the same dependency injection patterns as are used in ASP.NET Core.</span></span> <span data-ttu-id="c7aea-113">AddDbContext ou AddDbContextPool peut ensuite être utilisé comme dans les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c7aea-113">AddDbContext or AddDbContextPool can then be used just like in ASP.NET Core applications.</span></span>

<span data-ttu-id="c7aea-114">`Microsoft.Extensions.Logging` peut également être utilisé pour les applications qui n’utilisent pas l’injection de dépendances, bien que la [journalisation simple](xref:core/logging-events-diagnostics/simple-logging) puisse être plus facile à configurer.</span><span class="sxs-lookup"><span data-stu-id="c7aea-114">`Microsoft.Extensions.Logging` can also be used for applications that don't use dependency injection, although [simple logging](xref:core/logging-events-diagnostics/simple-logging) can be easier to set up.</span></span>

<span data-ttu-id="c7aea-115">`Microsoft.Extensions.Logging` requiert la création d’un <xref:Microsoft.Extensions.Logging.LoggerFactory> .</span><span class="sxs-lookup"><span data-stu-id="c7aea-115">`Microsoft.Extensions.Logging` requires creation of a <xref:Microsoft.Extensions.Logging.LoggerFactory>.</span></span> <span data-ttu-id="c7aea-116">Cette fabrique doit être stockée en tant qu’instance statique/globale quelque part et utilisée chaque fois qu’un DbContext est créé.</span><span class="sxs-lookup"><span data-stu-id="c7aea-116">This factory should be stored as a static/global instance somewhere and used each time a DbContext is created.</span></span> <span data-ttu-id="c7aea-117">Par exemple, il est courant de stocker la fabrique d’enregistreur d’événements en tant que propriété statique sur le DbContext.</span><span class="sxs-lookup"><span data-stu-id="c7aea-117">For example, it is common to store the logger factory as a static property on the DbContext.</span></span>

### <a name="ef-core-30-and-above"></a>[<span data-ttu-id="c7aea-118">EF Core 3,0 et versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="c7aea-118">EF Core 3.0 and above</span></span>](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[<span data-ttu-id="c7aea-119">EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="c7aea-119">EF Core 2.1</span></span>](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> <span data-ttu-id="c7aea-120">Dans EF Core 2,1, il est très important que les applications ne créent pas une nouvelle instance LoggerFactory pour chaque instance DbContext.</span><span class="sxs-lookup"><span data-stu-id="c7aea-120">In EF Core 2.1, It is very important that applications do not create a new LoggerFactory instance for each DbContext instance.</span></span> <span data-ttu-id="c7aea-121">Cela entraînera une fuite de mémoire et des performances médiocres.</span><span class="sxs-lookup"><span data-stu-id="c7aea-121">Doing so will result in a memory leak and poor performance.</span></span> <span data-ttu-id="c7aea-122">Ce problème a été résolu dans EF Core 3,0 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="c7aea-122">This has been fixed in EF Core 3.0 and above.</span></span>

***

<span data-ttu-id="c7aea-123">Cette instance de Singleton/global doit ensuite être inscrite auprès de EF Core sur le <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c7aea-123">This singleton/global instance should then be registered with EF Core on the <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="c7aea-124">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c7aea-124">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a><span data-ttu-id="c7aea-125">Obtention de messages détaillés</span><span class="sxs-lookup"><span data-stu-id="c7aea-125">Getting detailed messages</span></span>

> [!TIP]
> <span data-ttu-id="c7aea-126">OnConfiguring est toujours appelé lorsque AddDbContext est utilisé ou qu’une instance DbContextOptions est passée au constructeur DbContext.</span><span class="sxs-lookup"><span data-stu-id="c7aea-126">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="c7aea-127">C’est ainsi l’emplacement idéal pour appliquer la configuration du contexte, quelle que soit la façon dont la méthode DbContext est construite.</span><span class="sxs-lookup"><span data-stu-id="c7aea-127">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="c7aea-128">Données sensibles</span><span class="sxs-lookup"><span data-stu-id="c7aea-128">Sensitive data</span></span>

<span data-ttu-id="c7aea-129">Par défaut, EF Core n’inclut pas les valeurs des données dans les messages d’exception.</span><span class="sxs-lookup"><span data-stu-id="c7aea-129">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="c7aea-130">Cela est dû au fait que ces données peuvent être confidentielles et peuvent être révélées en production si une exception n’est pas gérée.</span><span class="sxs-lookup"><span data-stu-id="c7aea-130">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="c7aea-131">Toutefois, le fait de connaître les valeurs de données, en particulier pour les clés, peut être très utile lors du débogage.</span><span class="sxs-lookup"><span data-stu-id="c7aea-131">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="c7aea-132">Vous pouvez l’activer dans EF Core en appelant <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="c7aea-132">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="c7aea-133">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c7aea-133">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="c7aea-134">Exceptions de requête détaillées</span><span class="sxs-lookup"><span data-stu-id="c7aea-134">Detailed query exceptions</span></span>

<span data-ttu-id="c7aea-135">Pour des raisons de performances, EF Core n’encapsule pas chaque appel pour lire une valeur à partir du fournisseur de base de données dans un bloc try-catch.</span><span class="sxs-lookup"><span data-stu-id="c7aea-135">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="c7aea-136">Toutefois, cela entraîne parfois des exceptions difficiles à diagnostiquer, en particulier lorsque la base de données retourne une valeur NULL lorsqu’elle n’est pas autorisée par le modèle.</span><span class="sxs-lookup"><span data-stu-id="c7aea-136">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="c7aea-137">Si vous activez la fonction, <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduira ces blocs try-catch et fournira des erreurs plus détaillées.</span><span class="sxs-lookup"><span data-stu-id="c7aea-137">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="c7aea-138">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c7aea-138">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="c7aea-139">Configuration de messages spécifiques</span><span class="sxs-lookup"><span data-stu-id="c7aea-139">Configuration for specific messages</span></span>

<span data-ttu-id="c7aea-140">L' <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API EF Core permet aux applications de modifier ce qui se produit lorsqu’un événement spécifique est rencontré.</span><span class="sxs-lookup"><span data-stu-id="c7aea-140">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="c7aea-141">Cela peut être utilisé pour :</span><span class="sxs-lookup"><span data-stu-id="c7aea-141">This can be used to:</span></span>

* <span data-ttu-id="c7aea-142">Modifier le niveau de journal auquel l’événement est enregistré</span><span class="sxs-lookup"><span data-stu-id="c7aea-142">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="c7aea-143">Ignorer l’enregistrement de l’événement</span><span class="sxs-lookup"><span data-stu-id="c7aea-143">Skip logging the event altogether</span></span>
* <span data-ttu-id="c7aea-144">Lever une exception lorsque l’événement se produit</span><span class="sxs-lookup"><span data-stu-id="c7aea-144">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="c7aea-145">Modification du niveau de journalisation d’un événement</span><span class="sxs-lookup"><span data-stu-id="c7aea-145">Changing the log level for an event</span></span>

<span data-ttu-id="c7aea-146">Parfois, il peut être utile de modifier le niveau de journal prédéfini pour un événement.</span><span class="sxs-lookup"><span data-stu-id="c7aea-146">Sometimes it can be useful to change the pre-defined log level for an event.</span></span> <span data-ttu-id="c7aea-147">Par exemple, il peut être utilisé pour promouvoir deux événements supplémentaires de `LogLevel.Debug` à `LogLevel.Information` :</span><span class="sxs-lookup"><span data-stu-id="c7aea-147">For example, this can be used to promote two additional events from `LogLevel.Debug` to `LogLevel.Information`:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="c7aea-148">Supprimer la journalisation d’un événement</span><span class="sxs-lookup"><span data-stu-id="c7aea-148">Suppress logging an event</span></span>

<span data-ttu-id="c7aea-149">De la même façon, un événement individuel peut être supprimé de la journalisation.</span><span class="sxs-lookup"><span data-stu-id="c7aea-149">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="c7aea-150">Cela est particulièrement utile pour ignorer un avertissement qui a été révisé et compris.</span><span class="sxs-lookup"><span data-stu-id="c7aea-150">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="c7aea-151">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c7aea-151">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="c7aea-152">Lever pour un événement</span><span class="sxs-lookup"><span data-stu-id="c7aea-152">Throw for an event</span></span>

<span data-ttu-id="c7aea-153">Enfin, EF Core peut être configuré pour lever une exception pour un événement donné.</span><span class="sxs-lookup"><span data-stu-id="c7aea-153">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="c7aea-154">Cela s’avère particulièrement utile pour modifier un avertissement en erreur.</span><span class="sxs-lookup"><span data-stu-id="c7aea-154">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="c7aea-155">(En effet, c’était l’objectif initial de la `ConfigureWarnings` méthode, donc le nom.) Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c7aea-155">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a><span data-ttu-id="c7aea-156">Filtrage et autre configuration</span><span class="sxs-lookup"><span data-stu-id="c7aea-156">Filtering and other configuration</span></span>

<span data-ttu-id="c7aea-157">Consultez [journalisation dans .net](/dotnet/core/extensions/logging) pour obtenir des conseils sur le filtrage des journaux et d’autres configurations.</span><span class="sxs-lookup"><span data-stu-id="c7aea-157">See [Logging in .NET](/dotnet/core/extensions/logging) for guidance on log filtering and other configuration.</span></span>

<span data-ttu-id="c7aea-158">EF Core événements de journalisation sont définis dans l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="c7aea-158">EF Core logging events are defined in one of:</span></span>

* <span data-ttu-id="c7aea-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> pour les événements communs à tous les fournisseurs de bases de données EF Core</span><span class="sxs-lookup"><span data-stu-id="c7aea-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="c7aea-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> pour les événements communs à tous les fournisseurs de bases de données relationnelles</span><span class="sxs-lookup"><span data-stu-id="c7aea-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="c7aea-161">Classe similaire pour les événements spécifiques au fournisseur de base de données actuel.</span><span class="sxs-lookup"><span data-stu-id="c7aea-161">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="c7aea-162">Par exemple, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> pour le fournisseur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c7aea-162">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="c7aea-163">Ces définitions contiennent les ID d’événement, le niveau de journal et la catégorie de chaque événement, tels qu’ils sont utilisés par `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="c7aea-163">These definitions contain the event IDs, log level, and category for each event, as used by `Microsoft.Extensions.Logging`.</span></span>
