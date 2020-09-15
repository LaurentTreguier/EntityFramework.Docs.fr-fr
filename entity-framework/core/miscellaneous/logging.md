---
title: Journalisation-EF Core
description: Configuration de la journalisation avec Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/miscellaneous/logging
ms.openlocfilehash: 0fd1c83f01989095a813727390179db2327b610d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071665"
---
# <a name="logging"></a><span data-ttu-id="26297-103">Journalisation</span><span class="sxs-lookup"><span data-stu-id="26297-103">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="26297-104">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="26297-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="26297-105">Applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26297-105">ASP.NET Core applications</span></span>

<span data-ttu-id="26297-106">EF Core s’intègre automatiquement avec les mécanismes de journalisation de ASP.NET Core chaque fois que `AddDbContext` ou `AddDbContextPool` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="26297-106">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="26297-107">Par conséquent, lors de l’utilisation de ASP.NET Core, la journalisation doit être configurée comme décrit dans la [documentation de ASP.net Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="26297-107">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="26297-108">Autres applications</span><span class="sxs-lookup"><span data-stu-id="26297-108">Other applications</span></span>

<span data-ttu-id="26297-109">EF Core la journalisation requiert un ILoggerFactory qui est lui-même configuré avec un ou plusieurs fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="26297-109">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="26297-110">Les fournisseurs courants sont fournis dans les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="26297-110">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="26297-111">[Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): un simple journal de console.</span><span class="sxs-lookup"><span data-stu-id="26297-111">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="26297-112">[Microsoft. extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): prend en charge les fonctionnalités « journaux de diagnostic » et « flux de journal » des Services de Azure App.</span><span class="sxs-lookup"><span data-stu-id="26297-112">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="26297-113">[Microsoft. extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): se connecte à un moniteur du débogueur à l’aide de System. Diagnostics. Debug. WriteLine ().</span><span class="sxs-lookup"><span data-stu-id="26297-113">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="26297-114">[Microsoft. extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): enregistre dans le journal des événements Windows.</span><span class="sxs-lookup"><span data-stu-id="26297-114">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="26297-115">[Microsoft. extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): prend en charge EventSource/EventListener.</span><span class="sxs-lookup"><span data-stu-id="26297-115">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="26297-116">[Microsoft. extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): se connecte à un écouteur de suivi à l’aide de `System.Diagnostics.TraceSource.TraceEvent()` .</span><span class="sxs-lookup"><span data-stu-id="26297-116">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="26297-117">Après l’installation du ou des packages appropriés, l’application doit créer une instance singleton/globale d’un LoggerFactory.</span><span class="sxs-lookup"><span data-stu-id="26297-117">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="26297-118">Par exemple, à l’aide de l’enregistreur d’événements de console :</span><span class="sxs-lookup"><span data-stu-id="26297-118">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="26297-119">Version 3.x</span><span class="sxs-lookup"><span data-stu-id="26297-119">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="26297-120">Version 2.x</span><span class="sxs-lookup"><span data-stu-id="26297-120">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="26297-121">L’exemple de code suivant utilise un `ConsoleLoggerProvider` constructeur qui a été obsolète dans la version 2,2 et remplacé dans 3,0.</span><span class="sxs-lookup"><span data-stu-id="26297-121">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="26297-122">Il est possible d’ignorer et de supprimer sans risque les avertissements lors de l’utilisation de 2,2.</span><span class="sxs-lookup"><span data-stu-id="26297-122">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="26297-123">Cette instance de Singleton/global doit ensuite être inscrite auprès de EF Core sur le `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="26297-123">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="26297-124">Exemple :</span><span class="sxs-lookup"><span data-stu-id="26297-124">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="26297-125">Il est très important que les applications ne créent pas une nouvelle instance ILoggerFactory pour chaque instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="26297-125">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="26297-126">Cela entraînera une fuite de mémoire et des performances médiocres.</span><span class="sxs-lookup"><span data-stu-id="26297-126">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="26297-127">Filtrage des éléments consignés</span><span class="sxs-lookup"><span data-stu-id="26297-127">Filtering what is logged</span></span>

<span data-ttu-id="26297-128">L’application peut contrôler ce qui est enregistré en configurant un filtre sur le ILoggerProvider.</span><span class="sxs-lookup"><span data-stu-id="26297-128">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="26297-129">Exemple :</span><span class="sxs-lookup"><span data-stu-id="26297-129">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="26297-130">Version 3.x</span><span class="sxs-lookup"><span data-stu-id="26297-130">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="26297-131">Version 2.x</span><span class="sxs-lookup"><span data-stu-id="26297-131">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="26297-132">L’exemple de code suivant utilise un `ConsoleLoggerProvider` constructeur qui a été obsolète dans la version 2,2 et remplacé dans 3,0.</span><span class="sxs-lookup"><span data-stu-id="26297-132">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="26297-133">Il est possible d’ignorer et de supprimer sans risque les avertissements lors de l’utilisation de 2,2.</span><span class="sxs-lookup"><span data-stu-id="26297-133">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

<span data-ttu-id="26297-134">Dans cet exemple, le journal est filtré pour renvoyer uniquement les messages :</span><span class="sxs-lookup"><span data-stu-id="26297-134">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="26297-135">dans la catégorie « Microsoft. EntityFrameworkCore. Database. Command »</span><span class="sxs-lookup"><span data-stu-id="26297-135">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="26297-136">au niveau « information »</span><span class="sxs-lookup"><span data-stu-id="26297-136">at the 'Information' level</span></span>

<span data-ttu-id="26297-137">Par EF Core, les catégories de journalisation sont définies dans la `DbLoggerCategory` classe pour faciliter la recherche des catégories, mais elles sont résolues en chaînes simples.</span><span class="sxs-lookup"><span data-stu-id="26297-137">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="26297-138">Pour plus d’informations sur l’infrastructure de journalisation sous-jacente, consultez la [documentation ASP.net Core Logging](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="26297-138">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
