---
title: Mappages de fonction-fournisseur Azure Cosmos DB-EF Core
description: Mappages de fonctions du fournisseur de EF Core Azure Cosmos DB
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066607"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="48baa-103">Mappages de fonctions du fournisseur de EF Core Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="48baa-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="48baa-104">Cette page affiche les membres .NET qui sont traduits en fonctions SQL lors de l’utilisation du fournisseur Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="48baa-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="48baa-105">.NET</span><span class="sxs-lookup"><span data-stu-id="48baa-105">.NET</span></span>                          | <span data-ttu-id="48baa-106">SQL</span><span class="sxs-lookup"><span data-stu-id="48baa-106">SQL</span></span>                              | <span data-ttu-id="48baa-107">Ajouté à</span><span class="sxs-lookup"><span data-stu-id="48baa-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="48baa-108">collecte. Contains (Item)</span><span class="sxs-lookup"><span data-stu-id="48baa-108">collection.Contains(item)</span></span>     | <span data-ttu-id="48baa-109">@item DANS @collection</span><span class="sxs-lookup"><span data-stu-id="48baa-109">@item IN @collection</span></span>
<span data-ttu-id="48baa-110">stringValue. Contains (valeur)</span><span class="sxs-lookup"><span data-stu-id="48baa-110">stringValue.Contains(value)</span></span>   | <span data-ttu-id="48baa-111">CONTIENT ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="48baa-111">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="48baa-112">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="48baa-112">EF Core 5.0</span></span>
<span data-ttu-id="48baa-113">stringValue. EndsWith (valeur)</span><span class="sxs-lookup"><span data-stu-id="48baa-113">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="48baa-114">ENDSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="48baa-114">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="48baa-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="48baa-115">EF Core 5.0</span></span>
<span data-ttu-id="48baa-116">stringValue. FirstOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="48baa-116">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="48baa-117">GAUCHE ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="48baa-117">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="48baa-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="48baa-118">EF Core 5.0</span></span>
<span data-ttu-id="48baa-119">stringValue. LastOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="48baa-119">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="48baa-120">DROITE ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="48baa-120">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="48baa-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="48baa-121">EF Core 5.0</span></span>
<span data-ttu-id="48baa-122">stringValue. StartsWith (valeur)</span><span class="sxs-lookup"><span data-stu-id="48baa-122">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="48baa-123">STARTSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="48baa-123">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="48baa-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="48baa-124">EF Core 5.0</span></span>
