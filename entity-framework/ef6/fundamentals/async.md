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
# <a name="async-query-and-save"></a>Async requête et enregistrer
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

EF6 a introduit le soutien pour la requête asynchrone et enregistrer en utilisant [l’async et attendre les mots clés](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) qui ont été introduits dans .NET 4.5. Bien que toutes les applications ne puissent pas bénéficier d’asynchrony, elles peuvent être utilisées pour améliorer la réactivité des clients et l’évolutivité du serveur lors de la gestion de tâches à long terme, réseau ou I/O-bound.

## <a name="when-to-really-use-async"></a>Quand vraiment utiliser async

Le but de cette procédure pas à pas est d’introduire les concepts async d’une manière qui facilite l’observation de la différence entre l’exécution asynchrone et l’exécution synchrone du programme. Cette procédure pas à pas n’a pour but d’illustrer aucun des scénarios clés où la programmation async offre des avantages.

La programmation Async est principalement axée sur la libération du thread géré actuel (thread exécutant .NET code) pour faire d’autres travaux pendant qu’il attend une opération qui ne nécessite pas de temps de calcul à partir d’un thread géré. Par exemple, alors que le moteur de base de données traite une requête, il n’y a rien à faire par code .NET.

Dans les applications client (WinForms, WPF, etc.), le thread actuel peut être utilisé pour garder l’interface utilisateur réactive pendant que l’opération async est effectuée. Dans les applications serveur (ASP.NET etc.), le thread peut être utilisé pour traiter d’autres demandes entrantes - cela peut réduire l’utilisation de la mémoire et/ou augmenter le débit du serveur.

Dans la plupart des applications utilisant async n’aura pas d’avantages notables et pourrait même être préjudiciable. Utilisez des tests, du profilage et du bon sens pour mesurer l’impact de l’async dans votre scénario particulier avant de vous y engager.

Voici quelques ressources supplémentaires pour en savoir plus sur async :

-   [Aperçu de Brandon Bray de async/await in .NET 4.5](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   [Pages de programmation asynchrones](https://msdn.microsoft.com/library/hh191443.aspx) dans la bibliothèque MSDN
-   [Comment construire ASP.NET applications Web à l’aide d’Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (inclut une démo de débit serveur accru)

## <a name="create-the-model"></a>Créer le modèle

Nous utiliserons le flux de [travail Code First](~/ef6/modeling/code-first/workflows/new-database.md) pour créer notre modèle et générer la base de données, mais la fonctionnalité asynchrone fonctionnera avec tous les modèles EF, y compris ceux créés avec le concepteur EF.

-   Créez une application console et appelez-la **AsyncDemo**
-   Ajouter le forfait EntityFramework NuGet
    -   Dans Solution Explorer, cliquez à droite sur le projet **AsyncDemo**
    -   Sélectionnez **Gérer les forfaits NuGet...**
    -   Dans le dialogue Manage NuGet Packages, Sélectionnez l’onglet **En ligne** et choisissez le forfait **EntityFramework**
    -   Cliquez **sur Installation**
-   Ajouter une **classe Model.cs** avec la mise en œuvre suivante

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

Maintenant que nous avons un modèle EF, écrivons un code qui l’utilise pour effectuer un accès aux données.

-   Remplacer le contenu de **Program.cs** par le code suivant

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

Ce code appelle la méthode **PerformDatabaseOperations** qui enregistre un nouveau **blog** à la base de données, puis récupère tous les **blogs** de la base de données et les imprime à la **console**. Après cela, le programme écrit une citation du jour à la **Console**.

Étant donné que le code est synchrone, nous pouvons observer le flux d’exécution suivant lorsque nous exécuteons le programme :

1.  **SaveChanges** commence à pousser le nouveau **blog** à la base de données
2.  **SaveChanges** complète
3.  La requête pour tous les **blogs** est envoyée à la base de données
4.  Les retours et les résultats de requête sont écrits sur **console**
5.  Citation du jour est écrit à **Console**

![Synchroniser la sortie](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>Le rendre asynchrone

Maintenant que nous avons notre programme en place et en cours d’exécution, nous pouvons commencer à faire usage du nouvel async et attendre des mots clés. Nous avons apporté les changements suivants à Program.cs

1.  Ligne 2 : L’instruction d’utilisation de l’espace de nom **System.Data.Entity** nous donne accès aux méthodes d’extension d’async EF.
2.  Ligne 4 : L’instruction d’utilisation de l’espace de nom **System.Threading.Tasks** nous permet d’utiliser le type **De tâche.**
3.  Ligne 12 & 18: Nous capturons comme tâche qui surveille les progrès de **PerformSomeDatabaseOperations** (ligne 12) et puis le blocage de l’exécution du programme pour cette tâche à accomplir une fois que tout le travail pour le programme est fait (ligne 18).
4.  Ligne 25: Nous avons mis à jour **PerformSomeDatabaseOperations** pour être marqué comme **async** et retourner une **tâche**.
5.  Ligne 35 : Nous appelons maintenant la version Async de SaveChanges et attendons son achèvement.
6.  Ligne 42: Nous appelons maintenant la version Async de ToList et en attente sur le résultat.

Pour une liste complète des méthodes d’extension disponibles dans l’espace de nom System.Data.Entity, consultez la classe RequêtesExtensions. *Vous devrez également ajouter "utiliser System.Data.Entity" à votre utilisation des instructions.*

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

Maintenant que le code est asynchrone, nous pouvons observer un flux d’exécution différent lorsque nous relérons le programme:

1. **SaveChanges** commence à pousser le nouveau **blog** à la base de données  
    *Une fois que la commande est envoyée à la base de données, plus de temps de calcul n’est nécessaire sur le thread géré actuel. La méthode **PerformDatabaseOperations** revient (même si elle n’a pas fini l’exécution) et le flux du programme dans la méthode Principale se poursuit.*
2. **Citation du jour est écrit à Console**  
    *Comme il n’y a plus de travail à faire dans la méthode Principale, le thread géré est bloqué sur l’appel d’attente jusqu’à ce que l’opération de base de données se termine. Une fois qu’il sera terminé, le reste de nos **PerformDatabaseOperations** sera exécuté.*
3.  **SaveChanges** complète  
4.  La requête pour tous les **blogs** est envoyée à la base de données  
    *Encore une fois, le thread géré est libre de faire d’autres travaux pendant que la requête est traitée dans la base de données. Puisque toutes les autres exécutions sont terminées, le fil s’arrêtera sur l’appel d’attente cependant.*
5.  Les retours et les résultats de requête sont écrits sur **console**  

![Sortie Async](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>Le plat à emporter

Nous avons maintenant vu combien il est facile d’utiliser les méthodes asynchrones d’EF. Bien que les avantages de l’async ne soient pas très évidents avec une simple application de console, ces mêmes stratégies peuvent être appliquées dans des situations où des activités à long terme ou reliées au réseau pourraient autrement bloquer l’application, ou provoquer un grand nombre de threads pour augmenter l’empreinte mémoire.
