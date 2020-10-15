---
title: 'Filtres de requête globale : EF Core'
description: Utilisation de filtres de requête globaux pour filtrer les résultats avec Entity Framework Core
author: maumar
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 8a9eabd7e86864c9ebb4b1dc4a06bf7fc207d496
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062605"
---
# <a name="global-query-filters"></a><span data-ttu-id="d731b-103">Filtres de requête globale</span><span class="sxs-lookup"><span data-stu-id="d731b-103">Global Query Filters</span></span>

<span data-ttu-id="d731b-104">Les filtres de requête globaux sont des prédicats de requête LINQ appliqués aux types d’entité dans le modèle de métadonnées (généralement dans `OnModelCreating` ).</span><span class="sxs-lookup"><span data-stu-id="d731b-104">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in `OnModelCreating`).</span></span> <span data-ttu-id="d731b-105">Un prédicat de requête est une expression booléenne qui est généralement passée à l' `Where` opérateur de requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="d731b-105">A query predicate is a boolean expression typically passed to the LINQ `Where` query operator.</span></span>  <span data-ttu-id="d731b-106">EF Core applique ces filtres automatiquement à toutes les requêtes LINQ impliquant ces types d’entités.</span><span class="sxs-lookup"><span data-stu-id="d731b-106">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="d731b-107">EF Core les applique également aux types d’entités, référencés indirectement par le biais de l’utilisation de la propriété include ou de navigation.</span><span class="sxs-lookup"><span data-stu-id="d731b-107">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="d731b-108">Voici deux applications courantes de cette fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="d731b-108">Some common applications of this feature are:</span></span>

* <span data-ttu-id="d731b-109">**Suppression réversible** : un type d’entité définit une `IsDeleted` propriété.</span><span class="sxs-lookup"><span data-stu-id="d731b-109">**Soft delete** - An Entity Type defines an `IsDeleted` property.</span></span>
* <span data-ttu-id="d731b-110">Architecture **mutualisée : un** type d’entité définit une `TenantId` propriété.</span><span class="sxs-lookup"><span data-stu-id="d731b-110">**Multi-tenancy** - An Entity Type defines a `TenantId` property.</span></span>

## <a name="example"></a><span data-ttu-id="d731b-111">Exemple</span><span class="sxs-lookup"><span data-stu-id="d731b-111">Example</span></span>

<span data-ttu-id="d731b-112">L’exemple suivant montre comment utiliser des filtres de requête globaux pour implémenter des comportements de requête d’architecture mutualisée et de suppression réversible dans un modèle de blog simple.</span><span class="sxs-lookup"><span data-stu-id="d731b-112">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="d731b-113">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="d731b-113">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="d731b-114">Tout d’abord définissez les entités :</span><span class="sxs-lookup"><span data-stu-id="d731b-114">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Entities.cs#Entities)]

<span data-ttu-id="d731b-115">Notez la déclaration d’un `_tenantId` champ sur l' `Blog` entité.</span><span class="sxs-lookup"><span data-stu-id="d731b-115">Note the declaration of a `_tenantId` field on the `Blog` entity.</span></span> <span data-ttu-id="d731b-116">Ce champ sera utilisé pour associer chaque instance de blog à un locataire spécifique.</span><span class="sxs-lookup"><span data-stu-id="d731b-116">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="d731b-117">Également défini est une `IsDeleted` propriété sur le `Post` type d’entité.</span><span class="sxs-lookup"><span data-stu-id="d731b-117">Also defined is an `IsDeleted` property on the `Post` entity type.</span></span> <span data-ttu-id="d731b-118">Cette propriété est utilisée pour suivre si une instance de publication a été « supprimée de manière réversible ».</span><span class="sxs-lookup"><span data-stu-id="d731b-118">This property is used to keep track of whether a post instance has been "soft-deleted".</span></span> <span data-ttu-id="d731b-119">Autrement dit, l’instance est marquée comme supprimée sans que les données sous-jacentes soient réellement supprimées.</span><span class="sxs-lookup"><span data-stu-id="d731b-119">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="d731b-120">Ensuite, configurez les filtres de requête dans `OnModelCreating` à l’aide de l' `HasQueryFilter` API.</span><span class="sxs-lookup"><span data-stu-id="d731b-120">Next, configure the query filters in `OnModelCreating` using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/BloggingContext.cs#FilterConfiguration)]

<span data-ttu-id="d731b-121">Les expressions de prédicat transmises aux `HasQueryFilter` appels seront désormais automatiquement appliquées à toutes les requêtes LINQ pour ces types.</span><span class="sxs-lookup"><span data-stu-id="d731b-121">The predicate expressions passed to the `HasQueryFilter` calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="d731b-122">Notez l’utilisation d’un champ de niveau d’instance DbContext : `_tenantId` permet de définir le client en cours.</span><span class="sxs-lookup"><span data-stu-id="d731b-122">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="d731b-123">Les filtres au niveau du modèle utilisent la valeur de l’instance de contexte correcte (c’est-à-dire celle qui exécute la requête).</span><span class="sxs-lookup"><span data-stu-id="d731b-123">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="d731b-124">Il n’est actuellement pas possible de définir plusieurs filtres de requête sur la même entité ; seul le dernier sera appliqué.</span><span class="sxs-lookup"><span data-stu-id="d731b-124">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="d731b-125">Toutefois, vous pouvez définir un filtre unique avec plusieurs conditions à l’aide de l' `AND` opérateur logique ([ `&&` en C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span><span class="sxs-lookup"><span data-stu-id="d731b-125">However, you can define a single filter with multiple conditions using the logical `AND` operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="d731b-126">Utilisation des navigations</span><span class="sxs-lookup"><span data-stu-id="d731b-126">Use of navigations</span></span>

<span data-ttu-id="d731b-127">Vous pouvez également utiliser des navigations dans la définition de filtres de requête globaux.</span><span class="sxs-lookup"><span data-stu-id="d731b-127">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="d731b-128">L’utilisation de navigations dans le filtre de requête entraîne l’application de filtres de requête de manière récursive.</span><span class="sxs-lookup"><span data-stu-id="d731b-128">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="d731b-129">Lorsque EF Core développe les navigations utilisées dans les filtres de requête, il applique également des filtres de requête définis sur les entités référencées.</span><span class="sxs-lookup"><span data-stu-id="d731b-129">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

> [!NOTE]
> <span data-ttu-id="d731b-130">Actuellement EF Core ne détecte pas les cycles dans les définitions de filtre de requête globale. vous devez donc être prudent lorsque vous les définissez.</span><span class="sxs-lookup"><span data-stu-id="d731b-130">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="d731b-131">S’ils sont spécifiés de manière incorrecte, les cycles peuvent entraîner des boucles infinies lors de la traduction de la requête.</span><span class="sxs-lookup"><span data-stu-id="d731b-131">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="d731b-132">Accès à une entité avec un filtre de requête à l’aide de la navigation requise</span><span class="sxs-lookup"><span data-stu-id="d731b-132">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="d731b-133">L’utilisation de la navigation requise pour accéder à l’entité qui a un filtre de requête global défini peut entraîner des résultats inattendus.</span><span class="sxs-lookup"><span data-stu-id="d731b-133">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="d731b-134">La navigation obligatoire s’attend à ce que l’entité associée soit toujours présente.</span><span class="sxs-lookup"><span data-stu-id="d731b-134">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="d731b-135">Si l’entité connexe nécessaire est filtrée par le filtre de requête, l’entité parente n’est pas dans le résultat.</span><span class="sxs-lookup"><span data-stu-id="d731b-135">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="d731b-136">Vous risquez de recevoir moins d’éléments que prévu dans le résultat.</span><span class="sxs-lookup"><span data-stu-id="d731b-136">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="d731b-137">Pour illustrer le problème, nous pouvons utiliser `Blog` les `Post` entités et spécifiées ci-dessus, ainsi que la `OnModelCreating` méthode suivante :</span><span class="sxs-lookup"><span data-stu-id="d731b-137">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following `OnModelCreating` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#IncorrectFilter)]

<span data-ttu-id="d731b-138">Le modèle peut être amorcé avec les données suivantes :</span><span class="sxs-lookup"><span data-stu-id="d731b-138">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#SeedData)]

<span data-ttu-id="d731b-139">Le problème peut être observé lors de l’exécution de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="d731b-139">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#Queries)]

<span data-ttu-id="d731b-140">Avec le programme d’installation ci-dessus, la première requête retourne tous les 6 `Post` s, mais la deuxième requête retourne uniquement 3.</span><span class="sxs-lookup"><span data-stu-id="d731b-140">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="d731b-141">Cette incompatibilité se produit parce que `Include` la méthode de la deuxième requête charge les `Blog` entités associées.</span><span class="sxs-lookup"><span data-stu-id="d731b-141">This mismatch happens because `Include` method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="d731b-142">Dans la mesure où la navigation entre `Blog` et `Post` est requise, EF Core utilise `INNER JOIN` lors de la construction de la requête :</span><span class="sxs-lookup"><span data-stu-id="d731b-142">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Posts] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE [b].[Url] LIKE N'%fish%'
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="d731b-143">L’utilisation des `INNER JOIN` filtres exclut tous les `Post` s dont les `Blog` s associés ont été supprimés par un filtre de requête global.</span><span class="sxs-lookup"><span data-stu-id="d731b-143">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="d731b-144">Vous pouvez le résoudre à l’aide de la navigation facultative au lieu de obligatoire.</span><span class="sxs-lookup"><span data-stu-id="d731b-144">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="d731b-145">De cette façon, la première requête reste la même qu’avant, mais la deuxième requête génère `LEFT JOIN` et retourne 6 résultats.</span><span class="sxs-lookup"><span data-stu-id="d731b-145">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#OptionalNavigation)]

<span data-ttu-id="d731b-146">Une autre approche consiste à spécifier des filtres cohérents sur les `Blog` `Post` entités et.</span><span class="sxs-lookup"><span data-stu-id="d731b-146">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="d731b-147">De cette façon, les filtres de correspondance sont appliqués à `Blog` et à `Post` .</span><span class="sxs-lookup"><span data-stu-id="d731b-147">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="d731b-148">`Post`qui pourraient finir par un état inattendu sont supprimés et les deux requêtes retournent 3 résultats.</span><span class="sxs-lookup"><span data-stu-id="d731b-148">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#MatchingFilters)]

## <a name="disabling-filters"></a><span data-ttu-id="d731b-149">Désactivation des filtres</span><span class="sxs-lookup"><span data-stu-id="d731b-149">Disabling Filters</span></span>

<span data-ttu-id="d731b-150">Les filtres peuvent être désactivés pour des requêtes LINQ individuelles à l’aide de l’opérateur <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A>.</span><span class="sxs-lookup"><span data-stu-id="d731b-150">Filters may be disabled for individual LINQ queries by using the <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A> operator.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="d731b-151">Limites</span><span class="sxs-lookup"><span data-stu-id="d731b-151">Limitations</span></span>

<span data-ttu-id="d731b-152">Les filtres de requête globale présentent les limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="d731b-152">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="d731b-153">Les filtres ne peuvent être définis que pour le type d’entité racine d’une hiérarchie d’héritage.</span><span class="sxs-lookup"><span data-stu-id="d731b-153">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
