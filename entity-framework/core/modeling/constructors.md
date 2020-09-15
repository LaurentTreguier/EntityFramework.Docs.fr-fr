---
title: Types d’entité avec constructeurs-EF Core
description: Utilisation de constructeurs pour lier des données avec Entity Framework Core modèle
author: ajcvickers
ms.date: 02/23/2018
uid: core/modeling/constructors
ms.openlocfilehash: 06d18f173275599ad1e547193363e13c48fc8dcf
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071587"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="2b1d4-103">Types d’entité avec constructeurs</span><span class="sxs-lookup"><span data-stu-id="2b1d4-103">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="2b1d4-104">Cette fonctionnalité est une nouveauté d’EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-104">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="2b1d4-105">À compter de EF Core 2,1, il est maintenant possible de définir un constructeur avec des paramètres et d’avoir EF Core appeler ce constructeur lors de la création d’une instance de l’entité.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-105">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="2b1d4-106">Les paramètres de constructeur peuvent être liés à des propriétés mappées ou à différents genres de services pour faciliter les comportements tels que le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-106">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="2b1d4-107">À partir de EF Core 2,1, toute la liaison de constructeur est par Convention.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-107">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="2b1d4-108">La configuration de constructeurs spécifiques à utiliser est prévue pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-108">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="2b1d4-109">Lier à des propriétés mappées</span><span class="sxs-lookup"><span data-stu-id="2b1d4-109">Binding to mapped properties</span></span>

<span data-ttu-id="2b1d4-110">Prenons l’exemple d’un modèle de blog/publication classique :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-110">Consider a typical Blog/Post model:</span></span>

``` csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="2b1d4-111">Lorsque EF Core crée des instances de ces types, par exemple pour les résultats d’une requête, il commence par appeler le constructeur sans paramètre par défaut, puis affecte à chaque propriété la valeur de la base de données.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-111">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="2b1d4-112">Toutefois, si EF Core trouve un constructeur paramétrable avec des noms et des types de paramètres qui correspondent à ceux des propriétés mappées, il appellera plutôt le constructeur paramétrable avec des valeurs pour ces propriétés et ne définira pas explicitement chaque propriété.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-112">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="2b1d4-113">Exemple :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-113">For example:</span></span>

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="2b1d4-114">Quelques éléments à prendre en compte :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-114">Some things to note:</span></span>

* <span data-ttu-id="2b1d4-115">Toutes les propriétés ne doivent pas avoir de paramètres de constructeur.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-115">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="2b1d4-116">Par exemple, la propriété poster. Content n’est pas définie par un paramètre de constructeur, donc EF Core la définira après avoir appelé le constructeur de manière normale.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-116">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="2b1d4-117">Les types et les noms de paramètres doivent correspondre aux types de propriétés et aux noms, à ceci près que les propriétés peuvent respecter la casse Pascal alors que les paramètres sont en casse mixte.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-117">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="2b1d4-118">EF Core ne pouvez pas définir de propriétés de navigation (telles que les blogs ou les billets ci-dessus) à l’aide d’un constructeur.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-118">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="2b1d4-119">Le constructeur peut être public, privé ou avoir une autre accessibilité.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-119">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="2b1d4-120">Toutefois, les proxies à chargement différé requièrent que le constructeur soit accessible à partir de la classe proxy qui hérite.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-120">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="2b1d4-121">Cela signifie généralement qu’il devient public ou protégé.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-121">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="2b1d4-122">Propriétés en lecture seule</span><span class="sxs-lookup"><span data-stu-id="2b1d4-122">Read-only properties</span></span>

<span data-ttu-id="2b1d4-123">Une fois les propriétés définies par le biais du constructeur, il peut être judicieux de les rendre accessibles en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-123">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="2b1d4-124">EF Core prend cela en charge, mais il y a quelques éléments à consulter :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-124">EF Core supports this, but there are some things to look out for:</span></span>

* <span data-ttu-id="2b1d4-125">Les propriétés sans Setter ne sont pas mappées par Convention.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-125">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="2b1d4-126">(Cela a tendance à mapper les propriétés qui ne doivent pas être mappées, telles que les propriétés calculées.)</span><span class="sxs-lookup"><span data-stu-id="2b1d4-126">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="2b1d4-127">L’utilisation de valeurs de clés générées automatiquement nécessite une propriété de clé en lecture-écriture, puisque la valeur de clé doit être définie par le générateur de clé lors de l’insertion de nouvelles entités.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-127">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="2b1d4-128">Un moyen simple d’éviter ces choses consiste à utiliser des accesseurs set privés.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-128">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="2b1d4-129">Exemple :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-129">For example:</span></span>

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="2b1d4-130">EF Core voit une propriété avec un accesseur Set privé en lecture-écriture, ce qui signifie que toutes les propriétés sont mappées comme avant et que la clé peut toujours être générée par le magasin.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-130">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="2b1d4-131">Une alternative à l’utilisation des accesseurs set privés consiste à définir des propriétés en lecture seule et à ajouter des mappages plus explicites dans OnModelCreating.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-131">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="2b1d4-132">De même, certaines propriétés peuvent être complètement supprimées et remplacées par des champs uniquement.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-132">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="2b1d4-133">Par exemple, considérez les types d’entités suivants :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-133">For example, consider these entity types:</span></span>

``` csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="2b1d4-134">Et cette configuration dans OnModelCreating :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-134">And this configuration in OnModelCreating:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```

<span data-ttu-id="2b1d4-135">Points à noter :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-135">Things to note:</span></span>

* <span data-ttu-id="2b1d4-136">La clé « Property » est désormais un champ.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-136">The key "property" is now a field.</span></span> <span data-ttu-id="2b1d4-137">Ce n’est pas un `readonly` champ afin que les clés générées par le magasin puissent être utilisées.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-137">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="2b1d4-138">Les autres propriétés sont des propriétés en lecture seule définies uniquement dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-138">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="2b1d4-139">Si la valeur de clé primaire n’est jamais définie par EF ou lue à partir de la base de données, il n’est pas nécessaire de l’inclure dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-139">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="2b1d4-140">Cela laisse la clé « Property » en tant que champ simple et précise qu’elle ne doit pas être définie explicitement lors de la création de nouveaux blogs ou publications.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-140">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="2b1d4-141">Ce code génère l’avertissement du compilateur' 169 ', indiquant que le champ n’est jamais utilisé.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-141">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="2b1d4-142">Cela peut être ignoré, car en réalité EF Core utilise le champ d’une manière extralinguistic.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-142">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="2b1d4-143">Injection de services</span><span class="sxs-lookup"><span data-stu-id="2b1d4-143">Injecting services</span></span>

<span data-ttu-id="2b1d4-144">EF Core pouvez également injecter des « services » dans le constructeur d’un type d’entité.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-144">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="2b1d4-145">Par exemple, vous pouvez injecter les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-145">For example, the following can be injected:</span></span>

* <span data-ttu-id="2b1d4-146">`DbContext` -l’instance de contexte actuelle, qui peut également être typée comme votre type DbContext dérivé</span><span class="sxs-lookup"><span data-stu-id="2b1d4-146">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="2b1d4-147">`ILazyLoader` -le service de chargement différé : pour plus d’informations, consultez la documentation sur le [chargement différé](xref:core/querying/related-data) .</span><span class="sxs-lookup"><span data-stu-id="2b1d4-147">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="2b1d4-148">`Action<object, string>` -un délégué à chargement différé : consultez la documentation sur le [chargement différé](xref:core/querying/related-data) pour plus d’informations</span><span class="sxs-lookup"><span data-stu-id="2b1d4-148">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="2b1d4-149">`IEntityType` -les métadonnées de EF Core associées à ce type d’entité</span><span class="sxs-lookup"><span data-stu-id="2b1d4-149">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="2b1d4-150">À partir de EF Core 2,1, seuls les services connus par EF Core peuvent être injectés.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-150">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="2b1d4-151">La prise en charge de l’injection des services d’application est prise en compte pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-151">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="2b1d4-152">Par exemple, un DbContext injecté peut être utilisé pour accéder de manière sélective à la base de données afin d’obtenir des informations sur les entités associées sans les charger toutes.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-152">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="2b1d4-153">Dans l’exemple ci-dessous, il est utilisé pour obtenir le nombre de publications dans un blog sans charger les publications :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-153">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

``` csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="2b1d4-154">Voici quelques points à noter :</span><span class="sxs-lookup"><span data-stu-id="2b1d4-154">A few things to notice about this:</span></span>

* <span data-ttu-id="2b1d4-155">Le constructeur est privé, car il n’est jamais appelé par EF Core, et il existe un autre constructeur public pour une utilisation générale.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-155">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="2b1d4-156">Le code qui utilise le service injecté (autrement dit, le contexte) est défensif par rapport `null` à la gestion des cas où EF Core ne crée pas l’instance.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-156">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="2b1d4-157">Étant donné que le service est stocké dans une propriété en lecture/écriture, il est réinitialisé lorsque l’entité est attachée à une nouvelle instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-157">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="2b1d4-158">L’injection de DbContext comme celle-ci est souvent considérée comme un anti-modèle dans la mesure où elle couple vos types d’entité directement à EF Core.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-158">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="2b1d4-159">Examinez attentivement toutes les options avant d’utiliser l’injection de service comme celle-ci.</span><span class="sxs-lookup"><span data-stu-id="2b1d4-159">Carefully consider all options before using service injection like this.</span></span>
