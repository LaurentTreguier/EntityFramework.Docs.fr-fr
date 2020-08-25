---
title: Planifier Entity Framework Core 5,0
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: a0d41d6df844c9ca2c8a2dc8ba50ca669e23dced
ms.sourcegitcommit: 6f7af3f138bf7c724cbdda261f97e5cf7035e8d7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88847577"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="60571-102">Planifier Entity Framework Core 5,0</span><span class="sxs-lookup"><span data-stu-id="60571-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="60571-103">Comme décrit dans le [processus de planification](xref:core/what-is-new/release_planning), nous avons rassemblé les entrées des parties prenantes dans un plan provisoire pour la version EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="60571-103">As described in the [planning process](xref:core/what-is-new/release_planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="60571-104">Ce plan est toujours un travail en cours.</span><span class="sxs-lookup"><span data-stu-id="60571-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="60571-105">Rien ici n’est un engagement.</span><span class="sxs-lookup"><span data-stu-id="60571-105">Nothing here is a commitment.</span></span> <span data-ttu-id="60571-106">Ce plan est un point de départ qui évoluera à mesure que nous en apprendrons davantage.</span><span class="sxs-lookup"><span data-stu-id="60571-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="60571-107">Certains éléments qui ne sont pas actuellement planifiés pour 5,0 peuvent être extraits.</span><span class="sxs-lookup"><span data-stu-id="60571-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="60571-108">Certains éléments actuellement planifiés pour 5,0 peuvent devenir punted.</span><span class="sxs-lookup"><span data-stu-id="60571-108">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="60571-109">Informations générales</span><span class="sxs-lookup"><span data-stu-id="60571-109">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="60571-110">Numéro de version et date de publication</span><span class="sxs-lookup"><span data-stu-id="60571-110">Version number and release date</span></span>

<span data-ttu-id="60571-111">La version de EF Core 5,0 est actuellement planifiée en [même temps que .net 5,0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="60571-111">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="60571-112">La version « 5,0 » a été choisie pour s’aligner sur .NET 5,0.</span><span class="sxs-lookup"><span data-stu-id="60571-112">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="60571-113">Plateformes prises en charge</span><span class="sxs-lookup"><span data-stu-id="60571-113">Supported platforms</span></span>

<span data-ttu-id="60571-114">EF Core 5,0 est planifiée pour s’exécuter sur toute plateforme .NET Standard 2,1, y compris .NET 5,0.</span><span class="sxs-lookup"><span data-stu-id="60571-114">EF Core 5.0 is planned to run on any .NET Standard 2.1 platform, including .NET 5.0.</span></span> <span data-ttu-id="60571-115">Cela fait partie de la convergence .NET plus générale [des plateformes à .net Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="60571-115">This is part of the more general .NET wide [convergence of platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span>

<span data-ttu-id="60571-116">EF Core 5,0 ne s’exécutera pas sur .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="60571-116">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="60571-117">Changements cassants</span><span class="sxs-lookup"><span data-stu-id="60571-117">Breaking changes</span></span>

<span data-ttu-id="60571-118">EF Core 5,0 contiendra des [modifications avec rupture](xref:core/what-is-new/ef-core-5.0/breaking-changes), mais celles-ci seront bien moins sévères que dans le cas de EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="60571-118">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="60571-119">Notre objectif est de permettre la mise à jour sans interruption de la grande majorité des applications.</span><span class="sxs-lookup"><span data-stu-id="60571-119">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="60571-120">Il est prévu qu’il y ait des modifications avec rupture pour les fournisseurs de base de données, en particulier pour la prise en charge de TPT.</span><span class="sxs-lookup"><span data-stu-id="60571-120">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="60571-121">Toutefois, nous pensons que le travail de mise à jour d’un fournisseur pour 5,0 sera inférieur à la nécessité de mettre à jour 3,0.</span><span class="sxs-lookup"><span data-stu-id="60571-121">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="60571-122">Thèmes</span><span class="sxs-lookup"><span data-stu-id="60571-122">Themes</span></span>

<span data-ttu-id="60571-123">Nous avons extrait quelques principaux domaines ou thèmes qui constitueront la base des investissements importants en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="60571-123">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="60571-124">Mappage de plusieurs-à-plusieurs entièrement transparent par Convention</span><span class="sxs-lookup"><span data-stu-id="60571-124">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="60571-125">Développeurs en chef : @smitpatel , @AndriySvyryd et @lajones</span><span class="sxs-lookup"><span data-stu-id="60571-125">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="60571-126">Suivi par [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="60571-126">Tracked by [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span></span>

<span data-ttu-id="60571-127">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-127">T-shirt size: L</span></span>

<span data-ttu-id="60571-128">État : terminé</span><span class="sxs-lookup"><span data-stu-id="60571-128">Status: Done</span></span>

<span data-ttu-id="60571-129">Plusieurs-à-plusieurs est la [fonctionnalité la plus demandée](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (environ 506 votes) sur le backlog github.</span><span class="sxs-lookup"><span data-stu-id="60571-129">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="60571-130">La prise en charge des relations plusieurs-à-plusieurs peut être divisée en trois domaines principaux :</span><span class="sxs-lookup"><span data-stu-id="60571-130">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="60571-131">Ignorer les propriétés de navigation--couvertes par le thème suivant.</span><span class="sxs-lookup"><span data-stu-id="60571-131">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="60571-132">Types d’entité de conteneur de propriétés.</span><span class="sxs-lookup"><span data-stu-id="60571-132">Property-bag entity types.</span></span> <span data-ttu-id="60571-133">Celles-ci permettent l’utilisation d’un type CLR standard (par exemple, `Dictionary` ) pour les instances d’entité, de sorte qu’un type CLR explicite n’est pas nécessaire pour chaque type d’entité.</span><span class="sxs-lookup"><span data-stu-id="60571-133">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="60571-134">Suivi par [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span><span class="sxs-lookup"><span data-stu-id="60571-134">Tracked by [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span></span>
* <span data-ttu-id="60571-135">Sucre pour une configuration facile des relations plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="60571-135">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="60571-136">En plus de la prise en charge de la navigation par omission, nous allons maintenant extraire ces autres zones de plusieurs-à-plusieurs dans EF Core 5,0 afin de fournir une expérience complète.</span><span class="sxs-lookup"><span data-stu-id="60571-136">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="60571-137">Propriétés de navigation plusieurs-à-plusieurs (a. k. a "ignorer les navigations")</span><span class="sxs-lookup"><span data-stu-id="60571-137">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="60571-138">Développeurs en chef : @smitpatel et @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="60571-138">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="60571-139">Suivi par [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="60571-139">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="60571-140">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-140">T-shirt size: L</span></span>

<span data-ttu-id="60571-141">État : terminé</span><span class="sxs-lookup"><span data-stu-id="60571-141">Status: Done</span></span>

<span data-ttu-id="60571-142">Comme décrit dans le premier thème, la prise en charge de plusieurs-à-plusieurs a plusieurs aspects.</span><span class="sxs-lookup"><span data-stu-id="60571-142">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="60571-143">Ce thème suit spécifiquement l’utilisation des navigations ignorées.</span><span class="sxs-lookup"><span data-stu-id="60571-143">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="60571-144">Nous pensons que le bloqueur le plus significatif pour ceux qui souhaitent une prise en charge de type plusieurs-à-plusieurs ne peut pas utiliser les relations « naturelles », sans faire référence à la table de jointure, dans une logique métier telle que des requêtes.</span><span class="sxs-lookup"><span data-stu-id="60571-144">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="60571-145">Le type d’entité de la table de jointure peut encore exister, mais il ne doit pas être dans la logique métier.</span><span class="sxs-lookup"><span data-stu-id="60571-145">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="60571-146">Mappage de l’héritage TPT (table par type) (TPT)</span><span class="sxs-lookup"><span data-stu-id="60571-146">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="60571-147">Développeur principal : @AndriySvyryd et @smitpatel</span><span class="sxs-lookup"><span data-stu-id="60571-147">Lead developer: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="60571-148">Suivi par [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="60571-148">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="60571-149">Taille de T-shirt : XL</span><span class="sxs-lookup"><span data-stu-id="60571-149">T-shirt size: XL</span></span>

<span data-ttu-id="60571-150">État : terminé</span><span class="sxs-lookup"><span data-stu-id="60571-150">Status: Done</span></span>

<span data-ttu-id="60571-151">Nous faisons TPT parce qu’il s’agit d’une fonctionnalité hautement demandée (environ 289 votes ; 3e au total) et qu’elle nécessite des modifications de bas niveau que nous pensons à la nature fondamentale du plan .NET 5 global.</span><span class="sxs-lookup"><span data-stu-id="60571-151">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="60571-152">Nous pensons que cela entraînera des modifications avec rupture pour les fournisseurs de base de données, bien que ceux-ci soient nettement moins sévères que les modifications requises pour 3,0.</span><span class="sxs-lookup"><span data-stu-id="60571-152">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="60571-153">Include filtré</span><span class="sxs-lookup"><span data-stu-id="60571-153">Filtered Include</span></span>

<span data-ttu-id="60571-154">Développeur principal : @maumar</span><span class="sxs-lookup"><span data-stu-id="60571-154">Lead developer: @maumar</span></span>

<span data-ttu-id="60571-155">Suivi par [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="60571-155">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="60571-156">Taille de T-shirt : M</span><span class="sxs-lookup"><span data-stu-id="60571-156">T-shirt size: M</span></span>

<span data-ttu-id="60571-157">État : terminé</span><span class="sxs-lookup"><span data-stu-id="60571-157">Status: Done</span></span>

<span data-ttu-id="60571-158">Le modèle include filtré est une fonctionnalité très demandée (environ 376 votes ; 2e au total) qui n’est pas une énorme quantité de travail, et que nous pensons autoriser à débloquer ou à faciliter de nombreux scénarios qui nécessitent actuellement des filtres au niveau du modèle ou des requêtes plus complexes.</span><span class="sxs-lookup"><span data-stu-id="60571-158">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="60571-159">Fractionner inclure</span><span class="sxs-lookup"><span data-stu-id="60571-159">Split Include</span></span>

<span data-ttu-id="60571-160">Développeur principal : @smitpatel</span><span class="sxs-lookup"><span data-stu-id="60571-160">Lead developer: @smitpatel</span></span>

<span data-ttu-id="60571-161">Suivi par [#20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="60571-161">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="60571-162">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-162">T-shirt size: L</span></span>

<span data-ttu-id="60571-163">État : terminé</span><span class="sxs-lookup"><span data-stu-id="60571-163">Status: Done</span></span>

<span data-ttu-id="60571-164">EF Core 3,0 a modifié le comportement par défaut pour créer une requête SQL unique pour une requête LINQ donnée.</span><span class="sxs-lookup"><span data-stu-id="60571-164">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="60571-165">Cela entraînait des régressions de performances importantes pour les requêtes qui utilisent include pour plusieurs collections.</span><span class="sxs-lookup"><span data-stu-id="60571-165">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="60571-166">Dans EF Core 5,0, nous conservons le nouveau comportement par défaut.</span><span class="sxs-lookup"><span data-stu-id="60571-166">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="60571-167">Toutefois, EF Core 5,0 permet désormais la génération de plusieurs requêtes pour la collection, notamment lorsque l’utilisation d’une seule requête entraîne des performances incorrectes.</span><span class="sxs-lookup"><span data-stu-id="60571-167">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="required-one-to-one-dependents"></a><span data-ttu-id="60571-168">Dépendants un-à-un requis</span><span class="sxs-lookup"><span data-stu-id="60571-168">Required one-to-one dependents</span></span>

<span data-ttu-id="60571-169">Développeurs en chef : @AndriySvyryd et @smitpatel</span><span class="sxs-lookup"><span data-stu-id="60571-169">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="60571-170">Suivi par [#12100](https://github.com/dotnet/efcore/issues/12100)</span><span class="sxs-lookup"><span data-stu-id="60571-170">Tracked by [#12100](https://github.com/dotnet/efcore/issues/12100)</span></span>

<span data-ttu-id="60571-171">Taille de T-shirt : M</span><span class="sxs-lookup"><span data-stu-id="60571-171">T-shirt size: M</span></span>

<span data-ttu-id="60571-172">État : terminé</span><span class="sxs-lookup"><span data-stu-id="60571-172">Status: Done</span></span>

<span data-ttu-id="60571-173">Dans EF Core 3,0, tous les dépendants, y compris les types détenus, sont facultatifs (par exemple, Person. Address peut avoir la valeur null).</span><span class="sxs-lookup"><span data-stu-id="60571-173">In EF Core 3.0, all dependents, including owned types are optional (e.g. Person.Address can be null).</span></span> <span data-ttu-id="60571-174">Dans EF Core 5,0, les dépendants peuvent être configurés en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="60571-174">In EF Core 5.0, dependents can be configured as required.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="60571-175">Rationaliser ToTable, ToQuery, ToView, FromSql, etc.</span><span class="sxs-lookup"><span data-stu-id="60571-175">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="60571-176">Développeurs en chef : @AndriySvyryd et @smitpatel</span><span class="sxs-lookup"><span data-stu-id="60571-176">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="60571-177">Suivi par [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="60571-177">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="60571-178">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-178">T-shirt size: L</span></span>

<span data-ttu-id="60571-179">État : terminé</span><span class="sxs-lookup"><span data-stu-id="60571-179">Status: Done</span></span>

<span data-ttu-id="60571-180">Nous avons progressé dans les versions précédentes jusqu’à la prise en charge de SQL brut, de types sans clé et de zones associées.</span><span class="sxs-lookup"><span data-stu-id="60571-180">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="60571-181">Toutefois, il existe des lacunes et des incohérences dans la façon dont tout fonctionne ensemble dans son ensemble.</span><span class="sxs-lookup"><span data-stu-id="60571-181">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="60571-182">L’objectif de 5,0 est de les corriger et de créer une bonne expérience pour la définition, la migration et l’utilisation de différents types d’entités et de leurs requêtes et artefacts de base de données associés.</span><span class="sxs-lookup"><span data-stu-id="60571-182">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="60571-183">Cela peut également impliquer des mises à jour de l’API de requête compilée.</span><span class="sxs-lookup"><span data-stu-id="60571-183">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="60571-184">Notez que cet élément peut entraîner des modifications avec rupture au niveau de l’application, car certaines des fonctionnalités dont nous disposons actuellement sont trop permissive, ce qui permet à l’utilisateur de mener rapidement des gens sur des problèmes.</span><span class="sxs-lookup"><span data-stu-id="60571-184">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="60571-185">Nous nous contenterons de bloquer certaines de ces fonctionnalités, ainsi que des conseils sur la marche à suivre.</span><span class="sxs-lookup"><span data-stu-id="60571-185">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="60571-186">Améliorations générales relatives aux requêtes</span><span class="sxs-lookup"><span data-stu-id="60571-186">General query enhancements</span></span>

<span data-ttu-id="60571-187">Développeurs en chef : @smitpatel et @maumar</span><span class="sxs-lookup"><span data-stu-id="60571-187">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="60571-188">Suivi par des [problèmes étiquetés avec `area-query` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="60571-188">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="60571-189">Taille de T-shirt : XL</span><span class="sxs-lookup"><span data-stu-id="60571-189">T-shirt size: XL</span></span>

<span data-ttu-id="60571-190">État : terminé</span><span class="sxs-lookup"><span data-stu-id="60571-190">Status: Done</span></span>

<span data-ttu-id="60571-191">Le code de traduction de la requête a été largement réécrit pour EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="60571-191">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="60571-192">Le code de requête est généralement dans un état bien plus robuste.</span><span class="sxs-lookup"><span data-stu-id="60571-192">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="60571-193">Pour 5,0, nous ne prévoyons pas de modifier les requêtes majeures, en dehors de celles qui sont nécessaires pour prendre en charge les propriétés de navigation TPT et Skip.</span><span class="sxs-lookup"><span data-stu-id="60571-193">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="60571-194">Toutefois, il y a toujours un travail important nécessaire pour résoudre une dette technique à partir de la révision 3,0.</span><span class="sxs-lookup"><span data-stu-id="60571-194">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="60571-195">Nous prévoyons également de résoudre de nombreux bogues et d’implémenter de petites améliorations pour améliorer l’expérience globale des requêtes.</span><span class="sxs-lookup"><span data-stu-id="60571-195">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="60571-196">Migrations et expérience de déploiement</span><span class="sxs-lookup"><span data-stu-id="60571-196">Migrations and deployment experience</span></span>

<span data-ttu-id="60571-197">Développeurs en chef : @bricelam</span><span class="sxs-lookup"><span data-stu-id="60571-197">Lead developers: @bricelam</span></span>

<span data-ttu-id="60571-198">Suivi par [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="60571-198">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="60571-199">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-199">T-shirt size: L</span></span>

<span data-ttu-id="60571-200">État : étendues/terminées</span><span class="sxs-lookup"><span data-stu-id="60571-200">Status: Scoped/Done</span></span>

<span data-ttu-id="60571-201">Étendue : la [fonctionnalité de lots de migrations](https://github.com/dotnet/efcore/issues/19693) a été différée jusqu’à la fin de la EF Core version 5,0.</span><span class="sxs-lookup"><span data-stu-id="60571-201">Scoping: The [migrations bundles feature](https://github.com/dotnet/efcore/issues/19693) has been deferred until after the EF Core 5.0 release.</span></span> <span data-ttu-id="60571-202">Toutefois, plusieurs autres [améliorations ciblées relatives aux migrations](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) seront incluses dans EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="60571-202">However, several other [targeted improvements related to migrations](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) will be included in EF Core 5.0</span></span>

<span data-ttu-id="60571-203">Actuellement, de nombreux développeurs migrent leurs bases de données au moment du démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="60571-203">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="60571-204">C’est facile, mais cela n’est pas recommandé car :</span><span class="sxs-lookup"><span data-stu-id="60571-204">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="60571-205">Plusieurs threads/processus/serveurs peuvent tenter de migrer la base de données simultanément</span><span class="sxs-lookup"><span data-stu-id="60571-205">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="60571-206">Les applications peuvent tenter d’accéder à un état incohérent alors que cela se produit</span><span class="sxs-lookup"><span data-stu-id="60571-206">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="60571-207">En général, les autorisations de base de données pour modifier le schéma ne doivent pas être accordées pour l’exécution de l’application</span><span class="sxs-lookup"><span data-stu-id="60571-207">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="60571-208">Il est difficile de revenir à un état propre si un problème se pose</span><span class="sxs-lookup"><span data-stu-id="60571-208">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="60571-209">Nous souhaitons offrir une meilleure expérience ici, qui permet de migrer facilement la base de données au moment du déploiement.</span><span class="sxs-lookup"><span data-stu-id="60571-209">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="60571-210">Cela doit :</span><span class="sxs-lookup"><span data-stu-id="60571-210">This should:</span></span>

* <span data-ttu-id="60571-211">Travailler sur Linux, Mac et Windows</span><span class="sxs-lookup"><span data-stu-id="60571-211">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="60571-212">Soyez une bonne expérience sur la ligne de commande</span><span class="sxs-lookup"><span data-stu-id="60571-212">Be a good experience on the command line</span></span>
* <span data-ttu-id="60571-213">Scénarios de prise en charge avec conteneurs</span><span class="sxs-lookup"><span data-stu-id="60571-213">Support scenarios with containers</span></span>
* <span data-ttu-id="60571-214">Travailler avec des outils/flux de déploiement réels couramment utilisés</span><span class="sxs-lookup"><span data-stu-id="60571-214">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="60571-215">Intégrer dans au moins Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60571-215">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="60571-216">Le résultat est probablement de nombreuses améliorations de EF Core (par exemple, de meilleures migrations sur SQLite), ainsi que des conseils et des collaborations à long terme avec d’autres équipes pour améliorer les expériences de bout en bout qui vont au-delà d’EF.</span><span class="sxs-lookup"><span data-stu-id="60571-216">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="60571-217">Expérience des plates-formes EF Core</span><span class="sxs-lookup"><span data-stu-id="60571-217">EF Core platforms experience</span></span>

<span data-ttu-id="60571-218">Développeurs en chef : @roji et @bricelam</span><span class="sxs-lookup"><span data-stu-id="60571-218">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="60571-219">Suivi par [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="60571-219">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="60571-220">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-220">T-shirt size: L</span></span>

<span data-ttu-id="60571-221">État : étendue/terminé</span><span class="sxs-lookup"><span data-stu-id="60571-221">Status: Scope/Done</span></span>

<span data-ttu-id="60571-222">Portée : des conseils et des exemples de plateforme sont publiés pour éblouissant, Xamarin, WinForms et WPF.</span><span class="sxs-lookup"><span data-stu-id="60571-222">Scoping: Platform guidance and samples are published for Blazor, Xamarin, WinForms, and WPF.</span></span> <span data-ttu-id="60571-223">Xamarin et d’autres travaux de l’AOA/éditeur de liens sont maintenant prévus pour EF Core 6,0.</span><span class="sxs-lookup"><span data-stu-id="60571-223">Xamarin and other AOT/linker work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="60571-224">Nous avons de bonnes recommandations pour l’utilisation de EF Core dans des applications Web classiques basées sur MVC.</span><span class="sxs-lookup"><span data-stu-id="60571-224">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="60571-225">Des conseils pour d’autres plateformes et modèles d’application sont manquants ou obsolètes.</span><span class="sxs-lookup"><span data-stu-id="60571-225">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="60571-226">Pour EF Core 5,0, nous prévoyons d’examiner, d’améliorer et de documenter l’expérience d’utilisation de EF Core avec :</span><span class="sxs-lookup"><span data-stu-id="60571-226">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="60571-227">Blazor</span><span class="sxs-lookup"><span data-stu-id="60571-227">Blazor</span></span>
* <span data-ttu-id="60571-228">Xamarin, y compris l’utilisation de l’AOA/histoire de l’éditeur de liens</span><span class="sxs-lookup"><span data-stu-id="60571-228">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="60571-229">WinForms/WPF/WinUI et éventuellement d’autres U.I.</span><span class="sxs-lookup"><span data-stu-id="60571-229">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="60571-230">frameworks</span><span class="sxs-lookup"><span data-stu-id="60571-230">frameworks</span></span>

<span data-ttu-id="60571-231">Il s’agit probablement de nombreuses améliorations de EF Core, ainsi que de conseils et de collaborations à long terme avec d’autres équipes pour améliorer les expériences de bout en bout qui vont au-delà d’EF.</span><span class="sxs-lookup"><span data-stu-id="60571-231">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="60571-232">Voici quelques-uns des éléments que nous prévoyons de consulter :</span><span class="sxs-lookup"><span data-stu-id="60571-232">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="60571-233">Déploiement, y compris l’expérience d’utilisation des outils EF comme pour les migrations</span><span class="sxs-lookup"><span data-stu-id="60571-233">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="60571-234">Modèles d’application, notamment Xamarin et éblouissant, et probablement d’autres</span><span class="sxs-lookup"><span data-stu-id="60571-234">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="60571-235">Expériences SQLite, y compris l’expérience spatiale et les reconstructions de tables</span><span class="sxs-lookup"><span data-stu-id="60571-235">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="60571-236">AOA et liaison d’expériences</span><span class="sxs-lookup"><span data-stu-id="60571-236">AOT and linking experiences</span></span>
* <span data-ttu-id="60571-237">Intégration des diagnostics, y compris les compteurs de performances</span><span class="sxs-lookup"><span data-stu-id="60571-237">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="60571-238">Performances</span><span class="sxs-lookup"><span data-stu-id="60571-238">Performance</span></span>

<span data-ttu-id="60571-239">Développeur principal : @roji</span><span class="sxs-lookup"><span data-stu-id="60571-239">Lead developer: @roji</span></span>

<span data-ttu-id="60571-240">Suivi par des [problèmes étiquetés avec `area-perf` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="60571-240">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="60571-241">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-241">T-shirt size: L</span></span>

<span data-ttu-id="60571-242">État : étendues/terminées</span><span class="sxs-lookup"><span data-stu-id="60571-242">Status: Scoped/Done</span></span>

<span data-ttu-id="60571-243">Portée : les améliorations de performances majeures dans le fournisseur npgsql sont terminées.</span><span class="sxs-lookup"><span data-stu-id="60571-243">Scoping: Major performance improvements in the Npgsql provider are complete.</span></span> <span data-ttu-id="60571-244">D’autres tâches de performances sont désormais prévues pour EF Core 6,0.</span><span class="sxs-lookup"><span data-stu-id="60571-244">Other performance work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="60571-245">Par EF Core, nous prévoyons d’améliorer notre suite de tests de performances et d’améliorer les performances de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="60571-245">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="60571-246">En outre, nous prévoyons de terminer la nouvelle API de traitement par lot ADO.NET qui a été prototypée durant le cycle de publication de 3,0.</span><span class="sxs-lookup"><span data-stu-id="60571-246">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="60571-247">En outre, au niveau de la couche ADO.NET, nous prévoyons des améliorations de performances supplémentaires pour le fournisseur npgsql.</span><span class="sxs-lookup"><span data-stu-id="60571-247">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="60571-248">Dans le cadre de ce travail, nous prévoyons également d’ajouter des compteurs de performances ADO.NET/EF Core et d’autres diagnostics appropriés.</span><span class="sxs-lookup"><span data-stu-id="60571-248">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="60571-249">Documentation architecturale/contributeur</span><span class="sxs-lookup"><span data-stu-id="60571-249">Architectural/contributor documentation</span></span>

<span data-ttu-id="60571-250">Documentation en chef : @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="60571-250">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="60571-251">Suivi par [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="60571-251">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="60571-252">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-252">T-shirt size: L</span></span>

<span data-ttu-id="60571-253">État : couper</span><span class="sxs-lookup"><span data-stu-id="60571-253">Status: Cut</span></span>

<span data-ttu-id="60571-254">L’idée ici est de faciliter la compréhension de ce qui se passe dans les éléments internes de EF Core.</span><span class="sxs-lookup"><span data-stu-id="60571-254">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="60571-255">Cela peut être utile pour toute personne utilisant EF Core, mais la motivation principale est de faciliter la tâche pour les utilisateurs externes :</span><span class="sxs-lookup"><span data-stu-id="60571-255">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="60571-256">Contribuer au code EF Core</span><span class="sxs-lookup"><span data-stu-id="60571-256">Contribute to the EF Core code</span></span>
* <span data-ttu-id="60571-257">Créer des fournisseurs de base de données</span><span class="sxs-lookup"><span data-stu-id="60571-257">Create database providers</span></span>
* <span data-ttu-id="60571-258">Créer d’autres extensions</span><span class="sxs-lookup"><span data-stu-id="60571-258">Build other extensions</span></span>

<span data-ttu-id="60571-259">Mise à jour : Malheureusement, ce plan était trop ambitieux.</span><span class="sxs-lookup"><span data-stu-id="60571-259">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="60571-260">Nous pensons toujours qu’il s’agit d’un élément important, mais ce n’est malheureusement pas le EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="60571-260">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="60571-261">Documentation de Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="60571-261">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="60571-262">Documentation en chef : @bricelam</span><span class="sxs-lookup"><span data-stu-id="60571-262">Lead documenter: @bricelam</span></span>

<span data-ttu-id="60571-263">Suivi par [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="60571-263">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="60571-264">Taille de T-shirt : M</span><span class="sxs-lookup"><span data-stu-id="60571-264">T-shirt size: M</span></span>

<span data-ttu-id="60571-265">État : terminé.</span><span class="sxs-lookup"><span data-stu-id="60571-265">Status: Completed.</span></span> <span data-ttu-id="60571-266">La nouvelle documentation est [en ligne sur le site Microsoft docs](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span><span class="sxs-lookup"><span data-stu-id="60571-266">The new documentation is [live on the Microsoft docs site](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="60571-267">L’équipe EF possède également le fournisseur ADO.NET de Microsoft. Data. sqlite.</span><span class="sxs-lookup"><span data-stu-id="60571-267">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="60571-268">Nous prévoyons de documenter entièrement ce fournisseur dans le cadre de la version 5,0.</span><span class="sxs-lookup"><span data-stu-id="60571-268">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="60571-269">Documentation générale</span><span class="sxs-lookup"><span data-stu-id="60571-269">General documentation</span></span>

<span data-ttu-id="60571-270">Documentation en chef : @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="60571-270">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="60571-271">Suivi par des [problèmes dans la documentation référentiel dans le jalon 5,0](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="60571-271">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="60571-272">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-272">T-shirt size: L</span></span>

<span data-ttu-id="60571-273">État : en cours</span><span class="sxs-lookup"><span data-stu-id="60571-273">Status: In-progress</span></span>

<span data-ttu-id="60571-274">Nous sommes déjà en train de mettre à jour la documentation pour les versions 3,0 et 3,1.</span><span class="sxs-lookup"><span data-stu-id="60571-274">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="60571-275">Nous travaillons également sur :</span><span class="sxs-lookup"><span data-stu-id="60571-275">We are also working on:</span></span>

* <span data-ttu-id="60571-276">Une révision des documents de prise en main pour les rendre plus simples et plus faciles à suivre</span><span class="sxs-lookup"><span data-stu-id="60571-276">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="60571-277">Réorganisation de docs pour faciliter la recherche et ajouter des références croisées</span><span class="sxs-lookup"><span data-stu-id="60571-277">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="60571-278">Ajout de détails et de clarifications à des documents existants</span><span class="sxs-lookup"><span data-stu-id="60571-278">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="60571-279">Mise à jour des exemples et ajout d’autres exemples</span><span class="sxs-lookup"><span data-stu-id="60571-279">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="60571-280">Correction des bogues</span><span class="sxs-lookup"><span data-stu-id="60571-280">Fixing bugs</span></span>

<span data-ttu-id="60571-281">Suivi par des [problèmes étiquetés avec `type-bug` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="60571-281">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="60571-282">Développeurs : @roji , @maumar ,,, @bricelam @smitpatel @AndriySvyryd , @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="60571-282">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="60571-283">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-283">T-shirt size: L</span></span>

<span data-ttu-id="60571-284">État : en cours</span><span class="sxs-lookup"><span data-stu-id="60571-284">Status: In-progress</span></span>

<span data-ttu-id="60571-285">Au moment de la rédaction de ce document, nous avons 135 bogues en triage pour qu’ils soient corrigés dans la version 5,0 (avec 62 déjà corrigé), mais il y a un chevauchement significatif avec la section sur les _améliorations générales des requêtes_ ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="60571-285">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="60571-286">Le taux entrant (les problèmes qui se terminent par un travail dans une étape majeure) était d’environ 23 problèmes par mois au cours de la version 3,0.</span><span class="sxs-lookup"><span data-stu-id="60571-286">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="60571-287">Ces derniers ne doivent pas nécessairement être corrigés dans 5,0.</span><span class="sxs-lookup"><span data-stu-id="60571-287">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="60571-288">En guise d’estimation approximative, nous prévoyons de résoudre les problèmes 150 supplémentaires dans le délai de 5,0.</span><span class="sxs-lookup"><span data-stu-id="60571-288">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="60571-289">Petites améliorations</span><span class="sxs-lookup"><span data-stu-id="60571-289">Small enhancements</span></span>

<span data-ttu-id="60571-290">Suivi par des [problèmes étiquetés avec `type-enhancement` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="60571-290">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="60571-291">Développeurs : @roji , @maumar ,,, @bricelam @smitpatel @AndriySvyryd , @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="60571-291">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="60571-292">Taille de T-shirt : L</span><span class="sxs-lookup"><span data-stu-id="60571-292">T-shirt size: L</span></span>

<span data-ttu-id="60571-293">État : terminé</span><span class="sxs-lookup"><span data-stu-id="60571-293">Status: Done</span></span>

<span data-ttu-id="60571-294">En plus des fonctionnalités plus grandes décrites ci-dessus, nous avons également de nombreuses améliorations plus petites, planifiées pour 5,0, afin de corriger les « coupes papier ».</span><span class="sxs-lookup"><span data-stu-id="60571-294">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="60571-295">Notez que la plupart de ces améliorations sont également couvertes par les thèmes plus généraux décrits ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="60571-295">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="60571-296">En dessous de la ligne</span><span class="sxs-lookup"><span data-stu-id="60571-296">Below-the-line</span></span>

<span data-ttu-id="60571-297">Suivi par des [problèmes étiquetés `consider-for-next-release` avec](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="60571-297">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="60571-298">Ce sont des correctifs de bogues et des améliorations qui **ne sont pas** actuellement planifiés pour la version 5,0, mais nous examinerons les objectifs d’étirement en fonction de la progression de la tâche ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="60571-298">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="60571-299">En outre, nous considérons toujours les [problèmes les plus votés](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) lors de la planification.</span><span class="sxs-lookup"><span data-stu-id="60571-299">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="60571-300">La réduction de l’un de ces problèmes à partir d’une version est toujours pénible, mais nous avons besoin d’un plan réaliste pour les ressources dont nous disposons.</span><span class="sxs-lookup"><span data-stu-id="60571-300">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="60571-301">Suggestions</span><span class="sxs-lookup"><span data-stu-id="60571-301">Suggestions</span></span>

<span data-ttu-id="60571-302">Vos commentaires sur la planification sont importants.</span><span class="sxs-lookup"><span data-stu-id="60571-302">Your feedback on planning is important.</span></span> <span data-ttu-id="60571-303">La meilleure façon d’indiquer l’importance d’un problème est de voter (pouce vers le haut) pour ce problème sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="60571-303">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="60571-304">Ces données sont ensuite chargées dans le [processus de planification](xref:core/what-is-new/release_planning) de la prochaine version.</span><span class="sxs-lookup"><span data-stu-id="60571-304">This data will then feed into the [planning process](xref:core/what-is-new/release_planning) for the next release.</span></span>
