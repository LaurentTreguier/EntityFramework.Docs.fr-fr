---
title: Chargement des entités associées-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
ms.openlocfilehash: f40034475ed6659b60ab4317605fd1d802218d69
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565320"
---
# <a name="loading-related-entities"></a><span data-ttu-id="15101-102">Chargement des entités associées</span><span class="sxs-lookup"><span data-stu-id="15101-102">Loading Related Entities</span></span>
<span data-ttu-id="15101-103">Entity Framework prend en charge trois méthodes de chargement des données associées: le chargement hâtif, le chargement différé et le chargement explicite.</span><span class="sxs-lookup"><span data-stu-id="15101-103">Entity Framework supports three ways to load related data - eager loading, lazy loading and explicit loading.</span></span> <span data-ttu-id="15101-104">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="15101-104">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="eagerly-loading"></a><span data-ttu-id="15101-105">Chargement hâtif</span><span class="sxs-lookup"><span data-stu-id="15101-105">Eagerly Loading</span></span>  

<span data-ttu-id="15101-106">Le chargement hâtif est le processus par lequel une requête pour un type d’entité charge également des entités associées dans le cadre de la requête.</span><span class="sxs-lookup"><span data-stu-id="15101-106">Eager loading is the process whereby a query for one type of entity also loads related entities as part of the query.</span></span> <span data-ttu-id="15101-107">Le chargement hâtif est obtenu à l’aide de la méthode Include.</span><span class="sxs-lookup"><span data-stu-id="15101-107">Eager loading is achieved by use of the Include method.</span></span> <span data-ttu-id="15101-108">Par exemple, les requêtes ci-dessous chargent les blogs et toutes les publications associées à chaque blog.</span><span class="sxs-lookup"><span data-stu-id="15101-108">For example, the queries below will load blogs and all the posts related to each blog.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts  
    // using a string to specify the relationship
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts  
    // using a string to specify the relationship
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```  

<span data-ttu-id="15101-109">Notez que include est une méthode d’extension dans l’espace de noms System. Data. Entity. Assurez-vous que vous utilisez cet espace de noms.</span><span class="sxs-lookup"><span data-stu-id="15101-109">Note that Include is an extension method in the System.Data.Entity namespace so make sure you are using that namespace.</span></span>  

### <a name="eagerly-loading-multiple-levels"></a><span data-ttu-id="15101-110">Chargement à plusieurs niveaux</span><span class="sxs-lookup"><span data-stu-id="15101-110">Eagerly loading multiple levels</span></span>  

<span data-ttu-id="15101-111">Il est également possible de charger de façon dynamique plusieurs niveaux d’entités associées.</span><span class="sxs-lookup"><span data-stu-id="15101-111">It is also possible to eagerly load multiple levels of related entities.</span></span> <span data-ttu-id="15101-112">Les requêtes ci-dessous montrent des exemples de la procédure à suivre pour les propriétés de navigation de collection et de référence.</span><span class="sxs-lookup"><span data-stu-id="15101-112">The queries below show examples of how to do this for both collection and reference navigation properties.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

<span data-ttu-id="15101-113">Notez qu’il n’est pas possible actuellement de filtrer les entités associées qui sont chargées.</span><span class="sxs-lookup"><span data-stu-id="15101-113">Note that it is not currently possible to filter which related entities are loaded.</span></span> <span data-ttu-id="15101-114">Include affichera toujours toutes les entités associées.</span><span class="sxs-lookup"><span data-stu-id="15101-114">Include will always bring in all related entities.</span></span>  

## <a name="lazy-loading"></a><span data-ttu-id="15101-115">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="15101-115">Lazy Loading</span></span>  

<span data-ttu-id="15101-116">Le chargement différé est le processus par lequel une entité ou une collection d’entités est chargée automatiquement à partir de la base de données la première fois qu’une propriété faisant référence à l’entité/aux entités est accédée.</span><span class="sxs-lookup"><span data-stu-id="15101-116">Lazy loading is the process whereby an entity or collection of entities is automatically loaded from the database the first time that a property referring to the entity/entities is accessed.</span></span> <span data-ttu-id="15101-117">Lors de l’utilisation des types d’entités POCO, le chargement différé s’effectue en créant des instances de types de proxy dérivés, puis en substituant les propriétés virtuelles pour ajouter le raccordement de chargement.</span><span class="sxs-lookup"><span data-stu-id="15101-117">When using POCO entity types, lazy loading is achieved by creating instances of derived proxy types and then overriding virtual properties to add the loading hook.</span></span> <span data-ttu-id="15101-118">Par exemple, lors de l’utilisation de la classe d’entité blog définie ci-dessous, les publications associées sont chargées la première fois que la propriété de navigation publications est accédée:</span><span class="sxs-lookup"><span data-stu-id="15101-118">For example, when using the Blog entity class defined below, the related Posts will be loaded the first time the Posts navigation property is accessed:</span></span>  

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

### <a name="turn-lazy-loading-off-for-serialization"></a><span data-ttu-id="15101-119">Désactivation du chargement différé pour la sérialisation</span><span class="sxs-lookup"><span data-stu-id="15101-119">Turn lazy loading off for serialization</span></span>  

<span data-ttu-id="15101-120">Le chargement différé et la sérialisation ne sont pas bien confondus. Si vous n’êtes pas prudent, vous pouvez terminer l’interrogation de votre base de données tout simplement parce que le chargement différé est activé.</span><span class="sxs-lookup"><span data-stu-id="15101-120">Lazy loading and serialization don’t mix well, and if you aren’t careful you can end up querying for your entire database just because lazy loading is enabled.</span></span> <span data-ttu-id="15101-121">La plupart des sérialiseurs fonctionnent en accédant à chaque propriété sur une instance d’un type.</span><span class="sxs-lookup"><span data-stu-id="15101-121">Most serializers work by accessing each property on an instance of a type.</span></span> <span data-ttu-id="15101-122">L’accès aux propriétés déclenche un chargement différé, si bien que davantage d’entités sont sérialisées.</span><span class="sxs-lookup"><span data-stu-id="15101-122">Property access triggers lazy loading, so more entities get serialized.</span></span> <span data-ttu-id="15101-123">Sur ces entités, les propriétés sont accessibles, et d’autres entités sont chargées.</span><span class="sxs-lookup"><span data-stu-id="15101-123">On those entities properties are accessed, and even more entities are loaded.</span></span> <span data-ttu-id="15101-124">Il est recommandé de désactiver le chargement différé avant de sérialiser une entité.</span><span class="sxs-lookup"><span data-stu-id="15101-124">It’s a good practice to turn lazy loading off before you serialize an entity.</span></span> <span data-ttu-id="15101-125">Les sections suivantes montrent comment procéder.</span><span class="sxs-lookup"><span data-stu-id="15101-125">The following sections show how to do this.</span></span>  

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a><span data-ttu-id="15101-126">Désactivation du chargement différé pour des propriétés de navigation spécifiques</span><span class="sxs-lookup"><span data-stu-id="15101-126">Turning off lazy loading for specific navigation properties</span></span>  

<span data-ttu-id="15101-127">Le chargement différé de la collection de publications peut être désactivé en rendant la propriété des publications non virtuelle:</span><span class="sxs-lookup"><span data-stu-id="15101-127">Lazy loading of the Posts collection can be turned off by making the Posts property non-virtual:</span></span>  

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

<span data-ttu-id="15101-128">Le chargement de la collection de publications peut toujours être effectué à l’aide du chargement hâtif (consultez *chargement hâtif* ci-dessus) ou de la méthode Load (voir *chargement explicite* ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="15101-128">Loading of the Posts collection can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>  

### <a name="turn-off-lazy-loading-for-all-entities"></a><span data-ttu-id="15101-129">Désactiver le chargement différé pour toutes les entités</span><span class="sxs-lookup"><span data-stu-id="15101-129">Turn off lazy loading for all entities</span></span>  

<span data-ttu-id="15101-130">Le chargement différé peut être désactivé pour toutes les entités du contexte en définissant un indicateur sur la propriété de configuration.</span><span class="sxs-lookup"><span data-stu-id="15101-130">Lazy loading can be turned off for all entities in the context by setting a flag on the Configuration property.</span></span> <span data-ttu-id="15101-131">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="15101-131">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```  

<span data-ttu-id="15101-132">Le chargement d’entités associées peut toujours être effectué à l’aide du chargement hâtif (consultez *chargement hâtif* ci-dessus) ou de la méthode Load (voir *chargement explicite* ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="15101-132">Loading of related entities can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>  

## <a name="explicitly-loading"></a><span data-ttu-id="15101-133">Chargement explicite</span><span class="sxs-lookup"><span data-stu-id="15101-133">Explicitly Loading</span></span>  

<span data-ttu-id="15101-134">Même si le chargement différé est désactivé, il est toujours possible de charger tardivement les entités associées, mais cela doit être effectué avec un appel explicite.</span><span class="sxs-lookup"><span data-stu-id="15101-134">Even with lazy loading disabled it is still possible to lazily load related entities, but it must be done with an explicit call.</span></span> <span data-ttu-id="15101-135">Pour ce faire, vous utilisez la méthode Load sur l’entrée de l’entité associée.</span><span class="sxs-lookup"><span data-stu-id="15101-135">To do so you use the Load method on the related entity’s entry.</span></span> <span data-ttu-id="15101-136">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="15101-136">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string  
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog  
    // using a string to specify the relationship
    context.Entry(blog).Collection("Posts").Load();
}
```  

<span data-ttu-id="15101-137">Notez que la méthode de référence doit être utilisée lorsqu’une entité a une propriété de navigation vers une autre entité unique.</span><span class="sxs-lookup"><span data-stu-id="15101-137">Note that the Reference method should be used when an entity has a navigation property to another single entity.</span></span> <span data-ttu-id="15101-138">En revanche, la méthode de collection doit être utilisée lorsqu’une entité a une propriété de navigation vers une collection d’autres entités.</span><span class="sxs-lookup"><span data-stu-id="15101-138">On the other hand, the Collection method should be used when an entity has a navigation property to a collection of other entities.</span></span>  

### <a name="applying-filters-when-explicitly-loading-related-entities"></a><span data-ttu-id="15101-139">Application de filtres lors du chargement explicite d’entités associées</span><span class="sxs-lookup"><span data-stu-id="15101-139">Applying filters when explicitly loading related entities</span></span>  

<span data-ttu-id="15101-140">La méthode de requête fournit l’accès à la requête sous-jacente que Entity Framework utilisera lors du chargement des entités associées.</span><span class="sxs-lookup"><span data-stu-id="15101-140">The Query method provides access to the underlying query that Entity Framework will use when loading related entities.</span></span> <span data-ttu-id="15101-141">Vous pouvez ensuite utiliser LINQ pour appliquer des filtres à la requête avant de l’exécuter avec un appel à une méthode d’extension LINQ comme ToList, Load, etc. La méthode de requête peut être utilisée avec les propriétés de navigation de référence et de collection, mais elle est particulièrement utile pour les collections où elle peut être utilisée pour charger uniquement une partie de la collection.</span><span class="sxs-lookup"><span data-stu-id="15101-141">You can then use LINQ to apply filters to the query before executing it with a call to a LINQ extension method such as ToList, Load, etc. The Query method can be used with both reference and collection navigation properties but is most useful for collections where it can be used to load only part of the collection.</span></span> <span data-ttu-id="15101-142">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="15101-142">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog  
    // using a string to specify the relationship  
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```  

<span data-ttu-id="15101-143">Lors de l’utilisation de la méthode de requête, il est généralement préférable de désactiver le chargement différé pour la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="15101-143">When using the Query method it is usually best to turn off lazy loading for the navigation property.</span></span> <span data-ttu-id="15101-144">En effet, dans le cas contraire, l’ensemble de la collection peut être chargé automatiquement par le mécanisme de chargement différé avant ou après l’exécution de la requête filtrée.</span><span class="sxs-lookup"><span data-stu-id="15101-144">This is because otherwise the entire collection may get loaded automatically by the lazy loading mechanism either before or after the filtered query has been executed.</span></span>  

<span data-ttu-id="15101-145">Notez que même si la relation peut être spécifiée en tant que chaîne au lieu d’une expression lambda, le IQueryable retourné n’est pas générique lorsqu’une chaîne est utilisée et la méthode de cast est généralement nécessaire avant toute opération utile.</span><span class="sxs-lookup"><span data-stu-id="15101-145">Note that while the relationship can be specified as a string instead of a lambda expression, the returned IQueryable is not generic when a string is used and so the Cast method is usually needed before anything useful can be done with it.</span></span>  

## <a name="using-query-to-count-related-entities-without-loading-them"></a><span data-ttu-id="15101-146">Utilisation de Query pour compter les entités associées sans les charger</span><span class="sxs-lookup"><span data-stu-id="15101-146">Using Query to count related entities without loading them</span></span>  

<span data-ttu-id="15101-147">Il est parfois utile de savoir combien d’entités sont liées à une autre entité de la base de données sans réellement avoir à charger toutes ces entités.</span><span class="sxs-lookup"><span data-stu-id="15101-147">Sometimes it is useful to know how many entities are related to another entity in the database without actually incurring the cost of loading all those entities.</span></span> <span data-ttu-id="15101-148">Pour ce faire, vous pouvez utiliser la méthode Query avec la méthode Count LINQ.</span><span class="sxs-lookup"><span data-stu-id="15101-148">The Query method with the LINQ Count method can be used to do this.</span></span> <span data-ttu-id="15101-149">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="15101-149">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has  
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```  
