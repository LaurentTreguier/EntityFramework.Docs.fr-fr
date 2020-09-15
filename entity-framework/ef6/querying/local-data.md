---
title: Données locales-EF6
description: Données locales dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/local-data
ms.openlocfilehash: 5763424ab295ed6ceec655368fe3565a10c911d3
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073910"
---
# <a name="local-data"></a>Données locales
L’exécution d’une requête LINQ directement sur un DbSet enverra toujours une requête à la base de données, mais vous pouvez accéder aux données qui sont actuellement en mémoire à l’aide de la propriété DbSet. local. Vous pouvez également accéder aux informations supplémentaires EF en effectuant le suivi de vos entités à l’aide des méthodes DbContext. Entry et DbContext. ChangeTracker. Entries. Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.  

## <a name="using-local-to-look-at-local-data"></a>Utilisation de local pour examiner les données locales  

La propriété locale de DbSet fournit un accès simple aux entités du jeu qui font actuellement l’objet d’un suivi par le contexte et qui n’ont pas été marquées comme supprimées. L’accès à la propriété locale n’entraîne jamais l’envoi d’une requête à la base de données. Cela signifie qu’il est généralement utilisé après qu’une requête a déjà été exécutée. La méthode d’extension Load peut être utilisée pour exécuter une requête afin que le contexte effectue le suivi des résultats. Exemple :  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs from the database into the context
    context.Blogs.Load();

    // Add a new blog to the context
    context.Blogs.Add(new Blog { Name = "My New Blog" });

    // Mark one of the existing blogs as Deleted
    context.Blogs.Remove(context.Blogs.Find(1));

    // Loop over the blogs in the context.
    Console.WriteLine("In Local: ");
    foreach (var blog in context.Blogs.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }

    // Perform a query against the database.
    Console.WriteLine("\nIn DbSet query: ");
    foreach (var blog in context.Blogs)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }
}
```  

Si nous avions deux blogs dans la base de données « ADO.NET blog » avec un BlogId de 1 et un « the Visual Studio blog » avec un BlogId de 2, nous pourrions attendre la sortie suivante :  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

Cela illustre trois points :  

- Le nouveau blog « mon nouveau blog » est inclus dans la collection locale, même s’il n’a pas encore été enregistré dans la base de données. Ce blog a une clé primaire égale à zéro, car la base de données n’a pas encore généré de clé réelle pour l’entité.  
- Le « blog ADO.NET » n’est pas inclus dans la collection locale, bien qu’il soit toujours suivi par le contexte. Cela est dû au fait que nous avons supprimé le DbSet, ce qui le marque comme étant supprimé.  
- Lorsque DbSet est utilisé pour exécuter une requête, le blog marqué pour suppression (blog ADO.NET) est inclus dans les résultats et le nouveau blog (mon nouveau blog) qui n’a pas encore été enregistré dans la base de données n’est pas inclus dans les résultats. Cela est dû au fait que DbSet exécute une requête sur la base de données et que les résultats retournés reflètent toujours ce qui se trouve dans la base de données.  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a>Utilisation de l’environnement local pour ajouter et supprimer des entités dans le contexte  

La propriété locale sur DbSet retourne un [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) avec des événements raccordés de sorte qu’il reste synchronisé avec le contenu du contexte. Cela signifie que des entités peuvent être ajoutées ou supprimées de la collection locale ou du DbSet. Cela signifie également que les requêtes qui apportent de nouvelles entités dans le contexte entraînent la mise à jour de la collection locale avec ces entités. Exemple :  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework")).Load();  

    // Get the local collection and make some changes to it
    var localPosts = context.Posts.Local;
    localPosts.Add(new Post { Name = "What's New in EF" });
    localPosts.Remove(context.Posts.Find(1));  

    // Loop over the posts in the context.
    Console.WriteLine("In Local after entity-framework query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }

    var post1 = context.Posts.Find(1);
    Console.WriteLine(
        "State of post 1: {0} is {1}",
        post1.Name,  
        context.Entry(post1).State);  

    // Query some more posts from the database
    context.Posts.Where(p => p.Tags.Contains("asp.net").Load();  

    // Loop over the posts in the context again.
    Console.WriteLine("\nIn Local after asp.net query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }
}
```  

En supposant que nous avions quelques publications marquées avec’Entity-Framework’et’asp.net', la sortie peut ressembler à ceci :  

```console
In Local after entity-framework query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
State of post 1: EF Beginners Guide is Deleted

In Local after asp.net query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
Found 4: ASP.NET Beginners Guide with state Unchanged
```  

Cela illustre trois points :  

- La nouvelle publication « nouveautés dans EF » qui a été ajoutée à la collection locale est suivie par le contexte dans l’état ajouté. Elle est donc insérée dans la base de données lorsque SaveChanges est appelé.  
- La publication qui a été supprimée de la collection locale (Guide du débutant d’EF) est désormais marquée comme supprimée dans le contexte. Elle sera donc supprimée de la base de données lorsque SaveChanges est appelé.  
- La publication supplémentaire (Guide du débutant ASP.NET) chargée dans le contexte avec la seconde requête est automatiquement ajoutée à la collection locale.  

Une dernière chose à noter sur la version locale est qu’il s’agit d’une performance ObservableCollection qui n’est pas idéale pour un grand nombre d’entités. Par conséquent, si vous traitez des milliers d’entités dans votre contexte, il peut être déconseillé d’utiliser local.  

## <a name="using-local-for-wpf-data-binding"></a>Utilisation de la liaison de données locale pour WPF  

La propriété locale sur DbSet peut être utilisée directement pour la liaison de données dans une application WPF, car il s’agit d’une instance de ObservableCollection. Comme décrit dans les sections précédentes, cela signifie qu’elle sera automatiquement synchronisée avec le contenu du contexte et que le contenu du contexte sera automatiquement synchronisé avec lui. Notez que vous devez préremplir la collection locale avec des données pour qu’il n’y ait rien à lier, car local n’entraîne jamais de requête de base de données.  

Il ne s’agit pas d’un emplacement approprié pour un exemple de liaison de données WPF complète, mais les éléments clés sont :  

- Configurer une source de liaison  
- Liez-le à la propriété locale de votre ensemble  
- Remplir localement à l’aide d’une requête dans la base de données.  

## <a name="wpf-binding-to-navigation-properties"></a>Liaison WPF avec les propriétés de navigation  

Si vous effectuez une liaison de données maître/détail, vous pouvez lier l’affichage détails à une propriété de navigation de l’une de vos entités. Un moyen simple d’effectuer ce travail consiste à utiliser ObservableCollection pour la propriété de navigation. Exemple :  

``` csharp
public class Blog
{
    private readonly ObservableCollection<Post> _posts =
        new ObservableCollection<Post>();

    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual ObservableCollection<Post> Posts
    {
        get { return _posts; }
    }
}
```  

## <a name="using-local-to-clean-up-entities-in-savechanges"></a>Utilisation de local pour nettoyer les entités dans SaveChanges  

Dans la plupart des cas, les entités supprimées d’une propriété de navigation ne sont pas automatiquement marquées comme supprimées dans le contexte. Par exemple, si vous supprimez un objet post de la collection blog. publications, cette publication ne sera pas automatiquement supprimée lorsque SaveChanges est appelé. Si vous avez besoin de la supprimer, vous devrez peut-être trouver ces entités en suspens et les marquer comme supprimées avant d’appeler SaveChanges ou dans le cadre d’une SaveChanges substituée. Exemple :  

``` csharp
public override int SaveChanges()
{
    foreach (var post in this.Posts.Local.ToList())
    {
        if (post.Blog == null)
        {
            this.Posts.Remove(post);
        }
    }

    return base.SaveChanges();
}
```  

Le code ci-dessus utilise la collection locale pour rechercher toutes les publications et marque toutes celles qui n’ont pas de référence de blog comme étant supprimées. L’appel de ToList est requis, car sinon, la collection est modifiée par l’appel Remove pendant son énumération. Dans la plupart des autres cas, vous pouvez interroger directement la propriété locale sans utiliser ToList en premier.  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a>Utilisation des paramètres local et ToBindingList pour la liaison de données Windows Forms  

Windows Forms ne prend pas en charge la liaison de données de fidélité complète avec ObservableCollection directement. Toutefois, vous pouvez toujours utiliser la propriété locale DbSet pour la liaison de données afin d’obtenir tous les avantages décrits dans les sections précédentes. Pour cela, vous devez utiliser la méthode d’extension ToBindingList, qui crée une implémentation [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) sauvegardée par l’ObservableCollection local.  

Il ne s’agit pas d’un emplacement approprié pour un exemple de liaison de données Windows Forms complète, mais les éléments clés sont :  

- Configurer une source de liaison d’objet  
- Liez-le à la propriété locale de votre jeu à l’aide de local. ToBindingList ()  
- Remplir localement à l’aide d’une requête dans la base de données  

## <a name="getting-detailed-information-about-tracked-entities"></a>Obtention d’informations détaillées sur les entités suivies  

La plupart des exemples de cette série utilisent la méthode Entry pour retourner une instance DbEntityEntry pour une entité. Cet objet d’entrée sert ensuite de point de départ pour la collecte d’informations sur l’entité, par exemple son état actuel, ainsi que pour l’exécution d’opérations sur l’entité, telles que le chargement explicite d’une entité associée.  

Les méthodes d’entrée retournent des objets DbEntityEntry pour la plupart ou toutes les entités faisant l’objet d’un suivi par le contexte. Cela vous permet de collecter des informations ou d’effectuer des opérations sur de nombreuses entités plutôt que sur une seule entrée. Exemple :  

``` csharp
using (var context = new BloggingContext())
{
    // Load some entities into the context
    context.Blogs.Load();
    context.Authors.Load();
    context.Readers.Load();

    // Make some changes
    context.Blogs.Find(1).Title = "The New ADO.NET Blog";
    context.Blogs.Remove(context.Blogs.Find(2));
    context.Authors.Add(new Author { Name = "Jane Doe" });
    context.Readers.Find(1).Username = "johndoe1987";

    // Look at the state of all entities in the context
    Console.WriteLine("All tracked entities: ");
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Find modified entities of any type
    Console.WriteLine("\nAll modified entities: ");
    foreach (var entry in context.ChangeTracker.Entries()
                              .Where(e => e.State == EntityState.Modified))
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Get some information about just the tracked blogs
    Console.WriteLine("\nTracked blogs: ");
    foreach (var entry in context.ChangeTracker.Entries<Blog>())
    {
        Console.WriteLine(
            "Found Blog {0}: {1} with original Name {2}",
            entry.Entity.BlogId,  
            entry.Entity.Name,
            entry.Property(p => p.Name).OriginalValue);
    }

    // Find all people (author or reader)
    Console.WriteLine("\nPeople: ");
    foreach (var entry in context.ChangeTracker.Entries<IPerson>())
    {
        Console.WriteLine("Found Person {0}", entry.Entity.Name);
    }
}
```  

Vous remarquerez que nous introduisons une classe Author et Reader dans l’exemple, ces deux classes implémentent l’interface IPerson.  

``` csharp
public class Author : IPerson
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public string Biography { get; set; }
}

public class Reader : IPerson
{
    public int ReaderId { get; set; }
    public string Name { get; set; }
    public string Username { get; set; }
}

public interface IPerson
{
    string Name { get; }
}
```  

Supposons que nous ayons les données suivantes dans la base de données :

Blog avec BlogId = 1 et Name = 'ADO.NET blog'  
Blog avec BlogId = 2 et Name = 'blog de Visual Studio'  
Blog avec BlogId = 3 et Name = ' .NET Framework blog'  
Auteur avec réécriture = 1 et nom = « joe bloggs »  
Lecteur avec ReaderId = 1 et Name = « John Doe »  

La sortie de l’exécution du code est la suivante :  

```console
All tracked entities:
Found entity of type Blog with state Modified
Found entity of type Blog with state Deleted
Found entity of type Blog with state Unchanged
Found entity of type Author with state Unchanged
Found entity of type Author with state Added
Found entity of type Reader with state Modified

All modified entities:
Found entity of type Blog with state Modified
Found entity of type Reader with state Modified

Tracked blogs:
Found Blog 1: The New ADO.NET Blog with original Name ADO.NET Blog
Found Blog 2: The Visual Studio Blog with original Name The Visual Studio Blog
Found Blog 3: .NET Framework Blog with original Name .NET Framework Blog

People:
Found Person John Doe
Found Person Joe Bloggs
Found Person Jane Doe
```  

Ces exemples illustrent plusieurs points :  

- Les méthodes d’entrée retournent des entrées pour les entités dans tous les États, y compris Deleted. Comparez ce paramètre au paramètre local qui exclut les entités supprimées.  
- Les entrées de tous les types d’entités sont retournées lorsque la méthode des entrées non génériques est utilisée. Lorsque la méthode des entrées génériques est utilisée, les entrées sont retournées uniquement pour les entités qui sont des instances du type générique. Il a été utilisé ci-dessus pour obtenir des entrées pour tous les blogs. Elle a également été utilisée pour obtenir des entrées pour toutes les entités qui implémentent IPerson. Cela démontre que le type générique n’a pas besoin d’être un type d’entité réel.  
- LINQ to Objects peut être utilisé pour filtrer les résultats retournés. Il a été utilisé ci-dessus pour rechercher des entités de tout type, à condition qu’elles soient modifiées.  

Notez que les instances DbEntityEntry contiennent toujours une entité non null. Les entrées de relation et les entrées stub ne sont pas représentées en tant qu’instances DbEntityEntry. il n’est donc pas nécessaire de les filtrer.
