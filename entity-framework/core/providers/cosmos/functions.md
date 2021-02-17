---
title: Mappages de fonction-fournisseur Azure Cosmos DB-EF Core
description: Mappages de fonctions du fournisseur de EF Core Azure Cosmos DB
author: bricelam
ms.date: 1/26/2021
uid: core/providers/cosmos/functions
ms.openlocfilehash: d4d45ba7db50befb5eb775feed0af44468ee3f74
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543586"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="d54c5-103">Mappages de fonctions du fournisseur de EF Core Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="d54c5-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="d54c5-104">Cette page affiche les membres .NET qui sont traduits en fonctions SQL lors de l’utilisation du fournisseur Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="d54c5-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="d54c5-105">.NET</span><span class="sxs-lookup"><span data-stu-id="d54c5-105">.NET</span></span>                          | <span data-ttu-id="d54c5-106">SQL</span><span class="sxs-lookup"><span data-stu-id="d54c5-106">SQL</span></span>                              | <span data-ttu-id="d54c5-107">Ajouté à</span><span class="sxs-lookup"><span data-stu-id="d54c5-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="d54c5-108">collecte. Contains (Item)</span><span class="sxs-lookup"><span data-stu-id="d54c5-108">collection.Contains(item)</span></span>     | <span data-ttu-id="d54c5-109">@item DANS @collection</span><span class="sxs-lookup"><span data-stu-id="d54c5-109">@item IN @collection</span></span>
<span data-ttu-id="d54c5-110">EF. Functions. Random ()</span><span class="sxs-lookup"><span data-stu-id="d54c5-110">EF.Functions.Random()</span></span>         | <span data-ttu-id="d54c5-111">RAND ()</span><span class="sxs-lookup"><span data-stu-id="d54c5-111">RAND()</span></span>                           | <span data-ttu-id="d54c5-112">EF Core 6.0</span><span class="sxs-lookup"><span data-stu-id="d54c5-112">EF Core 6.0</span></span>
<span data-ttu-id="d54c5-113">stringValue. Contains (valeur)</span><span class="sxs-lookup"><span data-stu-id="d54c5-113">stringValue.Contains(value)</span></span>   | <span data-ttu-id="d54c5-114">CONTIENT ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="d54c5-114">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="d54c5-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="d54c5-115">EF Core 5.0</span></span>
<span data-ttu-id="d54c5-116">stringValue. EndsWith (valeur)</span><span class="sxs-lookup"><span data-stu-id="d54c5-116">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="d54c5-117">ENDSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="d54c5-117">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="d54c5-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="d54c5-118">EF Core 5.0</span></span>
<span data-ttu-id="d54c5-119">stringValue. FirstOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="d54c5-119">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="d54c5-120">GAUCHE ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="d54c5-120">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="d54c5-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="d54c5-121">EF Core 5.0</span></span>
<span data-ttu-id="d54c5-122">stringValue. LastOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="d54c5-122">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="d54c5-123">DROITE ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="d54c5-123">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="d54c5-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="d54c5-124">EF Core 5.0</span></span>
<span data-ttu-id="d54c5-125">stringValue. StartsWith (valeur)</span><span class="sxs-lookup"><span data-stu-id="d54c5-125">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="d54c5-126">STARTSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="d54c5-126">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="d54c5-127">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="d54c5-127">EF Core 5.0</span></span>
