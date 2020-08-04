---
title: 'Filtres de requête globale : EF Core'
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 57d81919dedb853d2a41066f76ec20685ae41d6b
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526899"
---
# <a name="global-query-filters"></a><span data-ttu-id="b9d15-102">Filtres de requête globale</span><span class="sxs-lookup"><span data-stu-id="b9d15-102">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="b9d15-103">Cette fonctionnalité date de la publication d’EF Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="b9d15-103">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="b9d15-104">Les filtres de requête globale permet aux prédicats de requête LINQ (expression booléenne généralement passée à l’opérateur de requête LINQ *Where*) d’être appliqués sur des types d’entité dans le modèle de métadonnées (généralement dans *OnModelCreating*).</span><span class="sxs-lookup"><span data-stu-id="b9d15-104">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="b9d15-105">Ces filtres sont automatiquement appliqués à toutes les requêtes LINQ impliquant ces types d’entités, y compris ceux référencés indirectement, par exemple à l’aide d’Include ou de références de propriété de navigation directe.</span><span class="sxs-lookup"><span data-stu-id="b9d15-105">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="b9d15-106">Voici deux applications courantes de cette fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="b9d15-106">Some common applications of this feature are:</span></span>

* <span data-ttu-id="b9d15-107">**Suppression réversible** : un type d’entité définit une propriété *IsDeleted*.</span><span class="sxs-lookup"><span data-stu-id="b9d15-107">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="b9d15-108">Architecture **mutualisée : un** type d’entité définit une propriété *TenantId* .</span><span class="sxs-lookup"><span data-stu-id="b9d15-108">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="b9d15-109">Exemple</span><span class="sxs-lookup"><span data-stu-id="b9d15-109">Example</span></span>

<span data-ttu-id="b9d15-110">L’exemple suivant montre comment utiliser les filtres de requête globale pour implémenter des comportements de suppression réversible et d’architecture multilocataire dans un modèle de création de blogs simple.</span><span class="sxs-lookup"><span data-stu-id="b9d15-110">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="b9d15-111">Vous pouvez afficher un [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) d’architecture mutualisée et des [exemples à l’aide des navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="b9d15-111">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span> 

<span data-ttu-id="b9d15-112">Tout d’abord définissez les entités :</span><span class="sxs-lookup"><span data-stu-id="b9d15-112">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="b9d15-113">Notez la déclaration d’un champ _tenantId_ sur l’entité _Blog_.</span><span class="sxs-lookup"><span data-stu-id="b9d15-113">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="b9d15-114">Celui-ci doit servir à associer chaque instance de blog à un client spécifique.</span><span class="sxs-lookup"><span data-stu-id="b9d15-114">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="b9d15-115">Une propriété _IsDeleted_ est également définie sur le type d’entité _Post_.</span><span class="sxs-lookup"><span data-stu-id="b9d15-115">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="b9d15-116">Elle est utilisée pour déterminer si une instance _Post_ a été « supprimée de façon réversible ».</span><span class="sxs-lookup"><span data-stu-id="b9d15-116">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="b9d15-117">Autrement dit, l’instance est marquée comme supprimée sans que les données sous-jacentes soient réellement supprimées.</span><span class="sxs-lookup"><span data-stu-id="b9d15-117">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="b9d15-118">Ensuite, configurez les filtres de requête dans _OnModelCreating_ à l’aide de l’API `HasQueryFilter`.</span><span class="sxs-lookup"><span data-stu-id="b9d15-118">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="b9d15-119">Les expressions de prédicat passées aux appels _HasQueryFilter_ seront désormais automatiquement appliquées à toutes les requêtes LINQ pour ces types.</span><span class="sxs-lookup"><span data-stu-id="b9d15-119">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="b9d15-120">Notez l’utilisation d’un champ de niveau d’instance DbContext : `_tenantId` permet de définir le client en cours.</span><span class="sxs-lookup"><span data-stu-id="b9d15-120">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="b9d15-121">Les filtres au niveau du modèle utilisent la valeur de l’instance de contexte correcte (c’est-à-dire celle qui exécute la requête).</span><span class="sxs-lookup"><span data-stu-id="b9d15-121">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="b9d15-122">Il n’est actuellement pas possible de définir plusieurs filtres de requête sur la même entité ; seul le dernier sera appliqué.</span><span class="sxs-lookup"><span data-stu-id="b9d15-122">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="b9d15-123">Toutefois, vous pouvez définir un filtre unique avec plusieurs conditions à l’aide de l’opérateur _and_ logique ([ `&&` en C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span><span class="sxs-lookup"><span data-stu-id="b9d15-123">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="b9d15-124">Utilisation des navigations</span><span class="sxs-lookup"><span data-stu-id="b9d15-124">Use of navigations</span></span>

<span data-ttu-id="b9d15-125">Les navigations peuvent être utilisées lors de la définition de filtres de requête globaux.</span><span class="sxs-lookup"><span data-stu-id="b9d15-125">Navigations can be used when defining global query filters.</span></span> <span data-ttu-id="b9d15-126">Elles sont appliquées de manière récursive : lorsque les navigations utilisées dans les filtres de requête sont traduites, les filtres de requête définis sur les entités référencées sont également appliqués, ce qui peut ajouter des navigations supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="b9d15-126">They are applied recursively - when navigations used in query filters are translated, query filters defined on referenced entities are also applied, potentially adding more navigations.</span></span>

> [!NOTE]
> <span data-ttu-id="b9d15-127">Actuellement EF Core ne détecte pas les cycles dans les définitions de filtre de requête globale. vous devez donc être prudent lorsque vous les définissez.</span><span class="sxs-lookup"><span data-stu-id="b9d15-127">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="b9d15-128">Si elle est spécifiée de manière incorrecte, cela peut entraîner des boucles infinies lors de la traduction de la requête.</span><span class="sxs-lookup"><span data-stu-id="b9d15-128">If specified incorrectly, this could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="b9d15-129">Accès à une entité avec un filtre de requête à l’aide de la navigation requise</span><span class="sxs-lookup"><span data-stu-id="b9d15-129">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="b9d15-130">L’utilisation de la navigation requise pour accéder à l’entité qui a un filtre de requête global défini peut entraîner des résultats inattendus.</span><span class="sxs-lookup"><span data-stu-id="b9d15-130">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span> 

<span data-ttu-id="b9d15-131">La navigation obligatoire s’attend à ce que l’entité associée soit toujours présente.</span><span class="sxs-lookup"><span data-stu-id="b9d15-131">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="b9d15-132">Si l’entité associée requise est filtrée par le filtre de requête, l’entité parente peut se retrouver dans un état inattendu.</span><span class="sxs-lookup"><span data-stu-id="b9d15-132">If required related entity is filtered out by the query filter, the parent entity could end up in unexpected state.</span></span> <span data-ttu-id="b9d15-133">Cela peut entraîner le retour de moins d’éléments que prévu.</span><span class="sxs-lookup"><span data-stu-id="b9d15-133">This may result in returning fewer elements than expected.</span></span> 

<span data-ttu-id="b9d15-134">Pour illustrer le problème, nous pouvons utiliser `Blog` les `Post` entités et spécifiées ci-dessus, ainsi que la méthode _OnModelCreating_ suivante :</span><span class="sxs-lookup"><span data-stu-id="b9d15-134">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="b9d15-135">Le modèle peut être amorcé avec les données suivantes :</span><span class="sxs-lookup"><span data-stu-id="b9d15-135">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="b9d15-136">Le problème peut être observé lors de l’exécution de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="b9d15-136">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="b9d15-137">Avec cette configuration, la première requête retourne tous les 6 `Post` s, mais la deuxième requête retourne uniquement 3.</span><span class="sxs-lookup"><span data-stu-id="b9d15-137">With this setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="b9d15-138">Cela se produit parce que la méthode _include_ de la deuxième requête charge les `Blog` entités associées.</span><span class="sxs-lookup"><span data-stu-id="b9d15-138">This happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="b9d15-139">Dans la mesure où la navigation entre `Blog` et `Post` est requise, EF Core utilise `INNER JOIN` lors de la construction de la requête :</span><span class="sxs-lookup"><span data-stu-id="b9d15-139">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="b9d15-140">L’utilisation des `INNER JOIN` filtres exclut tous les `Post` s dont les `Blog` s associés ont été supprimés par un filtre de requête global.</span><span class="sxs-lookup"><span data-stu-id="b9d15-140">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span> 

<span data-ttu-id="b9d15-141">Vous pouvez le résoudre à l’aide de la navigation facultative au lieu de obligatoire.</span><span class="sxs-lookup"><span data-stu-id="b9d15-141">It can be addressed by using optional navigation instead of required.</span></span> <span data-ttu-id="b9d15-142">De cette façon, la première requête reste la même qu’avant, mais la deuxième requête génère `LEFT JOIN` et retourne 6 résultats.</span><span class="sxs-lookup"><span data-stu-id="b9d15-142">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="b9d15-143">Une autre approche consiste à spécifier des filtres cohérents sur les `Blog` `Post` entités et.</span><span class="sxs-lookup"><span data-stu-id="b9d15-143">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="b9d15-144">De cette façon, les filtres de correspondance sont appliqués à `Blog` et à `Post` .</span><span class="sxs-lookup"><span data-stu-id="b9d15-144">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="b9d15-145">`Post`qui pourraient finir par un état inattendu sont supprimés et les deux requêtes retournent 3 résultats.</span><span class="sxs-lookup"><span data-stu-id="b9d15-145">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span> 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="b9d15-146">Désactivation des filtres</span><span class="sxs-lookup"><span data-stu-id="b9d15-146">Disabling Filters</span></span>

<span data-ttu-id="b9d15-147">Les filtres peuvent être désactivés pour des requêtes LINQ individuelles à l’aide de l’opérateur `IgnoreQueryFilters()`.</span><span class="sxs-lookup"><span data-stu-id="b9d15-147">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="b9d15-148">Limites</span><span class="sxs-lookup"><span data-stu-id="b9d15-148">Limitations</span></span>

<span data-ttu-id="b9d15-149">Les filtres de requête globale présentent les limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b9d15-149">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="b9d15-150">Les filtres ne peuvent être définis que pour le type d’entité racine d’une hiérarchie d’héritage.</span><span class="sxs-lookup"><span data-stu-id="b9d15-150">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
