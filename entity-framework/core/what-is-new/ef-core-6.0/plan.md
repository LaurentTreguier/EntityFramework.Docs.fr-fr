---
title: Planifier Entity Framework Core 6,0
description: Thèmes et fonctionnalités prévus pour EF Core 6,0
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: 612461bc6ad30778baa5c6d10dda5cabac91dcb2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129783"
---
# <a name="plan-for-entity-framework-core-60"></a><span data-ttu-id="9b93e-103">Planifier Entity Framework Core 6,0</span><span class="sxs-lookup"><span data-stu-id="9b93e-103">Plan for Entity Framework Core 6.0</span></span>

<span data-ttu-id="9b93e-104">Comme décrit dans le [processus de planification](xref:core/what-is-new/release-planning), nous avons rassemblé Entity Framework Core l’entrée des parties prenantes dans un plan pour la version 6,0 (EF Core).</span><span class="sxs-lookup"><span data-stu-id="9b93e-104">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a plan for the Entity Framework Core (EF Core) 6.0 release.</span></span>

<span data-ttu-id="9b93e-105">Contrairement aux versions précédentes, ce plan ne tente pas de couvrir tout le travail pour la version 6,0.</span><span class="sxs-lookup"><span data-stu-id="9b93e-105">Unlike previous releases, this plan does not attempt to cover all work for the 6.0 release.</span></span> <span data-ttu-id="9b93e-106">Au lieu de cela, il indique où et comment nous avons l’intention d’investir dans cette version, mais avec une certaine flexibilité pour ajuster l’étendue ou insérer un nouveau travail au fur et à mesure que nous recueillons des commentaires et que vous apprenez à travailler sur la version.</span><span class="sxs-lookup"><span data-stu-id="9b93e-106">Instead, it indicates where and how we intend to invest in this release, but with flexibility to adjust scope or pull in new work as we gather feedback and learn while working on the release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9b93e-107">Ce plan n’est pas un engagement.</span><span class="sxs-lookup"><span data-stu-id="9b93e-107">This plan is not a commitment.</span></span> <span data-ttu-id="9b93e-108">C’est un point de départ qui évoluera à mesure que nous en apprendrons davantage.</span><span class="sxs-lookup"><span data-stu-id="9b93e-108">It is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="9b93e-109">Certains éléments qui ne sont pas actuellement planifiés pour 6,0 peuvent être extraits.</span><span class="sxs-lookup"><span data-stu-id="9b93e-109">Some things not currently planned for 6.0 may get pulled in.</span></span> <span data-ttu-id="9b93e-110">Certains éléments actuellement planifiés pour 6,0 peuvent devenir punted.</span><span class="sxs-lookup"><span data-stu-id="9b93e-110">Some things currently planned for 6.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="9b93e-111">Informations générales</span><span class="sxs-lookup"><span data-stu-id="9b93e-111">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="9b93e-112">Numéro de version et date de publication</span><span class="sxs-lookup"><span data-stu-id="9b93e-112">Version number and release date</span></span>

<span data-ttu-id="9b93e-113">EF Core 6,0 est la prochaine version après EF Core 5,0 et est actuellement planifiée en version finale en novembre 2021 en même temps que .NET 6.</span><span class="sxs-lookup"><span data-stu-id="9b93e-113">EF Core 6.0 is the next release after EF Core 5.0 and is currently scheduled for release in November 2021 at the same time as .NET 6.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="9b93e-114">Plateformes prises en charge</span><span class="sxs-lookup"><span data-stu-id="9b93e-114">Supported platforms</span></span>

<span data-ttu-id="9b93e-115">EF Core 6,0 cible actuellement .NET 5.</span><span class="sxs-lookup"><span data-stu-id="9b93e-115">EF Core 6.0 currently targets .NET 5.</span></span> <span data-ttu-id="9b93e-116">Cela sera probablement mis à jour vers .NET 6 comme nous l’avons fait près de la version.</span><span class="sxs-lookup"><span data-stu-id="9b93e-116">This will likely be updated to .NET 6 as we near the release.</span></span> <span data-ttu-id="9b93e-117">EF Core 6,0 ne cible aucune version de .NET Standard ; Pour plus d’informations, consultez [l’avenir de .NET standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span><span class="sxs-lookup"><span data-stu-id="9b93e-117">EF Core 6.0 does not target any .NET Standard version; for more information see [the future of .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span></span>

<span data-ttu-id="9b93e-118">EF Core 6,0 ne s’exécutera pas sur .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="9b93e-118">EF Core 6.0 will not run on .NET Framework.</span></span>

<span data-ttu-id="9b93e-119">EF Core 6,0 s’alignera sur .NET 6 comme [version de support à long terme (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="9b93e-119">EF Core 6.0 will align with .NET 6 as a [long-term support (LTS) release](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="9b93e-120">Changements cassants</span><span class="sxs-lookup"><span data-stu-id="9b93e-120">Breaking changes</span></span>

<span data-ttu-id="9b93e-121">EF Core 6,0 contiendra un petit nombre de [modifications avec rupture](xref:core/what-is-new/ef-core-6.0/breaking-changes) à mesure que nous continuons à faire évoluer à la fois EF Core et la plateforme .net.</span><span class="sxs-lookup"><span data-stu-id="9b93e-121">EF Core 6.0 will contain a small number of [breaking changes](xref:core/what-is-new/ef-core-6.0/breaking-changes) as we continue to evolve both EF Core and the .NET platform.</span></span> <span data-ttu-id="9b93e-122">Notre objectif est de permettre la mise à jour sans interruption de la grande majorité des applications.</span><span class="sxs-lookup"><span data-stu-id="9b93e-122">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

## <a name="themes"></a><span data-ttu-id="9b93e-123">Thèmes</span><span class="sxs-lookup"><span data-stu-id="9b93e-123">Themes</span></span>

<span data-ttu-id="9b93e-124">Les zones suivantes constituent la base des investissements importants en EF Core 6,0.</span><span class="sxs-lookup"><span data-stu-id="9b93e-124">The following areas will form the basis for the large investments in EF Core 6.0.</span></span>

## <a name="highly-requested-features"></a><span data-ttu-id="9b93e-125">Fonctionnalités hautement demandées</span><span class="sxs-lookup"><span data-stu-id="9b93e-125">Highly requested features</span></span>

<span data-ttu-id="9b93e-126">Comme toujours, une entrée majeure dans le [processus de planification](xref:core/what-is-new/release-planning) provient du [vote ( 👍 ) des fonctionnalités sur GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="9b93e-126">As always, a major input into the [planning process](xref:core/what-is-new/release-planning) comes from the [voting (👍) for features on GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span></span> <span data-ttu-id="9b93e-127">Pour EF Core 6,0, nous prévoyons de travailler sur les fonctionnalités hautement demandées suivantes :</span><span class="sxs-lookup"><span data-stu-id="9b93e-127">For EF Core 6.0 we plan to work on the following highly requested features:</span></span>

### <a name="sql-server-temporal-tables"></a><span data-ttu-id="9b93e-128">SQL Server les tables temporelles</span><span class="sxs-lookup"><span data-stu-id="9b93e-128">SQL Server temporal tables</span></span>

<span data-ttu-id="9b93e-129">Suivi par [#4693](https://github.com/dotnet/efcore/issues/4693)</span><span class="sxs-lookup"><span data-stu-id="9b93e-129">Tracked by [#4693](https://github.com/dotnet/efcore/issues/4693)</span></span>

<span data-ttu-id="9b93e-130">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-130">Status: Not started</span></span>

<span data-ttu-id="9b93e-131">Taille de T-shirt : grande</span><span class="sxs-lookup"><span data-stu-id="9b93e-131">T-shirt size: Large</span></span>

<span data-ttu-id="9b93e-132">Les tables temporelles prennent en charge les requêtes pour les données stockées dans la table à _tout_ moment, par opposition aux données les plus récentes stockées comme c’est le cas pour les tables normales.</span><span class="sxs-lookup"><span data-stu-id="9b93e-132">Temporal tables support queries for data stored in the table at _any point in time_, as opposed to only the most recent data stored as is the case for normal tables.</span></span> <span data-ttu-id="9b93e-133">EF Core 6,0 autorise la création de tables temporelles via des migrations, ainsi que l’autorisation de l’accès aux données via des requêtes LINQ.</span><span class="sxs-lookup"><span data-stu-id="9b93e-133">EF Core 6.0 will allow temporal tables to be created via Migrations, as well as allowing access to the data through LINQ queries.</span></span>

<span data-ttu-id="9b93e-134">Ce travail est initialement étendu comme [décrit dans le problème](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974).</span><span class="sxs-lookup"><span data-stu-id="9b93e-134">This work is initially scoped as [described on the issue](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974).</span></span> <span data-ttu-id="9b93e-135">Nous pouvons intégrer un support supplémentaire en fonction des commentaires au cours de la version.</span><span class="sxs-lookup"><span data-stu-id="9b93e-135">We may pull in additional support based on feedback during the release.</span></span>

### <a name="json-columns"></a><span data-ttu-id="9b93e-136">Colonnes JSON</span><span class="sxs-lookup"><span data-stu-id="9b93e-136">JSON columns</span></span>

<span data-ttu-id="9b93e-137">Suivi par [#4021](https://github.com/dotnet/efcore/issues/4021)</span><span class="sxs-lookup"><span data-stu-id="9b93e-137">Tracked by [#4021](https://github.com/dotnet/efcore/issues/4021)</span></span>

<span data-ttu-id="9b93e-138">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-138">Status: Not started</span></span>

<span data-ttu-id="9b93e-139">Taille de T-shirt : moyenne</span><span class="sxs-lookup"><span data-stu-id="9b93e-139">T-shirt size: Medium</span></span>

<span data-ttu-id="9b93e-140">Cette fonctionnalité introduira un mécanisme commun et des modèles pour la prise en charge de JSON qui peuvent être implémentés par n’importe quel fournisseur de base de données.</span><span class="sxs-lookup"><span data-stu-id="9b93e-140">This feature will introduce a common mechanism and patterns for JSON support that can be implemented by any database provider.</span></span> <span data-ttu-id="9b93e-141">Nous travaillons avec la communauté pour aligner les implémentations existantes pour [npgsql](https://github.com/npgsql/efcore.pg) et [pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), ainsi que pour ajouter la prise en charge de SQL Server et SQLite.</span><span class="sxs-lookup"><span data-stu-id="9b93e-141">We will work with the community to align existing implementations for [Npgsql](https://github.com/npgsql/efcore.pg) and [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and also add support for SQL Server and SQLite.</span></span>

### <a name="columnattributeorder"></a><span data-ttu-id="9b93e-142">ColumnAttribute. Order</span><span class="sxs-lookup"><span data-stu-id="9b93e-142">ColumnAttribute.Order</span></span>

<span data-ttu-id="9b93e-143">Suivi par [#10059](https://github.com/dotnet/efcore/issues/10059)</span><span class="sxs-lookup"><span data-stu-id="9b93e-143">Tracked by [#10059](https://github.com/dotnet/efcore/issues/10059)</span></span>

<span data-ttu-id="9b93e-144">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-144">Status: Not started</span></span>

<span data-ttu-id="9b93e-145">Taille de T-shirt : petite</span><span class="sxs-lookup"><span data-stu-id="9b93e-145">T-shirt size: Small</span></span>

<span data-ttu-id="9b93e-146">Cette fonctionnalité permet un classement arbitraire des colonnes lors de **la création d’une table** avec des migrations ou `EnsureCreated` .</span><span class="sxs-lookup"><span data-stu-id="9b93e-146">This feature will allow arbitrary ordering of columns when **creating a table** with Migrations or `EnsureCreated`.</span></span> <span data-ttu-id="9b93e-147">Notez que la modification de l’ordre des colonnes dans les tables existantes nécessite la reconstruction de la table, et ce n’est pas un point que nous prévoyons de prendre en charge dans une version EF Core.</span><span class="sxs-lookup"><span data-stu-id="9b93e-147">Note that changing the order of columns in an existing tables requires that the table be rebuilt, and this is not something that we plan to support in any EF Core release.</span></span>

## <a name="performance"></a><span data-ttu-id="9b93e-148">Performances</span><span class="sxs-lookup"><span data-stu-id="9b93e-148">Performance</span></span>

<span data-ttu-id="9b93e-149">Bien que EF Core soit généralement plus rapide que EF6, il existe toujours des zones où des améliorations significatives des performances sont possibles.</span><span class="sxs-lookup"><span data-stu-id="9b93e-149">While EF Core is generally faster than EF6, there are still areas where significant improvements in performance are possible.</span></span> <span data-ttu-id="9b93e-150">Nous prévoyons de traiter plusieurs de ces domaines dans EF Core 6,0, tout en améliorant également notre infrastructure de performances et vos tests.</span><span class="sxs-lookup"><span data-stu-id="9b93e-150">We plan to tackle several of these areas in EF Core 6.0, while also improving our perf infrastructure and testing.</span></span>

<span data-ttu-id="9b93e-151">Ce thème implique un grand nombre d’investigations itératives, qui indiquent où nous mettons en avant les ressources.</span><span class="sxs-lookup"><span data-stu-id="9b93e-151">This theme will involve a lot of iterative investigation, which will inform where we focus resources.</span></span> <span data-ttu-id="9b93e-152">Nous prévoyons de commencer par :</span><span class="sxs-lookup"><span data-stu-id="9b93e-152">We plan to begin with:</span></span>

### <a name="performance-infrastructure-and-new-tests"></a><span data-ttu-id="9b93e-153">Infrastructure de performances et nouveaux tests</span><span class="sxs-lookup"><span data-stu-id="9b93e-153">Performance infrastructure and new tests</span></span>

<span data-ttu-id="9b93e-154">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-154">Status: Not started</span></span>

<span data-ttu-id="9b93e-155">Taille de T-shirt : moyenne</span><span class="sxs-lookup"><span data-stu-id="9b93e-155">T-shirt size: Medium</span></span>

<span data-ttu-id="9b93e-156">Le code base EF Core contient déjà un ensemble de tests de performances qui sont exécutés tous les jours.</span><span class="sxs-lookup"><span data-stu-id="9b93e-156">The EF Core codebase already contains a set of performance benchmarks that are executed every day.</span></span> <span data-ttu-id="9b93e-157">Pour 6,0, nous prévoyons d’améliorer l’infrastructure pour ces tests et d’ajouter de nouveaux tests.</span><span class="sxs-lookup"><span data-stu-id="9b93e-157">For 6.0, we plan to improve the infrastructure for these tests as well as adding new tests.</span></span> <span data-ttu-id="9b93e-158">Nous allons également profiler les scénarios de performances de la principale et corriger tout fruit faiblement trouvé.</span><span class="sxs-lookup"><span data-stu-id="9b93e-158">We will also profile mainline perf scenarios and fix any low-hanging fruit found.</span></span>

### <a name="compiled-models"></a><span data-ttu-id="9b93e-159">Modèles compilés</span><span class="sxs-lookup"><span data-stu-id="9b93e-159">Compiled models</span></span>

<span data-ttu-id="9b93e-160">Suivi par [#1906](https://github.com/dotnet/efcore/issues/1906)</span><span class="sxs-lookup"><span data-stu-id="9b93e-160">Tracked by [#1906](https://github.com/dotnet/efcore/issues/1906)</span></span>

<span data-ttu-id="9b93e-161">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-161">Status: Not started</span></span>

<span data-ttu-id="9b93e-162">Taille de T-shirt : X-large</span><span class="sxs-lookup"><span data-stu-id="9b93e-162">T-shirt size: X-Large</span></span>

<span data-ttu-id="9b93e-163">Les modèles compilés autorisent la génération d’une forme compilée du modèle EF.</span><span class="sxs-lookup"><span data-stu-id="9b93e-163">Compiled models will allow the generation of a compiled form of the EF model.</span></span> <span data-ttu-id="9b93e-164">Cela permet d’améliorer les performances de démarrage, ainsi que de meilleures performances lors de l’accès au modèle.</span><span class="sxs-lookup"><span data-stu-id="9b93e-164">This will provide both better startup performance, as well as generally better performance when accessing the model.</span></span>

### <a name="techempower-fortunes"></a><span data-ttu-id="9b93e-165">TechEmpower fortune</span><span class="sxs-lookup"><span data-stu-id="9b93e-165">TechEmpower Fortunes</span></span>

<span data-ttu-id="9b93e-166">Suivi par [#23611](https://github.com/dotnet/efcore/issues/23611)</span><span class="sxs-lookup"><span data-stu-id="9b93e-166">Tracked by [#23611](https://github.com/dotnet/efcore/issues/23611)</span></span>

<span data-ttu-id="9b93e-167">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-167">Status: Not started</span></span>

<span data-ttu-id="9b93e-168">Taille de T-shirt : X-large</span><span class="sxs-lookup"><span data-stu-id="9b93e-168">T-shirt size: X-Large</span></span>

<span data-ttu-id="9b93e-169">Nous avons exécuté les [bancs d’essai TechEmpower](https://www.techempower.com/benchmarks/) standard sur .net sur une base de données PostgreSQL depuis plusieurs années.</span><span class="sxs-lookup"><span data-stu-id="9b93e-169">We have been running the industry standard [TechEmpower benchmarks](https://www.techempower.com/benchmarks/) on .NET against a PostgreSQL database for several years.</span></span> <span data-ttu-id="9b93e-170">Le [test d’évaluation de fortune](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) est particulièrement pertinent pour les scénarios EF.</span><span class="sxs-lookup"><span data-stu-id="9b93e-170">The [Fortunes benchmark](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) is particularly relevant to EF scenarios.</span></span> <span data-ttu-id="9b93e-171">Nous avons plusieurs variantes de ce test, notamment :</span><span class="sxs-lookup"><span data-stu-id="9b93e-171">We have multiple variations of this benchmark, including:</span></span>

- <span data-ttu-id="9b93e-172">Implémentation de qui utilise directement ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="9b93e-172">An implementation that uses ADO.NET directly.</span></span> <span data-ttu-id="9b93e-173">Il s’agit de l’implémentation la plus rapide des trois répertoriés ici.</span><span class="sxs-lookup"><span data-stu-id="9b93e-173">This is the fastest implementation of the three listed here.</span></span>
- <span data-ttu-id="9b93e-174">Implémentation de qui utilise [dapper](https://www.nuget.org/packages/Dapper/).</span><span class="sxs-lookup"><span data-stu-id="9b93e-174">An implementation that uses [Dapper](https://www.nuget.org/packages/Dapper/).</span></span> <span data-ttu-id="9b93e-175">Cela est plus lent que l’utilisation directe de ADO.NET, mais toujours rapide.</span><span class="sxs-lookup"><span data-stu-id="9b93e-175">This is slower than using ADO.NET directly, but still fast.</span></span>
- <span data-ttu-id="9b93e-176">Implémentation de qui utilise EF Core.</span><span class="sxs-lookup"><span data-stu-id="9b93e-176">An implementation that uses EF Core.</span></span> <span data-ttu-id="9b93e-177">Il s’agit actuellement de l’implémentation la plus lente des trois.</span><span class="sxs-lookup"><span data-stu-id="9b93e-177">This is currently the slowest implementation of the three.</span></span>

<span data-ttu-id="9b93e-178">L’objectif de EF Core 6,0 est de faire en sorte que les performances de la EF Core correspondent à celles de dapper sur le banc d’essai TechEmpower fortune.</span><span class="sxs-lookup"><span data-stu-id="9b93e-178">The goal for EF Core 6.0 is to get the EF Core performance to match that of Dapper on the TechEmpower Fortunes benchmark.</span></span> <span data-ttu-id="9b93e-179">(Il s’agit d’un défi important, mais nous allons faire de notre mieux pour être aussi proche que possible.)</span><span class="sxs-lookup"><span data-stu-id="9b93e-179">(This is a significant challenge but we will do our best to get as close as we can.)</span></span>

### <a name="linkeraot"></a><span data-ttu-id="9b93e-180">Éditeur de liens/AOA</span><span class="sxs-lookup"><span data-stu-id="9b93e-180">Linker/AOT</span></span>

<span data-ttu-id="9b93e-181">Suivi par [#10963](https://github.com/dotnet/efcore/issues/10963)</span><span class="sxs-lookup"><span data-stu-id="9b93e-181">Tracked by [#10963](https://github.com/dotnet/efcore/issues/10963)</span></span>

<span data-ttu-id="9b93e-182">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-182">Status: Not started</span></span>

<span data-ttu-id="9b93e-183">Taille de T-shirt : moyenne</span><span class="sxs-lookup"><span data-stu-id="9b93e-183">T-shirt size: Medium</span></span>

<span data-ttu-id="9b93e-184">EF Core effectue une grande quantité de génération de code d’exécution.</span><span class="sxs-lookup"><span data-stu-id="9b93e-184">EF Core performs large amounts of runtime code generation.</span></span> <span data-ttu-id="9b93e-185">Cela est difficile pour les modèles d’application qui dépendent du tremblement de l’arborescence de l’éditeur de liens, tels que Xamarin et éblouissant, et les plateformes qui n’autorisent pas la compilation dynamique, comme iOS.</span><span class="sxs-lookup"><span data-stu-id="9b93e-185">This is challenging for app models that depend on linker tree shaking, such as Xamarin and Blazor, and platforms that don't allow dynamic compilation, such as iOS.</span></span> <span data-ttu-id="9b93e-186">Nous continuons à examiner cet espace dans le cadre de EF Core 6,0 et à apporter des améliorations ciblées.</span><span class="sxs-lookup"><span data-stu-id="9b93e-186">We will continue investigating in this space as part of EF Core 6.0 and make targeted improvements as we can.</span></span> <span data-ttu-id="9b93e-187">Toutefois, nous ne pensons pas que l’intervalle est entièrement proche dans le laps de temps 6,0.</span><span class="sxs-lookup"><span data-stu-id="9b93e-187">However, we do not expect to fully close the gap in the 6.0 time frame.</span></span>

## <a name="migrations-and-deployment"></a><span data-ttu-id="9b93e-188">Migrations et déploiement</span><span class="sxs-lookup"><span data-stu-id="9b93e-188">Migrations and deployment</span></span>

<span data-ttu-id="9b93e-189">Après les [investigations effectuées pour EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), nous prévoyons d’introduire une prise en charge améliorée pour la gestion des migrations et le déploiement de bases de données.</span><span class="sxs-lookup"><span data-stu-id="9b93e-189">Following on from the [investigations done for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), we plan to introduce improved support for managing migrations and deploying databases.</span></span> <span data-ttu-id="9b93e-190">Cela comprend deux domaines majeurs :</span><span class="sxs-lookup"><span data-stu-id="9b93e-190">This includes two major areas:</span></span>

### <a name="migrations-bundles"></a><span data-ttu-id="9b93e-191">Offres groupées de migrations</span><span class="sxs-lookup"><span data-stu-id="9b93e-191">Migrations bundles</span></span>

<span data-ttu-id="9b93e-192">Suivi par [#19693](https://github.com/dotnet/efcore/issues/19693)</span><span class="sxs-lookup"><span data-stu-id="9b93e-192">Tracked by [#19693](https://github.com/dotnet/efcore/issues/19693)</span></span>

<span data-ttu-id="9b93e-193">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-193">Status: Not started</span></span>

<span data-ttu-id="9b93e-194">Taille de T-shirt : moyenne</span><span class="sxs-lookup"><span data-stu-id="9b93e-194">T-shirt size: Medium</span></span>

<span data-ttu-id="9b93e-195">Une offre groupée de migrations est un exécutable autonome qui applique des migrations à une base de données de production.</span><span class="sxs-lookup"><span data-stu-id="9b93e-195">A migrations bundle is a self-contained executable that applies migrations to a production database.</span></span> <span data-ttu-id="9b93e-196">Le comportement est le même `dotnet ef database update` , mais il est préférable de faire en sorte que le déploiement du SSH/de l’ancrage/PowerShell soit beaucoup plus facile, car tout ce qui est nécessaire est contenu dans le seul exécutable.</span><span class="sxs-lookup"><span data-stu-id="9b93e-196">The behavior will match `dotnet ef database update`, but should make SSH/Docker/PowerShell deployment much easier, since everything needed is contained in the single executable.</span></span>

### <a name="managing-migrations"></a><span data-ttu-id="9b93e-197">Gestion des migrations</span><span class="sxs-lookup"><span data-stu-id="9b93e-197">Managing migrations</span></span>

<span data-ttu-id="9b93e-198">Suivi par [#22945](https://github.com/dotnet/efcore/issues/22945)</span><span class="sxs-lookup"><span data-stu-id="9b93e-198">Tracked by [#22945](https://github.com/dotnet/efcore/issues/22945)</span></span>

<span data-ttu-id="9b93e-199">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-199">Status: Not started</span></span>

<span data-ttu-id="9b93e-200">Taille de T-shirt : grande</span><span class="sxs-lookup"><span data-stu-id="9b93e-200">T-shirt size: Large</span></span>

<span data-ttu-id="9b93e-201">Le nombre de migrations créées pour une application peut croître pour devenir un fardeau.</span><span class="sxs-lookup"><span data-stu-id="9b93e-201">The number of migrations created for an application can grow to become a burden.</span></span> <span data-ttu-id="9b93e-202">En outre, ces migrations sont souvent déployées avec l’application, même si cela n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="9b93e-202">In addition, these migrations are frequently deployed with the application even when this is not needed.</span></span> <span data-ttu-id="9b93e-203">Dans EF Core 6,0, nous prévoyons d’améliorer cela grâce à de meilleurs outils et à la gestion de projet/assembly.</span><span class="sxs-lookup"><span data-stu-id="9b93e-203">In EF Core 6.0, we plan to improve this through better tooling and project/assembly management.</span></span> <span data-ttu-id="9b93e-204">Deux problèmes spécifiques que nous envisageons de traiter sont [l’écrasement de nombreuses migrations](https://github.com/dotnet/efcore/issues/2174) et [la régénération d’un instantané de modèle propre](https://github.com/dotnet/efcore/issues/18557).</span><span class="sxs-lookup"><span data-stu-id="9b93e-204">Two specific issues we plan to address are [squash many migrations into one](https://github.com/dotnet/efcore/issues/2174) and [regenerate a clean model snapshot](https://github.com/dotnet/efcore/issues/18557).</span></span>

## <a name="improve-existing-features-and-fix-bugs"></a><span data-ttu-id="9b93e-205">Améliorer les fonctionnalités existantes et corriger les bogues</span><span class="sxs-lookup"><span data-stu-id="9b93e-205">Improve existing features and fix bugs</span></span>

<span data-ttu-id="9b93e-206">Tout [problème ou bogue affecté à l’étape de jalon 6.0.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) est actuellement planifié pour cette version.</span><span class="sxs-lookup"><span data-stu-id="9b93e-206">Any [issue or bug assigned to the 6.0.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) is currently planned for this release.</span></span> <span data-ttu-id="9b93e-207">Cela comprend de nombreuses petites améliorations et des correctifs de bogues.</span><span class="sxs-lookup"><span data-stu-id="9b93e-207">This includes many small enhancements and bug fixes.</span></span>

### <a name="ef6-query-parity"></a><span data-ttu-id="9b93e-208">Parité de requête EF6</span><span class="sxs-lookup"><span data-stu-id="9b93e-208">EF6 query parity</span></span>

<span data-ttu-id="9b93e-209">Suivi par des [problèmes étiquetés avec « EF6-Parity » et dans le jalon 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span><span class="sxs-lookup"><span data-stu-id="9b93e-209">Tracked by [issues labeled with 'ef6-parity' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span></span>

<span data-ttu-id="9b93e-210">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-210">Status: Not started</span></span>

<span data-ttu-id="9b93e-211">Taille de T-shirt : grande</span><span class="sxs-lookup"><span data-stu-id="9b93e-211">T-shirt size: Large</span></span>

<span data-ttu-id="9b93e-212">EF Core 5,0 prend en charge la plupart des modèles de requête pris en charge par EF6, en plus des modèles non pris en charge dans EF6.</span><span class="sxs-lookup"><span data-stu-id="9b93e-212">EF Core 5.0 supports most query patterns supported by EF6, in addition to patterns not supported in EF6.</span></span> <span data-ttu-id="9b93e-213">Pour EF Core 6,0, nous prévoyons de fermer l’intervalle et de prendre en charge EF Core interroge un sur-ensemble réel des requêtes EF6 prises en charge.</span><span class="sxs-lookup"><span data-stu-id="9b93e-213">For EF Core 6.0, we plan to close the gap and make supported EF Core queries a true superset of supported EF6 queries.</span></span> <span data-ttu-id="9b93e-214">Cette opération est motivée par l’examen des lacunes, mais elle comprend déjà des problèmes de type GroupBy, tels que [translate GroupBy suivi de FirstOrDefault](https://github.com/dotnet/efcore/issues/12088)et des requêtes SQL brutes pour les types [primitifs](https://github.com/dotnet/efcore/issues/11624) et non [mappés](https://github.com/dotnet/efcore/issues/10753) .</span><span class="sxs-lookup"><span data-stu-id="9b93e-214">This will be driven by investigation of the gaps, but already includes GroupBy issues such as [translate GroupBy followed by FirstOrDefault](https://github.com/dotnet/efcore/issues/12088), and raw SQL queries for [primitive](https://github.com/dotnet/efcore/issues/11624) and [unmapped](https://github.com/dotnet/efcore/issues/10753) types.</span></span>  

### <a name="value-objects"></a><span data-ttu-id="9b93e-215">Objets de valeur</span><span class="sxs-lookup"><span data-stu-id="9b93e-215">Value objects</span></span>

<span data-ttu-id="9b93e-216">Suivi par [#9906](https://github.com/dotnet/efcore/issues/9906)</span><span class="sxs-lookup"><span data-stu-id="9b93e-216">Tracked by [#9906](https://github.com/dotnet/efcore/issues/9906)</span></span>

<span data-ttu-id="9b93e-217">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-217">Status: Not started</span></span>

<span data-ttu-id="9b93e-218">Taille de T-shirt : moyenne</span><span class="sxs-lookup"><span data-stu-id="9b93e-218">T-shirt size: Medium</span></span>

<span data-ttu-id="9b93e-219">C’était auparavant la vue d’équipe qui possédait des entités, destinées à la [prise en charge](https://www.martinfowler.com/bliki/DDD_Aggregate.html)de l’agrégation, serait également une approximation raisonnable des [objets de valeur](https://www.martinfowler.com/bliki/ValueObject.html).</span><span class="sxs-lookup"><span data-stu-id="9b93e-219">It was previously the team view that owned entities, intended for [aggregate support](https://www.martinfowler.com/bliki/DDD_Aggregate.html), would also be a reasonable approximation to [value objects](https://www.martinfowler.com/bliki/ValueObject.html).</span></span> <span data-ttu-id="9b93e-220">L’expérience a montré que cela ne serait pas le cas.</span><span class="sxs-lookup"><span data-stu-id="9b93e-220">Experience has shown this not to be the case.</span></span> <span data-ttu-id="9b93e-221">Par conséquent, dans EF Core 6,0, nous prévoyons d’introduire une meilleure expérience axée sur les besoins des objets de valeur dans la conception pilotée par le domaine.</span><span class="sxs-lookup"><span data-stu-id="9b93e-221">Therefore, in EF Core 6.0 we plan to introduce a better experience focused on the needs of value objects in domain-driven design.</span></span> <span data-ttu-id="9b93e-222">Cette approche est basée sur les convertisseurs de valeur plutôt que sur les entités détenues.</span><span class="sxs-lookup"><span data-stu-id="9b93e-222">This approach will be based on value converters rather than owned entities.</span></span>

<span data-ttu-id="9b93e-223">Ce travail est initialement étendu pour autoriser [les convertisseurs de valeurs qui sont mappés à plusieurs colonnes](https://github.com/dotnet/efcore/issues/13947).</span><span class="sxs-lookup"><span data-stu-id="9b93e-223">This work is initially scoped to allow [value converters which map to multiple columns](https://github.com/dotnet/efcore/issues/13947).</span></span> <span data-ttu-id="9b93e-224">Nous pouvons intégrer un support supplémentaire en fonction des commentaires au cours de la version.</span><span class="sxs-lookup"><span data-stu-id="9b93e-224">We may pull in additional support based on feedback during the release.</span></span>

### <a name="cosmos-database-provider"></a><span data-ttu-id="9b93e-225">Fournisseur de base de données Cosmos</span><span class="sxs-lookup"><span data-stu-id="9b93e-225">Cosmos database provider</span></span>

<span data-ttu-id="9b93e-226">Suivi par des [problèmes étiquetés avec « Area-Cosmos » et dans le jalon 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span><span class="sxs-lookup"><span data-stu-id="9b93e-226">Tracked by [issues labeled with 'area-cosmos' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span></span>

<span data-ttu-id="9b93e-227">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-227">Status: Not started</span></span>

<span data-ttu-id="9b93e-228">Taille de T-shirt : grande</span><span class="sxs-lookup"><span data-stu-id="9b93e-228">T-shirt size: Large</span></span>

<span data-ttu-id="9b93e-229">Nous recueillons activement des commentaires sur les améliorations apportées au fournisseur Cosmos dans EF Core 6,0.</span><span class="sxs-lookup"><span data-stu-id="9b93e-229">We are actively gathering feedback on which improvements to make to the Cosmos provider in EF Core 6.0.</span></span> <span data-ttu-id="9b93e-230">Nous mettrons à jour ce document au fur et à mesure que nous en apprendrons davantage.</span><span class="sxs-lookup"><span data-stu-id="9b93e-230">We will update this document as we learn more.</span></span> <span data-ttu-id="9b93e-231">Pour l’instant, assurez-vous de voter ( 👍 ) pour les fonctionnalités Cosmos dont vous avez besoin.</span><span class="sxs-lookup"><span data-stu-id="9b93e-231">For now, please make sure to vote (👍) for the Cosmos features that you need.</span></span>

### <a name="expose-model-building-conventions-to-applications"></a><span data-ttu-id="9b93e-232">Exposer les conventions de génération de modèle aux applications</span><span class="sxs-lookup"><span data-stu-id="9b93e-232">Expose model building conventions to applications</span></span>

<span data-ttu-id="9b93e-233">Suivi par [#214](https://github.com/dotnet/efcore/issues/214)</span><span class="sxs-lookup"><span data-stu-id="9b93e-233">Tracked by [#214](https://github.com/dotnet/efcore/issues/214)</span></span>

<span data-ttu-id="9b93e-234">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-234">Status: Not started</span></span>

<span data-ttu-id="9b93e-235">Taille de T-shirt : moyenne</span><span class="sxs-lookup"><span data-stu-id="9b93e-235">T-shirt size: medium</span></span>

<span data-ttu-id="9b93e-236">EF Core utilise un ensemble de conventions pour créer un modèle à partir de types .NET.</span><span class="sxs-lookup"><span data-stu-id="9b93e-236">EF Core uses a set of conventions for building a model from .NET types.</span></span> <span data-ttu-id="9b93e-237">Ces conventions sont actuellement contrôlées par le fournisseur de base de données.</span><span class="sxs-lookup"><span data-stu-id="9b93e-237">These conventions are currently controlled by the database provider.</span></span> <span data-ttu-id="9b93e-238">Dans EF Core 6,0, nous avons l’intention d’autoriser les applications à se raccorder à et à modifier ces conventions.</span><span class="sxs-lookup"><span data-stu-id="9b93e-238">In EF Core 6.0, we intend to allow applications to hook into and change these conventions.</span></span>

### <a name="zero-bug-balance-zbb"></a><span data-ttu-id="9b93e-239">Aucun déséquilibre de bogue (ZBB)</span><span class="sxs-lookup"><span data-stu-id="9b93e-239">Zero bug balance (ZBB)</span></span>

<span data-ttu-id="9b93e-240">Suivi par des [problèmes étiquetés avec `type-bug` dans le jalon 6,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="9b93e-240">Tracked by [issues labeled with `type-bug` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="9b93e-241">État : en cours</span><span class="sxs-lookup"><span data-stu-id="9b93e-241">Status: In-progress</span></span>

<span data-ttu-id="9b93e-242">Taille de T-shirt : grande</span><span class="sxs-lookup"><span data-stu-id="9b93e-242">T-shirt size: Large</span></span>

<span data-ttu-id="9b93e-243">Nous prévoyons de corriger tous les bogues en attente pendant la période de EF Core 6,0.</span><span class="sxs-lookup"><span data-stu-id="9b93e-243">We plan to fix all outstanding bugs during the EF Core 6.0 time frame.</span></span> <span data-ttu-id="9b93e-244">Voici quelques points à prendre en compte :</span><span class="sxs-lookup"><span data-stu-id="9b93e-244">Some things to keep in mind:</span></span>

- <span data-ttu-id="9b93e-245">Cela s’applique spécifiquement aux problèmes étiquetés [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).</span><span class="sxs-lookup"><span data-stu-id="9b93e-245">This specifically applies to issues labeled [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).</span></span>
- <span data-ttu-id="9b93e-246">Il y aura des exceptions, par exemple lorsque le bogue nécessite une modification de conception ou une nouvelle fonctionnalité pour corriger correctement.</span><span class="sxs-lookup"><span data-stu-id="9b93e-246">There will be exceptions, such as when the bug requires a design change or new feature to fix properly.</span></span> <span data-ttu-id="9b93e-247">Ces problèmes sont signalés par l' `blocked` étiquette.</span><span class="sxs-lookup"><span data-stu-id="9b93e-247">These issues will be marked with the `blocked` label.</span></span>
- <span data-ttu-id="9b93e-248">Nous chargeur des bogues en fonction du risque lorsque cela est normal, car nous sommes proches d’une version GA/RTM.</span><span class="sxs-lookup"><span data-stu-id="9b93e-248">We will punt bugs based on risk when needed as is normal as we get close to a GA/RTM release.</span></span>

### <a name="miscellaneous-features"></a><span data-ttu-id="9b93e-249">Fonctionnalités diverses</span><span class="sxs-lookup"><span data-stu-id="9b93e-249">Miscellaneous features</span></span>

<span data-ttu-id="9b93e-250">Suivi par des [problèmes étiquetés avec `type-enhancement` dans le jalon 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span><span class="sxs-lookup"><span data-stu-id="9b93e-250">Tracked by [issues labeled with `type-enhancement` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span></span>

<span data-ttu-id="9b93e-251">État : en cours</span><span class="sxs-lookup"><span data-stu-id="9b93e-251">Status: In-progress</span></span>

<span data-ttu-id="9b93e-252">Taille de T-shirt : grande</span><span class="sxs-lookup"><span data-stu-id="9b93e-252">T-shirt size: Large</span></span>

<span data-ttu-id="9b93e-253">Les fonctionnalités diverses prévues pour EF 6,0 incluent, mais ne sont pas limitées à :</span><span class="sxs-lookup"><span data-stu-id="9b93e-253">Miscellaneous features planned for EF 6.0 include, but are not limited to:</span></span>

- [<span data-ttu-id="9b93e-254">Fractionner la requête pour les collections non de navigation</span><span class="sxs-lookup"><span data-stu-id="9b93e-254">Split query for non-navigation collections</span></span>](https://github.com/dotnet/efcore/issues/21234)
- [<span data-ttu-id="9b93e-255">Détecter les tables de jointures simples dans l’ingénierie à rebours et créer des relations plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="9b93e-255">Detect simple join tables in reverse engineering and create many-to-many relationships</span></span>](https://github.com/dotnet/efcore/issues/22475)
- [<span data-ttu-id="9b93e-256">Effectuer une recherche en texte intégral/libre sur SQLite et SQL Server</span><span class="sxs-lookup"><span data-stu-id="9b93e-256">Complete full/free-text search on SQLite and SQL Server</span></span>](https://github.com/dotnet/efcore/issues/4823)
- [<span data-ttu-id="9b93e-257">SQL Server les index spatiaux</span><span class="sxs-lookup"><span data-stu-id="9b93e-257">SQL Server spatial indexes</span></span>](https://github.com/dotnet/efcore/issues/12538)
- [<span data-ttu-id="9b93e-258">Mécanisme/API permettant de spécifier une conversion par défaut pour n’importe quelle propriété d’un type donné dans le modèle</span><span class="sxs-lookup"><span data-stu-id="9b93e-258">Mechanism/API to specify a default conversion for any property of a given type in the model</span></span>](https://github.com/dotnet/efcore/issues/10784)
- [<span data-ttu-id="9b93e-259">Utiliser la nouvelle API de traitement par lot de ADO.NET</span><span class="sxs-lookup"><span data-stu-id="9b93e-259">Use the new batching API from ADO.NET</span></span>](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a><span data-ttu-id="9b93e-260">.NET (intégration)</span><span class="sxs-lookup"><span data-stu-id="9b93e-260">.NET integration</span></span>

<span data-ttu-id="9b93e-261">L’équipe EF Core travaille également sur plusieurs technologies connexes, mais indépendantes.</span><span class="sxs-lookup"><span data-stu-id="9b93e-261">The EF Core team also works on several related but independent technologies.</span></span> <span data-ttu-id="9b93e-262">En particulier, nous prévoyons de travailler sur :</span><span class="sxs-lookup"><span data-stu-id="9b93e-262">In particular, we plan to work on:</span></span>

### <a name="enhancements-to-systemdata"></a><span data-ttu-id="9b93e-263">Améliorations apportées à System. Data</span><span class="sxs-lookup"><span data-stu-id="9b93e-263">Enhancements to System.Data</span></span>

<span data-ttu-id="9b93e-264">Suivi par des [problèmes dans le référentiel dotnet\runtime étiqueté avec `area-System.Data` dans le jalon 6,0](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span><span class="sxs-lookup"><span data-stu-id="9b93e-264">Tracked by [issues in the dotnet\runtime repo labeled with `area-System.Data` in the 6.0 milestone](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span></span>

<span data-ttu-id="9b93e-265">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-265">Status: Not started</span></span>

<span data-ttu-id="9b93e-266">Taille de T-shirt : grande</span><span class="sxs-lookup"><span data-stu-id="9b93e-266">T-shirt size: Large</span></span>

<span data-ttu-id="9b93e-267">Ce travail comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="9b93e-267">This work includes:</span></span>

- <span data-ttu-id="9b93e-268">Implémentation de la nouvelle [API de traitement par lot](https://github.com/dotnet/runtime/issues/28633).</span><span class="sxs-lookup"><span data-stu-id="9b93e-268">Implementation of the new [batching API](https://github.com/dotnet/runtime/issues/28633).</span></span>
- <span data-ttu-id="9b93e-269">Continuez à travailler avec d’autres équipes .NET et la communauté pour comprendre et faire évoluer ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="9b93e-269">Continued work with other .NET teams and the community to understand and evolve ADO.NET.</span></span>
- <span data-ttu-id="9b93e-270">[Normaliser sur DiagnosticSource pour le suivi des composants System. Data. \*](https://github.com/dotnet/runtime/issues/22336).</span><span class="sxs-lookup"><span data-stu-id="9b93e-270">[Standardize on DiagnosticSource for tracing in System.Data.\* components](https://github.com/dotnet/runtime/issues/22336).</span></span>

### <a name="enhancements-to-microsoftdatasqlite"></a><span data-ttu-id="9b93e-271">Améliorations apportées à Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="9b93e-271">Enhancements to Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="9b93e-272">Suivi par des [problèmes étiquetés avec `type-enhancement` et `area-adonet-sqlite` dans le jalon 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span><span class="sxs-lookup"><span data-stu-id="9b93e-272">Tracked by [issues labeled with `type-enhancement` and `area-adonet-sqlite` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span></span>

<span data-ttu-id="9b93e-273">État : en cours</span><span class="sxs-lookup"><span data-stu-id="9b93e-273">Status: In-progress</span></span>

<span data-ttu-id="9b93e-274">Taille de T-shirt : moyenne</span><span class="sxs-lookup"><span data-stu-id="9b93e-274">T-shirt size: Medium</span></span>

<span data-ttu-id="9b93e-275">Plusieurs petites améliorations sont planifiées pour Microsoft. Data. sqlite, notamment le [regroupement de connexions](https://github.com/dotnet/efcore/issues/13837) et les [instructions préparées](https://github.com/dotnet/efcore/issues/14044) pour les performances.</span><span class="sxs-lookup"><span data-stu-id="9b93e-275">Several small improvements are planned for the Microsoft.Data.Sqlite, including [connection pooling](https://github.com/dotnet/efcore/issues/13837) and [prepared statements](https://github.com/dotnet/efcore/issues/14044) for performance.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="9b93e-276">Types références Nullables</span><span class="sxs-lookup"><span data-stu-id="9b93e-276">Nullable reference types</span></span>

<span data-ttu-id="9b93e-277">Suivi par [#14150](https://github.com/dotnet/efcore/issues/14150)</span><span class="sxs-lookup"><span data-stu-id="9b93e-277">Tracked by [#14150](https://github.com/dotnet/efcore/issues/14150)</span></span>

<span data-ttu-id="9b93e-278">État : en cours</span><span class="sxs-lookup"><span data-stu-id="9b93e-278">Status: In-progress</span></span>

<span data-ttu-id="9b93e-279">Taille de T-shirt : grande</span><span class="sxs-lookup"><span data-stu-id="9b93e-279">T-shirt size: Large</span></span>

<span data-ttu-id="9b93e-280">Nous allons annoter le code EF Core pour utiliser des [types de référence Nullable](/dotnet/csharp/nullable-references).</span><span class="sxs-lookup"><span data-stu-id="9b93e-280">We will annotate the EF Core code to use [nullable reference types](/dotnet/csharp/nullable-references).</span></span>

## <a name="experiments-and-investigations"></a><span data-ttu-id="9b93e-281">Expériences et investigations</span><span class="sxs-lookup"><span data-stu-id="9b93e-281">Experiments and investigations</span></span>

<span data-ttu-id="9b93e-282">L’équipe EF envisage de consacrer du temps au cours de la période de EF Core 6,0 de l’expérimentation et de l’examen dans deux domaines.</span><span class="sxs-lookup"><span data-stu-id="9b93e-282">The EF team is planning to invest time during the EF Core 6.0 timeframe experimenting and investigating in two areas.</span></span> <span data-ttu-id="9b93e-283">Il s’agit d’un processus d’apprentissage, car aucun livrable concret n’est prévu pour la version 6,0.</span><span class="sxs-lookup"><span data-stu-id="9b93e-283">This is a learning process and as such no concrete deliverables are planned for the 6.0 release.</span></span>

### <a name="sqlservercore"></a><span data-ttu-id="9b93e-284">SqlServer. Core</span><span class="sxs-lookup"><span data-stu-id="9b93e-284">SqlServer.Core</span></span>

<span data-ttu-id="9b93e-285">Suivi dans le [laboratoire de données .net référentiel](https://github.com/dotnet/datalab/)</span><span class="sxs-lookup"><span data-stu-id="9b93e-285">Tracked in the [.NET Data Lab repo](https://github.com/dotnet/datalab/)</span></span>

<span data-ttu-id="9b93e-286">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-286">Status: Not started</span></span>

<span data-ttu-id="9b93e-287">Taille de T-shirt : en cours</span><span class="sxs-lookup"><span data-stu-id="9b93e-287">T-shirt size: Ongoing</span></span>

<span data-ttu-id="9b93e-288">[Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) est un fournisseur de base de données ADO.net complet pour SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9b93e-288">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) is a fully-featured ADO.NET database provider for SQL Server.</span></span> <span data-ttu-id="9b93e-289">Il prend en charge un large éventail de fonctionnalités de SQL Server à la fois sur .NET Core et .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="9b93e-289">It supports a broad range of SQL Server features on both .NET Core and .NET Framework.</span></span> <span data-ttu-id="9b93e-290">Toutefois, il s’agit également d’un code base volumineux et ancien avec de nombreuses interactions complexes entre ses comportements.</span><span class="sxs-lookup"><span data-stu-id="9b93e-290">However, it is also a large and old codebase with many complex interactions between its behaviors.</span></span> <span data-ttu-id="9b93e-291">Cela complique l’étude des gains potentiels qui peuvent être réalisés à l’aide des fonctionnalités .NET Core plus récentes.</span><span class="sxs-lookup"><span data-stu-id="9b93e-291">This makes it difficult to investigate the potential gains the could be made using newer .NET Core features.</span></span> <span data-ttu-id="9b93e-292">Par conséquent, nous commençons une expérience en collaboration avec la communauté pour déterminer le potentiel d’un pilote de SQL Server très performant pour .NET.</span><span class="sxs-lookup"><span data-stu-id="9b93e-292">Therefore, we are starting an experiment in collaboration with the community to determine what potential there is for a highly performing SQL Server driver for .NET.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9b93e-293">L’investissement dans Microsoft. Data. SqlClient ne change pas.</span><span class="sxs-lookup"><span data-stu-id="9b93e-293">Investment in Microsoft.Data.SqlClient is not changing.</span></span> <span data-ttu-id="9b93e-294">Elle sera toujours la méthode recommandée pour se connecter à SQL Server et SQL Azure, avec et sans EF Core.</span><span class="sxs-lookup"><span data-stu-id="9b93e-294">It will continue to be the recommended way to connect to SQL Server and SQL Azure, both with and without EF Core.</span></span> <span data-ttu-id="9b93e-295">Il continuera à prendre en charge les nouvelles fonctionnalités de SQL Server à mesure qu’elles seront introduites.</span><span class="sxs-lookup"><span data-stu-id="9b93e-295">It will continue to support new SQL Server features as they are introduced.</span></span>

### <a name="graphql"></a><span data-ttu-id="9b93e-296">GraphQL</span><span class="sxs-lookup"><span data-stu-id="9b93e-296">GraphQL</span></span>

<span data-ttu-id="9b93e-297">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-297">Status: Not started</span></span>

<span data-ttu-id="9b93e-298">Taille de T-shirt : en cours</span><span class="sxs-lookup"><span data-stu-id="9b93e-298">T-shirt size: Ongoing</span></span>

<span data-ttu-id="9b93e-299">[GraphQL](https://graphql.org/) a gagné en traction au cours des dernières années sur une variété de plateformes.</span><span class="sxs-lookup"><span data-stu-id="9b93e-299">[GraphQL](https://graphql.org/) has been gaining traction over the last few years across a variety of platforms.</span></span> <span data-ttu-id="9b93e-300">Nous prévoyons d’examiner l’espace et de trouver des moyens d’améliorer l’expérience avec .NET.</span><span class="sxs-lookup"><span data-stu-id="9b93e-300">We plan to investigate the space and find ways to improve the experience with .NET.</span></span> <span data-ttu-id="9b93e-301">Cela implique l’utilisation de la communauté pour comprendre et prendre en charge l’écosystème existant.</span><span class="sxs-lookup"><span data-stu-id="9b93e-301">This will involve working with the community on understanding and supporting the existing ecosystem.</span></span> <span data-ttu-id="9b93e-302">Il peut également impliquer un investissement spécifique de Microsoft, sous la forme de contributions à un travail existant ou en développement de pièces complémentaires dans la pile Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9b93e-302">It may also involve specific investment from Microsoft, either in the form of contributions to existing work or in developing complimentary pieces in the Microsoft stack.</span></span>

### <a name="dataverse-formerly-common-data-services"></a><span data-ttu-id="9b93e-303">DataVerse (anciennement Data Services courantes)</span><span class="sxs-lookup"><span data-stu-id="9b93e-303">DataVerse (formerly Common Data Services)</span></span>

<span data-ttu-id="9b93e-304">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="9b93e-304">Status: Not started</span></span>

<span data-ttu-id="9b93e-305">Taille de T-shirt : en cours</span><span class="sxs-lookup"><span data-stu-id="9b93e-305">T-shirt size: Ongoing</span></span>

<span data-ttu-id="9b93e-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) est un magasin de données basé sur des colonnes conçu pour le développement rapide d’applications métier.</span><span class="sxs-lookup"><span data-stu-id="9b93e-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) is a column-based data store designed for rapid development of business applications.</span></span> <span data-ttu-id="9b93e-307">Il gère automatiquement des types de données complexes tels que des objets binaires (BLOB) et des entités et des relations intégrées, telles que les organisations et les contacts.</span><span class="sxs-lookup"><span data-stu-id="9b93e-307">It automatically handles complex data types like binary objects (BLOBs) and has built-in entities and relationships like organizations and contacts.</span></span> <span data-ttu-id="9b93e-308">Un kit de développement logiciel (SDK) existe, mais les développeurs peuvent tirer parti de la présence d’un fournisseur EF Core pour utiliser des requêtes LINQ avancées, tirer parti de l’unité de travail et disposer d’une API d’accès aux données cohérente.</span><span class="sxs-lookup"><span data-stu-id="9b93e-308">An SDK exists but developers may benefit from having an EF Core provider to use advanced LINQ queries, take advantage of unit of work and have a consistent data access API.</span></span> <span data-ttu-id="9b93e-309">L’équipe prendra en compte différentes façons d’améliorer l’expérience du développeur .NET en se connectant à DataVerse.</span><span class="sxs-lookup"><span data-stu-id="9b93e-309">The team will consider different ways to improve the .NET developer experience connecting to DataVerse.</span></span>

## <a name="below-the-cut-line"></a><span data-ttu-id="9b93e-310">En dessous du trait de coupe</span><span class="sxs-lookup"><span data-stu-id="9b93e-310">Below-the-cut-line</span></span>

<span data-ttu-id="9b93e-311">Suivi par des [problèmes étiquetés `consider-for-current-release` avec](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span><span class="sxs-lookup"><span data-stu-id="9b93e-311">Tracked by [issues labeled with `consider-for-current-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span></span>

<span data-ttu-id="9b93e-312">Ce sont des correctifs de bogues et des améliorations qui **ne sont pas** actuellement planifiés pour la version 6,0, mais nous examinerons en fonction des commentaires dans la version, ainsi que de la progression de la tâche ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="9b93e-312">These are bug fixes and enhancements that are **not** currently scheduled for the 6.0 release, but we will look at based on feedback throughout the release together with progress made on the work above.</span></span> <span data-ttu-id="9b93e-313">Ces problèmes peuvent être tirés dans EF Core 6,0 et devenir automatiquement des candidats pour la prochaine version.</span><span class="sxs-lookup"><span data-stu-id="9b93e-313">These issues may be pulled in to EF Core 6.0, and automatically become candidates for the next release.</span></span>

<span data-ttu-id="9b93e-314">En outre, nous considérons toujours les [problèmes les plus votés](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) lors de la planification.</span><span class="sxs-lookup"><span data-stu-id="9b93e-314">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="9b93e-315">La réduction de l’un de ces problèmes à partir d’une version est toujours pénible, mais nous avons besoin d’un plan réaliste pour les ressources dont nous disposons.</span><span class="sxs-lookup"><span data-stu-id="9b93e-315">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span> <span data-ttu-id="9b93e-316">Vérifiez que vous avez voté ( 👍 ) pour les fonctionnalités dont vous avez besoin.</span><span class="sxs-lookup"><span data-stu-id="9b93e-316">Make sure you have voted (👍) for the features you need.</span></span>

## <a name="suggestions"></a><span data-ttu-id="9b93e-317">Suggestions</span><span class="sxs-lookup"><span data-stu-id="9b93e-317">Suggestions</span></span>

<span data-ttu-id="9b93e-318">Vos commentaires sur la planification sont importants.</span><span class="sxs-lookup"><span data-stu-id="9b93e-318">Your feedback on planning is important.</span></span> <span data-ttu-id="9b93e-319">La meilleure façon d’indiquer l’importance d’un problème est de voter ( 👍 ) pour ce problème sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="9b93e-319">The best way to indicate the importance of an issue is to vote (👍) for that issue on GitHub.</span></span> <span data-ttu-id="9b93e-320">Ces données sont ensuite chargées dans le [processus de planification](xref:core/what-is-new/release-planning) de la prochaine version.</span><span class="sxs-lookup"><span data-stu-id="9b93e-320">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
