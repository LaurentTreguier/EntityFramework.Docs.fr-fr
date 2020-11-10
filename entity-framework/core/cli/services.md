---
title: Services au moment du design-EF Core
description: Informations sur les services Entity Framework Core au moment de la conception
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431570"
---
# <a name="design-time-services"></a><span data-ttu-id="2c946-103">Services à la conception</span><span class="sxs-lookup"><span data-stu-id="2c946-103">Design-time services</span></span>

<span data-ttu-id="2c946-104">Certains services utilisés par les outils sont utilisés uniquement au moment du Design.</span><span class="sxs-lookup"><span data-stu-id="2c946-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="2c946-105">Ces services sont gérés séparément des services d’exécution de EF Core pour les empêcher d’être déployés avec votre application.</span><span class="sxs-lookup"><span data-stu-id="2c946-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="2c946-106">Pour remplacer l’un de ces services (par exemple, le service pour générer des fichiers de migration), ajoutez une implémentation de `IDesignTimeServices` à votre projet de démarrage.</span><span class="sxs-lookup"><span data-stu-id="2c946-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a><span data-ttu-id="2c946-107">Référencement de Microsoft. EntityFrameworkCore. Design</span><span class="sxs-lookup"><span data-stu-id="2c946-107">Referencing Microsoft.EntityFrameworkCore.Design</span></span>

<span data-ttu-id="2c946-108">Microsoft. EntityFrameworkCore. Design est un package DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="2c946-108">Microsoft.EntityFrameworkCore.Design is a DevelopmentDependency package.</span></span> <span data-ttu-id="2c946-109">Cela signifie que la dépendance ne sera pas transitive dans d’autres projets, et que vous ne pouvez pas, par défaut, référencer ses types.</span><span class="sxs-lookup"><span data-stu-id="2c946-109">This means that the dependency won't flow transitively into other projects, and that you cannot, by default, reference its types.</span></span>

<span data-ttu-id="2c946-110">Pour référencer ses types et remplacer les services au moment du design, mettez à jour les métadonnées de l’élément PackageReference dans votre fichier projet.</span><span class="sxs-lookup"><span data-stu-id="2c946-110">In order to reference its types and override design-time services, update the PackageReference item's metadata in your project file.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="2c946-111">Si le package est référencé de manière transitive via Microsoft. EntityFrameworkCore. Tools, vous devrez ajouter un PackageReference explicite au package et modifier ses métadonnées.</span><span class="sxs-lookup"><span data-stu-id="2c946-111">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package and change its metadata.</span></span>

## <a name="list-of-services"></a><span data-ttu-id="2c946-112">Liste des services</span><span class="sxs-lookup"><span data-stu-id="2c946-112">List of services</span></span>

<span data-ttu-id="2c946-113">La liste suivante répertorie les services au moment du Design.</span><span class="sxs-lookup"><span data-stu-id="2c946-113">The following is a list of the design-time services.</span></span>

<span data-ttu-id="2c946-114">Service</span><span class="sxs-lookup"><span data-stu-id="2c946-114">Service</span></span>                                                                              | <span data-ttu-id="2c946-115">Description</span><span class="sxs-lookup"><span data-stu-id="2c946-115">Description</span></span>
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | <span data-ttu-id="2c946-116">Génère le code pour les annotations de modèle correspondantes.</span><span class="sxs-lookup"><span data-stu-id="2c946-116">Generates the code for corresponding model annotations.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | <span data-ttu-id="2c946-117">Aide à la génération de code C#.</span><span class="sxs-lookup"><span data-stu-id="2c946-117">Helps with generating C# code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | <span data-ttu-id="2c946-118">Pluralise et singularise.</span><span class="sxs-lookup"><span data-stu-id="2c946-118">Pluralizes and singularizes words.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | <span data-ttu-id="2c946-119">Génère du code pour une migration.</span><span class="sxs-lookup"><span data-stu-id="2c946-119">Generates code for a migration.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | <span data-ttu-id="2c946-120">Classe principale pour la gestion des fichiers de migration.</span><span class="sxs-lookup"><span data-stu-id="2c946-120">The main class for managing migration files.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | <span data-ttu-id="2c946-121">Crée un modèle de base de données à partir d’une base de données.</span><span class="sxs-lookup"><span data-stu-id="2c946-121">Creates a database model from a database.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | <span data-ttu-id="2c946-122">Génère du code pour un modèle.</span><span class="sxs-lookup"><span data-stu-id="2c946-122">Generates code for a model.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | <span data-ttu-id="2c946-123">Génère du code OnConfiguring.</span><span class="sxs-lookup"><span data-stu-id="2c946-123">Generates OnConfiguring code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | <span data-ttu-id="2c946-124">Classe principale pour la génération de modèles automatique de modèles rétroconçus.</span><span class="sxs-lookup"><span data-stu-id="2c946-124">The main class for scaffolding reverse engineered models.</span></span>

## <a name="using-services"></a><span data-ttu-id="2c946-125">Utilisation des services</span><span class="sxs-lookup"><span data-stu-id="2c946-125">Using services</span></span>

<span data-ttu-id="2c946-126">Ces services peuvent également être utiles pour la création de vos propres outils.</span><span class="sxs-lookup"><span data-stu-id="2c946-126">These services can also be useful for creating your own tools.</span></span> <span data-ttu-id="2c946-127">Par exemple, lorsque vous souhaitez automatiser une partie de votre flux de travail au moment de la conception.</span><span class="sxs-lookup"><span data-stu-id="2c946-127">For example, when you want to automate part of you design-time workflow.</span></span>

<span data-ttu-id="2c946-128">Vous pouvez créer un fournisseur de services contenant ces services à l’aide des méthodes d’extension AddEntityFrameworkDesignTimeServices et AddDbContextDesignTimeServices.</span><span class="sxs-lookup"><span data-stu-id="2c946-128">You can build a service provider containing these services using the AddEntityFrameworkDesignTimeServices and AddDbContextDesignTimeServices extension methods.</span></span>

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
