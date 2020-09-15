---
title: Alternance entre plusieurs modèles avec le même type DbContext-EF Core
description: Alternance entre plusieurs modèles ayant le même type DbContext à l’aide de Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/dynamic-model
ms.openlocfilehash: 0e0af67eab5262ab2b26edadea470c753b6349a0
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071522"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="cdfc1-103">Alternance entre plusieurs modèles ayant le même type DbContext</span><span class="sxs-lookup"><span data-stu-id="cdfc1-103">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="cdfc1-104">Le modèle intégré `OnModelCreating` peut utiliser une propriété du contexte pour modifier la façon dont le modèle est généré.</span><span class="sxs-lookup"><span data-stu-id="cdfc1-104">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="cdfc1-105">Par exemple, supposons que vous souhaitiez configurer une entité différemment en fonction de certaines propriétés :</span><span class="sxs-lookup"><span data-stu-id="cdfc1-105">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="cdfc1-106">Malheureusement, ce code ne fonctionnerait pas tel quel, car EF génère le modèle et `OnModelCreating` ne s’exécute qu’une seule fois, en mettant en cache le résultat pour des raisons de performances.</span><span class="sxs-lookup"><span data-stu-id="cdfc1-106">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="cdfc1-107">Toutefois, vous pouvez vous connecter au mécanisme de mise en cache du modèle pour que EF prenne en charge la propriété qui produit des modèles différents.</span><span class="sxs-lookup"><span data-stu-id="cdfc1-107">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="cdfc1-108">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="cdfc1-108">IModelCacheKeyFactory</span></span>

<span data-ttu-id="cdfc1-109">EF utilise `IModelCacheKeyFactory` pour générer des clés de cache pour les modèles ; par défaut, EF suppose que pour un type de contexte donné, le modèle sera le même, donc l’implémentation par défaut de ce service retourne une clé qui contient simplement le type de contexte.</span><span class="sxs-lookup"><span data-stu-id="cdfc1-109">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="cdfc1-110">Pour produire différents modèles à partir du même type de contexte, vous devez remplacer le `IModelCacheKeyFactory` service par l’implémentation correcte ; la clé générée sera comparée à d’autres clés de modèle à l’aide de la `Equals` méthode, en tenant compte de toutes les variables qui affectent le modèle.</span><span class="sxs-lookup"><span data-stu-id="cdfc1-110">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model.</span></span>

<span data-ttu-id="cdfc1-111">L’implémentation suivante prend le `UseIntProperty` en compte lors de la génération d’une clé de cache de modèle :</span><span class="sxs-lookup"><span data-stu-id="cdfc1-111">The following implementation takes the `UseIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="cdfc1-112">Enfin, inscrivez votre nouveau `IModelCacheKeyFactory` dans votre contexte `OnConfiguring` :</span><span class="sxs-lookup"><span data-stu-id="cdfc1-112">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="cdfc1-113">Pour plus de contexte, consultez l' [exemple de projet complet](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) .</span><span class="sxs-lookup"><span data-stu-id="cdfc1-113">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
