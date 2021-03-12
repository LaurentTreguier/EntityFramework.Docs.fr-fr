---
title: Alternance entre plusieurs modèles avec le même type DbContext-EF Core
description: Alternance entre plusieurs modèles ayant le même type DbContext à l’aide de Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/dynamic-model
ms.openlocfilehash: 0c418000b85c508569b8146af63bff205ae6d222
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024028"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>Alternance entre plusieurs modèles ayant le même type DbContext

Le modèle intégré `OnModelCreating` peut utiliser une propriété du contexte pour modifier la façon dont le modèle est généré. Par exemple, supposons que vous souhaitiez configurer une entité différemment en fonction de certaines propriétés :

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

Malheureusement, ce code ne fonctionnerait pas tel quel, car EF génère le modèle et `OnModelCreating` ne s’exécute qu’une seule fois, en mettant en cache le résultat pour des raisons de performances. Toutefois, vous pouvez vous connecter au mécanisme de mise en cache du modèle pour que EF prenne en charge la propriété qui produit des modèles différents.

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

EF utilise `IModelCacheKeyFactory` pour générer des clés de cache pour les modèles ; par défaut, EF suppose que pour un type de contexte donné, le modèle sera le même, donc l’implémentation par défaut de ce service retourne une clé qui contient simplement le type de contexte. Pour produire différents modèles à partir du même type de contexte, vous devez remplacer le `IModelCacheKeyFactory` service par l’implémentation correcte ; la clé générée sera comparée à d’autres clés de modèle à l’aide de la `Equals` méthode, en tenant compte de toutes les variables qui affectent le modèle.

L’implémentation suivante prend le `UseIntProperty` en compte lors de la génération d’une clé de cache de modèle :

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

Enfin, inscrivez votre nouveau `IModelCacheKeyFactory` dans votre contexte `OnConfiguring` :

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

Pour plus de contexte, consultez l' [exemple de projet complet](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/DynamicModel) .
