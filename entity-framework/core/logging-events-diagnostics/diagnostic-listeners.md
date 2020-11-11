---
title: Utilisation des écouteurs de diagnostic-EF Core
description: Utilisation de DiagnosticListener pour la consommation mondiale de diagnostics de EF Core
author: ajcvickers
ms.date: 10/16/2020
uid: core/logging-events-diagnostics/diagnostic-listeners
ms.openlocfilehash: a2a962ac714cf80c42c269cee3770699aaa4c0c9
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503226"
---
# <a name="using-diagnostic-listeners-in-ef-core"></a><span data-ttu-id="44091-103">Utilisation d’écouteurs de diagnostic dans EF Core</span><span class="sxs-lookup"><span data-stu-id="44091-103">Using Diagnostic Listeners in EF Core</span></span>

> [!TIP]  
> <span data-ttu-id="44091-104">Vous pouvez [Télécharger l’exemple de cet article](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) à partir de github.</span><span class="sxs-lookup"><span data-stu-id="44091-104">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) from GitHub.</span></span>

<span data-ttu-id="44091-105">Les écouteurs de diagnostic permettent d’écouter les EF Core événement qui se produit dans le processus .NET actuel.</span><span class="sxs-lookup"><span data-stu-id="44091-105">Diagnostic listeners allow listening for any EF Core event that occurs in the current .NET process.</span></span> <span data-ttu-id="44091-106">La <xref:System.Diagnostics.DiagnosticListener> classe fait partie d’un [mécanisme commun à travers .net](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) pour obtenir des informations de diagnostic à partir d’applications en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="44091-106">The <xref:System.Diagnostics.DiagnosticListener> class is a part of a [common mechanism across .NET](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) for obtaining diagnostic information from running applications.</span></span>

<span data-ttu-id="44091-107">Les écouteurs de diagnostic ne sont pas adaptés à l’obtention d’événements à partir d’une seule instance DbContext.</span><span class="sxs-lookup"><span data-stu-id="44091-107">Diagnostic listeners are not suitable for getting events from a single DbContext instance.</span></span> <span data-ttu-id="44091-108">Les [intercepteurs](xref:core/logging-events-diagnostics/interceptors) EF Core donnent accès aux mêmes événements avec l’inscription par contexte.</span><span class="sxs-lookup"><span data-stu-id="44091-108">EF Core [interceptors](xref:core/logging-events-diagnostics/interceptors) provide access to the same events with per-context registration.</span></span>

<span data-ttu-id="44091-109">Les écouteurs de diagnostic ne sont pas conçus pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="44091-109">Diagnostic listeners are not designed for logging.</span></span> <span data-ttu-id="44091-110">Envisagez d’utiliser la [journalisation simple](xref:core/logging-events-diagnostics/simple-logging) ou [Microsoft. extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) pour la journalisation.</span><span class="sxs-lookup"><span data-stu-id="44091-110">Consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) for logging.</span></span>

## <a name="example-observing-diagnostic-events"></a><span data-ttu-id="44091-111">Exemple : observation des événements de diagnostic</span><span class="sxs-lookup"><span data-stu-id="44091-111">Example: Observing diagnostic events</span></span>

<span data-ttu-id="44091-112">La résolution des événements de EF Core est un processus en deux étapes.</span><span class="sxs-lookup"><span data-stu-id="44091-112">Resolving EF Core events is a two-step process.</span></span> <span data-ttu-id="44091-113">Tout d’abord, un [Observateur](/dotnet/standard/events/observer-design-pattern) pour `DiagnosticListener` lui-même doit être créé :</span><span class="sxs-lookup"><span data-stu-id="44091-113">First, an [observer](/dotnet/standard/events/observer-design-pattern) for `DiagnosticListener` itself must be created:</span></span>

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

<span data-ttu-id="44091-114">La `OnNext` méthode recherche le DiagnosticListener qui provient de EF Core.</span><span class="sxs-lookup"><span data-stu-id="44091-114">The `OnNext` method looks for the DiagnosticListener that comes from EF Core.</span></span> <span data-ttu-id="44091-115">Cet écouteur porte le nom « Microsoft. EntityFrameworkCore », qui peut être obtenu à partir de la <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> classe, comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="44091-115">This listener has the name "Microsoft.EntityFrameworkCore", which can be obtained from the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class as shown.</span></span>

<span data-ttu-id="44091-116">Cet observateur doit ensuite être inscrit globalement, par exemple dans la méthode de l’application `Main` :</span><span class="sxs-lookup"><span data-stu-id="44091-116">This observer must then be registered globally, for example in the application's `Main` method:</span></span>

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

<span data-ttu-id="44091-117">Deuxièmement, une fois que le EF Core DiagnosticListener est trouvé, un nouvel observateur clé-valeur est créé pour s’abonner aux événements EF Core réels.</span><span class="sxs-lookup"><span data-stu-id="44091-117">Second, once the EF Core DiagnosticListener is found, a new key-value observer is created to subscribe to the actual EF Core events.</span></span> <span data-ttu-id="44091-118">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="44091-118">For example:</span></span>

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

<span data-ttu-id="44091-119">La `OnNext` méthode est cette fois appelée avec une paire clé/valeur pour chaque événement EF Core.</span><span class="sxs-lookup"><span data-stu-id="44091-119">The `OnNext` method is this time called with a key/value pair for each EF Core event.</span></span> <span data-ttu-id="44091-120">La clé est le nom de l’événement, qui peut être obtenu à partir de l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="44091-120">The key is the name of the event, which can be obtained from one of:</span></span>

* <span data-ttu-id="44091-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> pour les événements communs à tous les fournisseurs de bases de données EF Core</span><span class="sxs-lookup"><span data-stu-id="44091-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="44091-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> pour les événements communs à tous les fournisseurs de bases de données relationnelles</span><span class="sxs-lookup"><span data-stu-id="44091-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="44091-123">Classe similaire pour les événements spécifiques au fournisseur de base de données actuel.</span><span class="sxs-lookup"><span data-stu-id="44091-123">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="44091-124">Par exemple, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> pour le fournisseur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="44091-124">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="44091-125">La valeur de la paire clé/valeur est un type de charge utile spécifique à l’événement.</span><span class="sxs-lookup"><span data-stu-id="44091-125">The value of the key/value pair is a payload type specific to the event.</span></span> <span data-ttu-id="44091-126">Le type de charge utile à attendre est documenté sur chaque événement défini dans ces classes d’événements.</span><span class="sxs-lookup"><span data-stu-id="44091-126">The type of payload to expect is documented on each event defined in these event classes.</span></span>

<span data-ttu-id="44091-127">Par exemple, le code ci-dessus gère les <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> événements et.</span><span class="sxs-lookup"><span data-stu-id="44091-127">For example, the code above handles the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> and the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> events.</span></span> <span data-ttu-id="44091-128">Pour la première, la charge utile est <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> .</span><span class="sxs-lookup"><span data-stu-id="44091-128">For the first of these, the payload is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData>.</span></span> <span data-ttu-id="44091-129">Pour la seconde, il s’agit de <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> .</span><span class="sxs-lookup"><span data-stu-id="44091-129">For the second, it is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData>.</span></span>

> [!TIP]
> <span data-ttu-id="44091-130">ToString est substitué dans chaque classe de données d’événement EF Core pour générer le message de journal équivalent pour l’événement.</span><span class="sxs-lookup"><span data-stu-id="44091-130">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="44091-131">Par exemple, l’appel de `ContextInitializedEventData.ToString` génère « Entity Framework Core 5.0.0 a initialisé «BlogsContext » à l’aide du fournisseur « Microsoft. EntityFrameworkCore. sqlite » avec options : None».</span><span class="sxs-lookup"><span data-stu-id="44091-131">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

<span data-ttu-id="44091-132">L' [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) contient une application console simple qui apporte des modifications à la base de données de blog et imprime les événements de diagnostic rencontrés.</span><span class="sxs-lookup"><span data-stu-id="44091-132">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) contains a simple console application that makes changes to the blogging database and prints out the diagnostic events encountered.</span></span>

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

<span data-ttu-id="44091-133">La sortie de ce code montre les événements détectés :</span><span class="sxs-lookup"><span data-stu-id="44091-133">The output from this code shows the events detected:</span></span>

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
