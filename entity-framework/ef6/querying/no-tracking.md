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
# <a name="no-tracking-queries"></a>sans suivi
Il peut arriver que vous souhaitiez récupérer des entités à partir d’une requête, mais que ces entités ne soient pas suivies par le contexte. Cela peut entraîner de meilleures performances lors de l’interrogation d’un grand nombre d’entités dans des scénarios en lecture seule. Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.  

Une nouvelle méthode d’extension AsNoTracking permet d’exécuter n’importe quelle requête de cette manière. Par exemple :  

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
