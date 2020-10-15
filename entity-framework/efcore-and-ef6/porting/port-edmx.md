---
title: Portage à partir de EF6 vers EF Core-Portage d’un EDMX-Based modèle-EF
description: Informations spécifiques sur le portage d’une application de modèle Entity Framework 6 basée sur EDMX pour Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 27b37ad1c2e3436ae96a71bc97e953763c48ee50
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064261"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="094fd-103">Portage d’un modèle de EDMX-Based EF6 vers EF Core</span><span class="sxs-lookup"><span data-stu-id="094fd-103">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="094fd-104">EF Core ne prend pas en charge le format de fichier EDMX pour les modèles.</span><span class="sxs-lookup"><span data-stu-id="094fd-104">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="094fd-105">La meilleure option pour porter ces modèles consiste à générer un nouveau modèle basé sur du code à partir de la base de données de votre application.</span><span class="sxs-lookup"><span data-stu-id="094fd-105">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="094fd-106">Installer EF Core des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="094fd-106">Install EF Core NuGet packages</span></span>

<span data-ttu-id="094fd-107">Installez le package NuGet `Microsoft.EntityFrameworkCore.Tools`.</span><span class="sxs-lookup"><span data-stu-id="094fd-107">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="094fd-108">Régénérer le modèle</span><span class="sxs-lookup"><span data-stu-id="094fd-108">Regenerate the model</span></span>

<span data-ttu-id="094fd-109">Vous pouvez maintenant utiliser la fonctionnalité d’ingénierie à rebours pour créer un modèle basé sur votre base de données existante.</span><span class="sxs-lookup"><span data-stu-id="094fd-109">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="094fd-110">Exécutez la commande suivante dans la console du gestionnaire de package (outils – > gestionnaire de package NuGet – > console du gestionnaire de package).</span><span class="sxs-lookup"><span data-stu-id="094fd-110">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="094fd-111">Consultez [console du gestionnaire de package (Visual Studio)](xref:core/miscellaneous/cli/powershell) pour obtenir des options de commande permettant d’effectuer une génération de modèles automatique d’un sous-ensemble de tables.</span><span class="sxs-lookup"><span data-stu-id="094fd-111">See [Package Manager Console (Visual Studio)](xref:core/miscellaneous/cli/powershell) for command options to scaffold a subset of tables etc.</span></span>

```powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="094fd-112">Par exemple, voici la commande pour générer un modèle de structure à partir de la base de données de blogs sur votre instance SQL Server de base de données locale.</span><span class="sxs-lookup"><span data-stu-id="094fd-112">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="094fd-113">Supprimer le modèle EF6</span><span class="sxs-lookup"><span data-stu-id="094fd-113">Remove EF6 model</span></span>

<span data-ttu-id="094fd-114">Vous pouvez maintenant supprimer le modèle EF6 de votre application.</span><span class="sxs-lookup"><span data-stu-id="094fd-114">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="094fd-115">Il est parfait de conserver le package NuGet EF6 (EntityFramework) installé, car EF Core et EF6 peuvent être utilisés côte à côte dans la même application.</span><span class="sxs-lookup"><span data-stu-id="094fd-115">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="094fd-116">Toutefois, si vous n’avez pas l’intention d’utiliser EF6 dans les zones de votre application, la désinstallation du package permet d’obtenir des erreurs de compilation sur des portions de code nécessitant votre attention.</span><span class="sxs-lookup"><span data-stu-id="094fd-116">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="094fd-117">Mettre à jour votre code</span><span class="sxs-lookup"><span data-stu-id="094fd-117">Update your code</span></span>

<span data-ttu-id="094fd-118">À ce stade, il s’agit de traiter les erreurs de compilation et de consulter le code pour voir si les changements de comportement entre EF6 et EF Core ont un impact sur vous.</span><span class="sxs-lookup"><span data-stu-id="094fd-118">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="094fd-119">Tester le port</span><span class="sxs-lookup"><span data-stu-id="094fd-119">Test the port</span></span>

<span data-ttu-id="094fd-120">Simplement parce que votre application est compilée, ne signifie pas qu’elle est correctement reportée vers EF Core.</span><span class="sxs-lookup"><span data-stu-id="094fd-120">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="094fd-121">Vous devrez tester toutes les zones de votre application pour vous assurer qu’aucune des modifications de comportement n’a un impact négatif sur votre application.</span><span class="sxs-lookup"><span data-stu-id="094fd-121">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
