---
title: Informations de référence sur les outils Entity Framework Core - EF Core
description: Guide de référence de l’outil CLI Entity Framework Core et de la console du gestionnaire de package Visual Studio.
author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 90e8d799de8fa9426de6ac3d286bcd5458a43f0d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061994"
---
# <a name="entity-framework-core-tools-reference"></a>Informations de référence sur les outils Entity Framework Core

Les outils Entity Framework Core aident à effectuer les tâches de développement au moment du design. Ils servent principalement à gérer les migrations et à structurer un `DbContext` et des types d’entité en reconstituant la logique du schéma d’une base de données.

* Les [outils de la Console du Gestionnaire de package EF Core](xref:core/miscellaneous/cli/powershell) s’exécutent dans la [Console du Gestionnaire de package](/nuget/tools/package-manager-console) dans Visual Studio.

* Les [outils de l’interface de ligne de commande (CLI) EF Core .NET](xref:core/miscellaneous/cli/dotnet) sont une extension multiplateforme des [outils CLI .NET Core](/dotnet/core/tools/). Ces outils nécessitent un projet de SDK .NET Core (dont le fichier projet contient `Sdk="Microsoft.NET.Sdk"` ou une ligne similaire).

Les deux outils exposent les mêmes fonctionnalités. Si vous développez dans Visual Studio, nous vous recommandons d’utiliser les outils de la **Console du Gestionnaire de package**, car ils procurent une expérience plus intégrée.

## <a name="next-steps"></a>Étapes suivantes

* [Informations de référence sur les outils de la Console du Gestionnaire de package EF Core](xref:core/miscellaneous/cli/powershell)
* [Informations de référence sur les outils CLI .NET EF Core](xref:core/miscellaneous/cli/dotnet)
