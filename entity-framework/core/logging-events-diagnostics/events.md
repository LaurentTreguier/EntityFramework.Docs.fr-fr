---
title: Événements .NET-EF Core
description: Événements .NET définis par EF Core
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: 51c0bba5cf25e1d9ddd1fd9aebea50b9a03481a3
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635690"
---
# <a name="net-events-in-ef-core"></a>Événements .NET dans EF Core

> [!TIP]  
> Vous pouvez [Télécharger l’exemple Events](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) à partir de github.

Entity Framework Core (EF Core) expose les [événements .net](/dotnet/standard/events/) pour agir en tant que rappels lorsque certains événements se produisent dans le code de EF Core. Les événements sont plus simples que les [intercepteurs](xref:core/logging-events-diagnostics/interceptors) et permettent une inscription plus flexible. Toutefois, ils sont synchronisés uniquement et ne peuvent donc pas effectuer d’e/s Async non bloquantes.

Les événements sont inscrits par `DbContext` instance. Utilisez un [écouteur de diagnostic](xref:core/logging-events-diagnostics/diagnostic-listeners) pour obtenir les mêmes informations, mais pour toutes les instances de DbContext dans le processus.

## <a name="events-raised-by-ef-core"></a>Événements déclenchés par EF Core

Les événements suivants sont déclenchés par EF Core :

| Événement | Version introduite | En cas de déclenchement
|:------|--------------------|-------
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> | 5.0 | Au début de <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ou <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavedChanges?displayProperty=nameWithType> | 5.0 | À la fin d’une opération réussie <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ou <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesFailed?displayProperty=nameWithType> | 5.0 | À la fin d’un échec <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ou <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | 2.1 | Lorsqu’une entité est suivie par le contexte
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | 2.1 | Quand une entité suivie change d’État

### <a name="example-timestamp-state-changes"></a>Exemple : modifications de l’état de l’horodateur

Chaque entité suivie par DbContext a un <xref:Microsoft.EntityFrameworkCore.EntityState> . Par exemple, l' `Added` État indique que l’entité sera insérée dans la base de données.

Cet exemple utilise les <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> événements et pour détecter quand une entité change d’État. Il marque ensuite l’entité avec l’heure actuelle indiquant à quel moment cette modification s’est produite. Ainsi, les horodateurs indiquent le moment où l’entité a été insérée, supprimée et/ou la dernière mise à jour.

Les types d’entités de cet exemple implémentent une interface qui définit les propriétés d’horodatage :

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

Une méthode sur le DbContext de l’application peut ensuite définir des horodateurs pour toute entité qui implémente cette interface :

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

Cette méthode a la signature appropriée à utiliser comme gestionnaire d’événements pour les `Tracked` événements et `StateChanged` . Le gestionnaire est inscrit pour les deux événements dans le constructeur DbContext. Notez que les événements peuvent être attachés à un DbContext à tout moment ; Il n’est pas nécessaire que cela se produise dans le constructeur de contexte.

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

Les deux événements sont nécessaires, car les nouvelles entités déclenchent des `Tracked` événements lorsqu’ils sont suivis pour la première fois. `StateChanged` les événements sont déclenchés uniquement pour les entités qui changent d’État pendant qu’elles font _déjà_ l’objet d’un suivi.

L’exemple de cet exemple contient une application console simple qui [apporte des modifications](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) à la base de données de blog :

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

La sortie de ce code indique les changements d’État qui se produisent et les horodateurs appliqués :

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
