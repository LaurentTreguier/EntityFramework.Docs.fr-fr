---
title: Utilisation des écouteurs de diagnostic-EF Core
description: Utilisation de DiagnosticListener pour la consommation mondiale de diagnostics de EF Core
author: ajcvickers
ms.date: 10/16/2020
uid: core/logging-events-diagnostics/diagnostic-listeners
ms.openlocfilehash: 7035acaa6d306e73a0a2c071532ece0d8e9a0a1d
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024249"
---
# <a name="using-diagnostic-listeners-in-ef-core"></a>Utilisation d’écouteurs de diagnostic dans EF Core

> [!TIP]
> Vous pouvez [Télécharger l’exemple de cet article](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) à partir de github.

Les écouteurs de diagnostic permettent d’écouter les EF Core événement qui se produit dans le processus .NET actuel. La <xref:System.Diagnostics.DiagnosticListener> classe fait partie d’un [mécanisme commun à travers .net](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) pour obtenir des informations de diagnostic à partir d’applications en cours d’exécution.

Les écouteurs de diagnostic ne sont pas adaptés à l’obtention d’événements à partir d’une seule instance DbContext. Les [intercepteurs](xref:core/logging-events-diagnostics/interceptors) EF Core donnent accès aux mêmes événements avec l’inscription par contexte.

Les écouteurs de diagnostic ne sont pas conçus pour la journalisation. Envisagez d’utiliser la [journalisation simple](xref:core/logging-events-diagnostics/simple-logging) ou [Microsoft. extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) pour la journalisation.

## <a name="example-observing-diagnostic-events"></a>Exemple : observation des événements de diagnostic

La résolution des événements de EF Core est un processus en deux étapes. Tout d’abord, un [Observateur](/dotnet/standard/events/observer-design-pattern) pour `DiagnosticListener` lui-même doit être créé :

<!--
public class DiagnosticObserver : IObserver<DiagnosticListener>
{
    public void OnCompleted()
        => throw new NotImplementedException();

    public void OnError(Exception error)
        => throw new NotImplementedException();

    public void OnNext(DiagnosticListener value)
    {
        if (value.Name == DbLoggerCategory.Name) // "Microsoft.EntityFrameworkCore"
        {
            value.Subscribe(new KeyValueObserver());
        }
    }
}
-->
[!code-csharp[DiagnosticObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=DiagnosticObserver)]

La `OnNext` méthode recherche le DiagnosticListener qui provient de EF Core. Cet écouteur porte le nom « Microsoft. EntityFrameworkCore », qui peut être obtenu à partir de la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> classe, comme indiqué.

Cet observateur doit ensuite être inscrit globalement, par exemple dans la méthode de l’application `Main` :

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

Deuxièmement, une fois que le EF Core DiagnosticListener est trouvé, un nouvel observateur clé-valeur est créé pour s’abonner aux événements EF Core réels. Par exemple :

<!--
public class KeyValueObserver : IObserver<KeyValuePair<string, object>>
{
    public void OnCompleted()
        => throw new NotImplementedException();

    public void OnError(Exception error)
        => throw new NotImplementedException();

    public void OnNext(KeyValuePair<string, object> value)
    {
        if (value.Key == CoreEventId.ContextInitialized.Name)
        {
            var payload = (ContextInitializedEventData)value.Value;
            Console.WriteLine($"EF is initializing {payload.Context.GetType().Name} ");
        }

        if (value.Key == RelationalEventId.ConnectionOpening.Name)
        {
            var payload = (ConnectionEventData)value.Value;
            Console.WriteLine($"EF is opening a connection to {payload.Connection.ConnectionString} ");
        }
    }
}
-->
[!code-csharp[KeyValueObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=KeyValueObserver)]

La `OnNext` méthode est cette fois appelée avec une paire clé/valeur pour chaque événement EF Core. La clé est le nom de l’événement, qui peut être obtenu à partir de l’un des éléments suivants :

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> pour les événements communs à tous les fournisseurs de bases de données EF Core
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> pour les événements communs à tous les fournisseurs de bases de données relationnelles
* Classe similaire pour les événements spécifiques au fournisseur de base de données actuel. Par exemple, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> pour le fournisseur SQL Server.

La valeur de la paire clé/valeur est un type de charge utile spécifique à l’événement. Le type de charge utile à attendre est documenté sur chaque événement défini dans ces classes d’événements.

Par exemple, le code ci-dessus gère les <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> événements et. Pour la première, la charge utile est <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> . Pour la seconde, il s’agit de <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> .

> [!TIP]
> ToString est substitué dans chaque classe de données d’événement EF Core pour générer le message de journal équivalent pour l’événement. Par exemple, l’appel de `ContextInitializedEventData.ToString` génère « Entity Framework Core 5.0.0 a initialisé «BlogsContext » à l’aide du fournisseur « Microsoft. EntityFrameworkCore. sqlite » avec options : None».

L' [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/DiagnosticListeners) contient une application console simple qui apporte des modifications à la base de données de blog et imprime les événements de diagnostic rencontrés.

<!--
    public static void Main()
    {
        #region RegisterDiagnosticListener
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
        #endregion

        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();

            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
        #endregion
    }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=Program)]

La sortie de ce code montre les événements détectés :

```output
EF is initializing BlogsContext
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is initializing BlogsContext
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
```
