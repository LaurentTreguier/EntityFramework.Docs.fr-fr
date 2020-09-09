---
title: Services au moment du design-EF Core
description: Informations sur les services Entity Framework Core au moment de la conception
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: aa761c4a20e0848a77aa7b4ad625124a1d372a70
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617843"
---
# <a name="design-time-services"></a><span data-ttu-id="7a9a8-103">Services à la conception</span><span class="sxs-lookup"><span data-stu-id="7a9a8-103">Design-time services</span></span>

<span data-ttu-id="7a9a8-104">Certains services utilisés par les outils sont utilisés uniquement au moment du Design.</span><span class="sxs-lookup"><span data-stu-id="7a9a8-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="7a9a8-105">Ces services sont gérés séparément des services d’exécution de EF Core pour les empêcher d’être déployés avec votre application.</span><span class="sxs-lookup"><span data-stu-id="7a9a8-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="7a9a8-106">Pour remplacer l’un de ces services (par exemple, le service pour générer des fichiers de migration), ajoutez une implémentation de `IDesignTimeServices` à votre projet de démarrage.</span><span class="sxs-lookup"><span data-stu-id="7a9a8-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
