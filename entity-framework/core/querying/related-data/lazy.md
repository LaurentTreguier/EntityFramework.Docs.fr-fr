---
title: Chargement différé de la EF Core de données associées
description: Chargement différé de données associées avec Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/lazy
ms.openlocfilehash: 55622b9c5a8f70ef4e7246d6eb14678036948f18
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635456"
---
# <a name="lazy-loading-of-related-data"></a><span data-ttu-id="ffcbb-103">Chargement différé de données associées</span><span class="sxs-lookup"><span data-stu-id="ffcbb-103">Lazy Loading of Related Data</span></span>

## <a name="lazy-loading-with-proxies"></a><span data-ttu-id="ffcbb-104">Chargement différé avec des proxies</span><span class="sxs-lookup"><span data-stu-id="ffcbb-104">Lazy loading with proxies</span></span>

<span data-ttu-id="ffcbb-105">La façon la plus simple d’utiliser le chargement différé est d’installer le package [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) et de l’activer avec un appel à `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="ffcbb-105">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="ffcbb-106">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ffcbb-106">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="ffcbb-107">Ou bien, lors de l’utilisation d’AddDbContext :</span><span class="sxs-lookup"><span data-stu-id="ffcbb-107">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="ffcbb-108">EF Core active ensuite le chargement différé pour n’importe quelle propriété de navigation qui peut être substituée, c’est-à-dire qui doit être `virtual` et sur une classe qui peut être héritée.</span><span class="sxs-lookup"><span data-stu-id="ffcbb-108">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="ffcbb-109">Par exemple, dans les entités suivantes, les propriétés de navigation `Post.Blog` et `Blog.Posts` seront chargées en différé.</span><span class="sxs-lookup"><span data-stu-id="ffcbb-109">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

> [!WARNING]
> <span data-ttu-id="ffcbb-110">Le chargement différé peut entraîner des allers-retours supplémentaires sur la base de données (le problème appelé N + 1). vous devez donc veiller à éviter cela.</span><span class="sxs-lookup"><span data-stu-id="ffcbb-110">Lazy loading can cause unneeded extra database roundtrips to occur (the so-called N+1 problem), and care should be taken to avoid this.</span></span> <span data-ttu-id="ffcbb-111">Pour plus d’informations, consultez la [section relative aux performances](xref:core/performance/efficient-querying#beware-of-lazy-loading) .</span><span class="sxs-lookup"><span data-stu-id="ffcbb-111">See the [performance section](xref:core/performance/efficient-querying#beware-of-lazy-loading) for more details.</span></span>

## <a name="lazy-loading-without-proxies"></a><span data-ttu-id="ffcbb-112">Chargement différé sans proxy</span><span class="sxs-lookup"><span data-stu-id="ffcbb-112">Lazy loading without proxies</span></span>

<span data-ttu-id="ffcbb-113">Les proxys à chargement différé fonctionnent en injectant le service `ILazyLoader` dans une entité, comme décrit dans [Constructeurs de type d’entité](xref:core/modeling/constructors).</span><span class="sxs-lookup"><span data-stu-id="ffcbb-113">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](xref:core/modeling/constructors).</span></span> <span data-ttu-id="ffcbb-114">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ffcbb-114">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="ffcbb-115">Cette méthode ne requiert pas l’héritage des types d’entités ou des propriétés de navigation virtuelles, et permet aux instances d’entité créées avec la `new` valeur de chargement différé une fois attachées à un contexte.</span><span class="sxs-lookup"><span data-stu-id="ffcbb-115">This method doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="ffcbb-116">Toutefois, il requiert une référence au service `ILazyLoader`, qui est défini dans le package [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="ffcbb-116">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="ffcbb-117">Ce package contient un ensemble minimal de types afin qu’il n’y ait qu’un faible impact en fonction de celui-ci.</span><span class="sxs-lookup"><span data-stu-id="ffcbb-117">This package contains a minimal set of types so that there is little impact in depending on it.</span></span> <span data-ttu-id="ffcbb-118">Toutefois, pour éviter complètement en fonction des packages d’EF Core dans les types d’entité, il est possible d’injecter la `ILazyLoader.Load` méthode en tant que délégué.</span><span class="sxs-lookup"><span data-stu-id="ffcbb-118">However, to completely avoid depending on any EF Core packages in the entity types, it's possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="ffcbb-119">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ffcbb-119">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="ffcbb-120">Le code ci-dessus utilise une méthode d'extension `Load` pour rendre l’utilisation du délégué un peu plus propre :</span><span class="sxs-lookup"><span data-stu-id="ffcbb-120">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]
> <span data-ttu-id="ffcbb-121">Le paramètre de constructeur pour le délégué à chargement différé doit être appelé « lazyLoader ».</span><span class="sxs-lookup"><span data-stu-id="ffcbb-121">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="ffcbb-122">La configuration permettant d’utiliser un nom différent est prévue pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="ffcbb-122">Configuration to use a different name than this is planned for a future release.</span></span>
