---
title: Journalisation-EF Core
description: Configuration de la journalisation avec Entity Framework Core
author: ajcvickers
ms.date: 10/06/2020
uid: core/miscellaneous/logging
ms.openlocfilehash: 389834b3822aeeaefb8c085538bc6359ccfa7094
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063008"
---
# <a name="logging"></a><span data-ttu-id="526ee-103">Journalisation</span><span class="sxs-lookup"><span data-stu-id="526ee-103">Logging</span></span>

> [!TIP]
> <span data-ttu-id="526ee-104">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="526ee-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="526ee-105">Journalisation simple</span><span class="sxs-lookup"><span data-stu-id="526ee-105">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="526ee-106">Cette fonctionnalité a été ajoutée dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="526ee-106">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="526ee-107">Entity Framework Core (EF Core) génère des messages de journal pour des opérations telles que l’exécution d’une requête ou l’enregistrement des modifications apportées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="526ee-107">Entity Framework Core (EF Core) generates log messages for operations such as executing a query or saving changes to the database.</span></span> <span data-ttu-id="526ee-108">Vous pouvez y accéder à partir de n’importe quel type d’application via l’utilisation de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="526ee-108">These can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="526ee-109">lors [de la configuration d’une instance DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="526ee-109">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="526ee-110">Cette configuration s’effectue généralement dans une substitution de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="526ee-110">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="526ee-111">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="526ee-111">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="526ee-112">Ce concept est semblable à <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> dans EF6.</span><span class="sxs-lookup"><span data-stu-id="526ee-112">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="526ee-113">Pour plus d’informations, consultez [journalisation simple](xref:core/miscellaneous/events/simple-logging) .</span><span class="sxs-lookup"><span data-stu-id="526ee-113">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="526ee-114">applications ASP.NET Core ;</span><span class="sxs-lookup"><span data-stu-id="526ee-114">ASP.NET Core applications</span></span>

<span data-ttu-id="526ee-115">EF Core s’intègre automatiquement avec les mécanismes de journalisation de ASP.NET Core chaque fois que `AddDbContext` ou `AddDbContextPool` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="526ee-115">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="526ee-116">Par conséquent, lors de l’utilisation de ASP.NET Core, la journalisation doit être configurée comme décrit dans la [documentation de ASP.net Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="526ee-116">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="526ee-117">Autres applications</span><span class="sxs-lookup"><span data-stu-id="526ee-117">Other applications</span></span>

<span data-ttu-id="526ee-118">EF Core la journalisation requiert un ILoggerFactory qui est lui-même configuré avec un ou plusieurs fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="526ee-118">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="526ee-119">Les fournisseurs courants sont fournis dans les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="526ee-119">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="526ee-120">[Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): un simple journal de console.</span><span class="sxs-lookup"><span data-stu-id="526ee-120">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="526ee-121">[Microsoft. extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): prend en charge les fonctionnalités « journaux de diagnostic » et « flux de journal » des Services de Azure App.</span><span class="sxs-lookup"><span data-stu-id="526ee-121">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="526ee-122">[Microsoft. extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): se connecte à un moniteur du débogueur à l’aide de System. Diagnostics. Debug. WriteLine ().</span><span class="sxs-lookup"><span data-stu-id="526ee-122">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="526ee-123">[Microsoft. extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): enregistre dans le journal des événements Windows.</span><span class="sxs-lookup"><span data-stu-id="526ee-123">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="526ee-124">[Microsoft. extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): prend en charge EventSource/EventListener.</span><span class="sxs-lookup"><span data-stu-id="526ee-124">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="526ee-125">[Microsoft. extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): se connecte à un écouteur de suivi à l’aide de `System.Diagnostics.TraceSource.TraceEvent()` .</span><span class="sxs-lookup"><span data-stu-id="526ee-125">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="526ee-126">Après l’installation du ou des packages appropriés, l’application doit créer une instance singleton/globale d’un LoggerFactory.</span><span class="sxs-lookup"><span data-stu-id="526ee-126">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="526ee-127">Par exemple, à l’aide de l’enregistreur d’événements de console :</span><span class="sxs-lookup"><span data-stu-id="526ee-127">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="526ee-128">Version 3.x</span><span class="sxs-lookup"><span data-stu-id="526ee-128">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="526ee-129">Version 2.x</span><span class="sxs-lookup"><span data-stu-id="526ee-129">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="526ee-130">L’exemple de code suivant utilise un `ConsoleLoggerProvider` constructeur qui a été obsolète dans la version 2,2 et remplacé dans 3,0.</span><span class="sxs-lookup"><span data-stu-id="526ee-130">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="526ee-131">Il est possible d’ignorer et de supprimer sans risque les avertissements lors de l’utilisation de 2,2.</span><span class="sxs-lookup"><span data-stu-id="526ee-131">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="526ee-132">Cette instance de Singleton/global doit ensuite être inscrite auprès de EF Core sur le `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="526ee-132">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="526ee-133">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="526ee-133">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="526ee-134">Il est très important que les applications ne créent pas une nouvelle instance ILoggerFactory pour chaque instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="526ee-134">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="526ee-135">Cela entraînera une fuite de mémoire et des performances médiocres.</span><span class="sxs-lookup"><span data-stu-id="526ee-135">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="526ee-136">Filtrage des éléments consignés</span><span class="sxs-lookup"><span data-stu-id="526ee-136">Filtering what is logged</span></span>

<span data-ttu-id="526ee-137">L’application peut contrôler ce qui est enregistré en configurant un filtre sur le ILoggerProvider.</span><span class="sxs-lookup"><span data-stu-id="526ee-137">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="526ee-138">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="526ee-138">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="526ee-139">Version 3.x</span><span class="sxs-lookup"><span data-stu-id="526ee-139">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="526ee-140">Version 2.x</span><span class="sxs-lookup"><span data-stu-id="526ee-140">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="526ee-141">L’exemple de code suivant utilise un `ConsoleLoggerProvider` constructeur qui a été obsolète dans la version 2,2 et remplacé dans 3,0.</span><span class="sxs-lookup"><span data-stu-id="526ee-141">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="526ee-142">Il est possible d’ignorer et de supprimer sans risque les avertissements lors de l’utilisation de 2,2.</span><span class="sxs-lookup"><span data-stu-id="526ee-142">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

<span data-ttu-id="526ee-143">Dans cet exemple, le journal est filtré pour renvoyer uniquement les messages :</span><span class="sxs-lookup"><span data-stu-id="526ee-143">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="526ee-144">dans la catégorie « Microsoft. EntityFrameworkCore. Database. Command »</span><span class="sxs-lookup"><span data-stu-id="526ee-144">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="526ee-145">au niveau « information »</span><span class="sxs-lookup"><span data-stu-id="526ee-145">at the 'Information' level</span></span>

<span data-ttu-id="526ee-146">Par EF Core, les catégories de journalisation sont définies dans la `DbLoggerCategory` classe pour faciliter la recherche des catégories, mais elles sont résolues en chaînes simples.</span><span class="sxs-lookup"><span data-stu-id="526ee-146">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="526ee-147">Pour plus d’informations sur l’infrastructure de journalisation sous-jacente, consultez la [documentation ASP.net Core Logging](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="526ee-147">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
