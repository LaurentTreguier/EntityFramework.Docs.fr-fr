---
title: Bien démarrer - EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: d46c4bb9ac6c8f718b4da5ecd82d54710d41935f
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824489"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="fed73-102">Bien démarrer avec EF Core</span><span class="sxs-lookup"><span data-stu-id="fed73-102">Getting Started with EF Core</span></span>

<span data-ttu-id="fed73-103">Dans ce tutoriel, vous allez créer une application console .NET Core qui effectue un accès aux données d’une base de données SQLite à l’aide d’Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="fed73-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="fed73-104">Vous pouvez suivre le tutoriel à l’aide de Visual Studio sur Windows, ou à l’aide de l’interface CLI .NET Core sur Windows, macOS ou Linux.</span><span class="sxs-lookup"><span data-stu-id="fed73-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="fed73-105">[Affichez l’exemple proposé dans cet article sur GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="fed73-105">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fed73-106">Prérequis</span><span class="sxs-lookup"><span data-stu-id="fed73-106">Prerequisites</span></span>

<span data-ttu-id="fed73-107">Installez les logiciels suivants :</span><span class="sxs-lookup"><span data-stu-id="fed73-107">Install the following software:</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="fed73-108">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="fed73-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="fed73-109">[SDK .NET Core 3.0](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="fed73-109">[.NET Core 3.0 SDK](https://www.microsoft.com/net/download/core).</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fed73-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fed73-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fed73-111">[Visual Studio 2019 version 16.3 ou ultérieure](https://www.visualstudio.com/downloads/) avec cette charge de travail :</span><span class="sxs-lookup"><span data-stu-id="fed73-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="fed73-112">**Développement multiplateforme .NET Core** (sous **Autres ensembles d’outils**)</span><span class="sxs-lookup"><span data-stu-id="fed73-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="fed73-113">Créer un projet</span><span class="sxs-lookup"><span data-stu-id="fed73-113">Create a new project</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="fed73-114">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="fed73-114">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fed73-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fed73-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fed73-116">Ouvrir Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fed73-116">Open Visual Studio</span></span>
* <span data-ttu-id="fed73-117">Cliquez sur **Créer un nouveau projet**.</span><span class="sxs-lookup"><span data-stu-id="fed73-117">Click **Create a new project**</span></span>
* <span data-ttu-id="fed73-118">Sélectionnez **Application console (.NET Core)** avec l’étiquette **C#** , puis cliquez sur **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="fed73-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="fed73-119">Entrez le nom **EFGetStarted** et cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="fed73-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="fed73-120">Installer Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="fed73-120">Install Entity Framework Core</span></span>

<span data-ttu-id="fed73-121">Pour installer EF Core, installez le package pour le ou les fournisseurs de bases de données EF Core à cibler.</span><span class="sxs-lookup"><span data-stu-id="fed73-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="fed73-122">Ce tutoriel utilise SQLite, car il s’exécute sur toutes les plateformes prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fed73-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="fed73-123">Pour obtenir la liste des fournisseurs disponibles, consultez [Fournisseurs de bases de données](../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="fed73-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="fed73-124">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="fed73-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fed73-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fed73-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fed73-126">**Outils > Gestionnaire de package NuGet > Console du Gestionnaire de package**</span><span class="sxs-lookup"><span data-stu-id="fed73-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="fed73-127">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fed73-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="fed73-128">Conseil : Vous pouvez également installer des packages en cliquant avec le bouton droit sur le projet et en sélectionnant **Gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="fed73-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="fed73-129">Créer le modèle</span><span class="sxs-lookup"><span data-stu-id="fed73-129">Create the model</span></span>

<span data-ttu-id="fed73-130">Définissez une classe de contexte et des classes d’entité qui composent le modèle.</span><span class="sxs-lookup"><span data-stu-id="fed73-130">Define a context class and entity classes that make up the model.</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="fed73-131">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="fed73-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="fed73-132">Dans le répertoire du projet, créez **Model.cs** avec le code suivant.</span><span class="sxs-lookup"><span data-stu-id="fed73-132">In the project directory, create **Model.cs** with the following code</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fed73-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fed73-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fed73-134">Cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter > Classe**.</span><span class="sxs-lookup"><span data-stu-id="fed73-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="fed73-135">Entrez le nom **Model.cs** et cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="fed73-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="fed73-136">Remplacez le contenu du fichier par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="fed73-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="fed73-137">EF Core peut également [rétroconcevoir](../managing-schemas/scaffolding.md) un modèle à partir d’une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="fed73-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="fed73-138">Conseil : Dans une application réelle, vous placez chaque classe dans un fichier distinct et la [chaîne de connexion](../miscellaneous/connection-strings.md) dans un fichier de configuration ou une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="fed73-138">Tip: In a real app, you put each class in a separate file and put the [connection string](../miscellaneous/connection-strings.md) in a configuration file or environment variable.</span></span> <span data-ttu-id="fed73-139">Pour simplifier le tutoriel, tout est placé dans un seul et même fichier.</span><span class="sxs-lookup"><span data-stu-id="fed73-139">To keep the tutorial simple, everything is contained in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="fed73-140">Créer la base de données</span><span class="sxs-lookup"><span data-stu-id="fed73-140">Create the database</span></span>

<span data-ttu-id="fed73-141">Les étapes suivantes utilisent des [migrations](xref:core/managing-schemas/migrations/index) pour créer une base de données.</span><span class="sxs-lookup"><span data-stu-id="fed73-141">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="fed73-142">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="fed73-142">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="fed73-143">Exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fed73-143">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="fed73-144">Cette action installe [dotnet ef](../miscellaneous/cli/dotnet.md) et le package de conception requis pour exécuter la commande sur un projet.</span><span class="sxs-lookup"><span data-stu-id="fed73-144">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="fed73-145">La commande `migrations` structure une migration afin de créer le jeu initial de tables du modèle.</span><span class="sxs-lookup"><span data-stu-id="fed73-145">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="fed73-146">La commande `database update` crée la base de données et lui applique la nouvelle migration.</span><span class="sxs-lookup"><span data-stu-id="fed73-146">The `database update` command creates the database and applies the new migration to it.</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fed73-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fed73-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fed73-148">Exécutez les commandes suivantes dans la **Console du gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="fed73-148">Run the following commands in **Package Manager Console**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="fed73-149">Cette action installe les [outils PMC pour EF Core](../miscellaneous/cli/powershell.md).</span><span class="sxs-lookup"><span data-stu-id="fed73-149">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="fed73-150">La commande `Add-Migration` structure une migration afin de créer le jeu initial de tables du modèle.</span><span class="sxs-lookup"><span data-stu-id="fed73-150">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="fed73-151">La commande `Update-Database` crée la base de données et lui applique la nouvelle migration.</span><span class="sxs-lookup"><span data-stu-id="fed73-151">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="fed73-152">Créer, lire, mettre à jour et supprimer</span><span class="sxs-lookup"><span data-stu-id="fed73-152">Create, read, update & delete</span></span>

* <span data-ttu-id="fed73-153">Ouvrez le fichier *Program.cs* et remplacez le contenu par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="fed73-153">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="fed73-154">Exécuter l'application</span><span class="sxs-lookup"><span data-stu-id="fed73-154">Run the app</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="fed73-155">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="fed73-155">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fed73-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fed73-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fed73-157">Visual Studio utilise un répertoire de travail incohérent lors de l’exécution d’applications console .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fed73-157">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="fed73-158">(voir [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)). Cela entraîne la levée d’une exception : *aucune table correspondante : Blogs*.</span><span class="sxs-lookup"><span data-stu-id="fed73-158">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="fed73-159">Pour mettre à jour le répertoire de travail :</span><span class="sxs-lookup"><span data-stu-id="fed73-159">To update the working directory:</span></span>

* <span data-ttu-id="fed73-160">Cliquez avec le bouton droit sur le projet et sélectionnez **Modifier le fichier de projet**.</span><span class="sxs-lookup"><span data-stu-id="fed73-160">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="fed73-161">Juste en dessous de la propriété *TargetFramework*, ajoutez ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="fed73-161">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="fed73-162">Enregistrez le fichier.</span><span class="sxs-lookup"><span data-stu-id="fed73-162">Save the file</span></span>

<span data-ttu-id="fed73-163">Vous pouvez maintenant exécuter l’application :</span><span class="sxs-lookup"><span data-stu-id="fed73-163">Now you can run the app:</span></span>

* <span data-ttu-id="fed73-164">**Déboguer > Démarrer sans débogage**</span><span class="sxs-lookup"><span data-stu-id="fed73-164">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="fed73-165">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="fed73-165">Next steps</span></span>

* <span data-ttu-id="fed73-166">Suivre le [tutoriel ASP.NET Core](/aspnet/core/data/ef-rp/intro) pour utiliser EF Core dans une application web</span><span class="sxs-lookup"><span data-stu-id="fed73-166">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="fed73-167">Découvrir les [expressions de requête LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="fed73-167">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="fed73-168">[Configurer votre modèle](xref:core/modeling/index) pour spécifier des éléments tels que la longueur [requise](xref:core/modeling/required-optional) et la longueur [maximale](xref:core/modeling/max-length)</span><span class="sxs-lookup"><span data-stu-id="fed73-168">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/required-optional) and [maximum length](xref:core/modeling/max-length)</span></span>
* <span data-ttu-id="fed73-169">Utiliser des [migrations](xref:core/managing-schemas/migrations/index) pour mettre à jour le schéma de base de données après avoir changé votre modèle</span><span class="sxs-lookup"><span data-stu-id="fed73-169">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
