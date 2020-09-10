---
title: Portage à partir de EF6 vers EF Core-Portage d’un modèle basé sur EDMX-EF
description: Informations spécifiques sur le portage d’une application de modèle Entity Framework 6 basée sur EDMX pour Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 3767b1dc083ec886115cea9b0750101fb49ad84c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619607"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="f4be7-103">Portage d’un modèle basé sur EF6 EDMX vers EF Core</span><span class="sxs-lookup"><span data-stu-id="f4be7-103">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="f4be7-104">EF Core ne prend pas en charge le format de fichier EDMX pour les modèles.</span><span class="sxs-lookup"><span data-stu-id="f4be7-104">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="f4be7-105">La meilleure option pour porter ces modèles consiste à générer un nouveau modèle basé sur du code à partir de la base de données de votre application.</span><span class="sxs-lookup"><span data-stu-id="f4be7-105">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="f4be7-106">Installer EF Core des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="f4be7-106">Install EF Core NuGet packages</span></span>

<span data-ttu-id="f4be7-107">Installez le package NuGet `Microsoft.EntityFrameworkCore.Tools`.</span><span class="sxs-lookup"><span data-stu-id="f4be7-107">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="f4be7-108">Régénérer le modèle</span><span class="sxs-lookup"><span data-stu-id="f4be7-108">Regenerate the model</span></span>

<span data-ttu-id="f4be7-109">Vous pouvez maintenant utiliser la fonctionnalité d’ingénierie à rebours pour créer un modèle basé sur votre base de données existante.</span><span class="sxs-lookup"><span data-stu-id="f4be7-109">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="f4be7-110">Exécutez la commande suivante dans la console du gestionnaire de package (outils – > gestionnaire de package NuGet – > console du gestionnaire de package).</span><span class="sxs-lookup"><span data-stu-id="f4be7-110">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="f4be7-111">Consultez [console du gestionnaire de package (Visual Studio)](xref:core/miscellaneous/cli/powershell) pour obtenir des options de commande permettant d’effectuer une génération de modèles automatique d’un sous-ensemble de tables.</span><span class="sxs-lookup"><span data-stu-id="f4be7-111">See [Package Manager Console (Visual Studio)](xref:core/miscellaneous/cli/powershell) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="f4be7-112">Par exemple, voici la commande pour générer un modèle de structure à partir de la base de données de blogs sur votre instance SQL Server de base de données locale.</span><span class="sxs-lookup"><span data-stu-id="f4be7-112">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="f4be7-113">Supprimer le modèle EF6</span><span class="sxs-lookup"><span data-stu-id="f4be7-113">Remove EF6 model</span></span>

<span data-ttu-id="f4be7-114">Vous pouvez maintenant supprimer le modèle EF6 de votre application.</span><span class="sxs-lookup"><span data-stu-id="f4be7-114">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="f4be7-115">Il est parfait de conserver le package NuGet EF6 (EntityFramework) installé, car EF Core et EF6 peuvent être utilisés côte à côte dans la même application.</span><span class="sxs-lookup"><span data-stu-id="f4be7-115">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="f4be7-116">Toutefois, si vous n’avez pas l’intention d’utiliser EF6 dans les zones de votre application, la désinstallation du package permet d’obtenir des erreurs de compilation sur des portions de code nécessitant votre attention.</span><span class="sxs-lookup"><span data-stu-id="f4be7-116">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="f4be7-117">Mettre à jour votre code</span><span class="sxs-lookup"><span data-stu-id="f4be7-117">Update your code</span></span>

<span data-ttu-id="f4be7-118">À ce stade, il s’agit de traiter les erreurs de compilation et de consulter le code pour voir si les changements de comportement entre EF6 et EF Core ont un impact sur vous.</span><span class="sxs-lookup"><span data-stu-id="f4be7-118">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="f4be7-119">Tester le port</span><span class="sxs-lookup"><span data-stu-id="f4be7-119">Test the port</span></span>

<span data-ttu-id="f4be7-120">Simplement parce que votre application est compilée, ne signifie pas qu’elle est correctement reportée vers EF Core.</span><span class="sxs-lookup"><span data-stu-id="f4be7-120">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="f4be7-121">Vous devrez tester toutes les zones de votre application pour vous assurer qu’aucune des modifications de comportement n’a un impact négatif sur votre application.</span><span class="sxs-lookup"><span data-stu-id="f4be7-121">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
