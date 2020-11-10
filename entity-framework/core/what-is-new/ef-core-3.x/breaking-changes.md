---
title: Dernières modifications apportées à EF Core 3. x-EF Core
description: Liste complète des modifications avec rupture introduites dans Entity Framework Core 3. x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: bfcfb7257091d1b6889f7c0af00ddab10e0e12e3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429310"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="0a217-103">Modifications avec rupture incluses dans EF Core 3. x</span><span class="sxs-lookup"><span data-stu-id="0a217-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="0a217-104">Les modifications d’API et de comportement suivantes peuvent bloquer les applications existantes lors de leur mise à niveau vers 3. x.</span><span class="sxs-lookup"><span data-stu-id="0a217-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="0a217-105">Les changements qui, selon nous, auront une incidence uniquement sur les fournisseurs de base de données sont documentés dans [Changements ayant un impact sur les fournisseurs](xref:core/providers/provider-log).</span><span class="sxs-lookup"><span data-stu-id="0a217-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="0a217-106">Résumé</span><span class="sxs-lookup"><span data-stu-id="0a217-106">Summary</span></span>

| <span data-ttu-id="0a217-107">**Modification critique**</span><span class="sxs-lookup"><span data-stu-id="0a217-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="0a217-108">**Impact**</span><span class="sxs-lookup"><span data-stu-id="0a217-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="0a217-109">Les requêtes LINQ ne sont plus évaluées sur le client</span><span class="sxs-lookup"><span data-stu-id="0a217-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="0a217-110">Importante</span><span class="sxs-lookup"><span data-stu-id="0a217-110">High</span></span>       |
| [<span data-ttu-id="0a217-111">L’outil en ligne de commande EF Core, dotnet ef, ne fait plus partie du SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="0a217-111">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="0a217-112">Importante</span><span class="sxs-lookup"><span data-stu-id="0a217-112">High</span></span>      |
| [<span data-ttu-id="0a217-113">DetectChanges honore les valeurs de clés générées par le magasin</span><span class="sxs-lookup"><span data-stu-id="0a217-113">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="0a217-114">Importante</span><span class="sxs-lookup"><span data-stu-id="0a217-114">High</span></span>      |
| [<span data-ttu-id="0a217-115">FromSql, ExecuteSql et ExecuteSqlAsync ont été renommés</span><span class="sxs-lookup"><span data-stu-id="0a217-115">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="0a217-116">Importante</span><span class="sxs-lookup"><span data-stu-id="0a217-116">High</span></span>      |
| [<span data-ttu-id="0a217-117">Les types de requêtes sont regroupés avec les types d’entités</span><span class="sxs-lookup"><span data-stu-id="0a217-117">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="0a217-118">Importante</span><span class="sxs-lookup"><span data-stu-id="0a217-118">High</span></span>      |
| [<span data-ttu-id="0a217-119">Entity Framework Core ne fait plus partie du framework partagé ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a217-119">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="0a217-120">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-120">Medium</span></span>      |
| [<span data-ttu-id="0a217-121">Les suppressions en cascade se produisent désormais immédiatement par défaut</span><span class="sxs-lookup"><span data-stu-id="0a217-121">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="0a217-122">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-122">Medium</span></span>      |
| [<span data-ttu-id="0a217-123">Le chargement hâtif des entités associées se produit désormais dans une seule requête</span><span class="sxs-lookup"><span data-stu-id="0a217-123">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="0a217-124">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-124">Medium</span></span>      |
| [<span data-ttu-id="0a217-125">La sémantique de DeleteBehavior.Restrict est désormais plus propre</span><span class="sxs-lookup"><span data-stu-id="0a217-125">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="0a217-126">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-126">Medium</span></span>      |
| [<span data-ttu-id="0a217-127">L’API de configuration pour les relations de type détenu a changé</span><span class="sxs-lookup"><span data-stu-id="0a217-127">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="0a217-128">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-128">Medium</span></span>      |
| [<span data-ttu-id="0a217-129">Chaque propriété utilise la génération indépendante de clé de type entier en mémoire</span><span class="sxs-lookup"><span data-stu-id="0a217-129">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="0a217-130">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-130">Medium</span></span>      |
| [<span data-ttu-id="0a217-131">Les requêtes sans suivi ne procèdent plus à la résolution de l’identité</span><span class="sxs-lookup"><span data-stu-id="0a217-131">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="0a217-132">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-132">Medium</span></span>      |
| [<span data-ttu-id="0a217-133">Changements apportés à l’API de métadonnées</span><span class="sxs-lookup"><span data-stu-id="0a217-133">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="0a217-134">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-134">Medium</span></span>      |
| [<span data-ttu-id="0a217-135">Modifications de l’API de métadonnées spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="0a217-135">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="0a217-136">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-136">Medium</span></span>      |
| [<span data-ttu-id="0a217-137">UseRowNumberForPaging a été supprimé</span><span class="sxs-lookup"><span data-stu-id="0a217-137">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="0a217-138">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-138">Medium</span></span>      |
| [<span data-ttu-id="0a217-139">La méthode FromSql quand elle est utilisée avec une procédure stockée ne peut pas être composée</span><span class="sxs-lookup"><span data-stu-id="0a217-139">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="0a217-140">Moyenne</span><span class="sxs-lookup"><span data-stu-id="0a217-140">Medium</span></span>      |
| [<span data-ttu-id="0a217-141">Les méthodes FromSql peuvent uniquement être spécifiées sur les racines de requête</span><span class="sxs-lookup"><span data-stu-id="0a217-141">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="0a217-142">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-142">Low</span></span>      |
| [<span data-ttu-id="0a217-143">Les valeurs de clés temporaires ne sont plus définies sur les instances d’entités</span><span class="sxs-lookup"><span data-stu-id="0a217-143">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="0a217-144">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-144">Low</span></span>      |
| [<span data-ttu-id="0a217-145">Les entités dépendantes qui partagent la table avec le principal sont maintenant facultatives</span><span class="sxs-lookup"><span data-stu-id="0a217-145">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="0a217-146">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-146">Low</span></span>      |
| [<span data-ttu-id="0a217-147">Toutes les entités qui partagent une table avec une colonne de jeton de concurrence doivent la mapper à une propriété</span><span class="sxs-lookup"><span data-stu-id="0a217-147">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="0a217-148">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-148">Low</span></span>      |
| [<span data-ttu-id="0a217-149">Les entités détenues ne peuvent pas être interrogées sans le propriétaire à l’aide d’une requête de suivi</span><span class="sxs-lookup"><span data-stu-id="0a217-149">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="0a217-150">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-150">Low</span></span>      |
| [<span data-ttu-id="0a217-151">Les propriétés héritées de types non mappés sont maintenant mappées à une seule colonne pour tous les types dérivés</span><span class="sxs-lookup"><span data-stu-id="0a217-151">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="0a217-152">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-152">Low</span></span>      |
| [<span data-ttu-id="0a217-153">La convention de propriété de clé étrangère ne correspond plus au nom de la propriété principale</span><span class="sxs-lookup"><span data-stu-id="0a217-153">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="0a217-154">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-154">Low</span></span>      |
| [<span data-ttu-id="0a217-155">La connexion de base de données est maintenant fermée si elle n’est plus utilisée avant la fin de TransactionScope</span><span class="sxs-lookup"><span data-stu-id="0a217-155">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="0a217-156">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-156">Low</span></span>      |
| [<span data-ttu-id="0a217-157">Les champs de stockage sont utilisés par défaut</span><span class="sxs-lookup"><span data-stu-id="0a217-157">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="0a217-158">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-158">Low</span></span>      |
| [<span data-ttu-id="0a217-159">Erreur si plusieurs champs de stockage compatibles sont détectés</span><span class="sxs-lookup"><span data-stu-id="0a217-159">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="0a217-160">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-160">Low</span></span>      |
| [<span data-ttu-id="0a217-161">Les noms de propriété de type « champ uniquement » doivent correspondre au nom du champ</span><span class="sxs-lookup"><span data-stu-id="0a217-161">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="0a217-162">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-162">Low</span></span>      |
| [<span data-ttu-id="0a217-163">AddDbContext/AddDbContextPool n’appelle plus AddLogging et AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="0a217-163">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="0a217-164">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-164">Low</span></span>      |
| [<span data-ttu-id="0a217-165">AddEntityFramework \* ajoute IMemoryCache avec une limite de taille</span><span class="sxs-lookup"><span data-stu-id="0a217-165">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="0a217-166">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-166">Low</span></span>      |
| [<span data-ttu-id="0a217-167">DbContext.Entry effectue maintenant un DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="0a217-167">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="0a217-168">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-168">Low</span></span>      |
| [<span data-ttu-id="0a217-169">Les clés de tableaux d’octets et de chaînes ne sont pas générés par client par défaut</span><span class="sxs-lookup"><span data-stu-id="0a217-169">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="0a217-170">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-170">Low</span></span>      |
| [<span data-ttu-id="0a217-171">ILoggerFactory est désormais un service délimité</span><span class="sxs-lookup"><span data-stu-id="0a217-171">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="0a217-172">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-172">Low</span></span>      |
| [<span data-ttu-id="0a217-173">Les proxys à chargement différé ne partent plus du principe que les propriétés de navigation sont entièrement chargées</span><span class="sxs-lookup"><span data-stu-id="0a217-173">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="0a217-174">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-174">Low</span></span>      |
| [<span data-ttu-id="0a217-175">La création excessive de fournisseurs de services internes est maintenant une erreur par défaut</span><span class="sxs-lookup"><span data-stu-id="0a217-175">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="0a217-176">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-176">Low</span></span>      |
| [<span data-ttu-id="0a217-177">Nouveau comportement de HasOne/HasMany appelé avec une chaîne unique</span><span class="sxs-lookup"><span data-stu-id="0a217-177">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="0a217-178">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-178">Low</span></span>      |
| [<span data-ttu-id="0a217-179">Le type de retour Task pour plusieurs méthodes asynchrones a été remplacé par ValueTask</span><span class="sxs-lookup"><span data-stu-id="0a217-179">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="0a217-180">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-180">Low</span></span>      |
| [<span data-ttu-id="0a217-181">L’annotation Relational:TypeMapping est désormais simplement TypeMapping</span><span class="sxs-lookup"><span data-stu-id="0a217-181">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="0a217-182">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-182">Low</span></span>      |
| [<span data-ttu-id="0a217-183">ToTable sur un type dérivé lève une exception</span><span class="sxs-lookup"><span data-stu-id="0a217-183">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="0a217-184">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-184">Low</span></span>      |
| [<span data-ttu-id="0a217-185">EF Core n’envoie plus de pragma pour l’application de clé étrangère SQLite</span><span class="sxs-lookup"><span data-stu-id="0a217-185">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="0a217-186">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-186">Low</span></span>      |
| [<span data-ttu-id="0a217-187">Microsoft.EntityFrameworkCore.Sqlite dépend désormais de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="0a217-187">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="0a217-188">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-188">Low</span></span>      |
| [<span data-ttu-id="0a217-189">Les valeurs GUID sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="0a217-189">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="0a217-190">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-190">Low</span></span>      |
| [<span data-ttu-id="0a217-191">Les valeurs char sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="0a217-191">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="0a217-192">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-192">Low</span></span>      |
| [<span data-ttu-id="0a217-193">Les ID de migration sont maintenant générés à l’aide du calendrier de la culture invariante</span><span class="sxs-lookup"><span data-stu-id="0a217-193">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="0a217-194">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-194">Low</span></span>      |
| [<span data-ttu-id="0a217-195">Les infos/métadonnées d’extension ont été supprimées de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="0a217-195">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="0a217-196">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-196">Low</span></span>      |
| [<span data-ttu-id="0a217-197">LogQueryPossibleExceptionWithAggregateOperator a été renommé</span><span class="sxs-lookup"><span data-stu-id="0a217-197">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="0a217-198">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-198">Low</span></span>      |
| [<span data-ttu-id="0a217-199">Clarifier les noms de contrainte de clé étrangère dans l’API</span><span class="sxs-lookup"><span data-stu-id="0a217-199">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="0a217-200">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-200">Low</span></span>      |
| [<span data-ttu-id="0a217-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync ont été rendues publiques</span><span class="sxs-lookup"><span data-stu-id="0a217-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="0a217-202">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-202">Low</span></span>      |
| [<span data-ttu-id="0a217-203">Microsoft.EntityFrameworkCore.Design est désormais un package DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="0a217-203">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="0a217-204">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-204">Low</span></span>      |
| [<span data-ttu-id="0a217-205">SQLitePCL.raw mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="0a217-205">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="0a217-206">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-206">Low</span></span>      |
| [<span data-ttu-id="0a217-207">NetTopologySuite mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="0a217-207">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="0a217-208">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-208">Low</span></span>      |
| [<span data-ttu-id="0a217-209">Microsoft. Data. SqlClient est utilisé à la place de System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="0a217-209">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="0a217-210">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-210">Low</span></span>      |
| [<span data-ttu-id="0a217-211">Plusieurs relations autoréférencées ambiguës doivent être configurées</span><span class="sxs-lookup"><span data-stu-id="0a217-211">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="0a217-212">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-212">Low</span></span>      |
| [<span data-ttu-id="0a217-213">DbFunction. Schema étant null ou une chaîne vide, il est configuré pour être dans le schéma par défaut du modèle</span><span class="sxs-lookup"><span data-stu-id="0a217-213">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="0a217-214">Faible</span><span class="sxs-lookup"><span data-stu-id="0a217-214">Low</span></span>      |
| [<span data-ttu-id="0a217-215">~~EF Core 3,0 cibles .NET Standard 2,1 au lieu de .NET Standard 2,0~~ Rétablie</span><span class="sxs-lookup"><span data-stu-id="0a217-215">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>](#netstandard21) | |
| [<span data-ttu-id="0a217-216">~~L’exécution de requêtes est enregistrée au niveau du débogage ~~Rétabli</span><span class="sxs-lookup"><span data-stu-id="0a217-216">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | |

## <a name="high-impact-changes"></a><span data-ttu-id="0a217-217">Modifications à fort impact</span><span class="sxs-lookup"><span data-stu-id="0a217-217">High-impact changes</span></span>

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="0a217-218">Les requêtes LINQ ne sont plus évaluées sur le client</span><span class="sxs-lookup"><span data-stu-id="0a217-218">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="0a217-219">#14935 du problème de [suivi](https://github.com/dotnet/efcore/issues/14935) 
 [Consultez également le #12795 du problème](https://github.com/dotnet/efcore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="0a217-219">[Tracking Issue #14935](https://github.com/dotnet/efcore/issues/14935)
[Also see issue #12795](https://github.com/dotnet/efcore/issues/12795)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-220">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-220">Old behavior</span></span>

<span data-ttu-id="0a217-221">Avant la version 3.0, quand EF Core ne pouvait pas convertir une expression faisant partie d’une requête en SQL ou en paramètre, elle évaluait automatiquement l’expression sur le client.</span><span class="sxs-lookup"><span data-stu-id="0a217-221">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="0a217-222">Par défaut, l’évaluation sur le client d’expressions potentiellement coûteuses déclenchait uniquement un avertissement.</span><span class="sxs-lookup"><span data-stu-id="0a217-222">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-223">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-223">New behavior</span></span>

<span data-ttu-id="0a217-224">À compter de la version 3.0, EF Core autorise uniquement l’évaluation sur le client des expressions qui figurent dans la projection de niveau supérieur (le dernier appel `Select()` dans la requête).</span><span class="sxs-lookup"><span data-stu-id="0a217-224">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="0a217-225">Quand des expressions d’une autre partie de la requête ne peuvent pas être converties en SQL ou en paramètre, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="0a217-225">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-226">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-226">Why</span></span>

<span data-ttu-id="0a217-227">L’évaluation automatique des requêtes sur le client permet à de nombreuses requêtes d’être exécutées même si certaines de leurs parties importantes ne peuvent pas être traduites.</span><span class="sxs-lookup"><span data-stu-id="0a217-227">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="0a217-228">Ce comportement peut entraîner un comportement inattendu et potentiellement dangereux qui peut devenir évident uniquement en production.</span><span class="sxs-lookup"><span data-stu-id="0a217-228">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="0a217-229">Par exemple, une condition dans un appel `Where()` qui ne peut pas être traduite peut provoquer le transfert de toutes les lignes de la table hors du serveur de base de données, et l’application du filtre sur le client.</span><span class="sxs-lookup"><span data-stu-id="0a217-229">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="0a217-230">Cette situation peut facilement passer inaperçue si la table contient uniquement quelques lignes lors du développement, mais poser davantage de problèmes quand l’application passe en production, où la table peut contenir plusieurs millions de lignes.</span><span class="sxs-lookup"><span data-stu-id="0a217-230">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="0a217-231">Les avertissements relatifs à l’évaluation sur le client étaient également trop faciles à ignorer pendant le développement.</span><span class="sxs-lookup"><span data-stu-id="0a217-231">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="0a217-232">De plus, l’évaluation automatique sur le client peut entraîner des problèmes selon lesquels l’amélioration de la traduction des requêtes pour des expressions spécifiques provoque un changement cassant involontaire entre les versions.</span><span class="sxs-lookup"><span data-stu-id="0a217-232">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-233">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-233">Mitigations</span></span>

<span data-ttu-id="0a217-234">Si une requête ne peut pas être entièrement traduite, réécrivez-la sous une forme qui peut être traduite ou utilisez `AsEnumerable()`, `ToList()` ou autre méthode similaire pour réimporter explicitement les données sur le client, où elles peuvent ensuite être traitées à l’aide de LINQ-to-Objects.</span><span class="sxs-lookup"><span data-stu-id="0a217-234">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="no-longer"></a>

## <a name="medium-impact-changes"></a><span data-ttu-id="0a217-235">Modifications à moyenne impact</span><span class="sxs-lookup"><span data-stu-id="0a217-235">Medium-impact changes</span></span>

### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="0a217-236">Entity Framework Core ne fait plus partie du framework partagé ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a217-236">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="0a217-237">Annonces de suivi du problème n° 325</span><span class="sxs-lookup"><span data-stu-id="0a217-237">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-238">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-238">Old behavior</span></span>

<span data-ttu-id="0a217-239">Avant ASP.NET Core 3.0, quand vous ajoutiez une référence de package à `Microsoft.AspNetCore.App` ou `Microsoft.AspNetCore.All`, elle incluait EF Core et certains des fournisseurs de données EF Core tels que le fournisseur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="0a217-239">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-240">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-240">New behavior</span></span>

<span data-ttu-id="0a217-241">À compter de la version 3.0, le framework partagé ASP.NET Core n’inclut ni EF Core, ni aucun fournisseur de données EF Core.</span><span class="sxs-lookup"><span data-stu-id="0a217-241">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-242">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-242">Why</span></span>

<span data-ttu-id="0a217-243">Avant ce changement, l’obtention d’EF Core nécessitait différentes étapes selon que l’application ciblait ASP.NET Core et SQL Server ou non.</span><span class="sxs-lookup"><span data-stu-id="0a217-243">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span>
<span data-ttu-id="0a217-244">De plus, la mise à niveau d’ASP.NET Core forçait la mise à niveau d’EF Core et du fournisseur SQL Server, ce qui n’est pas toujours souhaitable.</span><span class="sxs-lookup"><span data-stu-id="0a217-244">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="0a217-245">Avec ce changement, l’expérience d’obtention d’EF Core est la même pour tous les fournisseurs, implémentations .NET prises en charge et types d’applications.</span><span class="sxs-lookup"><span data-stu-id="0a217-245">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="0a217-246">Les développeurs peuvent désormais contrôler exactement quand EF Core et les fournisseurs de données EF Core sont mis à niveau.</span><span class="sxs-lookup"><span data-stu-id="0a217-246">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-247">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-247">Mitigations</span></span>

<span data-ttu-id="0a217-248">Pour utiliser EF Core dans une application ASP.NET Core 3.0 ou toute autre application prise en charge, ajoutez explicitement une référence de package au fournisseur de base de données EF Core que votre application utilisera.</span><span class="sxs-lookup"><span data-stu-id="0a217-248">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>

### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="0a217-249">L’outil en ligne de commande EF Core, dotnet ef, ne fait plus partie du SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="0a217-249">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="0a217-250">Suivi du problème n° 14016</span><span class="sxs-lookup"><span data-stu-id="0a217-250">Tracking Issue #14016</span></span>](https://github.com/dotnet/efcore/issues/14016)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-251">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-251">Old behavior</span></span>

<span data-ttu-id="0a217-252">Avant la version 3.0, l’outil `dotnet ef` était inclus dans le SDK .NET Core et prêt à l’emploi depuis la ligne de commande d’un projet sans nécessiter d’étapes supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="0a217-252">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-253">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-253">New behavior</span></span>

<span data-ttu-id="0a217-254">À compter de la version 3.0, le SDK .NET n’inclut pas l’outil `dotnet ef`, donc vous pouvez explicitement l’installer pour pouvoir l’utiliser comme outil local ou global.</span><span class="sxs-lookup"><span data-stu-id="0a217-254">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-255">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-255">Why</span></span>

<span data-ttu-id="0a217-256">Ce changement nous permet de distribuer et mettre à jour `dotnet ef` sous forme d’outil CLI .NET normal sur NuGet, ce qui est cohérent avec le fait qu’EF Core 3.0 est également toujours distribué sous forme de package NuGet.</span><span class="sxs-lookup"><span data-stu-id="0a217-256">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-257">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-257">Mitigations</span></span>

<span data-ttu-id="0a217-258">Pour pouvoir gérer des migrations ou générer un `DbContext`, installez `dotnet-ef` comme outil général :</span><span class="sxs-lookup"><span data-stu-id="0a217-258">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="0a217-259">Vous pouvez également obtenir un outil local quand vous restaurez les dépendances d’un projet qui le déclare en tant que dépendance d’outil à l’aide d’un [fichier manifeste d’outil](/dotnet/core/tools/global-tools#install-a-local-tool).</span><span class="sxs-lookup"><span data-stu-id="0a217-259">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="0a217-260">Modifications à faible impact</span><span class="sxs-lookup"><span data-stu-id="0a217-260">Low-impact changes</span></span>

<a name="fromsql"></a>

### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="0a217-261">FromSql, ExecuteSql et ExecuteSqlAsync ont été renommés</span><span class="sxs-lookup"><span data-stu-id="0a217-261">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="0a217-262">Suivi du problème n<c0>o </c0>10996</span><span class="sxs-lookup"><span data-stu-id="0a217-262">Tracking Issue #10996</span></span>](https://github.com/dotnet/efcore/issues/10996)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-263">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-263">Old behavior</span></span>

<span data-ttu-id="0a217-264">Avant EF Core 3.0, ces noms de méthode étaient surchargés pour être utilisés avec une chaîne normale ou une chaîne devant être interpolée dans SQL et dans des paramètres.</span><span class="sxs-lookup"><span data-stu-id="0a217-264">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-265">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-265">New behavior</span></span>

<span data-ttu-id="0a217-266">À compter d’EF Core 3.0, utilisez `FromSqlRaw`, `ExecuteSqlRaw` et `ExecuteSqlRawAsync` pour créer une requête paramétrable, où les paramètres sont passés séparément à partir de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="0a217-266">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="0a217-267">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-267">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="0a217-268">Utilisez `FromSqlInterpolated`, `ExecuteSqlInterpolated` et `ExecuteSqlInterpolatedAsync` pour créer une requête paramétrable, où les paramètres sont passés dans le cadre d’une chaîne de requête interpolée.</span><span class="sxs-lookup"><span data-stu-id="0a217-268">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="0a217-269">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-269">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="0a217-270">Notez que les deux requêtes ci-dessus produisent le même SQL paramétrable avec les mêmes paramètres SQL.</span><span class="sxs-lookup"><span data-stu-id="0a217-270">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-271">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-271">Why</span></span>

<span data-ttu-id="0a217-272">Les surcharges de méthode comme celle-ci rendent très facile un appel accidentel à la méthode de chaîne brute quand l’intention est d’appeler la méthode de chaîne interpolée, et inversement.</span><span class="sxs-lookup"><span data-stu-id="0a217-272">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="0a217-273">De ce fait, les requêtes ne sont plus paramétrables alors qu’elles devraient l’être.</span><span class="sxs-lookup"><span data-stu-id="0a217-273">This could result in queries not being parameterized when they should have been.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-274">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-274">Mitigations</span></span>

<span data-ttu-id="0a217-275">Utilisez plutôt les nouveaux noms de méthode.</span><span class="sxs-lookup"><span data-stu-id="0a217-275">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>

### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="0a217-276">La méthode FromSql quand elle est utilisée avec une procédure stockée ne peut pas être composée</span><span class="sxs-lookup"><span data-stu-id="0a217-276">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="0a217-277">#15392 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="0a217-277">Tracking Issue #15392</span></span>](https://github.com/dotnet/efcore/issues/15392)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-278">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-278">Old behavior</span></span>

<span data-ttu-id="0a217-279">Avant le EF Core 3,0, la méthode FromSql a tenté de détecter si le SQL passé peut être composé.</span><span class="sxs-lookup"><span data-stu-id="0a217-279">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="0a217-280">Il a fait l’évaluation du client lorsque le SQL n’était pas composable comme une procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="0a217-280">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="0a217-281">La requête suivante a fonctionné en exécutant la procédure stockée sur le serveur et en procédant à l’opération FirstOrDefault côté client.</span><span class="sxs-lookup"><span data-stu-id="0a217-281">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

#### <a name="new-behavior"></a><span data-ttu-id="0a217-282">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-282">New behavior</span></span>

<span data-ttu-id="0a217-283">À compter de EF Core 3,0, EF Core n’essaiera pas d’analyser le SQL.</span><span class="sxs-lookup"><span data-stu-id="0a217-283">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="0a217-284">Par conséquent, si vous effectuez une composition après FromSqlRaw/FromSqlInterpolated, EF Core compose le SQL en provoquant une sous-requête.</span><span class="sxs-lookup"><span data-stu-id="0a217-284">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="0a217-285">Par conséquent, si vous utilisez une procédure stockée avec la composition, vous obtiendrez une exception pour la syntaxe SQL non valide.</span><span class="sxs-lookup"><span data-stu-id="0a217-285">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-286">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-286">Why</span></span>

<span data-ttu-id="0a217-287">EF Core 3,0 ne prend pas en charge l’évaluation automatique du client, car il s’agit d’une erreur, comme expliqué [ici](#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="0a217-287">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-288">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-288">Mitigations</span></span>

<span data-ttu-id="0a217-289">Si vous utilisez une procédure stockée dans FromSqlRaw/FromSqlInterpolated, vous savez qu’elle ne peut pas être composée. vous pouvez donc ajouter __AsEnumerable/AsAsyncEnumerable__ juste après l’appel de la méthode FromSql pour éviter toute composition côté serveur.</span><span class="sxs-lookup"><span data-stu-id="0a217-289">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="0a217-290">Les méthodes FromSql peuvent uniquement être spécifiées sur les racines de requête</span><span class="sxs-lookup"><span data-stu-id="0a217-290">FromSql methods can only be specified on query roots</span></span>

<span data-ttu-id="0a217-291">Suivi de problème no 15704</span><span class="sxs-lookup"><span data-stu-id="0a217-291">[Tracking Issue #15704](https://github.com/dotnet/efcore/issues/15704)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-292">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-292">Old behavior</span></span>

<span data-ttu-id="0a217-293">Avant EF Core 3.0, la méthode `FromSql` pouvant être spécifiée n’importe où dans la requête.</span><span class="sxs-lookup"><span data-stu-id="0a217-293">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-294">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-294">New behavior</span></span>

<span data-ttu-id="0a217-295">À partir d’EF Core 3.0, les nouvelles méthodes `FromSqlRaw` et `FromSqlInterpolated` (qui remplacent `FromSql`) peuvent être spécifiées uniquement sur les racines de requête, par exemple, directement sur le `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="0a217-295">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="0a217-296">Une erreur de compilation survient si vous tentez de les spécifier à un autre emplacement.</span><span class="sxs-lookup"><span data-stu-id="0a217-296">Attempting to specify them anywhere else will result in a compilation error.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-297">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-297">Why</span></span>

<span data-ttu-id="0a217-298">La spécification de `FromSql` n’importe où autre que sur un `DbSet` n’avait aucune signification ni valeur ajoutée et pouvait entraîner une ambiguïté dans certains scénarios.</span><span class="sxs-lookup"><span data-stu-id="0a217-298">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-299">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-299">Mitigations</span></span>

<span data-ttu-id="0a217-300">Les appels `FromSql` doivent être déplacés pour être directement sur le `DbSet` auquel ils s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="0a217-300">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>

### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="0a217-301">Les requêtes sans suivi ne procèdent plus à la résolution de l’identité</span><span class="sxs-lookup"><span data-stu-id="0a217-301">No-tracking queries no longer perform identity resolution</span></span>

<span data-ttu-id="0a217-302">Suivi de problème no 13518</span><span class="sxs-lookup"><span data-stu-id="0a217-302">[Tracking Issue #13518](https://github.com/dotnet/efcore/issues/13518)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-303">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-303">Old behavior</span></span>

<span data-ttu-id="0a217-304">Avant EF Core 3.0, la même instance d’entité était utilisée pour chaque occurrence d’une entité avec un type et un ID donnés.</span><span class="sxs-lookup"><span data-stu-id="0a217-304">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="0a217-305">Cela correspond au comportement des requêtes de suivi.</span><span class="sxs-lookup"><span data-stu-id="0a217-305">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="0a217-306">Par exemple, cette requête :</span><span class="sxs-lookup"><span data-stu-id="0a217-306">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```

<span data-ttu-id="0a217-307">retournait la même instance `Category` pour chaque `Product` associé à la catégorie donnée.</span><span class="sxs-lookup"><span data-stu-id="0a217-307">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-308">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-308">New behavior</span></span>

<span data-ttu-id="0a217-309">À compter de EF Core 3.0, différentes instances d’entité sont créées lorsqu’une entité avec un type et un ID donnés est rencontrée à différents emplacements dans le graphe retourné.</span><span class="sxs-lookup"><span data-stu-id="0a217-309">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="0a217-310">Par exemple, la requête ci-dessus retourne à présent une nouvelle instance `Category` pour chaque `Product` même si deux produits sont associés à la même catégorie.</span><span class="sxs-lookup"><span data-stu-id="0a217-310">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-311">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-311">Why</span></span>

<span data-ttu-id="0a217-312">La résolution de l’identité (autrement dit, le fait de déterminer qu’une entité a les mêmes type et ID qu’une entité précédemment rencontrée) améliore les performances et la surcharge de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="0a217-312">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="0a217-313">Cela agit généralement à l’opposé de la raison pour laquelle aucune requête de suivi n’est utilisée en premier lieu.</span><span class="sxs-lookup"><span data-stu-id="0a217-313">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="0a217-314">En outre, même si la résolution de l’identité peut parfois être utile, elle n’est pas nécessaire si les entités doivent être sérialisées et envoyées à un client, ce qui est courant pour les requêtes sans suivi.</span><span class="sxs-lookup"><span data-stu-id="0a217-314">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-315">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-315">Mitigations</span></span>

<span data-ttu-id="0a217-316">Utilisez une requête de suivi si la résolution de l’identité est requise.</span><span class="sxs-lookup"><span data-stu-id="0a217-316">Use a tracking query if identity resolution is required.</span></span>

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="0a217-317">Les valeurs de clés temporaires ne sont plus définies sur les instances d’entités</span><span class="sxs-lookup"><span data-stu-id="0a217-317">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="0a217-318">Suivi du problème n<c0>o </c0>12378</span><span class="sxs-lookup"><span data-stu-id="0a217-318">Tracking Issue #12378</span></span>](https://github.com/dotnet/efcore/issues/12378)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-319">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-319">Old behavior</span></span>

<span data-ttu-id="0a217-320">Avant EF Core 3.0, des valeurs temporaires étaient affectées à toutes les propriétés de clé qui auraient plus tard une valeur réelle générée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="0a217-320">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="0a217-321">Généralement, ces valeurs temporaires étaient de grands nombres négatifs.</span><span class="sxs-lookup"><span data-stu-id="0a217-321">Usually these temporary values were large negative numbers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-322">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-322">New behavior</span></span>

<span data-ttu-id="0a217-323">À compter de la version 3.0, EF Core stocke la valeur de clé temporaire dans le cadre des informations de suivi de l’entité, et laisse la propriété de clé proprement dite inchangée.</span><span class="sxs-lookup"><span data-stu-id="0a217-323">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-324">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-324">Why</span></span>

<span data-ttu-id="0a217-325">Ce changement a été apporté afin d’empêcher que les valeurs de clés temporaires ne deviennent à tort permanentes quand une entité qui a été suivie par une instance `DbContext` est déplacée vers une autre instance `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="0a217-325">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-326">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-326">Mitigations</span></span>

<span data-ttu-id="0a217-327">Les applications qui affectent des valeurs de clés primaires sur des clés étrangères afin de former des associations entre des entités peuvent dépendre de l’ancien comportement si les clés primaires sont générées par le magasin et appartiennent à des entités à l’état `Added`.</span><span class="sxs-lookup"><span data-stu-id="0a217-327">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="0a217-328">Vous pouvez éviter cela en :</span><span class="sxs-lookup"><span data-stu-id="0a217-328">This can be avoided by:</span></span>

* <span data-ttu-id="0a217-329">N’utilisant pas de clés générées par le magasin.</span><span class="sxs-lookup"><span data-stu-id="0a217-329">Not using store-generated keys.</span></span>
* <span data-ttu-id="0a217-330">Définissant des propriétés de navigation afin d’établir des relations au lieu de définir des valeurs de clés étrangères.</span><span class="sxs-lookup"><span data-stu-id="0a217-330">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="0a217-331">Obtenant les valeurs de clés temporaires réelles à partir des informations de suivi de l’entité.</span><span class="sxs-lookup"><span data-stu-id="0a217-331">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="0a217-332">Par exemple, `context.Entry(blog).Property(e => e.Id).CurrentValue` retournera la valeur temporaire même si la propriété `blog.Id` elle-même n’a pas été définie.</span><span class="sxs-lookup"><span data-stu-id="0a217-332">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="0a217-333">DetectChanges honore les valeurs de clés générées par le magasin</span><span class="sxs-lookup"><span data-stu-id="0a217-333">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="0a217-334">Suivi du problème n<c0>o </c0>14616</span><span class="sxs-lookup"><span data-stu-id="0a217-334">Tracking Issue #14616</span></span>](https://github.com/dotnet/efcore/issues/14616)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-335">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-335">Old behavior</span></span>

<span data-ttu-id="0a217-336">Avant EF Core 3.0, une entité non suivie détectée par `DetectChanges` était suivie à l’état `Added` et insérée en tant que nouvelle ligne quand `SaveChanges` était appelée.</span><span class="sxs-lookup"><span data-stu-id="0a217-336">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-337">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-337">New behavior</span></span>

<span data-ttu-id="0a217-338">À compter d’EF Core 3.0, si une entité utilise des valeurs de clés générées et qu’une valeur de clé est définie, alors l’entité est suivie à l’état `Modified`.</span><span class="sxs-lookup"><span data-stu-id="0a217-338">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="0a217-339">Cela signifie qu’une ligne pour l’entité est supposée exister et qu’elle sera mise à jour lors de l’appel à `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="0a217-339">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="0a217-340">Si la valeur de clé n’est pas définie, ou si le type d’entité n’utilise pas de clés générées, alors la nouvelle entité sera quand même suivie comme `Added`, comme dans les versions précédentes.</span><span class="sxs-lookup"><span data-stu-id="0a217-340">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-341">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-341">Why</span></span>

<span data-ttu-id="0a217-342">Ce changement a été apporté afin de simplifier l’utilisation des graphes d’entités déconnectées lors de l’utilisation de clés générées par le magasin.</span><span class="sxs-lookup"><span data-stu-id="0a217-342">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-343">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-343">Mitigations</span></span>

<span data-ttu-id="0a217-344">Ce changement peut casser une application si un type d’entité est configuré pour utiliser des clés générés, mais que les valeurs de clés sont définies explicitement pour les nouvelles instances.</span><span class="sxs-lookup"><span data-stu-id="0a217-344">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="0a217-345">La solution consiste à configurer explicitement les propriétés de clés de façon à ne pas utiliser des valeurs générées.</span><span class="sxs-lookup"><span data-stu-id="0a217-345">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="0a217-346">Par exemple, avec l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="0a217-346">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="0a217-347">Ou avec des annotations de données :</span><span class="sxs-lookup"><span data-stu-id="0a217-347">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

<a name="cascade"></a>

### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="0a217-348">Les suppressions en cascade se produisent désormais immédiatement par défaut</span><span class="sxs-lookup"><span data-stu-id="0a217-348">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="0a217-349">Suivi du problème n<c0>o </c0>10114</span><span class="sxs-lookup"><span data-stu-id="0a217-349">Tracking Issue #10114</span></span>](https://github.com/dotnet/efcore/issues/10114)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-350">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-350">Old behavior</span></span>

<span data-ttu-id="0a217-351">Avant la version 3.0, les actions en cascade appliquées par EF Core (suppression d’entités dépendantes quand un principal requis est supprimé ou quand la relation à un principal requis est interrompue) ne se produisaient qu’une fois que SaveChanges était appelée.</span><span class="sxs-lookup"><span data-stu-id="0a217-351">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-352">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-352">New behavior</span></span>

<span data-ttu-id="0a217-353">À compter de la version 3.0, EF Core applique les actions en cascade dès que la condition de déclenchement est détectée.</span><span class="sxs-lookup"><span data-stu-id="0a217-353">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="0a217-354">Par exemple, si vous appelez `context.Remove()` pour supprimer une entité principale, toutes les dépendances requises suivies associées seront également définies immédiatement sur `Deleted`.</span><span class="sxs-lookup"><span data-stu-id="0a217-354">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-355">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-355">Why</span></span>

<span data-ttu-id="0a217-356">Ce changement a été apporté afin d’améliorer l’expérience de liaison de données et les scénarios d’audit où il est important de comprendre quelles entités seront supprimées _avant l’appel à_ `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="0a217-356">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-357">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-357">Mitigations</span></span>

<span data-ttu-id="0a217-358">Vous pouvez restaurer le comportement précédent par le biais des paramètres sur `context.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="0a217-358">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="0a217-359">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-359">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

<a name="eager-loading-single-query"></a>

### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="0a217-360">Le chargement hâtif des entités associées se produit désormais dans une seule requête</span><span class="sxs-lookup"><span data-stu-id="0a217-360">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="0a217-361">#18022 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="0a217-361">Tracking issue #18022</span></span>](https://github.com/dotnet/efcore/issues/18022)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-362">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-362">Old behavior</span></span>

<span data-ttu-id="0a217-363">Avant le 3,0, le chargement des navigations de collections `Include` à l’aide d’opérateurs entraînait la génération de plusieurs requêtes sur une base de données relationnelle, une pour chaque type d’entité associée.</span><span class="sxs-lookup"><span data-stu-id="0a217-363">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-364">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-364">New behavior</span></span>

<span data-ttu-id="0a217-365">À partir de 3,0, EF Core génère une requête unique avec des JOINTUREs sur des bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="0a217-365">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-366">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-366">Why</span></span>

<span data-ttu-id="0a217-367">L’émission de plusieurs requêtes pour implémenter une seule requête LINQ a entraîné de nombreux problèmes, notamment des performances négatives lorsque plusieurs allers-retours de base de données étaient nécessaires et des problèmes de cohérence des données à mesure que chaque requête pouvait observer un état différent de la base de données.</span><span class="sxs-lookup"><span data-stu-id="0a217-367">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-368">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-368">Mitigations</span></span>

<span data-ttu-id="0a217-369">Techniquement, ce n’est pas une modification avec rupture, mais cela peut avoir un impact considérable sur les performances de l’application lorsqu’une seule requête contient un grand nombre d' `Include` opérateurs dans les navigations de collection.</span><span class="sxs-lookup"><span data-stu-id="0a217-369">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="0a217-370">Pour plus d’informations et pour réécrire des requêtes de manière plus efficace, [consultez ce commentaire](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) .</span><span class="sxs-lookup"><span data-stu-id="0a217-370">[See this comment](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>

### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="0a217-371">La sémantique de DeleteBehavior.Restrict est désormais plus propre</span><span class="sxs-lookup"><span data-stu-id="0a217-371">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="0a217-372">Suivi de problème no 12661</span><span class="sxs-lookup"><span data-stu-id="0a217-372">Tracking Issue #12661</span></span>](https://github.com/dotnet/efcore/issues/12661)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-373">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-373">Old behavior</span></span>

<span data-ttu-id="0a217-374">Avant la version 3.0, `DeleteBehavior.Restrict` créait les clés étrangères dans la base de données avec la sémantique `Restrict`, et il modifiait les corrections internes d’une manière non évidente.</span><span class="sxs-lookup"><span data-stu-id="0a217-374">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-375">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-375">New behavior</span></span>

<span data-ttu-id="0a217-376">À compter de la version 3.0, `DeleteBehavior.Restrict` garantit la création des clés étrangères avec la sémantique `Restrict` (autrement dit, sans levée de cascades lors d’une violation de contrainte), sans impact sur les corrections internes EF.</span><span class="sxs-lookup"><span data-stu-id="0a217-376">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-377">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-377">Why</span></span>

<span data-ttu-id="0a217-378">Ce changement a été apporté pour améliorer l’expérience et permettre une utilisation intuitive de `DeleteBehavior`, sans effets secondaires inattendus.</span><span class="sxs-lookup"><span data-stu-id="0a217-378">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-379">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-379">Mitigations</span></span>

<span data-ttu-id="0a217-380">Vous pouvez restaurer le comportement précédent par le biais de `DeleteBehavior.ClientNoAction`.</span><span class="sxs-lookup"><span data-stu-id="0a217-380">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>

### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="0a217-381">Les types de requêtes sont regroupés avec les types d’entités</span><span class="sxs-lookup"><span data-stu-id="0a217-381">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="0a217-382">Suivi du problème n<c0>o </c0>14194</span><span class="sxs-lookup"><span data-stu-id="0a217-382">Tracking Issue #14194</span></span>](https://github.com/dotnet/efcore/issues/14194)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-383">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-383">Old behavior</span></span>

<span data-ttu-id="0a217-384">Avant EF Core 3.0, les [types de requêtes](xref:core/modeling/keyless-entity-types) étaient un moyen d’interroger des données qui ne définissait pas de clé primaire de façon structurée.</span><span class="sxs-lookup"><span data-stu-id="0a217-384">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="0a217-385">Autrement dit, on utilisait un type de requête pour mapper des types d’entités sans clés (le plus souvent à partir d’une vue, mais aussi éventuellement à partir d’une table), alors qu’on utilisait un type d’entité normal quand une clé était disponible (le plus souvent à partir d’une table, mais aussi éventuellement à partir d’une vue).</span><span class="sxs-lookup"><span data-stu-id="0a217-385">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-386">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-386">New behavior</span></span>

<span data-ttu-id="0a217-387">Un type de requête devient maintenant simplement un type d’entité sans clé primaire.</span><span class="sxs-lookup"><span data-stu-id="0a217-387">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="0a217-388">Les types d’entités sans clé ont les mêmes fonctionnalités que les types de requêtes dans les versions précédentes.</span><span class="sxs-lookup"><span data-stu-id="0a217-388">Keyless entity types have the same functionality as query types in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-389">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-389">Why</span></span>

<span data-ttu-id="0a217-390">Ce changement a été apporté afin de réduire la confusion concernant l’objectif des types de requêtes.</span><span class="sxs-lookup"><span data-stu-id="0a217-390">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="0a217-391">Plus précisément, il s’agit de types d’entités sans clé et sont intrinsèquement en lecture seule pour cette raison, mais vous ne devez pas les utiliser au seul motif qu’un type d’entité doit être en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="0a217-391">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="0a217-392">De même, ils sont souvent mappés à des vues, mais c’est uniquement car les vues définissent rarement des clés.</span><span class="sxs-lookup"><span data-stu-id="0a217-392">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-393">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-393">Mitigations</span></span>

<span data-ttu-id="0a217-394">Les parties suivantes de l’API sont désormais obsolètes :</span><span class="sxs-lookup"><span data-stu-id="0a217-394">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="0a217-395">**`ModelBuilder.Query<>()`** -À la place, `ModelBuilder.Entity<>().HasNoKey()` doit être appelé pour marquer un type d’entité comme n’ayant aucune clé.</span><span class="sxs-lookup"><span data-stu-id="0a217-395">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="0a217-396">Cela ne serait toujours pas configuré par convention, afin d’éviter une configuration incorrecte quand une clé primaire est attendue mais ne correspond pas à la convention.</span><span class="sxs-lookup"><span data-stu-id="0a217-396">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="0a217-397">**`DbQuery<>`** - `DbSet<>` Doit être utilisé à la place.</span><span class="sxs-lookup"><span data-stu-id="0a217-397">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="0a217-398">**`DbContext.Query<>()`** - `DbContext.Set<>()` Doit être utilisé à la place.</span><span class="sxs-lookup"><span data-stu-id="0a217-398">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="0a217-399">**`IQueryTypeConfiguration<TQuery>`** - `IEntityTypeConfiguration<TEntity>` Doit être utilisé à la place.</span><span class="sxs-lookup"><span data-stu-id="0a217-399">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>` should be used.</span></span>

> [!NOTE]
> <span data-ttu-id="0a217-400">En raison d' [un problème dans 3. x](https://github.com/dotnet/efcore/issues/19537) lors de l’interrogation de keymoins les entités dont toutes les propriétés ont la valeur est `null` `null` retournée à la place d’une entité, si ce problème est applicable à votre scénario, ajoutez également une logique pour gérer les `null` résultats.</span><span class="sxs-lookup"><span data-stu-id="0a217-400">Due to [an issue in 3.x](https://github.com/dotnet/efcore/issues/19537) when querying keyless entities that have all properties set to `null` a `null` will be returned instead of an entity, if this issue is applicable to your scenario also add logic to handle `null` in results.</span></span>

<a name="config"></a>

### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="0a217-401">L’API de configuration pour les relations de type détenu a changé</span><span class="sxs-lookup"><span data-stu-id="0a217-401">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="0a217-402">#12444 du problème de [suivi](https://github.com/dotnet/efcore/issues/12444) 
 #9148 du problème de [suivi](https://github.com/dotnet/efcore/issues/9148) 
 [#14153 du problème de suivi](https://github.com/dotnet/efcore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="0a217-402">[Tracking Issue #12444](https://github.com/dotnet/efcore/issues/12444)
[Tracking Issue #9148](https://github.com/dotnet/efcore/issues/9148)
[Tracking Issue #14153](https://github.com/dotnet/efcore/issues/14153)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-403">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-403">Old behavior</span></span>

<span data-ttu-id="0a217-404">Avant EF Core 3.0, la configuration de la relation détenue était effectuée directement après l’appel à `OwnsOne` ou `OwnsMany`.</span><span class="sxs-lookup"><span data-stu-id="0a217-404">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-405">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-405">New behavior</span></span>

<span data-ttu-id="0a217-406">À compter d’EF Core 3.0, il existe une API Fluent afin de configurer une propriété de navigation pour le propriétaire à l’aide de `WithOwner()`.</span><span class="sxs-lookup"><span data-stu-id="0a217-406">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="0a217-407">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-407">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="0a217-408">La configuration liée à la relation entre le propriétaire et le détenu doit maintenant être chaînée après `WithOwner()` tout comme les autres relations.</span><span class="sxs-lookup"><span data-stu-id="0a217-408">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="0a217-409">En revanche, la configuration du type détenu lui-même serait toujours chaînée après `OwnsOne()/OwnsMany()`.</span><span class="sxs-lookup"><span data-stu-id="0a217-409">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="0a217-410">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-410">For example:</span></span>

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

<span data-ttu-id="0a217-411">De plus, l’appel à `Entity()`, `HasOne()` ou `Set()` avec un type détenu comme cible lève désormais une exception.</span><span class="sxs-lookup"><span data-stu-id="0a217-411">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-412">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-412">Why</span></span>

<span data-ttu-id="0a217-413">Ce changement a été apporté afin de créer une distinction plus claire entre la configuration du type détenu lui-même et la _relation au type détenu_.</span><span class="sxs-lookup"><span data-stu-id="0a217-413">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="0a217-414">Cela lève ainsi toute ambiguïté et toute confusion autour des méthodes telles que `HasForeignKey`.</span><span class="sxs-lookup"><span data-stu-id="0a217-414">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-415">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-415">Mitigations</span></span>

<span data-ttu-id="0a217-416">Changez la configuration des relations de type détenu de façon à utiliser la nouvelle surface d’API, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="0a217-416">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="0a217-417">Les entités dépendantes qui partagent la table avec le principal sont maintenant facultatives</span><span class="sxs-lookup"><span data-stu-id="0a217-417">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="0a217-418">Suivi du problème n<c0>o </c0>9005</span><span class="sxs-lookup"><span data-stu-id="0a217-418">Tracking Issue #9005</span></span>](https://github.com/dotnet/efcore/issues/9005)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-419">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-419">Old behavior</span></span>

<span data-ttu-id="0a217-420">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="0a217-420">Consider the following model:</span></span>

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

<span data-ttu-id="0a217-421">Avant EF Core 3.0, si `OrderDetails` est détenu par `Order` ou explicitement mappé à la même table, une instance `OrderDetails` est toujours nécessaire pour ajouter un nouveau `Order`.</span><span class="sxs-lookup"><span data-stu-id="0a217-421">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-422">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-422">New behavior</span></span>

<span data-ttu-id="0a217-423">À partir de la version 3.0, EF Core permet d’ajouter un `Order` sans `OrderDetails` et mappe toutes les propriétés de `OrderDetails` à l’exception de la clé primaire aux colonnes de type nullable.</span><span class="sxs-lookup"><span data-stu-id="0a217-423">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="0a217-424">Pendant l’interrogation, EF Core définit `OrderDetails` sur `null` si une de ses propriétés obligatoires n’a pas de valeur, ou s’il n’a pas de propriété obligatoire en plus de la clé primaire et que toutes les propriétés sont `null`.</span><span class="sxs-lookup"><span data-stu-id="0a217-424">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-425">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-425">Mitigations</span></span>

<span data-ttu-id="0a217-426">Si votre modèle a une table qui partage des dépendances avec toutes les colonnes facultatives, mais que la navigation qui pointe vers lui n’est pas censée être `null`, l’application doit être modifiée pour gérer les situations où la navigation est `null`.</span><span class="sxs-lookup"><span data-stu-id="0a217-426">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="0a217-427">Si ce n’est pas possible, une propriété obligatoire doit être ajoutée au type d’entité ou au moins une propriété doit avoir une valeur non-`null`.</span><span class="sxs-lookup"><span data-stu-id="0a217-427">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="0a217-428">Toutes les entités qui partagent une table avec une colonne de jeton de concurrence doivent la mapper à une propriété</span><span class="sxs-lookup"><span data-stu-id="0a217-428">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="0a217-429">Suivi du problème n<c0>o </c0>14154</span><span class="sxs-lookup"><span data-stu-id="0a217-429">Tracking Issue #14154</span></span>](https://github.com/dotnet/efcore/issues/14154)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-430">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-430">Old behavior</span></span>

<span data-ttu-id="0a217-431">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="0a217-431">Consider the following model:</span></span>

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

<span data-ttu-id="0a217-432">Avant EF Core 3.0, si `OrderDetails` est détenu par `Order` ou explicitement mappé à la même table, la mise à jour de `OrderDetails` seulement ne met pas à jour la valeur de `Version` sur le client et la prochaine mise à jour échoue.</span><span class="sxs-lookup"><span data-stu-id="0a217-432">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-433">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-433">New behavior</span></span>

<span data-ttu-id="0a217-434">À compter de la version 3.0, EF Core propage la nouvelle valeur `Version` sur `Order` s’il est propriétaire de `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="0a217-434">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="0a217-435">Sinon, une exception est levée pendant la validation de modèle.</span><span class="sxs-lookup"><span data-stu-id="0a217-435">Otherwise an exception is thrown during model validation.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-436">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-436">Why</span></span>

<span data-ttu-id="0a217-437">Ce changement a été effectué pour éviter la péremption de la valeur du jeton de concurrence quand une seule des entités mappées à la même table est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="0a217-437">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-438">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-438">Mitigations</span></span>

<span data-ttu-id="0a217-439">Toutes les entités partageant la table doivent inclure une propriété mappée à la colonne de jeton de concurrence.</span><span class="sxs-lookup"><span data-stu-id="0a217-439">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="0a217-440">Vous pouvez en créer une dans un état fantôme :</span><span class="sxs-lookup"><span data-stu-id="0a217-440">It's possible the create one in shadow-state:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="0a217-441">Les entités détenues ne peuvent pas être interrogées sans le propriétaire à l’aide d’une requête de suivi</span><span class="sxs-lookup"><span data-stu-id="0a217-441">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="0a217-442">#18876 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="0a217-442">Tracking Issue #18876</span></span>](https://github.com/dotnet/efcore/issues/18876)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-443">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-443">Old behavior</span></span>

<span data-ttu-id="0a217-444">Avant le EF Core 3,0, les entités appartenant peuvent être interrogées comme n’importe quelle autre navigation.</span><span class="sxs-lookup"><span data-stu-id="0a217-444">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

#### <a name="new-behavior"></a><span data-ttu-id="0a217-445">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-445">New behavior</span></span>

<span data-ttu-id="0a217-446">À partir de 3,0, EF Core lève une exception si une requête de suivi projette une entité détenue sans le propriétaire.</span><span class="sxs-lookup"><span data-stu-id="0a217-446">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-447">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-447">Why</span></span>

<span data-ttu-id="0a217-448">Les entités détenues ne peuvent pas être manipulées sans le propriétaire, donc dans la grande majorité des cas, l’interrogation de cette façon est une erreur.</span><span class="sxs-lookup"><span data-stu-id="0a217-448">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-449">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-449">Mitigations</span></span>

<span data-ttu-id="0a217-450">Si l’entité possédée doit être suivie pour être modifiée de quelque façon que ce soit ultérieurement, le propriétaire doit être inclus dans la requête.</span><span class="sxs-lookup"><span data-stu-id="0a217-450">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="0a217-451">Sinon, ajoutez un `AsNoTracking()` appel :</span><span class="sxs-lookup"><span data-stu-id="0a217-451">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="0a217-452">Les propriétés héritées de types non mappés sont maintenant mappées à une seule colonne pour tous les types dérivés</span><span class="sxs-lookup"><span data-stu-id="0a217-452">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="0a217-453">Suivi du problème n<c0>o </c0>13998</span><span class="sxs-lookup"><span data-stu-id="0a217-453">Tracking Issue #13998</span></span>](https://github.com/dotnet/efcore/issues/13998)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-454">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-454">Old behavior</span></span>

<span data-ttu-id="0a217-455">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="0a217-455">Consider the following model:</span></span>

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

<span data-ttu-id="0a217-456">Avant EF Core 3.0, la propriété `ShippingAddress` est mappée à des colonnes distinctes pour `BulkOrder` et `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="0a217-456">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-457">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-457">New behavior</span></span>

<span data-ttu-id="0a217-458">À compter de la version 3.0, EF Core crée uniquement une colonne pour `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="0a217-458">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-459">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-459">Why</span></span>

<span data-ttu-id="0a217-460">L’ancien comportement n’était pas attendu.</span><span class="sxs-lookup"><span data-stu-id="0a217-460">The old behavoir was unexpected.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-461">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-461">Mitigations</span></span>

<span data-ttu-id="0a217-462">La propriété peut toujours être mappée explicitement à une colonne distincte sur les types dérivés :</span><span class="sxs-lookup"><span data-stu-id="0a217-462">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="0a217-463">La convention de propriété de clé étrangère ne correspond plus au nom de la propriété principale</span><span class="sxs-lookup"><span data-stu-id="0a217-463">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="0a217-464">Suivi du problème n<c0>o </c0>13274</span><span class="sxs-lookup"><span data-stu-id="0a217-464">Tracking Issue #13274</span></span>](https://github.com/dotnet/efcore/issues/13274)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-465">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-465">Old behavior</span></span>

<span data-ttu-id="0a217-466">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="0a217-466">Consider the following model:</span></span>

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

<span data-ttu-id="0a217-467">Avant EF Core 3.0, la propriété `CustomerId` était utilisée pour la clé étrangère par convention.</span><span class="sxs-lookup"><span data-stu-id="0a217-467">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="0a217-468">Toutefois, si `Order` est un type détenu, cela fait également de `CustomerId` la clé primaire, ce qui ne correspond généralement pas aux attentes.</span><span class="sxs-lookup"><span data-stu-id="0a217-468">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-469">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-469">New behavior</span></span>

<span data-ttu-id="0a217-470">À compter de la version 3.0, EF Core ne tente pas d’utiliser des propriétés pour les clés étrangères par convention si elles ont le même nom que la propriété principale.</span><span class="sxs-lookup"><span data-stu-id="0a217-470">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="0a217-471">Les modèles de nom du type de principal concaténé au nom de la propriété de principal, et de nom de navigation concaténé au nom de propriété de principal sont toujours mis en correspondance.</span><span class="sxs-lookup"><span data-stu-id="0a217-471">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="0a217-472">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-472">For example:</span></span>

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

#### <a name="why"></a><span data-ttu-id="0a217-473">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-473">Why</span></span>

<span data-ttu-id="0a217-474">Ce changement a été apporté afin d’éviter de définir de manière erronée une propriété de clé primaire sur le type détenu.</span><span class="sxs-lookup"><span data-stu-id="0a217-474">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-475">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-475">Mitigations</span></span>

<span data-ttu-id="0a217-476">Si la propriété était censée être la clé étrangère, et par conséquent une partie de la clé primaire, configurez-la explicitement comme telle.</span><span class="sxs-lookup"><span data-stu-id="0a217-476">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="0a217-477">La connexion de base de données est maintenant fermée si elle n’est plus utilisée avant la fin de TransactionScope</span><span class="sxs-lookup"><span data-stu-id="0a217-477">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="0a217-478">Suivi du problème n<c0>o </c0>14218</span><span class="sxs-lookup"><span data-stu-id="0a217-478">Tracking Issue #14218</span></span>](https://github.com/dotnet/efcore/issues/14218)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-479">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-479">Old behavior</span></span>

<span data-ttu-id="0a217-480">Avant EF Core 3.0, si le contexte ouvre la connexion à l’intérieur d’un `TransactionScope`, la connexion reste ouverte quand le `TransactionScope` actuel est actif.</span><span class="sxs-lookup"><span data-stu-id="0a217-480">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="0a217-481">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-481">New behavior</span></span>

<span data-ttu-id="0a217-482">À compter de la version 3.0, EF Core ferme la connexion dès qu’il ne l’utilise plus.</span><span class="sxs-lookup"><span data-stu-id="0a217-482">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-483">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-483">Why</span></span>

<span data-ttu-id="0a217-484">Ce changement permet d’utiliser plusieurs contextes dans le même `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="0a217-484">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="0a217-485">Le nouveau comportement correspond également à EF6.</span><span class="sxs-lookup"><span data-stu-id="0a217-485">The new behavior also matches EF6.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-486">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-486">Mitigations</span></span>

<span data-ttu-id="0a217-487">Si la connexion doit rester ouverte, un appel explicite à `OpenConnection()` garantit qu’EF Core ne la ferme pas prématurément :</span><span class="sxs-lookup"><span data-stu-id="0a217-487">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="0a217-488">Chaque propriété utilise la génération indépendante de clé de type entier en mémoire</span><span class="sxs-lookup"><span data-stu-id="0a217-488">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="0a217-489">Suivi du problème n<c0>o </c0>6872</span><span class="sxs-lookup"><span data-stu-id="0a217-489">Tracking Issue #6872</span></span>](https://github.com/dotnet/efcore/issues/6872)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-490">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-490">Old behavior</span></span>

<span data-ttu-id="0a217-491">Avant EF Core 3.0, un seul générateur de valeurs partagées était utilisé pour toutes les propriétés de clé de type entier en mémoire.</span><span class="sxs-lookup"><span data-stu-id="0a217-491">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-492">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-492">New behavior</span></span>

<span data-ttu-id="0a217-493">À compter d’EF Core 3.0, chaque propriété de clé de type entier obtient son propre générateur de valeur lors de l’utilisation de la base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="0a217-493">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="0a217-494">De plus, si la base de données est supprimée, la génération de clé est réinitialisée pour toutes les tables.</span><span class="sxs-lookup"><span data-stu-id="0a217-494">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-495">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-495">Why</span></span>

<span data-ttu-id="0a217-496">Ce changement a été apporté afin d’aligner plus étroitement la génération de clé en mémoire à la génération de clé de base de données réelle, et afin d’améliorer la capacité à isoler les tests les uns des autres lors de l’utilisation de la base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="0a217-496">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-497">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-497">Mitigations</span></span>

<span data-ttu-id="0a217-498">Ceci peut casser une application qui repose sur la définition de valeurs de clé en mémoire spécifiques.</span><span class="sxs-lookup"><span data-stu-id="0a217-498">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="0a217-499">Au lieu de cela, ne vous appuyez pas sur des valeurs de clés spécifiques, ou procédez à une mise à jour pour vous aligner au nouveau comportement.</span><span class="sxs-lookup"><span data-stu-id="0a217-499">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="0a217-500">Les champs de stockage sont utilisés par défaut</span><span class="sxs-lookup"><span data-stu-id="0a217-500">Backing fields are used by default</span></span>

[<span data-ttu-id="0a217-501">Suivi du problème n<c0>o </c0>12430</span><span class="sxs-lookup"><span data-stu-id="0a217-501">Tracking Issue #12430</span></span>](https://github.com/dotnet/efcore/issues/12430)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-502">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-502">Old behavior</span></span>

<span data-ttu-id="0a217-503">Avant la version 3.0, même si le champ de stockage d’une propriété était connu, par défaut EF Core lisait et écrivait toujours la valeur de propriété à l’aide des méthodes get et set de la propriété.</span><span class="sxs-lookup"><span data-stu-id="0a217-503">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="0a217-504">L’exception à cette règle concernait l’exécution des requêtes, où le champ de stockage était défini directement s’il était connu.</span><span class="sxs-lookup"><span data-stu-id="0a217-504">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-505">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-505">New behavior</span></span>

<span data-ttu-id="0a217-506">Depuis EF Core 3.0, si le champ de stockage d’une propriété est connu, alors EF Core lit et écrit toujours cette propriété à l’aide du champ de stockage.</span><span class="sxs-lookup"><span data-stu-id="0a217-506">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="0a217-507">Cela peut casser une application si celle-ci repose sur un comportement supplémentaire codé dans les méthodes get ou set.</span><span class="sxs-lookup"><span data-stu-id="0a217-507">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-508">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-508">Why</span></span>

<span data-ttu-id="0a217-509">Ce changement a été apporté afin d’empêcher EF Core de déclencher par erreur une logique métier par défaut quand vous effectuez des opérations de base de données impliquant les entités.</span><span class="sxs-lookup"><span data-stu-id="0a217-509">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-510">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-510">Mitigations</span></span>

<span data-ttu-id="0a217-511">Vous pouvez restaurer le comportement antérieur à la version 3.0 en configurant le mode d’accès à la propriété sur `ModelBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0a217-511">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="0a217-512">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-512">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="0a217-513">Erreur si plusieurs champs de stockage compatibles sont détectés</span><span class="sxs-lookup"><span data-stu-id="0a217-513">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="0a217-514">Suivi du problème n<c0>o </c0>12523</span><span class="sxs-lookup"><span data-stu-id="0a217-514">Tracking Issue #12523</span></span>](https://github.com/dotnet/efcore/issues/12523)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-515">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-515">Old behavior</span></span>

<span data-ttu-id="0a217-516">Avant EF Core 3.0, si plusieurs champs respectaient les règles de recherche du champ de stockage d’une propriété, l’un d’entre eux était choisi selon un ordre de priorité.</span><span class="sxs-lookup"><span data-stu-id="0a217-516">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="0a217-517">Cela pouvait entraîner l’utilisation d’un champ incorrect en cas d’ambiguïté.</span><span class="sxs-lookup"><span data-stu-id="0a217-517">This could cause the wrong field to be used in ambiguous cases.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-518">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-518">New behavior</span></span>

<span data-ttu-id="0a217-519">À compter d’EF Core 3.0, si plusieurs champs sont mis en correspondance avec la même propriété, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="0a217-519">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-520">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-520">Why</span></span>

<span data-ttu-id="0a217-521">Ce changement a été apporté afin d’éviter d’utiliser en mode silencieux un champ plutôt qu’un autre quand un seul peut être correct.</span><span class="sxs-lookup"><span data-stu-id="0a217-521">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-522">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-522">Mitigations</span></span>

<span data-ttu-id="0a217-523">Pour les propriétés comportant des champs de stockage ambigus, le champ à utiliser doit être spécifié explicitement.</span><span class="sxs-lookup"><span data-stu-id="0a217-523">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="0a217-524">Par exemple, à l’aide de l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="0a217-524">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="0a217-525">Les noms de propriété de type « champ uniquement » doivent correspondre au nom du champ</span><span class="sxs-lookup"><span data-stu-id="0a217-525">Field-only property names should match the field name</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-526">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-526">Old behavior</span></span>

<span data-ttu-id="0a217-527">Avant le EF Core 3,0, une propriété pourrait être spécifiée par une valeur de chaîne et si aucune propriété portant ce nom n’a été trouvée sur le type .NET, EF Core essaiera de la faire correspondre à un champ à l’aide de règles de Convention.</span><span class="sxs-lookup"><span data-stu-id="0a217-527">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="0a217-528">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-528">New behavior</span></span>

<span data-ttu-id="0a217-529">À compter d’EF Core 3.0, une propriété de type « champ uniquement » doit correspondre exactement au nom du champ.</span><span class="sxs-lookup"><span data-stu-id="0a217-529">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

#### <a name="why"></a><span data-ttu-id="0a217-530">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-530">Why</span></span>

<span data-ttu-id="0a217-531">Ce changement a été apporté pour éviter d’utiliser un même champ pour deux propriétés portant un nom similaire. De plus, les règles de correspondance des propriétés de type « champ uniquement » sont désormais les mêmes que pour les propriétés mappées aux propriétés CLR.</span><span class="sxs-lookup"><span data-stu-id="0a217-531">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-532">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-532">Mitigations</span></span>

<span data-ttu-id="0a217-533">Les propriétés de type « champ uniquement » doivent porter le même nom que le champ auquel elles sont mappées.</span><span class="sxs-lookup"><span data-stu-id="0a217-533">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="0a217-534">Dans une version ultérieure de EF Core après 3,0, nous prévoyons de réactiver explicitement la configuration d’un nom de champ différent du nom de la propriété (voir le problème [#15307](https://github.com/dotnet/efcore/issues/15307)) :</span><span class="sxs-lookup"><span data-stu-id="0a217-534">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/dotnet/efcore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="0a217-535">AddDbContext/AddDbContextPool n’appelle plus AddLogging et AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="0a217-535">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="0a217-536">Suivi du problème n<c0>o </c0>14756</span><span class="sxs-lookup"><span data-stu-id="0a217-536">Tracking Issue #14756</span></span>](https://github.com/dotnet/efcore/issues/14756)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-537">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-537">Old behavior</span></span>

<span data-ttu-id="0a217-538">Avant le EF Core 3,0, l’appel de `AddDbContext` ou `AddDbContextPool` enregistrerait également des services de journalisation et de mise en cache de mémoire avec des appels à [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) et [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="0a217-538">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-539">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-539">New behavior</span></span>

<span data-ttu-id="0a217-540">À compter d’EF Core 3.0, `AddDbContext` et `AddDbContextPool` n’inscriront plus ces services auprès de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="0a217-540">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-541">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-541">Why</span></span>

<span data-ttu-id="0a217-542">Il n’est pas nécessaire pour EF Core 3.0 que ces services se trouvent dans le conteneur d’injection de dépendances de l’application.</span><span class="sxs-lookup"><span data-stu-id="0a217-542">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="0a217-543">Toutefois, `ILoggerFactory` est utilisé par EF Core même s’il est inscrit dans ce conteneur.</span><span class="sxs-lookup"><span data-stu-id="0a217-543">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-544">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-544">Mitigations</span></span>

<span data-ttu-id="0a217-545">Si votre application a besoin de ces services, inscrivez-les explicitement auprès du conteneur d’injection de dépendances avec [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) ou [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="0a217-545">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="0a217-546">AddEntityFramework \* ajoute IMemoryCache avec une limite de taille</span><span class="sxs-lookup"><span data-stu-id="0a217-546">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="0a217-547">#12905 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="0a217-547">Tracking Issue #12905</span></span>](https://github.com/dotnet/efcore/issues/12905)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-548">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-548">Old behavior</span></span>

<span data-ttu-id="0a217-549">Avant le EF Core 3,0, `AddEntityFramework*` les méthodes d’appel inscrivent également les services de mise en cache en mémoire avec l’injection de transaction sans limite de taille.</span><span class="sxs-lookup"><span data-stu-id="0a217-549">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-550">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-550">New behavior</span></span>

<span data-ttu-id="0a217-551">À compter de EF Core 3,0, `AddEntityFramework*` inscrira un service IMemoryCache avec une limite de taille.</span><span class="sxs-lookup"><span data-stu-id="0a217-551">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="0a217-552">Si d’autres services ajoutés par la suite dépendent de IMemoryCache, ils peuvent rapidement atteindre la limite par défaut provoquant des exceptions ou une dégradation des performances.</span><span class="sxs-lookup"><span data-stu-id="0a217-552">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-553">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-553">Why</span></span>

<span data-ttu-id="0a217-554">L’utilisation de IMemoryCache sans limite peut entraîner une utilisation incontrôlée de la mémoire s’il existe un bogue dans la logique de mise en cache des requêtes ou si les requêtes sont générées dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="0a217-554">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="0a217-555">Le fait d’avoir une limite par défaut atténue une attaque potentielle par déni de compte.</span><span class="sxs-lookup"><span data-stu-id="0a217-555">Having a default limit mitigates a potential DoS attack.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-556">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-556">Mitigations</span></span>

<span data-ttu-id="0a217-557">Dans la plupart des cas, l’appel de `AddEntityFramework*` n’est pas nécessaire si `AddDbContext` ou `AddDbContextPool` est également appelé.</span><span class="sxs-lookup"><span data-stu-id="0a217-557">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="0a217-558">Par conséquent, la meilleure atténuation consiste à supprimer l' `AddEntityFramework*` appel.</span><span class="sxs-lookup"><span data-stu-id="0a217-558">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="0a217-559">Si votre application a besoin de ces services, inscrivez une implémentation IMemoryCache explicitement avec le conteneur DI au préalable à l’aide de [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="0a217-559">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="0a217-560">DbContext.Entry effectue maintenant un DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="0a217-560">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="0a217-561">Suivi du problème n<c0>o </c0>13552</span><span class="sxs-lookup"><span data-stu-id="0a217-561">Tracking Issue #13552</span></span>](https://github.com/dotnet/efcore/issues/13552)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-562">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-562">Old behavior</span></span>

<span data-ttu-id="0a217-563">Avant EF Core 3.0, le fait d’appeler `DbContext.Entry` provoquait la détection des changements pour toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="0a217-563">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="0a217-564">Cela garantissait que l’état exposé dans `EntityEntry` était à jour.</span><span class="sxs-lookup"><span data-stu-id="0a217-564">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-565">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-565">New behavior</span></span>

<span data-ttu-id="0a217-566">À compter d’EF Core 3.0, l’appel à `DbContext.Entry` tente uniquement de détecter les changements apportés dans l’entité donnée et dans toute entité principale suivie qui lui est associée.</span><span class="sxs-lookup"><span data-stu-id="0a217-566">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="0a217-567">Cela signifie que les changements apportés ailleurs peuvent ne pas avoir été détectés par l’appel à cette méthode, ce qui pourrait avoir des conséquences sur l’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="0a217-567">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="0a217-568">Notez que si `ChangeTracker.AutoDetectChangesEnabled` a la valeur `false`, même cette détection de changement locale sera désactivée.</span><span class="sxs-lookup"><span data-stu-id="0a217-568">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="0a217-569">Les autres méthodes qui provoquent une détection des changements (par exemple `ChangeTracker.Entries` et `SaveChanges`) entraînent toujours un `DetectChanges` complet de toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="0a217-569">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-570">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-570">Why</span></span>

<span data-ttu-id="0a217-571">Ce changement a été apporté afin d’améliorer les performances par défaut de `context.Entry`.</span><span class="sxs-lookup"><span data-stu-id="0a217-571">This change was made to improve the default performance of using `context.Entry`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-572">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-572">Mitigations</span></span>

<span data-ttu-id="0a217-573">Appelez `ChangeTracker.DetectChanges()` explicitement avant d’appeler `Entry` pour garantir le comportement antérieur à la version 3.0.</span><span class="sxs-lookup"><span data-stu-id="0a217-573">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="0a217-574">Les clés de tableaux d’octets et de chaînes ne sont pas générés par client par défaut</span><span class="sxs-lookup"><span data-stu-id="0a217-574">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="0a217-575">Suivi du problème n<c0>o </c0>14617</span><span class="sxs-lookup"><span data-stu-id="0a217-575">Tracking Issue #14617</span></span>](https://github.com/dotnet/efcore/issues/14617)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-576">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-576">Old behavior</span></span>

<span data-ttu-id="0a217-577">Avant EF Core 3.0, les propriétés de clés `string` et `byte[]` pouvaient être utilisées sans définir explicitement de valeur non null.</span><span class="sxs-lookup"><span data-stu-id="0a217-577">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="0a217-578">Dans ce cas, la valeur de la clé était générée sur le client en tant que GUID, sérialisé en octets pour `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="0a217-578">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-579">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-579">New behavior</span></span>

<span data-ttu-id="0a217-580">À compter d’EF Core 3.0, une exception est levée pour signaler qu’aucune valeur de clé n’a été définie.</span><span class="sxs-lookup"><span data-stu-id="0a217-580">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-581">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-581">Why</span></span>

<span data-ttu-id="0a217-582">Ce changement a été apporté car les valeurs `string`/`byte[]` générées par le client ne sont généralement pas utiles, et le comportement par défaut rendait les valeurs de clés générés de manière courante difficiles à expliquer.</span><span class="sxs-lookup"><span data-stu-id="0a217-582">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-583">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-583">Mitigations</span></span>

<span data-ttu-id="0a217-584">Vos pouvez obtenir le comportement antérieur à la version 3.0 en spécifiant explicitement que les propriétés de clés doivent utiliser des valeurs générées si aucune autre valeur non nulle n’est définie.</span><span class="sxs-lookup"><span data-stu-id="0a217-584">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="0a217-585">Par exemple, avec l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="0a217-585">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="0a217-586">Ou avec des annotations de données :</span><span class="sxs-lookup"><span data-stu-id="0a217-586">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="0a217-587">ILoggerFactory est désormais un service délimité</span><span class="sxs-lookup"><span data-stu-id="0a217-587">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="0a217-588">Suivi du problème n<c0>o </c0>14698</span><span class="sxs-lookup"><span data-stu-id="0a217-588">Tracking Issue #14698</span></span>](https://github.com/dotnet/efcore/issues/14698)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-589">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-589">Old behavior</span></span>

<span data-ttu-id="0a217-590">Avant EF Core 3.0, `ILoggerFactory` était inscrit en tant que service singleton.</span><span class="sxs-lookup"><span data-stu-id="0a217-590">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-591">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-591">New behavior</span></span>

<span data-ttu-id="0a217-592">À compter d’EF Core 3.0, `ILoggerFactory` est inscrit en tant que service délimité.</span><span class="sxs-lookup"><span data-stu-id="0a217-592">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-593">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-593">Why</span></span>

<span data-ttu-id="0a217-594">Ce changement a été apporté afin d’autoriser l’association d’un journaliseur avec une instance `DbContext`, ce qui active d’autres fonctionnalités et élimine certains cas de comportement pathologique tels que l’explosion des fournisseurs de services internes.</span><span class="sxs-lookup"><span data-stu-id="0a217-594">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-595">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-595">Mitigations</span></span>

<span data-ttu-id="0a217-596">Ce changement ne devrait pas avoir d’impact sur le code de l’application, sauf en cas d’inscription et d’utilisation de services personnalisés sur le fournisseur de service interne EF Core,</span><span class="sxs-lookup"><span data-stu-id="0a217-596">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="0a217-597">Ce n’est pas courant.</span><span class="sxs-lookup"><span data-stu-id="0a217-597">This isn't common.</span></span>
<span data-ttu-id="0a217-598">Dans ces cas-là, la plupart des composants continueront à fonctionner, mais tout service singleton qui dépendait de `ILoggerFactory` devra être modifié pour obtenir le `ILoggerFactory` d’une manière différente.</span><span class="sxs-lookup"><span data-stu-id="0a217-598">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="0a217-599">Si vous faites face à ce genre de situation, veuillez soumettre un problème par le biais du [suivi des problèmes GitHub EF Core](https://github.com/dotnet/efcore/issues) pour nous dire comment vous utilisez `ILoggerFactory`, afin que nous puissions mieux comprendre comment ne rien casser à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="0a217-599">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/dotnet/efcore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="0a217-600">Les proxys à chargement différé ne partent plus du principe que les propriétés de navigation sont entièrement chargées</span><span class="sxs-lookup"><span data-stu-id="0a217-600">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="0a217-601">Suivi du problème n<c0>o </c0>12780</span><span class="sxs-lookup"><span data-stu-id="0a217-601">Tracking Issue #12780</span></span>](https://github.com/dotnet/efcore/issues/12780)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-602">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-602">Old behavior</span></span>

<span data-ttu-id="0a217-603">Avant EF Core 3.0, une fois qu’un `DbContext` était supprimé, il n’existait aucun moyen de savoir si une propriété de navigation donnée sur une entité obtenue à partir de ce contexte était entièrement chargée.</span><span class="sxs-lookup"><span data-stu-id="0a217-603">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="0a217-604">Les serveurs proxy partaient du principe qu’une navigation de référence était chargée si elle avait une valeur non null, et qu’une navigation de collection était chargée si elle n’était pas vide.</span><span class="sxs-lookup"><span data-stu-id="0a217-604">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="0a217-605">Dans ces cas-là, toute tentative de chargement différé constituait une no-op.</span><span class="sxs-lookup"><span data-stu-id="0a217-605">In these cases, attempting to lazy-load would be a no-op.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-606">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-606">New behavior</span></span>

<span data-ttu-id="0a217-607">À compter d’EF Core 3.0, les proxys effectuent le suivi du chargement d’une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="0a217-607">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="0a217-608">Cela signifie qu’une tentative d’accès à une propriété de navigation qui est chargée une fois que le contexte a été supprimé sera toujours une no-op, même quand la navigation chargée est vide ou null.</span><span class="sxs-lookup"><span data-stu-id="0a217-608">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="0a217-609">À l’inverse, une tentative d’accès à une propriété de navigation qui n’est pas chargée lèvera une exception si le contexte est supprimé, même si la propriété de navigation est une collection non vide.</span><span class="sxs-lookup"><span data-stu-id="0a217-609">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="0a217-610">Si cette situation se présente, cela signifie que le code d’application tente d’utiliser le chargement différé à un moment non valide, et que l’application doit être modifiée afin de ne pas tenter cette opération.</span><span class="sxs-lookup"><span data-stu-id="0a217-610">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-611">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-611">Why</span></span>

<span data-ttu-id="0a217-612">Ce changement a été apporté afin de rendre le comportement cohérent et correct lors de la tentative de chargement différé sur une instance `DbContext` supprimée.</span><span class="sxs-lookup"><span data-stu-id="0a217-612">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-613">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-613">Mitigations</span></span>

<span data-ttu-id="0a217-614">Mettez à jour le code d’application pour qu’il ne tente pas d’effectuer un chargement différé avec un contexte supprimé, ou configurez cette opération pour qu’il s’agisse d’une no-op comme décrit dans le message d’exception.</span><span class="sxs-lookup"><span data-stu-id="0a217-614">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="0a217-615">La création excessive de fournisseurs de services internes est maintenant une erreur par défaut</span><span class="sxs-lookup"><span data-stu-id="0a217-615">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="0a217-616">Suivi du problème n<c0>o </c0>10236</span><span class="sxs-lookup"><span data-stu-id="0a217-616">Tracking Issue #10236</span></span>](https://github.com/dotnet/efcore/issues/10236)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-617">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-617">Old behavior</span></span>

<span data-ttu-id="0a217-618">Avant EF Core 3.0, un avertissement était enregistré dans le journal quand une application créait une quantité pathologique de fournisseurs de services internes.</span><span class="sxs-lookup"><span data-stu-id="0a217-618">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-619">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-619">New behavior</span></span>

<span data-ttu-id="0a217-620">À compter d’EF Core 3.0, cet avertissement est désormais considéré comme une erreur, et une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="0a217-620">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-621">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-621">Why</span></span>

<span data-ttu-id="0a217-622">Ce changement a été apporté afin d’améliorer le code d’application avec une exposition plus explicite de ce cas pathologique.</span><span class="sxs-lookup"><span data-stu-id="0a217-622">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-623">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-623">Mitigations</span></span>

<span data-ttu-id="0a217-624">En présence de cette erreur, le mieux consiste à tenter de comprendre la cause racine, et de cesser de créer autant de fournisseurs de services internes.</span><span class="sxs-lookup"><span data-stu-id="0a217-624">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="0a217-625">Toutefois, vous pouvez reconvertir cette erreur en avertissement (ou l’ignorer) par le biais de la configuration sur le `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="0a217-625">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="0a217-626">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-626">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="0a217-627">Nouveau comportement de HasOne/HasMany appelé avec une chaîne unique</span><span class="sxs-lookup"><span data-stu-id="0a217-627">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="0a217-628">Suivi du problème n<c0>o </c0>9171</span><span class="sxs-lookup"><span data-stu-id="0a217-628">Tracking Issue #9171</span></span>](https://github.com/dotnet/efcore/issues/9171)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-629">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-629">Old behavior</span></span>

<span data-ttu-id="0a217-630">Avant EF Core 3.0, la façon dont était interprété le code qui appelait `HasOne` ou `HasMany` avec une seule chaîne prêtait à confusion.</span><span class="sxs-lookup"><span data-stu-id="0a217-630">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="0a217-631">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-631">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="0a217-632">Le code semble relier `Samurai` à un autre type d’entité avec la propriété de navigation `Entrance`, qui peut être privée.</span><span class="sxs-lookup"><span data-stu-id="0a217-632">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="0a217-633">En réalité, ce code tente de créer une relation avec un certain type d’entité nommé `Entrance` sans propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="0a217-633">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-634">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-634">New behavior</span></span>

<span data-ttu-id="0a217-635">À compter d’EF Core 3.0, le code ci-dessus effectue maintenant ce qu’il semblait devoir faire avant.</span><span class="sxs-lookup"><span data-stu-id="0a217-635">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-636">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-636">Why</span></span>

<span data-ttu-id="0a217-637">L’ancien comportement était très déroutant, en particulier pour qui lisait le code de configuration à la recherche d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="0a217-637">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-638">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-638">Mitigations</span></span>

<span data-ttu-id="0a217-639">Seules les applications qui configurent explicitement des relations en utilisant des chaînes comme noms de type sans spécifier explicitement la propriété de navigation sont concernées,</span><span class="sxs-lookup"><span data-stu-id="0a217-639">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="0a217-640">ce qui n’est pas courant.</span><span class="sxs-lookup"><span data-stu-id="0a217-640">This is not common.</span></span>
<span data-ttu-id="0a217-641">Pour revenir au comportement précédent, transmettez explicitement `null` comme nom de propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="0a217-641">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="0a217-642">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-642">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="0a217-643">Le type de retour Task pour plusieurs méthodes asynchrones a été remplacé par ValueTask</span><span class="sxs-lookup"><span data-stu-id="0a217-643">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="0a217-644">Suivi du problème n<c0>o </c0>15184</span><span class="sxs-lookup"><span data-stu-id="0a217-644">Tracking Issue #15184</span></span>](https://github.com/dotnet/efcore/issues/15184)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-645">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-645">Old behavior</span></span>

<span data-ttu-id="0a217-646">Les méthodes asynchrones suivantes retournaient précédemment un `Task<T>` :</span><span class="sxs-lookup"><span data-stu-id="0a217-646">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="0a217-647">`ValueGenerator.NextValueAsync()` (et classes dérivées)</span><span class="sxs-lookup"><span data-stu-id="0a217-647">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-648">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-648">New behavior</span></span>

<span data-ttu-id="0a217-649">Les méthodes mentionnées précédemment retournent maintenant un `ValueTask<T>` sur le même `T` qu’avant.</span><span class="sxs-lookup"><span data-stu-id="0a217-649">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-650">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-650">Why</span></span>

<span data-ttu-id="0a217-651">Ce changement réduit le nombre d’allocations de tas induits par l’appel de ces méthodes, ce qui améliore les performances générales.</span><span class="sxs-lookup"><span data-stu-id="0a217-651">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-652">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-652">Mitigations</span></span>

<span data-ttu-id="0a217-653">Les applications qui sont simplement en attente des API ci-dessus doivent uniquement être recompilées, vous n’avez pas besoin de changer la source.</span><span class="sxs-lookup"><span data-stu-id="0a217-653">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="0a217-654">Une utilisation plus complexe (par exemple, le passage du `Task` retourné à `Task.WhenAny()`) nécessite généralement que le `ValueTask<T>` retourné soit converti en `Task<T>` en appelant `AsTask()` sur lui.</span><span class="sxs-lookup"><span data-stu-id="0a217-654">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="0a217-655">Notez que cette opération annule la réduction d’allocation apportée par ce changement.</span><span class="sxs-lookup"><span data-stu-id="0a217-655">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="0a217-656">L’annotation Relational:TypeMapping est désormais simplement TypeMapping</span><span class="sxs-lookup"><span data-stu-id="0a217-656">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="0a217-657">Suivi du problème n<c0>o </c0>9913</span><span class="sxs-lookup"><span data-stu-id="0a217-657">Tracking Issue #9913</span></span>](https://github.com/dotnet/efcore/issues/9913)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-658">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-658">Old behavior</span></span>

<span data-ttu-id="0a217-659">Le nom d’annotation pour les annotations de mappage de types était « annotations ».</span><span class="sxs-lookup"><span data-stu-id="0a217-659">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-660">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-660">New behavior</span></span>

<span data-ttu-id="0a217-661">Le nom d’annotation pour les annotations de mappage de types est désormais « TypeMapping ».</span><span class="sxs-lookup"><span data-stu-id="0a217-661">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-662">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-662">Why</span></span>

<span data-ttu-id="0a217-663">Les mappages de types ne sont désormais plus utilisés uniquement pour les fournisseurs de bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="0a217-663">Type mappings are now used for more than just relational database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-664">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-664">Mitigations</span></span>

<span data-ttu-id="0a217-665">Ce changement casse uniquement les applications qui accèdent au mappage de types directement en tant qu’annotation, ce qui n’est pas courant.</span><span class="sxs-lookup"><span data-stu-id="0a217-665">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="0a217-666">La meilleure solution consiste à utiliser la surface d’API pour accéder aux mappages de types, plutôt que d’utiliser directement l’annotation.</span><span class="sxs-lookup"><span data-stu-id="0a217-666">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="0a217-667">ToTable sur un type dérivé lève une exception</span><span class="sxs-lookup"><span data-stu-id="0a217-667">ToTable on a derived type throws an exception</span></span>

[<span data-ttu-id="0a217-668">Suivi du problème n<c0>o </c0>11811</span><span class="sxs-lookup"><span data-stu-id="0a217-668">Tracking Issue #11811</span></span>](https://github.com/dotnet/efcore/issues/11811)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-669">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-669">Old behavior</span></span>

<span data-ttu-id="0a217-670">Avant EF Core 3.0, l’appel à `ToTable()` sur un type dérivé était ignoré, car seule la stratégie de mappage d’héritage était TPH où cela n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="0a217-670">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-671">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-671">New behavior</span></span>

<span data-ttu-id="0a217-672">À compter d’EF Core 3.0, et en préparation à l’ajout de la prise en charge de TPT et TPC dans une version ultérieure, l’appel à `ToTable()` sur un type dérivé lève maintenant une exception afin d’éviter tout changement inattendu du mappage à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="0a217-672">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-673">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-673">Why</span></span>

<span data-ttu-id="0a217-674">Actuellement le mappage d’un type dérivé à une autre table n’est pas une opération valide.</span><span class="sxs-lookup"><span data-stu-id="0a217-674">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="0a217-675">Ce changement permettra d’éviter toute cassure ultérieure quand cette opération deviendra valide.</span><span class="sxs-lookup"><span data-stu-id="0a217-675">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-676">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-676">Mitigations</span></span>

<span data-ttu-id="0a217-677">Supprimez toutes les tentatives de mappage de types dérivés à d’autres tables.</span><span class="sxs-lookup"><span data-stu-id="0a217-677">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="0a217-678">ForSqlServerHasIndex est remplacé par HasIndex</span><span class="sxs-lookup"><span data-stu-id="0a217-678">ForSqlServerHasIndex replaced with HasIndex</span></span>

[<span data-ttu-id="0a217-679">Suivi du problème n<c0>o </c0>12366</span><span class="sxs-lookup"><span data-stu-id="0a217-679">Tracking Issue #12366</span></span>](https://github.com/dotnet/efcore/issues/12366)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-680">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-680">Old behavior</span></span>

<span data-ttu-id="0a217-681">Avant EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` offrait un moyen de configurer des colonnes utilisées avec `INCLUDE`.</span><span class="sxs-lookup"><span data-stu-id="0a217-681">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-682">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-682">New behavior</span></span>

<span data-ttu-id="0a217-683">À compter d’EF Core 3.0, l’utilisation de `Include` sur un index est prise en charge au niveau relationnel.</span><span class="sxs-lookup"><span data-stu-id="0a217-683">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="0a217-684">Utiliser `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="0a217-684">Use `HasIndex().ForSqlServerInclude()`.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-685">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-685">Why</span></span>

<span data-ttu-id="0a217-686">Ce changement a été apporté afin de consolider l’API pour les index avec `Include` dans un seul emplacement pour tous les fournisseurs de bases de données.</span><span class="sxs-lookup"><span data-stu-id="0a217-686">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-687">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-687">Mitigations</span></span>

<span data-ttu-id="0a217-688">Utilisez la nouvelle API, comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="0a217-688">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="0a217-689">Changements apportés à l’API de métadonnées</span><span class="sxs-lookup"><span data-stu-id="0a217-689">Metadata API changes</span></span>

<span data-ttu-id="0a217-690">Suivi du problème no 214</span><span class="sxs-lookup"><span data-stu-id="0a217-690">[Tracking Issue #214](https://github.com/dotnet/efcore/issues/214)</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-691">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-691">New behavior</span></span>

<span data-ttu-id="0a217-692">Les propriétés suivantes ont été converties en méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="0a217-692">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

#### <a name="why"></a><span data-ttu-id="0a217-693">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-693">Why</span></span>

<span data-ttu-id="0a217-694">Ce changement simplifie l’implémentation des interfaces mentionnées précédemment.</span><span class="sxs-lookup"><span data-stu-id="0a217-694">This change simplifies the implementation of the aforementioned interfaces.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-695">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-695">Mitigations</span></span>

<span data-ttu-id="0a217-696">Utilisez les nouvelles méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="0a217-696">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="0a217-697">Modifications de l’API de métadonnées spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="0a217-697">Provider-specific Metadata API changes</span></span>

<span data-ttu-id="0a217-698">Suivi du problème no 214</span><span class="sxs-lookup"><span data-stu-id="0a217-698">[Tracking Issue #214](https://github.com/dotnet/efcore/issues/214)</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-699">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-699">New behavior</span></span>

<span data-ttu-id="0a217-700">Les méthodes d’extension spécifiques au fournisseur seront aplaties :</span><span class="sxs-lookup"><span data-stu-id="0a217-700">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

#### <a name="why"></a><span data-ttu-id="0a217-701">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-701">Why</span></span>

<span data-ttu-id="0a217-702">Ce changement simplifie l’implémentation des méthodes d’extension mentionnées précédemment.</span><span class="sxs-lookup"><span data-stu-id="0a217-702">This change simplifies the implementation of the aforementioned extension methods.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-703">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-703">Mitigations</span></span>

<span data-ttu-id="0a217-704">Utilisez les nouvelles méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="0a217-704">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="0a217-705">EF Core n’envoie plus de pragma pour l’application de clé étrangère SQLite</span><span class="sxs-lookup"><span data-stu-id="0a217-705">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="0a217-706">Suivi du problème n<c0>o </c0>12151</span><span class="sxs-lookup"><span data-stu-id="0a217-706">Tracking Issue #12151</span></span>](https://github.com/dotnet/efcore/issues/12151)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-707">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-707">Old behavior</span></span>

<span data-ttu-id="0a217-708">Avant EF Core 3.0, EF Core envoyait `PRAGMA foreign_keys = 1` quand une connexion à SQLite était ouverte.</span><span class="sxs-lookup"><span data-stu-id="0a217-708">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-709">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-709">New behavior</span></span>

<span data-ttu-id="0a217-710">À compter d’EF Core 3.0, EF Core n’envoie plus de `PRAGMA foreign_keys = 1` quand une connexion à SQLite est ouverte.</span><span class="sxs-lookup"><span data-stu-id="0a217-710">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-711">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-711">Why</span></span>

<span data-ttu-id="0a217-712">Ce changement a été apporté car EF Core utilise `SQLitePCLRaw.bundle_e_sqlite3` par défaut, ce qui signifie que l’application de clé étrangère est activée par défaut et n’a pas besoin d’être activée explicitement chaque fois qu’une connexion est ouverte.</span><span class="sxs-lookup"><span data-stu-id="0a217-712">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-713">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-713">Mitigations</span></span>

<span data-ttu-id="0a217-714">Les clés étrangères sont activées par défaut dans SQLitePCLRaw.bundle_e_sqlite3, qui est utilisé par défaut pour EF Core.</span><span class="sxs-lookup"><span data-stu-id="0a217-714">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="0a217-715">Pour les autres cas, vous pouvez activer les clés étrangères en spécifiant `Foreign Keys=True` dans votre chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="0a217-715">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="0a217-716">Microsoft.EntityFrameworkCore.Sqlite dépend désormais de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="0a217-716">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-717">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-717">Old behavior</span></span>

<span data-ttu-id="0a217-718">Avant EF Core 3.0, EF Core utilisait `SQLitePCLRaw.bundle_green`.</span><span class="sxs-lookup"><span data-stu-id="0a217-718">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-719">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-719">New behavior</span></span>

<span data-ttu-id="0a217-720">À compter d’EF Core 3.0, EF Core utilise `SQLitePCLRaw.bundle_e_sqlite3`.</span><span class="sxs-lookup"><span data-stu-id="0a217-720">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-721">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-721">Why</span></span>

<span data-ttu-id="0a217-722">Ce changement a été apporté afin que la version de SQLite utilisée sur iOS soit cohérente avec d’autres plateformes.</span><span class="sxs-lookup"><span data-stu-id="0a217-722">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-723">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-723">Mitigations</span></span>

<span data-ttu-id="0a217-724">Pour utiliser la version de SQLite native sur iOS, configurez `Microsoft.Data.Sqlite` de façon à utiliser un autre bundle `SQLitePCLRaw`.</span><span class="sxs-lookup"><span data-stu-id="0a217-724">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="0a217-725">Les valeurs GUID sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="0a217-725">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="0a217-726">Suivi du problème #15078</span><span class="sxs-lookup"><span data-stu-id="0a217-726">Tracking Issue #15078</span></span>](https://github.com/dotnet/efcore/issues/15078)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-727">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-727">Old behavior</span></span>

<span data-ttu-id="0a217-728">Avant, les valeurs GUID étaient stockées comme valeurs BLOB sur SQLite.</span><span class="sxs-lookup"><span data-stu-id="0a217-728">Guid values were previously stored as BLOB values on SQLite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-729">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-729">New behavior</span></span>

<span data-ttu-id="0a217-730">Maintenant, les valeurs Guid sont stockées au format TEXT.</span><span class="sxs-lookup"><span data-stu-id="0a217-730">Guid values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-731">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-731">Why</span></span>

<span data-ttu-id="0a217-732">Le format binaire des valeurs GUID n’est pas normalisé.</span><span class="sxs-lookup"><span data-stu-id="0a217-732">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="0a217-733">Le stockage des valeurs au format TEXT rend la base de données plus compatible avec d’autres technologies.</span><span class="sxs-lookup"><span data-stu-id="0a217-733">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-734">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-734">Mitigations</span></span>

<span data-ttu-id="0a217-735">Vous pouvez migrer des bases de données existantes vers le nouveau format en exécutant SQL comme suit.</span><span class="sxs-lookup"><span data-stu-id="0a217-735">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
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

<span data-ttu-id="0a217-736">Dans EF Core, vous pouvez également continuer à utiliser le comportement précédent en configurant un convertisseur de valeur sur ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="0a217-736">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="0a217-737">Microsoft.Data.Sqlite peut toujours lire les valeurs GUID dans les colonnes BLOB et TEXT. Toutefois, en raison du changement du format par défaut pour les paramètres et les constantes, vous devrez probablement apporter des modifications dans la plupart des scénarios utilisant des valeurs GUID.</span><span class="sxs-lookup"><span data-stu-id="0a217-737">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="0a217-738">Les valeurs char sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="0a217-738">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="0a217-739">Suivi du problème n<c0>o </c0>15020</span><span class="sxs-lookup"><span data-stu-id="0a217-739">Tracking Issue #15020</span></span>](https://github.com/dotnet/efcore/issues/15020)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-740">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-740">Old behavior</span></span>

<span data-ttu-id="0a217-741">Avant, les valeurs char étaient stockées comme valeurs INTEGER sur SQLite.</span><span class="sxs-lookup"><span data-stu-id="0a217-741">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="0a217-742">Par exemple, une valeur char de *A* était stockée comme valeur entière 65.</span><span class="sxs-lookup"><span data-stu-id="0a217-742">For example, a char value of *A* was stored as the integer value 65.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-743">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-743">New behavior</span></span>

<span data-ttu-id="0a217-744">Maintenant, les valeurs char sont stockées au format TEXT.</span><span class="sxs-lookup"><span data-stu-id="0a217-744">Char values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-745">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-745">Why</span></span>

<span data-ttu-id="0a217-746">Le stockage des valeurs au format TEXT est plus naturel et rend la base de données plus compatible avec d’autres technologies.</span><span class="sxs-lookup"><span data-stu-id="0a217-746">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-747">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-747">Mitigations</span></span>

<span data-ttu-id="0a217-748">Vous pouvez migrer des bases de données existantes vers le nouveau format en exécutant SQL comme suit.</span><span class="sxs-lookup"><span data-stu-id="0a217-748">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="0a217-749">Dans EF Core, vous pouvez également continuer à utiliser le comportement précédent en configurant un convertisseur de valeur sur ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="0a217-749">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="0a217-750">Microsoft.Data.Sqlite est aussi toujours capable de lire les valeurs de caractère à partir de colonnes INTEGER et TEXT, donc certains scénarios peuvent ne nécessiter aucune action.</span><span class="sxs-lookup"><span data-stu-id="0a217-750">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="0a217-751">Les ID de migration sont maintenant générés à l’aide du calendrier de la culture invariante</span><span class="sxs-lookup"><span data-stu-id="0a217-751">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="0a217-752">Suivi du problème n<c0>o </c0>12978</span><span class="sxs-lookup"><span data-stu-id="0a217-752">Tracking Issue #12978</span></span>](https://github.com/dotnet/efcore/issues/12978)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-753">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-753">Old behavior</span></span>

<span data-ttu-id="0a217-754">Les ID de migration ont été générés par inadvertance à l’aide du calendrier de la culture actuelle.</span><span class="sxs-lookup"><span data-stu-id="0a217-754">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-755">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-755">New behavior</span></span>

<span data-ttu-id="0a217-756">Maintenant, les ID de migration sont toujours générés à l’aide du calendrier de la culture invariante (grégorien).</span><span class="sxs-lookup"><span data-stu-id="0a217-756">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-757">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-757">Why</span></span>

<span data-ttu-id="0a217-758">L’ordre des migrations est important lors de la mise à jour de la base de données ou de la résolution des conflits de fusion.</span><span class="sxs-lookup"><span data-stu-id="0a217-758">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="0a217-759">L’utilisation du calendrier invariant permet d’éviter les problèmes de classement qui peuvent se produire si les membres de l’équipe ont des calendriers système différents.</span><span class="sxs-lookup"><span data-stu-id="0a217-759">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-760">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-760">Mitigations</span></span>

<span data-ttu-id="0a217-761">Ce changement affecte tous ceux qui utilisent un calendrier non grégorien où l’année est en avance sur le calendrier grégorien (comme le calendrier bouddhiste thaïlandais).</span><span class="sxs-lookup"><span data-stu-id="0a217-761">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="0a217-762">Les ID de migration existants devront être mis à jour afin que les nouvelles migrations soient classées après les migrations existantes.</span><span class="sxs-lookup"><span data-stu-id="0a217-762">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="0a217-763">Vous trouverez les ID de migration dans l’attribut Migration des fichiers de concepteur des migrations.</span><span class="sxs-lookup"><span data-stu-id="0a217-763">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

```diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="0a217-764">Le tableau de l’historique Migrations doit également être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="0a217-764">The Migrations history table also needs to be updated.</span></span>

```sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="0a217-765">UseRowNumberForPaging a été supprimé</span><span class="sxs-lookup"><span data-stu-id="0a217-765">UseRowNumberForPaging has been removed</span></span>

<span data-ttu-id="0a217-766">Suivi de problème no 16400</span><span class="sxs-lookup"><span data-stu-id="0a217-766">[Tracking Issue #16400](https://github.com/dotnet/efcore/issues/16400)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-767">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-767">Old behavior</span></span>

<span data-ttu-id="0a217-768">Avant EF Core 3.0, `UseRowNumberForPaging` pouvait être utilisé pour générer SQL pour la pagination qui est compatible avec SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="0a217-768">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-769">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-769">New behavior</span></span>

<span data-ttu-id="0a217-770">À compter de EF Core 3.0, EF génère uniquement SQL pour la pagination qui est uniquement compatible avec les versions de SQL Server ultérieures.</span><span class="sxs-lookup"><span data-stu-id="0a217-770">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-771">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-771">Why</span></span>

<span data-ttu-id="0a217-772">Nous effectuons cette modification, car [SQL Server 2008 n’est plus un produit pris en charge](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) et la mise à jour de cette fonctionnalité pour fonctionner avec les modifications de requête effectuées dans EF Core 3.0 est un travail significatif.</span><span class="sxs-lookup"><span data-stu-id="0a217-772">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-773">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-773">Mitigations</span></span>

<span data-ttu-id="0a217-774">Nous vous recommandons d’effectuer la mise à jour vers une version plus récente de SQL Server ou d’utiliser un niveau de compatibilité plus élevé, afin que le SQL généré soit pris en charge.</span><span class="sxs-lookup"><span data-stu-id="0a217-774">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="0a217-775">Cela dit, si vous ne pouvez pas faire cela, veuillez [commenter le problème de suivi](https://github.com/dotnet/efcore/issues/16400) en incluant des détails.</span><span class="sxs-lookup"><span data-stu-id="0a217-775">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/dotnet/efcore/issues/16400) with details.</span></span> <span data-ttu-id="0a217-776">Nous pourrions revisiter cette décision en fonction des commentaires.</span><span class="sxs-lookup"><span data-stu-id="0a217-776">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="0a217-777">Les info/métadonnées d’extension ont été supprimées de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="0a217-777">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

<span data-ttu-id="0a217-778">Suivi du problème no 16119</span><span class="sxs-lookup"><span data-stu-id="0a217-778">[Tracking Issue #16119](https://github.com/dotnet/efcore/issues/16119)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-779">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-779">Old behavior</span></span>

<span data-ttu-id="0a217-780">`IDbContextOptionsExtension` contenait des méthodes permettant de fournir des métadonnées relatives à l’extension.</span><span class="sxs-lookup"><span data-stu-id="0a217-780">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-781">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-781">New behavior</span></span>

<span data-ttu-id="0a217-782">Ces méthodes ont été déplacées vers une nouvelle classe de base abstraite `DbContextOptionsExtensionInfo`, qui est retournée à partir d’une nouvelle propriété `IDbContextOptionsExtension.Info`.</span><span class="sxs-lookup"><span data-stu-id="0a217-782">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-783">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-783">Why</span></span>

<span data-ttu-id="0a217-784">Sur les versions de 2.0 à 3.0, nous devions ajouter ou modifier ces méthodes plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="0a217-784">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="0a217-785">Les sortir dans une nouvelle classe de base abstraite simplifie l’apport de ces types de changements sans résiliation des extensions existantes.</span><span class="sxs-lookup"><span data-stu-id="0a217-785">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-786">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-786">Mitigations</span></span>

<span data-ttu-id="0a217-787">Mettre à jour des extensions pour suivre le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="0a217-787">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="0a217-788">Des exemples se trouvent dans la plupart des implémentations de `IDbContextOptionsExtension` pour différents types d’extensions dans le code source EF Core.</span><span class="sxs-lookup"><span data-stu-id="0a217-788">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="0a217-789">LogQueryPossibleExceptionWithAggregateOperator a été renommé</span><span class="sxs-lookup"><span data-stu-id="0a217-789">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="0a217-790">Suivi du problème #10985</span><span class="sxs-lookup"><span data-stu-id="0a217-790">Tracking Issue #10985</span></span>](https://github.com/dotnet/efcore/issues/10985)

#### <a name="change"></a><span data-ttu-id="0a217-791">Modifier</span><span class="sxs-lookup"><span data-stu-id="0a217-791">Change</span></span>

<span data-ttu-id="0a217-792">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` a été renommé en `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="0a217-792">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-793">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-793">Why</span></span>

<span data-ttu-id="0a217-794">Aligne le nom de cet événement d’avertissement avec tous les autres événements d’avertissement.</span><span class="sxs-lookup"><span data-stu-id="0a217-794">Aligns the naming of this warning event with all other warning events.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-795">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-795">Mitigations</span></span>

<span data-ttu-id="0a217-796">Utilisez le nouveau nom.</span><span class="sxs-lookup"><span data-stu-id="0a217-796">Use the new name.</span></span> <span data-ttu-id="0a217-797">(Notez que le numéro d’ID événement n’a pas changé.)</span><span class="sxs-lookup"><span data-stu-id="0a217-797">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="0a217-798">Clarifier les noms de contrainte de clé étrangère dans l’API</span><span class="sxs-lookup"><span data-stu-id="0a217-798">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="0a217-799">Suivi du problème #10730</span><span class="sxs-lookup"><span data-stu-id="0a217-799">Tracking Issue #10730</span></span>](https://github.com/dotnet/efcore/issues/10730)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-800">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-800">Old behavior</span></span>

<span data-ttu-id="0a217-801">Avant EF Core 3.0, les noms de contrainte de clé étrangère étaient désignés simplement par le terme « nom ».</span><span class="sxs-lookup"><span data-stu-id="0a217-801">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="0a217-802">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-802">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

#### <a name="new-behavior"></a><span data-ttu-id="0a217-803">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-803">New behavior</span></span>

<span data-ttu-id="0a217-804">À compter d’EF Core 3.0, les noms de contrainte de clé étrangère sont désignés par « noms de contrainte ».</span><span class="sxs-lookup"><span data-stu-id="0a217-804">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="0a217-805">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-805">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

#### <a name="why"></a><span data-ttu-id="0a217-806">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-806">Why</span></span>

<span data-ttu-id="0a217-807">Ce changement rend le nommage plus cohérent dans ce domaine. Il clarifie également le fait qu’il s’agit du nom de la contrainte de clé étrangère et pas du nom de la colonne ou de la propriété sur laquelle la clé étrangère est définie.</span><span class="sxs-lookup"><span data-stu-id="0a217-807">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-808">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-808">Mitigations</span></span>

<span data-ttu-id="0a217-809">Utilisez le nouveau nom.</span><span class="sxs-lookup"><span data-stu-id="0a217-809">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="0a217-810">IRelationalDatabaseCreator.HasTables/HasTablesAsync ont été rendues publiques</span><span class="sxs-lookup"><span data-stu-id="0a217-810">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

<span data-ttu-id="0a217-811">Suivi du problème no 15997</span><span class="sxs-lookup"><span data-stu-id="0a217-811">[Tracking Issue #15997](https://github.com/dotnet/efcore/issues/15997)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-812">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-812">Old behavior</span></span>

<span data-ttu-id="0a217-813">Avant EF Core 3.0, ces méthodes étaient protégées.</span><span class="sxs-lookup"><span data-stu-id="0a217-813">Before EF Core 3.0, these methods were protected.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-814">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-814">New behavior</span></span>

<span data-ttu-id="0a217-815">À compter d’EF Core 3.0, ces méthodes sont publiques.</span><span class="sxs-lookup"><span data-stu-id="0a217-815">Starting with EF Core 3.0, these methods are public.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-816">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-816">Why</span></span>

<span data-ttu-id="0a217-817">Ces méthodes sont utilisées par EF pour déterminer si une base de données est créée mais vide.</span><span class="sxs-lookup"><span data-stu-id="0a217-817">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="0a217-818">Cela peut également être utile depuis l’extérieur d’EF lorsque vous déterminez s’il faut appliquer des migrations ou pas.</span><span class="sxs-lookup"><span data-stu-id="0a217-818">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-819">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-819">Mitigations</span></span>

<span data-ttu-id="0a217-820">Modifiez l’accessibilité de n’importe quel remplacements.</span><span class="sxs-lookup"><span data-stu-id="0a217-820">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="0a217-821">Microsoft.EntityFrameworkCore.Design est désormais un package DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="0a217-821">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

<span data-ttu-id="0a217-822">Suivi du problème no 11506</span><span class="sxs-lookup"><span data-stu-id="0a217-822">[Tracking Issue #11506](https://github.com/dotnet/efcore/issues/11506)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-823">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-823">Old behavior</span></span>

<span data-ttu-id="0a217-824">Avant EF Core 3.0, Microsoft.EntityFrameworkCore.Design était un package NuGet normal dont l’assembly pouvait être référencée par des projets qui en dépendaient.</span><span class="sxs-lookup"><span data-stu-id="0a217-824">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-825">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-825">New behavior</span></span>

<span data-ttu-id="0a217-826">À compter d’EF Core 3.0, il s’agit d’un package DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="0a217-826">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="0a217-827">Cela signifie que la dépendance ne sera pas transitive dans d’autres projets, et que vous ne pouvez plus, par défaut, référencer son assembly.</span><span class="sxs-lookup"><span data-stu-id="0a217-827">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-828">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-828">Why</span></span>

<span data-ttu-id="0a217-829">Ce package est uniquement destiné à être utilisé au moment du design.</span><span class="sxs-lookup"><span data-stu-id="0a217-829">This package is only intended to be used at design time.</span></span> <span data-ttu-id="0a217-830">Les applications déployées ne doivent pas y faire référence.</span><span class="sxs-lookup"><span data-stu-id="0a217-830">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="0a217-831">Le fait de faire de ce package un DevelopmentDependency renforce cette recommandation.</span><span class="sxs-lookup"><span data-stu-id="0a217-831">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-832">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-832">Mitigations</span></span>

<span data-ttu-id="0a217-833">Si vous avez besoin de référencer ce package pour remplacer le comportement de EF Core au moment de la conception, vous pouvez mettre à jour les métadonnées de l’élément PackageReference dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="0a217-833">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="0a217-834">Si le package est référencé de manière transitive via Microsoft.EntityFrameworkCore.Tools, vous devez ajouter un PackageReference explicite au package pour modifier ses métadonnées.</span><span class="sxs-lookup"><span data-stu-id="0a217-834">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="0a217-835">Une telle référence explicite doit être ajoutée à n’importe quel projet où les types du package sont nécessaires.</span><span class="sxs-lookup"><span data-stu-id="0a217-835">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="0a217-836">SQLitePCL.raw mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="0a217-836">SQLitePCL.raw updated to version 2.0.0</span></span>

<span data-ttu-id="0a217-837">Suivi du problème no 14824</span><span class="sxs-lookup"><span data-stu-id="0a217-837">[Tracking Issue #14824](https://github.com/dotnet/efcore/issues/14824)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-838">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-838">Old behavior</span></span>

<span data-ttu-id="0a217-839">Microsoft.EntityFrameworkCore.Sqlite dépendait précédemment de la version 1.1.12 de SQLitePCL.raw.</span><span class="sxs-lookup"><span data-stu-id="0a217-839">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-840">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-840">New behavior</span></span>

<span data-ttu-id="0a217-841">Nous avons mis à jour notre package pour dépendre de la version 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="0a217-841">We've updated our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-842">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-842">Why</span></span>

<span data-ttu-id="0a217-843">La version 2.0.0 de SQLitePCL.raw cible .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="0a217-843">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="0a217-844">Elle ciblait précédemment .NET Standard 1.1 qui nécessitait une fermeture volumineuse de packages transitifs pour fonctionner.</span><span class="sxs-lookup"><span data-stu-id="0a217-844">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-845">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-845">Mitigations</span></span>

<span data-ttu-id="0a217-846">SQLitePCL.raw version 2.0.0 inclut des changements cassants.</span><span class="sxs-lookup"><span data-stu-id="0a217-846">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="0a217-847">Pour plus d’informations, consultez les [notes de publication](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) .</span><span class="sxs-lookup"><span data-stu-id="0a217-847">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="0a217-848">NetTopologySuite mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="0a217-848">NetTopologySuite updated to version 2.0.0</span></span>

<span data-ttu-id="0a217-849">Suivi de problème no 14825</span><span class="sxs-lookup"><span data-stu-id="0a217-849">[Tracking Issue #14825](https://github.com/dotnet/efcore/issues/14825)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-850">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-850">Old behavior</span></span>

<span data-ttu-id="0a217-851">Les packages spatiaux dépendaient précédemment de la version 1.15.1 de NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="0a217-851">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-852">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-852">New behavior</span></span>

<span data-ttu-id="0a217-853">Nous avons mis à jour notre package pour dépendre de la version 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="0a217-853">We've update our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-854">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-854">Why</span></span>

<span data-ttu-id="0a217-855">La version 2.0.0 de NetTopologySuite vise à résoudre plusieurs problèmes de facilité d’utilisation rencontrés par les utilisateurs EF Core.</span><span class="sxs-lookup"><span data-stu-id="0a217-855">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-856">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-856">Mitigations</span></span>

<span data-ttu-id="0a217-857">NetTopologySuite version 2.0.0 inclut des changements cassants.</span><span class="sxs-lookup"><span data-stu-id="0a217-857">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="0a217-858">Pour plus d’informations, consultez les [notes de publication](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) .</span><span class="sxs-lookup"><span data-stu-id="0a217-858">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="0a217-859">Microsoft. Data. SqlClient est utilisé à la place de System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="0a217-859">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="0a217-860">#15636 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="0a217-860">Tracking Issue #15636</span></span>](https://github.com/dotnet/efcore/issues/15636)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-861">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-861">Old behavior</span></span>

<span data-ttu-id="0a217-862">Microsoft. EntityFrameworkCore. SqlServer était auparavant tributaire de System. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="0a217-862">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0a217-863">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-863">New behavior</span></span>

<span data-ttu-id="0a217-864">Nous avons mis à jour notre package pour dépendre de Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="0a217-864">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-865">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-865">Why</span></span>

<span data-ttu-id="0a217-866">Microsoft. Data. SqlClient est le pilote d’accès aux données phare pour la SQL Server à l’avenir, et System. Data. SqlClient ne représente plus le point de vue du développement.</span><span class="sxs-lookup"><span data-stu-id="0a217-866">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="0a217-867">Certaines fonctionnalités importantes, telles que Always Encrypted, sont uniquement disponibles sur Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="0a217-867">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-868">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-868">Mitigations</span></span>

<span data-ttu-id="0a217-869">Si votre code prend une dépendance directe sur System. Data. SqlClient, vous devez le modifier pour qu’il fasse référence à Microsoft. Data. SqlClient à la place. étant donné que les deux packages maintiennent un très haut niveau de compatibilité d’API, il ne doit y avoir qu’une simple modification de package et d’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="0a217-869">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="0a217-870">Plusieurs relations autoréférencées ambiguës doivent être configurées</span><span class="sxs-lookup"><span data-stu-id="0a217-870">Multiple ambiguous self-referencing relationships must be configured</span></span>

<span data-ttu-id="0a217-871">Suivi de problème no 13573</span><span class="sxs-lookup"><span data-stu-id="0a217-871">[Tracking Issue #13573](https://github.com/dotnet/efcore/issues/13573)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="0a217-872">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-872">Old behavior</span></span>

<span data-ttu-id="0a217-873">Un type d’entité avec plusieurs propriétés de navigation unidirectionnelle autoréférencées et les clés étrangères correspondantes a été incorrectement configuré en tant que relation unique.</span><span class="sxs-lookup"><span data-stu-id="0a217-873">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="0a217-874">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-874">For example:</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="0a217-875">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-875">New behavior</span></span>

<span data-ttu-id="0a217-876">Ce scénario est maintenant détecté dans la génération de modèle et une exception est levée, indiquant que le modèle est ambigu.</span><span class="sxs-lookup"><span data-stu-id="0a217-876">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-877">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-877">Why</span></span>

<span data-ttu-id="0a217-878">Le modèle résultant est ambigu et probablement erroné dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="0a217-878">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-879">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-879">Mitigations</span></span>

<span data-ttu-id="0a217-880">Utilisez la configuration complète de la relation.</span><span class="sxs-lookup"><span data-stu-id="0a217-880">Use full configuration of the relationship.</span></span> <span data-ttu-id="0a217-881">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="0a217-881">For example:</span></span>

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

### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="0a217-882">DbFunction. Schema étant null ou une chaîne vide, il est configuré pour être dans le schéma par défaut du modèle</span><span class="sxs-lookup"><span data-stu-id="0a217-882">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="0a217-883">#12757 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="0a217-883">Tracking Issue #12757</span></span>](https://github.com/dotnet/efcore/issues/12757)

#### <a name="old-behavior"></a><span data-ttu-id="0a217-884">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-884">Old behavior</span></span>

<span data-ttu-id="0a217-885">Un DbFunction configuré avec un schéma sous forme de chaîne vide a été traité comme une fonction intégrée sans schéma.</span><span class="sxs-lookup"><span data-stu-id="0a217-885">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="0a217-886">Par exemple, le code suivant mappera la `DatePart` fonction CLR à la `DATEPART` fonction intégrée sur SqlServer.</span><span class="sxs-lookup"><span data-stu-id="0a217-886">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

#### <a name="new-behavior"></a><span data-ttu-id="0a217-887">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="0a217-887">New behavior</span></span>

<span data-ttu-id="0a217-888">Tous les mappages de DbFunction sont considérés comme mappés à des fonctions définies par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0a217-888">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="0a217-889">Par conséquent, la valeur de chaîne vide placerait la fonction dans le schéma par défaut pour le modèle.</span><span class="sxs-lookup"><span data-stu-id="0a217-889">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="0a217-890">Ce peut être le schéma configuré explicitement via l’API Fluent `modelBuilder.HasDefaultSchema()` ou `dbo` sinon.</span><span class="sxs-lookup"><span data-stu-id="0a217-890">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

#### <a name="why"></a><span data-ttu-id="0a217-891">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="0a217-891">Why</span></span>

<span data-ttu-id="0a217-892">Le schéma précédemment vide était un moyen de traiter que la fonction est intégrée, mais cette logique s’applique uniquement à SqlServer, où les fonctions intégrées n’appartiennent à aucun schéma.</span><span class="sxs-lookup"><span data-stu-id="0a217-892">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0a217-893">Corrections</span><span class="sxs-lookup"><span data-stu-id="0a217-893">Mitigations</span></span>

<span data-ttu-id="0a217-894">Configurez la traduction de DbFunction manuellement pour la mapper à une fonction intégrée.</span><span class="sxs-lookup"><span data-stu-id="0a217-894">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```

<a name="netstandard21"></a>

### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20-reverted"></a><span data-ttu-id="0a217-895">~~EF Core 3,0 cibles .NET Standard 2,1 au lieu de .NET Standard 2,0~~ Rétablie</span><span class="sxs-lookup"><span data-stu-id="0a217-895">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>

<span data-ttu-id="0a217-896">Suivi de problème no 15498</span><span class="sxs-lookup"><span data-stu-id="0a217-896">[Tracking Issue #15498](https://github.com/dotnet/efcore/issues/15498)</span></span>

<span data-ttu-id="0a217-897">EF Core 3,0 cible .NET Standard 2,1, qui est une modification avec rupture qui exclut les applications .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="0a217-897">EF Core 3.0 targets .NET Standard 2.1, which is a breaking change which excludes .NET Framework applications.</span></span> <span data-ttu-id="0a217-898">EF Core 3,1 a rétabli l’opération et cible à nouveau .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="0a217-898">EF Core 3.1 reverted this and targets .NET Standard 2.0 again.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="0a217-899">~~L’exécution de requêtes est enregistrée au niveau du débogage ~~Rétabli</span><span class="sxs-lookup"><span data-stu-id="0a217-899">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="0a217-900">Suivi du problème n<c0>o </c0>14523</span><span class="sxs-lookup"><span data-stu-id="0a217-900">Tracking Issue #14523</span></span>](https://github.com/dotnet/efcore/issues/14523)

<span data-ttu-id="0a217-901">Nous avons rétabli ce changement car la nouvelle configuration dans EF Core 3.0 permet la spécification du niveau d’enregistrement d’un événement par l’application.</span><span class="sxs-lookup"><span data-stu-id="0a217-901">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="0a217-902">Par exemple, pour basculer l’enregistrement de SQL vers `Debug`, configurez explicitement le niveau dans `OnConfiguring` ou `AddDbContext` :</span><span class="sxs-lookup"><span data-stu-id="0a217-902">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```
