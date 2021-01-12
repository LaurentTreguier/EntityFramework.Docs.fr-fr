---
title: Débogage du dispositif de suivi des modifications-EF Core
description: Utilisation du DebugView ChangeTracker et des messages du journal pour déboguer le suivi des modifications EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/debug-views
ms.openlocfilehash: 76108120cf7f532d085fef12121bb639b065add0
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129729"
---
# <a name="change-tracker-debugging"></a><span data-ttu-id="bf01c-103">Débogage du dispositif de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="bf01c-103">Change Tracker Debugging</span></span>

<span data-ttu-id="bf01c-104">Le dispositif de suivi des modifications de Entity Framework Core (EF Core) génère deux types de sortie pour faciliter le débogage :</span><span class="sxs-lookup"><span data-stu-id="bf01c-104">The Entity Framework Core (EF Core) change tracker generates two kinds of output to help with debugging:</span></span>

- <span data-ttu-id="bf01c-105">Le <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> fournit une vue explicite de toutes les entités faisant l’objet d’un suivi.</span><span class="sxs-lookup"><span data-stu-id="bf01c-105">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> provides a human-readable view of all entities being tracked</span></span>
- <span data-ttu-id="bf01c-106">Les messages de journal au niveau du débogage sont générés lorsque le dispositif de suivi des modifications détecte l’État et corrige les relations</span><span class="sxs-lookup"><span data-stu-id="bf01c-106">Debug-level log messages are generated when the change tracker detects state and fixes up relationships</span></span>

> [!TIP]
> <span data-ttu-id="bf01c-107">Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris.</span><span class="sxs-lookup"><span data-stu-id="bf01c-107">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="bf01c-108">Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="bf01c-108">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="bf01c-109">Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackerDebugging).</span><span class="sxs-lookup"><span data-stu-id="bf01c-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackerDebugging).</span></span>

## <a name="change-tracker-debug-view"></a><span data-ttu-id="bf01c-110">Modifier l’affichage de débogage du dispositif de suivi</span><span class="sxs-lookup"><span data-stu-id="bf01c-110">Change tracker debug view</span></span>

<span data-ttu-id="bf01c-111">Vous pouvez accéder à la vue de débogage du dispositif de suivi des modifications dans le débogueur de votre IDE.</span><span class="sxs-lookup"><span data-stu-id="bf01c-111">The change tracker debug view can be accessed in the debugger of your IDE.</span></span> <span data-ttu-id="bf01c-112">Par exemple, avec Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="bf01c-112">For example, with Visual Studio:</span></span>

![Accès à la vue de débogage du dispositif de suivi des modifications à partir du débogueur Visual Studio](_static/debug-view.png)

<span data-ttu-id="bf01c-114">Elle est également accessible directement à partir du code, par exemple pour envoyer la vue de débogage à la console :</span><span class="sxs-lookup"><span data-stu-id="bf01c-114">It can also be accessed directly from code, for example to send the debug view to the console:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.ShortView);
-->
[!code-csharp[Change_tracker_debug_view_1b](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1b)]

<span data-ttu-id="bf01c-115">L’affichage débogage a une forme abrégée et une forme longue.</span><span class="sxs-lookup"><span data-stu-id="bf01c-115">The debug view has a short form and a long form.</span></span> <span data-ttu-id="bf01c-116">La forme abrégée affiche les entités suivies, leur état et leurs valeurs de clés.</span><span class="sxs-lookup"><span data-stu-id="bf01c-116">The short form shows tracked entities, their state, and key values.</span></span> <span data-ttu-id="bf01c-117">La forme longue comprend également l’ensemble des valeurs de propriété et de navigation, ainsi que l’État.</span><span class="sxs-lookup"><span data-stu-id="bf01c-117">The long form also includes all property and navigation values and state.</span></span>

### <a name="the-short-view"></a><span data-ttu-id="bf01c-118">Vue abrégée</span><span class="sxs-lookup"><span data-stu-id="bf01c-118">The short view</span></span>

<span data-ttu-id="bf01c-119">Examinons un exemple de vue de débogage à l’aide du modèle indiqué à la fin de ce document.</span><span class="sxs-lookup"><span data-stu-id="bf01c-119">Let's look at a debug view example using the model shown at the end of this document.</span></span> <span data-ttu-id="bf01c-120">Tout d’abord, nous allons suivre certaines entités et les placer dans des États différents, de sorte que nous disposons de bonnes données de suivi des modifications pour afficher :</span><span class="sxs-lookup"><span data-stu-id="bf01c-120">First, we will track some entities and put them in some different states, just so we have good change tracking data to view:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts).ThenInclude(e => e.Tags)
            .Include(e => e.Assets)
            .ToList();

        // Mark something Added
        blogs[0].Posts.Add(
            new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many new features and..."
            });

        // Mark something Deleted
        blogs[1].Posts.Remove(blogs[1].Posts[1]);

        // Make something Modified
        blogs[0].Name = ".NET Blog (All new!)";

        context.ChangeTracker.DetectChanges();
-->
[!code-csharp[Change_tracker_debug_view_1a](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1a)]

<span data-ttu-id="bf01c-121">L’impression de la vue brève à ce stade, comme indiqué ci-dessus, entraîne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="bf01c-121">Printing the short view at this point, as shown above, results in the following output:</span></span>

```output
Blog {Id: 1} Modified AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}
Blog {Id: 2} Unchanged AK {AssetsId: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged FK {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged FK {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
Post {Id: -2147482643} Added FK {BlogId: 1}
Post {Id: 1} Unchanged FK {BlogId: 1}
Post {Id: 2} Unchanged FK {BlogId: 1}
Post {Id: 3} Unchanged FK {BlogId: 2}
Post {Id: 4} Deleted FK {BlogId: 2}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged FK {PostsId: 1} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged FK {PostsId: 1} FK {TagsId: 3}
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged FK {PostsId: 2} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged FK {PostsId: 3} FK {TagsId: 2}
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted FK {PostsId: 4} FK {TagsId: 2}
Tag {Id: 1} Unchanged
Tag {Id: 2} Unchanged
Tag {Id: 3} Unchanged
```

<span data-ttu-id="bf01c-122">Remarques :</span><span class="sxs-lookup"><span data-stu-id="bf01c-122">Notice:</span></span>

- <span data-ttu-id="bf01c-123">Chaque entité suivie est listée avec sa valeur de clé primaire (PK).</span><span class="sxs-lookup"><span data-stu-id="bf01c-123">Each tracked entity is listed with its primary key (PK) value.</span></span> <span data-ttu-id="bf01c-124">Par exemple : `Blog {Id: 1}`.</span><span class="sxs-lookup"><span data-stu-id="bf01c-124">For example, `Blog {Id: 1}`.</span></span>
- <span data-ttu-id="bf01c-125">Si l’entité est un type d’entité de type partagé, son type CLR est également indiqué.</span><span class="sxs-lookup"><span data-stu-id="bf01c-125">If the entity is a shared-type entity type, then it's CLR type is also shown.</span></span> <span data-ttu-id="bf01c-126">Par exemple : `PostTag (Dictionary<string, object>)`.</span><span class="sxs-lookup"><span data-stu-id="bf01c-126">For example, `PostTag (Dictionary<string, object>)`.</span></span>
- <span data-ttu-id="bf01c-127">Le <xref:Microsoft.EntityFrameworkCore.EntityState> s’affiche ensuite.</span><span class="sxs-lookup"><span data-stu-id="bf01c-127">The <xref:Microsoft.EntityFrameworkCore.EntityState> is shown next.</span></span> <span data-ttu-id="bf01c-128">Il peut s’agir de `Unchanged` , `Added` , `Modified` ou `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="bf01c-128">This will be one of `Unchanged`, `Added`, `Modified`, or `Deleted`.</span></span>
- <span data-ttu-id="bf01c-129">Les valeurs des autres clés (AKs) sont affichées ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="bf01c-129">Values for any alternate keys (AKs) are shown next.</span></span> <span data-ttu-id="bf01c-130">Par exemple : `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.</span><span class="sxs-lookup"><span data-stu-id="bf01c-130">For example, `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.</span></span>
- <span data-ttu-id="bf01c-131">Enfin, les valeurs de n’importe quelle clé étrangère (clés étrangères) sont affichées.</span><span class="sxs-lookup"><span data-stu-id="bf01c-131">Finally, values for any foreign keys (FKs) are shown.</span></span> <span data-ttu-id="bf01c-132">Par exemple : `FK {PostsId: 4} FK {TagsId: 2}`.</span><span class="sxs-lookup"><span data-stu-id="bf01c-132">For example, `FK {PostsId: 4} FK {TagsId: 2}`.</span></span>

### <a name="the-long-view"></a><span data-ttu-id="bf01c-133">Vue longue</span><span class="sxs-lookup"><span data-stu-id="bf01c-133">The long view</span></span>

<span data-ttu-id="bf01c-134">La vue longue peut être envoyée à la console de la même façon que la vue abrégée :</span><span class="sxs-lookup"><span data-stu-id="bf01c-134">The long view can be sent to the console in the same way as the short view:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracker_debug_view_1c](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1c)]

<span data-ttu-id="bf01c-135">La sortie pour le même État que le petit affichage ci-dessus est la suivante :</span><span class="sxs-lookup"><span data-stu-id="bf01c-135">The output for the same state as the short view above is:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK
  Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'
  Assets: {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  AssetsId: '3a54b880-2b9d-486b-9403-dc2e52d36d65' AK
  Name: 'Visual Studio Blog'
  Assets: {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
  Posts: [{Id: 3}]
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged
  Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK
  Banner: <null>
  Blog: {Id: 2}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged
  Id: 'ed727978-1ffe-4709-baee-73913e8e44a0' PK FK
  Banner: <null>
  Blog: {Id: 1}
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many new fe...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
  Tags: []
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: [{Id: 1}, {Id: 3}]
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: [{Id: 1}]
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: [{Id: 2}]
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: [{Id: 2}]
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged
  PostsId: 1 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged
  PostsId: 1 PK FK
  TagsId: 3 PK FK
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged
  PostsId: 2 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged
  PostsId: 3 PK FK
  TagsId: 2 PK FK
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted
  PostsId: 4 PK FK
  TagsId: 2 PK FK
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 1}, {Id: 2}]
Tag {Id: 2} Unchanged
  Id: 2 PK
  Text: 'Visual Studio'
  Posts: [{Id: 3}, {Id: 4}]
Tag {Id: 3} Unchanged
  Id: 3 PK
  Text: 'EF Core'
  Posts: [{Id: 1}]
```

<span data-ttu-id="bf01c-136">Chaque entité suivie et son état s’affichent comme avant.</span><span class="sxs-lookup"><span data-stu-id="bf01c-136">Each tracked entity and its state is shown as before.</span></span> <span data-ttu-id="bf01c-137">Toutefois, l’affichage long affiche également les valeurs de propriété et de navigation.</span><span class="sxs-lookup"><span data-stu-id="bf01c-137">However, the long view also shows property and navigation values.</span></span>

#### <a name="property-values"></a><span data-ttu-id="bf01c-138">Valeurs de propriétés</span><span class="sxs-lookup"><span data-stu-id="bf01c-138">Property values</span></span>

<span data-ttu-id="bf01c-139">Pour chaque propriété, l’affichage long indique si la propriété fait partie d’une clé primaire (PK), d’une clé secondaire (AK) ou d’une clé étrangère (FK).</span><span class="sxs-lookup"><span data-stu-id="bf01c-139">For each property, the long view shows whether or not the property is part of a primary key (PK), alternate key (AK), or foreign key (FK).</span></span> <span data-ttu-id="bf01c-140">Exemple :</span><span class="sxs-lookup"><span data-stu-id="bf01c-140">For example:</span></span>

- <span data-ttu-id="bf01c-141">`Blog.Id` est une propriété de clé primaire : `Id: 1 PK`</span><span class="sxs-lookup"><span data-stu-id="bf01c-141">`Blog.Id` is a primary key property: `Id: 1 PK`</span></span>
- <span data-ttu-id="bf01c-142">`Blog.AssetsId` est une propriété de clé secondaire : `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span><span class="sxs-lookup"><span data-stu-id="bf01c-142">`Blog.AssetsId` is an alternate key property: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span></span>
- <span data-ttu-id="bf01c-143">`Post.BlogId` est une propriété de clé étrangère : `BlogId: 2 FK`</span><span class="sxs-lookup"><span data-stu-id="bf01c-143">`Post.BlogId` is a foreign key property: `BlogId: 2 FK`</span></span>
- <span data-ttu-id="bf01c-144">`BlogAssets.Id` est une clé primaire et une propriété de clé étrangère : `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span><span class="sxs-lookup"><span data-stu-id="bf01c-144">`BlogAssets.Id` is both a primary key and a foreign key property: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span></span>

<span data-ttu-id="bf01c-145">Les valeurs de propriété qui ont été modifiées sont marquées comme telles, et la valeur d’origine de la propriété est également indiquée.</span><span class="sxs-lookup"><span data-stu-id="bf01c-145">Property values that have been modified are marked as such, and the original value of the property is also shown.</span></span> <span data-ttu-id="bf01c-146">Par exemple : `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.</span><span class="sxs-lookup"><span data-stu-id="bf01c-146">For example, `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.</span></span>

<span data-ttu-id="bf01c-147">Enfin, `Added` les entités avec des valeurs de clé temporaires indiquent que la valeur est temporaire.</span><span class="sxs-lookup"><span data-stu-id="bf01c-147">Finally, `Added` entities with temporary key values indicate that the value is temporary.</span></span> <span data-ttu-id="bf01c-148">Par exemple : `Id: -2147482643 PK Temporary`.</span><span class="sxs-lookup"><span data-stu-id="bf01c-148">For example, `Id: -2147482643 PK Temporary`.</span></span>

#### <a name="navigation-values"></a><span data-ttu-id="bf01c-149">Valeurs de navigation</span><span class="sxs-lookup"><span data-stu-id="bf01c-149">Navigation values</span></span>

<span data-ttu-id="bf01c-150">Les valeurs de navigation sont affichées à l’aide des valeurs de clé primaire des entités référencées par les navigations.</span><span class="sxs-lookup"><span data-stu-id="bf01c-150">Navigation values are displayed using the primary key values of the entities that the navigations reference.</span></span> <span data-ttu-id="bf01c-151">Par exemple, dans la sortie ci-dessus, le billet 3 est lié au blog 2.</span><span class="sxs-lookup"><span data-stu-id="bf01c-151">For example, in the output above, post 3 is related to blog 2.</span></span> <span data-ttu-id="bf01c-152">Cela signifie que la `Post.Blog` navigation pointe vers l' `Blog` instance avec l’ID 2.</span><span class="sxs-lookup"><span data-stu-id="bf01c-152">This means that the `Post.Blog` navigation points to the `Blog` instance with ID 2.</span></span> <span data-ttu-id="bf01c-153">Cela s’affiche sous la forme `Blog: {Id: 2}` .</span><span class="sxs-lookup"><span data-stu-id="bf01c-153">This is shown as `Blog: {Id: 2}`.</span></span>

<span data-ttu-id="bf01c-154">La même chose se produit pour les navigations de collection, sauf que dans ce cas, il peut y avoir plusieurs entités associées.</span><span class="sxs-lookup"><span data-stu-id="bf01c-154">The same thing happens for collection navigations, except that in this case there can be multiple related entities.</span></span> <span data-ttu-id="bf01c-155">Par exemple, la navigation dans la collection `Blog.Posts` contient trois entités, avec les valeurs de clé 1, 2 et-2147482643, respectivement.</span><span class="sxs-lookup"><span data-stu-id="bf01c-155">For example, the collection navigation `Blog.Posts` contains three entities, with key values 1, 2, and -2147482643 respectively.</span></span> <span data-ttu-id="bf01c-156">Cela s’affiche sous la forme `[{Id: 1}, {Id: 2}, {Id: -2147482643}]` .</span><span class="sxs-lookup"><span data-stu-id="bf01c-156">This is shown as `[{Id: 1}, {Id: 2}, {Id: -2147482643}]`.</span></span>

## <a name="change-tracker-logging"></a><span data-ttu-id="bf01c-157">Journalisation du suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="bf01c-157">Change tracker logging</span></span>

<span data-ttu-id="bf01c-158">Le dispositif de suivi des modifications consigne les messages à `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> chaque fois qu’il [détecte des modifications de propriété ou de navigation](xref:core/change-tracking/debug-views).</span><span class="sxs-lookup"><span data-stu-id="bf01c-158">The change tracker logs messages at the `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> whenever it [detects property or navigation changes](xref:core/change-tracking/debug-views).</span></span> <span data-ttu-id="bf01c-159">Par exemple, lorsque <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> est appelé dans le code en haut de ce document et que la [journalisation du débogage est activée](xref:core/logging-events-diagnostics/index), les journaux suivants sont générés :</span><span class="sxs-lookup"><span data-stu-id="bf01c-159">For example, when <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is called in the code at the top of this document and [debug logging is enabled](xref:core/logging-events-diagnostics/index), then the following logs are generated:</span></span>

```output
dbug: 12/30/2020 13:52:44.815 CoreEventId.DetectChangesStarting[10800] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges starting for 'BlogsContext'.
dbug: 12/30/2020 13:52:44.818 CoreEventId.PropertyChangeDetected[10802] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The unchanged property 'Blog.Name' was detected as changed from '.NET Blog' to '.NET Blog (All new!)' and will be marked as modified for entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.820 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Blog' entity with key '{Id: 1}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.821 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      1 entities were added and 0 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.ValueGenerated[10808] (Microsoft.EntityFrameworkCore.ChangeTracking)
      'BlogsContext' generated temporary value '-2147482638' for the property 'Id.Post'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.StartedTracking[10806] (Microsoft.EntityFrameworkCore.ChangeTracking)
      Context 'BlogsContext' started tracking 'Post' entity with key '{Id: -2147482638}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CascadeDeleteOrphan[10003] (Microsoft.EntityFrameworkCore.Update)
      An entity of type 'Post' with key '{Id: 4}' changed to 'Deleted' state due to severed required relationship to its parent entity of type 'Blog'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Modified' to 'Deleted'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.CascadeDelete[10002] (Microsoft.EntityFrameworkCore.Update)
      A cascade state change of an entity of type 'PostTag' with key '{PostsId: 4, TagsId: 2}' to 'Deleted' occurred due to the deletion of its parent entity of type 'Post' with key '{Id: 4}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'PostTag' entity with key '{PostsId: 4, TagsId: 2}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Deleted'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.DetectChangesCompleted[10801] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges completed for 'BlogsContext'.
```

<span data-ttu-id="bf01c-160">Le tableau suivant récapitule les messages de journalisation du dispositif de suivi des modifications :</span><span class="sxs-lookup"><span data-stu-id="bf01c-160">The following table summaries the change tracker logging messages:</span></span>

| <span data-ttu-id="bf01c-161">ID de l’événement</span><span class="sxs-lookup"><span data-stu-id="bf01c-161">Event ID</span></span>                                                                                                               | <span data-ttu-id="bf01c-162">Description</span><span class="sxs-lookup"><span data-stu-id="bf01c-162">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------------------|----
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesStarting?displayProperty=nameWithType>        | <span data-ttu-id="bf01c-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> est en cours de démarrage</span><span class="sxs-lookup"><span data-stu-id="bf01c-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is starting</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesCompleted?displayProperty=nameWithType>       | <span data-ttu-id="bf01c-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> est terminée</span><span class="sxs-lookup"><span data-stu-id="bf01c-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> has completed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.PropertyChangeDetected?displayProperty=nameWithType>       | <span data-ttu-id="bf01c-165">Une valeur de propriété normale a changé</span><span class="sxs-lookup"><span data-stu-id="bf01c-165">A normal property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ForeignKeyChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="bf01c-166">Une valeur de propriété de clé étrangère a été modifiée</span><span class="sxs-lookup"><span data-stu-id="bf01c-166">A foreign key property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.CollectionChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="bf01c-167">Des entités associées ont été ajoutées ou supprimées dans une navigation sans saut de collection.</span><span class="sxs-lookup"><span data-stu-id="bf01c-167">A non-skip collection navigation has had related entities added or removed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ReferenceChangeDetected?displayProperty=nameWithType>      | <span data-ttu-id="bf01c-168">Une navigation de référence a été modifiée pour pointer vers une autre entité, ou a la valeur null</span><span class="sxs-lookup"><span data-stu-id="bf01c-168">A reference navigation has been changed to point to another entity, or set to null</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StartedTracking?displayProperty=nameWithType>              | <span data-ttu-id="bf01c-169">EF Core démarré le suivi d’une entité</span><span class="sxs-lookup"><span data-stu-id="bf01c-169">EF Core started tracking an entity</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StateChanged?displayProperty=nameWithType>                 | <span data-ttu-id="bf01c-170">Le <xref:Microsoft.EntityFrameworkCore.EntityState> d’une entité a changé</span><span class="sxs-lookup"><span data-stu-id="bf01c-170">The <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ValueGenerated?displayProperty=nameWithType>               | <span data-ttu-id="bf01c-171">Une valeur a été générée pour une propriété</span><span class="sxs-lookup"><span data-stu-id="bf01c-171">A value was generated for a property</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.SkipCollectionChangeDetected?displayProperty=nameWithType> | <span data-ttu-id="bf01c-172">Des entités associées ont été ajoutées ou supprimées dans une navigation de collection ignorée.</span><span class="sxs-lookup"><span data-stu-id="bf01c-172">A skip collection navigation has had related entities added or removed</span></span>

## <a name="the-model"></a><span data-ttu-id="bf01c-173">Le modèle</span><span class="sxs-lookup"><span data-stu-id="bf01c-173">The model</span></span>

<span data-ttu-id="bf01c-174">Le modèle utilisé pour les exemples ci-dessus contient les types d’entités suivants :</span><span class="sxs-lookup"><span data-stu-id="bf01c-174">The model used for the examples above contains the following entity types:</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public Guid AssetsId { get; set; } // Alternate key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public Guid Id { get; set; } // Primary key and foreign key
    public byte[] Banner { get; set; }

    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation

    public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
}

public class Tag
{
    public int Id { get; set; } // Primary key
    public string Text { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Model)]

<span data-ttu-id="bf01c-175">Le modèle est principalement configuré par Convention, avec seulement quelques lignes dans OnModelCreating :</span><span class="sxs-lookup"><span data-stu-id="bf01c-175">The model is mostly configured by convention, with just a few lines in OnModelCreating:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Blog>()
            .Property(e => e.AssetsId)
            .ValueGeneratedOnAdd();

        modelBuilder
            .Entity<BlogAssets>()
            .HasOne(e => e.Blog)
            .WithOne(e => e.Assets)
            .HasForeignKey<BlogAssets>(e => e.Id)
            .HasPrincipalKey<Blog>(e => e.AssetsId);
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=OnModelCreating)]
