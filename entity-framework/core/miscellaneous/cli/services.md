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
# <a name="design-time-services"></a>Services à la conception

Certains services utilisés par les outils sont utilisés uniquement au moment du Design. Ces services sont gérés séparément des services d’exécution de EF Core pour les empêcher d’être déployés avec votre application. Pour remplacer l’un de ces services (par exemple, le service pour générer des fichiers de migration), ajoutez une implémentation de `IDesignTimeServices` à votre projet de démarrage.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
