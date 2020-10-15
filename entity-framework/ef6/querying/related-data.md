---
title: Chargement des entités associées-EF6
description: Chargement des entités associées dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/related-data
ms.openlocfilehash: 30d5bcff0696b4886655f5413e4878f1a611cf88
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064690"
---
# <a name="loading-related-entities"></a>Chargement des entités associées

Entity Framework prend en charge trois méthodes de chargement des données associées : le chargement hâtif, le chargement différé et le chargement explicite. Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.

## <a name="eagerly-loading"></a>Chargement hâtif

Le chargement hâtif est le processus par lequel une requête pour un type d’entité charge également des entités associées dans le cadre de la requête. Le chargement hâtif est obtenu à l’aide de la méthode Include. Par exemple, les requêtes ci-dessous chargent les blogs et toutes les publications associées à chaque blog.

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts.
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts
    // using a string to specify the relationship.
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts
    // using a string to specify the relationship.
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```

> [!NOTE]
> Include est une méthode d’extension dans l’espace de noms System. Data. Entity. Assurez-vous que vous utilisez cet espace de noms.

### <a name="eagerly-loading-multiple-levels"></a>Chargement à plusieurs niveaux

Il est également possible de charger de façon dynamique plusieurs niveaux d’entités associées. Les requêtes ci-dessous montrent des exemples de la procédure à suivre pour les propriétés de navigation de collection et de référence.  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar.
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships.
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships.
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

> [!NOTE]
> Il n’est pas possible actuellement de filtrer les entités associées qui sont chargées. Include affichera toujours toutes les entités associées.  

## <a name="lazy-loading"></a>Chargement différé

Le chargement différé est le processus par lequel une entité ou une collection d’entités est chargée automatiquement à partir de la base de données la première fois qu’une propriété faisant référence à l’entité/aux entités est accédée. Lors de l’utilisation des types d’entités POCO, le chargement différé s’effectue en créant des instances de types de proxy dérivés, puis en substituant les propriétés virtuelles pour ajouter le raccordement de chargement. Par exemple, lors de l’utilisation de la classe d’entité blog définie ci-dessous, les publications associées sont chargées la première fois que la propriété de navigation publications est accédée :

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

### <a name="turn-lazy-loading-off-for-serialization"></a>Désactivation du chargement différé pour la sérialisation

Le chargement différé et la sérialisation ne sont pas bien confondus. Si vous n’êtes pas prudent, vous pouvez terminer l’interrogation de votre base de données tout simplement parce que le chargement différé est activé. La plupart des sérialiseurs fonctionnent en accédant à chaque propriété sur une instance d’un type. L’accès aux propriétés déclenche un chargement différé, si bien que davantage d’entités sont sérialisées. Sur ces entités, les propriétés sont accessibles, et d’autres entités sont chargées. Il est recommandé de désactiver le chargement différé avant de sérialiser une entité. Les sections suivantes montrent comment effectuer cette opération.

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a>Désactivation du chargement différé pour des propriétés de navigation spécifiques

Le chargement différé de la collection de publications peut être désactivé en rendant la propriété des publications non virtuelle :

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public ICollection<Post> Posts { get; set; }
}
```

Le chargement de la collection de publications peut toujours être effectué à l’aide du chargement hâtif (consultez *chargement hâtif* ci-dessus) ou de la méthode Load (voir *chargement explicite* ci-dessous).

### <a name="turn-off-lazy-loading-for-all-entities"></a>Désactiver le chargement différé pour toutes les entités

Le chargement différé peut être désactivé pour toutes les entités du contexte en définissant un indicateur sur la propriété de configuration. Par exemple :

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

Le chargement d’entités associées peut toujours être effectué à l’aide du chargement hâtif (consultez *chargement hâtif* ci-dessus) ou de la méthode Load (voir *chargement explicite* ci-dessous).

## <a name="explicitly-loading"></a>Chargement explicite

Même si le chargement différé est désactivé, il est toujours possible de charger tardivement les entités associées, mais cela doit être effectué avec un appel explicite. Pour ce faire, vous utilisez la méthode Load sur l’entrée de l’entité associée. Par exemple :

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post.
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string.
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog.
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog).Collection("Posts").Load();
}
```

> [!NOTE]
> La méthode de référence doit être utilisée lorsqu’une entité a une propriété de navigation vers une autre entité unique. En revanche, la méthode de collection doit être utilisée lorsqu’une entité a une propriété de navigation vers une collection d’autres entités.

### <a name="applying-filters-when-explicitly-loading-related-entities"></a>Application de filtres lors du chargement explicite d’entités associées

La méthode de requête fournit l’accès à la requête sous-jacente que Entity Framework utilisera lors du chargement des entités associées. Vous pouvez ensuite utiliser LINQ pour appliquer des filtres à la requête avant de l’exécuter avec un appel à une méthode d’extension LINQ comme ToList, Load, etc. La méthode de requête peut être utilisée avec les propriétés de navigation de référence et de collection, mais elle est particulièrement utile pour les collections où elle peut être utilisée pour charger uniquement une partie de la collection. Par exemple :

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog.
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```

Lors de l’utilisation de la méthode de requête, il est généralement préférable de désactiver le chargement différé pour la propriété de navigation. En effet, dans le cas contraire, l’ensemble de la collection peut être chargé automatiquement par le mécanisme de chargement différé avant ou après l’exécution de la requête filtrée.

> [!NOTE]
> Alors que la relation peut être spécifiée en tant que chaîne au lieu d’une expression lambda, le IQueryable retourné n’est pas générique lorsqu’une chaîne est utilisée et la méthode de cast est généralement nécessaire avant toute opération utile.

## <a name="using-query-to-count-related-entities-without-loading-them"></a>Utilisation de Query pour compter les entités associées sans les charger

Il est parfois utile de savoir combien d’entités sont liées à une autre entité de la base de données sans réellement avoir à charger toutes ces entités. Pour ce faire, vous pouvez utiliser la méthode Query avec la méthode Count LINQ. Par exemple :

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has.
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```
