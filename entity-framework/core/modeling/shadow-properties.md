---
title: Propriétés des ombres et des indexeurs-EF Core
description: Configuration des propriétés d’ombre et de l’indexeur dans un modèle de Entity Framework Core
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: d419de2da2a9fc29e675dde76e824217347d2e9c
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003430"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="fa44e-103">Propriétés de l’instantané et de l’indexeur</span><span class="sxs-lookup"><span data-stu-id="fa44e-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="fa44e-104">Les propriétés Shadow sont des propriétés qui ne sont pas définies dans votre classe d’entité .NET, mais qui sont définies pour ce type d’entité dans le modèle EF Core.</span><span class="sxs-lookup"><span data-stu-id="fa44e-104">Shadow properties are properties that aren't defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="fa44e-105">La valeur et l’état de ces propriétés sont gérés uniquement dans le dispositif de suivi des modifications.</span><span class="sxs-lookup"><span data-stu-id="fa44e-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="fa44e-106">Les propriétés Shadow sont utiles quand il y a des données dans la base de données qui ne doivent pas être exposées sur les types d’entités mappés.</span><span class="sxs-lookup"><span data-stu-id="fa44e-106">Shadow properties are useful when there's data in the database that shouldn't be exposed on the mapped entity types.</span></span>

<span data-ttu-id="fa44e-107">Les propriétés de l’indexeur sont des propriétés de type d’entité, qui sont sauvegardées par un [indexeur](/dotnet/csharp/programming-guide/indexers/) dans une classe d’entité .net.</span><span class="sxs-lookup"><span data-stu-id="fa44e-107">Indexer properties are entity type properties, which are backed by an [indexer](/dotnet/csharp/programming-guide/indexers/) in .NET entity class.</span></span> <span data-ttu-id="fa44e-108">Ils sont accessibles à l’aide de l’indexeur sur les instances de classe .NET.</span><span class="sxs-lookup"><span data-stu-id="fa44e-108">They can be accessed using the indexer on the .NET class instances.</span></span> <span data-ttu-id="fa44e-109">Elle vous permet également d’ajouter des propriétés supplémentaires au type d’entité sans modifier la classe CLR.</span><span class="sxs-lookup"><span data-stu-id="fa44e-109">It also allows you to add additional properties to the entity type without changing the CLR class.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="fa44e-110">Propriétés de l’ombre de la clé étrangère</span><span class="sxs-lookup"><span data-stu-id="fa44e-110">Foreign key shadow properties</span></span>

<span data-ttu-id="fa44e-111">Les propriétés Shadow sont le plus souvent utilisées pour les propriétés de clé étrangère, où la relation entre deux entités est représentée par une valeur de clé étrangère dans la base de données, mais la relation est gérée sur les types d’entité à l’aide des propriétés de navigation entre les types d’entités.</span><span class="sxs-lookup"><span data-stu-id="fa44e-111">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="fa44e-112">Par Convention, EF introduit une propriété Shadow lorsqu’une relation est détectée, mais qu’aucune propriété de clé étrangère n’est trouvée dans la classe d’entité dépendante.</span><span class="sxs-lookup"><span data-stu-id="fa44e-112">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="fa44e-113">La propriété sera nommée `<navigation property name><principal key property name>` (la navigation sur l’entité dépendante, qui pointe vers l’entité principale, est utilisée pour le nommage).</span><span class="sxs-lookup"><span data-stu-id="fa44e-113">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="fa44e-114">Si le nom de la propriété de clé principale comprend le nom de la propriété de navigation, le nom sera simplement `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="fa44e-114">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="fa44e-115">S’il n’existe aucune propriété de navigation sur l’entité dépendante, le nom du type de principal est utilisé à la place.</span><span class="sxs-lookup"><span data-stu-id="fa44e-115">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="fa44e-116">Par exemple, la liste de code suivante entraînera l' `BlogId` introduction d’une propriété Shadow à l' `Post` entité :</span><span class="sxs-lookup"><span data-stu-id="fa44e-116">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="fa44e-117">Configuration des propriétés Shadow</span><span class="sxs-lookup"><span data-stu-id="fa44e-117">Configuring shadow properties</span></span>

<span data-ttu-id="fa44e-118">Vous pouvez utiliser l’API Fluent pour configurer les propriétés Shadow.</span><span class="sxs-lookup"><span data-stu-id="fa44e-118">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="fa44e-119">Une fois que vous avez appelé la surcharge de chaîne de `Property` , vous pouvez chaîner n’importe quel appel de configuration que vous feriez pour d’autres propriétés.</span><span class="sxs-lookup"><span data-stu-id="fa44e-119">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="fa44e-120">Dans l’exemple suivant, étant donné que n' `Blog` a aucune propriété CLR nommée `LastUpdated` , une propriété Shadow est créée :</span><span class="sxs-lookup"><span data-stu-id="fa44e-120">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="fa44e-121">Si le nom fourni à la `Property` méthode correspond au nom d’une propriété existante (une propriété Shadow ou une propriété Shadow définie sur la classe d’entité), le code configurera cette propriété existante au lieu d’introduire une nouvelle propriété Shadow.</span><span class="sxs-lookup"><span data-stu-id="fa44e-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="fa44e-122">Accès aux propriétés Shadow</span><span class="sxs-lookup"><span data-stu-id="fa44e-122">Accessing shadow properties</span></span>

<span data-ttu-id="fa44e-123">Les valeurs de propriété Shadow peuvent être obtenues et modifiées par le biais de l' `ChangeTracker` API :</span><span class="sxs-lookup"><span data-stu-id="fa44e-123">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="fa44e-124">Les propriétés Shadow peuvent être référencées dans des requêtes LINQ via la `EF.Property` méthode statique :</span><span class="sxs-lookup"><span data-stu-id="fa44e-124">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="fa44e-125">Les propriétés Shadow ne sont pas accessibles après une requête de non-suivi, car les entités retournées ne sont pas suivies par le dispositif de suivi des modifications.</span><span class="sxs-lookup"><span data-stu-id="fa44e-125">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="configuring-indexer-properties"></a><span data-ttu-id="fa44e-126">Configuration des propriétés de l’indexeur</span><span class="sxs-lookup"><span data-stu-id="fa44e-126">Configuring indexer properties</span></span>

<span data-ttu-id="fa44e-127">Vous pouvez utiliser l’API Fluent pour configurer les propriétés de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="fa44e-127">You can use the Fluent API to configure indexer properties.</span></span> <span data-ttu-id="fa44e-128">Une fois que vous avez appelé la méthode `IndexerProperty` , vous pouvez chaîner n’importe quel appel de configuration que vous feriez pour d’autres propriétés.</span><span class="sxs-lookup"><span data-stu-id="fa44e-128">Once you've called the method `IndexerProperty`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="fa44e-129">Dans l’exemple suivant, `Blog` a un indexeur défini et il sera utilisé pour créer une propriété d’indexeur.</span><span class="sxs-lookup"><span data-stu-id="fa44e-129">In the following sample, `Blog` has an indexer defined and it will be used to create an indexer property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexerProperty.cs?name=ShadowProperty&highlight=3)]

<span data-ttu-id="fa44e-130">Si le nom fourni à la `IndexerProperty` méthode correspond au nom d’une propriété d’indexeur existante, le code configurera cette propriété existante.</span><span class="sxs-lookup"><span data-stu-id="fa44e-130">If the name supplied to the `IndexerProperty` method matches the name of an existing indexer property, then the code will configure that existing property.</span></span> <span data-ttu-id="fa44e-131">Si le type d’entité a une propriété, qui est stockée par une propriété sur la classe d’entité, une exception est levée, car les propriétés de l’indexeur ne doivent être accessibles qu’à l’aide de l’indexeur.</span><span class="sxs-lookup"><span data-stu-id="fa44e-131">If the entity type has a property, which is backed by a property on the entity class, then an exception is thrown since indexer properties must only be accessed via the indexer.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="fa44e-132">Types d’entités du conteneur des propriétés</span><span class="sxs-lookup"><span data-stu-id="fa44e-132">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="fa44e-133">La prise en charge des types d’entité de conteneur de propriétés a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="fa44e-133">Support for Property bag entity types was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="fa44e-134">Les types d’entités qui contiennent uniquement des propriétés d’indexeur sont connus sous le nom de types d’entité de conteneur de propriétés.</span><span class="sxs-lookup"><span data-stu-id="fa44e-134">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="fa44e-135">Ces types d’entités n’ont pas de propriétés Shadow, à la place, EF crée des propriétés d’indexeur.</span><span class="sxs-lookup"><span data-stu-id="fa44e-135">These entity types don't have shadow properties, instead EF will create indexer properties.</span></span> <span data-ttu-id="fa44e-136">Actuellement `Dictionary<string, object>` , seul est pris en charge en tant que type d’entité de conteneur de propriétés.</span><span class="sxs-lookup"><span data-stu-id="fa44e-136">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="fa44e-137">Elle doit être configurée en tant que type d’entité partagée avec un nom unique et la `DbSet` propriété correspondante doit être implémentée à l’aide d’un `Set` appel.</span><span class="sxs-lookup"><span data-stu-id="fa44e-137">It must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
