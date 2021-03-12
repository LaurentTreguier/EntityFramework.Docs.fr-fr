---
title: Bien démarrer - EF Core
description: Tutoriel de prise en main d’Entity Framework Core.
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/overview/first-app
ms.openlocfilehash: 560a6f293e32f4e1f75c13367291c4e31e83b157
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024340"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="f0747-103">Bien démarrer avec EF Core</span><span class="sxs-lookup"><span data-stu-id="f0747-103">Getting Started with EF Core</span></span>

<span data-ttu-id="f0747-104">Dans ce tutoriel, vous allez créer une application console .NET Core qui effectue un accès aux données d’une base de données SQLite à l’aide d’Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f0747-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="f0747-105">Vous pouvez suivre le tutoriel à l’aide de Visual Studio sur Windows, ou à l’aide de l’interface CLI .NET Core sur Windows, macOS ou Linux.</span><span class="sxs-lookup"><span data-stu-id="f0747-105">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="f0747-106">[Affichez l’exemple proposé dans cet article sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="f0747-106">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f0747-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f0747-107">Prerequisites</span></span>

<span data-ttu-id="f0747-108">Installez les logiciels suivants :</span><span class="sxs-lookup"><span data-stu-id="f0747-108">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f0747-109">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f0747-109">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="f0747-110">[SDK .NET Core](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="f0747-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="f0747-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0747-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f0747-112">[Visual Studio 2019 version 16.3 ou ultérieure](https://www.visualstudio.com/downloads/) avec cette charge de travail :</span><span class="sxs-lookup"><span data-stu-id="f0747-112">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="f0747-113">**Développement multiplateforme .NET Core** (sous **Autres ensembles d’outils**)</span><span class="sxs-lookup"><span data-stu-id="f0747-113">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="f0747-114">Créer un projet</span><span class="sxs-lookup"><span data-stu-id="f0747-114">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f0747-115">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f0747-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="f0747-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0747-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f0747-117">Ouvrir Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0747-117">Open Visual Studio</span></span>
* <span data-ttu-id="f0747-118">Cliquez sur **Créer un nouveau projet**.</span><span class="sxs-lookup"><span data-stu-id="f0747-118">Click **Create a new project**</span></span>
* <span data-ttu-id="f0747-119">Sélectionnez **Application console (.NET Core)** avec l’étiquette **C#** , puis cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f0747-119">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="f0747-120">Entrez le nom **EFGetStarted** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="f0747-120">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="f0747-121">Installer Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f0747-121">Install Entity Framework Core</span></span>

<span data-ttu-id="f0747-122">Pour installer EF Core, installez le package pour le ou les fournisseurs de bases de données EF Core à cibler.</span><span class="sxs-lookup"><span data-stu-id="f0747-122">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="f0747-123">Ce tutoriel utilise SQLite, car il s’exécute sur toutes les plateformes prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f0747-123">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="f0747-124">Pour obtenir la liste des fournisseurs disponibles, consultez [Fournisseurs de bases de données](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="f0747-124">For a list of available providers, see [Database Providers](xref:core/providers/index).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f0747-125">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f0747-125">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="f0747-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0747-126">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f0747-127">**Outils > Gestionnaire de package NuGet > Console du Gestionnaire de package**</span><span class="sxs-lookup"><span data-stu-id="f0747-127">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="f0747-128">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f0747-128">Run the following commands:</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="f0747-129">Conseil : Vous pouvez également installer des packages en cliquant avec le bouton droit sur le projet et en sélectionnant **Gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f0747-129">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="f0747-130">Créer le modèle</span><span class="sxs-lookup"><span data-stu-id="f0747-130">Create the model</span></span>

<span data-ttu-id="f0747-131">Définissez une classe de contexte et des classes d’entité qui composent le modèle.</span><span class="sxs-lookup"><span data-stu-id="f0747-131">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f0747-132">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f0747-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="f0747-133">Dans le répertoire du projet, créez **Model.cs** avec le code suivant.</span><span class="sxs-lookup"><span data-stu-id="f0747-133">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="f0747-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0747-134">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f0747-135">Cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter > Classe**.</span><span class="sxs-lookup"><span data-stu-id="f0747-135">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="f0747-136">Entrez le nom **Model.cs** et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f0747-136">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="f0747-137">Remplacez le contenu du fichier par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="f0747-137">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="f0747-138">EF Core peut également [rétroconcevoir](xref:core/managing-schemas/scaffolding) un modèle à partir d’une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="f0747-138">EF Core can also [reverse engineer](xref:core/managing-schemas/scaffolding) a model from an existing database.</span></span>

<span data-ttu-id="f0747-139">Conseil : Cette application garde intentionnellement les choses simples par souci de clarté.</span><span class="sxs-lookup"><span data-stu-id="f0747-139">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="f0747-140">Les [chaînes de connexion](xref:core/miscellaneous/connection-strings) ne doivent pas être stockées dans le code pour les applications de production.</span><span class="sxs-lookup"><span data-stu-id="f0747-140">[Connection strings](xref:core/miscellaneous/connection-strings) should not be stored in the code for production applications.</span></span> <span data-ttu-id="f0747-141">Vous pouvez également fractionner les différentes classes C# dans leur propre fichier.</span><span class="sxs-lookup"><span data-stu-id="f0747-141">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="f0747-142">Créer la base de données</span><span class="sxs-lookup"><span data-stu-id="f0747-142">Create the database</span></span>

<span data-ttu-id="f0747-143">Les étapes suivantes utilisent des [migrations](xref:core/managing-schemas/migrations/index) pour créer une base de données.</span><span class="sxs-lookup"><span data-stu-id="f0747-143">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f0747-144">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f0747-144">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="f0747-145">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f0747-145">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="f0747-146">Cette action installe [dotnet ef](xref:core/cli/dotnet) et le package de conception requis pour exécuter la commande sur un projet.</span><span class="sxs-lookup"><span data-stu-id="f0747-146">This installs [dotnet ef](xref:core/cli/dotnet) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="f0747-147">La commande `migrations` structure une migration afin de créer le jeu initial de tables du modèle.</span><span class="sxs-lookup"><span data-stu-id="f0747-147">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="f0747-148">La commande `database update` crée la base de données et lui applique la nouvelle migration.</span><span class="sxs-lookup"><span data-stu-id="f0747-148">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="f0747-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0747-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f0747-150">Exécutez les commandes suivantes dans la **console du Gestionnaire de package (PMC)** .</span><span class="sxs-lookup"><span data-stu-id="f0747-150">Run the following commands in **Package Manager Console (PMC)**</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="f0747-151">Cette action installe les [outils PMC pour EF Core](xref:core/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="f0747-151">This installs the [PMC tools for EF Core](xref:core/cli/powershell).</span></span> <span data-ttu-id="f0747-152">La commande `Add-Migration` structure une migration afin de créer le jeu initial de tables du modèle.</span><span class="sxs-lookup"><span data-stu-id="f0747-152">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="f0747-153">La commande `Update-Database` crée la base de données et lui applique la nouvelle migration.</span><span class="sxs-lookup"><span data-stu-id="f0747-153">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="f0747-154">Créer, lire, mettre à jour et supprimer</span><span class="sxs-lookup"><span data-stu-id="f0747-154">Create, read, update & delete</span></span>

* <span data-ttu-id="f0747-155">Ouvrez le fichier *Program.cs* et remplacez le contenu par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="f0747-155">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="f0747-156">Exécuter l'application</span><span class="sxs-lookup"><span data-stu-id="f0747-156">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f0747-157">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f0747-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="f0747-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0747-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f0747-159">**Déboguer > Démarrer sans débogage**</span><span class="sxs-lookup"><span data-stu-id="f0747-159">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="f0747-160">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="f0747-160">Next steps</span></span>

* <span data-ttu-id="f0747-161">Suivre le [tutoriel ASP.NET Core](/aspnet/core/data/ef-rp/intro) pour utiliser EF Core dans une application web</span><span class="sxs-lookup"><span data-stu-id="f0747-161">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="f0747-162">Découvrir les [expressions de requête LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="f0747-162">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="f0747-163">[Configurer votre modèle](xref:core/modeling/index) pour spécifier des éléments tels que la longueur [requise](xref:core/modeling/entity-properties#required-and-optional-properties) et la longueur [maximale](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="f0747-163">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="f0747-164">Utiliser des [migrations](xref:core/managing-schemas/migrations/index) pour mettre à jour le schéma de base de données après avoir changé votre modèle</span><span class="sxs-lookup"><span data-stu-id="f0747-164">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
