---
title: Vue d’ensemble de la journalisation et de l’interception-EF Core
description: Vue d’ensemble de la journalisation, des événements, des intercepteurs et des diagnostics pour EF Core
author: ajcvickers
ms.date: 10/01/2020
uid: core/logging-events-diagnostics/index
ms.openlocfilehash: 2c44772b22112645f85cf0bffa680bc510ea5afb
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003521"
---
# <a name="overview-of-logging-and-interception"></a>Vue d’ensemble de la journalisation et de l’interception

Entity Framework Core (EF Core) contient plusieurs mécanismes permettant de générer des journaux, de répondre aux événements et d’obtenir des Diagnostics. Chacune d’elles est adaptée à différentes situations, et il est important de sélectionner le mécanisme le mieux adapté à la tâche, même lorsque plusieurs mécanismes peuvent fonctionner. Par exemple, un intercepteur de base de données peut être utilisé pour journaliser SQL, mais il est mieux géré par l’un des mécanismes adaptés à la journalisation. Cette page présente une vue d’ensemble de chacun de ces mécanismes et décrit les cas d’utilisation de chacun d’entre eux.

## <a name="quick-reference"></a>Aide-mémoire

Le tableau ci-dessous fournit une référence rapide pour les différences entre les mécanismes décrits ici.

| Mechanism |  Async | Étendue | Inscrit | Usage prévu
|:----------|--------|-------|------------|-------------
| Journalisation simple | Non | Par contexte | Configuration du contexte | Journalisation au moment du développement
| Microsoft.Extensions.Logging | Non | Par contexte * | D.I. configuration du contexte ou | Journalisation de la production
| Événements | Non | Par contexte | N'importe quand | Réagir aux événements EF
| Intercepteurs | Oui | Par contexte | Configuration du contexte | Manipulation des opérations EF
| Écouteurs de diagnostic | Non | Process | Globalement | Diagnostic d'application

* `Microsoft.Extensions.Logging` Est généralement configuré par application via l’injection de dépendances, mais au niveau d’EF, chaque contexte _peut_ être configuré avec un journal différent si nécessaire.

## <a name="simple-logging"></a>Journalisation simple

> [!NOTE]
> Cette fonctionnalité a été introduite dans EF Core 5,0.

Les journaux de EF Core sont accessibles à partir de n’importe quel type d’application par le biais de l’utilisation de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> lors [de la configuration d’une instance DbContext](xref:core/dbcontext-configuration/index). Cette configuration s’effectue généralement dans une substitution de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Ce concept est semblable à <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> dans EF6.

Pour plus d’informations, consultez [journalisation simple](xref:core/logging-events-diagnostics/simple-logging) .

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

[Microsoft. extensions. Logging](/dotnet/core/extensions/logging) est un mécanisme de journalisation extensible avec des fournisseurs de plug-ins pour de nombreux systèmes de journalisation courants. EF Core s’intègre entièrement à `Microsoft.Extensions.Logging` et cette forme de journalisation est utilisée par défaut pour les applications ASP.net core.

Pour plus d’informations, consultez [utilisation de Microsoft. extensions. Logging dans EF Core](xref:core/logging-events-diagnostics/extensions-logging) .

## <a name="events"></a>Événements

> [!NOTE]
> Des événements supplémentaires ont été introduits dans EF Core 5,0.

EF Core expose des [événements .net](/dotnet/standard/events/) pour agir en tant que rappels lorsque certains événements se produisent dans le code EF Core. Les événements sont plus simples que les intercepteurs et permettent une inscription plus flexible. Toutefois, ils sont synchronisés uniquement et ne peuvent donc pas effectuer d’e/s Async non bloquantes.

Les événements sont inscrits par instance DbContext et cette inscription peut être effectuée à tout moment. Utilisez un [écouteur de diagnostic](xref:core/logging-events-diagnostics/diagnostic-listeners) pour obtenir les mêmes informations, mais pour toutes les instances de DbContext dans le processus.

Pour plus d’informations, consultez [événements .net dans EF Core](xref:core/logging-events-diagnostics/events) .

## <a name="interception"></a>Interception

> [!NOTE]
> Cette fonctionnalité a été introduite dans EF Core 3,0. Des intercepteurs supplémentaires ont été introduits dans EF Core 5,0.

Les intercepteurs de EF Core permettent l’interception, la modification et/ou la suppression d’opérations de EF Core. Cela comprend les opérations de base de données de bas niveau telles que l’exécution d’une commande, ainsi que les opérations de niveau supérieur, telles que les appels à SaveChanges.

Les intercepteurs diffèrent de la journalisation et des diagnostics en ce qu’ils autorisent la modification ou la suppression de l’opération qui est interceptée. La journalisation [simple](xref:core/logging-events-diagnostics/simple-logging) ou [Microsoft. extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) sont de meilleures options pour la journalisation.

Les intercepteurs sont inscrits par instance DbContext lorsque le contexte est configuré. Utilisez un [écouteur de diagnostic](xref:core/logging-events-diagnostics/diagnostic-listeners) pour obtenir les mêmes informations, mais pour toutes les instances de DbContext dans le processus.

Pour plus d’informations, consultez [interception](xref:core/logging-events-diagnostics/interceptors) .

## <a name="diagnostic-listeners"></a>Écouteurs de diagnostic

Les écouteurs de diagnostic permettent d’écouter les EF Core événement qui se produit dans le processus .NET actuel.

Les écouteurs de diagnostic ne sont pas adaptés à l’obtention d’événements à partir d’une seule instance DbContext. Les intercepteurs EF Core donnent accès aux mêmes événements avec l’inscription par contexte.

Les écouteurs de diagnostic ne sont pas conçus pour la journalisation. La journalisation [simple](xref:core/logging-events-diagnostics/simple-logging) ou [Microsoft. extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) sont de meilleures options pour la journalisation.

Pour plus d’informations, consultez [utilisation des écouteurs de diagnostics dans EF Core](xref:core/logging-events-diagnostics/diagnostic-listeners) .
