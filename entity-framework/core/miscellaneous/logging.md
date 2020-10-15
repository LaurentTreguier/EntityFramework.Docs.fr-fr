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
# <a name="logging"></a>Journalisation

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) sur GitHub.

## <a name="simple-logging"></a>Journalisation simple

> [!NOTE]
> Cette fonctionnalité a été ajoutée dans EF Core 5,0.

Entity Framework Core (EF Core) génère des messages de journal pour des opérations telles que l’exécution d’une requête ou l’enregistrement des modifications apportées à la base de données. Vous pouvez y accéder à partir de n’importe quel type d’application via l’utilisation de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> lors [de la configuration d’une instance DbContext](xref:core/miscellaneous/configuring-dbcontext). Cette configuration s’effectue généralement dans une substitution de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Ce concept est semblable à <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> dans EF6.

Pour plus d’informations, consultez [journalisation simple](xref:core/miscellaneous/events/simple-logging) .

## <a name="aspnet-core-applications"></a>applications ASP.NET Core ;

EF Core s’intègre automatiquement avec les mécanismes de journalisation de ASP.NET Core chaque fois que `AddDbContext` ou `AddDbContextPool` est utilisé. Par conséquent, lors de l’utilisation de ASP.NET Core, la journalisation doit être configurée comme décrit dans la [documentation de ASP.net Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).

## <a name="other-applications"></a>Autres applications

EF Core la journalisation requiert un ILoggerFactory qui est lui-même configuré avec un ou plusieurs fournisseurs de journalisation. Les fournisseurs courants sont fournis dans les packages suivants :

* [Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): un simple journal de console.
* [Microsoft. extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): prend en charge les fonctionnalités « journaux de diagnostic » et « flux de journal » des Services de Azure App.
* [Microsoft. extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): se connecte à un moniteur du débogueur à l’aide de System. Diagnostics. Debug. WriteLine ().
* [Microsoft. extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): enregistre dans le journal des événements Windows.
* [Microsoft. extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): prend en charge EventSource/EventListener.
* [Microsoft. extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): se connecte à un écouteur de suivi à l’aide de `System.Diagnostics.TraceSource.TraceEvent()` .

Après l’installation du ou des packages appropriés, l’application doit créer une instance singleton/globale d’un LoggerFactory. Par exemple, à l’aide de l’enregistreur d’événements de console :

### <a name="version-3x"></a>[Version 3.x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[Version 2.x](#tab/v2)

> [!NOTE]
> L’exemple de code suivant utilise un `ConsoleLoggerProvider` constructeur qui a été obsolète dans la version 2,2 et remplacé dans 3,0. Il est possible d’ignorer et de supprimer sans risque les avertissements lors de l’utilisation de 2,2.

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

Cette instance de Singleton/global doit ensuite être inscrite auprès de EF Core sur le `DbContextOptionsBuilder` . Par exemple :

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> Il est très important que les applications ne créent pas une nouvelle instance ILoggerFactory pour chaque instance de contexte. Cela entraînera une fuite de mémoire et des performances médiocres.

## <a name="filtering-what-is-logged"></a>Filtrage des éléments consignés

L’application peut contrôler ce qui est enregistré en configurant un filtre sur le ILoggerProvider. Par exemple :

### <a name="version-3x"></a>[Version 3.x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[Version 2.x](#tab/v2)

> [!NOTE]
> L’exemple de code suivant utilise un `ConsoleLoggerProvider` constructeur qui a été obsolète dans la version 2,2 et remplacé dans 3,0. Il est possible d’ignorer et de supprimer sans risque les avertissements lors de l’utilisation de 2,2.

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

Dans cet exemple, le journal est filtré pour renvoyer uniquement les messages :

* dans la catégorie « Microsoft. EntityFrameworkCore. Database. Command »
* au niveau « information »

Par EF Core, les catégories de journalisation sont définies dans la `DbLoggerCategory` classe pour faciliter la recherche des catégories, mais elles sont résolues en chaînes simples.

Pour plus d’informations sur l’infrastructure de journalisation sous-jacente, consultez la [documentation ASP.net Core Logging](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).
