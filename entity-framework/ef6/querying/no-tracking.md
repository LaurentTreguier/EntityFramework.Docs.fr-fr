---
title: Requêtes de non-suivi-EF6
description: Requêtes de suivi sans suivi dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
uid: ef6/querying/no-tracking
ms.openlocfilehash: eb9e29c219e0cdf1e379cf8bb925f4226b1434a9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620293"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="a0433-103">sans suivi</span><span class="sxs-lookup"><span data-stu-id="a0433-103">No-Tracking Queries</span></span>
<span data-ttu-id="a0433-104">Il peut arriver que vous souhaitiez récupérer des entités à partir d’une requête, mais que ces entités ne soient pas suivies par le contexte.</span><span class="sxs-lookup"><span data-stu-id="a0433-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="a0433-105">Cela peut entraîner de meilleures performances lors de l’interrogation d’un grand nombre d’entités dans des scénarios en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="a0433-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="a0433-106">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="a0433-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="a0433-107">Une nouvelle méthode d’extension AsNoTracking permet d’exécuter n’importe quelle requête de cette manière.</span><span class="sxs-lookup"><span data-stu-id="a0433-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="a0433-108">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a0433-108">For example:</span></span>  

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
