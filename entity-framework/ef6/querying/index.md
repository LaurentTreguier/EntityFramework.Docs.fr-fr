---
title: Interrogation et recherche d’entités - EF6
description: Interrogation et recherche d’entités dans Entity Framework 6.
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/index
ms.openlocfilehash: ec0239ed54099226d009031af79388f5ae00cdc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065891"
---
# <a name="querying-and-finding-entities"></a><span data-ttu-id="d432f-103">Interrogation et recherche d’entités</span><span class="sxs-lookup"><span data-stu-id="d432f-103">Querying and Finding Entities</span></span>
<span data-ttu-id="d432f-104">Cette rubrique décrit les différentes méthodes de recherche de données à l’aide d’Entity Framework, y compris LINQ et la méthode Find.</span><span class="sxs-lookup"><span data-stu-id="d432f-104">This topic covers the various ways you can query for data using Entity Framework, including LINQ and the Find method.</span></span> <span data-ttu-id="d432f-105">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="d432f-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="finding-entities-using-a-query"></a><span data-ttu-id="d432f-106">Recherche d’entités à l’aide d’une requête</span><span class="sxs-lookup"><span data-stu-id="d432f-106">Finding entities using a query</span></span>  

<span data-ttu-id="d432f-107">DbSet et IDbSet implémentent IQueryable et peuvent donc servir de point de départ pour écrire une requête LINQ sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="d432f-107">DbSet and IDbSet implement IQueryable and so can be used as the starting point for writing a LINQ query against the database.</span></span> <span data-ttu-id="d432f-108">Nous ne décrirons pas LINQ en détail ici, mais voici quelques exemples simples :</span><span class="sxs-lookup"><span data-stu-id="d432f-108">This is not the appropriate place for an in-depth discussion of LINQ, but here are a couple of simple examples:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

<span data-ttu-id="d432f-109">Notez que DbSet et IDbSet créent toujours des requêtes sur la base de données et impliquent toujours un aller-retour vers la base de données même si les entités retournées existent déjà dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="d432f-109">Note that DbSet and IDbSet always create queries against the database and will always involve a round trip to the database even if the entities returned already exist in the context.</span></span> <span data-ttu-id="d432f-110">Une requête est exécutée sur la base de données quand :</span><span class="sxs-lookup"><span data-stu-id="d432f-110">A query is executed against the database when:</span></span>  

- <span data-ttu-id="d432f-111">Elle est énumérée par une instruction **foreach** (C#) ou **For Each** (Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="d432f-111">It is enumerated by a **foreach** (C#) or **For Each** (Visual Basic) statement.</span></span>  
- <span data-ttu-id="d432f-112">Elle est énumérée par une opération de collection comme [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary) ou [ToList](https://msdn.microsoft.com/library/bb342261).</span><span class="sxs-lookup"><span data-stu-id="d432f-112">It is enumerated by a collection operation such as [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary), or [ToList](https://msdn.microsoft.com/library/bb342261).</span></span>  
- <span data-ttu-id="d432f-113">Des opérateurs LINQ, comme [First](https://msdn.microsoft.com/library/bb291976) ou [Any](https://msdn.microsoft.com/library/bb337697) sont spécifiés dans la partie la plus extérieure de la requête.</span><span class="sxs-lookup"><span data-stu-id="d432f-113">LINQ operators such as [First](https://msdn.microsoft.com/library/bb291976) or [Any](https://msdn.microsoft.com/library/bb337697) are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="d432f-114">Les méthodes suivantes sont appelées : la méthode d’extension [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) sur DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx) et Database.ExecuteSqlCommand.</span><span class="sxs-lookup"><span data-stu-id="d432f-114">The following methods are called: the [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) extension method on a DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx), and Database.ExecuteSqlCommand.</span></span>  

<span data-ttu-id="d432f-115">Quand les résultats sont retournés à partir de la base de données, les objets qui n’existent pas dans le contexte sont attachés au contexte.</span><span class="sxs-lookup"><span data-stu-id="d432f-115">When results are returned from the database, objects that do not exist in the context are attached to the context.</span></span> <span data-ttu-id="d432f-116">Si un objet est déjà dans le contexte, l’objet existant est retourné (les valeurs actuelles et d’origine des propriétés de l’objet dans l’entrée ne sont **pas** remplacées par les valeurs de la base de données).</span><span class="sxs-lookup"><span data-stu-id="d432f-116">If an object is already in the context, the existing object is returned (the current and original values of the object's properties in the entry are **not** overwritten with database values).</span></span>  

<span data-ttu-id="d432f-117">Quand vous exécutez une requête, les entités qui ont été ajoutées au contexte, mais n’ont pas encore été enregistrées dans la base de données, ne sont pas retournées dans le jeu de résultats.</span><span class="sxs-lookup"><span data-stu-id="d432f-117">When you perform a query, entities that have been added to the context but have not yet been saved to the database are not returned as part of the result set.</span></span> <span data-ttu-id="d432f-118">Pour obtenir les données du contexte, consultez [Données locales](xref:ef6/querying/local-data).</span><span class="sxs-lookup"><span data-stu-id="d432f-118">To get the data that is in the context, see [Local Data](xref:ef6/querying/local-data).</span></span>  

<span data-ttu-id="d432f-119">Si une requête ne retourne aucune ligne de la base de données, le résultat est une collection vide au lieu de **null**.</span><span class="sxs-lookup"><span data-stu-id="d432f-119">If a query returns no rows from the database, the result will be an empty collection, rather than **null**.</span></span>  

## <a name="finding-entities-using-primary-keys"></a><span data-ttu-id="d432f-120">Recherche d’entités à l’aide de clés primaires</span><span class="sxs-lookup"><span data-stu-id="d432f-120">Finding entities using primary keys</span></span>  

<span data-ttu-id="d432f-121">La méthode Find sur DbSet utilise la valeur de clé primaire pour tenter de trouver une entité suivie par le contexte.</span><span class="sxs-lookup"><span data-stu-id="d432f-121">The Find method on DbSet uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="d432f-122">Si l’entité est introuvable dans le contexte, une requête est envoyée à la base de données pour y rechercher l’entité.</span><span class="sxs-lookup"><span data-stu-id="d432f-122">If the entity is not found in the context then a query will be sent to the database to find the entity there.</span></span> <span data-ttu-id="d432f-123">Null est retourné si l’entité est introuvable dans le contexte ou dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="d432f-123">Null is returned if the entity is not found in the context or in the database.</span></span>  

<span data-ttu-id="d432f-124">Find est différent de l’utilisation d’une requête pour deux raisons :</span><span class="sxs-lookup"><span data-stu-id="d432f-124">Find is different from using a query in two significant ways:</span></span>  

- <span data-ttu-id="d432f-125">Un aller-retour vers la base de données est effectué uniquement si l’entité avec la clé spécifiée est introuvable dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="d432f-125">A round-trip to the database will only be made if the entity with the given key is not found in the context.</span></span>  
- <span data-ttu-id="d432f-126">Find retourne les entités qui sont dans l’état Added.</span><span class="sxs-lookup"><span data-stu-id="d432f-126">Find will return entities that are in the Added state.</span></span> <span data-ttu-id="d432f-127">Autrement dit, Find retourne les entités qui ont été ajoutées au contexte, mais n’ont pas encore été enregistrées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="d432f-127">That is, Find will return entities that have been added to the context but have not yet been saved to the database.</span></span>  
### <a name="finding-an-entity-by-primary-key"></a><span data-ttu-id="d432f-128">Recherche d’une entité par clé primaire</span><span class="sxs-lookup"><span data-stu-id="d432f-128">Finding an entity by primary key</span></span>  

<span data-ttu-id="d432f-129">Le code suivant montre des exemples d’utilisation de Find :</span><span class="sxs-lookup"><span data-stu-id="d432f-129">The following code shows some uses of Find:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a><span data-ttu-id="d432f-130">Recherche d’une entité par clé primaire composite</span><span class="sxs-lookup"><span data-stu-id="d432f-130">Finding an entity by composite primary key</span></span>  

<span data-ttu-id="d432f-131">Entity Framework permet à vos entités d’avoir des clés composites, c’est-à-dire des clés composées de plusieurs propriétés.</span><span class="sxs-lookup"><span data-stu-id="d432f-131">Entity Framework allows your entities to have composite keys - that's a key that is made up of more than one property.</span></span> <span data-ttu-id="d432f-132">Par exemple, vous avez une entité BlogSettings qui représente des paramètres utilisateur pour un blog particulier.</span><span class="sxs-lookup"><span data-stu-id="d432f-132">For example, you could have a BlogSettings entity that represents a users settings for a particular blog.</span></span> <span data-ttu-id="d432f-133">Comme un utilisateur n’aura jamais une entité BlogSettings pour chaque blog, vous pouvez choisir pour BlogSettings une clé primaire qui est une combinaison de BlogId et Username.</span><span class="sxs-lookup"><span data-stu-id="d432f-133">Because a user would only ever have one BlogSettings for each blog you could chose to make the primary key of BlogSettings a combination of BlogId and Username.</span></span> <span data-ttu-id="d432f-134">Le code suivant tente de rechercher l’entité BlogSettings avec BlogId = 3 et Username = "johndoe1987" :</span><span class="sxs-lookup"><span data-stu-id="d432f-134">The following code attempts to find the BlogSettings with BlogId = 3 and Username = "johndoe1987":</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

<span data-ttu-id="d432f-135">Quand vous avez des clés composites, vous devez utiliser ColumnAttribute ou l’API Fluent pour spécifier l’ordre des propriétés de la clé composite.</span><span class="sxs-lookup"><span data-stu-id="d432f-135">Note that when you have composite keys you need to use ColumnAttribute or the fluent API to specify an ordering for the properties of the composite key.</span></span> <span data-ttu-id="d432f-136">L’appel de Find doit utiliser cet ordre quand vous spécifiez les valeurs qui forment la clé.</span><span class="sxs-lookup"><span data-stu-id="d432f-136">The call to Find must use this order when specifying the values that form the key.</span></span>  
