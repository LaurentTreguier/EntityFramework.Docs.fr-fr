---
title: 'Filtres de requête globale : EF Core'
description: Utilisation de filtres de requête globaux pour filtrer les résultats avec Entity Framework Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: d5793760ea2e61111416284db8d5a8102dd51a41
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616389"
---
# <a name="global-query-filters"></a><span data-ttu-id="45a64-103">Filtres de requête globale</span><span class="sxs-lookup"><span data-stu-id="45a64-103">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="45a64-104">Cette fonctionnalité date de la publication d’EF Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="45a64-104">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="45a64-105">Les filtres de requête globaux sont des prédicats de requête LINQ appliqués aux types d’entité dans le modèle de métadonnées (généralement dans *OnModelCreating*).</span><span class="sxs-lookup"><span data-stu-id="45a64-105">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="45a64-106">Un prédicat de requête est une expression booléenne qui est généralement passée à l’opérateur de requête LINQ *Where* .</span><span class="sxs-lookup"><span data-stu-id="45a64-106">A query predicate is a boolean expression typically passed to the LINQ *Where* query operator.</span></span>  <span data-ttu-id="45a64-107">EF Core applique ces filtres automatiquement à toutes les requêtes LINQ impliquant ces types d’entités.</span><span class="sxs-lookup"><span data-stu-id="45a64-107">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="45a64-108">EF Core les applique également aux types d’entités, référencés indirectement par le biais de l’utilisation de la propriété include ou de navigation.</span><span class="sxs-lookup"><span data-stu-id="45a64-108">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="45a64-109">Voici deux applications courantes de cette fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="45a64-109">Some common applications of this feature are:</span></span>

* <span data-ttu-id="45a64-110">**Suppression réversible** : un type d’entité définit une propriété *IsDeleted*.</span><span class="sxs-lookup"><span data-stu-id="45a64-110">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="45a64-111">Architecture **mutualisée : un** type d’entité définit une propriété *TenantId* .</span><span class="sxs-lookup"><span data-stu-id="45a64-111">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="45a64-112">Exemple</span><span class="sxs-lookup"><span data-stu-id="45a64-112">Example</span></span>

<span data-ttu-id="45a64-113">L’exemple suivant montre comment utiliser des filtres de requête globaux pour implémenter des comportements de requête d’architecture mutualisée et de suppression réversible dans un modèle de blog simple.</span><span class="sxs-lookup"><span data-stu-id="45a64-113">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="45a64-114">Vous pouvez afficher un [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) d’architecture mutualisée et des [exemples à l’aide des navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="45a64-114">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span>

<span data-ttu-id="45a64-115">Tout d’abord définissez les entités :</span><span class="sxs-lookup"><span data-stu-id="45a64-115">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="45a64-116">Notez la déclaration d’un champ _tenantId_ sur l’entité _Blog_.</span><span class="sxs-lookup"><span data-stu-id="45a64-116">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="45a64-117">Ce champ sera utilisé pour associer chaque instance de blog à un locataire spécifique.</span><span class="sxs-lookup"><span data-stu-id="45a64-117">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="45a64-118">Une propriété _IsDeleted_ est également définie sur le type d’entité _Post_.</span><span class="sxs-lookup"><span data-stu-id="45a64-118">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="45a64-119">Cette propriété est utilisée pour suivre si une instance de _publication_ a été « supprimée de manière réversible ».</span><span class="sxs-lookup"><span data-stu-id="45a64-119">This property is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="45a64-120">Autrement dit, l’instance est marquée comme supprimée sans que les données sous-jacentes soient réellement supprimées.</span><span class="sxs-lookup"><span data-stu-id="45a64-120">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="45a64-121">Ensuite, configurez les filtres de requête dans _OnModelCreating_ à l’aide de l’API `HasQueryFilter`.</span><span class="sxs-lookup"><span data-stu-id="45a64-121">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="45a64-122">Les expressions de prédicat passées aux appels _HasQueryFilter_ seront désormais automatiquement appliquées à toutes les requêtes LINQ pour ces types.</span><span class="sxs-lookup"><span data-stu-id="45a64-122">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="45a64-123">Notez l’utilisation d’un champ de niveau d’instance DbContext : `_tenantId` permet de définir le client en cours.</span><span class="sxs-lookup"><span data-stu-id="45a64-123">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="45a64-124">Les filtres au niveau du modèle utilisent la valeur de l’instance de contexte correcte (c’est-à-dire celle qui exécute la requête).</span><span class="sxs-lookup"><span data-stu-id="45a64-124">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="45a64-125">Il n’est actuellement pas possible de définir plusieurs filtres de requête sur la même entité ; seul le dernier sera appliqué.</span><span class="sxs-lookup"><span data-stu-id="45a64-125">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="45a64-126">Toutefois, vous pouvez définir un filtre unique avec plusieurs conditions à l’aide de l’opérateur _and_ logique ([ `&&` en C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span><span class="sxs-lookup"><span data-stu-id="45a64-126">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="45a64-127">Utilisation des navigations</span><span class="sxs-lookup"><span data-stu-id="45a64-127">Use of navigations</span></span>

<span data-ttu-id="45a64-128">Vous pouvez également utiliser des navigations dans la définition de filtres de requête globaux.</span><span class="sxs-lookup"><span data-stu-id="45a64-128">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="45a64-129">L’utilisation de navigations dans le filtre de requête entraîne l’application de filtres de requête de manière récursive.</span><span class="sxs-lookup"><span data-stu-id="45a64-129">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="45a64-130">Lorsque EF Core développe les navigations utilisées dans les filtres de requête, il applique également des filtres de requête définis sur les entités référencées.</span><span class="sxs-lookup"><span data-stu-id="45a64-130">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

> [!NOTE]
> <span data-ttu-id="45a64-131">Actuellement EF Core ne détecte pas les cycles dans les définitions de filtre de requête globale. vous devez donc être prudent lorsque vous les définissez.</span><span class="sxs-lookup"><span data-stu-id="45a64-131">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="45a64-132">S’ils sont spécifiés de manière incorrecte, les cycles peuvent entraîner des boucles infinies lors de la traduction de la requête.</span><span class="sxs-lookup"><span data-stu-id="45a64-132">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="45a64-133">Accès à une entité avec un filtre de requête à l’aide de la navigation requise</span><span class="sxs-lookup"><span data-stu-id="45a64-133">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="45a64-134">L’utilisation de la navigation requise pour accéder à l’entité qui a un filtre de requête global défini peut entraîner des résultats inattendus.</span><span class="sxs-lookup"><span data-stu-id="45a64-134">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="45a64-135">La navigation obligatoire s’attend à ce que l’entité associée soit toujours présente.</span><span class="sxs-lookup"><span data-stu-id="45a64-135">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="45a64-136">Si l’entité connexe nécessaire est filtrée par le filtre de requête, l’entité parente n’est pas dans le résultat.</span><span class="sxs-lookup"><span data-stu-id="45a64-136">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="45a64-137">Vous risquez de recevoir moins d’éléments que prévu dans le résultat.</span><span class="sxs-lookup"><span data-stu-id="45a64-137">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="45a64-138">Pour illustrer le problème, nous pouvons utiliser `Blog` les `Post` entités et spécifiées ci-dessus, ainsi que la méthode _OnModelCreating_ suivante :</span><span class="sxs-lookup"><span data-stu-id="45a64-138">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="45a64-139">Le modèle peut être amorcé avec les données suivantes :</span><span class="sxs-lookup"><span data-stu-id="45a64-139">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="45a64-140">Le problème peut être observé lors de l’exécution de deux requêtes :</span><span class="sxs-lookup"><span data-stu-id="45a64-140">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="45a64-141">Avec le programme d’installation ci-dessus, la première requête retourne tous les 6 `Post` s, mais la deuxième requête retourne uniquement 3.</span><span class="sxs-lookup"><span data-stu-id="45a64-141">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="45a64-142">Cette incompatibilité se produit parce que la méthode _include_ de la deuxième requête charge les `Blog` entités associées.</span><span class="sxs-lookup"><span data-stu-id="45a64-142">This mismatch happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="45a64-143">Dans la mesure où la navigation entre `Blog` et `Post` est requise, EF Core utilise `INNER JOIN` lors de la construction de la requête :</span><span class="sxs-lookup"><span data-stu-id="45a64-143">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="45a64-144">L’utilisation des `INNER JOIN` filtres exclut tous les `Post` s dont les `Blog` s associés ont été supprimés par un filtre de requête global.</span><span class="sxs-lookup"><span data-stu-id="45a64-144">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="45a64-145">Vous pouvez le résoudre à l’aide de la navigation facultative au lieu de obligatoire.</span><span class="sxs-lookup"><span data-stu-id="45a64-145">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="45a64-146">De cette façon, la première requête reste la même qu’avant, mais la deuxième requête génère `LEFT JOIN` et retourne 6 résultats.</span><span class="sxs-lookup"><span data-stu-id="45a64-146">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="45a64-147">Une autre approche consiste à spécifier des filtres cohérents sur les `Blog` `Post` entités et.</span><span class="sxs-lookup"><span data-stu-id="45a64-147">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="45a64-148">De cette façon, les filtres de correspondance sont appliqués à `Blog` et à `Post` .</span><span class="sxs-lookup"><span data-stu-id="45a64-148">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="45a64-149">`Post`qui pourraient finir par un état inattendu sont supprimés et les deux requêtes retournent 3 résultats.</span><span class="sxs-lookup"><span data-stu-id="45a64-149">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="45a64-150">Désactivation des filtres</span><span class="sxs-lookup"><span data-stu-id="45a64-150">Disabling Filters</span></span>

<span data-ttu-id="45a64-151">Les filtres peuvent être désactivés pour des requêtes LINQ individuelles à l’aide de l’opérateur `IgnoreQueryFilters()`.</span><span class="sxs-lookup"><span data-stu-id="45a64-151">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="45a64-152">Limites</span><span class="sxs-lookup"><span data-stu-id="45a64-152">Limitations</span></span>

<span data-ttu-id="45a64-153">Les filtres de requête globale présentent les limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="45a64-153">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="45a64-154">Les filtres ne peuvent être définis que pour le type d’entité racine d’une hiérarchie d’héritage.</span><span class="sxs-lookup"><span data-stu-id="45a64-154">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
