---
title: Services au moment du design-EF Core
description: Informations sur les services Entity Framework Core au moment de la conception
author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 6778a1fba025e9fbce1bb3e43462b0ee7c5dcfc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061942"
---
# <a name="design-time-services"></a><span data-ttu-id="2aa54-103">Services à la conception</span><span class="sxs-lookup"><span data-stu-id="2aa54-103">Design-time services</span></span>

<span data-ttu-id="2aa54-104">Certains services utilisés par les outils sont utilisés uniquement au moment du Design.</span><span class="sxs-lookup"><span data-stu-id="2aa54-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="2aa54-105">Ces services sont gérés séparément des services d’exécution de EF Core pour les empêcher d’être déployés avec votre application.</span><span class="sxs-lookup"><span data-stu-id="2aa54-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="2aa54-106">Pour remplacer l’un de ces services (par exemple, le service pour générer des fichiers de migration), ajoutez une implémentation de `IDesignTimeServices` à votre projet de démarrage.</span><span class="sxs-lookup"><span data-stu-id="2aa54-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
