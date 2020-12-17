---
title: Utilisation des proxies-EF6
description: Utilisation des proxies dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/proxies
ms.openlocfilehash: de44e891c074a8811e466d040f6451bea0f950f4
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635846"
---
# <a name="working-with-proxies"></a><span data-ttu-id="2e993-103">Utilisation des proxies</span><span class="sxs-lookup"><span data-stu-id="2e993-103">Working with proxies</span></span>
<span data-ttu-id="2e993-104">Lors de la création d’instances de types d’entités POCO, Entity Framework crée souvent des instances d’un type dérivé généré de manière dynamique qui agit comme un proxy pour l’entité.</span><span class="sxs-lookup"><span data-stu-id="2e993-104">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="2e993-105">Ce proxy remplace certaines propriétés virtuelles de l’entité pour insérer des raccordements pour l’exécution automatique des actions lors de l’accès à la propriété.</span><span class="sxs-lookup"><span data-stu-id="2e993-105">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="2e993-106">Par exemple, ce mécanisme est utilisé pour prendre en charge le chargement différé des relations.</span><span class="sxs-lookup"><span data-stu-id="2e993-106">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="2e993-107">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="2e993-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="2e993-108">Désactivation de la création de proxy</span><span class="sxs-lookup"><span data-stu-id="2e993-108">Disabling proxy creation</span></span>  

<span data-ttu-id="2e993-109">Il est parfois utile d’empêcher Entity Framework de créer des instances de proxy.</span><span class="sxs-lookup"><span data-stu-id="2e993-109">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="2e993-110">Par exemple, la sérialisation des instances non proxy est beaucoup plus facile que la sérialisation des instances de proxy.</span><span class="sxs-lookup"><span data-stu-id="2e993-110">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="2e993-111">La création du proxy peut être désactivée en désactivant l' `ProxyCreationEnabled` indicateur.</span><span class="sxs-lookup"><span data-stu-id="2e993-111">Proxy creation can be turned off by clearing the `ProxyCreationEnabled` flag.</span></span> <span data-ttu-id="2e993-112">Vous pouvez effectuer cette opération dans le constructeur de votre contexte.</span><span class="sxs-lookup"><span data-stu-id="2e993-112">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="2e993-113">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="2e993-113">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="2e993-114">Notez que le EF ne crée pas de proxy pour les types où il n’y a rien à faire pour le proxy.</span><span class="sxs-lookup"><span data-stu-id="2e993-114">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="2e993-115">Cela signifie que vous pouvez également éviter les proxies en ayant des types qui sont sealed et/ou n’ont pas de propriétés virtuelles.</span><span class="sxs-lookup"><span data-stu-id="2e993-115">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="2e993-116">Création explicite d’une instance d’un proxy</span><span class="sxs-lookup"><span data-stu-id="2e993-116">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="2e993-117">Une instance de proxy ne sera pas créée si vous créez une instance d’une entité à l’aide de l’opérateur New.</span><span class="sxs-lookup"><span data-stu-id="2e993-117">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="2e993-118">Ce n’est peut-être pas un problème, mais si vous avez besoin de créer une instance de proxy (par exemple, afin que le chargement différé ou le suivi des modifications de proxy fonctionnent), vous pouvez le faire à l’aide `Create` de la méthode de `DbSet` .</span><span class="sxs-lookup"><span data-stu-id="2e993-118">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the `Create` method of `DbSet`.</span></span> <span data-ttu-id="2e993-119">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="2e993-119">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="2e993-120">La version générique de `Create` peut être utilisée si vous souhaitez créer une instance d’un type d’entité dérivé.</span><span class="sxs-lookup"><span data-stu-id="2e993-120">The generic version of `Create` can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="2e993-121">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="2e993-121">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="2e993-122">Notez que la `Create` méthode n’ajoute pas ou n’attache pas l’entité créée au contexte.</span><span class="sxs-lookup"><span data-stu-id="2e993-122">Note that the `Create` method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="2e993-123">Notez que la `Create` méthode crée simplement une instance du type d’entité lui-même si la création d’un type de proxy pour l’entité n’aurait aucune valeur, car elle ne fait rien.</span><span class="sxs-lookup"><span data-stu-id="2e993-123">Note that the `Create` method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="2e993-124">Par exemple, si le type d’entité est sealed et/ou n’a pas de propriétés virtuelles, `Create` il suffit de créer une instance du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="2e993-124">For example, if the entity type is sealed and/or has no virtual properties then `Create` will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="2e993-125">Obtention du type d’entité réel à partir d’un type de proxy</span><span class="sxs-lookup"><span data-stu-id="2e993-125">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="2e993-126">Les types de proxy ont des noms qui ressemblent à ceci :</span><span class="sxs-lookup"><span data-stu-id="2e993-126">Proxy types have names that look something like this:</span></span>  

```
System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6
```

<span data-ttu-id="2e993-127">Vous pouvez rechercher le type d’entité pour ce type de proxy à l’aide `GetObjectType` de la méthode de `ObjectContext` .</span><span class="sxs-lookup"><span data-stu-id="2e993-127">You can find the entity type for this proxy type using the `GetObjectType` method from `ObjectContext`.</span></span> <span data-ttu-id="2e993-128">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="2e993-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="2e993-129">Notez que si le type passé à `GetObjectType` est une instance d’un type d’entité qui n’est pas un type de proxy, le type d’entité est toujours retourné.</span><span class="sxs-lookup"><span data-stu-id="2e993-129">Note that if the type passed to `GetObjectType` is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="2e993-130">Cela signifie que vous pouvez toujours utiliser cette méthode pour obtenir le type d’entité réel sans autre vérification pour vérifier si le type est un type de proxy ou non.</span><span class="sxs-lookup"><span data-stu-id="2e993-130">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  
