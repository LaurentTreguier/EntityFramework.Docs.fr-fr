---
title: Informations de référence sur les outils Entity Framework Core - EF Core
description: Guide de référence de l’outil CLI Entity Framework Core et de la console du gestionnaire de package Visual Studio.
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 1ffc773cb8ed30516d682b90bbd9accef634ae6a
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635378"
---
# <a name="entity-framework-core-tools-reference"></a>Informations de référence sur les outils Entity Framework Core

Les outils Entity Framework Core aident à effectuer les tâches de développement au moment du design. Ils servent principalement à gérer les migrations et à structurer un `DbContext` et des types d’entité en reconstituant la logique du schéma d’une base de données.

L’un des outils suivants peut être installé, car les deux outils exposent les mêmes fonctionnalités :

* Les [outils de la Console du Gestionnaire de package EF Core](xref:core/cli/powershell) s’exécutent dans la [Console du Gestionnaire de package](/nuget/tools/package-manager-console) dans Visual Studio. Nous vous recommandons d’utiliser ces outils si vous développez dans Visual Studio, car ils offrent une expérience plus intégrée.

* Les [outils de l’interface de ligne de commande (CLI) EF Core .NET](xref:core/cli/dotnet) sont une extension multiplateforme des [outils CLI .NET Core](/dotnet/core/tools/). Ces outils nécessitent un projet de SDK .NET Core (dont le fichier projet contient `Sdk="Microsoft.NET.Sdk"` ou une ligne similaire).

## <a name="next-steps"></a>Étapes suivantes

* [Informations de référence sur les outils de la Console du Gestionnaire de package EF Core](xref:core/cli/powershell)
* [Informations de référence sur les outils CLI .NET EF Core](xref:core/cli/dotnet)
