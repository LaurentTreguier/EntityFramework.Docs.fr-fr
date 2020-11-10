---
title: Écriture d’un fournisseur de base de données-EF Core
description: Informations sur l’écriture d’un nouveau fournisseur de Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/writing-a-provider
ms.openlocfilehash: 898a7a50211e68400ee012daa542bed14bdcec1c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430493"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="6f74d-103">Écriture d’un fournisseur de base de données</span><span class="sxs-lookup"><span data-stu-id="6f74d-103">Writing a Database Provider</span></span>

<span data-ttu-id="6f74d-104">Pour plus d’informations sur l’écriture d’un fournisseur de base de données Entity Framework Core, consultez. pour [ce faire, vous devez écrire un fournisseur EF Core](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) par [Arthur Vickers](https://github.com/ajcvickers).</span><span class="sxs-lookup"><span data-stu-id="6f74d-104">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="6f74d-105">Ces publications n’ont pas été mises à jour depuis EF Core 1,1 et des modifications significatives ont été apportées depuis ce moment.</span><span class="sxs-lookup"><span data-stu-id="6f74d-105">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time.</span></span>  
<span data-ttu-id="6f74d-106">Le [problème 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) est le suivi des mises à jour de cette documentation.</span><span class="sxs-lookup"><span data-stu-id="6f74d-106">[Issue 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="6f74d-107">La base de code EF Core est open source et contient plusieurs fournisseurs de bases de données qui peuvent être utilisés comme référence.</span><span class="sxs-lookup"><span data-stu-id="6f74d-107">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="6f74d-108">Vous pouvez trouver le code source à l’adresse <https://github.com/dotnet/efcore> .</span><span class="sxs-lookup"><span data-stu-id="6f74d-108">You can find the source code at <https://github.com/dotnet/efcore>.</span></span> <span data-ttu-id="6f74d-109">Il peut également être utile d’examiner le code pour les fournisseurs tiers couramment utilisés, tels que [npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)et [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span><span class="sxs-lookup"><span data-stu-id="6f74d-109">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="6f74d-110">En particulier, ces projets sont configurés pour étendre et exécuter des tests fonctionnels que nous publions sur NuGet.</span><span class="sxs-lookup"><span data-stu-id="6f74d-110">In particular, these projects are setup to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="6f74d-111">Ce type d’installation est fortement recommandé.</span><span class="sxs-lookup"><span data-stu-id="6f74d-111">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="6f74d-112">Tenir à jour les modifications apportées au fournisseur</span><span class="sxs-lookup"><span data-stu-id="6f74d-112">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="6f74d-113">À compter du travail après la version 2,1, nous avons créé un [Journal des modifications](xref:core/providers/provider-log) qui peut nécessiter des modifications correspondantes dans le code du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="6f74d-113">Starting with work after the 2.1 release, we have created a [log of changes](xref:core/providers/provider-log) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="6f74d-114">Cela est destiné à faciliter la mise à jour d’un fournisseur existant pour qu’il fonctionne avec une nouvelle version de EF Core.</span><span class="sxs-lookup"><span data-stu-id="6f74d-114">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="6f74d-115">Avant le 2,1, nous avons utilisé [`providers-beware`](https://github.com/dotnet/efcore/labels/providers-beware) les [`providers-fyi`](https://github.com/dotnet/efcore/labels/providers-fyi) étiquettes et sur nos problèmes GitHub et les demandes de tirage (pull requests) à des fins similaires.</span><span class="sxs-lookup"><span data-stu-id="6f74d-115">Prior to 2.1, we used the [`providers-beware`](https://github.com/dotnet/efcore/labels/providers-beware) and [`providers-fyi`](https://github.com/dotnet/efcore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="6f74d-116">Nous continiue utiliser ces étiquettes sur les problèmes pour donner une indication que les éléments de travail d’une version donnée peuvent également nécessiter un travail dans les fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="6f74d-116">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="6f74d-117">Une `providers-beware` étiquette signifie généralement que l’implémentation d’un élément de travail peut arrêter des fournisseurs, tandis qu’une `providers-fyi` étiquette signifie généralement que les fournisseurs ne seront pas rompus, mais le code devra peut-être être modifié malgré tout, par exemple, pour activer les nouvelles fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="6f74d-117">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="6f74d-118">Attribution d’un nom suggéré aux fournisseurs tiers</span><span class="sxs-lookup"><span data-stu-id="6f74d-118">Suggested naming of third party providers</span></span>

<span data-ttu-id="6f74d-119">Nous vous suggérons d’utiliser le nom suivant pour les packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="6f74d-119">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="6f74d-120">Cela est cohérent avec les noms des packages fournis par l’équipe EF Core.</span><span class="sxs-lookup"><span data-stu-id="6f74d-120">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="6f74d-121">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6f74d-121">For example:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
