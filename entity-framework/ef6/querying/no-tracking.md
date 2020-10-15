---
title: Requêtes de No-Tracking-EF6
description: No-Tracking des requêtes dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: 74826d3052cf6a249796db2845fa6b96e5ecb8f9
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065860"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="d119b-103">sans suivi</span><span class="sxs-lookup"><span data-stu-id="d119b-103">No-Tracking Queries</span></span>
<span data-ttu-id="d119b-104">Il peut arriver que vous souhaitiez récupérer des entités à partir d’une requête, mais que ces entités ne soient pas suivies par le contexte.</span><span class="sxs-lookup"><span data-stu-id="d119b-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="d119b-105">Cela peut entraîner de meilleures performances lors de l’interrogation d’un grand nombre d’entités dans des scénarios en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="d119b-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="d119b-106">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="d119b-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="d119b-107">Une nouvelle méthode d’extension AsNoTracking permet d’exécuter n’importe quelle requête de cette manière.</span><span class="sxs-lookup"><span data-stu-id="d119b-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="d119b-108">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d119b-108">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs without tracking them
    var blogs1 = context.Blogs.AsNoTracking();

    // Query for some blogs without tracking them
    var blogs2 = context.Blogs
                        .Where(b => b.Name.Contains(".NET"))
                        .AsNoTracking()
                        .ToList();
}
```  
