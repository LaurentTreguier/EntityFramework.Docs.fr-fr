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
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="5cdf2-103">Informations de référence sur les outils Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5cdf2-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="5cdf2-104">Les outils Entity Framework Core aident à effectuer les tâches de développement au moment du design.</span><span class="sxs-lookup"><span data-stu-id="5cdf2-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="5cdf2-105">Ils servent principalement à gérer les migrations et à structurer un `DbContext` et des types d’entité en reconstituant la logique du schéma d’une base de données.</span><span class="sxs-lookup"><span data-stu-id="5cdf2-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

<span data-ttu-id="5cdf2-106">L’un des outils suivants peut être installé, car les deux outils exposent les mêmes fonctionnalités :</span><span class="sxs-lookup"><span data-stu-id="5cdf2-106">Either of the following tools can be installed, as both tools expose the same functionality:</span></span>

* <span data-ttu-id="5cdf2-107">Les [outils de la Console du Gestionnaire de package EF Core](xref:core/cli/powershell) s’exécutent dans la [Console du Gestionnaire de package](/nuget/tools/package-manager-console) dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5cdf2-107">The [EF Core Package Manager Console tools](xref:core/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span> <span data-ttu-id="5cdf2-108">Nous vous recommandons d’utiliser ces outils si vous développez dans Visual Studio, car ils offrent une expérience plus intégrée.</span><span class="sxs-lookup"><span data-stu-id="5cdf2-108">We recommend using these tools if you are developing in Visual Studio as they provide a more integrated experience.</span></span>

* <span data-ttu-id="5cdf2-109">Les [outils de l’interface de ligne de commande (CLI) EF Core .NET](xref:core/cli/dotnet) sont une extension multiplateforme des [outils CLI .NET Core](/dotnet/core/tools/).</span><span class="sxs-lookup"><span data-stu-id="5cdf2-109">The [EF Core .NET command-line interface (CLI) tools](xref:core/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="5cdf2-110">Ces outils nécessitent un projet de SDK .NET Core (dont le fichier projet contient `Sdk="Microsoft.NET.Sdk"` ou une ligne similaire).</span><span class="sxs-lookup"><span data-stu-id="5cdf2-110">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

## <a name="next-steps"></a><span data-ttu-id="5cdf2-111">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="5cdf2-111">Next steps</span></span>

* [<span data-ttu-id="5cdf2-112">Informations de référence sur les outils de la Console du Gestionnaire de package EF Core</span><span class="sxs-lookup"><span data-stu-id="5cdf2-112">EF Core Package Manager Console tools reference</span></span>](xref:core/cli/powershell)
* [<span data-ttu-id="5cdf2-113">Informations de référence sur les outils CLI .NET EF Core</span><span class="sxs-lookup"><span data-stu-id="5cdf2-113">EF Core .NET CLI tools reference</span></span>](xref:core/cli/dotnet)
