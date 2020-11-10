---
title: Installation d’Entity Framework Core - EF Core
description: Instructions d’installation d’Entity Framework Core.
author: bricelam
ms.date: 08/06/2017
uid: core/get-started/overview/install
ms.openlocfilehash: 039e5339a6dba57f6a8f33acba6e467f8d6439c9
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431557"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="87b01-103">Installation d’Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="87b01-103">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="87b01-104">Prérequis</span><span class="sxs-lookup"><span data-stu-id="87b01-104">Prerequisites</span></span>

* <span data-ttu-id="87b01-105">EF Core est une bibliothèque [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="87b01-105">EF Core is a [.NET Standard 2.0](/dotnet/standard/net-standard) library.</span></span> <span data-ttu-id="87b01-106">Par conséquent, l’exécution d’EF Core nécessite une implémentation .NET prenant en charge .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="87b01-106">So EF Core requires a .NET implementation that supports .NET Standard 2.0 to run.</span></span> <span data-ttu-id="87b01-107">EF Core peut aussi être référencé par d’autres bibliothèques .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="87b01-107">EF Core can also be referenced by other .NET Standard 2.0 libraries.</span></span>

* <span data-ttu-id="87b01-108">Par exemple, vous pouvez utiliser EF Core pour développer des applications qui ciblent .NET Core.</span><span class="sxs-lookup"><span data-stu-id="87b01-108">For example, you can use EF Core to develop apps that target .NET Core.</span></span> <span data-ttu-id="87b01-109">La création d’applications .NET Core nécessite le [SDK .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="87b01-109">Building .NET Core apps requires the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="87b01-110">Vous pouvez aussi utiliser un environnement de développement comme [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac) ou [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="87b01-110">Optionally, you can also use a development environment like [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac), or [Visual Studio Code](https://code.visualstudio.com).</span></span> <span data-ttu-id="87b01-111">Pour plus d’informations, consultez [Bien démarrer avec .NET Core](/dotnet/core/get-started).</span><span class="sxs-lookup"><span data-stu-id="87b01-111">For more information, check [Getting Started with .NET Core](/dotnet/core/get-started).</span></span>

* <span data-ttu-id="87b01-112">Vous pouvez utiliser EF Core pour développer des applications sur Windows à l’aide de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="87b01-112">You can use EF Core to develop applications on Windows using Visual Studio.</span></span> <span data-ttu-id="87b01-113">La dernière version de [Visual Studio](https://visualstudio.microsoft.com/vs) est recommandée.</span><span class="sxs-lookup"><span data-stu-id="87b01-113">The latest version of [Visual Studio](https://visualstudio.microsoft.com/vs) is recommended.</span></span>

* <span data-ttu-id="87b01-114">EF Core peut s’exécuter sur d’autres implémentations .NET comme [Xamarin](https://dotnet.microsoft.com/apps/xamarin) et .NET Native.</span><span class="sxs-lookup"><span data-stu-id="87b01-114">EF Core can run on other .NET implementations like [Xamarin](https://dotnet.microsoft.com/apps/xamarin) and .NET Native.</span></span> <span data-ttu-id="87b01-115">Toutefois, dans la pratique, ces implémentations ont des limitations de runtime qui peuvent affecter le fonctionnement d’EF Core sur votre application.</span><span class="sxs-lookup"><span data-stu-id="87b01-115">But in practice those implementations have runtime limitations that may affect how well EF Core works on your app.</span></span> <span data-ttu-id="87b01-116">Pour plus d’informations, consultez [Implémentations .NET prises en charge par EF Core](xref:core/miscellaneous/platforms).</span><span class="sxs-lookup"><span data-stu-id="87b01-116">For more information, see [.NET implementations supported by EF Core](xref:core/miscellaneous/platforms).</span></span>

* <span data-ttu-id="87b01-117">Enfin, différents fournisseurs de base de données peuvent nécessiter des versions de moteur de base de données, des implémentations .NET ou des systèmes d’exploitation spécifiques.</span><span class="sxs-lookup"><span data-stu-id="87b01-117">Finally, different database providers may require specific database engine versions, .NET implementations, or operating systems.</span></span> <span data-ttu-id="87b01-118">Vérifiez qu’un [fournisseur de base de données EF Core](xref:core/providers/index) qui prend en charge l’environnement approprié pour votre application est disponible.</span><span class="sxs-lookup"><span data-stu-id="87b01-118">Make sure an [EF Core database provider](xref:core/providers/index) is available that supports the right environment for your application.</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="87b01-119">Obtenir le runtime Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="87b01-119">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="87b01-120">Pour ajouter EF Core à une application, installez le package NuGet du fournisseur de base de données à utiliser.</span><span class="sxs-lookup"><span data-stu-id="87b01-120">To add EF Core to an application, install the NuGet package for the database provider you want to use.</span></span>

<span data-ttu-id="87b01-121">Si vous créez une application ASP.NET Core, vous n’avez pas besoin d’installer les fournisseurs en mémoire et SQL Server.</span><span class="sxs-lookup"><span data-stu-id="87b01-121">If you're building an ASP.NET Core application, you don't need to install the in-memory and SQL Server providers.</span></span> <span data-ttu-id="87b01-122">Ces fournisseurs sont inclus dans les versions actuelles d’ASP.NET Core, en même temps que le runtime EF Core.</span><span class="sxs-lookup"><span data-stu-id="87b01-122">Those providers are included in current versions of ASP.NET Core, alongside the EF Core runtime.</span></span>

<span data-ttu-id="87b01-123">Pour installer ou mettre à jour les packages NuGet, vous pouvez utiliser l’interface de ligne de commande (CLI) .NET Core, la boîte de dialogue Gestionnaire de package Visual Studio ou la console du Gestionnaire de package Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="87b01-123">To install or update NuGet packages, you can use the .NET Core command-line interface (CLI), the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

### <a name="net-core-cli"></a><span data-ttu-id="87b01-124">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="87b01-124">.NET Core CLI</span></span>

* <span data-ttu-id="87b01-125">Utilisez la commande CLI .NET Core suivante à partir de la ligne de commande du système d’exploitation pour installer ou mettre à jour le fournisseur EF Core SQL Server :</span><span class="sxs-lookup"><span data-stu-id="87b01-125">Use the following .NET Core CLI command from the operating system's command line to install or update the EF Core SQL Server provider:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="87b01-126">Vous pouvez indiquer une version spécifique dans la commande `dotnet add package`, à l’aide du modificateur `-v`.</span><span class="sxs-lookup"><span data-stu-id="87b01-126">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="87b01-127">Par exemple, pour installer des packages EF Core 2.2.0, ajoutez `-v 2.2.0` à la commande.</span><span class="sxs-lookup"><span data-stu-id="87b01-127">For example, to install EF Core 2.2.0 packages, append `-v 2.2.0` to the command.</span></span>

<span data-ttu-id="87b01-128">Pour plus d’informations, consultez [Outils de l’interface de ligne de commande (CLI) .NET](/dotnet/core/tools/).</span><span class="sxs-lookup"><span data-stu-id="87b01-128">For more information, see [.NET command-line interface (CLI) tools](/dotnet/core/tools/).</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="87b01-129">Boîte de dialogue Gestionnaire de package NuGet Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87b01-129">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="87b01-130">Dans le menu Visual Studio, sélectionnez **Projet > Gérer les packages NuGet**</span><span class="sxs-lookup"><span data-stu-id="87b01-130">From the Visual Studio menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="87b01-131">Cliquez sur l’onglet **Parcourir** ou **Mises à jour**.</span><span class="sxs-lookup"><span data-stu-id="87b01-131">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="87b01-132">Pour installer ou mettre à jour le fournisseur SQL Server, sélectionnez le package `Microsoft.EntityFrameworkCore.SqlServer` et confirmez.</span><span class="sxs-lookup"><span data-stu-id="87b01-132">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="87b01-133">Pour plus d’informations, consultez [Boîte de dialogue Gestionnaire de package NuGet](/nuget/tools/package-manager-ui).</span><span class="sxs-lookup"><span data-stu-id="87b01-133">For more information, see [NuGet Package Manager Dialog](/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="87b01-134">Console du Gestionnaire de package NuGet Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87b01-134">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="87b01-135">Dans le menu Visual Studio, sélectionnez **Outils > Gestionnaire de package NuGet > Console du Gestionnaire de package**</span><span class="sxs-lookup"><span data-stu-id="87b01-135">From the Visual Studio menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="87b01-136">Pour installer le fournisseur SQL Server, exécutez la commande suivante dans la Console du Gestionnaire de package :</span><span class="sxs-lookup"><span data-stu-id="87b01-136">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="87b01-137">Pour mettre à jour le fournisseur, utilisez la commande `Update-Package`.</span><span class="sxs-lookup"><span data-stu-id="87b01-137">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="87b01-138">Pour indiquer une version spécifique, utilisez le modificateur `-Version`.</span><span class="sxs-lookup"><span data-stu-id="87b01-138">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="87b01-139">Par exemple, pour installer les packages EF Core 2.2.0, ajoutez `-Version 2.2.0` aux commandes</span><span class="sxs-lookup"><span data-stu-id="87b01-139">For example, to install EF Core 2.2.0 packages, append `-Version 2.2.0` to the commands</span></span>

<span data-ttu-id="87b01-140">Pour plus d’informations, consultez [Console Gestionnaire de package](/nuget/tools/package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="87b01-140">For more information, see [Package Manager Console](/nuget/tools/package-manager-console).</span></span>

## <a name="get-the-entity-framework-core-tools"></a><span data-ttu-id="87b01-141">Obtenir les outils Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="87b01-141">Get the Entity Framework Core tools</span></span>

<span data-ttu-id="87b01-142">Vous pouvez installer des outils pour effectuer des tâches liées à EF Core dans votre projet, comme créer et appliquer des migrations de base de données, ou créer un modèle EF Core basé sur une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="87b01-142">You can install tools to carry out EF Core-related tasks in your project, like creating and applying database migrations, or creating an EF Core model based on an existing database.</span></span>

<span data-ttu-id="87b01-143">Deux ensembles d’outils sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="87b01-143">Two sets of tools are available:</span></span>

* <span data-ttu-id="87b01-144">Les [outils de l’interface de ligne de commande (CLI) .NET Core](xref:core/cli/dotnet) peuvent être utilisés sur Windows, Linux ou macOS.</span><span class="sxs-lookup"><span data-stu-id="87b01-144">The [.NET Core command-line interface (CLI) tools](xref:core/cli/dotnet) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="87b01-145">Ces commandes commencent par `dotnet ef`.</span><span class="sxs-lookup"><span data-stu-id="87b01-145">These commands begin with `dotnet ef`.</span></span>

* <span data-ttu-id="87b01-146">Les [outils de la console du Gestionnaire de package](xref:core/cli/powershell) s’exécutent dans Visual Studio sur Windows.</span><span class="sxs-lookup"><span data-stu-id="87b01-146">The [Package Manager Console (PMC) tools](xref:core/cli/powershell) run in Visual Studio on Windows.</span></span> <span data-ttu-id="87b01-147">Ces commandes commencent par un verbe, par exemple `Add-Migration`, `Update-Database`.</span><span class="sxs-lookup"><span data-stu-id="87b01-147">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="87b01-148">Même si vous pouvez utiliser les commandes `dotnet ef` à partir de la console du Gestionnaire de package, nous vous recommandons d’utiliser les outils de la console du Gestionnaire de package avec Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="87b01-148">Although you can also use the `dotnet ef` commands from the Package Manager Console, it's recommended to use the Package Manager Console tools when you're using Visual Studio:</span></span>

* <span data-ttu-id="87b01-149">Ils fonctionnent automatiquement avec le projet sélectionné dans la console du Gestionnaire de package dans Visual Studio, sans que vous ayez besoin de changer manuellement les répertoires.</span><span class="sxs-lookup"><span data-stu-id="87b01-149">They automatically work with the current project selected in the PMC in Visual Studio, without requiring manually switching directories.</span></span>

* <span data-ttu-id="87b01-150">Elles ouvrent automatiquement les fichiers générés par les commandes de Visual Studio après avoir été exécutées.</span><span class="sxs-lookup"><span data-stu-id="87b01-150">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a><span data-ttu-id="87b01-151">Obtenir les outils CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="87b01-151">Get the .NET Core CLI tools</span></span>

<span data-ttu-id="87b01-152">Les outils CLI .NET Core nécessitent le SDK .NET Core, mentionné précédemment dans les [Prérequis](#prerequisites).</span><span class="sxs-lookup"><span data-stu-id="87b01-152">.NET Core CLI tools require the .NET Core SDK, mentioned earlier in [Prerequisites](#prerequisites).</span></span>

* <span data-ttu-id="87b01-153">`dotnet ef` doit être installé en tant qu’outil Global ou local.</span><span class="sxs-lookup"><span data-stu-id="87b01-153">`dotnet ef` must be installed as a global or local tool.</span></span> <span data-ttu-id="87b01-154">La plupart des développeurs préfèrent installer `dotnet ef` en tant qu’outil Global à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="87b01-154">Most developers prefer installing `dotnet ef` as a global tool using the following command:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  <span data-ttu-id="87b01-155">`dotnet ef` peut également être utilisé en tant qu’outil local.</span><span class="sxs-lookup"><span data-stu-id="87b01-155">`dotnet ef` can also be used as a local tool.</span></span> <span data-ttu-id="87b01-156">Pour l’utiliser en tant qu’outil local, restaurez les dépendances d’un projet qui le déclare en tant que dépendance de l’outil à l’aide d’un [fichier manifeste d’outil](/dotnet/core/tools/global-tools#install-a-local-tool).</span><span class="sxs-lookup"><span data-stu-id="87b01-156">To use it as a local tool, restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

* <span data-ttu-id="87b01-157">Pour mettre à jour les outils, utilisez la `dotnet tool update` commande.</span><span class="sxs-lookup"><span data-stu-id="87b01-157">To update the tools, use the `dotnet tool update` command.</span></span>

* <span data-ttu-id="87b01-158">Installez le package le plus récent `Microsoft.EntityFrameworkCore.Design` .</span><span class="sxs-lookup"><span data-stu-id="87b01-158">Install the latest `Microsoft.EntityFrameworkCore.Design` package.</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

> [!IMPORTANT]
> <span data-ttu-id="87b01-159">Utilisez toujours la version des packages d’outils qui correspond à la version principale des packages du runtime.</span><span class="sxs-lookup"><span data-stu-id="87b01-159">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="87b01-160">Obtenir les outils de la console du Gestionnaire de package</span><span class="sxs-lookup"><span data-stu-id="87b01-160">Get the Package Manager Console tools</span></span>

<span data-ttu-id="87b01-161">Pour obtenir les outils de la console du Gestionnaire de package pour EF Core, installez le package `Microsoft.EntityFrameworkCore.Tools`.</span><span class="sxs-lookup"><span data-stu-id="87b01-161">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span> <span data-ttu-id="87b01-162">Par exemple, à partir de Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="87b01-162">For example, from Visual Studio:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="87b01-163">Pour les applications ASP.NET Core, ce package est inclus automatiquement.</span><span class="sxs-lookup"><span data-stu-id="87b01-163">For ASP.NET Core apps, this package is included automatically.</span></span>

## <a name="upgrading-to-the-latest-ef-core"></a><span data-ttu-id="87b01-164">Mise à niveau vers la dernière version EF Core</span><span class="sxs-lookup"><span data-stu-id="87b01-164">Upgrading to the latest EF Core</span></span>

* <span data-ttu-id="87b01-165">Chaque fois que nous publions une nouvelle version d’EF Core, nous publions aussi une nouvelle version des fournisseurs qui font partie du projet EF Core, comme Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite et Microsoft.EntityFrameworkCore.InMemory.</span><span class="sxs-lookup"><span data-stu-id="87b01-165">Any time we release a new version of EF Core, we also release a new version of the providers that are part of the EF Core project, like Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite, and Microsoft.EntityFrameworkCore.InMemory.</span></span> <span data-ttu-id="87b01-166">Vous pouvez simplement passer à la nouvelle version du fournisseur pour obtenir toutes les améliorations.</span><span class="sxs-lookup"><span data-stu-id="87b01-166">You can just upgrade to the new version of the provider to get all the improvements.</span></span>

* <span data-ttu-id="87b01-167">EF Core ainsi que les fournisseurs en mémoire et SQL Server sont inclus dans les versions actuelles d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="87b01-167">EF Core, together with the SQL Server and the in-memory providers are included in current versions of ASP.NET Core.</span></span> <span data-ttu-id="87b01-168">Pour mettre à niveau une application ASP.NET Core existante vers une version plus récente d’EF Core, mettez toujours à niveau la version d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="87b01-168">To upgrade an existing ASP.NET Core application to a newer version of EF Core, always upgrade the version of ASP.NET Core.</span></span>

* <span data-ttu-id="87b01-169">Si vous devez mettre à jour une application qui utilise un fournisseur de base de données tiers, recherchez toujours une mise à jour du fournisseur qui est compatible avec la version d’EF Core à utiliser.</span><span class="sxs-lookup"><span data-stu-id="87b01-169">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="87b01-170">Par exemple, les fournisseurs de bases de données pour la version 1,0 ne sont pas compatibles avec la version 2,0 du runtime EF Core.</span><span class="sxs-lookup"><span data-stu-id="87b01-170">For example, database providers for version 1.0 are not compatible with version 2.0 of the EF Core runtime.</span></span>

* <span data-ttu-id="87b01-171">Les fournisseurs tiers d’EF Core ne publient généralement pas de versions correctives en même temps que le runtime EF Core.</span><span class="sxs-lookup"><span data-stu-id="87b01-171">Third-party providers for EF Core usually don't release patch versions alongside the EF Core runtime.</span></span> <span data-ttu-id="87b01-172">Pour mettre à niveau une application qui utilise un fournisseur tiers vers une version corrective d’EF Core, vous devez peut-être ajouter une référence directe à des composants d’exécution EF Core individuels, comme Microsoft.EntityFrameworkCore et Microsoft.EntityFrameworkCore.Relational.</span><span class="sxs-lookup"><span data-stu-id="87b01-172">To upgrade an application that uses a third-party provider to a patch version of EF Core, you may need to add a direct reference to individual EF Core runtime components, such as Microsoft.EntityFrameworkCore, and Microsoft.EntityFrameworkCore.Relational.</span></span>
