---
title: Définition de DbSets-EF6
description: Définition de DbSets dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: 49ffe3ddb8cd4f7c414d7d45d108405b88a0115e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073949"
---
# <a name="defining-dbsets"></a><span data-ttu-id="75857-103">Définition de DbSets</span><span class="sxs-lookup"><span data-stu-id="75857-103">Defining DbSets</span></span>
<span data-ttu-id="75857-104">Lors du développement avec le flux de travail Code First, vous définissez un DbContext dérivé qui représente votre session avec la base de données et expose un DbSet pour chaque type de votre modèle.</span><span class="sxs-lookup"><span data-stu-id="75857-104">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="75857-105">Cette rubrique décrit les différentes façons dont vous pouvez définir les propriétés DbSet.</span><span class="sxs-lookup"><span data-stu-id="75857-105">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="75857-106">DbContext avec les propriétés DbSet</span><span class="sxs-lookup"><span data-stu-id="75857-106">DbContext with DbSet properties</span></span>  

<span data-ttu-id="75857-107">Le cas courant présenté dans Code First exemples consiste à avoir un DbContext avec des propriétés DbSet automatiques publiques pour les types d’entité de votre modèle.</span><span class="sxs-lookup"><span data-stu-id="75857-107">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="75857-108">Exemple :</span><span class="sxs-lookup"><span data-stu-id="75857-108">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="75857-109">En cas d’utilisation en mode Code First, cette opération configure les blogs et les publications en tant que types d’entité, ainsi que la configuration d’autres types accessibles à partir de ceux-ci.</span><span class="sxs-lookup"><span data-stu-id="75857-109">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="75857-110">De plus, DbContext appellera automatiquement la méthode setter pour chacune de ces propriétés afin de définir une instance du DbSet approprié.</span><span class="sxs-lookup"><span data-stu-id="75857-110">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="75857-111">DbContext avec les propriétés IDbSet</span><span class="sxs-lookup"><span data-stu-id="75857-111">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="75857-112">Dans certaines situations, par exemple lors de la création de simulacres ou de substituts, il est plus utile de déclarer vos propriétés définies à l’aide d’une interface.</span><span class="sxs-lookup"><span data-stu-id="75857-112">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="75857-113">Dans ce cas, l’interface IDbSet peut être utilisée à la place de DbSet.</span><span class="sxs-lookup"><span data-stu-id="75857-113">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="75857-114">Exemple :</span><span class="sxs-lookup"><span data-stu-id="75857-114">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="75857-115">Ce contexte fonctionne exactement de la même façon que le contexte qui utilise la classe DbSet pour ses propriétés set.</span><span class="sxs-lookup"><span data-stu-id="75857-115">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="75857-116">DbContext avec propriétés de jeu en lecture seule</span><span class="sxs-lookup"><span data-stu-id="75857-116">DbContext with read-only set properties</span></span>  

<span data-ttu-id="75857-117">Si vous ne souhaitez pas exposer les accesseurs set publics pour vos propriétés DbSet ou IDbSet, vous pouvez créer des propriétés en lecture seule et créer vous-même les instances Set.</span><span class="sxs-lookup"><span data-stu-id="75857-117">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="75857-118">Exemple :</span><span class="sxs-lookup"><span data-stu-id="75857-118">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

<span data-ttu-id="75857-119">Notez que DbContext met en cache l’instance de DbSet retournée par la méthode Set afin que chacune de ces propriétés retourne la même instance chaque fois qu’elle est appelée.</span><span class="sxs-lookup"><span data-stu-id="75857-119">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="75857-120">La découverte des types d’entités pour Code First fonctionne de la même façon que pour les propriétés avec les accesseurs get et les Setters publics.</span><span class="sxs-lookup"><span data-stu-id="75857-120">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
