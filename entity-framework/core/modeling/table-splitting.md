---
title: Fractionnement de table-EF Core
description: Comment configurer le fractionnement de table à l’aide de Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: e7428bc516a69310b6a6f521acc49aee0ba9f802
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526497"
---
# <a name="table-splitting"></a><span data-ttu-id="abf02-103">Fractionnement de table</span><span class="sxs-lookup"><span data-stu-id="abf02-103">Table Splitting</span></span>

<span data-ttu-id="abf02-104">EF Core permet de mapper deux entités ou plus sur une seule ligne.</span><span class="sxs-lookup"><span data-stu-id="abf02-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="abf02-105">C’est ce que l’on appelle le _fractionnement de table_ ou le _partage de table_.</span><span class="sxs-lookup"><span data-stu-id="abf02-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="abf02-106">Configuration</span><span class="sxs-lookup"><span data-stu-id="abf02-106">Configuration</span></span>

<span data-ttu-id="abf02-107">Pour utiliser le fractionnement de table, les types d’entités doivent être mappés à la même table, les clés primaires sont mappées aux mêmes colonnes et au moins une relation configurée entre la clé primaire d’un type d’entité et une autre dans la même table.</span><span class="sxs-lookup"><span data-stu-id="abf02-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="abf02-108">Un scénario courant de fractionnement de table n’utilise qu’un sous-ensemble des colonnes de la table pour améliorer les performances ou l’encapsulation.</span><span class="sxs-lookup"><span data-stu-id="abf02-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="abf02-109">Dans cet exemple, `Order` représente un sous-ensemble de `DetailedOrder` .</span><span class="sxs-lookup"><span data-stu-id="abf02-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="abf02-110">Outre la configuration requise, nous appelons `Property(o => o.Status).HasColumnName("Status")` pour mapper `DetailedOrder.Status` à la même colonne que `Order.Status` .</span><span class="sxs-lookup"><span data-stu-id="abf02-110">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> <span data-ttu-id="abf02-111">Pour plus de contexte, consultez l' [exemple de projet complet](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) .</span><span class="sxs-lookup"><span data-stu-id="abf02-111">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="abf02-112">Utilisation</span><span class="sxs-lookup"><span data-stu-id="abf02-112">Usage</span></span>

<span data-ttu-id="abf02-113">L’enregistrement et l’interrogation d’entités à l’aide du fractionnement de table s’effectuent de la même façon que les autres entités :</span><span class="sxs-lookup"><span data-stu-id="abf02-113">Saving and querying entities using table splitting is done in the same way as other entities:</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a><span data-ttu-id="abf02-114">Entité dépendante facultative</span><span class="sxs-lookup"><span data-stu-id="abf02-114">Optional dependent entity</span></span>

> [!NOTE]
> <span data-ttu-id="abf02-115">Cette fonctionnalité a été introduite dans EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="abf02-115">This feature was introduced in EF Core 3.0.</span></span>

<span data-ttu-id="abf02-116">Si toutes les colonnes utilisées par une entité dépendante se trouvent `NULL` dans la base de données, aucune instance n’est créée lors de la requête.</span><span class="sxs-lookup"><span data-stu-id="abf02-116">If all of the columns used by a dependent entity are `NULL` in the database, then no instance for it will be created when queried.</span></span> <span data-ttu-id="abf02-117">Cela permet de modéliser une entité dépendante facultative, où la propriété de relation sur le principal est null.</span><span class="sxs-lookup"><span data-stu-id="abf02-117">This allows modeling an optional dependent entity, where the relationship property on the principal would be null.</span></span> <span data-ttu-id="abf02-118">Notez que cela se produit également si toutes les propriétés dépendantes sont facultatives et ont la valeur `null` , ce qui n’est peut-être pas prévu.</span><span class="sxs-lookup"><span data-stu-id="abf02-118">Note that this would also happen if all of the dependent's properties are optional and set to `null`, which might not be expected.</span></span>

## <a name="concurrency-tokens"></a><span data-ttu-id="abf02-119">Jetons d’accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="abf02-119">Concurrency tokens</span></span>

<span data-ttu-id="abf02-120">Si l’un des types d’entité partageant une table a un jeton d’accès concurrentiel, il doit également être inclus dans tous les autres types d’entités.</span><span class="sxs-lookup"><span data-stu-id="abf02-120">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types as well.</span></span> <span data-ttu-id="abf02-121">Cela est nécessaire pour éviter une valeur de jeton d’accès concurrentiel obsolète lorsqu’une seule des entités mappées à la même table est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="abf02-121">This is necessary in order to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="abf02-122">Pour éviter d’exposer le jeton d’accès concurrentiel au code consommateur, il est possible de créer un en tant que [propriété Shadow](xref:core/modeling/shadow-properties):</span><span class="sxs-lookup"><span data-stu-id="abf02-122">To avoid exposing the concurrency token to the consuming code, it's possible the create one as a [shadow property](xref:core/modeling/shadow-properties):</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
