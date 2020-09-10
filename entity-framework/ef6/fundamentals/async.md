---
title: Requête Async et Save-EF6
description: Requête asynchrone et enregistrement dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
uid: ef6/fundamentals/async
ms.openlocfilehash: 0ecfa116da9591a03ca50a8a186c5381c3e92436
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618543"
---
# <a name="async-query-and-save"></a><span data-ttu-id="040ca-103">Requête asynchrone et enregistrement</span><span class="sxs-lookup"><span data-stu-id="040ca-103">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="040ca-104">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="040ca-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="040ca-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="040ca-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="040ca-106">EF6 a introduit la prise en charge de la requête asynchrone et l’enregistre à l’aide des [Mots clés Async et await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) qui ont été introduits dans .net 4,5.</span><span class="sxs-lookup"><span data-stu-id="040ca-106">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="040ca-107">Même si toutes les applications ne peuvent pas tirer parti de l’asynchronie, elles peuvent être utilisées pour améliorer la réactivité du client et l’évolutivité du serveur lors du traitement des tâches de longue durée, réseau ou liées aux e/s.</span><span class="sxs-lookup"><span data-stu-id="040ca-107">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="040ca-108">Quand vraiment utiliser Async</span><span class="sxs-lookup"><span data-stu-id="040ca-108">When to really use async</span></span>

<span data-ttu-id="040ca-109">L’objectif de cette procédure pas à pas est d’introduire les concepts Async d’une manière qui facilite l’observation de la différence entre l’exécution asynchrone et synchrone du programme.</span><span class="sxs-lookup"><span data-stu-id="040ca-109">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="040ca-110">Cette procédure pas à pas n’est pas destinée à illustrer les principaux scénarios où la programmation asynchrone offre des avantages.</span><span class="sxs-lookup"><span data-stu-id="040ca-110">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="040ca-111">La programmation asynchrone est principalement axée sur la libération du thread managé actuel (thread exécutant du code .NET) pour effectuer d’autres tâches pendant qu’il attend une opération qui ne nécessite pas de temps de calcul d’un thread managé.</span><span class="sxs-lookup"><span data-stu-id="040ca-111">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="040ca-112">Par exemple, pendant que le moteur de base de données traite une requête, il n’y a rien à faire par le code .NET.</span><span class="sxs-lookup"><span data-stu-id="040ca-112">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="040ca-113">Dans les applications clientes (WinForms, WPF, etc.), le thread actuel peut être utilisé pour maintenir la réactivité de l’interface utilisateur pendant l’exécution de l’opération asynchrone.</span><span class="sxs-lookup"><span data-stu-id="040ca-113">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="040ca-114">Dans les applications serveur (ASP.NET, etc.), le thread peut être utilisé pour traiter d’autres demandes entrantes, ce qui peut réduire l’utilisation de la mémoire et/ou augmenter le débit du serveur.</span><span class="sxs-lookup"><span data-stu-id="040ca-114">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="040ca-115">Dans la plupart des applications utilisant Async n’auront pas d’avantages significatifs et même pourrait être nuisible.</span><span class="sxs-lookup"><span data-stu-id="040ca-115">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="040ca-116">Utilisez les tests, le profilage et le bon sens pour mesurer l’impact de Async dans votre scénario particulier avant de le valider.</span><span class="sxs-lookup"><span data-stu-id="040ca-116">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="040ca-117">Voici d’autres ressources pour en savoir plus sur Async :</span><span class="sxs-lookup"><span data-stu-id="040ca-117">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="040ca-118">Vue d’ensemble de Brandon Bray de Async/await dans .NET 4,5</span><span class="sxs-lookup"><span data-stu-id="040ca-118">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="040ca-119">Pages de [programmation asynchrones](https://msdn.microsoft.com/library/hh191443.aspx) dans MSDN Library</span><span class="sxs-lookup"><span data-stu-id="040ca-119">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="040ca-120">[Comment créer des applications Web ASP.net à l’aide de Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (comprend une démonstration du débit accru du serveur)</span><span class="sxs-lookup"><span data-stu-id="040ca-120">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="040ca-121">Créer le modèle</span><span class="sxs-lookup"><span data-stu-id="040ca-121">Create the model</span></span>

<span data-ttu-id="040ca-122">Nous utiliserons le flux de travail [Code First](xref:ef6/modeling/code-first/workflows/new-database) pour créer notre modèle et générer la base de données. Toutefois, les fonctionnalités asynchrones fonctionnent avec tous les modèles EF, y compris ceux créés avec le concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="040ca-122">We’ll be using the [Code First workflow](xref:ef6/modeling/code-first/workflows/new-database) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="040ca-123">Créer une application console et l’appeler **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="040ca-123">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="040ca-124">Ajouter le package NuGet EntityFramework</span><span class="sxs-lookup"><span data-stu-id="040ca-124">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="040ca-125">Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="040ca-125">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="040ca-126">Sélectionnez **gérer les packages NuGet...**</span><span class="sxs-lookup"><span data-stu-id="040ca-126">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="040ca-127">Dans la boîte de dialogue gérer les packages NuGet, sélectionnez l’onglet **en ligne** et choisissez le package **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="040ca-127">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="040ca-128">Cliquez sur **Install**.</span><span class="sxs-lookup"><span data-stu-id="040ca-128">Click **Install**</span></span>
-   <span data-ttu-id="040ca-129">Ajoutez une classe **Model.cs** avec l’implémentation suivante</span><span class="sxs-lookup"><span data-stu-id="040ca-129">Add a **Model.cs** class with the following implementation</span></span>

``` csharp
    using System.Collections.Generic;
    using System.Data.Entity;

    namespace AsyncDemo
    {
        public class BloggingContext : DbContext
        {
            public DbSet<Blog> Blogs { get; set; }
            public DbSet<Post> Posts { get; set; }
        }

        public class Blog
        {
            public int BlogId { get; set; }
            public string Name { get; set; }

            public virtual List<Post> Posts { get; set; }
        }

        public class Post
        {
            public int PostId { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public int BlogId { get; set; }
            public virtual Blog Blog { get; set; }
        }
    }
```

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="040ca-130">Créer un programme synchrone</span><span class="sxs-lookup"><span data-stu-id="040ca-130">Create a synchronous program</span></span>

<span data-ttu-id="040ca-131">Maintenant que nous disposons d’un modèle EF, nous allons écrire du code qui l’utilise pour effectuer un accès aux données.</span><span class="sxs-lookup"><span data-stu-id="040ca-131">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="040ca-132">Remplacez le contenu de **Program.cs** par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="040ca-132">Replace the contents of **Program.cs** with the following code</span></span>

``` csharp
    using System;
    using System.Linq;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static void PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    db.SaveChanges();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="040ca-133">Ce code appelle la méthode **PerformDatabaseOperations** qui enregistre un nouveau **blog** dans la base de données, puis récupère tous les **blogs** de la base de données et les imprime sur la **console**.</span><span class="sxs-lookup"><span data-stu-id="040ca-133">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="040ca-134">Après cela, le programme écrit un guillemet du jour sur la **console**.</span><span class="sxs-lookup"><span data-stu-id="040ca-134">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="040ca-135">Étant donné que le code est synchrone, nous pouvons observer le déroulement de l’exécution suivant lorsque nous exécutons le programme :</span><span class="sxs-lookup"><span data-stu-id="040ca-135">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="040ca-136">**SaveChanges** commence à envoyer le nouveau **blog** à la base de données</span><span class="sxs-lookup"><span data-stu-id="040ca-136">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="040ca-137">**SaveChanges** se termine</span><span class="sxs-lookup"><span data-stu-id="040ca-137">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="040ca-138">La requête de tous les **blogs** est envoyée à la base de données</span><span class="sxs-lookup"><span data-stu-id="040ca-138">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="040ca-139">La requête retourne et les résultats sont écrits dans la **console**</span><span class="sxs-lookup"><span data-stu-id="040ca-139">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="040ca-140">Le devis du jour est écrit dans la **console**</span><span class="sxs-lookup"><span data-stu-id="040ca-140">Quote of the day is written to **Console**</span></span>

![Sortie de la synchronisation](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="040ca-142">Rendre asynchrone</span><span class="sxs-lookup"><span data-stu-id="040ca-142">Making it asynchronous</span></span>

<span data-ttu-id="040ca-143">Maintenant que notre programme est opérationnel, nous pouvons commencer à utiliser les nouveaux mots clés Async et await.</span><span class="sxs-lookup"><span data-stu-id="040ca-143">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="040ca-144">Nous avons apporté les modifications suivantes à Program.cs</span><span class="sxs-lookup"><span data-stu-id="040ca-144">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="040ca-145">Ligne 2 : l’instruction using pour l’espace de noms **System. Data. Entity** nous donne accès aux méthodes d’extension EF Async.</span><span class="sxs-lookup"><span data-stu-id="040ca-145">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="040ca-146">Ligne 4 : l’instruction using pour l’espace de noms **System. Threading. Tasks** nous permet d’utiliser le type de **tâche** .</span><span class="sxs-lookup"><span data-stu-id="040ca-146">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="040ca-147">Ligne 12 & 18 : nous effectuons une capture en tant que tâche qui surveille la progression de **PerformSomeDatabaseOperations** (ligne 12), puis bloquent l’exécution de programme pour que cette tâche se termine une fois que tout le travail pour le programme est terminé (ligne 18).</span><span class="sxs-lookup"><span data-stu-id="040ca-147">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="040ca-148">Ligne 25 : nous mettons à jour **PerformSomeDatabaseOperations** pour qu’elles soient marquées comme **Async** et retournent une **tâche**.</span><span class="sxs-lookup"><span data-stu-id="040ca-148">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="040ca-149">Ligne 35 : nous appelons maintenant la version Async de SaveChanges et en attendant son achèvement.</span><span class="sxs-lookup"><span data-stu-id="040ca-149">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="040ca-150">Ligne 42 : nous appelons maintenant la version Async de ToList et en attendant le résultat.</span><span class="sxs-lookup"><span data-stu-id="040ca-150">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="040ca-151">Pour obtenir la liste complète des méthodes d’extension disponibles dans l’espace de noms System. Data. Entity, reportez-vous à la classe QueryableExtensions.</span><span class="sxs-lookup"><span data-stu-id="040ca-151">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="040ca-152">*Vous devez également ajouter « using System. Data. Entity » à vos instructions using.*</span><span class="sxs-lookup"><span data-stu-id="040ca-152">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

``` csharp
    using System;
    using System.Data.Entity;
    using System.Linq;
    using System.Threading.Tasks;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                var task = PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                task.Wait();

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static async Task PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    await db.SaveChangesAsync();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = await (from b in db.Blogs
                                orderby b.Name
                                select b).ToListAsync();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" - " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="040ca-153">Maintenant que le code est asynchrone, nous pouvons observer un autre workflow d’exécution lorsque nous exécutons le programme :</span><span class="sxs-lookup"><span data-stu-id="040ca-153">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="040ca-154">**SaveChanges** commence à envoyer le nouveau **blog** à la base de données</span><span class="sxs-lookup"><span data-stu-id="040ca-154">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="040ca-155">*Une fois la commande envoyée à la base de données, aucune durée de calcul supplémentaire n’est nécessaire sur le thread managé actuel. La méthode **PerformDatabaseOperations** retourne (même si elle n’a pas fini de s’exécuter) et le déroulement du programme dans la méthode main se poursuit.*</span><span class="sxs-lookup"><span data-stu-id="040ca-155">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="040ca-156">**Le devis du jour est écrit dans la console**</span><span class="sxs-lookup"><span data-stu-id="040ca-156">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="040ca-157">*Étant donné qu’il n’y a plus de travail à effectuer dans la méthode main, le thread managé est bloqué sur l’appel d’attente jusqu’à ce que l’opération de base de données soit terminée. Une fois l’opération terminée, le reste de notre **PerformDatabaseOperations** est exécuté.*</span><span class="sxs-lookup"><span data-stu-id="040ca-157">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="040ca-158">**SaveChanges** se termine</span><span class="sxs-lookup"><span data-stu-id="040ca-158">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="040ca-159">La requête de tous les **blogs** est envoyée à la base de données</span><span class="sxs-lookup"><span data-stu-id="040ca-159">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="040ca-160">*Là encore, le thread managé est libre d’effectuer d’autres tâches pendant le traitement de la requête dans la base de données. Étant donné que toutes les autres exécutions sont terminées, le thread s’arrêtera simplement sur l’appel d’attente.*</span><span class="sxs-lookup"><span data-stu-id="040ca-160">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="040ca-161">La requête retourne et les résultats sont écrits dans la **console**</span><span class="sxs-lookup"><span data-stu-id="040ca-161">Query returns and results are written to **Console**</span></span>  

![Sortie asynchrone](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="040ca-163">Le</span><span class="sxs-lookup"><span data-stu-id="040ca-163">The takeaway</span></span>

<span data-ttu-id="040ca-164">Nous avons maintenant vu combien il est facile d’utiliser les méthodes asynchrones d’EF.</span><span class="sxs-lookup"><span data-stu-id="040ca-164">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="040ca-165">Bien que les avantages de Async ne soient pas très évidents avec une application console simple, ces mêmes stratégies peuvent être appliquées dans les situations où des activités longues ou liées au réseau pourraient bloquer l’application, ou provoquer un grand nombre de threads pour augmenter l’encombrement mémoire.</span><span class="sxs-lookup"><span data-stu-id="040ca-165">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
