---
title: Fournisseur de Azure Cosmos DB-limitations-EF Core
description: Limitations du fournisseur Entity Framework Core Azure Cosmos DB par rapport à d’autres fournisseurs
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: e4d1c38da14d1e722797f8543313a69c7fb088cc
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064035"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="80c7b-103">Limitations des fournisseurs de EF Core Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="80c7b-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="80c7b-104">Le fournisseur Cosmos présente un certain nombre de limitations.</span><span class="sxs-lookup"><span data-stu-id="80c7b-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="80c7b-105">La plupart de ces limitations résultent de limitations dans le moteur de base de données Cosmos sous-jacent et ne sont pas spécifiques à EF.</span><span class="sxs-lookup"><span data-stu-id="80c7b-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="80c7b-106">Mais la plupart [n’ont pas encore été implémentées](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="80c7b-106">But most simply [haven't been implemented yet](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="80c7b-107">Limitations temporaires</span><span class="sxs-lookup"><span data-stu-id="80c7b-107">Temporary limitations</span></span>

- <span data-ttu-id="80c7b-108">Même s’il existe un seul type d’entité sans héritage mappé à un conteneur, il possède toujours une propriété de discriminateur.</span><span class="sxs-lookup"><span data-stu-id="80c7b-108">Even if there is only one entity type without inheritance mapped to a container it still has a discriminator property.</span></span>
- <span data-ttu-id="80c7b-109">Les types d’entités avec des clés de partition ne fonctionnent pas correctement dans certains scénarios</span><span class="sxs-lookup"><span data-stu-id="80c7b-109">Entity types with partition keys don't work correctly in some scenarios</span></span>
- <span data-ttu-id="80c7b-110">`Include` les appels ne sont pas pris en charge</span><span class="sxs-lookup"><span data-stu-id="80c7b-110">`Include` calls are not supported</span></span>
- <span data-ttu-id="80c7b-111">`Join` les appels ne sont pas pris en charge</span><span class="sxs-lookup"><span data-stu-id="80c7b-111">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="80c7b-112">Limitations du kit de développement logiciel Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="80c7b-112">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="80c7b-113">Seules les méthodes Async sont fournies</span><span class="sxs-lookup"><span data-stu-id="80c7b-113">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="80c7b-114">Étant donné qu’il n’existe aucune version de synchronisation des méthodes de bas niveau EF Core s’appuie sur, la fonctionnalité correspondante est actuellement implémentée en appelant `.Wait()` sur le retourné `Task` .</span><span class="sxs-lookup"><span data-stu-id="80c7b-114">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="80c7b-115">Cela signifie que l’utilisation de méthodes telles que `SaveChanges` , ou à `ToList` la place de leurs équivalents Async, peut entraîner un blocage dans votre application</span><span class="sxs-lookup"><span data-stu-id="80c7b-115">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="80c7b-116">Limitations de Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="80c7b-116">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="80c7b-117">Vous pouvez voir la vue d’ensemble complète des [fonctionnalités prises en charge par Azure Cosmos DB](/azure/cosmos-db/modeling-data). il s’agit des différences les plus notables par rapport à une base de données relationnelle :</span><span class="sxs-lookup"><span data-stu-id="80c7b-117">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="80c7b-118">Les transactions initiées par le client ne sont pas prises en charge</span><span class="sxs-lookup"><span data-stu-id="80c7b-118">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="80c7b-119">Certaines requêtes de plusieurs partitions ne sont pas prises en charge ou sont beaucoup plus lentes en fonction des opérateurs impliqués</span><span class="sxs-lookup"><span data-stu-id="80c7b-119">Some cross-partition queries are either not supported or much slower depending on the operators involved</span></span>
