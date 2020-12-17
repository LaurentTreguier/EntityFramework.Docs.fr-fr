---
title: Fournisseur de Azure Cosmos DB-limitations-EF Core
description: Limitations du fournisseur Entity Framework Core Azure Cosmos DB par rapport à d’autres fournisseurs
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 088f593dddd9b5691d87566d7e31a699ba90d7c5
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635716"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="17e02-103">Limitations des fournisseurs de EF Core Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="17e02-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="17e02-104">Le fournisseur Cosmos présente un certain nombre de limitations.</span><span class="sxs-lookup"><span data-stu-id="17e02-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="17e02-105">La plupart de ces limitations résultent de limitations dans le moteur de base de données Cosmos sous-jacent et ne sont pas spécifiques à EF.</span><span class="sxs-lookup"><span data-stu-id="17e02-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="17e02-106">Mais la plupart [n’ont pas encore été implémentées](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="17e02-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

<span data-ttu-id="17e02-107">Voici quelques-unes des fonctionnalités couramment demandées :</span><span class="sxs-lookup"><span data-stu-id="17e02-107">These are some of the commonly requested features:</span></span>

- [<span data-ttu-id="17e02-108">`Include` supporter</span><span class="sxs-lookup"><span data-stu-id="17e02-108">`Include` support</span></span>](https://github.com/dotnet/efcore/issues/16920)
- [<span data-ttu-id="17e02-109">`Join` supporter</span><span class="sxs-lookup"><span data-stu-id="17e02-109">`Join` support</span></span>](https://github.com/dotnet/efcore/issues/17314)
- [<span data-ttu-id="17e02-110">Les collections de types primitifs prennent en charge</span><span class="sxs-lookup"><span data-stu-id="17e02-110">Collections of primitive types support</span></span>](https://github.com/dotnet/efcore/issues/14762)

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="17e02-111">Limitations du kit de développement logiciel Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="17e02-111">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="17e02-112">Seules les méthodes Async sont fournies</span><span class="sxs-lookup"><span data-stu-id="17e02-112">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="17e02-113">Étant donné qu’il n’existe aucune version de synchronisation des méthodes de bas niveau EF Core s’appuie sur, la fonctionnalité correspondante est actuellement implémentée en appelant `.Wait()` sur le retourné `Task` .</span><span class="sxs-lookup"><span data-stu-id="17e02-113">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="17e02-114">Cela signifie que l’utilisation de méthodes telles que `SaveChanges` , ou à `ToList` la place de leurs équivalents Async, peut entraîner un blocage dans votre application</span><span class="sxs-lookup"><span data-stu-id="17e02-114">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="17e02-115">Limitations de Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="17e02-115">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="17e02-116">Vous pouvez voir la vue d’ensemble complète des [fonctionnalités prises en charge par Azure Cosmos DB](/azure/cosmos-db/modeling-data). il s’agit des différences les plus notables par rapport à une base de données relationnelle :</span><span class="sxs-lookup"><span data-stu-id="17e02-116">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="17e02-117">Les transactions initiées par le client ne sont pas prises en charge</span><span class="sxs-lookup"><span data-stu-id="17e02-117">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="17e02-118">Certaines requêtes de plusieurs partitions sont plus lentes en fonction des opérateurs impliqués (par exemple `Skip/Take` ou `OFFSET LIMIT` )</span><span class="sxs-lookup"><span data-stu-id="17e02-118">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
