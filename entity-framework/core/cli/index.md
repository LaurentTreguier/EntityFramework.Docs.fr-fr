---
title: Informations de référence sur les outils Entity Framework Core - EF Core
description: Guide de référence de l’outil CLI Entity Framework Core et de la console du gestionnaire de package Visual Studio.
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 9a6ae8f945e92453ddfaa089ae1d606d142f725a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431576"
---
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="ef816-103">Informations de référence sur les outils Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="ef816-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="ef816-104">Les outils Entity Framework Core aident à effectuer les tâches de développement au moment du design.</span><span class="sxs-lookup"><span data-stu-id="ef816-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="ef816-105">Ils servent principalement à gérer les migrations et à structurer un `DbContext` et des types d’entité en reconstituant la logique du schéma d’une base de données.</span><span class="sxs-lookup"><span data-stu-id="ef816-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="ef816-106">Les [outils de la Console du Gestionnaire de package EF Core](xref:core/cli/powershell) s’exécutent dans la [Console du Gestionnaire de package](/nuget/tools/package-manager-console) dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ef816-106">The [EF Core Package Manager Console tools](xref:core/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="ef816-107">Les [outils de l’interface de ligne de commande (CLI) EF Core .NET](xref:core/cli/dotnet) sont une extension multiplateforme des [outils CLI .NET Core](/dotnet/core/tools/).</span><span class="sxs-lookup"><span data-stu-id="ef816-107">The [EF Core .NET command-line interface (CLI) tools](xref:core/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="ef816-108">Ces outils nécessitent un projet de SDK .NET Core (dont le fichier projet contient `Sdk="Microsoft.NET.Sdk"` ou une ligne similaire).</span><span class="sxs-lookup"><span data-stu-id="ef816-108">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="ef816-109">Les deux outils exposent les mêmes fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="ef816-109">Both tools expose the same functionality.</span></span> <span data-ttu-id="ef816-110">Si vous développez dans Visual Studio, nous vous recommandons d’utiliser les outils de la **Console du Gestionnaire de package** , car ils procurent une expérience plus intégrée.</span><span class="sxs-lookup"><span data-stu-id="ef816-110">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ef816-111">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="ef816-111">Next steps</span></span>

* [<span data-ttu-id="ef816-112">Informations de référence sur les outils de la Console du Gestionnaire de package EF Core</span><span class="sxs-lookup"><span data-stu-id="ef816-112">EF Core Package Manager Console tools reference</span></span>](xref:core/cli/powershell)
* [<span data-ttu-id="ef816-113">Informations de référence sur les outils CLI .NET EF Core</span><span class="sxs-lookup"><span data-stu-id="ef816-113">EF Core .NET CLI tools reference</span></span>](xref:core/cli/dotnet)
