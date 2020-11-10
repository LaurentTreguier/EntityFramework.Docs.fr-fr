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
# <a name="using-microsoftextensionslogging-in-ef-core"></a>Utilisation de Microsoft. extensions. Logging dans EF Core

[Microsoft. extensions. Logging](/dotnet/core/extensions/logging) est un mécanisme de journalisation extensible avec des fournisseurs de plug-ins pour de nombreux systèmes de journalisation courants. Les plug-ins fournis par Microsoft (par exemple, [Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) et les plug-ins tiers (par exemple, [Serilog. extensions. Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) sont disponibles sous forme de packages NuGet.

Entity Framework Core (EF Core) s’intègre entièrement à `Microsoft.Extensions.Logging` . Toutefois, envisagez d’utiliser la [journalisation simple](xref:core/logging-events-diagnostics/simple-logging) pour un enregistrement plus simple, en particulier pour les applications qui n’utilisent pas l’injection de dépendances.

## <a name="aspnet-core-applications"></a>applications ASP.NET Core ;

`Microsoft.Extensions.Logging` est [utilisé par défaut dans les applications ASP.net Core](/aspnet/core/fundamentals/logging). Appel <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> de ou <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> .

## <a name="other-application-types"></a>Autres types d’applications

D’autres types d’applications peuvent utiliser [GenericHost](/dotnet/core/extensions/generic-host) pour récupérer les mêmes modèles d’injection de dépendances que ceux utilisés dans ASP.net core. AddDbContext ou AddDbContextPool peut ensuite être utilisé comme dans les applications ASP.NET Core.

`Microsoft.Extensions.Logging` peut également être utilisé pour les applications qui n’utilisent pas l’injection de dépendances, bien que la [journalisation simple](xref:core/logging-events-diagnostics/simple-logging) puisse être plus facile à configurer.

`Microsoft.Extensions.Logging` requiert la création d’un <xref:Microsoft.Extensions.Logging.LoggerFactory> . Cette fabrique doit être stockée en tant qu’instance statique/globale quelque part et utilisée chaque fois qu’un DbContext est créé. Par exemple, il est courant de stocker la fabrique d’enregistreur d’événements en tant que propriété statique sur le DbContext.

### <a name="ef-core-30-and-above"></a>[EF Core 3,0 et versions ultérieures](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[EF Core 2.1](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> Dans EF Core 2,1, il est très important que les applications ne créent pas une nouvelle instance LoggerFactory pour chaque instance DbContext. Cela entraînera une fuite de mémoire et des performances médiocres. Ce problème a été résolu dans EF Core 3,0 et versions ultérieures.

***

Cette instance de Singleton/global doit ensuite être inscrite auprès de EF Core sur le <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> . Par exemple :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a>Obtention de messages détaillés

> [!TIP]
> OnConfiguring est toujours appelé lorsque AddDbContext est utilisé ou qu’une instance DbContextOptions est passée au constructeur DbContext. C’est ainsi l’emplacement idéal pour appliquer la configuration du contexte, quelle que soit la façon dont la méthode DbContext est construite.

### <a name="sensitive-data"></a>Données sensibles

Par défaut, EF Core n’inclut pas les valeurs des données dans les messages d’exception. Cela est dû au fait que ces données peuvent être confidentielles et peuvent être révélées en production si une exception n’est pas gérée.

Toutefois, le fait de connaître les valeurs de données, en particulier pour les clés, peut être très utile lors du débogage. Vous pouvez l’activer dans EF Core en appelant <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> . Par exemple :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>Exceptions de requête détaillées

Pour des raisons de performances, EF Core n’encapsule pas chaque appel pour lire une valeur à partir du fournisseur de base de données dans un bloc try-catch. Toutefois, cela entraîne parfois des exceptions difficiles à diagnostiquer, en particulier lorsque la base de données retourne une valeur NULL lorsqu’elle n’est pas autorisée par le modèle.

Si vous activez la fonction, <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> EF introduira ces blocs try-catch et fournira des erreurs plus détaillées. Par exemple :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a>Configuration de messages spécifiques

L' <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API EF Core permet aux applications de modifier ce qui se produit lorsqu’un événement spécifique est rencontré. Cela peut être utilisé pour :

* Modifier le niveau de journal auquel l’événement est enregistré
* Ignorer l’enregistrement de l’événement
* Lever une exception lorsque l’événement se produit

### <a name="changing-the-log-level-for-an-event"></a>Modification du niveau de journalisation d’un événement

Parfois, il peut être utile de modifier le niveau de journal prédéfini pour un événement. Par exemple, il peut être utilisé pour promouvoir deux événements supplémentaires de `LogLevel.Debug` à `LogLevel.Information` :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>Supprimer la journalisation d’un événement

De la même façon, un événement individuel peut être supprimé de la journalisation. Cela est particulièrement utile pour ignorer un avertissement qui a été révisé et compris. Par exemple :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>Lever pour un événement

Enfin, EF Core peut être configuré pour lever une exception pour un événement donné. Cela s’avère particulièrement utile pour modifier un avertissement en erreur. (En effet, c’était l’objectif initial de la `ConfigureWarnings` méthode, donc le nom.) Par exemple :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a>Filtrage et autre configuration

Consultez [journalisation dans .net](/dotnet/core/extensions/logging) pour obtenir des conseils sur le filtrage des journaux et d’autres configurations.

EF Core événements de journalisation sont définis dans l’un des éléments suivants :

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> pour les événements communs à tous les fournisseurs de bases de données EF Core
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> pour les événements communs à tous les fournisseurs de bases de données relationnelles
* Classe similaire pour les événements spécifiques au fournisseur de base de données actuel. Par exemple, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> pour le fournisseur SQL Server.

Ces définitions contiennent les ID d’événement, le niveau de journal et la catégorie de chaque événement, tels qu’ils sont utilisés par `Microsoft.Extensions.Logging` .
