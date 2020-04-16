---
title: Async requête et sauver - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: a001df726b7cd3342fcca566c1373f452811d07d
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434238"
---
# <a name="async-query-and-save"></a><span data-ttu-id="03034-102">Async requête et enregistrer</span><span class="sxs-lookup"><span data-stu-id="03034-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="03034-103">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="03034-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="03034-104">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="03034-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="03034-105">EF6 a introduit le soutien pour la requête asynchrone et enregistrer en utilisant [l’async et attendre les mots clés](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) qui ont été introduits dans .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="03034-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="03034-106">Bien que toutes les applications ne puissent pas bénéficier d’asynchrony, elles peuvent être utilisées pour améliorer la réactivité des clients et l’évolutivité du serveur lors de la gestion de tâches à long terme, réseau ou I/O-bound.</span><span class="sxs-lookup"><span data-stu-id="03034-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="03034-107">Quand vraiment utiliser async</span><span class="sxs-lookup"><span data-stu-id="03034-107">When to really use async</span></span>

<span data-ttu-id="03034-108">Le but de cette procédure pas à pas est d’introduire les concepts async d’une manière qui facilite l’observation de la différence entre l’exécution asynchrone et l’exécution synchrone du programme.</span><span class="sxs-lookup"><span data-stu-id="03034-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="03034-109">Cette procédure pas à pas n’a pour but d’illustrer aucun des scénarios clés où la programmation async offre des avantages.</span><span class="sxs-lookup"><span data-stu-id="03034-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="03034-110">La programmation Async est principalement axée sur la libération du thread géré actuel (thread exécutant .NET code) pour faire d’autres travaux pendant qu’il attend une opération qui ne nécessite pas de temps de calcul à partir d’un thread géré.</span><span class="sxs-lookup"><span data-stu-id="03034-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="03034-111">Par exemple, alors que le moteur de base de données traite une requête, il n’y a rien à faire par code .NET.</span><span class="sxs-lookup"><span data-stu-id="03034-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="03034-112">Dans les applications client (WinForms, WPF, etc.), le thread actuel peut être utilisé pour garder l’interface utilisateur réactive pendant que l’opération async est effectuée.</span><span class="sxs-lookup"><span data-stu-id="03034-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="03034-113">Dans les applications serveur (ASP.NET etc.), le thread peut être utilisé pour traiter d’autres demandes entrantes - cela peut réduire l’utilisation de la mémoire et/ou augmenter le débit du serveur.</span><span class="sxs-lookup"><span data-stu-id="03034-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="03034-114">Dans la plupart des applications utilisant async n’aura pas d’avantages notables et pourrait même être préjudiciable.</span><span class="sxs-lookup"><span data-stu-id="03034-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="03034-115">Utilisez des tests, du profilage et du bon sens pour mesurer l’impact de l’async dans votre scénario particulier avant de vous y engager.</span><span class="sxs-lookup"><span data-stu-id="03034-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="03034-116">Voici quelques ressources supplémentaires pour en savoir plus sur async :</span><span class="sxs-lookup"><span data-stu-id="03034-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="03034-117">Aperçu de Brandon Bray de async/await in .NET 4.5</span><span class="sxs-lookup"><span data-stu-id="03034-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="03034-118">[Pages de programmation asynchrones](https://msdn.microsoft.com/library/hh191443.aspx) dans la bibliothèque MSDN</span><span class="sxs-lookup"><span data-stu-id="03034-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="03034-119">[Comment construire ASP.NET applications Web à l’aide d’Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (inclut une démo de débit serveur accru)</span><span class="sxs-lookup"><span data-stu-id="03034-119">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="03034-120">Créer le modèle</span><span class="sxs-lookup"><span data-stu-id="03034-120">Create the model</span></span>

<span data-ttu-id="03034-121">Nous utiliserons le flux de [travail Code First](~/ef6/modeling/code-first/workflows/new-database.md) pour créer notre modèle et générer la base de données, mais la fonctionnalité asynchrone fonctionnera avec tous les modèles EF, y compris ceux créés avec le concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="03034-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="03034-122">Créez une application console et appelez-la **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="03034-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="03034-123">Ajouter le forfait EntityFramework NuGet</span><span class="sxs-lookup"><span data-stu-id="03034-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="03034-124">Dans Solution Explorer, cliquez à droite sur le projet **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="03034-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="03034-125">Sélectionnez **Gérer les forfaits NuGet...**</span><span class="sxs-lookup"><span data-stu-id="03034-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="03034-126">Dans le dialogue Manage NuGet Packages, Sélectionnez l’onglet **En ligne** et choisissez le forfait **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="03034-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="03034-127">Cliquez **sur Installation**</span><span class="sxs-lookup"><span data-stu-id="03034-127">Click **Install**</span></span>
-   <span data-ttu-id="03034-128">Ajouter une **classe Model.cs** avec la mise en œuvre suivante</span><span class="sxs-lookup"><span data-stu-id="03034-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="03034-129">Créer un programme synchrone</span><span class="sxs-lookup"><span data-stu-id="03034-129">Create a synchronous program</span></span>

<span data-ttu-id="03034-130">Maintenant que nous avons un modèle EF, écrivons un code qui l’utilise pour effectuer un accès aux données.</span><span class="sxs-lookup"><span data-stu-id="03034-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="03034-131">Remplacer le contenu de **Program.cs** par le code suivant</span><span class="sxs-lookup"><span data-stu-id="03034-131">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="03034-132">Ce code appelle la méthode **PerformDatabaseOperations** qui enregistre un nouveau **blog** à la base de données, puis récupère tous les **blogs** de la base de données et les imprime à la **console**.</span><span class="sxs-lookup"><span data-stu-id="03034-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="03034-133">Après cela, le programme écrit une citation du jour à la **Console**.</span><span class="sxs-lookup"><span data-stu-id="03034-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="03034-134">Étant donné que le code est synchrone, nous pouvons observer le flux d’exécution suivant lorsque nous exécuteons le programme :</span><span class="sxs-lookup"><span data-stu-id="03034-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="03034-135">**SaveChanges** commence à pousser le nouveau **blog** à la base de données</span><span class="sxs-lookup"><span data-stu-id="03034-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="03034-136">**SaveChanges** complète</span><span class="sxs-lookup"><span data-stu-id="03034-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="03034-137">La requête pour tous les **blogs** est envoyée à la base de données</span><span class="sxs-lookup"><span data-stu-id="03034-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="03034-138">Les retours et les résultats de requête sont écrits sur **console**</span><span class="sxs-lookup"><span data-stu-id="03034-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="03034-139">Citation du jour est écrit à **Console**</span><span class="sxs-lookup"><span data-stu-id="03034-139">Quote of the day is written to **Console**</span></span>

![Synchroniser la sortie](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="03034-141">Le rendre asynchrone</span><span class="sxs-lookup"><span data-stu-id="03034-141">Making it asynchronous</span></span>

<span data-ttu-id="03034-142">Maintenant que nous avons notre programme en place et en cours d’exécution, nous pouvons commencer à faire usage du nouvel async et attendre des mots clés.</span><span class="sxs-lookup"><span data-stu-id="03034-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="03034-143">Nous avons apporté les changements suivants à Program.cs</span><span class="sxs-lookup"><span data-stu-id="03034-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="03034-144">Ligne 2 : L’instruction d’utilisation de l’espace de nom **System.Data.Entity** nous donne accès aux méthodes d’extension d’async EF.</span><span class="sxs-lookup"><span data-stu-id="03034-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="03034-145">Ligne 4 : L’instruction d’utilisation de l’espace de nom **System.Threading.Tasks** nous permet d’utiliser le type **De tâche.**</span><span class="sxs-lookup"><span data-stu-id="03034-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="03034-146">Ligne 12 & 18: Nous capturons comme tâche qui surveille les progrès de **PerformSomeDatabaseOperations** (ligne 12) et puis le blocage de l’exécution du programme pour cette tâche à accomplir une fois que tout le travail pour le programme est fait (ligne 18).</span><span class="sxs-lookup"><span data-stu-id="03034-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="03034-147">Ligne 25: Nous avons mis à jour **PerformSomeDatabaseOperations** pour être marqué comme **async** et retourner une **tâche**.</span><span class="sxs-lookup"><span data-stu-id="03034-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="03034-148">Ligne 35 : Nous appelons maintenant la version Async de SaveChanges et attendons son achèvement.</span><span class="sxs-lookup"><span data-stu-id="03034-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="03034-149">Ligne 42: Nous appelons maintenant la version Async de ToList et en attente sur le résultat.</span><span class="sxs-lookup"><span data-stu-id="03034-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="03034-150">Pour une liste complète des méthodes d’extension disponibles dans l’espace de nom System.Data.Entity, consultez la classe RequêtesExtensions.</span><span class="sxs-lookup"><span data-stu-id="03034-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="03034-151">*Vous devrez également ajouter "utiliser System.Data.Entity" à votre utilisation des instructions.*</span><span class="sxs-lookup"><span data-stu-id="03034-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="03034-152">Maintenant que le code est asynchrone, nous pouvons observer un flux d’exécution différent lorsque nous relérons le programme:</span><span class="sxs-lookup"><span data-stu-id="03034-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="03034-153">**SaveChanges** commence à pousser le nouveau **blog** à la base de données</span><span class="sxs-lookup"><span data-stu-id="03034-153">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="03034-154">*Une fois que la commande est envoyée à la base de données, plus de temps de calcul n’est nécessaire sur le thread géré actuel. La méthode **PerformDatabaseOperations** revient (même si elle n’a pas fini l’exécution) et le flux du programme dans la méthode Principale se poursuit.*</span><span class="sxs-lookup"><span data-stu-id="03034-154">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="03034-155">**Citation du jour est écrit à Console**</span><span class="sxs-lookup"><span data-stu-id="03034-155">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="03034-156">*Comme il n’y a plus de travail à faire dans la méthode Principale, le thread géré est bloqué sur l’appel d’attente jusqu’à ce que l’opération de base de données se termine. Une fois qu’il sera terminé, le reste de nos **PerformDatabaseOperations** sera exécuté.*</span><span class="sxs-lookup"><span data-stu-id="03034-156">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="03034-157">**SaveChanges** complète</span><span class="sxs-lookup"><span data-stu-id="03034-157">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="03034-158">La requête pour tous les **blogs** est envoyée à la base de données</span><span class="sxs-lookup"><span data-stu-id="03034-158">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="03034-159">*Encore une fois, le thread géré est libre de faire d’autres travaux pendant que la requête est traitée dans la base de données. Puisque toutes les autres exécutions sont terminées, le fil s’arrêtera sur l’appel d’attente cependant.*</span><span class="sxs-lookup"><span data-stu-id="03034-159">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="03034-160">Les retours et les résultats de requête sont écrits sur **console**</span><span class="sxs-lookup"><span data-stu-id="03034-160">Query returns and results are written to **Console**</span></span>  

![Sortie Async](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="03034-162">Le plat à emporter</span><span class="sxs-lookup"><span data-stu-id="03034-162">The takeaway</span></span>

<span data-ttu-id="03034-163">Nous avons maintenant vu combien il est facile d’utiliser les méthodes asynchrones d’EF.</span><span class="sxs-lookup"><span data-stu-id="03034-163">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="03034-164">Bien que les avantages de l’async ne soient pas très évidents avec une simple application de console, ces mêmes stratégies peuvent être appliquées dans des situations où des activités à long terme ou reliées au réseau pourraient autrement bloquer l’application, ou provoquer un grand nombre de threads pour augmenter l’empreinte mémoire.</span><span class="sxs-lookup"><span data-stu-id="03034-164">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
