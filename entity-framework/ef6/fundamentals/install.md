---
title: Obtient Entity Framework-EF6
description: Télécharger Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/install
ms.openlocfilehash: 7dc4c560cc4eb24a1c5d5dac0c9e28883de471b0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065457"
---
# <a name="get-entity-framework"></a><span data-ttu-id="6acdb-103">Obtenir Entity Framework</span><span class="sxs-lookup"><span data-stu-id="6acdb-103">Get Entity Framework</span></span>
<span data-ttu-id="6acdb-104">Entity Framework se compose des outils EF pour Visual Studio et du runtime EF.</span><span class="sxs-lookup"><span data-stu-id="6acdb-104">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="6acdb-105">Outils EF pour Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6acdb-105">EF Tools for Visual Studio</span></span>

<span data-ttu-id="6acdb-106">Le [Entity Framework Tools pour Visual Studio](/visualstudio/data-tools/entity-data-model-tools-in-visual-studio) inclut le concepteur EF et l’Assistant Modèle EF et sont requis pour les flux de travail First et Model First.</span><span class="sxs-lookup"><span data-stu-id="6acdb-106">The [Entity Framework Tools for Visual Studio](/visualstudio/data-tools/entity-data-model-tools-in-visual-studio) include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="6acdb-107">Les outils EF sont inclus dans toutes les versions récentes de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6acdb-107">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="6acdb-108">Si vous effectuez une installation personnalisée de Visual Studio, vous devez vous assurer que l’élément « outils Entity Framework 6 » est sélectionné en choisissant une charge de travail qui l’y ajoute ou en la sélectionnant en tant que composant individuel.</span><span class="sxs-lookup"><span data-stu-id="6acdb-108">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="6acdb-109">Pour certaines versions antérieures de Visual Studio, les outils EF mis à jour sont disponibles en téléchargement.</span><span class="sxs-lookup"><span data-stu-id="6acdb-109">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="6acdb-110">Pour obtenir des conseils sur la façon d’obtenir la dernière version des outils EF disponibles pour votre version de Visual Studio, consultez les [versions de Visual Studio](xref:ef6/what-is-new/visual-studio) .</span><span class="sxs-lookup"><span data-stu-id="6acdb-110">See [Visual Studio Versions](xref:ef6/what-is-new/visual-studio) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="6acdb-111">Runtime EF</span><span class="sxs-lookup"><span data-stu-id="6acdb-111">EF Runtime</span></span>

<span data-ttu-id="6acdb-112">La dernière version de Entity Framework est disponible en tant que [package NuGet](https://nuget.org/packages/EntityFramework/)de l’EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="6acdb-112">The latest version of Entity Framework is available as the [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="6acdb-113">Si vous n’êtes pas familiarisé avec le gestionnaire de package NuGet, nous vous invitons à lire la [vue d’ensemble de NuGet](/nuget/consume-packages/overview-and-workflow).</span><span class="sxs-lookup"><span data-stu-id="6acdb-113">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="6acdb-114">Installation du package NuGet d’EF</span><span class="sxs-lookup"><span data-stu-id="6acdb-114">Installing the EF NuGet Package</span></span>

<span data-ttu-id="6acdb-115">Vous pouvez installer le package EntityFramework en cliquant avec le bouton droit sur le dossier **références** de votre projet et en sélectionnant **gérer les packages NuGet...**</span><span class="sxs-lookup"><span data-stu-id="6acdb-115">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![Gérer les packages NuGet](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="6acdb-117">Installation à partir de la console du gestionnaire de package</span><span class="sxs-lookup"><span data-stu-id="6acdb-117">Installing from Package Manager Console</span></span>

<span data-ttu-id="6acdb-118">Vous pouvez également installer EntityFramework en exécutant la commande suivante dans la console du [Gestionnaire de package](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="6acdb-118">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="6acdb-119">Installation d’une version spécifique d’EF</span><span class="sxs-lookup"><span data-stu-id="6acdb-119">Installing a specific version of EF</span></span>

<span data-ttu-id="6acdb-120">À partir d’EF 4,1, les nouvelles versions du runtime EF ont été publiées en tant que [package NuGet](https://www.nuget.org/packages/EntityFramework/)de l’EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="6acdb-120">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Package](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="6acdb-121">N’importe laquelle de ces versions peut être ajoutée à un projet basé sur le .NET Framework en exécutant la commande suivante dans la [console du gestionnaire de package](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)de Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="6acdb-121">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="6acdb-122">Notez que `<number>` représente la version spécifique d’EF à installer.</span><span class="sxs-lookup"><span data-stu-id="6acdb-122">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="6acdb-123">Par exemple, 6.2.0 est la version du numéro pour EF 6,2.</span><span class="sxs-lookup"><span data-stu-id="6acdb-123">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="6acdb-124">Les exécutions EF antérieures à 4,1 faisaient partie de .NET Framework et ne peuvent pas être installées séparément.</span><span class="sxs-lookup"><span data-stu-id="6acdb-124">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="6acdb-125">Installation de la dernière version préliminaire</span><span class="sxs-lookup"><span data-stu-id="6acdb-125">Installing the Latest Preview</span></span>

<span data-ttu-id="6acdb-126">Les méthodes ci-dessus vous offriront la toute dernière version de Entity Framework entièrement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="6acdb-126">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="6acdb-127">Il existe souvent des versions préliminaires de Entity Framework disponibles que nous aimerions pouvoir essayer et nous envoyer vos commentaires.</span><span class="sxs-lookup"><span data-stu-id="6acdb-127">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="6acdb-128">Pour installer la dernière version préliminaire d’EntityFramework, vous pouvez sélectionner **inclure la version préliminaire** dans la fenêtre gérer les packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="6acdb-128">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="6acdb-129">Si aucune version préliminaire n’est disponible, vous obtiendrez automatiquement la dernière version entièrement prise en charge de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="6acdb-129">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![Inclure la version préliminaire](~/ef6/media/includeprerelease.png)

<span data-ttu-id="6acdb-131">Vous pouvez également exécuter la commande suivante dans la console du [Gestionnaire de package](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="6acdb-131">Alternatively, you can run the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```
