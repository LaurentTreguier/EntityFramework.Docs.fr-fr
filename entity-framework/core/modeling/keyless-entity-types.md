---
title: Types d’entités sans clé - EF Core
description: Comment configurer les types d’entités sans clé à l’aide de l’entité Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434212"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="58a17-103">Types d’entité sans clé</span><span class="sxs-lookup"><span data-stu-id="58a17-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="58a17-104">Cette fonctionnalité a été ajoutée dans EF Core 2.1 sous le nom de types de requêtes.</span><span class="sxs-lookup"><span data-stu-id="58a17-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="58a17-105">Dans EF Core 3.0, le concept a été rebaptisé en types d’entités sans clé.</span><span class="sxs-lookup"><span data-stu-id="58a17-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="58a17-106">En plus des types d’entités ordinaires, un modèle EF Core peut contenir des _types d’entités sans clé,_ qui peuvent être utilisés pour effectuer des requêtes de base de données contre des données qui ne contiennent pas de valeurs clés.</span><span class="sxs-lookup"><span data-stu-id="58a17-106">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="58a17-107">Définir les types d’entités sans clé</span><span class="sxs-lookup"><span data-stu-id="58a17-107">Defining Keyless entity types</span></span>

<span data-ttu-id="58a17-108">Les types d’entités sans clé peuvent être définis à l’aide de l’annotation des données ou de l’API fluide :</span><span class="sxs-lookup"><span data-stu-id="58a17-108">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="58a17-109">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="58a17-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="58a17-110">API Fluent</span><span class="sxs-lookup"><span data-stu-id="58a17-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="58a17-111">Caractéristiques des types d’entités sans clé</span><span class="sxs-lookup"><span data-stu-id="58a17-111">Keyless entity types characteristics</span></span>

<span data-ttu-id="58a17-112">Les types d’entités sans clé prennent en charge bon nombre des mêmes capacités de cartographie que les types d’entités ordinaires, comme la cartographie des héritages et les propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="58a17-112">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="58a17-113">Sur les magasins relationnels, ils peuvent configurer les objets et colonnes de base de données cibles via des méthodes D’API fluides ou des annotations de données.</span><span class="sxs-lookup"><span data-stu-id="58a17-113">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="58a17-114">Cependant, ils sont différents des types d’entités régulières en ce qu’ils :</span><span class="sxs-lookup"><span data-stu-id="58a17-114">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="58a17-115">Je ne peux pas avoir une clé définie.</span><span class="sxs-lookup"><span data-stu-id="58a17-115">Cannot have a key defined.</span></span>
- <span data-ttu-id="58a17-116">Ne sont jamais suivis pour les modifications du _DbContext_ et ne sont donc jamais insérés, mis à jour ou supprimés sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="58a17-116">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="58a17-117">Ne sont jamais découverts par convention.</span><span class="sxs-lookup"><span data-stu-id="58a17-117">Are never discovered by convention.</span></span>
- <span data-ttu-id="58a17-118">Ne supportez qu’un sous-ensemble de capacités de cartographie de navigation, en particulier :</span><span class="sxs-lookup"><span data-stu-id="58a17-118">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="58a17-119">Ils ne peuvent jamais agir comme la principale fin d’une relation.</span><span class="sxs-lookup"><span data-stu-id="58a17-119">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="58a17-120">Il se peut qu’ils n’aient pas de navigations vers des entités possédées</span><span class="sxs-lookup"><span data-stu-id="58a17-120">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="58a17-121">Ils ne peuvent contenir que des propriétés de navigation de référence pointant vers des entités régulières.</span><span class="sxs-lookup"><span data-stu-id="58a17-121">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="58a17-122">Les entités ne peuvent pas contenir de propriétés de navigation à des types d’entités sans clé.</span><span class="sxs-lookup"><span data-stu-id="58a17-122">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="58a17-123">Besoin d’être configuré avec `[Keyless]` une `.HasNoKey()` annotation de données ou un appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="58a17-123">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="58a17-124">Peut être cartographié à une _requête déterminante_.</span><span class="sxs-lookup"><span data-stu-id="58a17-124">May be mapped to a _defining query_.</span></span> <span data-ttu-id="58a17-125">Une requête déterminante est une requête déclarée dans le modèle qui agit comme source de données pour un type d’entité sans clé.</span><span class="sxs-lookup"><span data-stu-id="58a17-125">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="58a17-126">Scénarios d’usage</span><span class="sxs-lookup"><span data-stu-id="58a17-126">Usage scenarios</span></span>

<span data-ttu-id="58a17-127">Voici quelques-uns des principaux scénarios d’utilisation pour les types d’entités sans clé :</span><span class="sxs-lookup"><span data-stu-id="58a17-127">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="58a17-128">Servir de type de retour pour [les requêtes SQL brutes](xref:core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="58a17-128">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="58a17-129">Cartographier les vues de base de données qui ne contiennent pas de clé principale.</span><span class="sxs-lookup"><span data-stu-id="58a17-129">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="58a17-130">Cartographier les tableaux qui n’ont pas de clé primaire définie.</span><span class="sxs-lookup"><span data-stu-id="58a17-130">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="58a17-131">Cartographie aux requêtes définies dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="58a17-131">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="58a17-132">Cartographier les objets de base de données</span><span class="sxs-lookup"><span data-stu-id="58a17-132">Mapping to database objects</span></span>

<span data-ttu-id="58a17-133">La cartographie d’un type d’entité `ToTable` sans `ToView` clé à un objet de base de données est réalisée à l’aide de l’API ou couramment.</span><span class="sxs-lookup"><span data-stu-id="58a17-133">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="58a17-134">Du point de vue d’EF Core, l’objet de base de données spécifié dans cette méthode est une _vue,_ ce qui signifie qu’il est traité comme une source de requête la plus lisante et ne peut pas être la cible de mises à jour, d’insertion ou de suppression d’opérations.</span><span class="sxs-lookup"><span data-stu-id="58a17-134">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="58a17-135">Toutefois, cela ne signifie pas que l’objet de base de données est réellement nécessaire pour être une vue de base de données.</span><span class="sxs-lookup"><span data-stu-id="58a17-135">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="58a17-136">Il peut être alternativement un tableau de base de données qui sera traité comme lu uniquement.</span><span class="sxs-lookup"><span data-stu-id="58a17-136">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="58a17-137">Inversement, pour les types d’entités régulières, `ToTable` EF Core suppose qu’un objet de base de données spécifié dans la méthode peut être traité comme un _tableau,_ ce qui signifie qu’il peut être utilisé comme source de requête, mais aussi ciblé par la mise à jour, supprimer et insérer les opérations.</span><span class="sxs-lookup"><span data-stu-id="58a17-137">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="58a17-138">En fait, vous pouvez spécifier `ToTable` le nom d’une vue de base de données dans et tout devrait fonctionner bien tant que la vue est configurée pour être updatable sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="58a17-138">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="58a17-139">`ToView`suppose que l’objet existe déjà dans la base de données et qu’il ne sera pas créé par les migrations.</span><span class="sxs-lookup"><span data-stu-id="58a17-139">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="58a17-140">Exemple</span><span class="sxs-lookup"><span data-stu-id="58a17-140">Example</span></span>

<span data-ttu-id="58a17-141">L’exemple suivant montre comment utiliser des types d’entités sans clé pour interroger une vue de base de données.</span><span class="sxs-lookup"><span data-stu-id="58a17-141">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="58a17-142">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="58a17-142">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="58a17-143">Tout d’abord, nous définissons un modèle simple de blog et de poteau :</span><span class="sxs-lookup"><span data-stu-id="58a17-143">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="58a17-144">Ensuite, nous définissons une simple vue de base de données qui nous permettra d’interroger le nombre de messages associés à chaque blog:</span><span class="sxs-lookup"><span data-stu-id="58a17-144">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="58a17-145">Ensuite, nous définissons une classe pour tenir le résultat de la vue de base de données:</span><span class="sxs-lookup"><span data-stu-id="58a17-145">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="58a17-146">Ensuite, nous configurons le type d’entité `HasNoKey` sans clé dans _OnModelCreating_ à l’aide de l’API.</span><span class="sxs-lookup"><span data-stu-id="58a17-146">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="58a17-147">Nous utilisons l’API de configuration fluide pour configurer la cartographie pour le type d’entité sans clé :</span><span class="sxs-lookup"><span data-stu-id="58a17-147">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="58a17-148">Ensuite, nous `DbContext` configurons `DbSet<T>`le pour inclure le :</span><span class="sxs-lookup"><span data-stu-id="58a17-148">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="58a17-149">Enfin, nous pouvons interroger la vue de base de données de la manière standard :</span><span class="sxs-lookup"><span data-stu-id="58a17-149">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="58a17-150">Notez que nous avons également défini une propriété de requête de niveau contexte (DbSet) pour agir comme une racine pour les requêtes contre ce type.</span><span class="sxs-lookup"><span data-stu-id="58a17-150">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
