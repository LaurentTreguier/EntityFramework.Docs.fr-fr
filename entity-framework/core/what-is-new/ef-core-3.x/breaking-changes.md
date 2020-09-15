---
title: Dernières modifications apportées à EF Core 3. x-EF Core
description: Liste complète des modifications avec rupture introduites dans Entity Framework Core 3. x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: e348cb630d91ebe4536b73b9a7bd9a7b6a46db79
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072237"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="203d4-103">Modifications avec rupture incluses dans EF Core 3. x</span><span class="sxs-lookup"><span data-stu-id="203d4-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="203d4-104">Les modifications d’API et de comportement suivantes peuvent bloquer les applications existantes lors de leur mise à niveau vers 3. x.</span><span class="sxs-lookup"><span data-stu-id="203d4-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="203d4-105">Les changements qui, selon nous, auront une incidence uniquement sur les fournisseurs de base de données sont documentés dans [Changements ayant un impact sur les fournisseurs](xref:core/providers/provider-log).</span><span class="sxs-lookup"><span data-stu-id="203d4-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="203d4-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="203d4-106">Summary</span></span>

| <span data-ttu-id="203d4-107">**Modification critique**</span><span class="sxs-lookup"><span data-stu-id="203d4-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="203d4-108">**Impact**</span><span class="sxs-lookup"><span data-stu-id="203d4-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="203d4-109">Les requêtes LINQ ne sont plus évaluées sur le client</span><span class="sxs-lookup"><span data-stu-id="203d4-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="203d4-110">Élevé</span><span class="sxs-lookup"><span data-stu-id="203d4-110">High</span></span>       |
| [<span data-ttu-id="203d4-111">EF Core 3.0 cible .NET Standard 2.1 plutôt que .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="203d4-111">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="203d4-112">Élevé</span><span class="sxs-lookup"><span data-stu-id="203d4-112">High</span></span>      |
| [<span data-ttu-id="203d4-113">L’outil en ligne de commande EF Core, dotnet ef, ne fait plus partie du SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="203d4-113">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="203d4-114">Élevé</span><span class="sxs-lookup"><span data-stu-id="203d4-114">High</span></span>      |
| [<span data-ttu-id="203d4-115">DetectChanges honore les valeurs de clés générées par le magasin</span><span class="sxs-lookup"><span data-stu-id="203d4-115">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="203d4-116">Élevé</span><span class="sxs-lookup"><span data-stu-id="203d4-116">High</span></span>      |
| [<span data-ttu-id="203d4-117">FromSql, ExecuteSql et ExecuteSqlAsync ont été renommés</span><span class="sxs-lookup"><span data-stu-id="203d4-117">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="203d4-118">Élevé</span><span class="sxs-lookup"><span data-stu-id="203d4-118">High</span></span>      |
| [<span data-ttu-id="203d4-119">Les types de requêtes sont regroupés avec les types d’entités</span><span class="sxs-lookup"><span data-stu-id="203d4-119">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="203d4-120">Élevé</span><span class="sxs-lookup"><span data-stu-id="203d4-120">High</span></span>      |
| [<span data-ttu-id="203d4-121">Entity Framework Core ne fait plus partie du framework partagé ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="203d4-121">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="203d4-122">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-122">Medium</span></span>      |
| [<span data-ttu-id="203d4-123">Les suppressions en cascade se produisent désormais immédiatement par défaut</span><span class="sxs-lookup"><span data-stu-id="203d4-123">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="203d4-124">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-124">Medium</span></span>      |
| [<span data-ttu-id="203d4-125">Le chargement hâtif des entités associées se produit désormais dans une seule requête</span><span class="sxs-lookup"><span data-stu-id="203d4-125">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="203d4-126">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-126">Medium</span></span>      |
| [<span data-ttu-id="203d4-127">La sémantique de DeleteBehavior.Restrict est désormais plus propre</span><span class="sxs-lookup"><span data-stu-id="203d4-127">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="203d4-128">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-128">Medium</span></span>      |
| [<span data-ttu-id="203d4-129">L’API de configuration pour les relations de type détenu a changé</span><span class="sxs-lookup"><span data-stu-id="203d4-129">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="203d4-130">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-130">Medium</span></span>      |
| [<span data-ttu-id="203d4-131">Chaque propriété utilise la génération indépendante de clé de type entier en mémoire</span><span class="sxs-lookup"><span data-stu-id="203d4-131">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="203d4-132">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-132">Medium</span></span>      |
| [<span data-ttu-id="203d4-133">Les requêtes sans suivi ne procèdent plus à la résolution de l’identité</span><span class="sxs-lookup"><span data-stu-id="203d4-133">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="203d4-134">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-134">Medium</span></span>      |
| [<span data-ttu-id="203d4-135">Changements apportés à l’API de métadonnées</span><span class="sxs-lookup"><span data-stu-id="203d4-135">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="203d4-136">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-136">Medium</span></span>      |
| [<span data-ttu-id="203d4-137">Modifications de l’API de métadonnées spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="203d4-137">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="203d4-138">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-138">Medium</span></span>      |
| [<span data-ttu-id="203d4-139">UseRowNumberForPaging a été supprimé</span><span class="sxs-lookup"><span data-stu-id="203d4-139">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="203d4-140">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-140">Medium</span></span>      |
| [<span data-ttu-id="203d4-141">La méthode FromSql quand elle est utilisée avec une procédure stockée ne peut pas être composée</span><span class="sxs-lookup"><span data-stu-id="203d4-141">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="203d4-142">Moyenne</span><span class="sxs-lookup"><span data-stu-id="203d4-142">Medium</span></span>      |
| [<span data-ttu-id="203d4-143">Les méthodes FromSql peuvent uniquement être spécifiées sur les racines de requête</span><span class="sxs-lookup"><span data-stu-id="203d4-143">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="203d4-144">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-144">Low</span></span>      |
| [<span data-ttu-id="203d4-145">~~L’exécution de requêtes est enregistrée au niveau du débogage ~~Rétabli</span><span class="sxs-lookup"><span data-stu-id="203d4-145">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="203d4-146">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-146">Low</span></span>      |
| [<span data-ttu-id="203d4-147">Les valeurs de clés temporaires ne sont plus définies sur les instances d’entités</span><span class="sxs-lookup"><span data-stu-id="203d4-147">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="203d4-148">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-148">Low</span></span>      |
| [<span data-ttu-id="203d4-149">Les entités dépendantes qui partagent la table avec le principal sont maintenant facultatives</span><span class="sxs-lookup"><span data-stu-id="203d4-149">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="203d4-150">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-150">Low</span></span>      |
| [<span data-ttu-id="203d4-151">Toutes les entités qui partagent une table avec une colonne de jeton de concurrence doivent la mapper à une propriété</span><span class="sxs-lookup"><span data-stu-id="203d4-151">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="203d4-152">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-152">Low</span></span>      |
| [<span data-ttu-id="203d4-153">Les entités détenues ne peuvent pas être interrogées sans le propriétaire à l’aide d’une requête de suivi</span><span class="sxs-lookup"><span data-stu-id="203d4-153">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="203d4-154">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-154">Low</span></span>      |
| [<span data-ttu-id="203d4-155">Les propriétés héritées de types non mappés sont maintenant mappées à une seule colonne pour tous les types dérivés</span><span class="sxs-lookup"><span data-stu-id="203d4-155">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="203d4-156">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-156">Low</span></span>      |
| [<span data-ttu-id="203d4-157">La convention de propriété de clé étrangère ne correspond plus au nom de la propriété principale</span><span class="sxs-lookup"><span data-stu-id="203d4-157">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="203d4-158">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-158">Low</span></span>      |
| [<span data-ttu-id="203d4-159">La connexion de base de données est maintenant fermée si elle n’est plus utilisée avant la fin de TransactionScope</span><span class="sxs-lookup"><span data-stu-id="203d4-159">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="203d4-160">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-160">Low</span></span>      |
| [<span data-ttu-id="203d4-161">Les champs de stockage sont utilisés par défaut</span><span class="sxs-lookup"><span data-stu-id="203d4-161">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="203d4-162">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-162">Low</span></span>      |
| [<span data-ttu-id="203d4-163">Erreur si plusieurs champs de stockage compatibles sont détectés</span><span class="sxs-lookup"><span data-stu-id="203d4-163">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="203d4-164">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-164">Low</span></span>      |
| [<span data-ttu-id="203d4-165">Les noms de propriété de type « champ uniquement » doivent correspondre au nom du champ</span><span class="sxs-lookup"><span data-stu-id="203d4-165">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="203d4-166">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-166">Low</span></span>      |
| [<span data-ttu-id="203d4-167">AddDbContext/AddDbContextPool n’appelle plus AddLogging et AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="203d4-167">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="203d4-168">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-168">Low</span></span>      |
| [<span data-ttu-id="203d4-169">AddEntityFramework \* ajoute IMemoryCache avec une limite de taille</span><span class="sxs-lookup"><span data-stu-id="203d4-169">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="203d4-170">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-170">Low</span></span>      |
| [<span data-ttu-id="203d4-171">DbContext.Entry effectue maintenant un DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="203d4-171">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="203d4-172">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-172">Low</span></span>      |
| [<span data-ttu-id="203d4-173">Les clés de tableaux d’octets et de chaînes ne sont pas générés par client par défaut</span><span class="sxs-lookup"><span data-stu-id="203d4-173">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="203d4-174">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-174">Low</span></span>      |
| [<span data-ttu-id="203d4-175">ILoggerFactory est désormais un service délimité</span><span class="sxs-lookup"><span data-stu-id="203d4-175">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="203d4-176">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-176">Low</span></span>      |
| [<span data-ttu-id="203d4-177">Les proxys à chargement différé ne partent plus du principe que les propriétés de navigation sont entièrement chargées</span><span class="sxs-lookup"><span data-stu-id="203d4-177">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="203d4-178">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-178">Low</span></span>      |
| [<span data-ttu-id="203d4-179">La création excessive de fournisseurs de services internes est maintenant une erreur par défaut</span><span class="sxs-lookup"><span data-stu-id="203d4-179">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="203d4-180">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-180">Low</span></span>      |
| [<span data-ttu-id="203d4-181">Nouveau comportement de HasOne/HasMany appelé avec une chaîne unique</span><span class="sxs-lookup"><span data-stu-id="203d4-181">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="203d4-182">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-182">Low</span></span>      |
| [<span data-ttu-id="203d4-183">Le type de retour Task pour plusieurs méthodes asynchrones a été remplacé par ValueTask</span><span class="sxs-lookup"><span data-stu-id="203d4-183">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="203d4-184">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-184">Low</span></span>      |
| [<span data-ttu-id="203d4-185">L’annotation Relational:TypeMapping est désormais simplement TypeMapping</span><span class="sxs-lookup"><span data-stu-id="203d4-185">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="203d4-186">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-186">Low</span></span>      |
| [<span data-ttu-id="203d4-187">ToTable sur un type dérivé lève une exception</span><span class="sxs-lookup"><span data-stu-id="203d4-187">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="203d4-188">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-188">Low</span></span>      |
| [<span data-ttu-id="203d4-189">EF Core n’envoie plus de pragma pour l’application de clé étrangère SQLite</span><span class="sxs-lookup"><span data-stu-id="203d4-189">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="203d4-190">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-190">Low</span></span>      |
| [<span data-ttu-id="203d4-191">Microsoft.EntityFrameworkCore.Sqlite dépend désormais de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="203d4-191">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="203d4-192">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-192">Low</span></span>      |
| [<span data-ttu-id="203d4-193">Les valeurs GUID sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="203d4-193">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="203d4-194">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-194">Low</span></span>      |
| [<span data-ttu-id="203d4-195">Les valeurs char sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="203d4-195">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="203d4-196">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-196">Low</span></span>      |
| [<span data-ttu-id="203d4-197">Les ID de migration sont maintenant générés à l’aide du calendrier de la culture invariante</span><span class="sxs-lookup"><span data-stu-id="203d4-197">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="203d4-198">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-198">Low</span></span>      |
| [<span data-ttu-id="203d4-199">Les infos/métadonnées d’extension ont été supprimées de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="203d4-199">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="203d4-200">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-200">Low</span></span>      |
| [<span data-ttu-id="203d4-201">LogQueryPossibleExceptionWithAggregateOperator a été renommé</span><span class="sxs-lookup"><span data-stu-id="203d4-201">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="203d4-202">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-202">Low</span></span>      |
| [<span data-ttu-id="203d4-203">Clarifier les noms de contrainte de clé étrangère dans l’API</span><span class="sxs-lookup"><span data-stu-id="203d4-203">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="203d4-204">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-204">Low</span></span>      |
| [<span data-ttu-id="203d4-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync ont été rendues publiques</span><span class="sxs-lookup"><span data-stu-id="203d4-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="203d4-206">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-206">Low</span></span>      |
| [<span data-ttu-id="203d4-207">Microsoft.EntityFrameworkCore.Design est désormais un package DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="203d4-207">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="203d4-208">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-208">Low</span></span>      |
| [<span data-ttu-id="203d4-209">SQLitePCL.raw mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="203d4-209">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="203d4-210">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-210">Low</span></span>      |
| [<span data-ttu-id="203d4-211">NetTopologySuite mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="203d4-211">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="203d4-212">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-212">Low</span></span>      |
| [<span data-ttu-id="203d4-213">Microsoft. Data. SqlClient est utilisé à la place de System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="203d4-213">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="203d4-214">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-214">Low</span></span>      |
| [<span data-ttu-id="203d4-215">Plusieurs relations autoréférencées ambiguës doivent être configurées</span><span class="sxs-lookup"><span data-stu-id="203d4-215">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="203d4-216">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-216">Low</span></span>      |
| [<span data-ttu-id="203d4-217">DbFunction. Schema étant null ou une chaîne vide, il est configuré pour être dans le schéma par défaut du modèle</span><span class="sxs-lookup"><span data-stu-id="203d4-217">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="203d4-218">Faible</span><span class="sxs-lookup"><span data-stu-id="203d4-218">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="203d4-219">Les requêtes LINQ ne sont plus évaluées sur le client</span><span class="sxs-lookup"><span data-stu-id="203d4-219">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="203d4-220">#14935 du problème de [suivi](https://github.com/aspnet/EntityFrameworkCore/issues/14935) 
 [Consultez également le #12795 du problème](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="203d4-220">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="203d4-221">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-221">**Old behavior**</span></span>

<span data-ttu-id="203d4-222">Avant la version 3.0, quand EF Core ne pouvait pas convertir une expression faisant partie d’une requête en SQL ou en paramètre, elle évaluait automatiquement l’expression sur le client.</span><span class="sxs-lookup"><span data-stu-id="203d4-222">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="203d4-223">Par défaut, l’évaluation sur le client d’expressions potentiellement coûteuses déclenchait uniquement un avertissement.</span><span class="sxs-lookup"><span data-stu-id="203d4-223">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="203d4-224">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-224">**New behavior**</span></span>

<span data-ttu-id="203d4-225">À compter de la version 3.0, EF Core autorise uniquement l’évaluation sur le client des expressions qui figurent dans la projection de niveau supérieur (le dernier appel `Select()` dans la requête).</span><span class="sxs-lookup"><span data-stu-id="203d4-225">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="203d4-226">Quand des expressions d’une autre partie de la requête ne peuvent pas être converties en SQL ou en paramètre, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="203d4-226">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="203d4-227">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-227">**Why**</span></span>

<span data-ttu-id="203d4-228">L’évaluation automatique des requêtes sur le client permet à de nombreuses requêtes d’être exécutées même si certaines de leurs parties importantes ne peuvent pas être traduites.</span><span class="sxs-lookup"><span data-stu-id="203d4-228">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="203d4-229">Ce comportement peut entraîner un comportement inattendu et potentiellement dangereux qui peut devenir évident uniquement en production.</span><span class="sxs-lookup"><span data-stu-id="203d4-229">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="203d4-230">Par exemple, une condition dans un appel `Where()` qui ne peut pas être traduite peut provoquer le transfert de toutes les lignes de la table hors du serveur de base de données, et l’application du filtre sur le client.</span><span class="sxs-lookup"><span data-stu-id="203d4-230">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="203d4-231">Cette situation peut facilement passer inaperçue si la table contient uniquement quelques lignes lors du développement, mais poser davantage de problèmes quand l’application passe en production, où la table peut contenir plusieurs millions de lignes.</span><span class="sxs-lookup"><span data-stu-id="203d4-231">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="203d4-232">Les avertissements relatifs à l’évaluation sur le client étaient également trop faciles à ignorer pendant le développement.</span><span class="sxs-lookup"><span data-stu-id="203d4-232">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="203d4-233">De plus, l’évaluation automatique sur le client peut entraîner des problèmes selon lesquels l’amélioration de la traduction des requêtes pour des expressions spécifiques provoque un changement cassant involontaire entre les versions.</span><span class="sxs-lookup"><span data-stu-id="203d4-233">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="203d4-234">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-234">**Mitigations**</span></span>

<span data-ttu-id="203d4-235">Si une requête ne peut pas être entièrement traduite, réécrivez-la sous une forme qui peut être traduite ou utilisez `AsEnumerable()`, `ToList()` ou autre méthode similaire pour réimporter explicitement les données sur le client, où elles peuvent ensuite être traitées à l’aide de LINQ-to-Objects.</span><span class="sxs-lookup"><span data-stu-id="203d4-235">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="203d4-236">EF Core 3.0 cible .NET Standard 2.1 plutôt que .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="203d4-236">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

<span data-ttu-id="203d4-237">Suivi de problème no 15498</span><span class="sxs-lookup"><span data-stu-id="203d4-237">[Tracking Issue #15498](https://github.com/aspnet/EntityFrameworkCore/issues/15498)</span></span>

> [!IMPORTANT] 
> <span data-ttu-id="203d4-238">EF Core 3,1 cible .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="203d4-238">EF Core 3.1 targets .NET Standard 2.0 again.</span></span> <span data-ttu-id="203d4-239">La prise en charge de .NET Framework est alors rétablie.</span><span class="sxs-lookup"><span data-stu-id="203d4-239">This brings back support for .NET Framework.</span></span>

<span data-ttu-id="203d4-240">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-240">**Old behavior**</span></span>

<span data-ttu-id="203d4-241">Avant la version 3.0, EF Core ciblait .NET Standard 2.0 et s’exécutait sur toutes les plateformes qui prennent en charge cette norme, y compris .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="203d4-241">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="203d4-242">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-242">**New behavior**</span></span>

<span data-ttu-id="203d4-243">À partir de la version 3.0, EF Core cible .NET Standard 2.1 et s’exécute sur toutes les plateformes qui prennent en charge cette norme.</span><span class="sxs-lookup"><span data-stu-id="203d4-243">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="203d4-244">Cela n'inclut pas .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="203d4-244">This does not include .NET Framework.</span></span>

<span data-ttu-id="203d4-245">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-245">**Why**</span></span>

<span data-ttu-id="203d4-246">Cela fait partie d’une décision stratégique dans les technologies .NET de concentrer l’énergie sur .NET Core et d’autres plateformes .NET modernes, telles que Xamarin.</span><span class="sxs-lookup"><span data-stu-id="203d4-246">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="203d4-247">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-247">**Mitigations**</span></span>

<span data-ttu-id="203d4-248">Utilisez EF Core 3,1.</span><span class="sxs-lookup"><span data-stu-id="203d4-248">Use EF Core 3.1.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="203d4-249">Entity Framework Core ne fait plus partie du framework partagé ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="203d4-249">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="203d4-250">Annonces de suivi du problème n° 325</span><span class="sxs-lookup"><span data-stu-id="203d4-250">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="203d4-251">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-251">**Old behavior**</span></span>

<span data-ttu-id="203d4-252">Avant ASP.NET Core 3.0, quand vous ajoutiez une référence de package à `Microsoft.AspNetCore.App` ou `Microsoft.AspNetCore.All`, elle incluait EF Core et certains des fournisseurs de données EF Core tels que le fournisseur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="203d4-252">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="203d4-253">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-253">**New behavior**</span></span>

<span data-ttu-id="203d4-254">À compter de la version 3.0, le framework partagé ASP.NET Core n’inclut ni EF Core, ni aucun fournisseur de données EF Core.</span><span class="sxs-lookup"><span data-stu-id="203d4-254">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="203d4-255">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-255">**Why**</span></span>

<span data-ttu-id="203d4-256">Avant ce changement, l’obtention d’EF Core nécessitait différentes étapes selon que l’application ciblait ASP.NET Core et SQL Server ou non.</span><span class="sxs-lookup"><span data-stu-id="203d4-256">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="203d4-257">De plus, la mise à niveau d’ASP.NET Core forçait la mise à niveau d’EF Core et du fournisseur SQL Server, ce qui n’est pas toujours souhaitable.</span><span class="sxs-lookup"><span data-stu-id="203d4-257">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="203d4-258">Avec ce changement, l’expérience d’obtention d’EF Core est la même pour tous les fournisseurs, implémentations .NET prises en charge et types d’applications.</span><span class="sxs-lookup"><span data-stu-id="203d4-258">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="203d4-259">Les développeurs peuvent désormais contrôler exactement quand EF Core et les fournisseurs de données EF Core sont mis à niveau.</span><span class="sxs-lookup"><span data-stu-id="203d4-259">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="203d4-260">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-260">**Mitigations**</span></span>

<span data-ttu-id="203d4-261">Pour utiliser EF Core dans une application ASP.NET Core 3.0 ou toute autre application prise en charge, ajoutez explicitement une référence de package au fournisseur de base de données EF Core que votre application utilisera.</span><span class="sxs-lookup"><span data-stu-id="203d4-261">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="203d4-262">L’outil en ligne de commande EF Core, dotnet ef, ne fait plus partie du SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="203d4-262">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="203d4-263">Suivi du problème n° 14016</span><span class="sxs-lookup"><span data-stu-id="203d4-263">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="203d4-264">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-264">**Old behavior**</span></span>

<span data-ttu-id="203d4-265">Avant la version 3.0, l’outil `dotnet ef` était inclus dans le SDK .NET Core et prêt à l’emploi depuis la ligne de commande d’un projet sans nécessiter d’étapes supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="203d4-265">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="203d4-266">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-266">**New behavior**</span></span>

<span data-ttu-id="203d4-267">À compter de la version 3.0, le SDK .NET n’inclut pas l’outil `dotnet ef`, donc vous pouvez explicitement l’installer pour pouvoir l’utiliser comme outil local ou global.</span><span class="sxs-lookup"><span data-stu-id="203d4-267">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="203d4-268">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-268">**Why**</span></span>

<span data-ttu-id="203d4-269">Ce changement nous permet de distribuer et mettre à jour `dotnet ef` sous forme d’outil CLI .NET normal sur NuGet, ce qui est cohérent avec le fait qu’EF Core 3.0 est également toujours distribué sous forme de package NuGet.</span><span class="sxs-lookup"><span data-stu-id="203d4-269">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="203d4-270">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-270">**Mitigations**</span></span>

<span data-ttu-id="203d4-271">Pour pouvoir gérer des migrations ou générer un `DbContext`, installez `dotnet-ef` comme outil général :</span><span class="sxs-lookup"><span data-stu-id="203d4-271">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

<span data-ttu-id="203d4-272">Vous pouvez également obtenir un outil local quand vous restaurez les dépendances d’un projet qui le déclare en tant que dépendance d’outil à l’aide d’un [fichier manifeste d’outil](/dotnet/core/tools/global-tools#install-a-local-tool).</span><span class="sxs-lookup"><span data-stu-id="203d4-272">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="203d4-273">FromSql, ExecuteSql et ExecuteSqlAsync ont été renommés</span><span class="sxs-lookup"><span data-stu-id="203d4-273">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="203d4-274">Suivi du problème n<c0>o </c0>10996</span><span class="sxs-lookup"><span data-stu-id="203d4-274">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="203d4-275">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-275">**Old behavior**</span></span>

<span data-ttu-id="203d4-276">Avant EF Core 3.0, ces noms de méthode étaient surchargés pour être utilisés avec une chaîne normale ou une chaîne devant être interpolée dans SQL et dans des paramètres.</span><span class="sxs-lookup"><span data-stu-id="203d4-276">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="203d4-277">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-277">**New behavior**</span></span>

<span data-ttu-id="203d4-278">À compter d’EF Core 3.0, utilisez `FromSqlRaw`, `ExecuteSqlRaw` et `ExecuteSqlRawAsync` pour créer une requête paramétrable, où les paramètres sont passés séparément à partir de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="203d4-278">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="203d4-279">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-279">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="203d4-280">Utilisez `FromSqlInterpolated`, `ExecuteSqlInterpolated` et `ExecuteSqlInterpolatedAsync` pour créer une requête paramétrable, où les paramètres sont passés dans le cadre d’une chaîne de requête interpolée.</span><span class="sxs-lookup"><span data-stu-id="203d4-280">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="203d4-281">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-281">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="203d4-282">Notez que les deux requêtes ci-dessus produisent le même SQL paramétrable avec les mêmes paramètres SQL.</span><span class="sxs-lookup"><span data-stu-id="203d4-282">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="203d4-283">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-283">**Why**</span></span>

<span data-ttu-id="203d4-284">Les surcharges de méthode comme celle-ci rendent très facile un appel accidentel à la méthode de chaîne brute quand l’intention est d’appeler la méthode de chaîne interpolée, et inversement.</span><span class="sxs-lookup"><span data-stu-id="203d4-284">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="203d4-285">De ce fait, les requêtes ne sont plus paramétrables alors qu’elles devraient l’être.</span><span class="sxs-lookup"><span data-stu-id="203d4-285">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="203d4-286">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-286">**Mitigations**</span></span>

<span data-ttu-id="203d4-287">Utilisez plutôt les nouveaux noms de méthode.</span><span class="sxs-lookup"><span data-stu-id="203d4-287">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="203d4-288">La méthode FromSql quand elle est utilisée avec une procédure stockée ne peut pas être composée</span><span class="sxs-lookup"><span data-stu-id="203d4-288">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="203d4-289">#15392 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="203d4-289">Tracking Issue #15392</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

<span data-ttu-id="203d4-290">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-290">**Old behavior**</span></span>

<span data-ttu-id="203d4-291">Avant le EF Core 3,0, la méthode FromSql a tenté de détecter si le SQL passé peut être composé.</span><span class="sxs-lookup"><span data-stu-id="203d4-291">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="203d4-292">Il a fait l’évaluation du client lorsque le SQL n’était pas composable comme une procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="203d4-292">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="203d4-293">La requête suivante a fonctionné en exécutant la procédure stockée sur le serveur et en procédant à l’opération FirstOrDefault côté client.</span><span class="sxs-lookup"><span data-stu-id="203d4-293">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

<span data-ttu-id="203d4-294">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-294">**New behavior**</span></span>

<span data-ttu-id="203d4-295">À compter de EF Core 3,0, EF Core n’essaiera pas d’analyser le SQL.</span><span class="sxs-lookup"><span data-stu-id="203d4-295">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="203d4-296">Par conséquent, si vous effectuez une composition après FromSqlRaw/FromSqlInterpolated, EF Core compose le SQL en provoquant une sous-requête.</span><span class="sxs-lookup"><span data-stu-id="203d4-296">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="203d4-297">Par conséquent, si vous utilisez une procédure stockée avec la composition, vous obtiendrez une exception pour la syntaxe SQL non valide.</span><span class="sxs-lookup"><span data-stu-id="203d4-297">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

<span data-ttu-id="203d4-298">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-298">**Why**</span></span>

<span data-ttu-id="203d4-299">EF Core 3,0 ne prend pas en charge l’évaluation automatique du client, car il s’agit d’une erreur, comme expliqué [ici](#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="203d4-299">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

<span data-ttu-id="203d4-300">**Atténuation**</span><span class="sxs-lookup"><span data-stu-id="203d4-300">**Mitigation**</span></span>

<span data-ttu-id="203d4-301">Si vous utilisez une procédure stockée dans FromSqlRaw/FromSqlInterpolated, vous savez qu’elle ne peut pas être composée. vous pouvez donc ajouter __AsEnumerable/AsAsyncEnumerable__ juste après l’appel de la méthode FromSql pour éviter toute composition côté serveur.</span><span class="sxs-lookup"><span data-stu-id="203d4-301">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="203d4-302">Les méthodes FromSql peuvent uniquement être spécifiées sur les racines de requête</span><span class="sxs-lookup"><span data-stu-id="203d4-302">FromSql methods can only be specified on query roots</span></span>

<span data-ttu-id="203d4-303">Suivi de problème no 15704</span><span class="sxs-lookup"><span data-stu-id="203d4-303">[Tracking Issue #15704](https://github.com/aspnet/EntityFrameworkCore/issues/15704)</span></span>

<span data-ttu-id="203d4-304">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-304">**Old behavior**</span></span>

<span data-ttu-id="203d4-305">Avant EF Core 3.0, la méthode `FromSql` pouvant être spécifiée n’importe où dans la requête.</span><span class="sxs-lookup"><span data-stu-id="203d4-305">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="203d4-306">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-306">**New behavior**</span></span>

<span data-ttu-id="203d4-307">À partir d’EF Core 3.0, les nouvelles méthodes `FromSqlRaw` et `FromSqlInterpolated` (qui remplacent `FromSql`) peuvent être spécifiées uniquement sur les racines de requête, par exemple, directement sur le `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="203d4-307">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="203d4-308">Une erreur de compilation survient si vous tentez de les spécifier à un autre emplacement.</span><span class="sxs-lookup"><span data-stu-id="203d4-308">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="203d4-309">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-309">**Why**</span></span>

<span data-ttu-id="203d4-310">La spécification de `FromSql` n’importe où autre que sur un `DbSet` n’avait aucune signification ni valeur ajoutée et pouvait entraîner une ambiguïté dans certains scénarios.</span><span class="sxs-lookup"><span data-stu-id="203d4-310">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="203d4-311">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-311">**Mitigations**</span></span>

<span data-ttu-id="203d4-312">Les appels `FromSql` doivent être déplacés pour être directement sur le `DbSet` auquel ils s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="203d4-312">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="203d4-313">Les requêtes sans suivi ne procèdent plus à la résolution de l’identité</span><span class="sxs-lookup"><span data-stu-id="203d4-313">No-tracking queries no longer perform identity resolution</span></span>

<span data-ttu-id="203d4-314">Suivi de problème no 13518</span><span class="sxs-lookup"><span data-stu-id="203d4-314">[Tracking Issue #13518](https://github.com/aspnet/EntityFrameworkCore/issues/13518)</span></span>

<span data-ttu-id="203d4-315">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-315">**Old behavior**</span></span>

<span data-ttu-id="203d4-316">Avant EF Core 3.0, la même instance d’entité était utilisée pour chaque occurrence d’une entité avec un type et un ID donnés.</span><span class="sxs-lookup"><span data-stu-id="203d4-316">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="203d4-317">Cela correspond au comportement des requêtes de suivi.</span><span class="sxs-lookup"><span data-stu-id="203d4-317">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="203d4-318">Par exemple, cette requête :</span><span class="sxs-lookup"><span data-stu-id="203d4-318">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="203d4-319">retournait la même instance `Category` pour chaque `Product` associé à la catégorie donnée.</span><span class="sxs-lookup"><span data-stu-id="203d4-319">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="203d4-320">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-320">**New behavior**</span></span>

<span data-ttu-id="203d4-321">À compter de EF Core 3.0, différentes instances d’entité sont créées lorsqu’une entité avec un type et un ID donnés est rencontrée à différents emplacements dans le graphe retourné.</span><span class="sxs-lookup"><span data-stu-id="203d4-321">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="203d4-322">Par exemple, la requête ci-dessus retourne à présent une nouvelle instance `Category` pour chaque `Product` même si deux produits sont associés à la même catégorie.</span><span class="sxs-lookup"><span data-stu-id="203d4-322">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="203d4-323">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-323">**Why**</span></span>

<span data-ttu-id="203d4-324">La résolution de l’identité (autrement dit, le fait de déterminer qu’une entité a les mêmes type et ID qu’une entité précédemment rencontrée) améliore les performances et la surcharge de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="203d4-324">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="203d4-325">Cela agit généralement à l’opposé de la raison pour laquelle aucune requête de suivi n’est utilisée en premier lieu.</span><span class="sxs-lookup"><span data-stu-id="203d4-325">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="203d4-326">En outre, même si la résolution de l’identité peut parfois être utile, elle n’est pas nécessaire si les entités doivent être sérialisées et envoyées à un client, ce qui est courant pour les requêtes sans suivi.</span><span class="sxs-lookup"><span data-stu-id="203d4-326">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="203d4-327">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-327">**Mitigations**</span></span>

<span data-ttu-id="203d4-328">Utilisez une requête de suivi si la résolution de l’identité est requise.</span><span class="sxs-lookup"><span data-stu-id="203d4-328">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="203d4-329">~~L’exécution de requêtes est enregistrée au niveau du débogage ~~Rétabli</span><span class="sxs-lookup"><span data-stu-id="203d4-329">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="203d4-330">Suivi du problème n<c0>o </c0>14523</span><span class="sxs-lookup"><span data-stu-id="203d4-330">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="203d4-331">Nous avons rétabli ce changement car la nouvelle configuration dans EF Core 3.0 permet la spécification du niveau d’enregistrement d’un événement par l’application.</span><span class="sxs-lookup"><span data-stu-id="203d4-331">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="203d4-332">Par exemple, pour basculer l’enregistrement de SQL vers `Debug`, configurez explicitement le niveau dans `OnConfiguring` ou `AddDbContext` :</span><span class="sxs-lookup"><span data-stu-id="203d4-332">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="203d4-333">Les valeurs de clés temporaires ne sont plus définies sur les instances d’entités</span><span class="sxs-lookup"><span data-stu-id="203d4-333">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="203d4-334">Suivi du problème n<c0>o </c0>12378</span><span class="sxs-lookup"><span data-stu-id="203d4-334">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="203d4-335">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-335">**Old behavior**</span></span>

<span data-ttu-id="203d4-336">Avant EF Core 3.0, des valeurs temporaires étaient affectées à toutes les propriétés de clé qui auraient plus tard une valeur réelle générée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="203d4-336">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="203d4-337">Généralement, ces valeurs temporaires étaient de grands nombres négatifs.</span><span class="sxs-lookup"><span data-stu-id="203d4-337">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="203d4-338">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-338">**New behavior**</span></span>

<span data-ttu-id="203d4-339">À compter de la version 3.0, EF Core stocke la valeur de clé temporaire dans le cadre des informations de suivi de l’entité, et laisse la propriété de clé proprement dite inchangée.</span><span class="sxs-lookup"><span data-stu-id="203d4-339">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="203d4-340">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-340">**Why**</span></span>

<span data-ttu-id="203d4-341">Ce changement a été apporté afin d’empêcher que les valeurs de clés temporaires ne deviennent à tort permanentes quand une entité qui a été suivie par une instance `DbContext` est déplacée vers une autre instance `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="203d4-341">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="203d4-342">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-342">**Mitigations**</span></span>

<span data-ttu-id="203d4-343">Les applications qui affectent des valeurs de clés primaires sur des clés étrangères afin de former des associations entre des entités peuvent dépendre de l’ancien comportement si les clés primaires sont générées par le magasin et appartiennent à des entités à l’état `Added`.</span><span class="sxs-lookup"><span data-stu-id="203d4-343">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="203d4-344">Vous pouvez éviter cela en :</span><span class="sxs-lookup"><span data-stu-id="203d4-344">This can be avoided by:</span></span>
* <span data-ttu-id="203d4-345">N’utilisant pas de clés générées par le magasin.</span><span class="sxs-lookup"><span data-stu-id="203d4-345">Not using store-generated keys.</span></span>
* <span data-ttu-id="203d4-346">Définissant des propriétés de navigation afin d’établir des relations au lieu de définir des valeurs de clés étrangères.</span><span class="sxs-lookup"><span data-stu-id="203d4-346">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="203d4-347">Obtenant les valeurs de clés temporaires réelles à partir des informations de suivi de l’entité.</span><span class="sxs-lookup"><span data-stu-id="203d4-347">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="203d4-348">Par exemple, `context.Entry(blog).Property(e => e.Id).CurrentValue` retournera la valeur temporaire même si la propriété `blog.Id` elle-même n’a pas été définie.</span><span class="sxs-lookup"><span data-stu-id="203d4-348">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="203d4-349">DetectChanges honore les valeurs de clés générées par le magasin</span><span class="sxs-lookup"><span data-stu-id="203d4-349">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="203d4-350">Suivi du problème n<c0>o </c0>14616</span><span class="sxs-lookup"><span data-stu-id="203d4-350">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="203d4-351">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-351">**Old behavior**</span></span>

<span data-ttu-id="203d4-352">Avant EF Core 3.0, une entité non suivie détectée par `DetectChanges` était suivie à l’état `Added` et insérée en tant que nouvelle ligne quand `SaveChanges` était appelée.</span><span class="sxs-lookup"><span data-stu-id="203d4-352">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="203d4-353">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-353">**New behavior**</span></span>

<span data-ttu-id="203d4-354">À compter d’EF Core 3.0, si une entité utilise des valeurs de clés générées et qu’une valeur de clé est définie, alors l’entité est suivie à l’état `Modified`.</span><span class="sxs-lookup"><span data-stu-id="203d4-354">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="203d4-355">Cela signifie qu’une ligne pour l’entité est supposée exister et qu’elle sera mise à jour lors de l’appel à `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="203d4-355">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="203d4-356">Si la valeur de clé n’est pas définie, ou si le type d’entité n’utilise pas de clés générées, alors la nouvelle entité sera quand même suivie comme `Added`, comme dans les versions précédentes.</span><span class="sxs-lookup"><span data-stu-id="203d4-356">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="203d4-357">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-357">**Why**</span></span>

<span data-ttu-id="203d4-358">Ce changement a été apporté afin de simplifier l’utilisation des graphes d’entités déconnectées lors de l’utilisation de clés générées par le magasin.</span><span class="sxs-lookup"><span data-stu-id="203d4-358">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="203d4-359">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-359">**Mitigations**</span></span>

<span data-ttu-id="203d4-360">Ce changement peut casser une application si un type d’entité est configuré pour utiliser des clés générés, mais que les valeurs de clés sont définies explicitement pour les nouvelles instances.</span><span class="sxs-lookup"><span data-stu-id="203d4-360">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="203d4-361">La solution consiste à configurer explicitement les propriétés de clés de façon à ne pas utiliser des valeurs générées.</span><span class="sxs-lookup"><span data-stu-id="203d4-361">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="203d4-362">Par exemple, avec l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="203d4-362">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="203d4-363">Ou avec des annotations de données :</span><span class="sxs-lookup"><span data-stu-id="203d4-363">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="203d4-364">Les suppressions en cascade se produisent désormais immédiatement par défaut</span><span class="sxs-lookup"><span data-stu-id="203d4-364">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="203d4-365">Suivi du problème n<c0>o </c0>10114</span><span class="sxs-lookup"><span data-stu-id="203d4-365">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="203d4-366">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-366">**Old behavior**</span></span>

<span data-ttu-id="203d4-367">Avant la version 3.0, les actions en cascade appliquées par EF Core (suppression d’entités dépendantes quand un principal requis est supprimé ou quand la relation à un principal requis est interrompue) ne se produisaient qu’une fois que SaveChanges était appelée.</span><span class="sxs-lookup"><span data-stu-id="203d4-367">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="203d4-368">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-368">**New behavior**</span></span>

<span data-ttu-id="203d4-369">À compter de la version 3.0, EF Core applique les actions en cascade dès que la condition de déclenchement est détectée.</span><span class="sxs-lookup"><span data-stu-id="203d4-369">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="203d4-370">Par exemple, si vous appelez `context.Remove()` pour supprimer une entité principale, toutes les dépendances requises suivies associées seront également définies immédiatement sur `Deleted`.</span><span class="sxs-lookup"><span data-stu-id="203d4-370">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="203d4-371">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-371">**Why**</span></span>

<span data-ttu-id="203d4-372">Ce changement a été apporté afin d’améliorer l’expérience de liaison de données et les scénarios d’audit où il est important de comprendre quelles entités seront supprimées _avant l’appel à _ `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="203d4-372">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="203d4-373">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-373">**Mitigations**</span></span>

<span data-ttu-id="203d4-374">Vous pouvez restaurer le comportement précédent par le biais des paramètres sur `context.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="203d4-374">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="203d4-375">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-375">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="203d4-376">Le chargement hâtif des entités associées se produit désormais dans une seule requête</span><span class="sxs-lookup"><span data-stu-id="203d4-376">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="203d4-377">#18022 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="203d4-377">Tracking issue #18022</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

<span data-ttu-id="203d4-378">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-378">**Old behavior**</span></span>

<span data-ttu-id="203d4-379">Avant le 3,0, le chargement des navigations de collections `Include` à l’aide d’opérateurs entraînait la génération de plusieurs requêtes sur une base de données relationnelle, une pour chaque type d’entité associée.</span><span class="sxs-lookup"><span data-stu-id="203d4-379">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

<span data-ttu-id="203d4-380">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-380">**New behavior**</span></span>

<span data-ttu-id="203d4-381">À partir de 3,0, EF Core génère une requête unique avec des JOINTUREs sur des bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="203d4-381">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

<span data-ttu-id="203d4-382">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-382">**Why**</span></span>

<span data-ttu-id="203d4-383">L’émission de plusieurs requêtes pour implémenter une seule requête LINQ a entraîné de nombreux problèmes, notamment des performances négatives lorsque plusieurs allers-retours de base de données étaient nécessaires et des problèmes de cohérence des données à mesure que chaque requête pouvait observer un état différent de la base de données.</span><span class="sxs-lookup"><span data-stu-id="203d4-383">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

<span data-ttu-id="203d4-384">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-384">**Mitigations**</span></span>

<span data-ttu-id="203d4-385">Techniquement, ce n’est pas une modification avec rupture, mais cela peut avoir un impact considérable sur les performances de l’application lorsqu’une seule requête contient un grand nombre d' `Include` opérateurs dans les navigations de collection.</span><span class="sxs-lookup"><span data-stu-id="203d4-385">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="203d4-386">Pour plus d’informations et pour réécrire des requêtes de manière plus efficace, [consultez ce commentaire](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) .</span><span class="sxs-lookup"><span data-stu-id="203d4-386">[See this comment](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="203d4-387">La sémantique de DeleteBehavior.Restrict est désormais plus propre</span><span class="sxs-lookup"><span data-stu-id="203d4-387">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="203d4-388">Suivi de problème no 12661</span><span class="sxs-lookup"><span data-stu-id="203d4-388">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="203d4-389">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-389">**Old behavior**</span></span>

<span data-ttu-id="203d4-390">Avant la version 3.0, `DeleteBehavior.Restrict` créait les clés étrangères dans la base de données avec la sémantique `Restrict`, et il modifiait les corrections internes d’une manière non évidente.</span><span class="sxs-lookup"><span data-stu-id="203d4-390">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="203d4-391">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-391">**New behavior**</span></span>

<span data-ttu-id="203d4-392">À compter de la version 3.0, `DeleteBehavior.Restrict` garantit la création des clés étrangères avec la sémantique `Restrict` (autrement dit, sans levée de cascades lors d’une violation de contrainte), sans impact sur les corrections internes EF.</span><span class="sxs-lookup"><span data-stu-id="203d4-392">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="203d4-393">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-393">**Why**</span></span>

<span data-ttu-id="203d4-394">Ce changement a été apporté pour améliorer l’expérience et permettre une utilisation intuitive de `DeleteBehavior`, sans effets secondaires inattendus.</span><span class="sxs-lookup"><span data-stu-id="203d4-394">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="203d4-395">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-395">**Mitigations**</span></span>

<span data-ttu-id="203d4-396">Vous pouvez restaurer le comportement précédent par le biais de `DeleteBehavior.ClientNoAction`.</span><span class="sxs-lookup"><span data-stu-id="203d4-396">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="203d4-397">Les types de requêtes sont regroupés avec les types d’entités</span><span class="sxs-lookup"><span data-stu-id="203d4-397">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="203d4-398">Suivi du problème n<c0>o </c0>14194</span><span class="sxs-lookup"><span data-stu-id="203d4-398">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="203d4-399">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-399">**Old behavior**</span></span>

<span data-ttu-id="203d4-400">Avant EF Core 3.0, les [types de requêtes](xref:core/modeling/keyless-entity-types) étaient un moyen d’interroger des données qui ne définissait pas de clé primaire de façon structurée.</span><span class="sxs-lookup"><span data-stu-id="203d4-400">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="203d4-401">Autrement dit, on utilisait un type de requête pour mapper des types d’entités sans clés (le plus souvent à partir d’une vue, mais aussi éventuellement à partir d’une table), alors qu’on utilisait un type d’entité normal quand une clé était disponible (le plus souvent à partir d’une table, mais aussi éventuellement à partir d’une vue).</span><span class="sxs-lookup"><span data-stu-id="203d4-401">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="203d4-402">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-402">**New behavior**</span></span>

<span data-ttu-id="203d4-403">Un type de requête devient maintenant simplement un type d’entité sans clé primaire.</span><span class="sxs-lookup"><span data-stu-id="203d4-403">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="203d4-404">Les types d’entités sans clé ont les mêmes fonctionnalités que les types de requêtes dans les versions précédentes.</span><span class="sxs-lookup"><span data-stu-id="203d4-404">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="203d4-405">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-405">**Why**</span></span>

<span data-ttu-id="203d4-406">Ce changement a été apporté afin de réduire la confusion concernant l’objectif des types de requêtes.</span><span class="sxs-lookup"><span data-stu-id="203d4-406">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="203d4-407">Plus précisément, il s’agit de types d’entités sans clé et sont intrinsèquement en lecture seule pour cette raison, mais vous ne devez pas les utiliser au seul motif qu’un type d’entité doit être en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="203d4-407">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="203d4-408">De même, ils sont souvent mappés à des vues, mais c’est uniquement car les vues définissent rarement des clés.</span><span class="sxs-lookup"><span data-stu-id="203d4-408">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="203d4-409">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-409">**Mitigations**</span></span>

<span data-ttu-id="203d4-410">Les parties suivantes de l’API sont désormais obsolètes :</span><span class="sxs-lookup"><span data-stu-id="203d4-410">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="203d4-411">**`ModelBuilder.Query<>()`** -À la place, `ModelBuilder.Entity<>().HasNoKey()` doit être appelé pour marquer un type d’entité comme n’ayant aucune clé.</span><span class="sxs-lookup"><span data-stu-id="203d4-411">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="203d4-412">Cela ne serait toujours pas configuré par convention, afin d’éviter une configuration incorrecte quand une clé primaire est attendue mais ne correspond pas à la convention.</span><span class="sxs-lookup"><span data-stu-id="203d4-412">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="203d4-413">**`DbQuery<>`** - `DbSet<>` Doit être utilisé à la place.</span><span class="sxs-lookup"><span data-stu-id="203d4-413">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="203d4-414">**`DbContext.Query<>()`** - `DbContext.Set<>()` Doit être utilisé à la place.</span><span class="sxs-lookup"><span data-stu-id="203d4-414">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="203d4-415">**`IQueryTypeConfiguration<TQuery>`** -À la place, `IEntityTypeConfiguration<TEntity>` \* \* doit être utilisé.</span><span class="sxs-lookup"><span data-stu-id="203d4-415">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>`\*\* should be used.</span></span>

> [!NOTE]
> <span data-ttu-id="203d4-416">En raison d' [un problème dans 3. x](https://github.com/dotnet/efcore/issues/19537) lors de l’interrogation de keymoins les entités dont toutes les propriétés ont la valeur est `null` `null` retournée à la place d’une entité, si ce problème est applicable à votre scénario, ajoutez également une logique pour gérer les `null` résultats.</span><span class="sxs-lookup"><span data-stu-id="203d4-416">Due to [an issue in 3.x](https://github.com/dotnet/efcore/issues/19537) when querying keyless entities that have all properties set to `null` a `null` will be returned instead of an entity, if this issue is applicable to your scenario also add logic to handle `null` in results.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="203d4-417">L’API de configuration pour les relations de type détenu a changé</span><span class="sxs-lookup"><span data-stu-id="203d4-417">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="203d4-418">#12444 du problème de [suivi](https://github.com/aspnet/EntityFrameworkCore/issues/12444) 
 #9148 du problème de [suivi](https://github.com/aspnet/EntityFrameworkCore/issues/9148) 
 [#14153 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="203d4-418">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="203d4-419">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-419">**Old behavior**</span></span>

<span data-ttu-id="203d4-420">Avant EF Core 3.0, la configuration de la relation détenue était effectuée directement après l’appel à `OwnsOne` ou `OwnsMany`.</span><span class="sxs-lookup"><span data-stu-id="203d4-420">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="203d4-421">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-421">**New behavior**</span></span>

<span data-ttu-id="203d4-422">À compter d’EF Core 3.0, il existe une API Fluent afin de configurer une propriété de navigation pour le propriétaire à l’aide de `WithOwner()`.</span><span class="sxs-lookup"><span data-stu-id="203d4-422">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="203d4-423">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-423">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="203d4-424">La configuration liée à la relation entre le propriétaire et le détenu doit maintenant être chaînée après `WithOwner()` tout comme les autres relations.</span><span class="sxs-lookup"><span data-stu-id="203d4-424">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="203d4-425">En revanche, la configuration du type détenu lui-même serait toujours chaînée après `OwnsOne()/OwnsMany()`.</span><span class="sxs-lookup"><span data-stu-id="203d4-425">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="203d4-426">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-426">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

<span data-ttu-id="203d4-427">De plus, l’appel à `Entity()`, `HasOne()` ou `Set()` avec un type détenu comme cible lève désormais une exception.</span><span class="sxs-lookup"><span data-stu-id="203d4-427">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="203d4-428">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-428">**Why**</span></span>

<span data-ttu-id="203d4-429">Ce changement a été apporté afin de créer une distinction plus claire entre la configuration du type détenu lui-même et la _relation au type détenu_.</span><span class="sxs-lookup"><span data-stu-id="203d4-429">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="203d4-430">Cela lève ainsi toute ambiguïté et toute confusion autour des méthodes telles que `HasForeignKey`.</span><span class="sxs-lookup"><span data-stu-id="203d4-430">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="203d4-431">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-431">**Mitigations**</span></span>

<span data-ttu-id="203d4-432">Changez la configuration des relations de type détenu de façon à utiliser la nouvelle surface d’API, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="203d4-432">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="203d4-433">Les entités dépendantes qui partagent la table avec le principal sont maintenant facultatives</span><span class="sxs-lookup"><span data-stu-id="203d4-433">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="203d4-434">Suivi du problème n<c0>o </c0>9005</span><span class="sxs-lookup"><span data-stu-id="203d4-434">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="203d4-435">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-435">**Old behavior**</span></span>

<span data-ttu-id="203d4-436">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="203d4-436">Consider the following model:</span></span>
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
<span data-ttu-id="203d4-437">Avant EF Core 3.0, si `OrderDetails` est détenu par `Order` ou explicitement mappé à la même table, une instance `OrderDetails` est toujours nécessaire pour ajouter un nouveau `Order`.</span><span class="sxs-lookup"><span data-stu-id="203d4-437">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="203d4-438">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-438">**New behavior**</span></span>

<span data-ttu-id="203d4-439">À partir de la version 3.0, EF Core permet d’ajouter un `Order` sans `OrderDetails` et mappe toutes les propriétés de `OrderDetails` à l’exception de la clé primaire aux colonnes de type nullable.</span><span class="sxs-lookup"><span data-stu-id="203d4-439">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="203d4-440">Pendant l’interrogation, EF Core définit `OrderDetails` sur `null` si une de ses propriétés obligatoires n’a pas de valeur, ou s’il n’a pas de propriété obligatoire en plus de la clé primaire et que toutes les propriétés sont `null`.</span><span class="sxs-lookup"><span data-stu-id="203d4-440">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="203d4-441">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-441">**Mitigations**</span></span>

<span data-ttu-id="203d4-442">Si votre modèle a une table qui partage des dépendances avec toutes les colonnes facultatives, mais que la navigation qui pointe vers lui n’est pas censée être `null`, l’application doit être modifiée pour gérer les situations où la navigation est `null`.</span><span class="sxs-lookup"><span data-stu-id="203d4-442">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="203d4-443">Si ce n’est pas possible, une propriété obligatoire doit être ajoutée au type d’entité ou au moins une propriété doit avoir une valeur non-`null`.</span><span class="sxs-lookup"><span data-stu-id="203d4-443">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="203d4-444">Toutes les entités qui partagent une table avec une colonne de jeton de concurrence doivent la mapper à une propriété</span><span class="sxs-lookup"><span data-stu-id="203d4-444">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="203d4-445">Suivi du problème n<c0>o </c0>14154</span><span class="sxs-lookup"><span data-stu-id="203d4-445">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="203d4-446">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-446">**Old behavior**</span></span>

<span data-ttu-id="203d4-447">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="203d4-447">Consider the following model:</span></span>
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
<span data-ttu-id="203d4-448">Avant EF Core 3.0, si `OrderDetails` est détenu par `Order` ou explicitement mappé à la même table, la mise à jour de `OrderDetails` seulement ne met pas à jour la valeur de `Version` sur le client et la prochaine mise à jour échoue.</span><span class="sxs-lookup"><span data-stu-id="203d4-448">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="203d4-449">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-449">**New behavior**</span></span>

<span data-ttu-id="203d4-450">À compter de la version 3.0, EF Core propage la nouvelle valeur `Version` sur `Order` s’il est propriétaire de `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="203d4-450">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="203d4-451">Sinon, une exception est levée pendant la validation de modèle.</span><span class="sxs-lookup"><span data-stu-id="203d4-451">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="203d4-452">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-452">**Why**</span></span>

<span data-ttu-id="203d4-453">Ce changement a été effectué pour éviter la péremption de la valeur du jeton de concurrence quand une seule des entités mappées à la même table est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="203d4-453">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="203d4-454">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-454">**Mitigations**</span></span>

<span data-ttu-id="203d4-455">Toutes les entités partageant la table doivent inclure une propriété mappée à la colonne de jeton de concurrence.</span><span class="sxs-lookup"><span data-stu-id="203d4-455">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="203d4-456">Vous pouvez en créer une dans un état fantôme :</span><span class="sxs-lookup"><span data-stu-id="203d4-456">It's possible the create one in shadow-state:</span></span>
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="203d4-457">Les entités détenues ne peuvent pas être interrogées sans le propriétaire à l’aide d’une requête de suivi</span><span class="sxs-lookup"><span data-stu-id="203d4-457">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="203d4-458">#18876 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="203d4-458">Tracking Issue #18876</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

<span data-ttu-id="203d4-459">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-459">**Old behavior**</span></span>

<span data-ttu-id="203d4-460">Avant le EF Core 3,0, les entités appartenant peuvent être interrogées comme n’importe quelle autre navigation.</span><span class="sxs-lookup"><span data-stu-id="203d4-460">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

<span data-ttu-id="203d4-461">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-461">**New behavior**</span></span>

<span data-ttu-id="203d4-462">À partir de 3,0, EF Core lève une exception si une requête de suivi projette une entité détenue sans le propriétaire.</span><span class="sxs-lookup"><span data-stu-id="203d4-462">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

<span data-ttu-id="203d4-463">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-463">**Why**</span></span>

<span data-ttu-id="203d4-464">Les entités détenues ne peuvent pas être manipulées sans le propriétaire, donc dans la grande majorité des cas, l’interrogation de cette façon est une erreur.</span><span class="sxs-lookup"><span data-stu-id="203d4-464">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

<span data-ttu-id="203d4-465">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-465">**Mitigations**</span></span>

<span data-ttu-id="203d4-466">Si l’entité possédée doit être suivie pour être modifiée de quelque façon que ce soit ultérieurement, le propriétaire doit être inclus dans la requête.</span><span class="sxs-lookup"><span data-stu-id="203d4-466">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="203d4-467">Sinon, ajoutez un `AsNoTracking()` appel :</span><span class="sxs-lookup"><span data-stu-id="203d4-467">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="203d4-468">Les propriétés héritées de types non mappés sont maintenant mappées à une seule colonne pour tous les types dérivés</span><span class="sxs-lookup"><span data-stu-id="203d4-468">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="203d4-469">Suivi du problème n<c0>o </c0>13998</span><span class="sxs-lookup"><span data-stu-id="203d4-469">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="203d4-470">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-470">**Old behavior**</span></span>

<span data-ttu-id="203d4-471">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="203d4-471">Consider the following model:</span></span>
```csharp
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

<span data-ttu-id="203d4-472">Avant EF Core 3.0, la propriété `ShippingAddress` est mappée à des colonnes distinctes pour `BulkOrder` et `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="203d4-472">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="203d4-473">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-473">**New behavior**</span></span>

<span data-ttu-id="203d4-474">À compter de la version 3.0, EF Core crée uniquement une colonne pour `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="203d4-474">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="203d4-475">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-475">**Why**</span></span>

<span data-ttu-id="203d4-476">L’ancien comportement n’était pas attendu.</span><span class="sxs-lookup"><span data-stu-id="203d4-476">The old behavoir was unexpected.</span></span>

<span data-ttu-id="203d4-477">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-477">**Mitigations**</span></span>

<span data-ttu-id="203d4-478">La propriété peut toujours être mappée explicitement à une colonne distincte sur les types dérivés :</span><span class="sxs-lookup"><span data-stu-id="203d4-478">The property can still be explicitly mapped to separate column on the derived types:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="203d4-479">La convention de propriété de clé étrangère ne correspond plus au nom de la propriété principale</span><span class="sxs-lookup"><span data-stu-id="203d4-479">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="203d4-480">Suivi du problème n<c0>o </c0>13274</span><span class="sxs-lookup"><span data-stu-id="203d4-480">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="203d4-481">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-481">**Old behavior**</span></span>

<span data-ttu-id="203d4-482">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="203d4-482">Consider the following model:</span></span>
```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
<span data-ttu-id="203d4-483">Avant EF Core 3.0, la propriété `CustomerId` était utilisée pour la clé étrangère par convention.</span><span class="sxs-lookup"><span data-stu-id="203d4-483">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="203d4-484">Toutefois, si `Order` est un type détenu, cela fait également de `CustomerId` la clé primaire, ce qui ne correspond généralement pas aux attentes.</span><span class="sxs-lookup"><span data-stu-id="203d4-484">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="203d4-485">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-485">**New behavior**</span></span>

<span data-ttu-id="203d4-486">À compter de la version 3.0, EF Core ne tente pas d’utiliser des propriétés pour les clés étrangères par convention si elles ont le même nom que la propriété principale.</span><span class="sxs-lookup"><span data-stu-id="203d4-486">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="203d4-487">Les modèles de nom du type de principal concaténé au nom de la propriété de principal, et de nom de navigation concaténé au nom de propriété de principal sont toujours mis en correspondance.</span><span class="sxs-lookup"><span data-stu-id="203d4-487">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="203d4-488">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-488">For example:</span></span>

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

<span data-ttu-id="203d4-489">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-489">**Why**</span></span>

<span data-ttu-id="203d4-490">Ce changement a été apporté afin d’éviter de définir de manière erronée une propriété de clé primaire sur le type détenu.</span><span class="sxs-lookup"><span data-stu-id="203d4-490">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="203d4-491">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-491">**Mitigations**</span></span>

<span data-ttu-id="203d4-492">Si la propriété était censée être la clé étrangère, et par conséquent une partie de la clé primaire, configurez-la explicitement comme telle.</span><span class="sxs-lookup"><span data-stu-id="203d4-492">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="203d4-493">La connexion de base de données est maintenant fermée si elle n’est plus utilisée avant la fin de TransactionScope</span><span class="sxs-lookup"><span data-stu-id="203d4-493">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="203d4-494">Suivi du problème n<c0>o </c0>14218</span><span class="sxs-lookup"><span data-stu-id="203d4-494">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="203d4-495">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-495">**Old behavior**</span></span>

<span data-ttu-id="203d4-496">Avant EF Core 3.0, si le contexte ouvre la connexion à l’intérieur d’un `TransactionScope`, la connexion reste ouverte quand le `TransactionScope` actuel est actif.</span><span class="sxs-lookup"><span data-stu-id="203d4-496">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point

        var categories = context.ProductCategories().ToList();
    }
}
```

<span data-ttu-id="203d4-497">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-497">**New behavior**</span></span>

<span data-ttu-id="203d4-498">À compter de la version 3.0, EF Core ferme la connexion dès qu’il ne l’utilise plus.</span><span class="sxs-lookup"><span data-stu-id="203d4-498">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="203d4-499">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-499">**Why**</span></span>

<span data-ttu-id="203d4-500">Ce changement permet d’utiliser plusieurs contextes dans le même `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="203d4-500">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="203d4-501">Le nouveau comportement correspond également à EF6.</span><span class="sxs-lookup"><span data-stu-id="203d4-501">The new behavior also matches EF6.</span></span>

<span data-ttu-id="203d4-502">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-502">**Mitigations**</span></span>

<span data-ttu-id="203d4-503">Si la connexion doit rester ouverte, un appel explicite à `OpenConnection()` garantit qu’EF Core ne la ferme pas prématurément :</span><span class="sxs-lookup"><span data-stu-id="203d4-503">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="203d4-504">Chaque propriété utilise la génération indépendante de clé de type entier en mémoire</span><span class="sxs-lookup"><span data-stu-id="203d4-504">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="203d4-505">Suivi du problème n<c0>o </c0>6872</span><span class="sxs-lookup"><span data-stu-id="203d4-505">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="203d4-506">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-506">**Old behavior**</span></span>

<span data-ttu-id="203d4-507">Avant EF Core 3.0, un seul générateur de valeurs partagées était utilisé pour toutes les propriétés de clé de type entier en mémoire.</span><span class="sxs-lookup"><span data-stu-id="203d4-507">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="203d4-508">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-508">**New behavior**</span></span>

<span data-ttu-id="203d4-509">À compter d’EF Core 3.0, chaque propriété de clé de type entier obtient son propre générateur de valeur lors de l’utilisation de la base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="203d4-509">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="203d4-510">De plus, si la base de données est supprimée, la génération de clé est réinitialisée pour toutes les tables.</span><span class="sxs-lookup"><span data-stu-id="203d4-510">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="203d4-511">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-511">**Why**</span></span>

<span data-ttu-id="203d4-512">Ce changement a été apporté afin d’aligner plus étroitement la génération de clé en mémoire à la génération de clé de base de données réelle, et afin d’améliorer la capacité à isoler les tests les uns des autres lors de l’utilisation de la base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="203d4-512">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="203d4-513">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-513">**Mitigations**</span></span>

<span data-ttu-id="203d4-514">Ceci peut casser une application qui repose sur la définition de valeurs de clé en mémoire spécifiques.</span><span class="sxs-lookup"><span data-stu-id="203d4-514">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="203d4-515">Au lieu de cela, ne vous appuyez pas sur des valeurs de clés spécifiques, ou procédez à une mise à jour pour vous aligner au nouveau comportement.</span><span class="sxs-lookup"><span data-stu-id="203d4-515">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="203d4-516">Les champs de stockage sont utilisés par défaut</span><span class="sxs-lookup"><span data-stu-id="203d4-516">Backing fields are used by default</span></span>

[<span data-ttu-id="203d4-517">Suivi du problème n<c0>o </c0>12430</span><span class="sxs-lookup"><span data-stu-id="203d4-517">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="203d4-518">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-518">**Old behavior**</span></span>

<span data-ttu-id="203d4-519">Avant la version 3.0, même si le champ de stockage d’une propriété était connu, par défaut EF Core lisait et écrivait toujours la valeur de propriété à l’aide des méthodes get et set de la propriété.</span><span class="sxs-lookup"><span data-stu-id="203d4-519">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="203d4-520">L’exception à cette règle concernait l’exécution des requêtes, où le champ de stockage était défini directement s’il était connu.</span><span class="sxs-lookup"><span data-stu-id="203d4-520">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="203d4-521">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-521">**New behavior**</span></span>

<span data-ttu-id="203d4-522">Depuis EF Core 3.0, si le champ de stockage d’une propriété est connu, alors EF Core lit et écrit toujours cette propriété à l’aide du champ de stockage.</span><span class="sxs-lookup"><span data-stu-id="203d4-522">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="203d4-523">Cela peut casser une application si celle-ci repose sur un comportement supplémentaire codé dans les méthodes get ou set.</span><span class="sxs-lookup"><span data-stu-id="203d4-523">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="203d4-524">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-524">**Why**</span></span>

<span data-ttu-id="203d4-525">Ce changement a été apporté afin d’empêcher EF Core de déclencher par erreur une logique métier par défaut quand vous effectuez des opérations de base de données impliquant les entités.</span><span class="sxs-lookup"><span data-stu-id="203d4-525">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="203d4-526">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-526">**Mitigations**</span></span>

<span data-ttu-id="203d4-527">Vous pouvez restaurer le comportement antérieur à la version 3.0 en configurant le mode d’accès à la propriété sur `ModelBuilder`.</span><span class="sxs-lookup"><span data-stu-id="203d4-527">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="203d4-528">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-528">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="203d4-529">Erreur si plusieurs champs de stockage compatibles sont détectés</span><span class="sxs-lookup"><span data-stu-id="203d4-529">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="203d4-530">Suivi du problème n<c0>o </c0>12523</span><span class="sxs-lookup"><span data-stu-id="203d4-530">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="203d4-531">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-531">**Old behavior**</span></span>

<span data-ttu-id="203d4-532">Avant EF Core 3.0, si plusieurs champs respectaient les règles de recherche du champ de stockage d’une propriété, l’un d’entre eux était choisi selon un ordre de priorité.</span><span class="sxs-lookup"><span data-stu-id="203d4-532">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="203d4-533">Cela pouvait entraîner l’utilisation d’un champ incorrect en cas d’ambiguïté.</span><span class="sxs-lookup"><span data-stu-id="203d4-533">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="203d4-534">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-534">**New behavior**</span></span>

<span data-ttu-id="203d4-535">À compter d’EF Core 3.0, si plusieurs champs sont mis en correspondance avec la même propriété, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="203d4-535">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="203d4-536">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-536">**Why**</span></span>

<span data-ttu-id="203d4-537">Ce changement a été apporté afin d’éviter d’utiliser en mode silencieux un champ plutôt qu’un autre quand un seul peut être correct.</span><span class="sxs-lookup"><span data-stu-id="203d4-537">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="203d4-538">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-538">**Mitigations**</span></span>

<span data-ttu-id="203d4-539">Pour les propriétés comportant des champs de stockage ambigus, le champ à utiliser doit être spécifié explicitement.</span><span class="sxs-lookup"><span data-stu-id="203d4-539">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="203d4-540">Par exemple, à l’aide de l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="203d4-540">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="203d4-541">Les noms de propriété de type « champ uniquement » doivent correspondre au nom du champ</span><span class="sxs-lookup"><span data-stu-id="203d4-541">Field-only property names should match the field name</span></span>

<span data-ttu-id="203d4-542">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-542">**Old behavior**</span></span>

<span data-ttu-id="203d4-543">Avant le EF Core 3,0, une propriété pourrait être spécifiée par une valeur de chaîne et si aucune propriété portant ce nom n’a été trouvée sur le type .NET, EF Core essaiera de la faire correspondre à un champ à l’aide de règles de Convention.</span><span class="sxs-lookup"><span data-stu-id="203d4-543">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

<span data-ttu-id="203d4-544">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-544">**New behavior**</span></span>

<span data-ttu-id="203d4-545">À compter d’EF Core 3.0, une propriété de type « champ uniquement » doit correspondre exactement au nom du champ.</span><span class="sxs-lookup"><span data-stu-id="203d4-545">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="203d4-546">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-546">**Why**</span></span>

<span data-ttu-id="203d4-547">Ce changement a été apporté pour éviter d’utiliser un même champ pour deux propriétés portant un nom similaire. De plus, les règles de correspondance des propriétés de type « champ uniquement » sont désormais les mêmes que pour les propriétés mappées aux propriétés CLR.</span><span class="sxs-lookup"><span data-stu-id="203d4-547">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="203d4-548">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-548">**Mitigations**</span></span>

<span data-ttu-id="203d4-549">Les propriétés de type « champ uniquement » doivent porter le même nom que le champ auquel elles sont mappées.</span><span class="sxs-lookup"><span data-stu-id="203d4-549">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="203d4-550">Dans une version ultérieure de EF Core après 3,0, nous prévoyons de réactiver explicitement la configuration d’un nom de champ différent du nom de la propriété (voir le problème [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)) :</span><span class="sxs-lookup"><span data-stu-id="203d4-550">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="203d4-551">AddDbContext/AddDbContextPool n’appelle plus AddLogging et AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="203d4-551">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="203d4-552">Suivi du problème n<c0>o </c0>14756</span><span class="sxs-lookup"><span data-stu-id="203d4-552">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="203d4-553">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-553">**Old behavior**</span></span>

<span data-ttu-id="203d4-554">Avant le EF Core 3,0, l’appel de `AddDbContext` ou `AddDbContextPool` enregistrerait également des services de journalisation et de mise en cache de mémoire avec des appels à [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) et [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="203d4-554">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="203d4-555">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-555">**New behavior**</span></span>

<span data-ttu-id="203d4-556">À compter d’EF Core 3.0, `AddDbContext` et `AddDbContextPool` n’inscriront plus ces services auprès de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="203d4-556">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="203d4-557">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-557">**Why**</span></span>

<span data-ttu-id="203d4-558">Il n’est pas nécessaire pour EF Core 3.0 que ces services se trouvent dans le conteneur d’injection de dépendances de l’application.</span><span class="sxs-lookup"><span data-stu-id="203d4-558">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="203d4-559">Toutefois, `ILoggerFactory` est utilisé par EF Core même s’il est inscrit dans ce conteneur.</span><span class="sxs-lookup"><span data-stu-id="203d4-559">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="203d4-560">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-560">**Mitigations**</span></span>

<span data-ttu-id="203d4-561">Si votre application a besoin de ces services, inscrivez-les explicitement auprès du conteneur d’injection de dépendances avec [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) ou [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="203d4-561">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="203d4-562">AddEntityFramework \* ajoute IMemoryCache avec une limite de taille</span><span class="sxs-lookup"><span data-stu-id="203d4-562">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="203d4-563">#12905 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="203d4-563">Tracking Issue #12905</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

<span data-ttu-id="203d4-564">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-564">**Old behavior**</span></span>

<span data-ttu-id="203d4-565">Avant le EF Core 3,0, `AddEntityFramework*` les méthodes d’appel inscrivent également les services de mise en cache en mémoire avec l’injection de transaction sans limite de taille.</span><span class="sxs-lookup"><span data-stu-id="203d4-565">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

<span data-ttu-id="203d4-566">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-566">**New behavior**</span></span>

<span data-ttu-id="203d4-567">À compter de EF Core 3,0, `AddEntityFramework*` inscrira un service IMemoryCache avec une limite de taille.</span><span class="sxs-lookup"><span data-stu-id="203d4-567">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="203d4-568">Si d’autres services ajoutés par la suite dépendent de IMemoryCache, ils peuvent rapidement atteindre la limite par défaut provoquant des exceptions ou une dégradation des performances.</span><span class="sxs-lookup"><span data-stu-id="203d4-568">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

<span data-ttu-id="203d4-569">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-569">**Why**</span></span>

<span data-ttu-id="203d4-570">L’utilisation de IMemoryCache sans limite peut entraîner une utilisation incontrôlée de la mémoire s’il existe un bogue dans la logique de mise en cache des requêtes ou si les requêtes sont générées dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="203d4-570">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="203d4-571">Le fait d’avoir une limite par défaut atténue une attaque potentielle par déni de compte.</span><span class="sxs-lookup"><span data-stu-id="203d4-571">Having a default limit mitigates a potential DoS attack.</span></span>

<span data-ttu-id="203d4-572">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-572">**Mitigations**</span></span>

<span data-ttu-id="203d4-573">Dans la plupart des cas, l’appel de `AddEntityFramework*` n’est pas nécessaire si `AddDbContext` ou `AddDbContextPool` est également appelé.</span><span class="sxs-lookup"><span data-stu-id="203d4-573">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="203d4-574">Par conséquent, la meilleure atténuation consiste à supprimer l' `AddEntityFramework*` appel.</span><span class="sxs-lookup"><span data-stu-id="203d4-574">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="203d4-575">Si votre application a besoin de ces services, inscrivez une implémentation IMemoryCache explicitement avec le conteneur DI au préalable à l’aide de [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="203d4-575">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="203d4-576">DbContext.Entry effectue maintenant un DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="203d4-576">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="203d4-577">Suivi du problème n<c0>o </c0>13552</span><span class="sxs-lookup"><span data-stu-id="203d4-577">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="203d4-578">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-578">**Old behavior**</span></span>

<span data-ttu-id="203d4-579">Avant EF Core 3.0, le fait d’appeler `DbContext.Entry` provoquait la détection des changements pour toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="203d4-579">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="203d4-580">Cela garantissait que l’état exposé dans `EntityEntry` était à jour.</span><span class="sxs-lookup"><span data-stu-id="203d4-580">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="203d4-581">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-581">**New behavior**</span></span>

<span data-ttu-id="203d4-582">À compter d’EF Core 3.0, l’appel à `DbContext.Entry` tente uniquement de détecter les changements apportés dans l’entité donnée et dans toute entité principale suivie qui lui est associée.</span><span class="sxs-lookup"><span data-stu-id="203d4-582">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="203d4-583">Cela signifie que les changements apportés ailleurs peuvent ne pas avoir été détectés par l’appel à cette méthode, ce qui pourrait avoir des conséquences sur l’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="203d4-583">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="203d4-584">Notez que si `ChangeTracker.AutoDetectChangesEnabled` a la valeur `false`, même cette détection de changement locale sera désactivée.</span><span class="sxs-lookup"><span data-stu-id="203d4-584">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="203d4-585">Les autres méthodes qui provoquent une détection des changements (par exemple `ChangeTracker.Entries` et `SaveChanges`) entraînent toujours un `DetectChanges` complet de toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="203d4-585">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="203d4-586">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-586">**Why**</span></span>

<span data-ttu-id="203d4-587">Ce changement a été apporté afin d’améliorer les performances par défaut de `context.Entry`.</span><span class="sxs-lookup"><span data-stu-id="203d4-587">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="203d4-588">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-588">**Mitigations**</span></span>

<span data-ttu-id="203d4-589">Appelez `ChangeTracker.DetectChanges()` explicitement avant d’appeler `Entry` pour garantir le comportement antérieur à la version 3.0.</span><span class="sxs-lookup"><span data-stu-id="203d4-589">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="203d4-590">Les clés de tableaux d’octets et de chaînes ne sont pas générés par client par défaut</span><span class="sxs-lookup"><span data-stu-id="203d4-590">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="203d4-591">Suivi du problème n<c0>o </c0>14617</span><span class="sxs-lookup"><span data-stu-id="203d4-591">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="203d4-592">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-592">**Old behavior**</span></span>

<span data-ttu-id="203d4-593">Avant EF Core 3.0, les propriétés de clés `string` et `byte[]` pouvaient être utilisées sans définir explicitement de valeur non null.</span><span class="sxs-lookup"><span data-stu-id="203d4-593">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="203d4-594">Dans ce cas, la valeur de la clé était générée sur le client en tant que GUID, sérialisé en octets pour `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="203d4-594">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="203d4-595">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-595">**New behavior**</span></span>

<span data-ttu-id="203d4-596">À compter d’EF Core 3.0, une exception est levée pour signaler qu’aucune valeur de clé n’a été définie.</span><span class="sxs-lookup"><span data-stu-id="203d4-596">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="203d4-597">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-597">**Why**</span></span>

<span data-ttu-id="203d4-598">Ce changement a été apporté car les valeurs `string`/`byte[]` générées par le client ne sont généralement pas utiles, et le comportement par défaut rendait les valeurs de clés générés de manière courante difficiles à expliquer.</span><span class="sxs-lookup"><span data-stu-id="203d4-598">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="203d4-599">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-599">**Mitigations**</span></span>

<span data-ttu-id="203d4-600">Vos pouvez obtenir le comportement antérieur à la version 3.0 en spécifiant explicitement que les propriétés de clés doivent utiliser des valeurs générées si aucune autre valeur non nulle n’est définie.</span><span class="sxs-lookup"><span data-stu-id="203d4-600">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="203d4-601">Par exemple, avec l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="203d4-601">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="203d4-602">Ou avec des annotations de données :</span><span class="sxs-lookup"><span data-stu-id="203d4-602">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="203d4-603">ILoggerFactory est désormais un service délimité</span><span class="sxs-lookup"><span data-stu-id="203d4-603">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="203d4-604">Suivi du problème n<c0>o </c0>14698</span><span class="sxs-lookup"><span data-stu-id="203d4-604">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="203d4-605">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-605">**Old behavior**</span></span>

<span data-ttu-id="203d4-606">Avant EF Core 3.0, `ILoggerFactory` était inscrit en tant que service singleton.</span><span class="sxs-lookup"><span data-stu-id="203d4-606">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="203d4-607">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-607">**New behavior**</span></span>

<span data-ttu-id="203d4-608">À compter d’EF Core 3.0, `ILoggerFactory` est inscrit en tant que service délimité.</span><span class="sxs-lookup"><span data-stu-id="203d4-608">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="203d4-609">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-609">**Why**</span></span>

<span data-ttu-id="203d4-610">Ce changement a été apporté afin d’autoriser l’association d’un journaliseur avec une instance `DbContext`, ce qui active d’autres fonctionnalités et élimine certains cas de comportement pathologique tels que l’explosion des fournisseurs de services internes.</span><span class="sxs-lookup"><span data-stu-id="203d4-610">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="203d4-611">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-611">**Mitigations**</span></span>

<span data-ttu-id="203d4-612">Ce changement ne devrait pas avoir d’impact sur le code de l’application, sauf en cas d’inscription et d’utilisation de services personnalisés sur le fournisseur de service interne EF Core,</span><span class="sxs-lookup"><span data-stu-id="203d4-612">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="203d4-613">Ce n’est pas courant.</span><span class="sxs-lookup"><span data-stu-id="203d4-613">This isn't common.</span></span>
<span data-ttu-id="203d4-614">Dans ces cas-là, la plupart des composants continueront à fonctionner, mais tout service singleton qui dépendait de `ILoggerFactory` devra être modifié pour obtenir le `ILoggerFactory` d’une manière différente.</span><span class="sxs-lookup"><span data-stu-id="203d4-614">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="203d4-615">Si vous faites face à ce genre de situation, veuillez soumettre un problème par le biais du [suivi des problèmes GitHub EF Core](https://github.com/aspnet/EntityFrameworkCore/issues) pour nous dire comment vous utilisez `ILoggerFactory`, afin que nous puissions mieux comprendre comment ne rien casser à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="203d4-615">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="203d4-616">Les proxys à chargement différé ne partent plus du principe que les propriétés de navigation sont entièrement chargées</span><span class="sxs-lookup"><span data-stu-id="203d4-616">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="203d4-617">Suivi du problème n<c0>o </c0>12780</span><span class="sxs-lookup"><span data-stu-id="203d4-617">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="203d4-618">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-618">**Old behavior**</span></span>

<span data-ttu-id="203d4-619">Avant EF Core 3.0, une fois qu’un `DbContext` était supprimé, il n’existait aucun moyen de savoir si une propriété de navigation donnée sur une entité obtenue à partir de ce contexte était entièrement chargée.</span><span class="sxs-lookup"><span data-stu-id="203d4-619">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="203d4-620">Les serveurs proxy partaient du principe qu’une navigation de référence était chargée si elle avait une valeur non null, et qu’une navigation de collection était chargée si elle n’était pas vide.</span><span class="sxs-lookup"><span data-stu-id="203d4-620">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="203d4-621">Dans ces cas-là, toute tentative de chargement différé constituait une no-op.</span><span class="sxs-lookup"><span data-stu-id="203d4-621">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="203d4-622">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-622">**New behavior**</span></span>

<span data-ttu-id="203d4-623">À compter d’EF Core 3.0, les proxys effectuent le suivi du chargement d’une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="203d4-623">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="203d4-624">Cela signifie qu’une tentative d’accès à une propriété de navigation qui est chargée une fois que le contexte a été supprimé sera toujours une no-op, même quand la navigation chargée est vide ou null.</span><span class="sxs-lookup"><span data-stu-id="203d4-624">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="203d4-625">À l’inverse, une tentative d’accès à une propriété de navigation qui n’est pas chargée lèvera une exception si le contexte est supprimé, même si la propriété de navigation est une collection non vide.</span><span class="sxs-lookup"><span data-stu-id="203d4-625">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="203d4-626">Si cette situation se présente, cela signifie que le code d’application tente d’utiliser le chargement différé à un moment non valide, et que l’application doit être modifiée afin de ne pas tenter cette opération.</span><span class="sxs-lookup"><span data-stu-id="203d4-626">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="203d4-627">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-627">**Why**</span></span>

<span data-ttu-id="203d4-628">Ce changement a été apporté afin de rendre le comportement cohérent et correct lors de la tentative de chargement différé sur une instance `DbContext` supprimée.</span><span class="sxs-lookup"><span data-stu-id="203d4-628">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="203d4-629">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-629">**Mitigations**</span></span>

<span data-ttu-id="203d4-630">Mettez à jour le code d’application pour qu’il ne tente pas d’effectuer un chargement différé avec un contexte supprimé, ou configurez cette opération pour qu’il s’agisse d’une no-op comme décrit dans le message d’exception.</span><span class="sxs-lookup"><span data-stu-id="203d4-630">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="203d4-631">La création excessive de fournisseurs de services internes est maintenant une erreur par défaut</span><span class="sxs-lookup"><span data-stu-id="203d4-631">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="203d4-632">Suivi du problème n<c0>o </c0>10236</span><span class="sxs-lookup"><span data-stu-id="203d4-632">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="203d4-633">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-633">**Old behavior**</span></span>

<span data-ttu-id="203d4-634">Avant EF Core 3.0, un avertissement était enregistré dans le journal quand une application créait une quantité pathologique de fournisseurs de services internes.</span><span class="sxs-lookup"><span data-stu-id="203d4-634">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="203d4-635">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-635">**New behavior**</span></span>

<span data-ttu-id="203d4-636">À compter d’EF Core 3.0, cet avertissement est désormais considéré comme une erreur, et une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="203d4-636">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="203d4-637">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-637">**Why**</span></span>

<span data-ttu-id="203d4-638">Ce changement a été apporté afin d’améliorer le code d’application avec une exposition plus explicite de ce cas pathologique.</span><span class="sxs-lookup"><span data-stu-id="203d4-638">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="203d4-639">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-639">**Mitigations**</span></span>

<span data-ttu-id="203d4-640">En présence de cette erreur, le mieux consiste à tenter de comprendre la cause racine, et de cesser de créer autant de fournisseurs de services internes.</span><span class="sxs-lookup"><span data-stu-id="203d4-640">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="203d4-641">Toutefois, vous pouvez reconvertir cette erreur en avertissement (ou l’ignorer) par le biais de la configuration sur le `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="203d4-641">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="203d4-642">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-642">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="203d4-643">Nouveau comportement de HasOne/HasMany appelé avec une chaîne unique</span><span class="sxs-lookup"><span data-stu-id="203d4-643">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="203d4-644">Suivi du problème n<c0>o </c0>9171</span><span class="sxs-lookup"><span data-stu-id="203d4-644">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="203d4-645">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-645">**Old behavior**</span></span>

<span data-ttu-id="203d4-646">Avant EF Core 3.0, la façon dont était interprété le code qui appelait `HasOne` ou `HasMany` avec une seule chaîne prêtait à confusion.</span><span class="sxs-lookup"><span data-stu-id="203d4-646">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="203d4-647">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-647">For example:</span></span>
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="203d4-648">Le code semble relier `Samurai` à un autre type d’entité avec la propriété de navigation `Entrance`, qui peut être privée.</span><span class="sxs-lookup"><span data-stu-id="203d4-648">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="203d4-649">En réalité, ce code tente de créer une relation avec un certain type d’entité nommé `Entrance` sans propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="203d4-649">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="203d4-650">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-650">**New behavior**</span></span>

<span data-ttu-id="203d4-651">À compter d’EF Core 3.0, le code ci-dessus effectue maintenant ce qu’il semblait devoir faire avant.</span><span class="sxs-lookup"><span data-stu-id="203d4-651">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="203d4-652">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-652">**Why**</span></span>

<span data-ttu-id="203d4-653">L’ancien comportement était très déroutant, en particulier pour qui lisait le code de configuration à la recherche d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="203d4-653">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="203d4-654">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-654">**Mitigations**</span></span>

<span data-ttu-id="203d4-655">Seules les applications qui configurent explicitement des relations en utilisant des chaînes comme noms de type sans spécifier explicitement la propriété de navigation sont concernées,</span><span class="sxs-lookup"><span data-stu-id="203d4-655">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="203d4-656">ce qui n’est pas courant.</span><span class="sxs-lookup"><span data-stu-id="203d4-656">This is not common.</span></span>
<span data-ttu-id="203d4-657">Pour revenir au comportement précédent, transmettez explicitement `null` comme nom de propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="203d4-657">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="203d4-658">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-658">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="203d4-659">Le type de retour Task pour plusieurs méthodes asynchrones a été remplacé par ValueTask</span><span class="sxs-lookup"><span data-stu-id="203d4-659">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="203d4-660">Suivi du problème n<c0>o </c0>15184</span><span class="sxs-lookup"><span data-stu-id="203d4-660">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="203d4-661">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-661">**Old behavior**</span></span>

<span data-ttu-id="203d4-662">Les méthodes asynchrones suivantes retournaient précédemment un `Task<T>` :</span><span class="sxs-lookup"><span data-stu-id="203d4-662">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="203d4-663">`ValueGenerator.NextValueAsync()` (et classes dérivées)</span><span class="sxs-lookup"><span data-stu-id="203d4-663">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="203d4-664">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-664">**New behavior**</span></span>

<span data-ttu-id="203d4-665">Les méthodes mentionnées précédemment retournent maintenant un `ValueTask<T>` sur le même `T` qu’avant.</span><span class="sxs-lookup"><span data-stu-id="203d4-665">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="203d4-666">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-666">**Why**</span></span>

<span data-ttu-id="203d4-667">Ce changement réduit le nombre d’allocations de tas induits par l’appel de ces méthodes, ce qui améliore les performances générales.</span><span class="sxs-lookup"><span data-stu-id="203d4-667">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="203d4-668">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-668">**Mitigations**</span></span>

<span data-ttu-id="203d4-669">Les applications qui sont simplement en attente des API ci-dessus doivent uniquement être recompilées, vous n’avez pas besoin de changer la source.</span><span class="sxs-lookup"><span data-stu-id="203d4-669">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="203d4-670">Une utilisation plus complexe (par exemple, le passage du `Task` retourné à `Task.WhenAny()`) nécessite généralement que le `ValueTask<T>` retourné soit converti en `Task<T>` en appelant `AsTask()` sur lui.</span><span class="sxs-lookup"><span data-stu-id="203d4-670">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="203d4-671">Notez que cette opération annule la réduction d’allocation apportée par ce changement.</span><span class="sxs-lookup"><span data-stu-id="203d4-671">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="203d4-672">L’annotation Relational:TypeMapping est désormais simplement TypeMapping</span><span class="sxs-lookup"><span data-stu-id="203d4-672">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="203d4-673">Suivi du problème n<c0>o </c0>9913</span><span class="sxs-lookup"><span data-stu-id="203d4-673">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="203d4-674">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-674">**Old behavior**</span></span>

<span data-ttu-id="203d4-675">Le nom d’annotation pour les annotations de mappage de types était « annotations ».</span><span class="sxs-lookup"><span data-stu-id="203d4-675">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="203d4-676">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-676">**New behavior**</span></span>

<span data-ttu-id="203d4-677">Le nom d’annotation pour les annotations de mappage de types est désormais « TypeMapping ».</span><span class="sxs-lookup"><span data-stu-id="203d4-677">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="203d4-678">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-678">**Why**</span></span>

<span data-ttu-id="203d4-679">Les mappages de types ne sont désormais plus utilisés uniquement pour les fournisseurs de bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="203d4-679">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="203d4-680">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-680">**Mitigations**</span></span>

<span data-ttu-id="203d4-681">Ce changement casse uniquement les applications qui accèdent au mappage de types directement en tant qu’annotation, ce qui n’est pas courant.</span><span class="sxs-lookup"><span data-stu-id="203d4-681">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="203d4-682">La meilleure solution consiste à utiliser la surface d’API pour accéder aux mappages de types, plutôt que d’utiliser directement l’annotation.</span><span class="sxs-lookup"><span data-stu-id="203d4-682">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="203d4-683">ToTable sur un type dérivé lève une exception</span><span class="sxs-lookup"><span data-stu-id="203d4-683">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="203d4-684">Suivi du problème n<c0>o </c0>11811</span><span class="sxs-lookup"><span data-stu-id="203d4-684">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="203d4-685">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-685">**Old behavior**</span></span>

<span data-ttu-id="203d4-686">Avant EF Core 3.0, l’appel à `ToTable()` sur un type dérivé était ignoré, car seule la stratégie de mappage d’héritage était TPH où cela n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="203d4-686">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="203d4-687">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-687">**New behavior**</span></span>

<span data-ttu-id="203d4-688">À compter d’EF Core 3.0, et en préparation à l’ajout de la prise en charge de TPT et TPC dans une version ultérieure, l’appel à `ToTable()` sur un type dérivé lève maintenant une exception afin d’éviter tout changement inattendu du mappage à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="203d4-688">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="203d4-689">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-689">**Why**</span></span>

<span data-ttu-id="203d4-690">Actuellement le mappage d’un type dérivé à une autre table n’est pas une opération valide.</span><span class="sxs-lookup"><span data-stu-id="203d4-690">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="203d4-691">Ce changement permettra d’éviter toute cassure ultérieure quand cette opération deviendra valide.</span><span class="sxs-lookup"><span data-stu-id="203d4-691">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="203d4-692">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-692">**Mitigations**</span></span>

<span data-ttu-id="203d4-693">Supprimez toutes les tentatives de mappage de types dérivés à d’autres tables.</span><span class="sxs-lookup"><span data-stu-id="203d4-693">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="203d4-694">ForSqlServerHasIndex est remplacé par HasIndex</span><span class="sxs-lookup"><span data-stu-id="203d4-694">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="203d4-695">Suivi du problème n<c0>o </c0>12366</span><span class="sxs-lookup"><span data-stu-id="203d4-695">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="203d4-696">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-696">**Old behavior**</span></span>

<span data-ttu-id="203d4-697">Avant EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` offrait un moyen de configurer des colonnes utilisées avec `INCLUDE`.</span><span class="sxs-lookup"><span data-stu-id="203d4-697">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="203d4-698">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-698">**New behavior**</span></span>

<span data-ttu-id="203d4-699">À compter d’EF Core 3.0, l’utilisation de `Include` sur un index est prise en charge au niveau relationnel.</span><span class="sxs-lookup"><span data-stu-id="203d4-699">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="203d4-700">Utilisez `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="203d4-700">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="203d4-701">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-701">**Why**</span></span>

<span data-ttu-id="203d4-702">Ce changement a été apporté afin de consolider l’API pour les index avec `Include` dans un seul emplacement pour tous les fournisseurs de bases de données.</span><span class="sxs-lookup"><span data-stu-id="203d4-702">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="203d4-703">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-703">**Mitigations**</span></span>

<span data-ttu-id="203d4-704">Utilisez la nouvelle API, comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="203d4-704">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="203d4-705">Changements apportés à l’API de métadonnées</span><span class="sxs-lookup"><span data-stu-id="203d4-705">Metadata API changes</span></span>

<span data-ttu-id="203d4-706">Suivi du problème no 214</span><span class="sxs-lookup"><span data-stu-id="203d4-706">[Tracking Issue #214](https://github.com/aspnet/EntityFrameworkCore/issues/214)</span></span>

<span data-ttu-id="203d4-707">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-707">**New behavior**</span></span>

<span data-ttu-id="203d4-708">Les propriétés suivantes ont été converties en méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="203d4-708">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="203d4-709">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-709">**Why**</span></span>

<span data-ttu-id="203d4-710">Ce changement simplifie l’implémentation des interfaces mentionnées précédemment.</span><span class="sxs-lookup"><span data-stu-id="203d4-710">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="203d4-711">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-711">**Mitigations**</span></span>

<span data-ttu-id="203d4-712">Utilisez les nouvelles méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="203d4-712">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="203d4-713">Modifications de l’API de métadonnées spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="203d4-713">Provider-specific Metadata API changes</span></span>

<span data-ttu-id="203d4-714">Suivi du problème no 214</span><span class="sxs-lookup"><span data-stu-id="203d4-714">[Tracking Issue #214](https://github.com/aspnet/EntityFrameworkCore/issues/214)</span></span>

<span data-ttu-id="203d4-715">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-715">**New behavior**</span></span>

<span data-ttu-id="203d4-716">Les méthodes d’extension spécifiques au fournisseur seront aplaties :</span><span class="sxs-lookup"><span data-stu-id="203d4-716">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="203d4-717">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-717">**Why**</span></span>

<span data-ttu-id="203d4-718">Ce changement simplifie l’implémentation des méthodes d’extension mentionnées précédemment.</span><span class="sxs-lookup"><span data-stu-id="203d4-718">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="203d4-719">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-719">**Mitigations**</span></span>

<span data-ttu-id="203d4-720">Utilisez les nouvelles méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="203d4-720">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="203d4-721">EF Core n’envoie plus de pragma pour l’application de clé étrangère SQLite</span><span class="sxs-lookup"><span data-stu-id="203d4-721">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="203d4-722">Suivi du problème n<c0>o </c0>12151</span><span class="sxs-lookup"><span data-stu-id="203d4-722">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="203d4-723">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-723">**Old behavior**</span></span>

<span data-ttu-id="203d4-724">Avant EF Core 3.0, EF Core envoyait `PRAGMA foreign_keys = 1` quand une connexion à SQLite était ouverte.</span><span class="sxs-lookup"><span data-stu-id="203d4-724">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="203d4-725">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-725">**New behavior**</span></span>

<span data-ttu-id="203d4-726">À compter d’EF Core 3.0, EF Core n’envoie plus de `PRAGMA foreign_keys = 1` quand une connexion à SQLite est ouverte.</span><span class="sxs-lookup"><span data-stu-id="203d4-726">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="203d4-727">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-727">**Why**</span></span>

<span data-ttu-id="203d4-728">Ce changement a été apporté car EF Core utilise `SQLitePCLRaw.bundle_e_sqlite3` par défaut, ce qui signifie que l’application de clé étrangère est activée par défaut et n’a pas besoin d’être activée explicitement chaque fois qu’une connexion est ouverte.</span><span class="sxs-lookup"><span data-stu-id="203d4-728">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="203d4-729">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-729">**Mitigations**</span></span>

<span data-ttu-id="203d4-730">Les clés étrangères sont activées par défaut dans SQLitePCLRaw.bundle_e_sqlite3, qui est utilisé par défaut pour EF Core.</span><span class="sxs-lookup"><span data-stu-id="203d4-730">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="203d4-731">Pour les autres cas, vous pouvez activer les clés étrangères en spécifiant `Foreign Keys=True` dans votre chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="203d4-731">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="203d4-732">Microsoft.EntityFrameworkCore.Sqlite dépend désormais de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="203d4-732">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="203d4-733">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-733">**Old behavior**</span></span>

<span data-ttu-id="203d4-734">Avant EF Core 3.0, EF Core utilisait `SQLitePCLRaw.bundle_green`.</span><span class="sxs-lookup"><span data-stu-id="203d4-734">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="203d4-735">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-735">**New behavior**</span></span>

<span data-ttu-id="203d4-736">À compter d’EF Core 3.0, EF Core utilise `SQLitePCLRaw.bundle_e_sqlite3`.</span><span class="sxs-lookup"><span data-stu-id="203d4-736">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="203d4-737">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-737">**Why**</span></span>

<span data-ttu-id="203d4-738">Ce changement a été apporté afin que la version de SQLite utilisée sur iOS soit cohérente avec d’autres plateformes.</span><span class="sxs-lookup"><span data-stu-id="203d4-738">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="203d4-739">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-739">**Mitigations**</span></span>

<span data-ttu-id="203d4-740">Pour utiliser la version de SQLite native sur iOS, configurez `Microsoft.Data.Sqlite` de façon à utiliser un autre bundle `SQLitePCLRaw`.</span><span class="sxs-lookup"><span data-stu-id="203d4-740">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="203d4-741">Les valeurs GUID sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="203d4-741">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="203d4-742">Suivi du problème #15078</span><span class="sxs-lookup"><span data-stu-id="203d4-742">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="203d4-743">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-743">**Old behavior**</span></span>

<span data-ttu-id="203d4-744">Avant, les valeurs GUID étaient stockées comme valeurs BLOB sur SQLite.</span><span class="sxs-lookup"><span data-stu-id="203d4-744">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="203d4-745">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-745">**New behavior**</span></span>

<span data-ttu-id="203d4-746">Maintenant, les valeurs Guid sont stockées au format TEXT.</span><span class="sxs-lookup"><span data-stu-id="203d4-746">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="203d4-747">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-747">**Why**</span></span>

<span data-ttu-id="203d4-748">Le format binaire des valeurs GUID n’est pas normalisé.</span><span class="sxs-lookup"><span data-stu-id="203d4-748">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="203d4-749">Le stockage des valeurs au format TEXT rend la base de données plus compatible avec d’autres technologies.</span><span class="sxs-lookup"><span data-stu-id="203d4-749">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="203d4-750">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-750">**Mitigations**</span></span>

<span data-ttu-id="203d4-751">Vous pouvez migrer des bases de données existantes vers le nouveau format en exécutant SQL comme suit.</span><span class="sxs-lookup"><span data-stu-id="203d4-751">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

<span data-ttu-id="203d4-752">Dans EF Core, vous pouvez également continuer à utiliser le comportement précédent en configurant un convertisseur de valeur sur ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="203d4-752">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="203d4-753">Microsoft.Data.Sqlite peut toujours lire les valeurs GUID dans les colonnes BLOB et TEXT. Toutefois, en raison du changement du format par défaut pour les paramètres et les constantes, vous devrez probablement apporter des modifications dans la plupart des scénarios utilisant des valeurs GUID.</span><span class="sxs-lookup"><span data-stu-id="203d4-753">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="203d4-754">Les valeurs char sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="203d4-754">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="203d4-755">Suivi du problème n<c0>o </c0>15020</span><span class="sxs-lookup"><span data-stu-id="203d4-755">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="203d4-756">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-756">**Old behavior**</span></span>

<span data-ttu-id="203d4-757">Avant, les valeurs char étaient stockées comme valeurs INTEGER sur SQLite.</span><span class="sxs-lookup"><span data-stu-id="203d4-757">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="203d4-758">Par exemple, une valeur char de *A* était stockée comme valeur entière 65.</span><span class="sxs-lookup"><span data-stu-id="203d4-758">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="203d4-759">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-759">**New behavior**</span></span>

<span data-ttu-id="203d4-760">Maintenant, les valeurs char sont stockées au format TEXT.</span><span class="sxs-lookup"><span data-stu-id="203d4-760">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="203d4-761">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-761">**Why**</span></span>

<span data-ttu-id="203d4-762">Le stockage des valeurs au format TEXT est plus naturel et rend la base de données plus compatible avec d’autres technologies.</span><span class="sxs-lookup"><span data-stu-id="203d4-762">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="203d4-763">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-763">**Mitigations**</span></span>

<span data-ttu-id="203d4-764">Vous pouvez migrer des bases de données existantes vers le nouveau format en exécutant SQL comme suit.</span><span class="sxs-lookup"><span data-stu-id="203d4-764">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="203d4-765">Dans EF Core, vous pouvez également continuer à utiliser le comportement précédent en configurant un convertisseur de valeur sur ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="203d4-765">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="203d4-766">Microsoft.Data.Sqlite est aussi toujours capable de lire les valeurs de caractère à partir de colonnes INTEGER et TEXT, donc certains scénarios peuvent ne nécessiter aucune action.</span><span class="sxs-lookup"><span data-stu-id="203d4-766">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="203d4-767">Les ID de migration sont maintenant générés à l’aide du calendrier de la culture invariante</span><span class="sxs-lookup"><span data-stu-id="203d4-767">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="203d4-768">Suivi du problème n<c0>o </c0>12978</span><span class="sxs-lookup"><span data-stu-id="203d4-768">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="203d4-769">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-769">**Old behavior**</span></span>

<span data-ttu-id="203d4-770">Les ID de migration ont été générés par inadvertance à l’aide du calendrier de la culture actuelle.</span><span class="sxs-lookup"><span data-stu-id="203d4-770">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="203d4-771">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-771">**New behavior**</span></span>

<span data-ttu-id="203d4-772">Maintenant, les ID de migration sont toujours générés à l’aide du calendrier de la culture invariante (grégorien).</span><span class="sxs-lookup"><span data-stu-id="203d4-772">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="203d4-773">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-773">**Why**</span></span>

<span data-ttu-id="203d4-774">L’ordre des migrations est important lors de la mise à jour de la base de données ou de la résolution des conflits de fusion.</span><span class="sxs-lookup"><span data-stu-id="203d4-774">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="203d4-775">L’utilisation du calendrier invariant permet d’éviter les problèmes de classement qui peuvent se produire si les membres de l’équipe ont des calendriers système différents.</span><span class="sxs-lookup"><span data-stu-id="203d4-775">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="203d4-776">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-776">**Mitigations**</span></span>

<span data-ttu-id="203d4-777">Ce changement affecte tous ceux qui utilisent un calendrier non grégorien où l’année est en avance sur le calendrier grégorien (comme le calendrier bouddhiste thaïlandais).</span><span class="sxs-lookup"><span data-stu-id="203d4-777">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="203d4-778">Les ID de migration existants devront être mis à jour afin que les nouvelles migrations soient classées après les migrations existantes.</span><span class="sxs-lookup"><span data-stu-id="203d4-778">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="203d4-779">Vous trouverez les ID de migration dans l’attribut Migration des fichiers de concepteur des migrations.</span><span class="sxs-lookup"><span data-stu-id="203d4-779">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="203d4-780">Le tableau de l’historique Migrations doit également être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="203d4-780">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="203d4-781">UseRowNumberForPaging a été supprimé</span><span class="sxs-lookup"><span data-stu-id="203d4-781">UseRowNumberForPaging has been removed</span></span>

<span data-ttu-id="203d4-782">Suivi de problème no 16400</span><span class="sxs-lookup"><span data-stu-id="203d4-782">[Tracking Issue #16400](https://github.com/aspnet/EntityFrameworkCore/issues/16400)</span></span>

<span data-ttu-id="203d4-783">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-783">**Old behavior**</span></span>

<span data-ttu-id="203d4-784">Avant EF Core 3.0, `UseRowNumberForPaging` pouvait être utilisé pour générer SQL pour la pagination qui est compatible avec SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="203d4-784">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="203d4-785">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-785">**New behavior**</span></span>

<span data-ttu-id="203d4-786">À compter de EF Core 3.0, EF génère uniquement SQL pour la pagination qui est uniquement compatible avec les versions de SQL Server ultérieures.</span><span class="sxs-lookup"><span data-stu-id="203d4-786">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span> 

<span data-ttu-id="203d4-787">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-787">**Why**</span></span>

<span data-ttu-id="203d4-788">Nous effectuons cette modification, car [SQL Server 2008 n’est plus un produit pris en charge](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) et la mise à jour de cette fonctionnalité pour fonctionner avec les modifications de requête effectuées dans EF Core 3.0 est un travail significatif.</span><span class="sxs-lookup"><span data-stu-id="203d4-788">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="203d4-789">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-789">**Mitigations**</span></span>

<span data-ttu-id="203d4-790">Nous vous recommandons d’effectuer la mise à jour vers une version plus récente de SQL Server ou d’utiliser un niveau de compatibilité plus élevé, afin que le SQL généré soit pris en charge.</span><span class="sxs-lookup"><span data-stu-id="203d4-790">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="203d4-791">Cela dit, si vous ne pouvez pas faire cela, veuillez [commenter le problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/16400) en incluant des détails.</span><span class="sxs-lookup"><span data-stu-id="203d4-791">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="203d4-792">Nous pourrions revisiter cette décision en fonction des commentaires.</span><span class="sxs-lookup"><span data-stu-id="203d4-792">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="203d4-793">Les info/métadonnées d’extension ont été supprimées de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="203d4-793">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

<span data-ttu-id="203d4-794">Suivi du problème no 16119</span><span class="sxs-lookup"><span data-stu-id="203d4-794">[Tracking Issue #16119](https://github.com/aspnet/EntityFrameworkCore/issues/16119)</span></span>

<span data-ttu-id="203d4-795">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-795">**Old behavior**</span></span>

<span data-ttu-id="203d4-796">`IDbContextOptionsExtension` contenait des méthodes permettant de fournir des métadonnées relatives à l’extension.</span><span class="sxs-lookup"><span data-stu-id="203d4-796">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="203d4-797">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-797">**New behavior**</span></span>

<span data-ttu-id="203d4-798">Ces méthodes ont été déplacées vers une nouvelle classe de base abstraite `DbContextOptionsExtensionInfo`, qui est retournée à partir d’une nouvelle propriété `IDbContextOptionsExtension.Info`.</span><span class="sxs-lookup"><span data-stu-id="203d4-798">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="203d4-799">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-799">**Why**</span></span>

<span data-ttu-id="203d4-800">Sur les versions de 2.0 à 3.0, nous devions ajouter ou modifier ces méthodes plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="203d4-800">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="203d4-801">Les sortir dans une nouvelle classe de base abstraite simplifie l’apport de ces types de changements sans résiliation des extensions existantes.</span><span class="sxs-lookup"><span data-stu-id="203d4-801">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="203d4-802">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-802">**Mitigations**</span></span>

<span data-ttu-id="203d4-803">Mettre à jour des extensions pour suivre le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="203d4-803">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="203d4-804">Des exemples se trouvent dans la plupart des implémentations de `IDbContextOptionsExtension` pour différents types d’extensions dans le code source EF Core.</span><span class="sxs-lookup"><span data-stu-id="203d4-804">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="203d4-805">LogQueryPossibleExceptionWithAggregateOperator a été renommé</span><span class="sxs-lookup"><span data-stu-id="203d4-805">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="203d4-806">Suivi du problème #10985</span><span class="sxs-lookup"><span data-stu-id="203d4-806">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="203d4-807">**Changement**</span><span class="sxs-lookup"><span data-stu-id="203d4-807">**Change**</span></span>

<span data-ttu-id="203d4-808">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` a été renommé en `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="203d4-808">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="203d4-809">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-809">**Why**</span></span>

<span data-ttu-id="203d4-810">Aligne le nom de cet événement d’avertissement avec tous les autres événements d’avertissement.</span><span class="sxs-lookup"><span data-stu-id="203d4-810">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="203d4-811">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-811">**Mitigations**</span></span>

<span data-ttu-id="203d4-812">Utilisez le nouveau nom.</span><span class="sxs-lookup"><span data-stu-id="203d4-812">Use the new name.</span></span> <span data-ttu-id="203d4-813">(Notez que le numéro d’ID événement n’a pas changé.)</span><span class="sxs-lookup"><span data-stu-id="203d4-813">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="203d4-814">Clarifier les noms de contrainte de clé étrangère dans l’API</span><span class="sxs-lookup"><span data-stu-id="203d4-814">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="203d4-815">Suivi du problème #10730</span><span class="sxs-lookup"><span data-stu-id="203d4-815">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="203d4-816">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-816">**Old behavior**</span></span>

<span data-ttu-id="203d4-817">Avant EF Core 3.0, les noms de contrainte de clé étrangère étaient désignés simplement par le terme « nom ».</span><span class="sxs-lookup"><span data-stu-id="203d4-817">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="203d4-818">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-818">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="203d4-819">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-819">**New behavior**</span></span>

<span data-ttu-id="203d4-820">À compter d’EF Core 3.0, les noms de contrainte de clé étrangère sont désignés par « noms de contrainte ».</span><span class="sxs-lookup"><span data-stu-id="203d4-820">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="203d4-821">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-821">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="203d4-822">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-822">**Why**</span></span>

<span data-ttu-id="203d4-823">Ce changement rend le nommage plus cohérent dans ce domaine. Il clarifie également le fait qu’il s’agit du nom de la contrainte de clé étrangère et pas du nom de la colonne ou de la propriété sur laquelle la clé étrangère est définie.</span><span class="sxs-lookup"><span data-stu-id="203d4-823">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="203d4-824">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-824">**Mitigations**</span></span>

<span data-ttu-id="203d4-825">Utilisez le nouveau nom.</span><span class="sxs-lookup"><span data-stu-id="203d4-825">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="203d4-826">IRelationalDatabaseCreator.HasTables/HasTablesAsync ont été rendues publiques</span><span class="sxs-lookup"><span data-stu-id="203d4-826">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

<span data-ttu-id="203d4-827">Suivi du problème no 15997</span><span class="sxs-lookup"><span data-stu-id="203d4-827">[Tracking Issue #15997](https://github.com/aspnet/EntityFrameworkCore/issues/15997)</span></span>

<span data-ttu-id="203d4-828">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-828">**Old behavior**</span></span>

<span data-ttu-id="203d4-829">Avant EF Core 3.0, ces méthodes étaient protégées.</span><span class="sxs-lookup"><span data-stu-id="203d4-829">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="203d4-830">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-830">**New behavior**</span></span>

<span data-ttu-id="203d4-831">À compter d’EF Core 3.0, ces méthodes sont publiques.</span><span class="sxs-lookup"><span data-stu-id="203d4-831">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="203d4-832">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-832">**Why**</span></span>

<span data-ttu-id="203d4-833">Ces méthodes sont utilisées par EF pour déterminer si une base de données est créée mais vide.</span><span class="sxs-lookup"><span data-stu-id="203d4-833">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="203d4-834">Cela peut également être utile depuis l’extérieur d’EF lorsque vous déterminez s’il faut appliquer des migrations ou pas.</span><span class="sxs-lookup"><span data-stu-id="203d4-834">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="203d4-835">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-835">**Mitigations**</span></span>

<span data-ttu-id="203d4-836">Modifiez l’accessibilité de n’importe quel remplacements.</span><span class="sxs-lookup"><span data-stu-id="203d4-836">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="203d4-837">Microsoft.EntityFrameworkCore.Design est désormais un package DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="203d4-837">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

<span data-ttu-id="203d4-838">Suivi du problème no 11506</span><span class="sxs-lookup"><span data-stu-id="203d4-838">[Tracking Issue #11506](https://github.com/aspnet/EntityFrameworkCore/issues/11506)</span></span>

<span data-ttu-id="203d4-839">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-839">**Old behavior**</span></span>

<span data-ttu-id="203d4-840">Avant EF Core 3.0, Microsoft.EntityFrameworkCore.Design était un package NuGet normal dont l’assembly pouvait être référencée par des projets qui en dépendaient.</span><span class="sxs-lookup"><span data-stu-id="203d4-840">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="203d4-841">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-841">**New behavior**</span></span>

<span data-ttu-id="203d4-842">À compter d’EF Core 3.0, il s’agit d’un package DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="203d4-842">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="203d4-843">Cela signifie que la dépendance ne sera pas transitive dans d’autres projets, et que vous ne pouvez plus, par défaut, référencer son assembly.</span><span class="sxs-lookup"><span data-stu-id="203d4-843">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="203d4-844">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-844">**Why**</span></span>

<span data-ttu-id="203d4-845">Ce package est uniquement destiné à être utilisé au moment du design.</span><span class="sxs-lookup"><span data-stu-id="203d4-845">This package is only intended to be used at design time.</span></span> <span data-ttu-id="203d4-846">Les applications déployées ne doivent pas y faire référence.</span><span class="sxs-lookup"><span data-stu-id="203d4-846">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="203d4-847">Le fait de faire de ce package un DevelopmentDependency renforce cette recommandation.</span><span class="sxs-lookup"><span data-stu-id="203d4-847">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="203d4-848">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-848">**Mitigations**</span></span>

<span data-ttu-id="203d4-849">Si vous avez besoin de référencer ce package pour remplacer le comportement de EF Core au moment de la conception, vous pouvez mettre à jour les métadonnées de l’élément PackageReference dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="203d4-849">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="203d4-850">Si le package est référencé de manière transitive via Microsoft.EntityFrameworkCore.Tools, vous devez ajouter un PackageReference explicite au package pour modifier ses métadonnées.</span><span class="sxs-lookup"><span data-stu-id="203d4-850">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="203d4-851">Une telle référence explicite doit être ajoutée à n’importe quel projet où les types du package sont nécessaires.</span><span class="sxs-lookup"><span data-stu-id="203d4-851">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="203d4-852">SQLitePCL.raw mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="203d4-852">SQLitePCL.raw updated to version 2.0.0</span></span>

<span data-ttu-id="203d4-853">Suivi du problème no 14824</span><span class="sxs-lookup"><span data-stu-id="203d4-853">[Tracking Issue #14824](https://github.com/aspnet/EntityFrameworkCore/issues/14824)</span></span>

<span data-ttu-id="203d4-854">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-854">**Old behavior**</span></span>

<span data-ttu-id="203d4-855">Microsoft.EntityFrameworkCore.Sqlite dépendait précédemment de la version 1.1.12 de SQLitePCL.raw.</span><span class="sxs-lookup"><span data-stu-id="203d4-855">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="203d4-856">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-856">**New behavior**</span></span>

<span data-ttu-id="203d4-857">Nous avons mis à jour notre package pour dépendre de la version 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="203d4-857">We've updated our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="203d4-858">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-858">**Why**</span></span>

<span data-ttu-id="203d4-859">La version 2.0.0 de SQLitePCL.raw cible .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="203d4-859">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="203d4-860">Elle ciblait précédemment .NET Standard 1.1 qui nécessitait une fermeture volumineuse de packages transitifs pour fonctionner.</span><span class="sxs-lookup"><span data-stu-id="203d4-860">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="203d4-861">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-861">**Mitigations**</span></span>

<span data-ttu-id="203d4-862">SQLitePCL.raw version 2.0.0 inclut des changements cassants.</span><span class="sxs-lookup"><span data-stu-id="203d4-862">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="203d4-863">Pour plus d’informations, consultez les [notes de publication](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) .</span><span class="sxs-lookup"><span data-stu-id="203d4-863">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="203d4-864">NetTopologySuite mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="203d4-864">NetTopologySuite updated to version 2.0.0</span></span>

<span data-ttu-id="203d4-865">Suivi de problème no 14825</span><span class="sxs-lookup"><span data-stu-id="203d4-865">[Tracking Issue #14825](https://github.com/aspnet/EntityFrameworkCore/issues/14825)</span></span>

<span data-ttu-id="203d4-866">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-866">**Old behavior**</span></span>

<span data-ttu-id="203d4-867">Les packages spatiaux dépendaient précédemment de la version 1.15.1 de NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="203d4-867">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="203d4-868">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-868">**New behavior**</span></span>

<span data-ttu-id="203d4-869">Nous avons mis à jour notre package pour dépendre de la version 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="203d4-869">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="203d4-870">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-870">**Why**</span></span>

<span data-ttu-id="203d4-871">La version 2.0.0 de NetTopologySuite vise à résoudre plusieurs problèmes de facilité d’utilisation rencontrés par les utilisateurs EF Core.</span><span class="sxs-lookup"><span data-stu-id="203d4-871">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="203d4-872">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-872">**Mitigations**</span></span>

<span data-ttu-id="203d4-873">NetTopologySuite version 2.0.0 inclut des changements cassants.</span><span class="sxs-lookup"><span data-stu-id="203d4-873">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="203d4-874">Pour plus d’informations, consultez les [notes de publication](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) .</span><span class="sxs-lookup"><span data-stu-id="203d4-874">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="203d4-875">Microsoft. Data. SqlClient est utilisé à la place de System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="203d4-875">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="203d4-876">#15636 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="203d4-876">Tracking Issue #15636</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

<span data-ttu-id="203d4-877">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-877">**Old behavior**</span></span>

<span data-ttu-id="203d4-878">Microsoft. EntityFrameworkCore. SqlServer était auparavant tributaire de System. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="203d4-878">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

<span data-ttu-id="203d4-879">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-879">**New behavior**</span></span>

<span data-ttu-id="203d4-880">Nous avons mis à jour notre package pour dépendre de Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="203d4-880">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="203d4-881">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-881">**Why**</span></span>

<span data-ttu-id="203d4-882">Microsoft. Data. SqlClient est le pilote d’accès aux données phare pour la SQL Server à l’avenir, et System. Data. SqlClient ne représente plus le point de vue du développement.</span><span class="sxs-lookup"><span data-stu-id="203d4-882">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="203d4-883">Certaines fonctionnalités importantes, telles que Always Encrypted, sont uniquement disponibles sur Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="203d4-883">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="203d4-884">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-884">**Mitigations**</span></span>

<span data-ttu-id="203d4-885">Si votre code prend une dépendance directe sur System. Data. SqlClient, vous devez le modifier pour qu’il fasse référence à Microsoft. Data. SqlClient à la place. étant donné que les deux packages maintiennent un très haut niveau de compatibilité d’API, il ne doit y avoir qu’une simple modification de package et d’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="203d4-885">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="203d4-886">Plusieurs relations autoréférencées ambiguës doivent être configurées</span><span class="sxs-lookup"><span data-stu-id="203d4-886">Multiple ambiguous self-referencing relationships must be configured</span></span> 

<span data-ttu-id="203d4-887">Suivi de problème no 13573</span><span class="sxs-lookup"><span data-stu-id="203d4-887">[Tracking Issue #13573](https://github.com/aspnet/EntityFrameworkCore/issues/13573)</span></span>

<span data-ttu-id="203d4-888">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-888">**Old behavior**</span></span>

<span data-ttu-id="203d4-889">Un type d’entité avec plusieurs propriétés de navigation unidirectionnelle autoréférencées et les clés étrangères correspondantes a été incorrectement configuré en tant que relation unique.</span><span class="sxs-lookup"><span data-stu-id="203d4-889">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="203d4-890">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-890">For example:</span></span>

```csharp
public class User 
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

<span data-ttu-id="203d4-891">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-891">**New behavior**</span></span>

<span data-ttu-id="203d4-892">Ce scénario est maintenant détecté dans la génération de modèle et une exception est levée, indiquant que le modèle est ambigu.</span><span class="sxs-lookup"><span data-stu-id="203d4-892">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="203d4-893">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-893">**Why**</span></span>

<span data-ttu-id="203d4-894">Le modèle résultant est ambigu et probablement erroné dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="203d4-894">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="203d4-895">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-895">**Mitigations**</span></span>

<span data-ttu-id="203d4-896">Utilisez la configuration complète de la relation.</span><span class="sxs-lookup"><span data-stu-id="203d4-896">Use full configuration of the relationship.</span></span> <span data-ttu-id="203d4-897">Exemple :</span><span class="sxs-lookup"><span data-stu-id="203d4-897">For example:</span></span>

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();
 
 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="203d4-898">DbFunction. Schema étant null ou une chaîne vide, il est configuré pour être dans le schéma par défaut du modèle</span><span class="sxs-lookup"><span data-stu-id="203d4-898">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="203d4-899">#12757 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="203d4-899">Tracking Issue #12757</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

<span data-ttu-id="203d4-900">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-900">**Old behavior**</span></span>

<span data-ttu-id="203d4-901">Un DbFunction configuré avec un schéma sous forme de chaîne vide a été traité comme une fonction intégrée sans schéma.</span><span class="sxs-lookup"><span data-stu-id="203d4-901">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="203d4-902">Par exemple, le code suivant mappera la `DatePart` fonction CLR à la `DATEPART` fonction intégrée sur SqlServer.</span><span class="sxs-lookup"><span data-stu-id="203d4-902">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

<span data-ttu-id="203d4-903">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="203d4-903">**New behavior**</span></span>

<span data-ttu-id="203d4-904">Tous les mappages de DbFunction sont considérés comme mappés à des fonctions définies par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="203d4-904">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="203d4-905">Par conséquent, la valeur de chaîne vide placerait la fonction dans le schéma par défaut pour le modèle.</span><span class="sxs-lookup"><span data-stu-id="203d4-905">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="203d4-906">Ce peut être le schéma configuré explicitement via l’API Fluent `modelBuilder.HasDefaultSchema()` ou `dbo` sinon.</span><span class="sxs-lookup"><span data-stu-id="203d4-906">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

<span data-ttu-id="203d4-907">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="203d4-907">**Why**</span></span>

<span data-ttu-id="203d4-908">Le schéma précédemment vide était un moyen de traiter que la fonction est intégrée, mais cette logique s’applique uniquement à SqlServer, où les fonctions intégrées n’appartiennent à aucun schéma.</span><span class="sxs-lookup"><span data-stu-id="203d4-908">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

<span data-ttu-id="203d4-909">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="203d4-909">**Mitigations**</span></span>

<span data-ttu-id="203d4-910">Configurez la traduction de DbFunction manuellement pour la mapper à une fonction intégrée.</span><span class="sxs-lookup"><span data-stu-id="203d4-910">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```