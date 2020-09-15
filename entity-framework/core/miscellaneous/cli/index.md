---
title: Informations de référence sur les outils Entity Framework Core - EF Core
description: Guide de référence de l’outil CLI Entity Framework Core et de la console du gestionnaire de package Visual Studio.
author: bricelam
ms.author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 7d5fb984aafed2cf45efa9e5b83bf4bc6c18a44a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619422"
---
# <a name="entity-framework-core-tools-reference"></a>Informations de référence sur les outils Entity Framework Core

Les outils Entity Framework Core aident à effectuer les tâches de développement au moment du design. Ils servent principalement à gérer les migrations et à structurer un `DbContext` et des types d’entité en reconstituant la logique du schéma d’une base de données.

* Les [outils de la Console du Gestionnaire de package EF Core](xref:core/miscellaneous/cli/powershell) s’exécutent dans la [Console du Gestionnaire de package](/nuget/tools/package-manager-console) dans Visual Studio.

* Les [outils de l’interface de ligne de commande (CLI) EF Core .NET](xref:core/miscellaneous/cli/dotnet) sont une extension multiplateforme des [outils CLI .NET Core](/dotnet/core/tools/). Ces outils nécessitent un projet de SDK .NET Core (dont le fichier projet contient `Sdk="Microsoft.NET.Sdk"` ou une ligne similaire).

Les deux outils exposent les mêmes fonctionnalités. Si vous développez dans Visual Studio, nous vous recommandons d’utiliser les outils de la **Console du Gestionnaire de package**, car ils procurent une expérience plus intégrée.

## <a name="next-steps"></a>Étapes suivantes

* [Informations de référence sur les outils de la Console du Gestionnaire de package EF Core](xref:core/miscellaneous/cli/powershell)
* [Informations de référence sur les outils CLI .NET EF Core](xref:core/miscellaneous/cli/dotnet)
