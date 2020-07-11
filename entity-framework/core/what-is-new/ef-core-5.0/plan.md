---
title: Planifier Entity Framework Core 5,0
author: ajcvickers
ms.date: 06/11/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: a107f901b215e7abaa5905852f81c2d9ab094b98
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238318"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="78718-102">Planifier Entity Framework Core 5,0</span><span class="sxs-lookup"><span data-stu-id="78718-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="78718-103">Comme décrit dans le [processus de planification](xref:core/what-is-new/release_planning), nous avons rassemblé les entrées des parties prenantes dans un plan provisoire pour la version EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="78718-103">As described in the [planning process](xref:core/what-is-new/release_planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="78718-104">Ce plan est toujours un travail en cours.</span><span class="sxs-lookup"><span data-stu-id="78718-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="78718-105">Rien ici n’est un engagement.</span><span class="sxs-lookup"><span data-stu-id="78718-105">Nothing here is a commitment.</span></span> <span data-ttu-id="78718-106">Ce plan est un point de départ qui évoluera à mesure que nous en apprendrons davantage.</span><span class="sxs-lookup"><span data-stu-id="78718-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="78718-107">Certains éléments qui ne sont pas actuellement planifiés pour 5,0 peuvent être extraits.</span><span class="sxs-lookup"><span data-stu-id="78718-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="78718-108">Certains éléments actuellement planifiés pour 5,0 peuvent devenir punted.</span><span class="sxs-lookup"><span data-stu-id="78718-108">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="78718-109">Informations générales</span><span class="sxs-lookup"><span data-stu-id="78718-109">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="78718-110">Numéro de version et date de publication</span><span class="sxs-lookup"><span data-stu-id="78718-110">Version number and release date</span></span>

<span data-ttu-id="78718-111">La version de EF Core 5,0 est actuellement planifiée en [même temps que .net 5,0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="78718-111">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="78718-112">La version « 5,0 » a été choisie pour s’aligner sur .NET 5,0.</span><span class="sxs-lookup"><span data-stu-id="78718-112">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="78718-113">Plateformes prises en charge</span><span class="sxs-lookup"><span data-stu-id="78718-113">Supported platforms</span></span>

<span data-ttu-id="78718-114">EF Core 5,0 est planifiée pour s’exécuter sur toute plateforme .NET 5,0 basée sur la [convergence de ces plateformes sur .net Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="78718-114">EF Core 5.0 is planned to run on any .NET 5.0 platform based on the [convergence of these platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="78718-115">Ce que cela signifie en termes de .NET Standard et le TFM réel utilisé est toujours TBD.</span><span class="sxs-lookup"><span data-stu-id="78718-115">What this means in terms of .NET Standard and the actual TFM used is still TBD.</span></span>

<span data-ttu-id="78718-116">EF Core 5,0 ne s’exécutera pas sur .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="78718-116">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="78718-117">Changements cassants</span><span class="sxs-lookup"><span data-stu-id="78718-117">Breaking changes</span></span>

<span data-ttu-id="78718-118">EF Core 5,0 contiendra des [modifications avec rupture](xref:core/what-is-new/ef-core-5.0/breaking-changes), mais celles-ci seront bien moins sévères que dans le cas de EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="78718-118">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="78718-119">Notre objectif est de permettre la mise à jour sans interruption de la grande majorité des applications.</span><span class="sxs-lookup"><span data-stu-id="78718-119">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="78718-120">Il est prévu qu’il y ait des modifications avec rupture pour les fournisseurs de base de données, en particulier pour la prise en charge de TPT.</span><span class="sxs-lookup"><span data-stu-id="78718-120">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="78718-121">Toutefois, nous pensons que le travail de mise à jour d’un fournisseur pour 5,0 sera inférieur à la nécessité de mettre à jour 3,0.</span><span class="sxs-lookup"><span data-stu-id="78718-121">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="78718-122">Thèmes</span><span class="sxs-lookup"><span data-stu-id="78718-122">Themes</span></span>

<span data-ttu-id="78718-123">Nous avons extrait quelques principaux domaines ou thèmes qui constitueront la base des investissements importants en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="78718-123">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="78718-124">Mappage de plusieurs-à-plusieurs entièrement transparent par Convention</span><span class="sxs-lookup"><span data-stu-id="78718-124">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="78718-125">Développeurs en chef : @smitpatel , @AndriySvyryd et@lajones</span><span class="sxs-lookup"><span data-stu-id="78718-125">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="78718-126">Suivi par [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="78718-126">Tracked by [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span></span>

<span data-ttu-id="78718-127">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-127">T-shirt size: L</span></span>

<span data-ttu-id="78718-128">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-128">Status: In-progress</span></span>

<span data-ttu-id="78718-129">Plusieurs-à-plusieurs est la [fonctionnalité la plus demandée](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (environ 506 votes) sur le backlog github.</span><span class="sxs-lookup"><span data-stu-id="78718-129">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="78718-130">La prise en charge des relations plusieurs-à-plusieurs peut être divisée en trois domaines principaux :</span><span class="sxs-lookup"><span data-stu-id="78718-130">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="78718-131">Ignorer les propriétés de navigation--couvertes par le thème suivant.</span><span class="sxs-lookup"><span data-stu-id="78718-131">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="78718-132">Types d’entité de conteneur de propriétés.</span><span class="sxs-lookup"><span data-stu-id="78718-132">Property-bag entity types.</span></span> <span data-ttu-id="78718-133">Celles-ci permettent l’utilisation d’un type CLR standard (par exemple, `Dictionary` ) pour les instances d’entité, de sorte qu’un type CLR explicite n’est pas nécessaire pour chaque type d’entité.</span><span class="sxs-lookup"><span data-stu-id="78718-133">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="78718-134">Suivi par [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span><span class="sxs-lookup"><span data-stu-id="78718-134">Tracked by [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span></span>
* <span data-ttu-id="78718-135">Sucre pour une configuration facile des relations plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="78718-135">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="78718-136">En plus de la prise en charge de la navigation par omission, nous allons maintenant extraire ces autres zones de plusieurs-à-plusieurs dans EF Core 5,0 afin de fournir une expérience complète.</span><span class="sxs-lookup"><span data-stu-id="78718-136">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="78718-137">Propriétés de navigation plusieurs-à-plusieurs (a. k. a "ignorer les navigations")</span><span class="sxs-lookup"><span data-stu-id="78718-137">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="78718-138">Développeurs en chef : @smitpatel et@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="78718-138">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="78718-139">Suivi par [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="78718-139">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="78718-140">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-140">T-shirt size: L</span></span>

<span data-ttu-id="78718-141">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-141">Status: In-progress</span></span>

<span data-ttu-id="78718-142">Comme décrit dans le premier thème, la prise en charge de plusieurs-à-plusieurs a plusieurs aspects.</span><span class="sxs-lookup"><span data-stu-id="78718-142">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="78718-143">Ce thème suit spécifiquement l’utilisation des navigations ignorées.</span><span class="sxs-lookup"><span data-stu-id="78718-143">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="78718-144">Nous pensons que le bloqueur le plus significatif pour ceux qui souhaitent une prise en charge de type plusieurs-à-plusieurs ne peut pas utiliser les relations « naturelles », sans faire référence à la table de jointure, dans une logique métier telle que des requêtes.</span><span class="sxs-lookup"><span data-stu-id="78718-144">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="78718-145">Le type d’entité de la table de jointure peut encore exister, mais il ne doit pas être dans la logique métier.</span><span class="sxs-lookup"><span data-stu-id="78718-145">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="78718-146">Mappage de l’héritage TPT (table par type) (TPT)</span><span class="sxs-lookup"><span data-stu-id="78718-146">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="78718-147">Développeur principal :@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="78718-147">Lead developer: @AndriySvyryd</span></span>

<span data-ttu-id="78718-148">Suivi par [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="78718-148">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="78718-149">Taille de T-shirt : XL</span><span class="sxs-lookup"><span data-stu-id="78718-149">T-shirt size: XL</span></span>

<span data-ttu-id="78718-150">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-150">Status: In-progress</span></span>

<span data-ttu-id="78718-151">Nous faisons TPT parce qu’il s’agit d’une fonctionnalité hautement demandée (environ 289 votes ; 3e au total) et qu’elle nécessite des modifications de bas niveau que nous pensons à la nature fondamentale du plan .NET 5 global.</span><span class="sxs-lookup"><span data-stu-id="78718-151">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="78718-152">Nous pensons que cela entraînera des modifications avec rupture pour les fournisseurs de base de données, bien que ceux-ci soient nettement moins sévères que les modifications requises pour 3,0.</span><span class="sxs-lookup"><span data-stu-id="78718-152">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="78718-153">Include filtré</span><span class="sxs-lookup"><span data-stu-id="78718-153">Filtered Include</span></span>

<span data-ttu-id="78718-154">Développeur principal :@maumar</span><span class="sxs-lookup"><span data-stu-id="78718-154">Lead developer: @maumar</span></span>

<span data-ttu-id="78718-155">Suivi par [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="78718-155">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="78718-156">Taille de T-shirt : M</span><span class="sxs-lookup"><span data-stu-id="78718-156">T-shirt size: M</span></span>

<span data-ttu-id="78718-157">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-157">Status: In-progress</span></span>

<span data-ttu-id="78718-158">Le modèle include filtré est une fonctionnalité très demandée (environ 376 votes ; 2e au total) qui n’est pas une énorme quantité de travail, et que nous pensons autoriser à débloquer ou à faciliter de nombreux scénarios qui nécessitent actuellement des filtres au niveau du modèle ou des requêtes plus complexes.</span><span class="sxs-lookup"><span data-stu-id="78718-158">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="78718-159">Fractionner inclure</span><span class="sxs-lookup"><span data-stu-id="78718-159">Split Include</span></span>

<span data-ttu-id="78718-160">Développeur principal :@smitpatel</span><span class="sxs-lookup"><span data-stu-id="78718-160">Lead developer: @smitpatel</span></span>

<span data-ttu-id="78718-161">Suivi par [#20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="78718-161">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="78718-162">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-162">T-shirt size: L</span></span>

<span data-ttu-id="78718-163">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-163">Status: In-progress</span></span>

<span data-ttu-id="78718-164">EF Core 3,0 a modifié le comportement par défaut pour créer une requête SQL unique pour une requête LINQ donnée.</span><span class="sxs-lookup"><span data-stu-id="78718-164">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="78718-165">Cela entraînait des régressions de performances importantes pour les requêtes qui utilisent include pour plusieurs collections.</span><span class="sxs-lookup"><span data-stu-id="78718-165">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="78718-166">Dans EF Core 5,0, nous conservons le nouveau comportement par défaut.</span><span class="sxs-lookup"><span data-stu-id="78718-166">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="78718-167">Toutefois, EF Core 5,0 permet désormais la génération de plusieurs requêtes pour la collection, notamment lorsque l’utilisation d’une seule requête entraîne des performances incorrectes.</span><span class="sxs-lookup"><span data-stu-id="78718-167">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="78718-168">Rationaliser ToTable, ToQuery, ToView, FromSql, etc.</span><span class="sxs-lookup"><span data-stu-id="78718-168">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="78718-169">Développeurs en chef : @maumar et@smitpatel</span><span class="sxs-lookup"><span data-stu-id="78718-169">Lead developers: @maumar and @smitpatel</span></span>

<span data-ttu-id="78718-170">Suivi par [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="78718-170">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="78718-171">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-171">T-shirt size: L</span></span>

<span data-ttu-id="78718-172">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-172">Status: In-progress</span></span>

<span data-ttu-id="78718-173">Nous avons progressé dans les versions précédentes jusqu’à la prise en charge de SQL brut, de types sans clé et de zones associées.</span><span class="sxs-lookup"><span data-stu-id="78718-173">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="78718-174">Toutefois, il existe des lacunes et des incohérences dans la façon dont tout fonctionne ensemble dans son ensemble.</span><span class="sxs-lookup"><span data-stu-id="78718-174">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="78718-175">L’objectif de 5,0 est de les corriger et de créer une bonne expérience pour la définition, la migration et l’utilisation de différents types d’entités et de leurs requêtes et artefacts de base de données associés.</span><span class="sxs-lookup"><span data-stu-id="78718-175">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="78718-176">Cela peut également impliquer des mises à jour de l’API de requête compilée.</span><span class="sxs-lookup"><span data-stu-id="78718-176">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="78718-177">Notez que cet élément peut entraîner des modifications avec rupture au niveau de l’application, car certaines des fonctionnalités dont nous disposons actuellement sont trop permissive, ce qui permet à l’utilisateur de mener rapidement des gens sur des problèmes.</span><span class="sxs-lookup"><span data-stu-id="78718-177">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="78718-178">Nous nous contenterons de bloquer certaines de ces fonctionnalités, ainsi que des conseils sur la marche à suivre.</span><span class="sxs-lookup"><span data-stu-id="78718-178">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="78718-179">Améliorations générales relatives aux requêtes</span><span class="sxs-lookup"><span data-stu-id="78718-179">General query enhancements</span></span>

<span data-ttu-id="78718-180">Développeurs en chef : @smitpatel et@maumar</span><span class="sxs-lookup"><span data-stu-id="78718-180">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="78718-181">Suivi par des [problèmes étiquetés avec `area-query` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="78718-181">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="78718-182">Taille de T-shirt : XL</span><span class="sxs-lookup"><span data-stu-id="78718-182">T-shirt size: XL</span></span>

<span data-ttu-id="78718-183">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-183">Status: In-progress</span></span>

<span data-ttu-id="78718-184">Le code de traduction de la requête a été largement réécrit pour EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="78718-184">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="78718-185">Le code de requête est généralement dans un état bien plus robuste.</span><span class="sxs-lookup"><span data-stu-id="78718-185">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="78718-186">Pour 5,0, nous ne prévoyons pas de modifier les requêtes majeures, en dehors de celles qui sont nécessaires pour prendre en charge les propriétés de navigation TPT et Skip.</span><span class="sxs-lookup"><span data-stu-id="78718-186">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="78718-187">Toutefois, il y a toujours un travail important nécessaire pour résoudre une dette technique à partir de la révision 3,0.</span><span class="sxs-lookup"><span data-stu-id="78718-187">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="78718-188">Nous prévoyons également de résoudre de nombreux bogues et d’implémenter de petites améliorations pour améliorer l’expérience globale des requêtes.</span><span class="sxs-lookup"><span data-stu-id="78718-188">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="78718-189">Migrations et expérience de déploiement</span><span class="sxs-lookup"><span data-stu-id="78718-189">Migrations and deployment experience</span></span>

<span data-ttu-id="78718-190">Développeurs en chef :@bricelam</span><span class="sxs-lookup"><span data-stu-id="78718-190">Lead developers: @bricelam</span></span>

<span data-ttu-id="78718-191">Suivi par [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="78718-191">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="78718-192">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-192">T-shirt size: L</span></span>

<span data-ttu-id="78718-193">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-193">Status: In-progress</span></span>

<span data-ttu-id="78718-194">Actuellement, de nombreux développeurs migrent leurs bases de données au moment du démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="78718-194">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="78718-195">C’est facile, mais cela n’est pas recommandé car :</span><span class="sxs-lookup"><span data-stu-id="78718-195">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="78718-196">Plusieurs threads/processus/serveurs peuvent tenter de migrer la base de données simultanément</span><span class="sxs-lookup"><span data-stu-id="78718-196">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="78718-197">Les applications peuvent tenter d’accéder à un état incohérent alors que cela se produit</span><span class="sxs-lookup"><span data-stu-id="78718-197">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="78718-198">En général, les autorisations de base de données pour modifier le schéma ne doivent pas être accordées pour l’exécution de l’application</span><span class="sxs-lookup"><span data-stu-id="78718-198">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="78718-199">Il est difficile de revenir à un état propre si un problème se pose</span><span class="sxs-lookup"><span data-stu-id="78718-199">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="78718-200">Nous souhaitons offrir une meilleure expérience ici, qui permet de migrer facilement la base de données au moment du déploiement.</span><span class="sxs-lookup"><span data-stu-id="78718-200">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="78718-201">Cela doit :</span><span class="sxs-lookup"><span data-stu-id="78718-201">This should:</span></span>

* <span data-ttu-id="78718-202">Travailler sur Linux, Mac et Windows</span><span class="sxs-lookup"><span data-stu-id="78718-202">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="78718-203">Soyez une bonne expérience sur la ligne de commande</span><span class="sxs-lookup"><span data-stu-id="78718-203">Be a good experience on the command line</span></span>
* <span data-ttu-id="78718-204">Scénarios de prise en charge avec conteneurs</span><span class="sxs-lookup"><span data-stu-id="78718-204">Support scenarios with containers</span></span>
* <span data-ttu-id="78718-205">Travailler avec des outils/flux de déploiement réels couramment utilisés</span><span class="sxs-lookup"><span data-stu-id="78718-205">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="78718-206">Intégrer dans au moins Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78718-206">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="78718-207">Le résultat est probablement de nombreuses améliorations de EF Core (par exemple, de meilleures migrations sur SQLite), ainsi que des conseils et des collaborations à long terme avec d’autres équipes pour améliorer les expériences de bout en bout qui vont au-delà d’EF.</span><span class="sxs-lookup"><span data-stu-id="78718-207">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="78718-208">Expérience des plates-formes EF Core</span><span class="sxs-lookup"><span data-stu-id="78718-208">EF Core platforms experience</span></span>

<span data-ttu-id="78718-209">Développeurs en chef : @roji et@bricelam</span><span class="sxs-lookup"><span data-stu-id="78718-209">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="78718-210">Suivi par [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="78718-210">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="78718-211">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-211">T-shirt size: L</span></span>

<span data-ttu-id="78718-212">État : non démarré</span><span class="sxs-lookup"><span data-stu-id="78718-212">Status: Not started</span></span>

<span data-ttu-id="78718-213">Nous avons de bonnes recommandations pour l’utilisation de EF Core dans des applications Web classiques basées sur MVC.</span><span class="sxs-lookup"><span data-stu-id="78718-213">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="78718-214">Des conseils pour d’autres plateformes et modèles d’application sont manquants ou obsolètes.</span><span class="sxs-lookup"><span data-stu-id="78718-214">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="78718-215">Pour EF Core 5,0, nous prévoyons d’examiner, d’améliorer et de documenter l’expérience d’utilisation de EF Core avec :</span><span class="sxs-lookup"><span data-stu-id="78718-215">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="78718-216">Blazor</span><span class="sxs-lookup"><span data-stu-id="78718-216">Blazor</span></span>
* <span data-ttu-id="78718-217">Xamarin, y compris l’utilisation de l’AOA/histoire de l’éditeur de liens</span><span class="sxs-lookup"><span data-stu-id="78718-217">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="78718-218">WinForms/WPF/WinUI et éventuellement d’autres U.I.</span><span class="sxs-lookup"><span data-stu-id="78718-218">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="78718-219">frameworks</span><span class="sxs-lookup"><span data-stu-id="78718-219">frameworks</span></span>

<span data-ttu-id="78718-220">Il s’agit probablement de nombreuses améliorations de EF Core, ainsi que de conseils et de collaborations à long terme avec d’autres équipes pour améliorer les expériences de bout en bout qui vont au-delà d’EF.</span><span class="sxs-lookup"><span data-stu-id="78718-220">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="78718-221">Voici quelques-uns des éléments que nous prévoyons de consulter :</span><span class="sxs-lookup"><span data-stu-id="78718-221">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="78718-222">Déploiement, y compris l’expérience d’utilisation des outils EF comme pour les migrations</span><span class="sxs-lookup"><span data-stu-id="78718-222">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="78718-223">Modèles d’application, notamment Xamarin et éblouissant, et probablement d’autres</span><span class="sxs-lookup"><span data-stu-id="78718-223">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="78718-224">Expériences SQLite, y compris l’expérience spatiale et les reconstructions de tables</span><span class="sxs-lookup"><span data-stu-id="78718-224">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="78718-225">AOA et liaison d’expériences</span><span class="sxs-lookup"><span data-stu-id="78718-225">AOT and linking experiences</span></span>
* <span data-ttu-id="78718-226">Intégration des diagnostics, y compris les compteurs de performances</span><span class="sxs-lookup"><span data-stu-id="78718-226">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="78718-227">Performances</span><span class="sxs-lookup"><span data-stu-id="78718-227">Performance</span></span>

<span data-ttu-id="78718-228">Développeur principal :@roji</span><span class="sxs-lookup"><span data-stu-id="78718-228">Lead developer: @roji</span></span>

<span data-ttu-id="78718-229">Suivi par des [problèmes étiquetés avec `area-perf` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="78718-229">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="78718-230">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-230">T-shirt size: L</span></span>

<span data-ttu-id="78718-231">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-231">Status: In-progress</span></span>

<span data-ttu-id="78718-232">Par EF Core, nous prévoyons d’améliorer notre suite de tests de performances et d’améliorer les performances de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="78718-232">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="78718-233">En outre, nous prévoyons de terminer la nouvelle API de traitement par lot ADO.NET qui a été prototypée durant le cycle de publication de 3,0.</span><span class="sxs-lookup"><span data-stu-id="78718-233">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="78718-234">En outre, au niveau de la couche ADO.NET, nous prévoyons des améliorations de performances supplémentaires pour le fournisseur npgsql.</span><span class="sxs-lookup"><span data-stu-id="78718-234">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="78718-235">Dans le cadre de ce travail, nous prévoyons également d’ajouter des compteurs de performances ADO.NET/EF Core et d’autres diagnostics appropriés.</span><span class="sxs-lookup"><span data-stu-id="78718-235">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="78718-236">Documentation architecturale/contributeur</span><span class="sxs-lookup"><span data-stu-id="78718-236">Architectural/contributor documentation</span></span>

<span data-ttu-id="78718-237">Documentation en chef :@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="78718-237">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="78718-238">Suivi par [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="78718-238">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="78718-239">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-239">T-shirt size: L</span></span>

<span data-ttu-id="78718-240">État : couper</span><span class="sxs-lookup"><span data-stu-id="78718-240">Status: Cut</span></span>

<span data-ttu-id="78718-241">L’idée ici est de faciliter la compréhension de ce qui se passe dans les éléments internes de EF Core.</span><span class="sxs-lookup"><span data-stu-id="78718-241">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="78718-242">Cela peut être utile pour toute personne utilisant EF Core, mais la motivation principale est de faciliter la tâche pour les utilisateurs externes :</span><span class="sxs-lookup"><span data-stu-id="78718-242">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="78718-243">Contribuer au code EF Core</span><span class="sxs-lookup"><span data-stu-id="78718-243">Contribute to the EF Core code</span></span>
* <span data-ttu-id="78718-244">Créer des fournisseurs de base de données</span><span class="sxs-lookup"><span data-stu-id="78718-244">Create database providers</span></span>
* <span data-ttu-id="78718-245">Créer d’autres extensions</span><span class="sxs-lookup"><span data-stu-id="78718-245">Build other extensions</span></span>

<span data-ttu-id="78718-246">Mise à jour : Malheureusement, ce plan était trop ambitieux.</span><span class="sxs-lookup"><span data-stu-id="78718-246">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="78718-247">Nous pensons toujours qu’il s’agit d’un élément important, mais ce n’est malheureusement pas le EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="78718-247">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="78718-248">Documentation de Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="78718-248">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="78718-249">Documentation en chef :@bricelam</span><span class="sxs-lookup"><span data-stu-id="78718-249">Lead documenter: @bricelam</span></span>

<span data-ttu-id="78718-250">Suivi par [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="78718-250">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="78718-251">Taille de T-shirt : M</span><span class="sxs-lookup"><span data-stu-id="78718-251">T-shirt size: M</span></span>

<span data-ttu-id="78718-252">État : terminé.</span><span class="sxs-lookup"><span data-stu-id="78718-252">Status: Completed.</span></span> <span data-ttu-id="78718-253">La nouvelle documentation est [en ligne sur le site Microsoft docs](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span><span class="sxs-lookup"><span data-stu-id="78718-253">The new documentation is [live on the Microsoft docs site](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="78718-254">L’équipe EF possède également le fournisseur ADO.NET de Microsoft. Data. sqlite.</span><span class="sxs-lookup"><span data-stu-id="78718-254">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="78718-255">Nous prévoyons de documenter entièrement ce fournisseur dans le cadre de la version 5,0.</span><span class="sxs-lookup"><span data-stu-id="78718-255">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="78718-256">Documentation générale</span><span class="sxs-lookup"><span data-stu-id="78718-256">General documentation</span></span>

<span data-ttu-id="78718-257">Documentation en chef :@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="78718-257">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="78718-258">Suivi par des [problèmes dans la documentation référentiel dans le jalon 5,0](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="78718-258">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="78718-259">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-259">T-shirt size: L</span></span>

<span data-ttu-id="78718-260">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-260">Status: In-progress</span></span>

<span data-ttu-id="78718-261">Nous sommes déjà en train de mettre à jour la documentation pour les versions 3,0 et 3,1.</span><span class="sxs-lookup"><span data-stu-id="78718-261">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="78718-262">Nous travaillons également sur :</span><span class="sxs-lookup"><span data-stu-id="78718-262">We are also working on:</span></span>

* <span data-ttu-id="78718-263">Une révision des documents de prise en main pour les rendre plus simples et plus faciles à suivre</span><span class="sxs-lookup"><span data-stu-id="78718-263">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="78718-264">Réorganisation de docs pour faciliter la recherche et ajouter des références croisées</span><span class="sxs-lookup"><span data-stu-id="78718-264">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="78718-265">Ajout de détails et de clarifications à des documents existants</span><span class="sxs-lookup"><span data-stu-id="78718-265">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="78718-266">Mise à jour des exemples et ajout d’autres exemples</span><span class="sxs-lookup"><span data-stu-id="78718-266">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="78718-267">Correction des bogues</span><span class="sxs-lookup"><span data-stu-id="78718-267">Fixing bugs</span></span>

<span data-ttu-id="78718-268">Suivi par des [problèmes étiquetés avec `type-bug` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="78718-268">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="78718-269">Développeurs : @roji , @maumar ,,, @bricelam @smitpatel @AndriySvyryd ,@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="78718-269">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="78718-270">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-270">T-shirt size: L</span></span>

<span data-ttu-id="78718-271">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-271">Status: In-progress</span></span>

<span data-ttu-id="78718-272">Au moment de la rédaction de ce document, nous avons 135 bogues en triage pour qu’ils soient corrigés dans la version 5,0 (avec 62 déjà corrigé), mais il y a un chevauchement significatif avec la section sur les _améliorations générales des requêtes_ ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="78718-272">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="78718-273">Le taux entrant (les problèmes qui se terminent par un travail dans une étape majeure) était d’environ 23 problèmes par mois au cours de la version 3,0.</span><span class="sxs-lookup"><span data-stu-id="78718-273">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="78718-274">Ces derniers ne doivent pas nécessairement être corrigés dans 5,0.</span><span class="sxs-lookup"><span data-stu-id="78718-274">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="78718-275">En guise d’estimation approximative, nous prévoyons de résoudre les problèmes 150 supplémentaires dans le délai de 5,0.</span><span class="sxs-lookup"><span data-stu-id="78718-275">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="78718-276">Petites améliorations</span><span class="sxs-lookup"><span data-stu-id="78718-276">Small enhancements</span></span>

<span data-ttu-id="78718-277">Suivi par des [problèmes étiquetés avec `type-enhancement` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="78718-277">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="78718-278">Développeurs : @roji , @maumar ,,, @bricelam @smitpatel @AndriySvyryd ,@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="78718-278">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="78718-279">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="78718-279">T-shirt size: L</span></span>

<span data-ttu-id="78718-280">État : en cours</span><span class="sxs-lookup"><span data-stu-id="78718-280">Status: In-progress</span></span>

<span data-ttu-id="78718-281">En plus des fonctionnalités plus grandes décrites ci-dessus, nous avons également de nombreuses améliorations plus petites, planifiées pour 5,0, afin de corriger les « coupes papier ».</span><span class="sxs-lookup"><span data-stu-id="78718-281">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="78718-282">Notez que la plupart de ces améliorations sont également couvertes par les thèmes plus généraux décrits ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="78718-282">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="78718-283">En dessous de la ligne</span><span class="sxs-lookup"><span data-stu-id="78718-283">Below-the-line</span></span>

<span data-ttu-id="78718-284">Suivi par des [problèmes étiquetés `consider-for-next-release` avec](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="78718-284">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="78718-285">Ce sont des correctifs de bogues et des améliorations qui **ne sont pas** actuellement planifiés pour la version 5,0, mais nous examinerons les objectifs d’étirement en fonction de la progression de la tâche ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="78718-285">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="78718-286">En outre, nous considérons toujours les [problèmes les plus votés](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) lors de la planification.</span><span class="sxs-lookup"><span data-stu-id="78718-286">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="78718-287">La réduction de l’un de ces problèmes à partir d’une version est toujours pénible, mais nous avons besoin d’un plan réaliste pour les ressources dont nous disposons.</span><span class="sxs-lookup"><span data-stu-id="78718-287">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="78718-288">Suggestions</span><span class="sxs-lookup"><span data-stu-id="78718-288">Suggestions</span></span>

<span data-ttu-id="78718-289">Vos commentaires sur la planification sont importants.</span><span class="sxs-lookup"><span data-stu-id="78718-289">Your feedback on planning is important.</span></span> <span data-ttu-id="78718-290">La meilleure façon d’indiquer l’importance d’un problème est de voter (pouce vers le haut) pour ce problème sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="78718-290">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="78718-291">Ces données sont ensuite chargées dans le [processus de planification](xref:core/what-is-new/release_planning) de la prochaine version.</span><span class="sxs-lookup"><span data-stu-id="78718-291">This data will then feed into the [planning process](xref:core/what-is-new/release_planning) for the next release.</span></span>
