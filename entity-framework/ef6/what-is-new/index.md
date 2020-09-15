---
title: Nouveautés – EF6
description: Nouveautés d’Entity Framework 6.
author: divega
ms.date: 09/12/2019
uid: ef6/what-is-new/index
ms.openlocfilehash: 3663276174b7f17701756a82b592ebb971516719
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073654"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="b99e9-103">Nouveautés dans EF6</span><span class="sxs-lookup"><span data-stu-id="b99e9-103">What's new in EF6</span></span>

<span data-ttu-id="b99e9-104">Nous vous recommandons vivement d’utiliser la dernière version publiée d’Entity Framework pour bénéficier des dernières fonctionnalités et d’une stabilité optimale.</span><span class="sxs-lookup"><span data-stu-id="b99e9-104">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="b99e9-105">Toutefois, vous pouvez aussi avoir besoin d’utiliser une version antérieure ou vouloir tester les nouvelles améliorations de la dernière préversion.</span><span class="sxs-lookup"><span data-stu-id="b99e9-105">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="b99e9-106">Pour installer des versions spécifiques d’EF, consultez [Obtenir Entity Framework](xref:ef6/fundamentals/install).</span><span class="sxs-lookup"><span data-stu-id="b99e9-106">To install specific versions of EF, see [Get Entity Framework](xref:ef6/fundamentals/install).</span></span>

## <a name="ef-640"></a><span data-ttu-id="b99e9-107">EF 6.4.0</span><span class="sxs-lookup"><span data-stu-id="b99e9-107">EF 6.4.0</span></span>

<span data-ttu-id="b99e9-108">Le runtime EF 6.4.0 a été publié sur NuGet en décembre 2019.</span><span class="sxs-lookup"><span data-stu-id="b99e9-108">The EF 6.4.0 runtime was released to NuGet in December  2019.</span></span> <span data-ttu-id="b99e9-109">L’objectif principal d’EF 6.4 est de peaufiner les fonctionnalités et les scénarios fournis par EF 6.3.</span><span class="sxs-lookup"><span data-stu-id="b99e9-109">The primary goal of EF 6.4 is to polish the features and scenarios that was delivered in EF 6.3.</span></span> <span data-ttu-id="b99e9-110">Consultez la [liste des correctifs importants](https://github.com/dotnet/ef6/milestone/14?closed=1) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="b99e9-110">See [list of important fixes](https://github.com/dotnet/ef6/milestone/14?closed=1) on Github.</span></span>

## <a name="ef-630"></a><span data-ttu-id="b99e9-111">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="b99e9-111">EF 6.3.0</span></span>

<span data-ttu-id="b99e9-112">Le runtime EF 6.3.0 a été publié sur NuGet en septembre 2019.</span><span class="sxs-lookup"><span data-stu-id="b99e9-112">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="b99e9-113">L’objectif principal de cette version était de faciliter la migration des applications existantes qui utilisent EF 6 vers .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="b99e9-113">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="b99e9-114">La communauté a également contribué à plusieurs correctifs de bogues et améliorations.</span><span class="sxs-lookup"><span data-stu-id="b99e9-114">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="b99e9-115">Pour plus d’informations, consultez les problèmes fermés à chaque [jalon](https://github.com/aspnet/EntityFramework6/milestones?state=closed) de la version 6.3.0.</span><span class="sxs-lookup"><span data-stu-id="b99e9-115">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="b99e9-116">Voici les plus connus :</span><span class="sxs-lookup"><span data-stu-id="b99e9-116">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="b99e9-117">Prise en charge de .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="b99e9-117">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="b99e9-118">Le package EntityFramework cible maintenant .NET Standard 2.1 en plus de .NET Framework 4.x.</span><span class="sxs-lookup"><span data-stu-id="b99e9-118">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x.</span></span>
  - <span data-ttu-id="b99e9-119">Cela signifie que EF 6.3 est multiplateforme et pris en charge sur d’autres systèmes d’exploitation que Windows, comme Linux et macOS.</span><span class="sxs-lookup"><span data-stu-id="b99e9-119">This means that EF 6.3 is cross-platform and supported on other operating systems besides Windows, like Linux and macOS.</span></span>
  - <span data-ttu-id="b99e9-120">Les commandes de migrations ont été réécrites pour s’exécuter hors processus et fonctionner avec des projets de type SDK.</span><span class="sxs-lookup"><span data-stu-id="b99e9-120">The migrations commands have been rewritten to execute out of process and work with SDK-style projects.</span></span>
- <span data-ttu-id="b99e9-121">Prise en charge de SQL Server HierarchyId.</span><span class="sxs-lookup"><span data-stu-id="b99e9-121">Support for SQL Server HierarchyId.</span></span>
- <span data-ttu-id="b99e9-122">Compatibilité améliorée avec Roslyn et NuGet PackageReference.</span><span class="sxs-lookup"><span data-stu-id="b99e9-122">Improved compatibility with Roslyn and NuGet PackageReference.</span></span>
- <span data-ttu-id="b99e9-123">Ajout de l’utilitaire `ef6.exe` pour l’activation, l’ajout, l’écriture de scripts et l’application de migrations à partir d’assemblys.</span><span class="sxs-lookup"><span data-stu-id="b99e9-123">Added `ef6.exe` utility for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="b99e9-124">Ceci remplace `migrate.exe`.</span><span class="sxs-lookup"><span data-stu-id="b99e9-124">This replaces `migrate.exe`.</span></span>

### <a name="ef-designer-support"></a><span data-ttu-id="b99e9-125">Prise en charge du concepteur EF</span><span class="sxs-lookup"><span data-stu-id="b99e9-125">EF designer support</span></span>

<span data-ttu-id="b99e9-126">L’utilisation du concepteur EF directement dans les projets .NET Core ou .NET Standard, ou dans les projets .NET Framework de style SDK, n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="b99e9-126">There's currently no support for using the EF designer directly on .NET Core or .NET Standard projects or on an SDK-style .NET Framework project.</span></span> 

<span data-ttu-id="b99e9-127">Vous pouvez contourner cette limitation en ajoutant le fichier EDMX et les classes générées pour les entités et pour DbContext en tant que fichiers liés à un projet .NET Core 3.0 ou .NET Standard 2.1 dans la même solution.</span><span class="sxs-lookup"><span data-stu-id="b99e9-127">You can work around this limitation by adding the EDMX file and the generated classes for the entities and the DbContext as linked files to a .NET Core 3.0 or .NET Standard 2.1 project in the same solution.</span></span>

<span data-ttu-id="b99e9-128">Les fichiers liés ressembleront à ce qui suit dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="b99e9-128">The linked files will look like this in the project file:</span></span>

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

<span data-ttu-id="b99e9-129">Notez que le fichier EDMX est lié à l’action de création EntityDeploy.</span><span class="sxs-lookup"><span data-stu-id="b99e9-129">Note that the EDMX file is linked with the EntityDeploy build action.</span></span> <span data-ttu-id="b99e9-130">Il s’agit d’une tâche MSBuild spéciale (désormais incluse dans le package EF 6.3) qui prend en charge l’ajout du modèle EF à l’assembly cible en tant que ressources incorporées (ou copiez-le en tant que fichiers dans le dossier de sortie, en fonction du paramètre de traitement des artefacts de métadonnées dans EDMX).</span><span class="sxs-lookup"><span data-stu-id="b99e9-130">This is a special MSBuild task (now included in the EF 6.3 package) that takes care of adding the EF model into the target assembly as embedded resources (or copying it as files in the output folder, depending on the Metadata Artifact Processing setting in the EDMX).</span></span> <span data-ttu-id="b99e9-131">Pour plus d’informations sur la façon d’obtenir cette configuration, consultez notre [exemple .NET Core EDMX](https://aka.ms/EdmxDotNetCoreSample).</span><span class="sxs-lookup"><span data-stu-id="b99e9-131">For more details on how to get this set up, see our [EDMX .NET Core sample](https://aka.ms/EdmxDotNetCoreSample).</span></span>

<span data-ttu-id="b99e9-132">Avertissement : vérifiez que le projet .NET Framework utilisant l’ancien style (autre que le style SDK) qui définit le « vrai » fichier .edmx est situé _avant_ le projet qui définit le lien situé dans le fichier .sln.</span><span class="sxs-lookup"><span data-stu-id="b99e9-132">Warning: make sure the old style (i.e. non-SDK-style) .NET Framework project defining the "real" .edmx file comes _before_ the project defining the link inside the .sln file.</span></span> <span data-ttu-id="b99e9-133">Sinon, lorsque vous ouvrirez le fichier .edmx dans le concepteur, vous verrez le message d’erreur « Entity Framework n’est pas disponible dans la version cible de .NET Framework actuellement spécifiée pour le projet.</span><span class="sxs-lookup"><span data-stu-id="b99e9-133">Otherwise, when you open the .edmx file in the designer, you see the error message "The Entity Framework is not available in the target framework currently specified for the project.</span></span> <span data-ttu-id="b99e9-134">Vous pouvez changer la version cible de .NET Framework du projet ou modifier le modèle dans XmlEditor ».</span><span class="sxs-lookup"><span data-stu-id="b99e9-134">You can change the target framework of the project or edit the model in the XmlEditor".</span></span>

## <a name="past-releases"></a><span data-ttu-id="b99e9-135">Versions précédentes</span><span class="sxs-lookup"><span data-stu-id="b99e9-135">Past Releases</span></span>

<span data-ttu-id="b99e9-136">La page [Versions précédentes](xref:ef6/what-is-new/past-releases) contient une archive de toutes les versions précédentes d’Entity Framework et des principales fonctionnalités introduites dans chaque version.</span><span class="sxs-lookup"><span data-stu-id="b99e9-136">The [Past Releases](xref:ef6/what-is-new/past-releases) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
