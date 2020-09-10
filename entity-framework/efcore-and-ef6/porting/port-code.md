---
title: Portage à partir de EF6 vers EF Core-Portage d’un modèle basé sur du code-EF
description: Informations spécifiques sur le portage d’une application de modèle Entity Framework 6 basée sur du code pour Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: d3920e6132576e3a93dd0ffb9dac1412b6e511aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619613"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="1f6cc-103">Portage d’un modèle basé sur du code EF6 vers EF Core</span><span class="sxs-lookup"><span data-stu-id="1f6cc-103">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="1f6cc-104">Si vous avez lu tous les avertissements et que vous êtes prêt à utiliser le port, voici quelques conseils pour vous aider à démarrer.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-104">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="1f6cc-105">Installer EF Core des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="1f6cc-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="1f6cc-106">Pour utiliser EF Core, vous installez le package NuGet pour le fournisseur de base de données que vous souhaitez utiliser.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-106">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="1f6cc-107">Par exemple, lorsque vous ciblez SQL Server, vous devez installer `Microsoft.EntityFrameworkCore.SqlServer` .</span><span class="sxs-lookup"><span data-stu-id="1f6cc-107">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="1f6cc-108">Pour plus d’informations, consultez [fournisseurs de bases de données](xref:core/providers/index) .</span><span class="sxs-lookup"><span data-stu-id="1f6cc-108">See [Database Providers](xref:core/providers/index) for details.</span></span>

<span data-ttu-id="1f6cc-109">Si vous envisagez d’utiliser des migrations, vous devez également installer le `Microsoft.EntityFrameworkCore.Tools` Package.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-109">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="1f6cc-110">Il est parfait de conserver le package NuGet EF6 (EntityFramework) installé, car EF Core et EF6 peuvent être utilisés côte à côte dans la même application.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-110">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="1f6cc-111">Toutefois, si vous n’avez pas l’intention d’utiliser EF6 dans les zones de votre application, la désinstallation du package permet d’obtenir des erreurs de compilation sur des portions de code nécessitant votre attention.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-111">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="1f6cc-112">Permuter les espaces de noms</span><span class="sxs-lookup"><span data-stu-id="1f6cc-112">Swap namespaces</span></span>

<span data-ttu-id="1f6cc-113">La plupart des API que vous utilisez dans EF6 se trouvent dans l' `System.Data.Entity` espace de noms (et les sous-espaces de noms connexes).</span><span class="sxs-lookup"><span data-stu-id="1f6cc-113">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="1f6cc-114">La première modification du code consiste à basculer vers l' `Microsoft.EntityFrameworkCore` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-114">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="1f6cc-115">En général, vous démarrez avec votre fichier de code de contexte dérivé, puis vous travaillez à partir de là, en résolvant les erreurs de compilation à mesure qu’elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-115">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="1f6cc-116">Configuration du contexte (connexion, etc.)</span><span class="sxs-lookup"><span data-stu-id="1f6cc-116">Context configuration (connection etc.)</span></span>

<span data-ttu-id="1f6cc-117">Comme décrit dans [la section s’assurer EF Core fonctionne pour votre application](xref:efcore-and-ef6/porting/index), EF Core a moins de magie pour détecter la base de données à laquelle se connecter.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-117">As described in [Ensure EF Core Will Work for Your Application](xref:efcore-and-ef6/porting/index), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="1f6cc-118">Vous devez substituer la `OnConfiguring` méthode dans votre contexte dérivé et utiliser l’API spécifique du fournisseur de base de données pour configurer la connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-118">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="1f6cc-119">La plupart des applications EF6 stockent la chaîne de connexion dans le fichier d’application `App/Web.config` .</span><span class="sxs-lookup"><span data-stu-id="1f6cc-119">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="1f6cc-120">Dans EF Core, vous lisez cette chaîne de connexion à l’aide de l' `ConfigurationManager` API.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-120">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="1f6cc-121">Vous devrez peut-être ajouter une référence à l' `System.Configuration` assembly de Framework pour pouvoir utiliser cette API.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-121">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="update-your-code"></a><span data-ttu-id="1f6cc-122">Mettre à jour votre code</span><span class="sxs-lookup"><span data-stu-id="1f6cc-122">Update your code</span></span>

<span data-ttu-id="1f6cc-123">À ce stade, il s’agit de traiter les erreurs de compilation et de consulter le code pour voir si les changements de comportement ont un impact sur vous.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-123">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="1f6cc-124">Migrations existantes</span><span class="sxs-lookup"><span data-stu-id="1f6cc-124">Existing migrations</span></span>

<span data-ttu-id="1f6cc-125">Il n’existe pas vraiment un moyen pratique de porter des migrations EF6 existantes vers EF Core.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-125">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="1f6cc-126">Si possible, il est préférable de supposer que toutes les migrations précédentes de EF6 ont été appliquées à la base de données, puis de commencer à migrer le schéma à partir de ce point à l’aide de EF Core.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-126">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="1f6cc-127">Pour ce faire, vous devez utiliser la `Add-Migration` commande pour ajouter une migration une fois que le modèle est porté sur EF Core.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-127">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="1f6cc-128">Ensuite, vous supprimez tout le code `Up` des `Down` méthodes et de la migration par génération de modèles automatique.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-128">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="1f6cc-129">Les migrations suivantes seront comparées au modèle lors de la génération de modèles automatique de la migration initiale.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-129">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="1f6cc-130">Tester le port</span><span class="sxs-lookup"><span data-stu-id="1f6cc-130">Test the port</span></span>

<span data-ttu-id="1f6cc-131">Simplement parce que votre application est compilée, ne signifie pas qu’elle est correctement reportée vers EF Core.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-131">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="1f6cc-132">Vous devrez tester toutes les zones de votre application pour vous assurer qu’aucune des modifications de comportement n’a un impact négatif sur votre application.</span><span class="sxs-lookup"><span data-stu-id="1f6cc-132">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
