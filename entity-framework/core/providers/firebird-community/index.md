---
title: "Fournisseur de base de données FirebirdSQL - EF Core"
author: ralmsdeveloper
ms.author: ralmsdeveloper
ms.date: 11/22/2017
ms.assetid: d0168c04-d30d-4219-98f8-a54690cea3c6
ms.technology: entity-framework-core
uid: core/providers/firebird-community/index
ms.openlocfilehash: 682988a91ef04dbd552588a537f53124b931f17d
ms.sourcegitcommit: 1cbd3d3cd92bdaf8223b8821c58200bcfed10ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="firebird-ef-core-database-provider"></a><span data-ttu-id="73bdc-102">Fournisseur de base de données EF Core Firebird</span><span class="sxs-lookup"><span data-stu-id="73bdc-102">Firebird EF Core Database Provider</span></span>

<span data-ttu-id="73bdc-103">Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec FirebirdSQL.</span><span class="sxs-lookup"><span data-stu-id="73bdc-103">This database provider allows Entity Framework Core to be used with FirebirdSQL.</span></span> <span data-ttu-id="73bdc-104">Le fournisseur est géré dans le cadre du [projet GitHub ralmsdeveloper/EntityFrameworkCore.FirebirdSQL](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL).</span><span class="sxs-lookup"><span data-stu-id="73bdc-104">The provider is maintained as part of the [ralmsdeveloper/EntityFrameworkCore.FirebirdSQL GitHub Project](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL).</span></span>

> [!NOTE]  
>
> <span data-ttu-id="73bdc-105">Il n’est pas géré dans le cadre du projet Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="73bdc-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="73bdc-106">Quand vous envisagez un fournisseur tiers, veillez à évaluer la qualité, la gestion des licences, la prise en charge, etc., pour vous assurer qu’il répond à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="73bdc-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="73bdc-107">Installer</span><span class="sxs-lookup"><span data-stu-id="73bdc-107">Install</span></span>

<span data-ttu-id="73bdc-108">Installez le [package NuGet EntityFrameworkCore.FirebirdSQL](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSQL).</span><span class="sxs-lookup"><span data-stu-id="73bdc-108">Install the [EntityFrameworkCore.FirebirdSQL NuGet package](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSQL).</span></span>

``` powershell
Install-Package EntityFrameworkCore.FirebirdSQL
```

## <a name="get-started"></a><span data-ttu-id="73bdc-109">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="73bdc-109">Get Started</span></span>

<span data-ttu-id="73bdc-110">Consultez la [documentation de prise en main sur le site du projet](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki).</span><span class="sxs-lookup"><span data-stu-id="73bdc-110">See the [getting started documentation on the project site](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="73bdc-111">Moteurs de base de données pris en charge</span><span class="sxs-lookup"><span data-stu-id="73bdc-111">Supported Database Engines</span></span>

* <span data-ttu-id="73bdc-112">FirebirdSql 2.5</span><span class="sxs-lookup"><span data-stu-id="73bdc-112">FirebirdSql 2.5</span></span>
* <span data-ttu-id="73bdc-113">FirebirdSql 3.X</span><span class="sxs-lookup"><span data-stu-id="73bdc-113">FirebirdSql 3.X</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="73bdc-114">Plateformes prises en charge</span><span class="sxs-lookup"><span data-stu-id="73bdc-114">Supported Platforms</span></span>

* <span data-ttu-id="73bdc-115">.NET Framework (versions 4.5.1 et suivantes)</span><span class="sxs-lookup"><span data-stu-id="73bdc-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="73bdc-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="73bdc-116">.NET Core</span></span>

* <span data-ttu-id="73bdc-117">Mono (versions 4.2.0 et suivantes)</span><span class="sxs-lookup"><span data-stu-id="73bdc-117">Mono (4.2.0 onwards)</span></span>