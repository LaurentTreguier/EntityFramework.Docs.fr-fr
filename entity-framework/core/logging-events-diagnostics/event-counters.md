---
title: Compteurs d’événements-EF Core
description: Suivi des performances de EF Core et diagnostic des anomalies avec les compteurs d’événements .NET
author: roji
ms.date: 11/17/2020
uid: core/logging-events-diagnostics/event-counters
ms.openlocfilehash: 73d360b266db3d3252defbf4a4035c0eb430e22e
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635820"
---
# <a name="event-counters"></a><span data-ttu-id="3e7f4-103">Compteurs d’événements</span><span class="sxs-lookup"><span data-stu-id="3e7f4-103">Event Counters</span></span>

> [!NOTE]
> <span data-ttu-id="3e7f4-104">Cette fonctionnalité a été ajoutée dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-104">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="3e7f4-105">Entity Framework Core (EF Core) expose des mesures numériques continues qui peuvent fournir une bonne indication de l’intégrité de votre programme.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-105">Entity Framework Core (EF Core) exposes continuous numeric metrics which can provide a good indication of your program's health.</span></span> <span data-ttu-id="3e7f4-106">Ces métriques peuvent être utilisées dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="3e7f4-106">These metrics can be used for the following purposes:</span></span>

* <span data-ttu-id="3e7f4-107">Suivre le chargement général de la base de données en temps réel à mesure que l’application est en cours d’exécution</span><span class="sxs-lookup"><span data-stu-id="3e7f4-107">Track general database load in realtime as the application is running</span></span>
* <span data-ttu-id="3e7f4-108">Exposer les pratiques de codage problématiques qui peuvent entraîner une dégradation des performances</span><span class="sxs-lookup"><span data-stu-id="3e7f4-108">Expose problematic coding practices which can lead to degraded performance</span></span>
* <span data-ttu-id="3e7f4-109">Suivre et isoler le comportement anormal du programme</span><span class="sxs-lookup"><span data-stu-id="3e7f4-109">Track down and isolate anomalous program behavior</span></span>

<span data-ttu-id="3e7f4-110">EF Core signale les métriques par le biais de la fonctionnalité de compteurs d’événements .NET standard. Nous vous recommandons de lire [ce](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) billet de blog pour obtenir une vue d’ensemble rapide du fonctionnement des compteurs.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-110">EF Core reports metrics via the standard .NET event counters feature; it's recommended to read [this blog post](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) for a quick overview of how counters work.</span></span>

## <a name="attach-to-a-process-using-dotnet-counters"></a><span data-ttu-id="3e7f4-111">Attacher à un processus à l’aide de dotnet-Counters</span><span class="sxs-lookup"><span data-stu-id="3e7f4-111">Attach to a process using dotnet-counters</span></span>

<span data-ttu-id="3e7f4-112">L' [outil dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) peut être utilisé pour attacher à un processus en cours d’exécution et à des rapports EF Core des compteurs d’événements régulièrement. rien ne doit être spécial dans le programme pour que ces compteurs soient disponibles.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-112">The [dotnet-counters tool](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) can be used to attach to a running process and report EF Core event counters regularly; nothing special needs to be done in the program for these counters to be available.</span></span>

<span data-ttu-id="3e7f4-113">Tout d’abord, installez l' `dotnet-counters` outil : `dotnet tool install --global dotnet-counters` .</span><span class="sxs-lookup"><span data-stu-id="3e7f4-113">First, install the `dotnet-counters` tool: `dotnet tool install --global dotnet-counters`.</span></span>

<span data-ttu-id="3e7f4-114">Recherchez ensuite l’ID de processus (PID) du processus .NET exécutant votre application EF Core :</span><span class="sxs-lookup"><span data-stu-id="3e7f4-114">Next, find the process ID (PID) of the .NET process running your EF Core application:</span></span>

### <a name="windows"></a>[<span data-ttu-id="3e7f4-115">Windows</span><span class="sxs-lookup"><span data-stu-id="3e7f4-115">Windows</span></span>](#tab/windows)

1. <span data-ttu-id="3e7f4-116">Ouvrez le gestionnaire des tâches Windows en cliquant avec le bouton droit sur la barre des tâches et en sélectionnant « gestionnaire des tâches ».</span><span class="sxs-lookup"><span data-stu-id="3e7f4-116">Open the Windows Task Manager by right-clicking on the task bar and selecting "Task Manager".</span></span>
2. <span data-ttu-id="3e7f4-117">Assurez-vous que l’option « plus de détails » est sélectionnée au bas de la fenêtre.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-117">Make sure that the "More details" option is selected at the bottom of the window.</span></span>
3. <span data-ttu-id="3e7f4-118">Sous l’onglet processus, cliquez avec le bouton droit sur une colonne et assurez-vous que la colonne PID est activée.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-118">In the Processes tab, right-click a column and make sure that the PID column is enabled.</span></span>
4. <span data-ttu-id="3e7f4-119">Localisez votre application dans la liste des processus et récupérez son ID de processus à partir de la colonne PID.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-119">Locate your application in the process list, and get its process ID from the PID column.</span></span>

### <a name="linux-or-macos"></a>[<span data-ttu-id="3e7f4-120">Linux ou macOS</span><span class="sxs-lookup"><span data-stu-id="3e7f4-120">Linux or macOS</span></span>](#tab/fluent-api)

1. <span data-ttu-id="3e7f4-121">Utilisez la `ps` commande pour répertorier tous les processus en cours d’exécution pour votre utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-121">Use the `ps` command to list all processes running for your user.</span></span>
2. <span data-ttu-id="3e7f4-122">Localisez votre application dans la liste des processus et récupérez son ID de processus à partir de la colonne PID.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-122">Locate your application in the process list, and get its process ID from the PID column.</span></span>

***

<span data-ttu-id="3e7f4-123">Dans votre application .NET, l’ID de processus est disponible sous la forme `Process.GetCurrentProcess().Id` ; cela peut être utile pour l’impression du PID au démarrage.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-123">Inside your .NET application, the process ID is available as `Process.GetCurrentProcess().Id`; this can be useful for printing the PID upon startup.</span></span>

<span data-ttu-id="3e7f4-124">Enfin, lancez `dotnet-counters` comme suit :</span><span class="sxs-lookup"><span data-stu-id="3e7f4-124">Finally, launch `dotnet-counters` as follows:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

<span data-ttu-id="3e7f4-125">`dotnet-counters` va maintenant s’attacher à votre processus en cours d’exécution et démarrer la création de rapports sur les données de compteur continues :</span><span class="sxs-lookup"><span data-stu-id="3e7f4-125">`dotnet-counters` will now attach to your running process and start reporting continuous counter data:</span></span>

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

## <a name="counters-and-their-meaning"></a><span data-ttu-id="3e7f4-126">Compteurs et leur signification</span><span class="sxs-lookup"><span data-stu-id="3e7f4-126">Counters and their meaning</span></span>

<span data-ttu-id="3e7f4-127">Nom du compteur</span><span class="sxs-lookup"><span data-stu-id="3e7f4-127">Counter name</span></span>                          | <span data-ttu-id="3e7f4-128">Description</span><span class="sxs-lookup"><span data-stu-id="3e7f4-128">Description</span></span>
------------------------------------- | ----
<span data-ttu-id="3e7f4-129">DbContexts active</span><span class="sxs-lookup"><span data-stu-id="3e7f4-129">Active DbContexts</span></span>                     | <span data-ttu-id="3e7f4-130">Nombre d’instances de DbContext actives et inactives actuellement dans votre application.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-130">The number of active, undisposed DbContext instances currently in your application.</span></span> <span data-ttu-id="3e7f4-131">Si ce nombre augmente continuellement, vous pouvez avoir une fuite, car les instances de DbContext ne sont pas correctement supprimées.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-131">If this number grows continuously, you may have a leak because DbContext instances aren't being properly disposed.</span></span> <span data-ttu-id="3e7f4-132">Notez que si le [regroupement de contexte](xref:core/performance/advanced-performance-topics#dbcontext-pooling) est activé, ce nombre comprend les instances de DbContext regroupées qui ne sont pas en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-132">Note that if [context pooling](xref:core/performance/advanced-performance-topics#dbcontext-pooling) is enabled, this number includes pooled DbContext instances not currently in use.</span></span>
<span data-ttu-id="3e7f4-133">Échecs de l’opération de stratégie d’exécution</span><span class="sxs-lookup"><span data-stu-id="3e7f4-133">Execution Strategy Operation Failures</span></span> | <span data-ttu-id="3e7f4-134">Nombre de fois qu’une opération de base de données n’a pas pu s’exécuter.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-134">The number of times a database operation failed to execute.</span></span> <span data-ttu-id="3e7f4-135">Si une stratégie de nouvelle tentative d’exécution est activée, cela comprend chaque échec individuel dans plusieurs tentatives sur la même opération.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-135">If a retrying execution strategy is enabled, this includes each individual failure within multiple attempts on the same operation.</span></span> <span data-ttu-id="3e7f4-136">Cela peut être utilisé pour détecter les problèmes temporaires liés à votre infrastructure.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-136">This can be used to detect transient issues with your infrastructure.</span></span>
<span data-ttu-id="3e7f4-137">Échecs d’accès concurrentiel optimiste</span><span class="sxs-lookup"><span data-stu-id="3e7f4-137">Optimistic Concurrency Failures</span></span>       | <span data-ttu-id="3e7f4-138">Nombre d' `SaveChanges` échecs en raison d’une erreur d’accès concurrentiel optimiste, car les données du magasin de données ont été modifiées depuis le chargement du code.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-138">The number of times `SaveChanges` failed because of an optimistic concurrency error, because data in the data store was changed since your code loaded it.</span></span> <span data-ttu-id="3e7f4-139">Cela correspond à une <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> levée.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-139">This corresponds to a <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> being thrown.</span></span>
<span data-ttu-id="3e7f4-140">Requêtes</span><span class="sxs-lookup"><span data-stu-id="3e7f4-140">Queries</span></span>                               | <span data-ttu-id="3e7f4-141">Nombre de requêtes exécutées.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-141">The number of queries executed.</span></span>
<span data-ttu-id="3e7f4-142">Taux d’accès au cache des requêtes (%)</span><span class="sxs-lookup"><span data-stu-id="3e7f4-142">Query Cache Hit Rate (%)</span></span>              | <span data-ttu-id="3e7f4-143">Taux d’accès au cache de requêtes pour les échecs.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-143">The ratio of query cache hits to misses.</span></span> <span data-ttu-id="3e7f4-144">La première fois qu’une requête LINQ donnée est exécutée par EF Core (à l’exclusion des paramètres), elle doit être compilée dans ce qui est un processus relativement lourd.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-144">The first time a given LINQ query is executed by EF Core (excluding parameters), it must be compiled in what is a relatively heavy process.</span></span> <span data-ttu-id="3e7f4-145">Dans une application normale, toutes les requêtes sont réutilisées et le taux d’accès au cache de requête doit être stable à 100% après une période de préparation initiale.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-145">In a normal application, all queries are reused, and the query cache hit rate should be stable at 100% after an initial warmup period.</span></span> <span data-ttu-id="3e7f4-146">Si ce nombre est inférieur à 100% dans le temps, il se peut que vous rencontriez une dégradation des performances en raison de compilations répétées, qui peuvent résulter d’une génération de requêtes dynamiques non optimales.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-146">If this number is less than 100% over time, you may experience degraded perf due to repeated compilations, which could be a result of suboptimal dynamic query generation.</span></span>
<span data-ttu-id="3e7f4-147">SaveChanges</span><span class="sxs-lookup"><span data-stu-id="3e7f4-147">SaveChanges</span></span>                           | <span data-ttu-id="3e7f4-148">Nombre de fois où `SaveChanges` a été appelé.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-148">The number of times `SaveChanges` has been called.</span></span> <span data-ttu-id="3e7f4-149">Notez que l' `SaveChanges` enregistrement de plusieurs modifications dans un même lot ne représente pas nécessairement chaque mise à jour individuelle effectuée sur une seule entité.</span><span class="sxs-lookup"><span data-stu-id="3e7f4-149">Note that `SaveChanges` saves multiple changes in a single batch, so this doesn't necessarily represent each individual update done on a single entity.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e7f4-150">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3e7f4-150">Additional resources</span></span>

* [<span data-ttu-id="3e7f4-151">Documentation .NET sur les compteurs d’événements</span><span class="sxs-lookup"><span data-stu-id="3e7f4-151">.NET documentation on event counters</span></span>](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
