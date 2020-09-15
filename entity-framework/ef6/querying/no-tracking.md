---
title: Requêtes de non-suivi-EF6
description: Requêtes de suivi sans suivi dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: ea4f05eb7a9b95fba55f70f249876bc9c5630f18
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073897"
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
