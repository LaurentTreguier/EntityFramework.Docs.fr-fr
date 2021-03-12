---
title: Détection des modifications et notifications-EF Core
description: Détection des modifications des propriétés et des relations à l’aide de DetectChanges ou de notifications
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: fae8bdb1a89478531535b377f4ba8b02d1c848f4
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023183"
---
# <a name="change-detection-and-notifications"></a><span data-ttu-id="55821-103">Détection des modifications et notifications</span><span class="sxs-lookup"><span data-stu-id="55821-103">Change Detection and Notifications</span></span>

<span data-ttu-id="55821-104">Chaque <xref:Microsoft.EntityFrameworkCore.DbContext> instance suit les modifications apportées aux entités.</span><span class="sxs-lookup"><span data-stu-id="55821-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="55821-105">Ces entités suivies entraînent à leur tour les modifications apportées à la base de données lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="55821-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="55821-106">Cela est abordé dans [change Tracking dans EF Core](xref:core/change-tracking/index), et ce document suppose que les États d’entité et les principes de base du suivi des modifications Entity Framework Core (EF Core) sont compris.</span><span class="sxs-lookup"><span data-stu-id="55821-106">This is covered in [Change Tracking in EF Core](xref:core/change-tracking/index), and this document assumes that entity states and the basics of Entity Framework Core (EF Core) change tracking are understood.</span></span>

<span data-ttu-id="55821-107">Les modifications de propriété et de relation de suivi requièrent que DbContext soit en mesure de détecter ces modifications.</span><span class="sxs-lookup"><span data-stu-id="55821-107">Tracking property and relationship changes requires that the DbContext is able to detect these changes.</span></span> <span data-ttu-id="55821-108">Ce document explique comment cette détection se produit, et comment utiliser les notifications de propriétés ou les proxys de suivi des modifications pour forcer la détection immédiate des modifications.</span><span class="sxs-lookup"><span data-stu-id="55821-108">This document covers how this detection happens, as well as how to use property notifications or change-tracking proxies to force immediate detection of changes.</span></span>

> [!TIP]
> <span data-ttu-id="55821-109">Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeDetectionAndNotifications).</span><span class="sxs-lookup"><span data-stu-id="55821-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeDetectionAndNotifications).</span></span>

## <a name="snapshot-change-tracking"></a><span data-ttu-id="55821-110">Suivi des modifications par instantané</span><span class="sxs-lookup"><span data-stu-id="55821-110">Snapshot change tracking</span></span>

<span data-ttu-id="55821-111">Par défaut, EF Core crée un instantané des valeurs de propriété de chaque entité lorsqu’il est d’abord suivi par une instance DbContext.</span><span class="sxs-lookup"><span data-stu-id="55821-111">By default, EF Core creates a snapshot of every entity's property values when it is first tracked by a DbContext instance.</span></span> <span data-ttu-id="55821-112">Les valeurs stockées dans cet instantané sont ensuite comparées aux valeurs actuelles de l’entité afin de déterminer les valeurs de propriété qui ont changé.</span><span class="sxs-lookup"><span data-stu-id="55821-112">The values stored in this snapshot are then compared against the current values of the entity in order to determine which property values have changed.</span></span>

<span data-ttu-id="55821-113">Cette détection des modifications se produit lorsque SaveChanges est appelé pour s’assurer que toutes les valeurs modifiées sont détectées avant d’envoyer des mises à jour à la base de données.</span><span class="sxs-lookup"><span data-stu-id="55821-113">This detection of changes happens when SaveChanges is called to ensure all changed values are detected before sending updates to the database.</span></span> <span data-ttu-id="55821-114">Toutefois, la détection des modifications intervient également à d’autres moments pour s’assurer que l’application fonctionne avec des informations de suivi à jour.</span><span class="sxs-lookup"><span data-stu-id="55821-114">However, the detection of changes also happens at other times to ensure the application is working with up-to-date tracking information.</span></span> <span data-ttu-id="55821-115">La détection des modifications peut être forcée à tout moment en appelant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="55821-115">Detection of changes can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="when-change-detection-is-needed"></a><span data-ttu-id="55821-116">Quand la détection des modifications est nécessaire</span><span class="sxs-lookup"><span data-stu-id="55821-116">When change detection is needed</span></span>

<span data-ttu-id="55821-117">La détection des modifications est nécessaire quand une propriété ou une navigation a été modifiée _sans utiliser EF Core pour effectuer cette modification_.</span><span class="sxs-lookup"><span data-stu-id="55821-117">Detection of changes is needed when a property or navigation has been changed _without using EF Core to make this change_.</span></span> <span data-ttu-id="55821-118">Par exemple, envisagez de charger des blogs et des billets, puis d’apporter des modifications à ces entités :</span><span class="sxs-lookup"><span data-stu-id="55821-118">For example, consider loading blogs and posts and then making changes to these entities:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

<span data-ttu-id="55821-119">Si vous examinez l' [affichage de débogage du dispositif de suivi](xref:core/change-tracking/debug-views) des modifications avant d’appeler, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> cela indique que les modifications apportées n’ont pas été détectées et ne sont donc pas reflétées dans les États d’entité et les données de propriété modifiées</span><span class="sxs-lookup"><span data-stu-id="55821-119">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> shows that the changes made have not been detected and hence are not reflected in the entity states and modified property data:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="55821-120">Plus précisément, l’état de l’entrée de blog est toujours `Unchanged` , et la nouvelle publication n’apparaît pas en tant qu’entité suivie.</span><span class="sxs-lookup"><span data-stu-id="55821-120">Specifically, the state of the blog entry is still `Unchanged`, and the new post does not appear as a tracked entity.</span></span> <span data-ttu-id="55821-121">(Le astucieux notera que les propriétés signalent leurs nouvelles valeurs, même si ces modifications n’ont pas encore été détectées par EF Core.</span><span class="sxs-lookup"><span data-stu-id="55821-121">(The astute will notice properties report their new values, even though these changes have not yet been detected by EF Core.</span></span> <span data-ttu-id="55821-122">Cela est dû au fait que la vue de débogage lit les valeurs actuelles directement à partir de l’instance d’entité.)</span><span class="sxs-lookup"><span data-stu-id="55821-122">This is because the debug view is reading current values directly from the entity instance.)</span></span>

<span data-ttu-id="55821-123">Comparez ceci avec la vue Debug après avoir appelé DetectChanges :</span><span class="sxs-lookup"><span data-stu-id="55821-123">Contrast this with the debug view after calling DetectChanges:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="55821-124">À présent, le blog est correctement marqué comme `Modified` et le nouveau billet a été détecté et suivi en tant que `Added` .</span><span class="sxs-lookup"><span data-stu-id="55821-124">Now the blog is correctly marked as `Modified` and the new post has been detected and is tracked as `Added`.</span></span>

<span data-ttu-id="55821-125">Au début de cette section, nous avons indiqué que la détection des modifications est nécessaire lors de l’utilisation _de EF Core pour effectuer la modification_.</span><span class="sxs-lookup"><span data-stu-id="55821-125">At the start of this section we stated that detecting changes is needed when not using _using EF Core to make the change_.</span></span> <span data-ttu-id="55821-126">C’est ce qui se passe dans le code ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="55821-126">This is what is happening in the code above.</span></span> <span data-ttu-id="55821-127">Autrement dit, les modifications apportées à la propriété et à la navigation sont effectuées _directement sur les instances d’entité_, et non à l’aide des méthodes de EF Core.</span><span class="sxs-lookup"><span data-stu-id="55821-127">That is, the changes to the property and navigation are made _directly on the entity instances_, and not by using any EF Core methods.</span></span>

<span data-ttu-id="55821-128">Comparez ceci au code suivant qui modifie les entités de la même façon, mais cette fois à l’aide de EF Core méthodes :</span><span class="sxs-lookup"><span data-stu-id="55821-128">Contrast this to the following code which modifies the entities in the same way, but this time using EF Core methods:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

<span data-ttu-id="55821-129">Dans ce cas, la vue de débogage change tracker indique que tous les États d’entité et les modifications de propriété sont connus, même si la détection de modifications n’a pas été effectuée.</span><span class="sxs-lookup"><span data-stu-id="55821-129">In this case the change tracker debug view shows that all entity states and property modifications are known, even though detection of changes has not happened.</span></span> <span data-ttu-id="55821-130"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType>En effet, est une méthode EF Core, ce qui signifie que EF Core connaît immédiatement la modification apportée par cette méthode.</span><span class="sxs-lookup"><span data-stu-id="55821-130">This is because <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> is an EF Core method, which means that EF Core immediately knows about the change made by this method.</span></span> <span data-ttu-id="55821-131">De même, <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> l’appel de permet EF Core de connaître immédiatement la nouvelle entité et de la suivre de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="55821-131">Likewise, calling <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> allows EF Core to immediately know about the new entity and track it appropriately.</span></span>

> [!TIP]
> <span data-ttu-id="55821-132">N’essayez pas de détecter les modifications en utilisant toujours EF Core méthodes pour apporter des modifications d’entité.</span><span class="sxs-lookup"><span data-stu-id="55821-132">Don't attempt to avoid detecting changes by always using EF Core methods to make entity changes.</span></span> <span data-ttu-id="55821-133">Cela est souvent plus fastidieux et fonctionne moins bien que d’apporter des modifications aux entités de manière normale.</span><span class="sxs-lookup"><span data-stu-id="55821-133">Doing so is often more cumbersome and performs less well than making changes to entities in the normal way.</span></span> <span data-ttu-id="55821-134">Ce document a pour but de vous informer de la détection des modifications et du moment où elles sont nécessaires.</span><span class="sxs-lookup"><span data-stu-id="55821-134">The intention of this document is to inform as to when detecting changes is needed and when it is not.</span></span> <span data-ttu-id="55821-135">L’objectif n’est pas de favoriser l’évitement de la détection des modifications.</span><span class="sxs-lookup"><span data-stu-id="55821-135">The intention is not to encourage avoidance of change detection.</span></span>

### <a name="methods-that-automatically-detect-changes"></a><span data-ttu-id="55821-136">Méthodes qui détectent automatiquement les modifications</span><span class="sxs-lookup"><span data-stu-id="55821-136">Methods that automatically detect changes</span></span>

<span data-ttu-id="55821-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> est appelé automatiquement par les méthodes dans lesquelles cela risque d’avoir un impact sur les résultats.</span><span class="sxs-lookup"><span data-stu-id="55821-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is called automatically by methods where doing so is likely to impact the results.</span></span> <span data-ttu-id="55821-138">Ces méthodes sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="55821-138">These methods are:</span></span>

- <span data-ttu-id="55821-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType> , pour vous assurer que toutes les modifications sont détectées avant de mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="55821-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType>, to ensure that all changes are detected before updating the database.</span></span>
- <span data-ttu-id="55821-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> , pour garantir que les États d’entité et les propriétés modifiées sont à jour.</span><span class="sxs-lookup"><span data-stu-id="55821-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType>, to ensure entity states and modified properties are up-to-date.</span></span>
- <span data-ttu-id="55821-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, pour s’assurer que le résultat est exact.</span><span class="sxs-lookup"><span data-stu-id="55821-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, to ensure that the result is accurate.</span></span>
- <span data-ttu-id="55821-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, pour garantir des États d’entité corrects pour les entités principal/parent avant le en cascade.</span><span class="sxs-lookup"><span data-stu-id="55821-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, to ensure correct entity states for principal/parent entities before cascading.</span></span>
- <span data-ttu-id="55821-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>pour vous assurer que le graphique suivi est à jour.</span><span class="sxs-lookup"><span data-stu-id="55821-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, to ensure that the tracked graph is up-to-date.</span></span>

<span data-ttu-id="55821-144">Il existe également certains emplacements où la détection des modifications se produit sur une seule instance d’entité, plutôt que sur l’ensemble du graphique des entités suivies.</span><span class="sxs-lookup"><span data-stu-id="55821-144">There are also some places where detection of changes happens on only a single entity instance, rather than on the entire graph of tracked entities.</span></span> <span data-ttu-id="55821-145">Ces emplacements sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="55821-145">These places are:</span></span>

- <span data-ttu-id="55821-146">Lorsque <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> vous utilisez, pour vous assurer que l’état de l’entité et les propriétés modifiées sont à jour.</span><span class="sxs-lookup"><span data-stu-id="55821-146">When using <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>, to ensure that the entity's state and modified properties are up-to-date.</span></span>
- <span data-ttu-id="55821-147">Lorsque <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> vous utilisez des méthodes telles que `Property` , `Collection` `Reference` ou `Member` pour vous assurer que les modifications de propriété, les valeurs actuelles, etc. sont à jour.</span><span class="sxs-lookup"><span data-stu-id="55821-147">When using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> methods such as `Property`, `Collection`, `Reference` or `Member` to ensure property modifications, current values, etc. are up-to-date.</span></span>
- <span data-ttu-id="55821-148">Lorsqu’une entité dépendante/enfant va être supprimée parce qu’une relation requise a été interrompue.</span><span class="sxs-lookup"><span data-stu-id="55821-148">When a dependent/child entity is going to be deleted because a required relationship has been severed.</span></span> <span data-ttu-id="55821-149">Cela détecte quand une entité ne doit pas être supprimée car elle a été reapparentée.</span><span class="sxs-lookup"><span data-stu-id="55821-149">This detects when an entity should not be deleted because it has been re-parented.</span></span>

<span data-ttu-id="55821-150">La détection locale des modifications pour une seule entité peut être déclenchée explicitement en appelant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="55821-150">Local detection of changes for a single entity can be triggered explicitly by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType>.</span></span>

> [!NOTE]
> <span data-ttu-id="55821-151">Les modifications de détection locale peuvent manquer certaines modifications qu’une détection complète trouverait.</span><span class="sxs-lookup"><span data-stu-id="55821-151">Local detect changes can miss some changes that a full detection would find.</span></span> <span data-ttu-id="55821-152">Cela se produit lorsque des actions en cascade résultant de modifications non détectées d’autres entités ont un impact sur l’entité en question.</span><span class="sxs-lookup"><span data-stu-id="55821-152">This happens when cascading actions resulting from undetected changes to other entities have an impact on the entity in question.</span></span> <span data-ttu-id="55821-153">Dans ce cas, l’application peut avoir besoin de forcer une analyse complète de toutes les entités en appelant explicitement <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="55821-153">In such situations the application may need to force a full scan of all entities by explicitly calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="disabling-automatic-change-detection"></a><span data-ttu-id="55821-154">Désactivation de la détection automatique des modifications</span><span class="sxs-lookup"><span data-stu-id="55821-154">Disabling automatic change detection</span></span>

<span data-ttu-id="55821-155">Les performances de la détection des modifications ne sont pas un goulet d’étranglement pour la plupart des applications.</span><span class="sxs-lookup"><span data-stu-id="55821-155">The performance of detecting changes is not a bottleneck for most applications.</span></span> <span data-ttu-id="55821-156">Toutefois, la détection des modifications peut devenir un problème de performances pour certaines applications qui effectuent le suivi de milliers d’entités.</span><span class="sxs-lookup"><span data-stu-id="55821-156">However, detecting changes can become a performance problem for some applications that track thousands of entities.</span></span> <span data-ttu-id="55821-157">(Le nombre exact dépend de nombreux éléments, tels que le nombre de propriétés de l’entité.) Pour cette raison, la détection automatique des modifications peut être désactivée à l’aide de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="55821-157">(The exact number will dependent on many things, such as the number of properties in the entity.) For this reason the automatic detection of changes can be disabled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType>.</span></span> <span data-ttu-id="55821-158">Par exemple, envisagez de traiter les entités de jointure dans une relation plusieurs-à-plusieurs avec des charges utiles :</span><span class="sxs-lookup"><span data-stu-id="55821-158">For example, consider processing join entities in a many-to-many relationship with payloads:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

<span data-ttu-id="55821-159">Comme nous le savons dans la section précédente, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> détectent automatiquement les modifications.</span><span class="sxs-lookup"><span data-stu-id="55821-159">As we know from the previous section, both <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> automatically detect changes.</span></span> <span data-ttu-id="55821-160">Toutefois, après avoir appelé des entrées, le code n’effectue pas de modification de l’état d’une entité ou d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="55821-160">However, after calling Entries, the code does not then make any entity or property state changes.</span></span> <span data-ttu-id="55821-161">(La définition de valeurs de propriétés normales sur des entités ajoutées n’entraîne pas de modification de l’État.) Par conséquent, le code désactive la détection automatique des modifications inutile lors de l’appel de la méthode de base SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="55821-161">(Setting normal property values on Added entities does not cause any state changes.) The code therefore disables unnecessary automatic change detection when calling down into the base SaveChanges method.</span></span> <span data-ttu-id="55821-162">Le code utilise également un bloc try/finally pour s’assurer que le paramètre par défaut est restauré même si SaveChanges échoue.</span><span class="sxs-lookup"><span data-stu-id="55821-162">The code also makes use of a try/finally block to ensure that the default setting is restored even if SaveChanges fails.</span></span>

> [!TIP]
> <span data-ttu-id="55821-163">Ne partez pas du principe que votre code doit désactiver la détection automatique des modifications à pour s’exécuter correctement.</span><span class="sxs-lookup"><span data-stu-id="55821-163">Do not assume that your code must disable automatic change detection to to perform well.</span></span> <span data-ttu-id="55821-164">Cela est nécessaire uniquement pour le profilage d’une application qui effectue le suivi de nombreuses entités indique que les performances de la détection des modifications sont un problème.</span><span class="sxs-lookup"><span data-stu-id="55821-164">This is only needed when profiling an application tracking many entities indicates that performance of change detection is an issue.</span></span>

### <a name="detecting-changes-and-value-conversions"></a><span data-ttu-id="55821-165">Détection des modifications et des conversions de valeurs</span><span class="sxs-lookup"><span data-stu-id="55821-165">Detecting changes and value conversions</span></span>

<span data-ttu-id="55821-166">Pour utiliser le suivi des modifications d’instantané avec un type d’entité, EF Core devez être en mesure d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="55821-166">To use snapshot change tracking with an entity type, EF Core must be able to:</span></span>

- <span data-ttu-id="55821-167">Créer un instantané de chaque valeur de propriété lorsque l’entité est suivie</span><span class="sxs-lookup"><span data-stu-id="55821-167">Make a snapshot of each property value when the entity is tracked</span></span>
- <span data-ttu-id="55821-168">Comparez cette valeur à la valeur actuelle de la propriété.</span><span class="sxs-lookup"><span data-stu-id="55821-168">Compare this value to the current value of the property</span></span>
- <span data-ttu-id="55821-169">Générer un code de hachage pour la valeur</span><span class="sxs-lookup"><span data-stu-id="55821-169">Generate a hash code for the value</span></span>

<span data-ttu-id="55821-170">Cela est géré automatiquement par EF Core pour les types qui peuvent être mappés directement à la base de données.</span><span class="sxs-lookup"><span data-stu-id="55821-170">This is handled automatically by EF Core for types that can be directly mapped to the database.</span></span> <span data-ttu-id="55821-171">Toutefois, lorsqu’un [convertisseur de valeurs est utilisé pour mapper une propriété](xref:core/modeling/value-conversions), ce convertisseur doit spécifier comment effectuer ces actions.</span><span class="sxs-lookup"><span data-stu-id="55821-171">However, when a [value converter is used to map a property](xref:core/modeling/value-conversions), then that converter must specify how to perform these actions.</span></span> <span data-ttu-id="55821-172">Cela est accompli avec un comparateur de valeur et est décrit en détail dans la documentation sur les [comparateurs de valeurs](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="55821-172">This is achieved with a value comparer, and is described in detail in the [Value Comparers](xref:core/modeling/value-comparers) documentation.</span></span>

## <a name="notification-entities"></a><span data-ttu-id="55821-173">Entités de notification</span><span class="sxs-lookup"><span data-stu-id="55821-173">Notification entities</span></span>

<span data-ttu-id="55821-174">Le suivi des modifications d’instantané est recommandé pour la plupart des applications.</span><span class="sxs-lookup"><span data-stu-id="55821-174">Snapshot change tracking is recommended for most applications.</span></span> <span data-ttu-id="55821-175">Toutefois, les applications qui effectuent le suivi de nombreuses entités et/ou apportent de nombreuses modifications à ces entités peuvent tirer parti de l’implémentation d’entités qui notifient automatiquement EF Core lorsque leurs valeurs de propriété et de navigation changent.</span><span class="sxs-lookup"><span data-stu-id="55821-175">However, applications that track many entities and/or make many changes to those entities may benefit from implementing entities that automatically notify EF Core when their property and navigation values change.</span></span> <span data-ttu-id="55821-176">Ils sont appelés « entités de notification ».</span><span class="sxs-lookup"><span data-stu-id="55821-176">These are known as "notification entities".</span></span>

### <a name="implementing-notification-entities"></a><span data-ttu-id="55821-177">Implémentation des entités de notification</span><span class="sxs-lookup"><span data-stu-id="55821-177">Implementing notification entities</span></span>

<span data-ttu-id="55821-178">Les entités de notification utilisent les <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> interfaces et, qui font partie de la bibliothèque de classes de base .net (BCL).</span><span class="sxs-lookup"><span data-stu-id="55821-178">Notification entities make use of the <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged> interfaces, which are part of the .NET base class library (BCL).</span></span> <span data-ttu-id="55821-179">Ces interfaces définissent les événements qui doivent être déclenchés avant et après la modification d’une valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="55821-179">These interfaces define events that must be fired before and after changing a property value.</span></span> <span data-ttu-id="55821-180">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="55821-180">For example:</span></span>

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

<span data-ttu-id="55821-181">En outre, les navigations dans les collections doivent implémenter `INotifyCollectionChanged` ; dans l’exemple ci-dessus, cela satisfait à l’aide d’un <xref:System.Collections.ObjectModel.ObservableCollection%601> de publications.</span><span class="sxs-lookup"><span data-stu-id="55821-181">In addition, any collection navigations must implement `INotifyCollectionChanged`; in the example above this satisfied by using an <xref:System.Collections.ObjectModel.ObservableCollection%601> of posts.</span></span> <span data-ttu-id="55821-182">EF Core est également fourni avec une <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implémentation qui offre des recherches plus efficaces au détriment du classement stable.</span><span class="sxs-lookup"><span data-stu-id="55821-182">EF Core also ships with an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implementation that has more efficient lookups at the expense of stable ordering.</span></span>

<span data-ttu-id="55821-183">La majeure partie de ce code de notification est généralement déplacée dans une classe de base non mappée.</span><span class="sxs-lookup"><span data-stu-id="55821-183">Most of this notification code is typically moved into an unmapped base class.</span></span> <span data-ttu-id="55821-184">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="55821-184">For example:</span></span>

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a><span data-ttu-id="55821-185">Configuration des entités de notification</span><span class="sxs-lookup"><span data-stu-id="55821-185">Configuring notification entities</span></span>

<span data-ttu-id="55821-186">Il n’existe aucun moyen pour EF Core de valider que `INotifyPropertyChanging` ou `INotifyPropertyChanged` sont entièrement implémentés pour une utilisation avec EF Core.</span><span class="sxs-lookup"><span data-stu-id="55821-186">There is no way for EF Core to validate that `INotifyPropertyChanging` or `INotifyPropertyChanged` are fully implemented for use with EF Core.</span></span> <span data-ttu-id="55821-187">En particulier, certaines utilisations de ces interfaces le font avec des notifications uniquement sur certaines propriétés, plutôt que sur toutes les propriétés (y compris les navigations) requises par EF Core.</span><span class="sxs-lookup"><span data-stu-id="55821-187">In particular, some uses of these interfaces do so with notifications only on certain properties, rather than on all properties (including navigations) as required by EF Core.</span></span> <span data-ttu-id="55821-188">C’est la raison pour laquelle EF Core ne se raccorde pas automatiquement à ces événements.</span><span class="sxs-lookup"><span data-stu-id="55821-188">For this reason, EF Core does not automatically hook into these events.</span></span>

<span data-ttu-id="55821-189">Au lieu de cela, EF Core doit être configuré pour utiliser ces entités de notification.</span><span class="sxs-lookup"><span data-stu-id="55821-189">Instead, EF Core must be configured to use these notification entities.</span></span> <span data-ttu-id="55821-190">Cette opération est généralement effectuée pour tous les types d’entité en appelant <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="55821-190">This is usually done for all entity types by calling <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="55821-191">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="55821-191">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

<span data-ttu-id="55821-192">(La stratégie peut également être définie différemment pour les différents types d’entité à l’aide de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> , mais cela est généralement productif, car DetectChanges est toujours requis pour ces types qui ne sont pas des entités de notification.)</span><span class="sxs-lookup"><span data-stu-id="55821-192">(The strategy can also be set differently for different entity types using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>, but this is usually counterproductive since DetectChanges is still required for those types that are not notification entities.)</span></span>

<span data-ttu-id="55821-193">Le suivi des modifications de notification complet requiert que `INotifyPropertyChanging` et `INotifyPropertyChanged` soient implémentés.</span><span class="sxs-lookup"><span data-stu-id="55821-193">Full notification change tracking requires that both `INotifyPropertyChanging` and `INotifyPropertyChanged` are implemented.</span></span> <span data-ttu-id="55821-194">Cela permet d’enregistrer les valeurs d’origine juste avant la modification de la valeur de propriété, ce qui évite d’avoir à EF Core créer un instantané lors du suivi de l’entité.</span><span class="sxs-lookup"><span data-stu-id="55821-194">This allows original values to be saved just before the property value is changed, avoiding the need for EF Core to create a snapshot when tracking the entity.</span></span> <span data-ttu-id="55821-195">Les types d’entités qui implémentent uniquement `INotifyPropertyChanged` peuvent également être utilisés avec EF Core.</span><span class="sxs-lookup"><span data-stu-id="55821-195">Entity types that implement only `INotifyPropertyChanged` can also be used with EF Core.</span></span> <span data-ttu-id="55821-196">Dans ce cas, EF crée toujours un instantané lors du suivi d’une entité pour effectuer le suivi des valeurs d’origine, mais utilise ensuite les notifications pour détecter les modifications immédiatement, plutôt que d’avoir à appeler DetectChanges.</span><span class="sxs-lookup"><span data-stu-id="55821-196">In this case, EF still creates a snapshot when tracking an entity to keep track of original values, but then uses the notifications to detect changes immediately, rather than needing DetectChanges to be called.</span></span>

<span data-ttu-id="55821-197">Les différentes <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> valeurs sont résumées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="55821-197">The different <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> values are summarized in the the following table.</span></span>

| <span data-ttu-id="55821-198">ChangeTrackingStrategy</span><span class="sxs-lookup"><span data-stu-id="55821-198">ChangeTrackingStrategy</span></span>                              | <span data-ttu-id="55821-199">Interfaces nécessaires</span><span class="sxs-lookup"><span data-stu-id="55821-199">Interfaces needed</span></span>                                      | <span data-ttu-id="55821-200">Nécessite DetectChanges</span><span class="sxs-lookup"><span data-stu-id="55821-200">Needs DetectChanges</span></span> | <span data-ttu-id="55821-201">Instantanés des valeurs d’origine</span><span class="sxs-lookup"><span data-stu-id="55821-201">Snapshots original values</span></span>
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| <span data-ttu-id="55821-202">Instantané</span><span class="sxs-lookup"><span data-stu-id="55821-202">Snapshot</span></span>                                            | <span data-ttu-id="55821-203">Aucun</span><span class="sxs-lookup"><span data-stu-id="55821-203">None</span></span>                                                   | <span data-ttu-id="55821-204">Oui</span><span class="sxs-lookup"><span data-stu-id="55821-204">Yes</span></span>                 | <span data-ttu-id="55821-205">Oui</span><span class="sxs-lookup"><span data-stu-id="55821-205">Yes</span></span>
| <span data-ttu-id="55821-206">ChangedNotifications</span><span class="sxs-lookup"><span data-stu-id="55821-206">ChangedNotifications</span></span>                                | <span data-ttu-id="55821-207">Interface INotifyPropertyChanged</span><span class="sxs-lookup"><span data-stu-id="55821-207">INotifyPropertyChanged</span></span>                                 | <span data-ttu-id="55821-208">Non</span><span class="sxs-lookup"><span data-stu-id="55821-208">No</span></span>                  | <span data-ttu-id="55821-209">Oui</span><span class="sxs-lookup"><span data-stu-id="55821-209">Yes</span></span>
| <span data-ttu-id="55821-210">ChangingAndChangedNotifications</span><span class="sxs-lookup"><span data-stu-id="55821-210">ChangingAndChangedNotifications</span></span>                     | <span data-ttu-id="55821-211">INotifyPropertyChanged et INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="55821-211">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="55821-212">Non</span><span class="sxs-lookup"><span data-stu-id="55821-212">No</span></span>                  | <span data-ttu-id="55821-213">Non</span><span class="sxs-lookup"><span data-stu-id="55821-213">No</span></span>
| <span data-ttu-id="55821-214">ChangingAndChangedNotificationsWithOriginalValues</span><span class="sxs-lookup"><span data-stu-id="55821-214">ChangingAndChangedNotificationsWithOriginalValues</span></span>   | <span data-ttu-id="55821-215">INotifyPropertyChanged et INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="55821-215">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="55821-216">Non</span><span class="sxs-lookup"><span data-stu-id="55821-216">No</span></span>                  | <span data-ttu-id="55821-217">Oui</span><span class="sxs-lookup"><span data-stu-id="55821-217">Yes</span></span>

### <a name="using-notification-entities"></a><span data-ttu-id="55821-218">Utilisation des entités de notification</span><span class="sxs-lookup"><span data-stu-id="55821-218">Using notification entities</span></span>

<span data-ttu-id="55821-219">Les entités de notification se comportent comme n’importe quelle autre entité, sauf que les modifications apportées aux instances d’entité ne nécessitent pas d’appel à <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> pour détecter ces modifications.</span><span class="sxs-lookup"><span data-stu-id="55821-219">Notification entities behave like any other entities, except that making changes to the entity instances do not require a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> to detect these changes.</span></span> <span data-ttu-id="55821-220">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="55821-220">For example:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

<span data-ttu-id="55821-221">Avec les entités normales, l' [affichage de débogage du suivi des modifications](xref:core/change-tracking/debug-views) a montré que ces modifications n’ont pas été détectées tant que DetectChanges n’a pas été appelé.</span><span class="sxs-lookup"><span data-stu-id="55821-221">With normal entities, the [change tracker debug view](xref:core/change-tracking/debug-views) showed that these changes were not detected until DetectChanges was called.</span></span> <span data-ttu-id="55821-222">Si vous examinez l’affichage débogage lorsque les entités de notification sont utilisées, cela indique que ces modifications ont été détectées immédiatement :</span><span class="sxs-lookup"><span data-stu-id="55821-222">Looking at the debug view when notification entities are used shows that these changes have been detected immediately:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a><span data-ttu-id="55821-223">Proxys de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="55821-223">Change-tracking proxies</span></span>

> [!NOTE]
> <span data-ttu-id="55821-224">Les proxys de suivi des modifications ont été introduits dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="55821-224">Change-tracking proxies were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="55821-225">EF Core pouvez générer de manière dynamique des types de proxy qui implémentent <xref:System.ComponentModel.INotifyPropertyChanging> et <xref:System.ComponentModel.INotifyPropertyChanged> .</span><span class="sxs-lookup"><span data-stu-id="55821-225">EF Core can dynamically generate proxy types that implement <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged>.</span></span> <span data-ttu-id="55821-226">Pour cela, vous devez installer le package NuGet [Microsoft. EntityFrameworkCore. proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) et activer les proxys de suivi des modifications avec, <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> par exemple :</span><span class="sxs-lookup"><span data-stu-id="55821-226">This requires installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package, and enabling change-tracking proxies with <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

<span data-ttu-id="55821-227">La création d’un proxy dynamique implique la création d’un nouveau type .NET dynamique (à l’aide de l’implémentation des proxies [Castle. Core](https://www.nuget.org/packages/Castle.Core/) ), qui hérite du type d’entité, puis remplace tous les accesseurs set de propriété.</span><span class="sxs-lookup"><span data-stu-id="55821-227">Creating a dynamic proxy involves creating a new, dynamic .NET type (using the [Castle.Core](https://www.nuget.org/packages/Castle.Core/) proxies implementation), which inherits from the entity type and then overrides all property setters.</span></span> <span data-ttu-id="55821-228">Par conséquent, les types d’entités pour les proxies doivent être des types qui peuvent être hérités de et doivent avoir des propriétés qui peuvent être substituées.</span><span class="sxs-lookup"><span data-stu-id="55821-228">Entity types for proxies must therefore be types that can be inherited from and must have properties that can be overridden.</span></span> <span data-ttu-id="55821-229">En outre, les navigations de collection créées explicitement doivent implémenter l' <xref:System.Collections.Specialized.INotifyCollectionChanged> exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="55821-229">Also, collection navigations created explicitly must implement <xref:System.Collections.Specialized.INotifyCollectionChanged> For example:</span></span>

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

<span data-ttu-id="55821-230">L’un des inconvénients majeurs des proxys de suivi des modifications est que EF Core devez toujours suivre les instances du proxy, et jamais les instances du type d’entité sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="55821-230">One significant downside to change-tracking proxies is that EF Core must always track instances of the proxy, never instances of the underlying entity type.</span></span> <span data-ttu-id="55821-231">Cela est dû au fait que les instances du type d’entité sous-jacent ne génèrent pas de notifications, ce qui signifie que les modifications apportées à ces entités ne seront pas exécutées.</span><span class="sxs-lookup"><span data-stu-id="55821-231">This is because instances of the underlying entity type will not generate notifications, which means changes made to these entities will be missed.</span></span>

<span data-ttu-id="55821-232">EF Core crée automatiquement des instances de proxy lors de l’interrogation de la base de données, cet inconvénient est généralement limité au suivi de nouvelles instances d’entité.</span><span class="sxs-lookup"><span data-stu-id="55821-232">EF Core creates proxy instances automatically when querying the database, so this downside is generally limited to tracking new entity instances.</span></span> <span data-ttu-id="55821-233">Ces instances doivent être créées à l’aide des <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> méthodes d’extension, et **pas** normalement à l’aide de `new` .</span><span class="sxs-lookup"><span data-stu-id="55821-233">These instances must be created using the <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> extension methods, and **not** in the normal way using `new`.</span></span> <span data-ttu-id="55821-234">Cela signifie que le code des exemples précédents doit maintenant utiliser `CreateProxy` :</span><span class="sxs-lookup"><span data-stu-id="55821-234">This means the code from the previous examples must now make use of `CreateProxy`:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a><span data-ttu-id="55821-235">Événements de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="55821-235">Change tracking events</span></span>

<span data-ttu-id="55821-236">EF Core déclenche l' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> événement lorsqu’une entité est suivie pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="55821-236">EF Core fires the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> event when an entity is tracked for the first time.</span></span> <span data-ttu-id="55821-237">Les futures modifications de l’état de l’entité entraînent des <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> événements.</span><span class="sxs-lookup"><span data-stu-id="55821-237">Future entity state changes result in <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> events.</span></span> <span data-ttu-id="55821-238">Pour plus d’informations, consultez [événements .net dans EF Core](xref:core/logging-events-diagnostics/events) .</span><span class="sxs-lookup"><span data-stu-id="55821-238">See [.NET Events in EF Core](xref:core/logging-events-diagnostics/events) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="55821-239">L' `StateChanged` événement n’est pas déclenché lorsqu’une entité est suivie pour la première fois, même si l’État est passé de `Detached` à l’un des autres États.</span><span class="sxs-lookup"><span data-stu-id="55821-239">The `StateChanged` event is not fired when an entity is first tracked, even though the state has changed from `Detached` to one of the other states.</span></span> <span data-ttu-id="55821-240">Veillez à écouter les `StateChanged` événements et `Tracked` pour obtenir toutes les notifications pertinentes.</span><span class="sxs-lookup"><span data-stu-id="55821-240">Make sure to listen for both `StateChanged` and `Tracked` events to get all relevant notifications.</span></span>
