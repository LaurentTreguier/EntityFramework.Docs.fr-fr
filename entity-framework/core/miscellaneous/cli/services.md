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
# <a name="design-time-services"></a>Services à la conception

Certains services utilisés par les outils sont utilisés uniquement au moment du Design. Ces services sont gérés séparément des services d’exécution de EF Core pour les empêcher d’être déployés avec votre application. Pour remplacer l’un de ces services (par exemple, le service pour générer des fichiers de migration), ajoutez une implémentation de `IDesignTimeServices` à votre projet de démarrage.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
