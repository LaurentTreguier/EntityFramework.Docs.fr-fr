---
title: Types d’entités avec des constructeurs - EF Core
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: 5bf49718f02c1860871b1f4c255ec4d98fce2fc7
ms.sourcegitcommit: 960e42a01b3a2f76da82e074f64f52252a8afecc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405243"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="f2b31-102">Types d’entités avec des constructeurs</span><span class="sxs-lookup"><span data-stu-id="f2b31-102">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="f2b31-103">Cette fonctionnalité est une nouveauté d’EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="f2b31-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="f2b31-104">À compter d’EF Core 2.1, il est désormais possible de définir un constructeur avec des paramètres et appeler ce constructeur lorsque vous créez une instance de l’entité de EF Core.</span><span class="sxs-lookup"><span data-stu-id="f2b31-104">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="f2b31-105">Les paramètres du constructeur peuvent être liés à la propriété mappée, ou à différents types de services pour faciliter les comportements, tels que chargement différé.</span><span class="sxs-lookup"><span data-stu-id="f2b31-105">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="f2b31-106">À compter d’EF Core 2.1, tous les liaison constructeur est par convention.</span><span class="sxs-lookup"><span data-stu-id="f2b31-106">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="f2b31-107">Configuration des constructeurs spécifiques à utiliser est prévue pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="f2b31-107">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="f2b31-108">Liaison aux propriétés mappées</span><span class="sxs-lookup"><span data-stu-id="f2b31-108">Binding to mapped properties</span></span>

<span data-ttu-id="f2b31-109">Considérez un modèle de billet de Blog/classique :</span><span class="sxs-lookup"><span data-stu-id="f2b31-109">Consider a typical Blog/Post model:</span></span>

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

<span data-ttu-id="f2b31-110">Quand EF Core crée des instances de ces types, comme pour les résultats d’une requête, il sera tout d’abord appeler le constructeur sans paramètre par défaut et définissez chaque propriété sur la valeur à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="f2b31-110">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="f2b31-111">Toutefois, si EF Core recherche un constructeur paramétrable avec des noms de paramètre et les types qui correspondent à ceux de mappé des propriétés, puis il appelle à la place du constructeur paramétré avec des valeurs pour ces propriétés et ne définit pas explicitement de chaque propriété.</span><span class="sxs-lookup"><span data-stu-id="f2b31-111">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="f2b31-112">Exemple :</span><span class="sxs-lookup"><span data-stu-id="f2b31-112">For example:</span></span>

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
<span data-ttu-id="f2b31-113">Voici quelques éléments à noter :</span><span class="sxs-lookup"><span data-stu-id="f2b31-113">Some things to note:</span></span>
* <span data-ttu-id="f2b31-114">Toutes les propriétés ne doivent avoir des paramètres du constructeur.</span><span class="sxs-lookup"><span data-stu-id="f2b31-114">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="f2b31-115">Par exemple, la propriété de Post.Content n’est pas définie par n’importe quel paramètre de constructeur, donc EF Core est la valeur après l’appel du constructeur de façon normale.</span><span class="sxs-lookup"><span data-stu-id="f2b31-115">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="f2b31-116">Les noms et types de paramètres doivent correspondre à des types de propriété et les noms, à ceci près que les propriétés peuvent être casse Pascal tandis que les paramètres sont de casse mixte.</span><span class="sxs-lookup"><span data-stu-id="f2b31-116">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="f2b31-117">EF Core ne peut pas définir les propriétés de navigation (par exemple, Blog ou les billets ci-dessus) à l’aide d’un constructeur.</span><span class="sxs-lookup"><span data-stu-id="f2b31-117">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="f2b31-118">Le constructeur peut être public, privé, ou avoir n’importe quelle autre accessibilité.</span><span class="sxs-lookup"><span data-stu-id="f2b31-118">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="f2b31-119">Toutefois, les proxys de chargement différé requièrent que le constructeur est accessible à partir de la classe qui hérite de proxy.</span><span class="sxs-lookup"><span data-stu-id="f2b31-119">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="f2b31-120">Cela signifie généralement rendant public ou protégé.</span><span class="sxs-lookup"><span data-stu-id="f2b31-120">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="f2b31-121">Propriétés en lecture seule</span><span class="sxs-lookup"><span data-stu-id="f2b31-121">Read-only properties</span></span>

<span data-ttu-id="f2b31-122">Une fois que les propriétés sont définies via le constructeur, il peut être judicieux pour rendre certains d'entre eux en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="f2b31-122">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="f2b31-123">EF Core prend en charge, mais il existe quelques éléments à rechercher :</span><span class="sxs-lookup"><span data-stu-id="f2b31-123">EF Core supports this, but there are some things to look out for:</span></span>
* <span data-ttu-id="f2b31-124">Propriétés sans accesseurs Set ne sont pas mappées par convention.</span><span class="sxs-lookup"><span data-stu-id="f2b31-124">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="f2b31-125">(Cela a tendance à mapper des propriétés qui ne doivent pas être mappées, telles que les propriétés calculées.)</span><span class="sxs-lookup"><span data-stu-id="f2b31-125">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="f2b31-126">À l’aide de valeurs de clés générées automatiquement nécessite une propriété de clé qui est en lecture-écriture, dans la mesure où la valeur de clé doit être définie par le Générateur de clé lors de l’insertion de nouvelles entités.</span><span class="sxs-lookup"><span data-stu-id="f2b31-126">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="f2b31-127">Un moyen simple pour éviter ces choses consiste à utiliser les méthodes Set privées.</span><span class="sxs-lookup"><span data-stu-id="f2b31-127">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="f2b31-128">Exemple :</span><span class="sxs-lookup"><span data-stu-id="f2b31-128">For example:</span></span>
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
<span data-ttu-id="f2b31-129">EF Core voit une propriété avec un setter privée en lecture-écriture, ce qui signifie que toutes les propriétés sont mappées comme auparavant et la clé peut toujours être générées par le magasin.</span><span class="sxs-lookup"><span data-stu-id="f2b31-129">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="f2b31-130">Une alternative à l’utilisation de méthodes setter privée consiste à rendre les propriétés vraiment en lecture seule et ajouter un mappage plus explicite dans OnModelCreating.</span><span class="sxs-lookup"><span data-stu-id="f2b31-130">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="f2b31-131">De même, certaines propriétés peuvent être complètement supprimées et remplacées par uniquement des champs.</span><span class="sxs-lookup"><span data-stu-id="f2b31-131">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="f2b31-132">Par exemple, considérez ces types d’entités :</span><span class="sxs-lookup"><span data-stu-id="f2b31-132">For example, consider these entity types:</span></span>

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
<span data-ttu-id="f2b31-133">Et cette configuration dans OnModelCreating :</span><span class="sxs-lookup"><span data-stu-id="f2b31-133">And this configuration in OnModelCreating:</span></span>
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
<span data-ttu-id="f2b31-134">Points à noter :</span><span class="sxs-lookup"><span data-stu-id="f2b31-134">Things to note:</span></span>
* <span data-ttu-id="f2b31-135">La clé « property » est désormais un champ.</span><span class="sxs-lookup"><span data-stu-id="f2b31-135">The key "property" is now a field.</span></span> <span data-ttu-id="f2b31-136">Il n’est pas un `readonly` afin que les clés générées par le magasin peuvent être utilisées.</span><span class="sxs-lookup"><span data-stu-id="f2b31-136">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="f2b31-137">Les autres propriétés sont en lecture seule défini uniquement dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="f2b31-137">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="f2b31-138">Si la valeur de clé primaire est toujours définie par Entity Framework ou lire à partir de la base de données, il est inutile d’inclure cela dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="f2b31-138">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="f2b31-139">Cela laisse la clé « property » comme un champ simple et permet d’identifier clairement qu’il ne doit pas être défini explicitement lors de la création de nouveaux blogs ou les billets.</span><span class="sxs-lookup"><span data-stu-id="f2b31-139">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="f2b31-140">Ce code entraîne '169' indiquant que le champ n’est jamais utilisé d’avertissement du compilateur.</span><span class="sxs-lookup"><span data-stu-id="f2b31-140">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="f2b31-141">Cela peut être ignoré, car en réalité EF Core utilise le champ de manière extralinguistic.</span><span class="sxs-lookup"><span data-stu-id="f2b31-141">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="f2b31-142">Injection de services</span><span class="sxs-lookup"><span data-stu-id="f2b31-142">Injecting services</span></span>

<span data-ttu-id="f2b31-143">EF Core peut également injecter des « services » dans le constructeur du type d’une entité.</span><span class="sxs-lookup"><span data-stu-id="f2b31-143">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="f2b31-144">Par exemple, ce qui suit peut être injecté :</span><span class="sxs-lookup"><span data-stu-id="f2b31-144">For example, the following can be injected:</span></span>
* <span data-ttu-id="f2b31-145">`DbContext` -l’instance de contexte actuel, ce qui peut également être tapé sous forme votre type DbContext dérivée</span><span class="sxs-lookup"><span data-stu-id="f2b31-145">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="f2b31-146">`ILazyLoader` -le service de chargement différé, consultez le [chargement différé documentation](../querying/related-data.md) pour plus d’informations</span><span class="sxs-lookup"><span data-stu-id="f2b31-146">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="f2b31-147">`Action<object, string>` -un délégué sur le chargement différé, consultez le [chargement différé documentation](../querying/related-data.md) pour plus d’informations</span><span class="sxs-lookup"><span data-stu-id="f2b31-147">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="f2b31-148">`IEntityType` -les métadonnées de EF Core associées à ce type d’entité</span><span class="sxs-lookup"><span data-stu-id="f2b31-148">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="f2b31-149">À compter d’EF Core 2.1, seuls les services connus par EF Core peuvent être injectées.</span><span class="sxs-lookup"><span data-stu-id="f2b31-149">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="f2b31-150">Prise en charge d’injection de services d’application est envisagée pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="f2b31-150">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="f2b31-151">Par exemple, un DbContext injecté peut être utilisé pour accéder de façon sélective la base de données pour obtenir des informations sur les entités connexes sans charger toutes les.</span><span class="sxs-lookup"><span data-stu-id="f2b31-151">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="f2b31-152">Dans l’exemple ci-dessous, cela est utilisé pour obtenir le nombre de billets dans un blog sans charger les billets de :</span><span class="sxs-lookup"><span data-stu-id="f2b31-152">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

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
<span data-ttu-id="f2b31-153">Choses à remarquer à ce sujet :</span><span class="sxs-lookup"><span data-stu-id="f2b31-153">A few things to notice about this:</span></span>
* <span data-ttu-id="f2b31-154">Le constructeur est privé, dans la mesure où elle est toujours appelée par EF Core, et il existe un autre constructeur public pour une utilisation générale.</span><span class="sxs-lookup"><span data-stu-id="f2b31-154">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="f2b31-155">Le code qui utilise le service injecté (autrement dit, le contexte) est la défense contre lui en cours `null` pour gérer les cas où EF Core n’est pas création de l’instance.</span><span class="sxs-lookup"><span data-stu-id="f2b31-155">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="f2b31-156">Étant donné que le service est stocké dans une propriété en lecture/écriture, elle sera réinitialisée lorsque l’entité est attachée à une nouvelle instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="f2b31-156">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="f2b31-157">Injecter le DbContext, comme cela est souvent considéré comme un anti-modèle dans la mesure où elle associe vos types d’entité directement vers EF Core.</span><span class="sxs-lookup"><span data-stu-id="f2b31-157">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="f2b31-158">Étudiez attentivement toutes les options avant d’utiliser l’injection de service comme suit.</span><span class="sxs-lookup"><span data-stu-id="f2b31-158">Carefully consider all options before using service injection like this.</span></span>
