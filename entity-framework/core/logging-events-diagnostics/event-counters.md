---
title: Compteurs d’événements-EF Core
description: Suivi des performances de EF Core et diagnostic des anomalies avec les compteurs d’événements .NET
author: roji
ms.date: 11/17/2020
uid: core/logging-events-diagnostics/event-counters
ms.openlocfilehash: 46acfe82d8aeb7d16146bae0cc2cd4ff733e2831
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003704"
---
# <a name="event-counters"></a>Compteurs d’événements

> [!NOTE]
> Cette fonctionnalité a été ajoutée dans EF Core 5,0.

Entity Framework Core (EF Core) expose des mesures numériques continues qui peuvent fournir une bonne indication de l’intégrité de votre programme. Ces métriques peuvent être utilisées dans les cas suivants :

* Suivre le chargement général de la base de données en temps réel à mesure que l’application est en cours d’exécution
* Exposer les pratiques de codage problématiques qui peuvent entraîner une dégradation des performances
* Suivre et isoler le comportement anormal du programme

EF Core signale les métriques par le biais de la fonctionnalité de compteurs d’événements .NET standard. Nous vous recommandons de lire [ce](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) billet de blog pour obtenir une vue d’ensemble rapide du fonctionnement des compteurs.

## <a name="attach-to-a-process-using-dotnet-counters"></a>Attacher à un processus à l’aide de dotnet-Counters

L' [outil dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) peut être utilisé pour attacher à un processus en cours d’exécution et à des rapports EF Core des compteurs d’événements régulièrement. rien ne doit être spécial dans le programme pour que ces compteurs soient disponibles.

Tout d’abord, installez l' `dotnet-counters` outil : `dotnet tool install --global dotnet-counters` .

Recherchez ensuite l’ID de processus (PID) du processus .NET exécutant votre application EF Core :

### <a name="windows"></a>[Windows](#tab/windows)

1. Ouvrez le gestionnaire des tâches Windows en cliquant avec le bouton droit sur la barre des tâches et en sélectionnant « gestionnaire des tâches ».
2. Assurez-vous que l’option « plus de détails » est sélectionnée au bas de la fenêtre.
3. Sous l’onglet processus, cliquez avec le bouton droit sur une colonne et assurez-vous que la colonne PID est activée.
4. Localisez votre application dans la liste des processus et récupérez son ID de processus à partir de la colonne PID.

### <a name="linux-or-macos"></a>[Linux ou macOS](#tab/fluent-api)

1. Utilisez la `ps` commande pour répertorier tous les processus en cours d’exécution pour votre utilisateur.
2. Localisez votre application dans la liste des processus et récupérez son ID de processus à partir de la colonne PID.

***

Dans votre application .NET, l’ID de processus est disponible sous la forme `Process.GetCurrentProcess().Id` ; cela peut être utile pour l’impression du PID au démarrage.

Enfin, lancez `dotnet-counters` comme suit :

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

`dotnet-counters` va maintenant s’attacher à votre processus en cours d’exécution et démarrer la création de rapports sur les données de compteur continues :

```console
Press p to pause, r to resume, q to quit.
 Status: Running

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                         1
    Queries (Total)                                               189
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             0
```

## <a name="counters-and-their-meaning"></a>Compteurs et leur signification

Nom du compteur                          | Description
------------------------------------- | ----
DbContexts active                     | Nombre d’instances de DbContext actives et inactives actuellement dans votre application. Si ce nombre augmente continuellement, vous pouvez avoir une fuite, car les instances de DbContext ne sont pas correctement supprimées. Notez que si le [regroupement de contexte](xref:core/miscellaneous/context-pooling) est activé, ce nombre comprend les instances de DbContext regroupées qui ne sont pas en cours d’utilisation.
Échecs de l’opération de stratégie d’exécution | Nombre de fois qu’une opération de base de données n’a pas pu s’exécuter. Si une stratégie de nouvelle tentative d’exécution est activée, cela comprend chaque échec individuel dans plusieurs tentatives sur la même opération. Cela peut être utilisé pour détecter les problèmes temporaires liés à votre infrastructure.
Échecs d’accès concurrentiel optimiste       | Nombre d' `SaveChanges` échecs en raison d’une erreur d’accès concurrentiel optimiste, car les données du magasin de données ont été modifiées depuis le chargement du code. Cela correspond à une <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> levée.
Requêtes                               | Nombre de requêtes exécutées.
Taux d’accès au cache des requêtes (%)              | Taux d’accès au cache de requêtes pour les échecs. La première fois qu’une requête LINQ donnée est exécutée par EF Core (à l’exclusion des paramètres), elle doit être compilée dans ce qui est un processus relativement lourd. Dans une application normale, toutes les requêtes sont réutilisées et le taux d’accès au cache de requête doit être stable à 100% après une période de préparation initiale. Si ce nombre est inférieur à 100% dans le temps, il se peut que vous rencontriez une dégradation des performances en raison de compilations répétées, qui peuvent résulter d’une génération de requêtes dynamiques non optimales.
SaveChanges                           | Nombre de fois où `SaveChanges` a été appelé. Notez que l' `SaveChanges` enregistrement de plusieurs modifications dans un même lot ne représente pas nécessairement chaque mise à jour individuelle effectuée sur une seule entité.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Documentation .NET sur les compteurs d’événements](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
