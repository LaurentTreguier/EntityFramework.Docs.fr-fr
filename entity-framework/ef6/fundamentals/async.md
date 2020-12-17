---
title: Requête Async et Save-EF6
description: Requête asynchrone et enregistrement dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/async
ms.openlocfilehash: 77204f56e4dca63322c8ae2e1117318262f16f83
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635729"
---
# <a name="async-query-and-save"></a>Requête asynchrone et enregistrement
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

EF6 a introduit la prise en charge de la requête asynchrone et l’enregistre à l’aide des [Mots clés Async et await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) qui ont été introduits dans .net 4,5. Même si toutes les applications ne peuvent pas tirer parti de l’asynchronie, elles peuvent être utilisées pour améliorer la réactivité du client et l’évolutivité du serveur lors du traitement des tâches de longue durée, réseau ou liées aux e/s.

## <a name="when-to-really-use-async"></a>Quand vraiment utiliser Async

L’objectif de cette procédure pas à pas est d’introduire les concepts Async d’une manière qui facilite l’observation de la différence entre l’exécution asynchrone et synchrone du programme. Cette procédure pas à pas n’est pas destinée à illustrer les principaux scénarios où la programmation asynchrone offre des avantages.

La programmation asynchrone est principalement axée sur la libération du thread managé actuel (thread exécutant du code .NET) pour effectuer d’autres tâches pendant qu’il attend une opération qui ne nécessite pas de temps de calcul d’un thread managé. Par exemple, pendant que le moteur de base de données traite une requête, il n’y a rien à faire par le code .NET.

Dans les applications clientes (WinForms, WPF, etc.), le thread actuel peut être utilisé pour maintenir la réactivité de l’interface utilisateur pendant l’exécution de l’opération asynchrone. Dans les applications serveur (ASP.NET, etc.), le thread peut être utilisé pour traiter d’autres demandes entrantes, ce qui peut réduire l’utilisation de la mémoire et/ou augmenter le débit du serveur.

Dans la plupart des applications utilisant Async n’auront pas d’avantages significatifs et même pourrait être nuisible. Utilisez les tests, le profilage et le bon sens pour mesurer l’impact de Async dans votre scénario particulier avant de le valider.

Voici d’autres ressources pour en savoir plus sur Async :

-   [Vue d’ensemble de Brandon Bray de Async/await dans .NET 4,5](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   Pages de [programmation asynchrones](https://msdn.microsoft.com/library/hh191443.aspx) dans MSDN Library
-   [Comment créer des applications Web ASP.net à l’aide de Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (comprend une démonstration du débit accru du serveur)

## <a name="create-the-model"></a>Créer le modèle

Nous utiliserons le flux de travail [Code First](xref:ef6/modeling/code-first/workflows/new-database) pour créer notre modèle et générer la base de données. Toutefois, les fonctionnalités asynchrones fonctionnent avec tous les modèles EF, y compris ceux créés avec le concepteur EF.

-   Créer une application console et l’appeler **AsyncDemo**
-   Ajouter le package NuGet EntityFramework
    -   Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet **AsyncDemo**
    -   Sélectionnez **gérer les packages NuGet...**
    -   Dans la boîte de dialogue gérer les packages NuGet, sélectionnez l’onglet **en ligne** et choisissez le package **EntityFramework** .
    -   Cliquez sur **Install**.
-   Ajoutez une classe **Model.cs** avec l’implémentation suivante

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

 

## <a name="create-a-synchronous-program"></a>Créer un programme synchrone

Maintenant que nous disposons d’un modèle EF, nous allons écrire du code qui l’utilise pour effectuer un accès aux données.

-   Remplacez le contenu de **Program.cs** par le code suivant :

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

Ce code appelle la `PerformDatabaseOperations` méthode qui enregistre un nouveau **blog** dans la base de données, puis récupère tous les **blogs** de la base de données et les imprime sur la **console**. Après cela, le programme écrit un guillemet du jour sur la **console**.

Étant donné que le code est synchrone, nous pouvons observer le déroulement de l’exécution suivant lorsque nous exécutons le programme :

1.  `SaveChanges` commence à envoyer le nouveau **blog** à la base de données
2.  `SaveChanges` se termine
3.  La requête de tous les **blogs** est envoyée à la base de données
4.  La requête retourne et les résultats sont écrits dans la **console**
5.  Le devis du jour est écrit dans la **console**

![Sortie de la synchronisation](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>Rendre asynchrone

Maintenant que notre programme est opérationnel, nous pouvons commencer à utiliser les nouveaux mots clés Async et await. Nous avons apporté les modifications suivantes à Program.cs

1.  Ligne 2 : l’instruction using pour l' `System.Data.Entity` espace de noms nous donne accès aux méthodes d’extension EF Async.
2.  Ligne 4 : l’instruction using pour l' `System.Threading.Tasks` espace de noms nous permet d’utiliser le `Task` type.
3.  Ligne 12 & 18 : nous effectuons une capture en tant que tâche qui surveille la progression de `PerformSomeDatabaseOperations` (ligne 12), puis bloquent l’exécution du programme pour que cette tâche se termine une fois que tout le travail pour le programme est terminé (ligne 18).
4.  Ligne 25 : nous avons mis à jour `PerformSomeDatabaseOperations` pour être marqué comme `async` et retournent un `Task` .
5.  Ligne 35 : nous appelons maintenant la version Async de `SaveChanges` et en attendant la fin de son exécution.
6.  Ligne 42 : nous appelons maintenant la version Async de `ToList` et en attente du résultat.

Pour obtenir la liste complète des méthodes d’extension disponibles dans l' `System.Data.Entity` espace de noms, reportez-vous à la `QueryableExtensions` classe. *Vous devez également ajouter `using System.Data.Entity` à vos instructions using.*

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

Maintenant que le code est asynchrone, nous pouvons observer un autre workflow d’exécution lorsque nous exécutons le programme :

1. `SaveChanges` commence à envoyer le nouveau **blog** à la base de données  
    *Une fois la commande envoyée à la base de données, aucune durée de calcul supplémentaire n’est nécessaire sur le thread managé actuel. La `PerformDatabaseOperations` méthode retourne (même si elle n’a pas fini de s’exécuter) et le déroulement du programme dans la méthode main se poursuit.*
2. **Le devis du jour est écrit dans la console**  
    *Étant donné qu’il n’y a plus de travail à effectuer dans la méthode main, le thread managé est bloqué sur l' `Wait` appel jusqu’à ce que l’opération de base de données se termine. Une fois l’opération terminée, le reste de notre `PerformDatabaseOperations` est exécuté.*
3.  `SaveChanges` se termine  
4.  La requête de tous les **blogs** est envoyée à la base de données  
    *Là encore, le thread managé est libre d’effectuer d’autres tâches pendant le traitement de la requête dans la base de données. Étant donné que toutes les autres exécutions sont terminées, le thread s’arrêtera simplement sur l’appel d’attente.*
5.  La requête retourne et les résultats sont écrits dans la **console**  

![Sortie asynchrone](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>Le

Nous avons maintenant vu combien il est facile d’utiliser les méthodes asynchrones d’EF. Bien que les avantages de Async ne soient pas très évidents avec une application console simple, ces mêmes stratégies peuvent être appliquées dans les situations où des activités longues ou liées au réseau pourraient bloquer l’application, ou provoquer un grand nombre de threads pour augmenter l’encombrement mémoire.
