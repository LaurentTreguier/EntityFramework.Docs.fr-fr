---
title: Sélection de Entity Framework version du runtime pour les modèles du concepteur EF-EF6
description: Sélection de Entity Framework version du runtime pour les modèles du concepteur EF dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: dcd06ed9585028b3a318d867b03980254979a32f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066094"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a><span data-ttu-id="9df34-103">Sélection de Entity Framework version du runtime pour les modèles du concepteur EF</span><span class="sxs-lookup"><span data-stu-id="9df34-103">Selecting Entity Framework Runtime Version for EF Designer Models</span></span>
> [!NOTE]
> <span data-ttu-id="9df34-104">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="9df34-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="9df34-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="9df34-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="9df34-106">À partir de EF6, l’écran suivant a été ajouté au concepteur EF pour vous permettre de sélectionner la version du runtime que vous souhaitez cibler lors de la création d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="9df34-106">Starting with EF6 the following screen was added to the EF Designer to allow you to select the version of the runtime you wish to target when creating a model.</span></span> <span data-ttu-id="9df34-107">L’écran s’affiche lorsque la dernière version de Entity Framework n’est pas déjà installée dans le projet.</span><span class="sxs-lookup"><span data-stu-id="9df34-107">The screen will appear when the latest version of Entity Framework is not already installed in the project.</span></span> <span data-ttu-id="9df34-108">Si la version la plus récente est déjà installée, elle est utilisée par défaut.</span><span class="sxs-lookup"><span data-stu-id="9df34-108">If the latest version is already installed it will just be used by default.</span></span>

![Sélectionner la version du Runtime](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a><span data-ttu-id="9df34-110">Ciblage de EF6. x</span><span class="sxs-lookup"><span data-stu-id="9df34-110">Targeting EF6.x</span></span>

<span data-ttu-id="9df34-111">Vous pouvez choisir EF6 dans l’écran « choisir votre version » pour ajouter le runtime EF6 à votre projet.</span><span class="sxs-lookup"><span data-stu-id="9df34-111">You can choose EF6 from the 'Choose Your Version' screen to add the EF6 runtime to your project.</span></span> <span data-ttu-id="9df34-112">Une fois que vous avez ajouté EF6, vous ne voyez plus cet écran dans le projet actuel.</span><span class="sxs-lookup"><span data-stu-id="9df34-112">Once you've added EF6, you’ll stop seeing this screen in the current project.</span></span>

<span data-ttu-id="9df34-113">EF6 sera désactivé si vous avez déjà installé une version antérieure d’EF (puisque vous ne pouvez pas cibler plusieurs versions du runtime à partir du même projet).</span><span class="sxs-lookup"><span data-stu-id="9df34-113">EF6 will be disabled if you already have an older version of EF installed (since you can't target multiple versions of the runtime from the same project).</span></span> <span data-ttu-id="9df34-114">Si l’option EF6 n’est pas activée ici, procédez comme suit pour mettre à niveau votre projet vers EF6 :</span><span class="sxs-lookup"><span data-stu-id="9df34-114">If EF6 option is not enabled here, follow these steps to upgrade your project to EF6:</span></span>

1.  <span data-ttu-id="9df34-115">Dans Explorateur de solutions, cliquez avec le bouton droit sur votre projet, puis sélectionnez **gérer les packages NuGet...**</span><span class="sxs-lookup"><span data-stu-id="9df34-115">Right-click on your project in Solution Explorer and select **Manage NuGet Packages...**</span></span>
2.  <span data-ttu-id="9df34-116">Sélectionner les **mises à jour**</span><span class="sxs-lookup"><span data-stu-id="9df34-116">Select **Updates**</span></span>
3.  <span data-ttu-id="9df34-117">Sélectionnez **EntityFramework** (Assurez-vous qu’il va le mettre à jour vers la version de votre choix)</span><span class="sxs-lookup"><span data-stu-id="9df34-117">Select **EntityFramework** (make sure it is going to update it to the version you want)</span></span>
4.  <span data-ttu-id="9df34-118">Cliquez sur **Mettre à jour**</span><span class="sxs-lookup"><span data-stu-id="9df34-118">Click **Update**</span></span>

 

## <a name="targeting-ef5x"></a><span data-ttu-id="9df34-119">Ciblage de EF5. x</span><span class="sxs-lookup"><span data-stu-id="9df34-119">Targeting EF5.x</span></span>

<span data-ttu-id="9df34-120">Vous pouvez choisir EF5 dans l’écran « choisir votre version » pour ajouter le runtime EF5 à votre projet.</span><span class="sxs-lookup"><span data-stu-id="9df34-120">You can choose EF5 from the 'Choose Your Version' screen to add the EF5 runtime to your project.</span></span> <span data-ttu-id="9df34-121">Une fois que vous avez ajouté EF5, vous voyez toujours l’écran avec l’option EF6 désactivée.</span><span class="sxs-lookup"><span data-stu-id="9df34-121">Once you've added EF5, you’ll still see the screen with the EF6 option disabled.</span></span>

<span data-ttu-id="9df34-122">Si vous avez une version EF4. x du runtime déjà installée, vous verrez que la version d’EF est indiquée à l’écran plutôt que EF5.</span><span class="sxs-lookup"><span data-stu-id="9df34-122">If you have an EF4.x version of the runtime already installed then you will see that version of EF listed in the screen rather than EF5.</span></span> <span data-ttu-id="9df34-123">Dans ce cas, vous pouvez effectuer la mise à niveau vers EF5 en procédant comme suit :</span><span class="sxs-lookup"><span data-stu-id="9df34-123">In this situation you can upgrade to EF5 using the following steps:</span></span>

1.  <span data-ttu-id="9df34-124">Sélectionnez **outils- &gt; bibliothèque gestionnaire de package- &gt; console du gestionnaire de package**</span><span class="sxs-lookup"><span data-stu-id="9df34-124">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="9df34-125">Exécuter l' **Install-Package EntityFramework-version 5.0.0**</span><span class="sxs-lookup"><span data-stu-id="9df34-125">Run **Install-Package EntityFramework -version 5.0.0**</span></span>

 

## <a name="targeting-ef4x"></a><span data-ttu-id="9df34-126">Ciblage de EF4. x</span><span class="sxs-lookup"><span data-stu-id="9df34-126">Targeting EF4.x</span></span>

<span data-ttu-id="9df34-127">Vous pouvez installer le runtime EF4. x dans votre projet en procédant comme suit :</span><span class="sxs-lookup"><span data-stu-id="9df34-127">You can install the EF4.x runtime to your project using the following steps:</span></span>

1.  <span data-ttu-id="9df34-128">Sélectionnez **outils- &gt; bibliothèque gestionnaire de package- &gt; console du gestionnaire de package**</span><span class="sxs-lookup"><span data-stu-id="9df34-128">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="9df34-129">Exécuter l' **Install-Package EntityFramework-version 4.3.0**</span><span class="sxs-lookup"><span data-stu-id="9df34-129">Run **Install-Package EntityFramework -version 4.3.0**</span></span>
