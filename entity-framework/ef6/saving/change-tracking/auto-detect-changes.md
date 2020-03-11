---
title: Détection automatique des modifications-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
ms.openlocfilehash: 9af85fd7ca48a14432a1f33c59079fc438ef8810
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416976"
---
# <a name="automatic-detect-changes"></a>Détection automatique des modifications
Lors de l’utilisation de la plupart des entités POCO, la détermination de la manière dont une entité a changé (et par conséquent les mises à jour qui doivent être envoyées à la base de données) est gérée par l’algorithme de détection des modifications. Détecter les modifications fonctionne en détectant les différences entre les valeurs de propriété actuelles de l’entité et les valeurs de propriétés d’origine qui sont stockées dans un instantané lorsque l’entité a été interrogée ou attachée. Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.  

Par défaut, Entity Framework effectue la détection automatique des modifications quand les méthodes suivantes sont appelées :  

- DbSet.Find  
- DbSet. local  
- DbSet. Add  
- DbSet. AddRange
- DbSet. Remove  
- DbSet. RemoveRange
- DbSet. Attach  
- DbContext.SaveChanges  
- DbContext. GetValidationErrors  
- DbContext.Entry  
- DbChangeTracker. entrées  

## <a name="disabling-automatic-detection-of-changes"></a>Désactivation de la détection automatique des modifications  

Si vous effectuez le suivi d’un grand nombre d’entités dans votre contexte et que vous appelez l’une de ces méthodes plusieurs fois dans une boucle, vous pouvez obtenir des améliorations significatives en matière de performances en désactivant la détection des modifications pour la durée de la boucle. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    try
    {
        context.Configuration.AutoDetectChangesEnabled = false;

        // Make many calls in a loop
        foreach (var blog in aLotOfBlogs)
        {
            context.Blogs.Add(blog);
        }
    }
    finally
    {
        context.Configuration.AutoDetectChangesEnabled = true;
    }
}
```  

N’oubliez pas de réactiver la détection des modifications après la boucle. nous avons utilisé un try/finally pour vous assurer qu’il est toujours réactivé, même si le code de la boucle lève une exception.  

Une alternative à la désactivation et à la réactivation consiste à maintenir la détection automatique des modifications désactivées à tout moment et dans le contexte de l’appel. ChangeTracker. DetectChanges explicitement ou utilisez les proxys de suivi des modifications avec soin. Ces deux options sont avancées et peuvent facilement introduire des bogues subtils dans votre application et les utiliser avec précaution.  

Si vous devez ajouter ou supprimer un grand nombre d’objets d’un contexte, envisagez d’utiliser DbSet. AddRange et DbSet. RemoveRange. Ces méthodes détectent automatiquement les modifications une seule fois après l’exécution des opérations d’ajout ou de suppression. 
