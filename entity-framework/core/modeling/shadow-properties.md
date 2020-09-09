---
title: Propriétés Shadow-EF Core
description: Configuration des propriétés Shadow dans un modèle Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 06dbba7a4f6da247e6afde96debb0762fa55ce7e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616720"
---
# <a name="shadow-properties"></a><span data-ttu-id="e7865-103">Propriétés cachées</span><span class="sxs-lookup"><span data-stu-id="e7865-103">Shadow Properties</span></span>

<span data-ttu-id="e7865-104">Les propriétés Shadow sont des propriétés qui ne sont pas définies dans votre classe d’entité .NET, mais qui sont définies pour ce type d’entité dans le modèle EF Core.</span><span class="sxs-lookup"><span data-stu-id="e7865-104">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="e7865-105">La valeur et l’état de ces propriétés sont gérés uniquement dans le dispositif de suivi des modifications.</span><span class="sxs-lookup"><span data-stu-id="e7865-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="e7865-106">Les propriétés Shadow sont utiles lorsque la base de données contient des données qui ne doivent pas être exposées sur les types d’entités mappés.</span><span class="sxs-lookup"><span data-stu-id="e7865-106">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="e7865-107">Propriétés de l’ombre de la clé étrangère</span><span class="sxs-lookup"><span data-stu-id="e7865-107">Foreign key shadow properties</span></span>

<span data-ttu-id="e7865-108">Les propriétés Shadow sont le plus souvent utilisées pour les propriétés de clé étrangère, où la relation entre deux entités est représentée par une valeur de clé étrangère dans la base de données, mais la relation est gérée sur les types d’entité à l’aide des propriétés de navigation entre les types d’entités.</span><span class="sxs-lookup"><span data-stu-id="e7865-108">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="e7865-109">Par Convention, EF introduit une propriété Shadow lorsqu’une relation est détectée, mais qu’aucune propriété de clé étrangère n’est trouvée dans la classe d’entité dépendante.</span><span class="sxs-lookup"><span data-stu-id="e7865-109">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="e7865-110">La propriété sera nommée `<navigation property name><principal key property name>` (la navigation sur l’entité dépendante, qui pointe vers l’entité principale, est utilisée pour le nommage).</span><span class="sxs-lookup"><span data-stu-id="e7865-110">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="e7865-111">Si le nom de la propriété de clé principale comprend le nom de la propriété de navigation, le nom sera simplement `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="e7865-111">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="e7865-112">S’il n’existe aucune propriété de navigation sur l’entité dépendante, le nom du type de principal est utilisé à la place.</span><span class="sxs-lookup"><span data-stu-id="e7865-112">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="e7865-113">Par exemple, la liste de code suivante entraînera l' `BlogId` introduction d’une propriété Shadow à l' `Post` entité :</span><span class="sxs-lookup"><span data-stu-id="e7865-113">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="e7865-114">Configuration des propriétés Shadow</span><span class="sxs-lookup"><span data-stu-id="e7865-114">Configuring shadow properties</span></span>

<span data-ttu-id="e7865-115">Vous pouvez utiliser l’API Fluent pour configurer les propriétés Shadow.</span><span class="sxs-lookup"><span data-stu-id="e7865-115">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="e7865-116">Une fois que vous avez appelé la surcharge de chaîne de `Property` , vous pouvez chaîner n’importe quel appel de configuration que vous feriez pour d’autres propriétés.</span><span class="sxs-lookup"><span data-stu-id="e7865-116">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="e7865-117">Dans l’exemple suivant, étant donné que n' `Blog` a aucune propriété CLR nommée `LastUpdated` , une propriété Shadow est créée :</span><span class="sxs-lookup"><span data-stu-id="e7865-117">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="e7865-118">Si le nom fourni à la `Property` méthode correspond au nom d’une propriété existante (une propriété Shadow ou une propriété Shadow définie sur la classe d’entité), le code configurera cette propriété existante au lieu d’introduire une nouvelle propriété Shadow.</span><span class="sxs-lookup"><span data-stu-id="e7865-118">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="e7865-119">Accès aux propriétés Shadow</span><span class="sxs-lookup"><span data-stu-id="e7865-119">Accessing shadow properties</span></span>

<span data-ttu-id="e7865-120">Les valeurs de propriété Shadow peuvent être obtenues et modifiées par le biais de l' `ChangeTracker` API :</span><span class="sxs-lookup"><span data-stu-id="e7865-120">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="e7865-121">Les propriétés Shadow peuvent être référencées dans des requêtes LINQ via la `EF.Property` méthode statique :</span><span class="sxs-lookup"><span data-stu-id="e7865-121">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="e7865-122">Les propriétés Shadow ne sont pas accessibles après une requête de non-suivi, car les entités retournées ne sont pas suivies par le dispositif de suivi des modifications.</span><span class="sxs-lookup"><span data-stu-id="e7865-122">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>
