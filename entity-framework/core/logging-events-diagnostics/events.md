---
title: Événements .NET-EF Core
description: Événements .NET définis par EF Core
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: c26032d19d7bc05d30d4576534c7425da4472072
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024236"
---
# <a name="net-events-in-ef-core"></a><span data-ttu-id="c4786-103">Événements .NET dans EF Core</span><span class="sxs-lookup"><span data-stu-id="c4786-103">.NET Events in EF Core</span></span>

> [!TIP]
> <span data-ttu-id="c4786-104">Vous pouvez [Télécharger l’exemple Events](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) à partir de github.</span><span class="sxs-lookup"><span data-stu-id="c4786-104">You can [download the events sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) from GitHub.</span></span>

<span data-ttu-id="c4786-105">Entity Framework Core (EF Core) expose les [événements .net](/dotnet/standard/events/) pour agir en tant que rappels lorsque certains événements se produisent dans le code de EF Core.</span><span class="sxs-lookup"><span data-stu-id="c4786-105">Entity Framework Core (EF Core) exposes [.NET events](/dotnet/standard/events/) to act as callbacks when certain things happen in the EF Core code.</span></span> <span data-ttu-id="c4786-106">Les événements sont plus simples que les [intercepteurs](xref:core/logging-events-diagnostics/interceptors) et permettent une inscription plus flexible.</span><span class="sxs-lookup"><span data-stu-id="c4786-106">Events are simpler than [interceptors](xref:core/logging-events-diagnostics/interceptors) and allow more flexible registration.</span></span> <span data-ttu-id="c4786-107">Toutefois, ils sont synchronisés uniquement et ne peuvent donc pas effectuer d’e/s Async non bloquantes.</span><span class="sxs-lookup"><span data-stu-id="c4786-107">However, they are sync only and so cannot perform non-blocking async I/O.</span></span>

<span data-ttu-id="c4786-108">Les événements sont inscrits par `DbContext` instance.</span><span class="sxs-lookup"><span data-stu-id="c4786-108">Events are registered per `DbContext` instance.</span></span> <span data-ttu-id="c4786-109">Utilisez un [écouteur de diagnostic](xref:core/logging-events-diagnostics/diagnostic-listeners) pour obtenir les mêmes informations, mais pour toutes les instances de DbContext dans le processus.</span><span class="sxs-lookup"><span data-stu-id="c4786-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="events-raised-by-ef-core"></a><span data-ttu-id="c4786-110">Événements déclenchés par EF Core</span><span class="sxs-lookup"><span data-stu-id="c4786-110">Events raised by EF Core</span></span>

<span data-ttu-id="c4786-111">Les événements suivants sont déclenchés par EF Core :</span><span class="sxs-lookup"><span data-stu-id="c4786-111">The following events are raised by EF Core:</span></span>

| <span data-ttu-id="c4786-112">Événement</span><span class="sxs-lookup"><span data-stu-id="c4786-112">Event</span></span> | <span data-ttu-id="c4786-113">Version introduite</span><span class="sxs-lookup"><span data-stu-id="c4786-113">Version introduced</span></span> | <span data-ttu-id="c4786-114">En cas de déclenchement</span><span class="sxs-lookup"><span data-stu-id="c4786-114">When raised</span></span>
|:------|--------------------|-------
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> | <span data-ttu-id="c4786-115">5.0</span><span class="sxs-lookup"><span data-stu-id="c4786-115">5.0</span></span> | <span data-ttu-id="c4786-116">Au début de <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ou <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="c4786-116">At the start of <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavedChanges?displayProperty=nameWithType> | <span data-ttu-id="c4786-117">5.0</span><span class="sxs-lookup"><span data-stu-id="c4786-117">5.0</span></span> | <span data-ttu-id="c4786-118">À la fin d’une opération réussie <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ou <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="c4786-118">At the end of a successful <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesFailed?displayProperty=nameWithType> | <span data-ttu-id="c4786-119">5.0</span><span class="sxs-lookup"><span data-stu-id="c4786-119">5.0</span></span> | <span data-ttu-id="c4786-120">À la fin d’un échec <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ou <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="c4786-120">At the end of a failed <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | <span data-ttu-id="c4786-121">2.1</span><span class="sxs-lookup"><span data-stu-id="c4786-121">2.1</span></span> | <span data-ttu-id="c4786-122">Lorsqu’une entité est suivie par le contexte</span><span class="sxs-lookup"><span data-stu-id="c4786-122">When an entity is tracked by the context</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | <span data-ttu-id="c4786-123">2.1</span><span class="sxs-lookup"><span data-stu-id="c4786-123">2.1</span></span> | <span data-ttu-id="c4786-124">Quand une entité suivie change d’État</span><span class="sxs-lookup"><span data-stu-id="c4786-124">When a tracked entity changes its state</span></span>

### <a name="example-timestamp-state-changes"></a><span data-ttu-id="c4786-125">Exemple : modifications de l’état de l’horodateur</span><span class="sxs-lookup"><span data-stu-id="c4786-125">Example: Timestamp state changes</span></span>

<span data-ttu-id="c4786-126">Chaque entité suivie par DbContext a un <xref:Microsoft.EntityFrameworkCore.EntityState> .</span><span class="sxs-lookup"><span data-stu-id="c4786-126">Each entity tracked by a DbContext has an <xref:Microsoft.EntityFrameworkCore.EntityState>.</span></span> <span data-ttu-id="c4786-127">Par exemple, l' `Added` État indique que l’entité sera insérée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="c4786-127">For example, the `Added` state indicates that the entity will be inserted into the database.</span></span>

<span data-ttu-id="c4786-128">Cet exemple utilise les <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> événements et pour détecter quand une entité change d’État.</span><span class="sxs-lookup"><span data-stu-id="c4786-128">This example uses the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> events to detect when an entity changes state.</span></span> <span data-ttu-id="c4786-129">Il marque ensuite l’entité avec l’heure actuelle indiquant à quel moment cette modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="c4786-129">It then stamps the entity with the current time indicating when this change happened.</span></span> <span data-ttu-id="c4786-130">Ainsi, les horodateurs indiquent le moment où l’entité a été insérée, supprimée et/ou la dernière mise à jour.</span><span class="sxs-lookup"><span data-stu-id="c4786-130">This results in timestamps indicating when the entity was inserted, deleted, and/or last updated.</span></span>

<span data-ttu-id="c4786-131">Les types d’entités de cet exemple implémentent une interface qui définit les propriétés d’horodatage :</span><span class="sxs-lookup"><span data-stu-id="c4786-131">The entity types in this example implement an interface that defines the timestamp properties:</span></span>

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

<span data-ttu-id="c4786-132">Une méthode sur le DbContext de l’application peut ensuite définir des horodateurs pour toute entité qui implémente cette interface :</span><span class="sxs-lookup"><span data-stu-id="c4786-132">A method on the application's DbContext can then set timestamps for any entity that implements this interface:</span></span>

<!--
    private static void UpdateTimestamps(object sender, EntityEntryEventArgs e)
    {
        if (e.Entry.Entity is IHasTimestamps entityWithTimestamps)
        {
            switch (e.Entry.State)
            {
                case EntityState.Deleted:
                    entityWithTimestamps.Deleted = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for delete: {e.Entry.Entity}");
                    break;
                case EntityState.Modified:
                    entityWithTimestamps.Modified = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for update: {e.Entry.Entity}");
                    break;
                case EntityState.Added:
                    entityWithTimestamps.Added = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for insert: {e.Entry.Entity}");
                    break;
            }
        }
    }
-->
[!code-csharp[UpdateTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=UpdateTimestamps)]

<span data-ttu-id="c4786-133">Cette méthode a la signature appropriée à utiliser comme gestionnaire d’événements pour les `Tracked` événements et `StateChanged` .</span><span class="sxs-lookup"><span data-stu-id="c4786-133">This method has the appropriate signature to use as an event handler for both the `Tracked` and `StateChanged` events.</span></span> <span data-ttu-id="c4786-134">Le gestionnaire est inscrit pour les deux événements dans le constructeur DbContext.</span><span class="sxs-lookup"><span data-stu-id="c4786-134">The handler is registered for both events in the DbContext constructor.</span></span> <span data-ttu-id="c4786-135">Notez que les événements peuvent être attachés à un DbContext à tout moment ; Il n’est pas nécessaire que cela se produise dans le constructeur de contexte.</span><span class="sxs-lookup"><span data-stu-id="c4786-135">Note that events can be attached to a DbContext at any time; it is not required that this happen in the context constructor.</span></span>

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

<span data-ttu-id="c4786-136">Les deux événements sont nécessaires, car les nouvelles entités déclenchent des `Tracked` événements lorsqu’ils sont suivis pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="c4786-136">Both events are needed because new entities fire `Tracked` events when they are first tracked.</span></span> <span data-ttu-id="c4786-137">`StateChanged` les événements sont déclenchés uniquement pour les entités qui changent d’État pendant qu’elles font _déjà_ l’objet d’un suivi.</span><span class="sxs-lookup"><span data-stu-id="c4786-137">`StateChanged` events are only fired for entities that change state while they are _already_ being tracked.</span></span>

<span data-ttu-id="c4786-138">L’exemple de cet exemple contient une application console simple qui [apporte des modifications](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) à la base de données de blog :</span><span class="sxs-lookup"><span data-stu-id="c4786-138">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Events) for this example contains a simple console application that makes changes to the blogging database:</span></span>

<!--
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();

            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Id = 1, Title = "EF Core 3.1!" },
                        new Post { Id = 2, Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Id = 3, Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
-->
[!code-csharp[Demonstration](../../../samples/core/Miscellaneous/Events/Program.cs?name=Demonstration)]

<span data-ttu-id="c4786-139">La sortie de ce code indique les changements d’État qui se produisent et les horodateurs appliqués :</span><span class="sxs-lookup"><span data-stu-id="c4786-139">The output from this code shows the state changes happening and the timestamps being applied:</span></span>

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
