---
title: Types d’entité sans clé-EF Core
description: Comment configurer les types d’entité sans clé à l’aide de Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 00e8f17c88fd51e39df3c1e45c648c203bbbe324
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103124"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="71a3f-103">Types d’entité sans clé</span><span class="sxs-lookup"><span data-stu-id="71a3f-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="71a3f-104">Cette fonctionnalité a été ajoutée à EF Core 2,1 sous le nom des types de requêtes.</span><span class="sxs-lookup"><span data-stu-id="71a3f-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="71a3f-105">Dans EF Core 3,0, le concept a été renommé en types d’entité sans clé.</span><span class="sxs-lookup"><span data-stu-id="71a3f-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span> <span data-ttu-id="71a3f-106">L' `[Keyless]` annotation de données est devenue disponible dans EFCore 5,0.</span><span class="sxs-lookup"><span data-stu-id="71a3f-106">The `[Keyless]` Data Annotation became available in EFCore 5.0.</span></span>

<span data-ttu-id="71a3f-107">En plus des types d’entités standard, un modèle de EF Core peut contenir des _types d’entité sans_clé, qui peuvent être utilisés pour exécuter des requêtes de base de données sur des données qui ne contiennent pas de valeurs de clés.</span><span class="sxs-lookup"><span data-stu-id="71a3f-107">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="71a3f-108">Définition des types d’entités sans clé</span><span class="sxs-lookup"><span data-stu-id="71a3f-108">Defining Keyless entity types</span></span>

<span data-ttu-id="71a3f-109">Les types d’entités Keyless peuvent être définis à l’aide de l’annotation de données ou de l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="71a3f-109">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="71a3f-110">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="71a3f-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="71a3f-111">API Fluent</span><span class="sxs-lookup"><span data-stu-id="71a3f-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="71a3f-112">Caractéristiques des types d’entité sans clé</span><span class="sxs-lookup"><span data-stu-id="71a3f-112">Keyless entity types characteristics</span></span>

<span data-ttu-id="71a3f-113">Les types d’entité sans clé prennent en charge un grand nombre des mêmes fonctionnalités de mappage que les types d’entités standard, tels que le mappage d’héritage et les propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="71a3f-113">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="71a3f-114">Sur les magasins relationnels, ils peuvent configurer les objets et les colonnes de base de données cibles par le biais de méthodes d’API Fluent ou d’annotations de données.</span><span class="sxs-lookup"><span data-stu-id="71a3f-114">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="71a3f-115">Toutefois, ils diffèrent des types d’entités standard en ce qu’ils :</span><span class="sxs-lookup"><span data-stu-id="71a3f-115">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="71a3f-116">Impossible d’avoir une clé définie.</span><span class="sxs-lookup"><span data-stu-id="71a3f-116">Cannot have a key defined.</span></span>
- <span data-ttu-id="71a3f-117">Ne sont jamais suivis pour les modifications apportées à _DbContext_ et, par conséquent, ne sont jamais insérées, mises à jour ou supprimées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="71a3f-117">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="71a3f-118">Ne sont jamais découvertes par Convention.</span><span class="sxs-lookup"><span data-stu-id="71a3f-118">Are never discovered by convention.</span></span>
- <span data-ttu-id="71a3f-119">Ne prennent en charge qu’un sous-ensemble de fonctionnalités de mappage de navigation, notamment :</span><span class="sxs-lookup"><span data-stu-id="71a3f-119">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="71a3f-120">Ils peuvent ne jamais agir comme la terminaison principale d’une relation.</span><span class="sxs-lookup"><span data-stu-id="71a3f-120">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="71a3f-121">Ils n’ont peut-être pas de navigation vers les entités détenues</span><span class="sxs-lookup"><span data-stu-id="71a3f-121">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="71a3f-122">Ils peuvent uniquement contenir des propriétés de navigation de référence pointant vers des entités normales.</span><span class="sxs-lookup"><span data-stu-id="71a3f-122">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="71a3f-123">Les entités ne peuvent pas contenir de propriétés de navigation pour les types d’entités Keyless.</span><span class="sxs-lookup"><span data-stu-id="71a3f-123">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="71a3f-124">Doit être configuré avec une `[Keyless]` annotation de données ou un `.HasNoKey()` appel de méthode.</span><span class="sxs-lookup"><span data-stu-id="71a3f-124">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="71a3f-125">Peut être mappé à une _requête de définition_.</span><span class="sxs-lookup"><span data-stu-id="71a3f-125">May be mapped to a _defining query_.</span></span> <span data-ttu-id="71a3f-126">Une requête de définition est une requête déclarée dans le modèle qui joue le rôle d’une source de données pour un type d’entité sans clé.</span><span class="sxs-lookup"><span data-stu-id="71a3f-126">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="71a3f-127">Scénarios d’usage</span><span class="sxs-lookup"><span data-stu-id="71a3f-127">Usage scenarios</span></span>

<span data-ttu-id="71a3f-128">Voici quelques-uns des principaux scénarios d’utilisation pour les types d’entité sans clé :</span><span class="sxs-lookup"><span data-stu-id="71a3f-128">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="71a3f-129">Servir de type de retour pour les [requêtes SQL brutes](xref:core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="71a3f-129">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="71a3f-130">Mappage à des vues de base de données qui ne contiennent pas de clé primaire.</span><span class="sxs-lookup"><span data-stu-id="71a3f-130">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="71a3f-131">Mappage à des tables qui n’ont pas de clé primaire définie.</span><span class="sxs-lookup"><span data-stu-id="71a3f-131">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="71a3f-132">Mappage aux requêtes définies dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="71a3f-132">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="71a3f-133">Mapper à des objets de base de données</span><span class="sxs-lookup"><span data-stu-id="71a3f-133">Mapping to database objects</span></span>

<span data-ttu-id="71a3f-134">Le mappage d’un type d’entité clé-inférieur à un objet de `ToTable` base `ToView` de données s’effectue à l’aide de l’API Fluent ou.</span><span class="sxs-lookup"><span data-stu-id="71a3f-134">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="71a3f-135">Du point de vue de EF Core, l’objet de base de données spécifié dans cette méthode est une _vue_, ce qui signifie qu’elle est traitée comme une source de requête en lecture seule et ne peut pas être la cible d’opérations de mise à jour, d’insertion ou de suppression.</span><span class="sxs-lookup"><span data-stu-id="71a3f-135">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="71a3f-136">Toutefois, cela ne signifie pas que l’objet de base de données est réellement requis pour être une vue de base de données.</span><span class="sxs-lookup"><span data-stu-id="71a3f-136">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="71a3f-137">Il peut également s’agir d’une table de base de données qui sera traitée en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="71a3f-137">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="71a3f-138">À l’inverse, pour les types d’entités standard, EF Core suppose qu’un objet de base de `ToTable` données spécifié dans la méthode peut être traité comme une _table_, ce qui signifie qu’il peut être utilisé comme source de requête, mais également ciblé par les opérations Update, DELETE et insert.</span><span class="sxs-lookup"><span data-stu-id="71a3f-138">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="71a3f-139">En fait, vous pouvez spécifier le nom d’une vue de base `ToTable` de données dans et tout fonctionne correctement tant que la vue est configurée pour être mise à jour sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="71a3f-139">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="71a3f-140">`ToView`suppose que l’objet existe déjà dans la base de données et qu’il n’est pas créé par des migrations.</span><span class="sxs-lookup"><span data-stu-id="71a3f-140">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="71a3f-141">Exemple</span><span class="sxs-lookup"><span data-stu-id="71a3f-141">Example</span></span>

<span data-ttu-id="71a3f-142">L’exemple suivant montre comment utiliser les types d’entités Keyless pour interroger une vue de base de données.</span><span class="sxs-lookup"><span data-stu-id="71a3f-142">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="71a3f-143">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="71a3f-143">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="71a3f-144">Tout d’abord, nous définissons un blog simple et un modèle de publication :</span><span class="sxs-lookup"><span data-stu-id="71a3f-144">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="71a3f-145">Ensuite, nous définissons une vue de base de données simple qui nous permettra d’interroger le nombre de publications associées à chaque blog :</span><span class="sxs-lookup"><span data-stu-id="71a3f-145">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="71a3f-146">Ensuite, nous définissons une classe qui contiendra le résultat de la vue de base de données :</span><span class="sxs-lookup"><span data-stu-id="71a3f-146">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="71a3f-147">Ensuite, vous configurez le type d’entité _OnModelCreating_ « sans clé `HasNoKey` » dans OnModelCreating à l’aide de l’API.</span><span class="sxs-lookup"><span data-stu-id="71a3f-147">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="71a3f-148">Nous utilisons l’API de configuration Fluent pour configurer le mappage pour le type d’entité « sans clé » :</span><span class="sxs-lookup"><span data-stu-id="71a3f-148">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="71a3f-149">Ensuite, nous configurons `DbContext` le pour inclure `DbSet<T>`les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="71a3f-149">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="71a3f-150">Enfin, nous pouvons interroger la vue de base de données de la manière standard :</span><span class="sxs-lookup"><span data-stu-id="71a3f-150">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="71a3f-151">Notez que nous avons également défini une propriété de requête au niveau du contexte (DbSet) pour agir en tant que racine pour les requêtes sur ce type.</span><span class="sxs-lookup"><span data-stu-id="71a3f-151">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
