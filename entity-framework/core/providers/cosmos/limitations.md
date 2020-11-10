---
title: Fournisseur de Azure Cosmos DB-limitations-EF Core
description: Limitations du fournisseur Entity Framework Core Azure Cosmos DB par rapport à d’autres fournisseurs
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: f7517954b854c8ea1020c039e490b4fb0dea7181
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430194"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="a60b4-103">Limitations des fournisseurs de EF Core Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="a60b4-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="a60b4-104">Le fournisseur Cosmos présente un certain nombre de limitations.</span><span class="sxs-lookup"><span data-stu-id="a60b4-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="a60b4-105">La plupart de ces limitations résultent de limitations dans le moteur de base de données Cosmos sous-jacent et ne sont pas spécifiques à EF.</span><span class="sxs-lookup"><span data-stu-id="a60b4-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="a60b4-106">Mais la plupart [n’ont pas encore été implémentées](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="a60b4-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="a60b4-107">Limitations temporaires</span><span class="sxs-lookup"><span data-stu-id="a60b4-107">Temporary limitations</span></span>

- <span data-ttu-id="a60b4-108">`Include` les appels ne sont pas pris en charge</span><span class="sxs-lookup"><span data-stu-id="a60b4-108">`Include` calls are not supported</span></span>
- <span data-ttu-id="a60b4-109">`Join` les appels ne sont pas pris en charge</span><span class="sxs-lookup"><span data-stu-id="a60b4-109">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="a60b4-110">Limitations du kit de développement logiciel Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="a60b4-110">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="a60b4-111">Seules les méthodes Async sont fournies</span><span class="sxs-lookup"><span data-stu-id="a60b4-111">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="a60b4-112">Étant donné qu’il n’existe aucune version de synchronisation des méthodes de bas niveau EF Core s’appuie sur, la fonctionnalité correspondante est actuellement implémentée en appelant `.Wait()` sur le retourné `Task` .</span><span class="sxs-lookup"><span data-stu-id="a60b4-112">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="a60b4-113">Cela signifie que l’utilisation de méthodes telles que `SaveChanges` , ou à `ToList` la place de leurs équivalents Async, peut entraîner un blocage dans votre application</span><span class="sxs-lookup"><span data-stu-id="a60b4-113">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="a60b4-114">Limitations de Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="a60b4-114">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="a60b4-115">Vous pouvez voir la vue d’ensemble complète des [fonctionnalités prises en charge par Azure Cosmos DB](/azure/cosmos-db/modeling-data). il s’agit des différences les plus notables par rapport à une base de données relationnelle :</span><span class="sxs-lookup"><span data-stu-id="a60b4-115">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="a60b4-116">Les transactions initiées par le client ne sont pas prises en charge</span><span class="sxs-lookup"><span data-stu-id="a60b4-116">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="a60b4-117">Certaines requêtes de plusieurs partitions sont plus lentes en fonction des opérateurs impliqués (par exemple `Skip/Take` ou `OFFSET LIMIT` )</span><span class="sxs-lookup"><span data-stu-id="a60b4-117">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
