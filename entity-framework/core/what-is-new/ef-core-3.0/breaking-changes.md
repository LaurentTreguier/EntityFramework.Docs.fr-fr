---
title: Changements cassants dans EF Core 3.0 - EF Core
author: ajcvickers
ms.date: 12/03/2019
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: cac166e9e194e512de7d730d27c061e6deaf5191
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502225"
---
# <a name="breaking-changes-included-in-ef-core-30"></a><span data-ttu-id="929f3-102">Dernières modifications incluses dans EF Core 3,0</span><span class="sxs-lookup"><span data-stu-id="929f3-102">Breaking changes included in EF Core 3.0</span></span>

<span data-ttu-id="929f3-103">Les modifications d’API et de comportement suivantes peuvent bloquer les applications existantes lors de leur mise à niveau vers 3.0.0.</span><span class="sxs-lookup"><span data-stu-id="929f3-103">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="929f3-104">Les changements qui, selon nous, auront une incidence uniquement sur les fournisseurs de base de données sont documentés dans [Changements ayant un impact sur les fournisseurs](xref:core/providers/provider-log).</span><span class="sxs-lookup"><span data-stu-id="929f3-104">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="929f3-105">Récapitulatif</span><span class="sxs-lookup"><span data-stu-id="929f3-105">Summary</span></span>

| <span data-ttu-id="929f3-106">**Modification critique**</span><span class="sxs-lookup"><span data-stu-id="929f3-106">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="929f3-107">**Impact**</span><span class="sxs-lookup"><span data-stu-id="929f3-107">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="929f3-108">Les requêtes LINQ ne sont plus évaluées sur le client</span><span class="sxs-lookup"><span data-stu-id="929f3-108">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="929f3-109">Élevée</span><span class="sxs-lookup"><span data-stu-id="929f3-109">High</span></span>       |
| [<span data-ttu-id="929f3-110">EF Core 3.0 cible .NET Standard 2.1 plutôt que .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="929f3-110">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="929f3-111">Élevée</span><span class="sxs-lookup"><span data-stu-id="929f3-111">High</span></span>      |
| [<span data-ttu-id="929f3-112">L’outil en ligne de commande EF Core, dotnet ef, ne fait plus partie du SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="929f3-112">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="929f3-113">Élevée</span><span class="sxs-lookup"><span data-stu-id="929f3-113">High</span></span>      |
| [<span data-ttu-id="929f3-114">DetectChanges honore les valeurs de clés générées par le magasin</span><span class="sxs-lookup"><span data-stu-id="929f3-114">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="929f3-115">Élevée</span><span class="sxs-lookup"><span data-stu-id="929f3-115">High</span></span>      |
| [<span data-ttu-id="929f3-116">FromSql, ExecuteSql et ExecuteSqlAsync ont été renommés</span><span class="sxs-lookup"><span data-stu-id="929f3-116">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="929f3-117">Élevée</span><span class="sxs-lookup"><span data-stu-id="929f3-117">High</span></span>      |
| [<span data-ttu-id="929f3-118">Les types de requêtes sont regroupés avec les types d’entités</span><span class="sxs-lookup"><span data-stu-id="929f3-118">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="929f3-119">Élevée</span><span class="sxs-lookup"><span data-stu-id="929f3-119">High</span></span>      |
| [<span data-ttu-id="929f3-120">Entity Framework Core ne fait plus partie du framework partagé ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="929f3-120">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="929f3-121">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-121">Medium</span></span>      |
| [<span data-ttu-id="929f3-122">Les suppressions en cascade se produisent désormais immédiatement par défaut</span><span class="sxs-lookup"><span data-stu-id="929f3-122">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="929f3-123">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-123">Medium</span></span>      |
| [<span data-ttu-id="929f3-124">Le chargement hâtif des entités associées se produit désormais dans une seule requête</span><span class="sxs-lookup"><span data-stu-id="929f3-124">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="929f3-125">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-125">Medium</span></span>      |
| [<span data-ttu-id="929f3-126">La sémantique de DeleteBehavior.Restrict est désormais plus propre</span><span class="sxs-lookup"><span data-stu-id="929f3-126">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="929f3-127">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-127">Medium</span></span>      |
| [<span data-ttu-id="929f3-128">L’API de configuration pour les relations de type détenu a changé</span><span class="sxs-lookup"><span data-stu-id="929f3-128">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="929f3-129">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-129">Medium</span></span>      |
| [<span data-ttu-id="929f3-130">Chaque propriété utilise la génération indépendante de clé de type entier en mémoire</span><span class="sxs-lookup"><span data-stu-id="929f3-130">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="929f3-131">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-131">Medium</span></span>      |
| [<span data-ttu-id="929f3-132">Les requêtes sans suivi ne procèdent plus à la résolution de l’identité</span><span class="sxs-lookup"><span data-stu-id="929f3-132">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="929f3-133">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-133">Medium</span></span>      |
| [<span data-ttu-id="929f3-134">Changements apportés à l’API de métadonnées</span><span class="sxs-lookup"><span data-stu-id="929f3-134">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="929f3-135">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-135">Medium</span></span>      |
| [<span data-ttu-id="929f3-136">Modifications de l’API de métadonnées spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="929f3-136">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="929f3-137">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-137">Medium</span></span>      |
| [<span data-ttu-id="929f3-138">UseRowNumberForPaging a été supprimé</span><span class="sxs-lookup"><span data-stu-id="929f3-138">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="929f3-139">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-139">Medium</span></span>      |
| [<span data-ttu-id="929f3-140">La méthode FromSql quand elle est utilisée avec une procédure stockée ne peut pas être composée</span><span class="sxs-lookup"><span data-stu-id="929f3-140">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="929f3-141">Moyen</span><span class="sxs-lookup"><span data-stu-id="929f3-141">Medium</span></span>      |
| [<span data-ttu-id="929f3-142">Les méthodes FromSql peuvent uniquement être spécifiées sur les racines de requête</span><span class="sxs-lookup"><span data-stu-id="929f3-142">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="929f3-143">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-143">Low</span></span>      |
| [<span data-ttu-id="929f3-144">~~L’exécution de requêtes est enregistrée au niveau du débogage ~~Rétabli</span><span class="sxs-lookup"><span data-stu-id="929f3-144">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="929f3-145">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-145">Low</span></span>      |
| [<span data-ttu-id="929f3-146">Les valeurs de clés temporaires ne sont plus définies sur les instances d’entités</span><span class="sxs-lookup"><span data-stu-id="929f3-146">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="929f3-147">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-147">Low</span></span>      |
| [<span data-ttu-id="929f3-148">Les entités dépendantes qui partagent la table avec le principal sont maintenant facultatives</span><span class="sxs-lookup"><span data-stu-id="929f3-148">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="929f3-149">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-149">Low</span></span>      |
| [<span data-ttu-id="929f3-150">Toutes les entités qui partagent une table avec une colonne de jeton de concurrence doivent la mapper à une propriété</span><span class="sxs-lookup"><span data-stu-id="929f3-150">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="929f3-151">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-151">Low</span></span>      |
| [<span data-ttu-id="929f3-152">Les entités détenues ne peuvent pas être interrogées sans le propriétaire à l’aide d’une requête de suivi</span><span class="sxs-lookup"><span data-stu-id="929f3-152">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="929f3-153">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-153">Low</span></span>      |
| [<span data-ttu-id="929f3-154">Les propriétés héritées de types non mappés sont maintenant mappées à une seule colonne pour tous les types dérivés</span><span class="sxs-lookup"><span data-stu-id="929f3-154">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="929f3-155">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-155">Low</span></span>      |
| [<span data-ttu-id="929f3-156">La convention de propriété de clé étrangère ne correspond plus au nom de la propriété principale</span><span class="sxs-lookup"><span data-stu-id="929f3-156">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="929f3-157">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-157">Low</span></span>      |
| [<span data-ttu-id="929f3-158">La connexion de base de données est maintenant fermée si elle n’est plus utilisée avant la fin de TransactionScope</span><span class="sxs-lookup"><span data-stu-id="929f3-158">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="929f3-159">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-159">Low</span></span>      |
| [<span data-ttu-id="929f3-160">Les champs de stockage sont utilisés par défaut</span><span class="sxs-lookup"><span data-stu-id="929f3-160">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="929f3-161">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-161">Low</span></span>      |
| [<span data-ttu-id="929f3-162">Erreur si plusieurs champs de stockage compatibles sont détectés</span><span class="sxs-lookup"><span data-stu-id="929f3-162">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="929f3-163">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-163">Low</span></span>      |
| [<span data-ttu-id="929f3-164">Les noms de propriété de type « champ uniquement » doivent correspondre au nom du champ</span><span class="sxs-lookup"><span data-stu-id="929f3-164">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="929f3-165">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-165">Low</span></span>      |
| [<span data-ttu-id="929f3-166">AddDbContext/AddDbContextPool n’appelle plus AddLogging et AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="929f3-166">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="929f3-167">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-167">Low</span></span>      |
| [<span data-ttu-id="929f3-168">AddEntityFramework \* ajoute IMemoryCache avec une limite de taille</span><span class="sxs-lookup"><span data-stu-id="929f3-168">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="929f3-169">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-169">Low</span></span>      |
| [<span data-ttu-id="929f3-170">DbContext.Entry effectue maintenant un DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="929f3-170">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="929f3-171">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-171">Low</span></span>      |
| [<span data-ttu-id="929f3-172">Les clés de tableaux d’octets et de chaînes ne sont pas générées par client par défaut</span><span class="sxs-lookup"><span data-stu-id="929f3-172">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="929f3-173">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-173">Low</span></span>      |
| [<span data-ttu-id="929f3-174">ILoggerFactory est désormais un service délimité</span><span class="sxs-lookup"><span data-stu-id="929f3-174">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="929f3-175">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-175">Low</span></span>      |
| [<span data-ttu-id="929f3-176">Les proxys à chargement différé ne partent plus du principe que les propriétés de navigation sont entièrement chargées</span><span class="sxs-lookup"><span data-stu-id="929f3-176">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="929f3-177">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-177">Low</span></span>      |
| [<span data-ttu-id="929f3-178">La création excessive de fournisseurs de services internes est maintenant une erreur par défaut</span><span class="sxs-lookup"><span data-stu-id="929f3-178">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="929f3-179">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-179">Low</span></span>      |
| [<span data-ttu-id="929f3-180">Nouveau comportement de HasOne/HasMany appelé avec une chaîne unique</span><span class="sxs-lookup"><span data-stu-id="929f3-180">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="929f3-181">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-181">Low</span></span>      |
| [<span data-ttu-id="929f3-182">Le type de retour Task pour plusieurs méthodes asynchrones a été remplacé par ValueTask</span><span class="sxs-lookup"><span data-stu-id="929f3-182">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="929f3-183">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-183">Low</span></span>      |
| [<span data-ttu-id="929f3-184">L’annotation Relational:TypeMapping est désormais simplement TypeMapping</span><span class="sxs-lookup"><span data-stu-id="929f3-184">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="929f3-185">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-185">Low</span></span>      |
| [<span data-ttu-id="929f3-186">ToTable sur un type dérivé lève une exception</span><span class="sxs-lookup"><span data-stu-id="929f3-186">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="929f3-187">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-187">Low</span></span>      |
| [<span data-ttu-id="929f3-188">EF Core n’envoie plus de pragma pour l’application de clé étrangère SQLite</span><span class="sxs-lookup"><span data-stu-id="929f3-188">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="929f3-189">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-189">Low</span></span>      |
| [<span data-ttu-id="929f3-190">Microsoft.EntityFrameworkCore.Sqlite dépend désormais de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="929f3-190">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="929f3-191">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-191">Low</span></span>      |
| [<span data-ttu-id="929f3-192">Les valeurs GUID sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="929f3-192">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="929f3-193">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-193">Low</span></span>      |
| [<span data-ttu-id="929f3-194">Les valeurs char sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="929f3-194">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="929f3-195">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-195">Low</span></span>      |
| [<span data-ttu-id="929f3-196">Les ID de migration sont maintenant générés à l’aide du calendrier de la culture invariante</span><span class="sxs-lookup"><span data-stu-id="929f3-196">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="929f3-197">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-197">Low</span></span>      |
| [<span data-ttu-id="929f3-198">Les infos/métadonnées d’extension ont été supprimées de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="929f3-198">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="929f3-199">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-199">Low</span></span>      |
| [<span data-ttu-id="929f3-200">LogQueryPossibleExceptionWithAggregateOperator a été renommé</span><span class="sxs-lookup"><span data-stu-id="929f3-200">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="929f3-201">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-201">Low</span></span>      |
| [<span data-ttu-id="929f3-202">Clarifier les noms de contrainte de clé étrangère dans l’API</span><span class="sxs-lookup"><span data-stu-id="929f3-202">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="929f3-203">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-203">Low</span></span>      |
| [<span data-ttu-id="929f3-204">IRelationalDatabaseCreator.HasTables/HasTablesAsync ont été rendues publiques</span><span class="sxs-lookup"><span data-stu-id="929f3-204">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="929f3-205">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-205">Low</span></span>      |
| [<span data-ttu-id="929f3-206">Microsoft.EntityFrameworkCore.Design est désormais un package DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="929f3-206">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="929f3-207">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-207">Low</span></span>      |
| [<span data-ttu-id="929f3-208">SQLitePCL.raw mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="929f3-208">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="929f3-209">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-209">Low</span></span>      |
| [<span data-ttu-id="929f3-210">NetTopologySuite mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="929f3-210">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="929f3-211">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-211">Low</span></span>      |
| [<span data-ttu-id="929f3-212">Microsoft. Data. SqlClient est utilisé à la place de System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="929f3-212">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="929f3-213">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-213">Low</span></span>      |
| [<span data-ttu-id="929f3-214">Plusieurs relations autoréférencées ambiguës doivent être configurées</span><span class="sxs-lookup"><span data-stu-id="929f3-214">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="929f3-215">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-215">Low</span></span>      |
| [<span data-ttu-id="929f3-216">DbFunction. Schema étant null ou une chaîne vide, il est configuré pour être dans le schéma par défaut du modèle</span><span class="sxs-lookup"><span data-stu-id="929f3-216">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="929f3-217">Faible</span><span class="sxs-lookup"><span data-stu-id="929f3-217">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="929f3-218">Les requêtes LINQ ne sont plus évaluées sur le client</span><span class="sxs-lookup"><span data-stu-id="929f3-218">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="929f3-219">[Suivi de problème no 14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Voir également problème no 12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="929f3-219">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="929f3-220">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-220">**Old behavior**</span></span>

<span data-ttu-id="929f3-221">Avant la version 3.0, quand EF Core ne pouvait pas convertir une expression faisant partie d’une requête en SQL ou en paramètre, elle évaluait automatiquement l’expression sur le client.</span><span class="sxs-lookup"><span data-stu-id="929f3-221">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="929f3-222">Par défaut, l’évaluation sur le client d’expressions potentiellement coûteuses déclenchait uniquement un avertissement.</span><span class="sxs-lookup"><span data-stu-id="929f3-222">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="929f3-223">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-223">**New behavior**</span></span>

<span data-ttu-id="929f3-224">À compter de la version 3.0, EF Core autorise uniquement l’évaluation sur le client des expressions qui figurent dans la projection de niveau supérieur (le dernier appel `Select()` dans la requête).</span><span class="sxs-lookup"><span data-stu-id="929f3-224">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="929f3-225">Quand des expressions d’une autre partie de la requête ne peuvent pas être converties en SQL ou en paramètre, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="929f3-225">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="929f3-226">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-226">**Why**</span></span>

<span data-ttu-id="929f3-227">L’évaluation automatique des requêtes sur le client permet à de nombreuses requêtes d’être exécutées même si certaines de leurs parties importantes ne peuvent pas être traduites.</span><span class="sxs-lookup"><span data-stu-id="929f3-227">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="929f3-228">Ce comportement peut entraîner un comportement inattendu et potentiellement dangereux qui peut devenir évident uniquement en production.</span><span class="sxs-lookup"><span data-stu-id="929f3-228">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="929f3-229">Par exemple, une condition dans un appel `Where()` qui ne peut pas être traduite peut provoquer le transfert de toutes les lignes de la table hors du serveur de base de données, et l’application du filtre sur le client.</span><span class="sxs-lookup"><span data-stu-id="929f3-229">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="929f3-230">Cette situation peut facilement passer inaperçue si la table contient uniquement quelques lignes lors du développement, mais poser davantage de problèmes quand l’application passe en production, où la table peut contenir plusieurs millions de lignes.</span><span class="sxs-lookup"><span data-stu-id="929f3-230">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="929f3-231">Les avertissements relatifs à l’évaluation sur le client étaient également trop faciles à ignorer pendant le développement.</span><span class="sxs-lookup"><span data-stu-id="929f3-231">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="929f3-232">De plus, l’évaluation automatique sur le client peut entraîner des problèmes selon lesquels l’amélioration de la traduction des requêtes pour des expressions spécifiques provoque un changement cassant involontaire entre les versions.</span><span class="sxs-lookup"><span data-stu-id="929f3-232">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="929f3-233">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-233">**Mitigations**</span></span>

<span data-ttu-id="929f3-234">Si une requête ne peut pas être entièrement traduite, réécrivez-la sous une forme qui peut être traduite ou utilisez `AsEnumerable()`, `ToList()` ou autre méthode similaire pour réimporter explicitement les données sur le client, où elles peuvent ensuite être traitées à l’aide de LINQ-to-Objects.</span><span class="sxs-lookup"><span data-stu-id="929f3-234">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="929f3-235">EF Core 3.0 cible .NET Standard 2.1 plutôt que .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="929f3-235">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

[<span data-ttu-id="929f3-236">Suivi de problème no 15498</span><span class="sxs-lookup"><span data-stu-id="929f3-236">Tracking Issue #15498</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

<span data-ttu-id="929f3-237">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-237">**Old behavior**</span></span>

<span data-ttu-id="929f3-238">Avant la version 3.0, EF Core ciblait .NET Standard 2.0 et s’exécutait sur toutes les plateformes qui prennent en charge cette norme, y compris .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="929f3-238">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="929f3-239">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-239">**New behavior**</span></span>

<span data-ttu-id="929f3-240">À partir de la version 3.0, EF Core cible .NET Standard 2.1 et s’exécute sur toutes les plateformes qui prennent en charge cette norme.</span><span class="sxs-lookup"><span data-stu-id="929f3-240">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="929f3-241">Cela n'inclut pas .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="929f3-241">This does not include .NET Framework.</span></span>

<span data-ttu-id="929f3-242">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-242">**Why**</span></span>

<span data-ttu-id="929f3-243">Cela fait partie d’une décision stratégique dans les technologies .NET de concentrer l’énergie sur .NET Core et d’autres plateformes .NET modernes, telles que Xamarin.</span><span class="sxs-lookup"><span data-stu-id="929f3-243">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="929f3-244">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-244">**Mitigations**</span></span>

<span data-ttu-id="929f3-245">Envisagez de passer à une plateforme .NET moderne.</span><span class="sxs-lookup"><span data-stu-id="929f3-245">Consider moving to a modern .NET platform.</span></span> <span data-ttu-id="929f3-246">Si ce n’est pas possible, continuez à utiliser EF Core 2.1 ou EF Core 2.2, qui prennent tous deux en charge .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="929f3-246">If this is not possible, then continue to use EF Core 2.1 or EF Core 2.2, both of which support .NET Framework.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="929f3-247">Entity Framework Core ne fait plus partie du framework partagé ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="929f3-247">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="929f3-248">Annonces de suivi de problème n°325</span><span class="sxs-lookup"><span data-stu-id="929f3-248">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="929f3-249">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-249">**Old behavior**</span></span>

<span data-ttu-id="929f3-250">Avant ASP.NET Core 3.0, quand vous ajoutiez une référence de package à `Microsoft.AspNetCore.App` ou `Microsoft.AspNetCore.All`, elle incluait EF Core et certains des fournisseurs de données EF Core tels que le fournisseur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="929f3-250">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="929f3-251">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-251">**New behavior**</span></span>

<span data-ttu-id="929f3-252">À compter de la version 3.0, le framework partagé ASP.NET Core n’inclut ni EF Core, ni aucun fournisseur de données EF Core.</span><span class="sxs-lookup"><span data-stu-id="929f3-252">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="929f3-253">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-253">**Why**</span></span>

<span data-ttu-id="929f3-254">Avant ce changement, l’obtention d’EF Core nécessitait différentes étapes selon que l’application ciblait ASP.NET Core et SQL Server ou non.</span><span class="sxs-lookup"><span data-stu-id="929f3-254">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="929f3-255">De plus, la mise à niveau d’ASP.NET Core forçait la mise à niveau d’EF Core et du fournisseur SQL Server, ce qui n’est pas toujours souhaitable.</span><span class="sxs-lookup"><span data-stu-id="929f3-255">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="929f3-256">Avec ce changement, l’expérience d’obtention d’EF Core est la même pour tous les fournisseurs, implémentations .NET prises en charge et types d’applications.</span><span class="sxs-lookup"><span data-stu-id="929f3-256">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="929f3-257">Les développeurs peuvent désormais contrôler exactement quand EF Core et les fournisseurs de données EF Core sont mis à niveau.</span><span class="sxs-lookup"><span data-stu-id="929f3-257">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="929f3-258">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-258">**Mitigations**</span></span>

<span data-ttu-id="929f3-259">Pour utiliser EF Core dans une application ASP.NET Core 3.0 ou toute autre application prise en charge, ajoutez explicitement une référence de package au fournisseur de base de données EF Core que votre application utilisera.</span><span class="sxs-lookup"><span data-stu-id="929f3-259">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="929f3-260">L’outil en ligne de commande EF Core, dotnet ef, ne fait plus partie du SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="929f3-260">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="929f3-261">Suivi du problème n° 14016</span><span class="sxs-lookup"><span data-stu-id="929f3-261">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="929f3-262">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-262">**Old behavior**</span></span>

<span data-ttu-id="929f3-263">Avant la version 3.0, l’outil `dotnet ef` était inclus dans le SDK .NET Core et prêt à l’emploi depuis la ligne de commande d’un projet sans nécessiter d’étapes supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="929f3-263">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="929f3-264">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-264">**New behavior**</span></span>

<span data-ttu-id="929f3-265">À compter de la version 3.0, le SDK .NET n’inclut pas l’outil `dotnet ef`, donc vous pouvez explicitement l’installer pour pouvoir l’utiliser comme outil local ou global.</span><span class="sxs-lookup"><span data-stu-id="929f3-265">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="929f3-266">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-266">**Why**</span></span>

<span data-ttu-id="929f3-267">Ce changement nous permet de distribuer et mettre à jour `dotnet ef` sous forme d’outil CLI .NET normal sur NuGet, ce qui est cohérent avec le fait qu’EF Core 3.0 est également toujours distribué sous forme de package NuGet.</span><span class="sxs-lookup"><span data-stu-id="929f3-267">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="929f3-268">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-268">**Mitigations**</span></span>

<span data-ttu-id="929f3-269">Pour pouvoir gérer des migrations ou générer un `DbContext`, installez `dotnet-ef` comme outil général :</span><span class="sxs-lookup"><span data-stu-id="929f3-269">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

<span data-ttu-id="929f3-270">Vous pouvez également obtenir un outil local quand vous restaurez les dépendances d’un projet qui le déclare en tant que dépendance d’outil à l’aide d’un [fichier manifeste d’outil](https://github.com/dotnet/cli/issues/10288).</span><span class="sxs-lookup"><span data-stu-id="929f3-270">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="929f3-271">FromSql, ExecuteSql et ExecuteSqlAsync ont été renommés</span><span class="sxs-lookup"><span data-stu-id="929f3-271">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="929f3-272">Suivi du problème no 10996</span><span class="sxs-lookup"><span data-stu-id="929f3-272">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="929f3-273">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-273">**Old behavior**</span></span>

<span data-ttu-id="929f3-274">Avant EF Core 3.0, ces noms de méthode étaient surchargés pour être utilisés avec une chaîne normale ou une chaîne devant être interpolée dans SQL et dans des paramètres.</span><span class="sxs-lookup"><span data-stu-id="929f3-274">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="929f3-275">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-275">**New behavior**</span></span>

<span data-ttu-id="929f3-276">À compter d’EF Core 3.0, utilisez `FromSqlRaw`, `ExecuteSqlRaw` et `ExecuteSqlRawAsync` pour créer une requête paramétrable, où les paramètres sont passés séparément à partir de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="929f3-276">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="929f3-277">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-277">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="929f3-278">Utilisez `FromSqlInterpolated`, `ExecuteSqlInterpolated` et `ExecuteSqlInterpolatedAsync` pour créer une requête paramétrable, où les paramètres sont passés dans le cadre d’une chaîne de requête interpolée.</span><span class="sxs-lookup"><span data-stu-id="929f3-278">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="929f3-279">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-279">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="929f3-280">Notez que les deux requêtes ci-dessus produisent le même SQL paramétrable avec les mêmes paramètres SQL.</span><span class="sxs-lookup"><span data-stu-id="929f3-280">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="929f3-281">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-281">**Why**</span></span>

<span data-ttu-id="929f3-282">Les surcharges de méthode comme celle-ci rendent très facile un appel accidentel à la méthode de chaîne brute quand l’intention est d’appeler la méthode de chaîne interpolée, et inversement.</span><span class="sxs-lookup"><span data-stu-id="929f3-282">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="929f3-283">De ce fait, les requêtes ne sont plus paramétrables alors qu’elles devraient l’être.</span><span class="sxs-lookup"><span data-stu-id="929f3-283">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="929f3-284">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-284">**Mitigations**</span></span>

<span data-ttu-id="929f3-285">Utilisez plutôt les nouveaux noms de méthode.</span><span class="sxs-lookup"><span data-stu-id="929f3-285">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="929f3-286">La méthode FromSql quand elle est utilisée avec une procédure stockée ne peut pas être composée</span><span class="sxs-lookup"><span data-stu-id="929f3-286">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="929f3-287">#15392 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="929f3-287">Tracking Issue #15392</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

<span data-ttu-id="929f3-288">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-288">**Old behavior**</span></span>

<span data-ttu-id="929f3-289">Avant le EF Core 3,0, la méthode FromSql a tenté de détecter si le SQL passé peut être composé.</span><span class="sxs-lookup"><span data-stu-id="929f3-289">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="929f3-290">Il a fait l’évaluation du client lorsque le SQL n’était pas composable comme une procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="929f3-290">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="929f3-291">La requête suivante a fonctionné en exécutant la procédure stockée sur le serveur et en procédant à l’opération FirstOrDefault côté client.</span><span class="sxs-lookup"><span data-stu-id="929f3-291">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

<span data-ttu-id="929f3-292">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-292">**New behavior**</span></span>

<span data-ttu-id="929f3-293">À compter de EF Core 3,0, EF Core n’essaiera pas d’analyser le SQL.</span><span class="sxs-lookup"><span data-stu-id="929f3-293">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="929f3-294">Par conséquent, si vous effectuez une composition après FromSqlRaw/FromSqlInterpolated, EF Core compose le SQL en provoquant une sous-requête.</span><span class="sxs-lookup"><span data-stu-id="929f3-294">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="929f3-295">Par conséquent, si vous utilisez une procédure stockée avec la composition, vous obtiendrez une exception pour la syntaxe SQL non valide.</span><span class="sxs-lookup"><span data-stu-id="929f3-295">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

<span data-ttu-id="929f3-296">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-296">**Why**</span></span>

<span data-ttu-id="929f3-297">EF Core 3,0 ne prend pas en charge l’évaluation automatique du client, car il s’agit d’une erreur, comme expliqué [ici](#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="929f3-297">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

<span data-ttu-id="929f3-298">**Atténuation**</span><span class="sxs-lookup"><span data-stu-id="929f3-298">**Mitigation**</span></span>

<span data-ttu-id="929f3-299">Si vous utilisez une procédure stockée dans FromSqlRaw/FromSqlInterpolated, vous savez qu’elle ne peut pas être composée. vous pouvez donc ajouter __AsEnumerable/AsAsyncEnumerable__ juste après l’appel de la méthode FromSql pour éviter toute composition côté serveur.</span><span class="sxs-lookup"><span data-stu-id="929f3-299">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="929f3-300">Les méthodes FromSql peuvent uniquement être spécifiées sur les racines de requête</span><span class="sxs-lookup"><span data-stu-id="929f3-300">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="929f3-301">Suivi de problème no 15704</span><span class="sxs-lookup"><span data-stu-id="929f3-301">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="929f3-302">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-302">**Old behavior**</span></span>

<span data-ttu-id="929f3-303">Avant EF Core 3.0, la méthode `FromSql` pouvant être spécifiée n’importe où dans la requête.</span><span class="sxs-lookup"><span data-stu-id="929f3-303">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="929f3-304">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-304">**New behavior**</span></span>

<span data-ttu-id="929f3-305">À partir d’EF Core 3.0, les nouvelles méthodes `FromSqlRaw` et `FromSqlInterpolated` (qui remplacent `FromSql`) peuvent être spécifiées uniquement sur les racines de requête, par exemple, directement sur le `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="929f3-305">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="929f3-306">Une erreur de compilation survient si vous tentez de les spécifier à un autre emplacement.</span><span class="sxs-lookup"><span data-stu-id="929f3-306">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="929f3-307">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-307">**Why**</span></span>

<span data-ttu-id="929f3-308">La spécification de `FromSql` n’importe où autre que sur un `DbSet` n’avait aucune signification ni valeur ajoutée et pouvait entraîner une ambiguïté dans certains scénarios.</span><span class="sxs-lookup"><span data-stu-id="929f3-308">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="929f3-309">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-309">**Mitigations**</span></span>

<span data-ttu-id="929f3-310">Les appels `FromSql` doivent être déplacés pour être directement sur le `DbSet` auquel ils s’appliquent.</span><span class="sxs-lookup"><span data-stu-id="929f3-310">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="929f3-311">Les requêtes sans suivi ne procèdent plus à la résolution de l’identité</span><span class="sxs-lookup"><span data-stu-id="929f3-311">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="929f3-312">Suivi de problème no 13518</span><span class="sxs-lookup"><span data-stu-id="929f3-312">Tracking Issue #13518</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

<span data-ttu-id="929f3-313">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-313">**Old behavior**</span></span>

<span data-ttu-id="929f3-314">Avant EF Core 3.0, la même instance d’entité était utilisée pour chaque occurrence d’une entité avec un type et un ID donnés.</span><span class="sxs-lookup"><span data-stu-id="929f3-314">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="929f3-315">Cela correspond au comportement des requêtes de suivi.</span><span class="sxs-lookup"><span data-stu-id="929f3-315">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="929f3-316">Par exemple, cette requête :</span><span class="sxs-lookup"><span data-stu-id="929f3-316">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="929f3-317">retournait la même instance `Category` pour chaque `Product` associé à la catégorie donnée.</span><span class="sxs-lookup"><span data-stu-id="929f3-317">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="929f3-318">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-318">**New behavior**</span></span>

<span data-ttu-id="929f3-319">À compter de EF Core 3.0, différentes instances d’entité sont créées lorsqu’une entité avec un type et un ID donnés est rencontrée à différents emplacements dans le graphe retourné.</span><span class="sxs-lookup"><span data-stu-id="929f3-319">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="929f3-320">Par exemple, la requête ci-dessus retourne à présent une nouvelle instance `Category` pour chaque `Product` même si deux produits sont associés à la même catégorie.</span><span class="sxs-lookup"><span data-stu-id="929f3-320">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="929f3-321">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-321">**Why**</span></span>

<span data-ttu-id="929f3-322">La résolution de l’identité (autrement dit, le fait de déterminer qu’une entité a les mêmes type et ID qu’une entité précédemment rencontrée) améliore les performances et la surcharge de la mémoire.</span><span class="sxs-lookup"><span data-stu-id="929f3-322">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="929f3-323">Cela agit généralement à l’opposé de la raison pour laquelle aucune requête de suivi n’est utilisée en premier lieu.</span><span class="sxs-lookup"><span data-stu-id="929f3-323">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="929f3-324">En outre, même si la résolution de l’identité peut parfois être utile, elle n’est pas nécessaire si les entités doivent être sérialisées et envoyées à un client, ce qui est courant pour les requêtes sans suivi.</span><span class="sxs-lookup"><span data-stu-id="929f3-324">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="929f3-325">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-325">**Mitigations**</span></span>

<span data-ttu-id="929f3-326">Utilisez une requête de suivi si la résolution de l’identité est requise.</span><span class="sxs-lookup"><span data-stu-id="929f3-326">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="929f3-327">~~L’exécution de requêtes est enregistrée au niveau du débogage~~ rétabli</span><span class="sxs-lookup"><span data-stu-id="929f3-327">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="929f3-328">Suivi de problème n°14523</span><span class="sxs-lookup"><span data-stu-id="929f3-328">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="929f3-329">Nous avons rétabli ce changement car la nouvelle configuration dans EF Core 3.0 permet la spécification du niveau d’enregistrement d’un événement par l’application.</span><span class="sxs-lookup"><span data-stu-id="929f3-329">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="929f3-330">Par exemple, pour basculer l’enregistrement de SQL vers `Debug`, configurez explicitement le niveau dans `OnConfiguring` ou `AddDbContext` :</span><span class="sxs-lookup"><span data-stu-id="929f3-330">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="929f3-331">Les valeurs de clés temporaires ne sont plus définies sur les instances d’entités</span><span class="sxs-lookup"><span data-stu-id="929f3-331">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="929f3-332">Suivi de problème n°12378</span><span class="sxs-lookup"><span data-stu-id="929f3-332">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="929f3-333">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-333">**Old behavior**</span></span>

<span data-ttu-id="929f3-334">Avant EF Core 3.0, des valeurs temporaires étaient affectées à toutes les propriétés de clé qui auraient plus tard une valeur réelle générée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="929f3-334">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="929f3-335">Généralement, ces valeurs temporaires étaient de grands nombres négatifs.</span><span class="sxs-lookup"><span data-stu-id="929f3-335">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="929f3-336">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-336">**New behavior**</span></span>

<span data-ttu-id="929f3-337">À compter de la version 3.0, EF Core stocke la valeur de clé temporaire dans le cadre des informations de suivi de l’entité, et laisse la propriété de clé proprement dite inchangée.</span><span class="sxs-lookup"><span data-stu-id="929f3-337">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="929f3-338">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-338">**Why**</span></span>

<span data-ttu-id="929f3-339">Ce changement a été apporté afin d’empêcher que les valeurs de clés temporaires ne deviennent à tort permanentes quand une entité qui a été suivie par une instance `DbContext` est déplacée vers une autre instance `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="929f3-339">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="929f3-340">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-340">**Mitigations**</span></span>

<span data-ttu-id="929f3-341">Les applications qui affectent des valeurs de clés primaires sur des clés étrangères afin de former des associations entre des entités peuvent dépendre de l’ancien comportement si les clés primaires sont générées par le magasin et appartiennent à des entités à l’état `Added`.</span><span class="sxs-lookup"><span data-stu-id="929f3-341">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="929f3-342">Vous pouvez éviter cela en :</span><span class="sxs-lookup"><span data-stu-id="929f3-342">This can be avoided by:</span></span>
* <span data-ttu-id="929f3-343">N’utilisant pas de clés générées par le magasin.</span><span class="sxs-lookup"><span data-stu-id="929f3-343">Not using store-generated keys.</span></span>
* <span data-ttu-id="929f3-344">Définissant des propriétés de navigation afin d’établir des relations au lieu de définir des valeurs de clés étrangères.</span><span class="sxs-lookup"><span data-stu-id="929f3-344">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="929f3-345">Obtenant les valeurs de clés temporaires réelles à partir des informations de suivi de l’entité.</span><span class="sxs-lookup"><span data-stu-id="929f3-345">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="929f3-346">Par exemple, `context.Entry(blog).Property(e => e.Id).CurrentValue` retournera la valeur temporaire même si la propriété `blog.Id` elle-même n’a pas été définie.</span><span class="sxs-lookup"><span data-stu-id="929f3-346">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="929f3-347">DetectChanges honore les valeurs de clés générées par le magasin</span><span class="sxs-lookup"><span data-stu-id="929f3-347">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="929f3-348">Suivi de problème n°14616</span><span class="sxs-lookup"><span data-stu-id="929f3-348">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="929f3-349">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-349">**Old behavior**</span></span>

<span data-ttu-id="929f3-350">Avant EF Core 3.0, une entité non suivie détectée par `DetectChanges` était suivie à l’état `Added` et insérée en tant que nouvelle ligne quand `SaveChanges` était appelée.</span><span class="sxs-lookup"><span data-stu-id="929f3-350">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="929f3-351">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-351">**New behavior**</span></span>

<span data-ttu-id="929f3-352">À compter d’EF Core 3.0, si une entité utilise des valeurs de clés générées et qu’une valeur de clé est définie, alors l’entité est suivie à l’état `Modified`.</span><span class="sxs-lookup"><span data-stu-id="929f3-352">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="929f3-353">Cela signifie qu’une ligne pour l’entité est supposée exister et qu’elle sera mise à jour lors de l’appel à `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="929f3-353">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="929f3-354">Si la valeur de clé n’est pas définie, ou si le type d’entité n’utilise pas de clés générées, alors la nouvelle entité sera quand même suivie comme `Added`, comme dans les versions précédentes.</span><span class="sxs-lookup"><span data-stu-id="929f3-354">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="929f3-355">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-355">**Why**</span></span>

<span data-ttu-id="929f3-356">Ce changement a été apporté afin de simplifier l’utilisation des graphes d’entités déconnectées lors de l’utilisation de clés générées par le magasin.</span><span class="sxs-lookup"><span data-stu-id="929f3-356">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="929f3-357">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-357">**Mitigations**</span></span>

<span data-ttu-id="929f3-358">Ce changement peut casser une application si un type d’entité est configuré pour utiliser des clés générés, mais que les valeurs de clés sont définies explicitement pour les nouvelles instances.</span><span class="sxs-lookup"><span data-stu-id="929f3-358">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="929f3-359">La solution consiste à configurer explicitement les propriétés de clés de façon à ne pas utiliser des valeurs générées.</span><span class="sxs-lookup"><span data-stu-id="929f3-359">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="929f3-360">Par exemple, avec l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="929f3-360">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="929f3-361">Ou avec des annotations de données :</span><span class="sxs-lookup"><span data-stu-id="929f3-361">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="929f3-362">Les suppressions en cascade se produisent désormais immédiatement par défaut</span><span class="sxs-lookup"><span data-stu-id="929f3-362">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="929f3-363">Suivi de problème n°10114</span><span class="sxs-lookup"><span data-stu-id="929f3-363">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="929f3-364">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-364">**Old behavior**</span></span>

<span data-ttu-id="929f3-365">Avant la version 3.0, les actions en cascade appliquées par EF Core (suppression d’entités dépendantes quand un principal requis est supprimé ou quand la relation à un principal requis est interrompue) ne se produisaient qu’une fois que SaveChanges était appelée.</span><span class="sxs-lookup"><span data-stu-id="929f3-365">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="929f3-366">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-366">**New behavior**</span></span>

<span data-ttu-id="929f3-367">À compter de la version 3.0, EF Core applique les actions en cascade dès que la condition de déclenchement est détectée.</span><span class="sxs-lookup"><span data-stu-id="929f3-367">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="929f3-368">Par exemple, si vous appelez `context.Remove()` pour supprimer une entité principale, toutes les dépendances requises suivies associées seront également définies immédiatement sur `Deleted`.</span><span class="sxs-lookup"><span data-stu-id="929f3-368">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="929f3-369">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-369">**Why**</span></span>

<span data-ttu-id="929f3-370">Cette modification a été apportée pour améliorer l’expérience des scénarios de liaison de données et d’audit dans lesquels il est important de comprendre les entités qui seront supprimées _avant_ l’appel de `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="929f3-370">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="929f3-371">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-371">**Mitigations**</span></span>

<span data-ttu-id="929f3-372">Vous pouvez restaurer le comportement précédent par le biais des paramètres sur `context.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="929f3-372">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="929f3-373">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-373">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="929f3-374">Le chargement hâtif des entités associées se produit désormais dans une seule requête</span><span class="sxs-lookup"><span data-stu-id="929f3-374">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="929f3-375">#18022 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="929f3-375">Tracking issue #18022</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

<span data-ttu-id="929f3-376">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-376">**Old behavior**</span></span>

<span data-ttu-id="929f3-377">Avant le 3,0, le chargement des navigations de collections à l’aide d’opérateurs `Include` entraînait la génération de plusieurs requêtes sur une base de données relationnelle, une pour chaque type d’entité associée.</span><span class="sxs-lookup"><span data-stu-id="929f3-377">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

<span data-ttu-id="929f3-378">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-378">**New behavior**</span></span>

<span data-ttu-id="929f3-379">À partir de 3,0, EF Core génère une requête unique avec des JOINTUREs sur des bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="929f3-379">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

<span data-ttu-id="929f3-380">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-380">**Why**</span></span>

<span data-ttu-id="929f3-381">L’émission de plusieurs requêtes pour implémenter une seule requête LINQ a entraîné de nombreux problèmes, notamment des performances négatives lorsque plusieurs allers-retours de base de données étaient nécessaires et des problèmes de cohérence des données à mesure que chaque requête pouvait observer un état différent de la base de données.</span><span class="sxs-lookup"><span data-stu-id="929f3-381">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

<span data-ttu-id="929f3-382">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-382">**Mitigations**</span></span>

<span data-ttu-id="929f3-383">Bien qu’techniquement, il ne s’agit pas d’une modification avec rupture, elle peut avoir un impact considérable sur les performances de l’application lorsqu’une seule requête contient un grand nombre d' `Include` opérateur dans les navigations de collection.</span><span class="sxs-lookup"><span data-stu-id="929f3-383">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="929f3-384">Pour plus d’informations et pour réécrire des requêtes de manière plus efficace, [consultez ce commentaire](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) .</span><span class="sxs-lookup"><span data-stu-id="929f3-384">[See this comment](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="929f3-385">La sémantique de DeleteBehavior.Restrict est désormais plus propre</span><span class="sxs-lookup"><span data-stu-id="929f3-385">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="929f3-386">Suivi de problème no 12661</span><span class="sxs-lookup"><span data-stu-id="929f3-386">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="929f3-387">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-387">**Old behavior**</span></span>

<span data-ttu-id="929f3-388">Avant la version 3.0, `DeleteBehavior.Restrict` créait les clés étrangères dans la base de données avec la sémantique `Restrict`, et il modifiait les corrections internes d’une manière non évidente.</span><span class="sxs-lookup"><span data-stu-id="929f3-388">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="929f3-389">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-389">**New behavior**</span></span>

<span data-ttu-id="929f3-390">À compter de la version 3.0, `DeleteBehavior.Restrict` garantit la création des clés étrangères avec la sémantique `Restrict` (autrement dit, sans levée de cascades lors d’une violation de contrainte), sans impact sur les corrections internes EF.</span><span class="sxs-lookup"><span data-stu-id="929f3-390">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="929f3-391">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-391">**Why**</span></span>

<span data-ttu-id="929f3-392">Ce changement a été apporté pour améliorer l’expérience et permettre une utilisation intuitive de `DeleteBehavior`, sans effets secondaires inattendus.</span><span class="sxs-lookup"><span data-stu-id="929f3-392">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="929f3-393">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-393">**Mitigations**</span></span>

<span data-ttu-id="929f3-394">Vous pouvez restaurer le comportement précédent par le biais de `DeleteBehavior.ClientNoAction`.</span><span class="sxs-lookup"><span data-stu-id="929f3-394">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="929f3-395">Les types de requêtes sont regroupés avec les types d’entités</span><span class="sxs-lookup"><span data-stu-id="929f3-395">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="929f3-396">Suivi de problème n°14194</span><span class="sxs-lookup"><span data-stu-id="929f3-396">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="929f3-397">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-397">**Old behavior**</span></span>

<span data-ttu-id="929f3-398">Avant EF Core 3.0, les [types de requêtes](xref:core/modeling/keyless-entity-types) étaient un moyen d’interroger des données qui ne définissait pas de clé primaire de façon structurée.</span><span class="sxs-lookup"><span data-stu-id="929f3-398">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="929f3-399">Autrement dit, on utilisait un type de requête pour mapper des types d’entités sans clés (le plus souvent à partir d’une vue, mais aussi éventuellement à partir d’une table), alors qu’on utilisait un type d’entité normal quand une clé était disponible (le plus souvent à partir d’une table, mais aussi éventuellement à partir d’une vue).</span><span class="sxs-lookup"><span data-stu-id="929f3-399">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="929f3-400">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-400">**New behavior**</span></span>

<span data-ttu-id="929f3-401">Un type de requête devient maintenant simplement un type d’entité sans clé primaire.</span><span class="sxs-lookup"><span data-stu-id="929f3-401">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="929f3-402">Les types d’entités sans clé ont les mêmes fonctionnalités que les types de requêtes dans les versions précédentes.</span><span class="sxs-lookup"><span data-stu-id="929f3-402">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="929f3-403">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-403">**Why**</span></span>

<span data-ttu-id="929f3-404">Ce changement a été apporté afin de réduire la confusion concernant l’objectif des types de requêtes.</span><span class="sxs-lookup"><span data-stu-id="929f3-404">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="929f3-405">Plus précisément, il s’agit de types d’entités sans clé et sont intrinsèquement en lecture seule pour cette raison, mais vous ne devez pas les utiliser au seul motif qu’un type d’entité doit être en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="929f3-405">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="929f3-406">De même, ils sont souvent mappés à des vues, mais c’est uniquement car les vues définissent rarement des clés.</span><span class="sxs-lookup"><span data-stu-id="929f3-406">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="929f3-407">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-407">**Mitigations**</span></span>

<span data-ttu-id="929f3-408">Les parties suivantes de l’API sont désormais obsolètes :</span><span class="sxs-lookup"><span data-stu-id="929f3-408">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="929f3-409">**`ModelBuilder.Query<>()`** - Au lieu de cela, vous devez appeler `ModelBuilder.Entity<>().HasNoKey()` pour marquer un type d’entité comme n’ayant pas de clé.</span><span class="sxs-lookup"><span data-stu-id="929f3-409">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="929f3-410">Cela ne serait toujours pas configuré par convention, afin d’éviter une configuration incorrecte quand une clé primaire est attendue mais ne correspond pas à la convention.</span><span class="sxs-lookup"><span data-stu-id="929f3-410">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="929f3-411">**`DbQuery<>`** - Utilisez plutôt `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="929f3-411">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="929f3-412">**`DbContext.Query<>()`** - Utilisez plutôt `DbContext.Set<>()`.</span><span class="sxs-lookup"><span data-stu-id="929f3-412">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="929f3-413">L’API de configuration pour les relations de type détenu a changé</span><span class="sxs-lookup"><span data-stu-id="929f3-413">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="929f3-414">[Suivi de problème n°12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Suivi de problème n°9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Suivi de problème n°14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="929f3-414">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="929f3-415">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-415">**Old behavior**</span></span>

<span data-ttu-id="929f3-416">Avant EF Core 3.0, la configuration de la relation détenue était effectuée directement après l’appel à `OwnsOne` ou `OwnsMany`.</span><span class="sxs-lookup"><span data-stu-id="929f3-416">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="929f3-417">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-417">**New behavior**</span></span>

<span data-ttu-id="929f3-418">À compter d’EF Core 3.0, il existe une API Fluent afin de configurer une propriété de navigation pour le propriétaire à l’aide de `WithOwner()`.</span><span class="sxs-lookup"><span data-stu-id="929f3-418">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="929f3-419">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-419">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="929f3-420">La configuration liée à la relation entre le propriétaire et le détenu doit maintenant être chaînée après `WithOwner()` tout comme les autres relations.</span><span class="sxs-lookup"><span data-stu-id="929f3-420">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="929f3-421">En revanche, la configuration du type détenu lui-même serait toujours chaînée après `OwnsOne()/OwnsMany()`.</span><span class="sxs-lookup"><span data-stu-id="929f3-421">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="929f3-422">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-422">For example:</span></span>

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

<span data-ttu-id="929f3-423">De plus, l’appel à `Entity()`, `HasOne()` ou `Set()` avec un type détenu comme cible lève désormais une exception.</span><span class="sxs-lookup"><span data-stu-id="929f3-423">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="929f3-424">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-424">**Why**</span></span>

<span data-ttu-id="929f3-425">Ce changement a été apporté afin de créer une distinction plus claire entre la configuration du type détenu lui-même et la _relation au type détenu_.</span><span class="sxs-lookup"><span data-stu-id="929f3-425">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="929f3-426">Cela lève ainsi toute ambiguïté et toute confusion autour des méthodes telles que `HasForeignKey`.</span><span class="sxs-lookup"><span data-stu-id="929f3-426">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="929f3-427">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-427">**Mitigations**</span></span>

<span data-ttu-id="929f3-428">Changez la configuration des relations de type détenu de façon à utiliser la nouvelle surface d’API, comme indiqué dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="929f3-428">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="929f3-429">Les entités dépendantes qui partagent la table avec le principal sont maintenant facultatives</span><span class="sxs-lookup"><span data-stu-id="929f3-429">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="929f3-430">Suivi du problème no 9005</span><span class="sxs-lookup"><span data-stu-id="929f3-430">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="929f3-431">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-431">**Old behavior**</span></span>

<span data-ttu-id="929f3-432">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="929f3-432">Consider the following model:</span></span>
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
<span data-ttu-id="929f3-433">Avant EF Core 3.0, si `OrderDetails` est détenu par `Order` ou explicitement mappé à la même table, une instance `OrderDetails` est toujours nécessaire pour ajouter un nouveau `Order`.</span><span class="sxs-lookup"><span data-stu-id="929f3-433">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="929f3-434">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-434">**New behavior**</span></span>

<span data-ttu-id="929f3-435">À partir de la version 3.0, EF Core permet d’ajouter un `Order` sans `OrderDetails` et mappe toutes les propriétés de `OrderDetails` à l’exception de la clé primaire aux colonnes de type nullable.</span><span class="sxs-lookup"><span data-stu-id="929f3-435">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="929f3-436">Pendant l’interrogation, EF Core définit `OrderDetails` sur `null` si une de ses propriétés obligatoires n’a pas de valeur, ou s’il n’a pas de propriété obligatoire en plus de la clé primaire et que toutes les propriétés sont `null`.</span><span class="sxs-lookup"><span data-stu-id="929f3-436">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="929f3-437">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-437">**Mitigations**</span></span>

<span data-ttu-id="929f3-438">Si votre modèle a une table qui partage des dépendances avec toutes les colonnes facultatives, mais que la navigation qui pointe vers lui n’est pas censée être `null`, l’application doit être modifiée pour gérer les situations où la navigation est `null`.</span><span class="sxs-lookup"><span data-stu-id="929f3-438">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="929f3-439">Si ce n’est pas possible, une propriété obligatoire doit être ajoutée au type d’entité ou au moins une propriété doit avoir une valeur non-`null`.</span><span class="sxs-lookup"><span data-stu-id="929f3-439">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="929f3-440">Toutes les entités qui partagent une table avec une colonne de jeton de concurrence doivent la mapper à une propriété</span><span class="sxs-lookup"><span data-stu-id="929f3-440">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="929f3-441">Suivi du problème no 14154</span><span class="sxs-lookup"><span data-stu-id="929f3-441">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="929f3-442">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-442">**Old behavior**</span></span>

<span data-ttu-id="929f3-443">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="929f3-443">Consider the following model:</span></span>
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
<span data-ttu-id="929f3-444">Avant EF Core 3.0, si `OrderDetails` est détenu par `Order` ou explicitement mappé à la même table, la mise à jour de `OrderDetails` seulement ne met pas à jour la valeur de `Version` sur le client et la prochaine mise à jour échoue.</span><span class="sxs-lookup"><span data-stu-id="929f3-444">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="929f3-445">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-445">**New behavior**</span></span>

<span data-ttu-id="929f3-446">À compter de la version 3.0, EF Core propage la nouvelle valeur `Version` sur `Order` s’il est propriétaire de `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="929f3-446">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="929f3-447">Sinon, une exception est levée pendant la validation de modèle.</span><span class="sxs-lookup"><span data-stu-id="929f3-447">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="929f3-448">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-448">**Why**</span></span>

<span data-ttu-id="929f3-449">Ce changement a été effectué pour éviter la péremption de la valeur du jeton de concurrence quand une seule des entités mappées à la même table est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="929f3-449">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="929f3-450">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-450">**Mitigations**</span></span>

<span data-ttu-id="929f3-451">Toutes les entités partageant la table doivent inclure une propriété mappée à la colonne de jeton de concurrence.</span><span class="sxs-lookup"><span data-stu-id="929f3-451">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="929f3-452">Vous pouvez en créer une dans un état fantôme :</span><span class="sxs-lookup"><span data-stu-id="929f3-452">It's possible the create one in shadow-state:</span></span>
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="929f3-453">Les entités détenues ne peuvent pas être interrogées sans le propriétaire à l’aide d’une requête de suivi</span><span class="sxs-lookup"><span data-stu-id="929f3-453">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="929f3-454">#18876 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="929f3-454">Tracking Issue #18876</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

<span data-ttu-id="929f3-455">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-455">**Old behavior**</span></span>

<span data-ttu-id="929f3-456">Avant le EF Core 3,0, les entités appartenant peuvent être interrogées comme n’importe quelle autre navigation.</span><span class="sxs-lookup"><span data-stu-id="929f3-456">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

<span data-ttu-id="929f3-457">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-457">**New behavior**</span></span>

<span data-ttu-id="929f3-458">À partir de 3,0, EF Core lève une exception si une requête de suivi projette une entité détenue sans le propriétaire.</span><span class="sxs-lookup"><span data-stu-id="929f3-458">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

<span data-ttu-id="929f3-459">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-459">**Why**</span></span>

<span data-ttu-id="929f3-460">Les entités détenues ne peuvent pas être manipulées sans le propriétaire, donc dans la grande majorité des cas, l’interrogation de cette façon est une erreur.</span><span class="sxs-lookup"><span data-stu-id="929f3-460">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

<span data-ttu-id="929f3-461">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-461">**Mitigations**</span></span>

<span data-ttu-id="929f3-462">Si l’entité possédée doit être suivie pour être modifiée de quelque façon que ce soit ultérieurement, le propriétaire doit être inclus dans la requête.</span><span class="sxs-lookup"><span data-stu-id="929f3-462">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="929f3-463">Sinon, ajoutez un appel `AsNoTracking()` :</span><span class="sxs-lookup"><span data-stu-id="929f3-463">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="929f3-464">Les propriétés héritées de types non mappés sont maintenant mappées à une seule colonne pour tous les types dérivés</span><span class="sxs-lookup"><span data-stu-id="929f3-464">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="929f3-465">Suivi du problème no 13998</span><span class="sxs-lookup"><span data-stu-id="929f3-465">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="929f3-466">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-466">**Old behavior**</span></span>

<span data-ttu-id="929f3-467">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="929f3-467">Consider the following model:</span></span>
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

<span data-ttu-id="929f3-468">Avant EF Core 3.0, la propriété `ShippingAddress` est mappée à des colonnes distinctes pour `BulkOrder` et `Order` par défaut.</span><span class="sxs-lookup"><span data-stu-id="929f3-468">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="929f3-469">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-469">**New behavior**</span></span>

<span data-ttu-id="929f3-470">À compter de la version 3.0, EF Core crée uniquement une colonne pour `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="929f3-470">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="929f3-471">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-471">**Why**</span></span>

<span data-ttu-id="929f3-472">L’ancien comportement n’était pas attendu.</span><span class="sxs-lookup"><span data-stu-id="929f3-472">The old behavoir was unexpected.</span></span>

<span data-ttu-id="929f3-473">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-473">**Mitigations**</span></span>

<span data-ttu-id="929f3-474">La propriété peut toujours être mappée explicitement à une colonne distincte sur les types dérivés :</span><span class="sxs-lookup"><span data-stu-id="929f3-474">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="929f3-475">La convention de propriété de clé étrangère ne correspond plus au nom de la propriété principale</span><span class="sxs-lookup"><span data-stu-id="929f3-475">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="929f3-476">Suivi de problème n°13274</span><span class="sxs-lookup"><span data-stu-id="929f3-476">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="929f3-477">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-477">**Old behavior**</span></span>

<span data-ttu-id="929f3-478">Considérez le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="929f3-478">Consider the following model:</span></span>
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
<span data-ttu-id="929f3-479">Avant EF Core 3.0, la propriété `CustomerId` était utilisée pour la clé étrangère par convention.</span><span class="sxs-lookup"><span data-stu-id="929f3-479">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="929f3-480">Toutefois, si `Order` est un type détenu, cela fait également de `CustomerId` la clé primaire, ce qui ne correspond généralement pas aux attentes.</span><span class="sxs-lookup"><span data-stu-id="929f3-480">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="929f3-481">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-481">**New behavior**</span></span>

<span data-ttu-id="929f3-482">À compter de la version 3.0, EF Core ne tente pas d’utiliser des propriétés pour les clés étrangères par convention si elles ont le même nom que la propriété principale.</span><span class="sxs-lookup"><span data-stu-id="929f3-482">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="929f3-483">Les modèles de nom du type de principal concaténé au nom de la propriété de principal, et de nom de navigation concaténé au nom de propriété de principal sont toujours mis en correspondance.</span><span class="sxs-lookup"><span data-stu-id="929f3-483">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="929f3-484">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-484">For example:</span></span>

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

<span data-ttu-id="929f3-485">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-485">**Why**</span></span>

<span data-ttu-id="929f3-486">Ce changement a été apporté afin d’éviter de définir de manière erronée une propriété de clé primaire sur le type détenu.</span><span class="sxs-lookup"><span data-stu-id="929f3-486">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="929f3-487">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-487">**Mitigations**</span></span>

<span data-ttu-id="929f3-488">Si la propriété était censée être la clé étrangère, et par conséquent une partie de la clé primaire, configurez-la explicitement comme telle.</span><span class="sxs-lookup"><span data-stu-id="929f3-488">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="929f3-489">La connexion de base de données est maintenant fermée si elle n’est plus utilisée avant la fin de TransactionScope</span><span class="sxs-lookup"><span data-stu-id="929f3-489">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="929f3-490">Suivi du problème no 14218</span><span class="sxs-lookup"><span data-stu-id="929f3-490">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="929f3-491">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-491">**Old behavior**</span></span>

<span data-ttu-id="929f3-492">Avant EF Core 3.0, si le contexte ouvre la connexion à l’intérieur d’un `TransactionScope`, la connexion reste ouverte quand le `TransactionScope` actuel est actif.</span><span class="sxs-lookup"><span data-stu-id="929f3-492">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

<span data-ttu-id="929f3-493">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-493">**New behavior**</span></span>

<span data-ttu-id="929f3-494">À compter de la version 3.0, EF Core ferme la connexion dès qu’il ne l’utilise plus.</span><span class="sxs-lookup"><span data-stu-id="929f3-494">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="929f3-495">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-495">**Why**</span></span>

<span data-ttu-id="929f3-496">Ce changement permet d’utiliser plusieurs contextes dans le même `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="929f3-496">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="929f3-497">Le nouveau comportement correspond également à EF6.</span><span class="sxs-lookup"><span data-stu-id="929f3-497">The new behavior also matches EF6.</span></span>

<span data-ttu-id="929f3-498">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-498">**Mitigations**</span></span>

<span data-ttu-id="929f3-499">Si la connexion doit rester ouverte, un appel explicite à `OpenConnection()` garantit qu’EF Core ne la ferme pas prématurément :</span><span class="sxs-lookup"><span data-stu-id="929f3-499">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="929f3-500">Chaque propriété utilise la génération indépendante de clé de type entier en mémoire</span><span class="sxs-lookup"><span data-stu-id="929f3-500">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="929f3-501">Suivi de problème n°6872</span><span class="sxs-lookup"><span data-stu-id="929f3-501">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="929f3-502">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-502">**Old behavior**</span></span>

<span data-ttu-id="929f3-503">Avant EF Core 3.0, un seul générateur de valeurs partagées était utilisé pour toutes les propriétés de clé de type entier en mémoire.</span><span class="sxs-lookup"><span data-stu-id="929f3-503">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="929f3-504">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-504">**New behavior**</span></span>

<span data-ttu-id="929f3-505">À compter d’EF Core 3.0, chaque propriété de clé de type entier obtient son propre générateur de valeur lors de l’utilisation de la base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="929f3-505">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="929f3-506">De plus, si la base de données est supprimée, la génération de clé est réinitialisée pour toutes les tables.</span><span class="sxs-lookup"><span data-stu-id="929f3-506">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="929f3-507">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-507">**Why**</span></span>

<span data-ttu-id="929f3-508">Ce changement a été apporté afin d’aligner plus étroitement la génération de clé en mémoire à la génération de clé de base de données réelle, et afin d’améliorer la capacité à isoler les tests les uns des autres lors de l’utilisation de la base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="929f3-508">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="929f3-509">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-509">**Mitigations**</span></span>

<span data-ttu-id="929f3-510">Ceci peut casser une application qui repose sur la définition de valeurs de clé en mémoire spécifiques.</span><span class="sxs-lookup"><span data-stu-id="929f3-510">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="929f3-511">Au lieu de cela, ne vous appuyez pas sur des valeurs de clés spécifiques, ou procédez à une mise à jour pour vous aligner au nouveau comportement.</span><span class="sxs-lookup"><span data-stu-id="929f3-511">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="929f3-512">Les champs de stockage sont utilisés par défaut</span><span class="sxs-lookup"><span data-stu-id="929f3-512">Backing fields are used by default</span></span>

[<span data-ttu-id="929f3-513">Suivi de problème n°12430</span><span class="sxs-lookup"><span data-stu-id="929f3-513">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="929f3-514">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-514">**Old behavior**</span></span>

<span data-ttu-id="929f3-515">Avant la version 3.0, même si le champ de stockage d’une propriété était connu, par défaut EF Core lisait et écrivait toujours la valeur de propriété à l’aide des méthodes get et set de la propriété.</span><span class="sxs-lookup"><span data-stu-id="929f3-515">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="929f3-516">L’exception à cette règle concernait l’exécution des requêtes, où le champ de stockage était défini directement s’il était connu.</span><span class="sxs-lookup"><span data-stu-id="929f3-516">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="929f3-517">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-517">**New behavior**</span></span>

<span data-ttu-id="929f3-518">Depuis EF Core 3.0, si le champ de stockage d’une propriété est connu, alors EF Core lit et écrit toujours cette propriété à l’aide du champ de stockage.</span><span class="sxs-lookup"><span data-stu-id="929f3-518">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="929f3-519">Cela peut casser une application si celle-ci repose sur un comportement supplémentaire codé dans les méthodes get ou set.</span><span class="sxs-lookup"><span data-stu-id="929f3-519">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="929f3-520">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-520">**Why**</span></span>

<span data-ttu-id="929f3-521">Ce changement a été apporté afin d’empêcher EF Core de déclencher par erreur une logique métier par défaut quand vous effectuez des opérations de base de données impliquant les entités.</span><span class="sxs-lookup"><span data-stu-id="929f3-521">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="929f3-522">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-522">**Mitigations**</span></span>

<span data-ttu-id="929f3-523">Vous pouvez restaurer le comportement antérieur à la version 3.0 en configurant le mode d’accès à la propriété sur `ModelBuilder`.</span><span class="sxs-lookup"><span data-stu-id="929f3-523">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="929f3-524">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-524">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="929f3-525">Erreur si plusieurs champs de stockage compatibles sont détectés</span><span class="sxs-lookup"><span data-stu-id="929f3-525">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="929f3-526">Suivi de problème n°12523</span><span class="sxs-lookup"><span data-stu-id="929f3-526">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="929f3-527">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-527">**Old behavior**</span></span>

<span data-ttu-id="929f3-528">Avant EF Core 3.0, si plusieurs champs respectaient les règles de recherche du champ de stockage d’une propriété, l’un d’entre eux était choisi selon un ordre de priorité.</span><span class="sxs-lookup"><span data-stu-id="929f3-528">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="929f3-529">Cela pouvait entraîner l’utilisation d’un champ incorrect en cas d’ambiguïté.</span><span class="sxs-lookup"><span data-stu-id="929f3-529">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="929f3-530">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-530">**New behavior**</span></span>

<span data-ttu-id="929f3-531">À compter d’EF Core 3.0, si plusieurs champs sont mis en correspondance avec la même propriété, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="929f3-531">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="929f3-532">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-532">**Why**</span></span>

<span data-ttu-id="929f3-533">Ce changement a été apporté afin d’éviter d’utiliser en mode silencieux un champ plutôt qu’un autre quand un seul peut être correct.</span><span class="sxs-lookup"><span data-stu-id="929f3-533">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="929f3-534">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-534">**Mitigations**</span></span>

<span data-ttu-id="929f3-535">Pour les propriétés comportant des champs de stockage ambigus, le champ à utiliser doit être spécifié explicitement.</span><span class="sxs-lookup"><span data-stu-id="929f3-535">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="929f3-536">Par exemple, à l’aide de l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="929f3-536">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="929f3-537">Les noms de propriété de type « champ uniquement » doivent correspondre au nom du champ</span><span class="sxs-lookup"><span data-stu-id="929f3-537">Field-only property names should match the field name</span></span>

<span data-ttu-id="929f3-538">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-538">**Old behavior**</span></span>

<span data-ttu-id="929f3-539">Avant le EF Core 3,0, une propriété pourrait être spécifiée par une valeur de chaîne et si aucune propriété portant ce nom n’a été trouvée sur le type .NET, EF Core essaiera de la faire correspondre à un champ à l’aide de règles de Convention.</span><span class="sxs-lookup"><span data-stu-id="929f3-539">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

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

<span data-ttu-id="929f3-540">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-540">**New behavior**</span></span>

<span data-ttu-id="929f3-541">À compter d’EF Core 3.0, une propriété de type « champ uniquement » doit correspondre exactement au nom du champ.</span><span class="sxs-lookup"><span data-stu-id="929f3-541">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="929f3-542">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-542">**Why**</span></span>

<span data-ttu-id="929f3-543">Ce changement a été apporté pour éviter d’utiliser un même champ pour deux propriétés portant un nom similaire. De plus, les règles de correspondance des propriétés de type « champ uniquement » sont désormais les mêmes que pour les propriétés mappées aux propriétés CLR.</span><span class="sxs-lookup"><span data-stu-id="929f3-543">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="929f3-544">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-544">**Mitigations**</span></span>

<span data-ttu-id="929f3-545">Les propriétés de type « champ uniquement » doivent porter le même nom que le champ auquel elles sont mappées.</span><span class="sxs-lookup"><span data-stu-id="929f3-545">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="929f3-546">Dans une version ultérieure de EF Core après 3,0, nous prévoyons de réactiver explicitement la configuration d’un nom de champ différent du nom de la propriété (voir le problème [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)) :</span><span class="sxs-lookup"><span data-stu-id="929f3-546">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="929f3-547">AddDbContext/AddDbContextPool n’appelle plus AddLogging et AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="929f3-547">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="929f3-548">Suivi de problème no 14756</span><span class="sxs-lookup"><span data-stu-id="929f3-548">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="929f3-549">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-549">**Old behavior**</span></span>

<span data-ttu-id="929f3-550">Avant le EF Core 3,0, l’appel de `AddDbContext` ou `AddDbContextPool` inscrirait également les services de journalisation et de mise en cache de la mémoire avec des appels à [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) et [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="929f3-550">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="929f3-551">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-551">**New behavior**</span></span>

<span data-ttu-id="929f3-552">À compter d’EF Core 3.0, `AddDbContext` et `AddDbContextPool` n’inscriront plus ces services auprès de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="929f3-552">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="929f3-553">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-553">**Why**</span></span>

<span data-ttu-id="929f3-554">Il n’est pas nécessaire pour EF Core 3.0 que ces services se trouvent dans le conteneur d’injection de dépendances de l’application.</span><span class="sxs-lookup"><span data-stu-id="929f3-554">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="929f3-555">Toutefois, `ILoggerFactory` est utilisé par EF Core même s’il est inscrit dans ce conteneur.</span><span class="sxs-lookup"><span data-stu-id="929f3-555">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="929f3-556">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-556">**Mitigations**</span></span>

<span data-ttu-id="929f3-557">Si votre application a besoin de ces services, inscrivez-les explicitement auprès du conteneur d’injection de dépendances avec [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) ou [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="929f3-557">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="929f3-558">AddEntityFramework \* ajoute IMemoryCache avec une limite de taille</span><span class="sxs-lookup"><span data-stu-id="929f3-558">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="929f3-559">#12905 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="929f3-559">Tracking Issue #12905</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

<span data-ttu-id="929f3-560">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-560">**Old behavior**</span></span>

<span data-ttu-id="929f3-561">Avant le EF Core 3,0, l’appel de `AddEntityFramework*` méthodes inscrirait également les services de mise en cache de mémoire avec DI sans limite de taille.</span><span class="sxs-lookup"><span data-stu-id="929f3-561">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

<span data-ttu-id="929f3-562">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-562">**New behavior**</span></span>

<span data-ttu-id="929f3-563">À compter de EF Core 3,0, `AddEntityFramework*` inscrit un service IMemoryCache avec une limite de taille.</span><span class="sxs-lookup"><span data-stu-id="929f3-563">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="929f3-564">Si d’autres services ajoutés par la suite dépendent de IMemoryCache, ils peuvent rapidement atteindre la limite par défaut provoquant des exceptions ou une dégradation des performances.</span><span class="sxs-lookup"><span data-stu-id="929f3-564">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

<span data-ttu-id="929f3-565">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-565">**Why**</span></span>

<span data-ttu-id="929f3-566">L’utilisation de IMemoryCache sans limite peut entraîner une utilisation incontrôlée de la mémoire s’il existe un bogue dans la logique de mise en cache des requêtes ou si les requêtes sont générées dynamiquement.</span><span class="sxs-lookup"><span data-stu-id="929f3-566">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="929f3-567">Le fait d’avoir une limite par défaut atténue une attaque potentielle par déni de compte.</span><span class="sxs-lookup"><span data-stu-id="929f3-567">Having a default limit mitigates a potential DoS attack.</span></span>

<span data-ttu-id="929f3-568">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-568">**Mitigations**</span></span>

<span data-ttu-id="929f3-569">Dans la plupart des cas, l’appel de `AddEntityFramework*` n’est pas nécessaire si `AddDbContext` ou `AddDbContextPool` est également appelé.</span><span class="sxs-lookup"><span data-stu-id="929f3-569">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="929f3-570">Par conséquent, la meilleure atténuation consiste à supprimer l’appel de `AddEntityFramework*`.</span><span class="sxs-lookup"><span data-stu-id="929f3-570">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="929f3-571">Si votre application a besoin de ces services, inscrivez une implémentation IMemoryCache explicitement avec le conteneur DI au préalable à l’aide de [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="929f3-571">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="929f3-572">DbContext.Entry effectue maintenant un DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="929f3-572">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="929f3-573">Suivi de problème n°13552</span><span class="sxs-lookup"><span data-stu-id="929f3-573">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="929f3-574">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-574">**Old behavior**</span></span>

<span data-ttu-id="929f3-575">Avant EF Core 3.0, le fait d’appeler `DbContext.Entry` provoquait la détection des changements pour toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="929f3-575">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="929f3-576">Cela garantissait que l’état exposé dans `EntityEntry` était à jour.</span><span class="sxs-lookup"><span data-stu-id="929f3-576">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="929f3-577">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-577">**New behavior**</span></span>

<span data-ttu-id="929f3-578">À compter d’EF Core 3.0, l’appel à `DbContext.Entry` tente uniquement de détecter les changements apportés dans l’entité donnée et dans toute entité principale suivie qui lui est associée.</span><span class="sxs-lookup"><span data-stu-id="929f3-578">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="929f3-579">Cela signifie que les changements apportés ailleurs peuvent ne pas avoir été détectés par l’appel à cette méthode, ce qui pourrait avoir des conséquences sur l’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="929f3-579">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="929f3-580">Notez que si `ChangeTracker.AutoDetectChangesEnabled` a la valeur `false`, même cette détection de changement locale sera désactivée.</span><span class="sxs-lookup"><span data-stu-id="929f3-580">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="929f3-581">Les autres méthodes qui provoquent une détection des changements (par exemple `ChangeTracker.Entries` et `SaveChanges`) entraînent toujours un `DetectChanges` complet de toutes les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="929f3-581">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="929f3-582">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-582">**Why**</span></span>

<span data-ttu-id="929f3-583">Ce changement a été apporté afin d’améliorer les performances par défaut de `context.Entry`.</span><span class="sxs-lookup"><span data-stu-id="929f3-583">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="929f3-584">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-584">**Mitigations**</span></span>

<span data-ttu-id="929f3-585">Appelez `ChgangeTracker.DetectChanges()` explicitement avant d’appeler `Entry` pour garantir le comportement antérieur à la version 3.0.</span><span class="sxs-lookup"><span data-stu-id="929f3-585">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="929f3-586">Les clés de tableaux d’octets et de chaînes ne sont pas générés par client par défaut</span><span class="sxs-lookup"><span data-stu-id="929f3-586">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="929f3-587">Suivi de problème n°14617</span><span class="sxs-lookup"><span data-stu-id="929f3-587">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="929f3-588">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-588">**Old behavior**</span></span>

<span data-ttu-id="929f3-589">Avant EF Core 3.0, les propriétés de clés `string` et `byte[]` pouvaient être utilisées sans définir explicitement de valeur non null.</span><span class="sxs-lookup"><span data-stu-id="929f3-589">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="929f3-590">Dans ce cas, la valeur de la clé était générée sur le client en tant que GUID, sérialisé en octets pour `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="929f3-590">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="929f3-591">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-591">**New behavior**</span></span>

<span data-ttu-id="929f3-592">À compter d’EF Core 3.0, une exception est levée pour signaler qu’aucune valeur de clé n’a été définie.</span><span class="sxs-lookup"><span data-stu-id="929f3-592">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="929f3-593">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-593">**Why**</span></span>

<span data-ttu-id="929f3-594">Ce changement a été apporté car les valeurs `string`/`byte[]` générées par le client ne sont généralement pas utiles, et le comportement par défaut rendait les valeurs de clés générés de manière courante difficiles à expliquer.</span><span class="sxs-lookup"><span data-stu-id="929f3-594">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="929f3-595">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-595">**Mitigations**</span></span>

<span data-ttu-id="929f3-596">Vos pouvez obtenir le comportement antérieur à la version 3.0 en spécifiant explicitement que les propriétés de clés doivent utiliser des valeurs générées si aucune autre valeur non nulle n’est définie.</span><span class="sxs-lookup"><span data-stu-id="929f3-596">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="929f3-597">Par exemple, avec l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="929f3-597">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="929f3-598">Ou avec des annotations de données :</span><span class="sxs-lookup"><span data-stu-id="929f3-598">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="929f3-599">ILoggerFactory est désormais un service délimité</span><span class="sxs-lookup"><span data-stu-id="929f3-599">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="929f3-600">Suivi de problème n°14698</span><span class="sxs-lookup"><span data-stu-id="929f3-600">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="929f3-601">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-601">**Old behavior**</span></span>

<span data-ttu-id="929f3-602">Avant EF Core 3.0, `ILoggerFactory` était inscrit en tant que service singleton.</span><span class="sxs-lookup"><span data-stu-id="929f3-602">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="929f3-603">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-603">**New behavior**</span></span>

<span data-ttu-id="929f3-604">À compter d’EF Core 3.0, `ILoggerFactory` est inscrit en tant que service délimité.</span><span class="sxs-lookup"><span data-stu-id="929f3-604">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="929f3-605">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-605">**Why**</span></span>

<span data-ttu-id="929f3-606">Ce changement a été apporté afin d’autoriser l’association d’un journaliseur avec une instance `DbContext`, ce qui active d’autres fonctionnalités et élimine certains cas de comportement pathologique tels que l’explosion des fournisseurs de services internes.</span><span class="sxs-lookup"><span data-stu-id="929f3-606">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="929f3-607">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-607">**Mitigations**</span></span>

<span data-ttu-id="929f3-608">Ce changement ne devrait pas avoir d’impact sur le code de l’application, sauf en cas d’inscription et d’utilisation de services personnalisés sur le fournisseur de service interne EF Core,</span><span class="sxs-lookup"><span data-stu-id="929f3-608">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="929f3-609">ce qui n’est pas courant.</span><span class="sxs-lookup"><span data-stu-id="929f3-609">This isn't common.</span></span>
<span data-ttu-id="929f3-610">Dans ces cas-là, la plupart des composants continueront à fonctionner, mais tout service singleton qui dépendait de `ILoggerFactory` devra être modifié pour obtenir le `ILoggerFactory` d’une manière différente.</span><span class="sxs-lookup"><span data-stu-id="929f3-610">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="929f3-611">Si vous faites face à ce genre de situation, veuillez soumettre un problème par le biais du [suivi des problèmes GitHub EF Core](https://github.com/aspnet/EntityFrameworkCore/issues) pour nous dire comment vous utilisez `ILoggerFactory`, afin que nous puissions mieux comprendre comment ne rien casser à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="929f3-611">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="929f3-612">Les proxys à chargement différé ne partent plus du principe que les propriétés de navigation sont entièrement chargées</span><span class="sxs-lookup"><span data-stu-id="929f3-612">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="929f3-613">Suivi de problème n°12780</span><span class="sxs-lookup"><span data-stu-id="929f3-613">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="929f3-614">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-614">**Old behavior**</span></span>

<span data-ttu-id="929f3-615">Avant EF Core 3.0, une fois qu’un `DbContext` était supprimé, il n’existait aucun moyen de savoir si une propriété de navigation donnée sur une entité obtenue à partir de ce contexte était entièrement chargée.</span><span class="sxs-lookup"><span data-stu-id="929f3-615">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="929f3-616">Les serveurs proxy partaient du principe qu’une navigation de référence était chargée si elle avait une valeur non null, et qu’une navigation de collection était chargée si elle n’était pas vide.</span><span class="sxs-lookup"><span data-stu-id="929f3-616">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="929f3-617">Dans ces cas-là, toute tentative de chargement différé constituait une no-op.</span><span class="sxs-lookup"><span data-stu-id="929f3-617">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="929f3-618">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-618">**New behavior**</span></span>

<span data-ttu-id="929f3-619">À compter d’EF Core 3.0, les proxys effectuent le suivi du chargement d’une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="929f3-619">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="929f3-620">Cela signifie qu’une tentative d’accès à une propriété de navigation qui est chargée une fois que le contexte a été supprimé sera toujours une no-op, même quand la navigation chargée est vide ou null.</span><span class="sxs-lookup"><span data-stu-id="929f3-620">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="929f3-621">À l’inverse, une tentative d’accès à une propriété de navigation qui n’est pas chargée lèvera une exception si le contexte est supprimé, même si la propriété de navigation est une collection non vide.</span><span class="sxs-lookup"><span data-stu-id="929f3-621">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="929f3-622">Si cette situation se présente, cela signifie que le code d’application tente d’utiliser le chargement différé à un moment non valide, et que l’application doit être modifiée afin de ne pas tenter cette opération.</span><span class="sxs-lookup"><span data-stu-id="929f3-622">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="929f3-623">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-623">**Why**</span></span>

<span data-ttu-id="929f3-624">Ce changement a été apporté afin de rendre le comportement cohérent et correct lors de la tentative de chargement différé sur une instance `DbContext` supprimée.</span><span class="sxs-lookup"><span data-stu-id="929f3-624">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="929f3-625">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-625">**Mitigations**</span></span>

<span data-ttu-id="929f3-626">Mettez à jour le code d’application pour qu’il ne tente pas d’effectuer un chargement différé avec un contexte supprimé, ou configurez cette opération pour qu’il s’agisse d’une no-op comme décrit dans le message d’exception.</span><span class="sxs-lookup"><span data-stu-id="929f3-626">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="929f3-627">La création excessive de fournisseurs de services internes est maintenant une erreur par défaut</span><span class="sxs-lookup"><span data-stu-id="929f3-627">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="929f3-628">Suivi de problème n°10236</span><span class="sxs-lookup"><span data-stu-id="929f3-628">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="929f3-629">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-629">**Old behavior**</span></span>

<span data-ttu-id="929f3-630">Avant EF Core 3.0, un avertissement était enregistré dans le journal quand une application créait une quantité pathologique de fournisseurs de services internes.</span><span class="sxs-lookup"><span data-stu-id="929f3-630">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="929f3-631">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-631">**New behavior**</span></span>

<span data-ttu-id="929f3-632">À compter d’EF Core 3.0, cet avertissement est désormais considéré comme une erreur, et une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="929f3-632">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="929f3-633">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-633">**Why**</span></span>

<span data-ttu-id="929f3-634">Ce changement a été apporté afin d’améliorer le code d’application avec une exposition plus explicite de ce cas pathologique.</span><span class="sxs-lookup"><span data-stu-id="929f3-634">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="929f3-635">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-635">**Mitigations**</span></span>

<span data-ttu-id="929f3-636">En présence de cette erreur, le mieux consiste à tenter de comprendre la cause racine, et de cesser de créer autant de fournisseurs de services internes.</span><span class="sxs-lookup"><span data-stu-id="929f3-636">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="929f3-637">Toutefois, vous pouvez reconvertir cette erreur en avertissement (ou l’ignorer) par le biais de la configuration sur le `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="929f3-637">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="929f3-638">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-638">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="929f3-639">Nouveau comportement de HasOne/HasMany appelé avec une chaîne unique</span><span class="sxs-lookup"><span data-stu-id="929f3-639">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="929f3-640">Suivi de problème no 9171</span><span class="sxs-lookup"><span data-stu-id="929f3-640">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="929f3-641">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-641">**Old behavior**</span></span>

<span data-ttu-id="929f3-642">Avant EF Core 3.0, la façon dont était interprété le code qui appelait `HasOne` ou `HasMany` avec une seule chaîne prêtait à confusion.</span><span class="sxs-lookup"><span data-stu-id="929f3-642">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="929f3-643">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-643">For example:</span></span>
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="929f3-644">Le code semble relier `Samurai` à un autre type d’entité avec la propriété de navigation `Entrance`, qui peut être privée.</span><span class="sxs-lookup"><span data-stu-id="929f3-644">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="929f3-645">En réalité, ce code tente de créer une relation avec un certain type d’entité nommé `Entrance` sans propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="929f3-645">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="929f3-646">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-646">**New behavior**</span></span>

<span data-ttu-id="929f3-647">À compter d’EF Core 3.0, le code ci-dessus effectue maintenant ce qu’il semblait devoir faire avant.</span><span class="sxs-lookup"><span data-stu-id="929f3-647">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="929f3-648">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-648">**Why**</span></span>

<span data-ttu-id="929f3-649">L’ancien comportement était très déroutant, en particulier pour qui lisait le code de configuration à la recherche d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="929f3-649">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="929f3-650">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-650">**Mitigations**</span></span>

<span data-ttu-id="929f3-651">Seules les applications qui configurent explicitement des relations en utilisant des chaînes comme noms de type sans spécifier explicitement la propriété de navigation sont concernées,</span><span class="sxs-lookup"><span data-stu-id="929f3-651">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="929f3-652">ce qui n’est pas courant.</span><span class="sxs-lookup"><span data-stu-id="929f3-652">This is not common.</span></span>
<span data-ttu-id="929f3-653">Pour revenir au comportement précédent, transmettez explicitement `null` comme nom de propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="929f3-653">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="929f3-654">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-654">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="929f3-655">Le type de retour Task pour plusieurs méthodes asynchrones a été remplacé par ValueTask</span><span class="sxs-lookup"><span data-stu-id="929f3-655">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="929f3-656">Suivi du problème no 15184</span><span class="sxs-lookup"><span data-stu-id="929f3-656">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="929f3-657">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-657">**Old behavior**</span></span>

<span data-ttu-id="929f3-658">Les méthodes asynchrones suivantes retournaient précédemment un `Task<T>` :</span><span class="sxs-lookup"><span data-stu-id="929f3-658">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="929f3-659">`ValueGenerator.NextValueAsync()` (et classes dérivées)</span><span class="sxs-lookup"><span data-stu-id="929f3-659">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="929f3-660">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-660">**New behavior**</span></span>

<span data-ttu-id="929f3-661">Les méthodes mentionnées précédemment retournent maintenant un `ValueTask<T>` sur le même `T` qu’avant.</span><span class="sxs-lookup"><span data-stu-id="929f3-661">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="929f3-662">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-662">**Why**</span></span>

<span data-ttu-id="929f3-663">Ce changement réduit le nombre d’allocations de tas induits par l’appel de ces méthodes, ce qui améliore les performances générales.</span><span class="sxs-lookup"><span data-stu-id="929f3-663">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="929f3-664">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-664">**Mitigations**</span></span>

<span data-ttu-id="929f3-665">Les applications qui sont simplement en attente des API ci-dessus doivent uniquement être recompilées, vous n’avez pas besoin de changer la source.</span><span class="sxs-lookup"><span data-stu-id="929f3-665">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="929f3-666">Une utilisation plus complexe (par exemple, le passage du `Task` retourné à `Task.WhenAny()`) nécessite généralement que le `ValueTask<T>` retourné soit converti en `Task<T>` en appelant `AsTask()` sur lui.</span><span class="sxs-lookup"><span data-stu-id="929f3-666">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="929f3-667">Notez que cette opération annule la réduction d’allocation apportée par ce changement.</span><span class="sxs-lookup"><span data-stu-id="929f3-667">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="929f3-668">L’annotation Relational:TypeMapping est désormais simplement TypeMapping</span><span class="sxs-lookup"><span data-stu-id="929f3-668">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="929f3-669">Suivi de problème n°9913</span><span class="sxs-lookup"><span data-stu-id="929f3-669">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="929f3-670">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-670">**Old behavior**</span></span>

<span data-ttu-id="929f3-671">Le nom d’annotation pour les annotations de mappage de types était « annotations ».</span><span class="sxs-lookup"><span data-stu-id="929f3-671">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="929f3-672">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-672">**New behavior**</span></span>

<span data-ttu-id="929f3-673">Le nom d’annotation pour les annotations de mappage de types est désormais « TypeMapping ».</span><span class="sxs-lookup"><span data-stu-id="929f3-673">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="929f3-674">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-674">**Why**</span></span>

<span data-ttu-id="929f3-675">Les mappages de types ne sont désormais plus utilisés uniquement pour les fournisseurs de bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="929f3-675">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="929f3-676">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-676">**Mitigations**</span></span>

<span data-ttu-id="929f3-677">Ce changement casse uniquement les applications qui accèdent au mappage de types directement en tant qu’annotation, ce qui n’est pas courant.</span><span class="sxs-lookup"><span data-stu-id="929f3-677">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="929f3-678">La meilleure solution consiste à utiliser la surface d’API pour accéder aux mappages de types, plutôt que d’utiliser directement l’annotation.</span><span class="sxs-lookup"><span data-stu-id="929f3-678">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="929f3-679">ToTable sur un type dérivé lève une exception</span><span class="sxs-lookup"><span data-stu-id="929f3-679">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="929f3-680">Suivi de problème n°11811</span><span class="sxs-lookup"><span data-stu-id="929f3-680">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="929f3-681">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-681">**Old behavior**</span></span>

<span data-ttu-id="929f3-682">Avant EF Core 3.0, l’appel à `ToTable()` sur un type dérivé était ignoré, car seule la stratégie de mappage d’héritage était TPH où cela n’est pas valide.</span><span class="sxs-lookup"><span data-stu-id="929f3-682">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="929f3-683">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-683">**New behavior**</span></span>

<span data-ttu-id="929f3-684">À compter d’EF Core 3.0, et en préparation à l’ajout de la prise en charge de TPT et TPC dans une version ultérieure, l’appel à `ToTable()` sur un type dérivé lève maintenant une exception afin d’éviter tout changement inattendu du mappage à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="929f3-684">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="929f3-685">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-685">**Why**</span></span>

<span data-ttu-id="929f3-686">Actuellement le mappage d’un type dérivé à une autre table n’est pas une opération valide.</span><span class="sxs-lookup"><span data-stu-id="929f3-686">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="929f3-687">Ce changement permettra d’éviter toute cassure ultérieure quand cette opération deviendra valide.</span><span class="sxs-lookup"><span data-stu-id="929f3-687">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="929f3-688">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-688">**Mitigations**</span></span>

<span data-ttu-id="929f3-689">Supprimez toutes les tentatives de mappage de types dérivés à d’autres tables.</span><span class="sxs-lookup"><span data-stu-id="929f3-689">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="929f3-690">ForSqlServerHasIndex est remplacé par HasIndex</span><span class="sxs-lookup"><span data-stu-id="929f3-690">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="929f3-691">Suivi de problème n°12366</span><span class="sxs-lookup"><span data-stu-id="929f3-691">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="929f3-692">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-692">**Old behavior**</span></span>

<span data-ttu-id="929f3-693">Avant EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` offrait un moyen de configurer des colonnes utilisées avec `INCLUDE`.</span><span class="sxs-lookup"><span data-stu-id="929f3-693">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="929f3-694">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-694">**New behavior**</span></span>

<span data-ttu-id="929f3-695">À compter d’EF Core 3.0, l’utilisation de `Include` sur un index est prise en charge au niveau relationnel.</span><span class="sxs-lookup"><span data-stu-id="929f3-695">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="929f3-696">Utilisez `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="929f3-696">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="929f3-697">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-697">**Why**</span></span>

<span data-ttu-id="929f3-698">Ce changement a été apporté afin de consolider l’API pour les index avec `Include` dans un seul emplacement pour tous les fournisseurs de bases de données.</span><span class="sxs-lookup"><span data-stu-id="929f3-698">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="929f3-699">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-699">**Mitigations**</span></span>

<span data-ttu-id="929f3-700">Utilisez la nouvelle API, comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="929f3-700">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="929f3-701">Changements apportés à l’API de métadonnées</span><span class="sxs-lookup"><span data-stu-id="929f3-701">Metadata API changes</span></span>

[<span data-ttu-id="929f3-702">Suivi du problème no 214</span><span class="sxs-lookup"><span data-stu-id="929f3-702">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="929f3-703">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-703">**New behavior**</span></span>

<span data-ttu-id="929f3-704">Les propriétés suivantes ont été converties en méthodes d’extension :</span><span class="sxs-lookup"><span data-stu-id="929f3-704">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="929f3-705">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-705">**Why**</span></span>

<span data-ttu-id="929f3-706">Ce changement simplifie l’implémentation des interfaces mentionnées précédemment.</span><span class="sxs-lookup"><span data-stu-id="929f3-706">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="929f3-707">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-707">**Mitigations**</span></span>

<span data-ttu-id="929f3-708">Utilisez les nouvelles méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="929f3-708">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="929f3-709">Modifications de l’API de métadonnées spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="929f3-709">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="929f3-710">Suivi du problème no 214</span><span class="sxs-lookup"><span data-stu-id="929f3-710">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="929f3-711">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-711">**New behavior**</span></span>

<span data-ttu-id="929f3-712">Les méthodes d’extension spécifiques au fournisseur seront aplaties :</span><span class="sxs-lookup"><span data-stu-id="929f3-712">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="929f3-713">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-713">**Why**</span></span>

<span data-ttu-id="929f3-714">Ce changement simplifie l’implémentation des méthodes d’extension mentionnées précédemment.</span><span class="sxs-lookup"><span data-stu-id="929f3-714">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="929f3-715">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-715">**Mitigations**</span></span>

<span data-ttu-id="929f3-716">Utilisez les nouvelles méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="929f3-716">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="929f3-717">EF Core n’envoie plus de pragma pour l’application de clé étrangère SQLite</span><span class="sxs-lookup"><span data-stu-id="929f3-717">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="929f3-718">Suivi de problème n°12151</span><span class="sxs-lookup"><span data-stu-id="929f3-718">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="929f3-719">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-719">**Old behavior**</span></span>

<span data-ttu-id="929f3-720">Avant EF Core 3.0, EF Core envoyait `PRAGMA foreign_keys = 1` quand une connexion à SQLite était ouverte.</span><span class="sxs-lookup"><span data-stu-id="929f3-720">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="929f3-721">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-721">**New behavior**</span></span>

<span data-ttu-id="929f3-722">À compter d’EF Core 3.0, EF Core n’envoie plus de `PRAGMA foreign_keys = 1` quand une connexion à SQLite est ouverte.</span><span class="sxs-lookup"><span data-stu-id="929f3-722">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="929f3-723">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-723">**Why**</span></span>

<span data-ttu-id="929f3-724">Ce changement a été apporté car EF Core utilise `SQLitePCLRaw.bundle_e_sqlite3` par défaut, ce qui signifie que l’application de clé étrangère est activée par défaut et n’a pas besoin d’être activée explicitement chaque fois qu’une connexion est ouverte.</span><span class="sxs-lookup"><span data-stu-id="929f3-724">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="929f3-725">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-725">**Mitigations**</span></span>

<span data-ttu-id="929f3-726">Les clés étrangères sont activées par défaut dans SQLitePCLRaw.bundle_e_sqlite3, qui est utilisé par défaut pour EF Core.</span><span class="sxs-lookup"><span data-stu-id="929f3-726">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="929f3-727">Pour les autres cas, vous pouvez activer les clés étrangères en spécifiant `Foreign Keys=True` dans votre chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="929f3-727">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="929f3-728">Microsoft.EntityFrameworkCore.Sqlite dépend désormais de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="929f3-728">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="929f3-729">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-729">**Old behavior**</span></span>

<span data-ttu-id="929f3-730">Avant EF Core 3.0, EF Core utilisait `SQLitePCLRaw.bundle_green`.</span><span class="sxs-lookup"><span data-stu-id="929f3-730">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="929f3-731">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-731">**New behavior**</span></span>

<span data-ttu-id="929f3-732">À compter d’EF Core 3.0, EF Core utilise `SQLitePCLRaw.bundle_e_sqlite3`.</span><span class="sxs-lookup"><span data-stu-id="929f3-732">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="929f3-733">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-733">**Why**</span></span>

<span data-ttu-id="929f3-734">Ce changement a été apporté afin que la version de SQLite utilisée sur iOS soit cohérente avec d’autres plateformes.</span><span class="sxs-lookup"><span data-stu-id="929f3-734">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="929f3-735">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-735">**Mitigations**</span></span>

<span data-ttu-id="929f3-736">Pour utiliser la version de SQLite native sur iOS, configurez `Microsoft.Data.Sqlite` de façon à utiliser un autre bundle `SQLitePCLRaw`.</span><span class="sxs-lookup"><span data-stu-id="929f3-736">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="929f3-737">Les valeurs GUID sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="929f3-737">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="929f3-738">Suivi de problème #15078</span><span class="sxs-lookup"><span data-stu-id="929f3-738">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="929f3-739">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-739">**Old behavior**</span></span>

<span data-ttu-id="929f3-740">Avant, les valeurs GUID étaient stockées comme valeurs BLOB sur SQLite.</span><span class="sxs-lookup"><span data-stu-id="929f3-740">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="929f3-741">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-741">**New behavior**</span></span>

<span data-ttu-id="929f3-742">Maintenant, les valeurs Guid sont stockées au format TEXT.</span><span class="sxs-lookup"><span data-stu-id="929f3-742">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="929f3-743">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-743">**Why**</span></span>

<span data-ttu-id="929f3-744">Le format binaire des valeurs GUID n’est pas normalisé.</span><span class="sxs-lookup"><span data-stu-id="929f3-744">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="929f3-745">Le stockage des valeurs au format TEXT rend la base de données plus compatible avec d’autres technologies.</span><span class="sxs-lookup"><span data-stu-id="929f3-745">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="929f3-746">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-746">**Mitigations**</span></span>

<span data-ttu-id="929f3-747">Vous pouvez migrer des bases de données existantes vers le nouveau format en exécutant SQL comme suit.</span><span class="sxs-lookup"><span data-stu-id="929f3-747">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="929f3-748">Dans EF Core, vous pouvez également continuer à utiliser le comportement précédent en configurant un convertisseur de valeur sur ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="929f3-748">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="929f3-749">Microsoft.Data.Sqlite peut toujours lire les valeurs GUID dans les colonnes BLOB et TEXT. Toutefois, en raison du changement du format par défaut pour les paramètres et les constantes, vous devrez probablement apporter des modifications dans la plupart des scénarios utilisant des valeurs GUID.</span><span class="sxs-lookup"><span data-stu-id="929f3-749">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="929f3-750">Les valeurs char sont maintenant stockées au format TEXT sur SQLite</span><span class="sxs-lookup"><span data-stu-id="929f3-750">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="929f3-751">Suivi de problème no 15020</span><span class="sxs-lookup"><span data-stu-id="929f3-751">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="929f3-752">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-752">**Old behavior**</span></span>

<span data-ttu-id="929f3-753">Avant, les valeurs char étaient stockées comme valeurs INTEGER sur SQLite.</span><span class="sxs-lookup"><span data-stu-id="929f3-753">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="929f3-754">Par exemple, une valeur char de *A* était stockée comme valeur entière 65.</span><span class="sxs-lookup"><span data-stu-id="929f3-754">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="929f3-755">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-755">**New behavior**</span></span>

<span data-ttu-id="929f3-756">Maintenant, les valeurs char sont stockées au format TEXT.</span><span class="sxs-lookup"><span data-stu-id="929f3-756">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="929f3-757">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-757">**Why**</span></span>

<span data-ttu-id="929f3-758">Le stockage des valeurs au format TEXT est plus naturel et rend la base de données plus compatible avec d’autres technologies.</span><span class="sxs-lookup"><span data-stu-id="929f3-758">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="929f3-759">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-759">**Mitigations**</span></span>

<span data-ttu-id="929f3-760">Vous pouvez migrer des bases de données existantes vers le nouveau format en exécutant SQL comme suit.</span><span class="sxs-lookup"><span data-stu-id="929f3-760">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="929f3-761">Dans EF Core, vous pouvez également continuer à utiliser le comportement précédent en configurant un convertisseur de valeur sur ces propriétés.</span><span class="sxs-lookup"><span data-stu-id="929f3-761">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="929f3-762">Microsoft.Data.Sqlite est aussi toujours capable de lire les valeurs de caractère à partir de colonnes INTEGER et TEXT, donc certains scénarios peuvent ne nécessiter aucune action.</span><span class="sxs-lookup"><span data-stu-id="929f3-762">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="929f3-763">Les ID de migration sont maintenant générés à l’aide du calendrier de la culture invariante</span><span class="sxs-lookup"><span data-stu-id="929f3-763">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="929f3-764">Suivi de problème no 12978</span><span class="sxs-lookup"><span data-stu-id="929f3-764">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="929f3-765">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-765">**Old behavior**</span></span>

<span data-ttu-id="929f3-766">Les ID de migration ont été générés par inadvertance à l’aide du calendrier de la culture actuelle.</span><span class="sxs-lookup"><span data-stu-id="929f3-766">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="929f3-767">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-767">**New behavior**</span></span>

<span data-ttu-id="929f3-768">Maintenant, les ID de migration sont toujours générés à l’aide du calendrier de la culture invariante (grégorien).</span><span class="sxs-lookup"><span data-stu-id="929f3-768">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="929f3-769">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-769">**Why**</span></span>

<span data-ttu-id="929f3-770">L’ordre des migrations est important lors de la mise à jour de la base de données ou de la résolution des conflits de fusion.</span><span class="sxs-lookup"><span data-stu-id="929f3-770">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="929f3-771">L’utilisation du calendrier invariant permet d’éviter les problèmes de classement qui peuvent se produire si les membres de l’équipe ont des calendriers système différents.</span><span class="sxs-lookup"><span data-stu-id="929f3-771">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="929f3-772">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-772">**Mitigations**</span></span>

<span data-ttu-id="929f3-773">Ce changement affecte tous ceux qui utilisent un calendrier non grégorien où l’année est en avance sur le calendrier grégorien (comme le calendrier bouddhiste thaïlandais).</span><span class="sxs-lookup"><span data-stu-id="929f3-773">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="929f3-774">Les ID de migration existants devront être mis à jour afin que les nouvelles migrations soient classées après les migrations existantes.</span><span class="sxs-lookup"><span data-stu-id="929f3-774">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="929f3-775">Vous trouverez les ID de migration dans l’attribut Migration des fichiers de concepteur des migrations.</span><span class="sxs-lookup"><span data-stu-id="929f3-775">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="929f3-776">Le tableau de l’historique Migrations doit également être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="929f3-776">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="929f3-777">UseRowNumberForPaging a été supprimé</span><span class="sxs-lookup"><span data-stu-id="929f3-777">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="929f3-778">Suivi de problème no 16400</span><span class="sxs-lookup"><span data-stu-id="929f3-778">Tracking Issue #16400</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

<span data-ttu-id="929f3-779">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-779">**Old behavior**</span></span>

<span data-ttu-id="929f3-780">Avant EF Core 3.0, `UseRowNumberForPaging` pouvait être utilisé pour générer SQL pour la pagination qui est compatible avec SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="929f3-780">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="929f3-781">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-781">**New behavior**</span></span>

<span data-ttu-id="929f3-782">À compter de EF Core 3.0, EF génère uniquement SQL pour la pagination qui est uniquement compatible avec les versions de SQL Server ultérieures.</span><span class="sxs-lookup"><span data-stu-id="929f3-782">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span> 

<span data-ttu-id="929f3-783">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-783">**Why**</span></span>

<span data-ttu-id="929f3-784">Nous effectuons cette modification, car [SQL Server 2008 n’est plus un produit pris en charge](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) et la mise à jour de cette fonctionnalité pour fonctionner avec les modifications de requête effectuées dans EF Core 3.0 est un travail significatif.</span><span class="sxs-lookup"><span data-stu-id="929f3-784">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="929f3-785">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-785">**Mitigations**</span></span>

<span data-ttu-id="929f3-786">Nous vous recommandons d’effectuer la mise à jour vers une version plus récente de SQL Server ou d’utiliser un niveau de compatibilité plus élevé, afin que le SQL généré soit pris en charge.</span><span class="sxs-lookup"><span data-stu-id="929f3-786">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="929f3-787">Cela dit, si vous ne pouvez pas faire cela, veuillez [commenter le problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/16400) en incluant des détails.</span><span class="sxs-lookup"><span data-stu-id="929f3-787">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="929f3-788">Nous pourrions revisiter cette décision en fonction des commentaires.</span><span class="sxs-lookup"><span data-stu-id="929f3-788">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="929f3-789">Les info/métadonnées d’extension ont été supprimées de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="929f3-789">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="929f3-790">Suivi du problème no 16119</span><span class="sxs-lookup"><span data-stu-id="929f3-790">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="929f3-791">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-791">**Old behavior**</span></span>

<span data-ttu-id="929f3-792">`IDbContextOptionsExtension` contenait des méthodes permettant de fournir des métadonnées relatives à l’extension.</span><span class="sxs-lookup"><span data-stu-id="929f3-792">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="929f3-793">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-793">**New behavior**</span></span>

<span data-ttu-id="929f3-794">Ces méthodes ont été déplacées vers une nouvelle classe de base abstraite `DbContextOptionsExtensionInfo`, qui est retournée à partir d’une nouvelle propriété `IDbContextOptionsExtension.Info`.</span><span class="sxs-lookup"><span data-stu-id="929f3-794">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="929f3-795">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-795">**Why**</span></span>

<span data-ttu-id="929f3-796">Sur les versions de 2.0 à 3.0, nous devions ajouter ou modifier ces méthodes plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="929f3-796">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="929f3-797">Les sortir dans une nouvelle classe de base abstraite simplifie l’apport de ces types de changements sans résiliation des extensions existantes.</span><span class="sxs-lookup"><span data-stu-id="929f3-797">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="929f3-798">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-798">**Mitigations**</span></span>

<span data-ttu-id="929f3-799">Mettre à jour des extensions pour suivre le nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="929f3-799">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="929f3-800">Des exemples se trouvent dans la plupart des implémentations de `IDbContextOptionsExtension` pour différents types d’extensions dans le code source EF Core.</span><span class="sxs-lookup"><span data-stu-id="929f3-800">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="929f3-801">LogQueryPossibleExceptionWithAggregateOperator a été renommé</span><span class="sxs-lookup"><span data-stu-id="929f3-801">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="929f3-802">Suivi de problème #10985</span><span class="sxs-lookup"><span data-stu-id="929f3-802">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="929f3-803">**Changement**</span><span class="sxs-lookup"><span data-stu-id="929f3-803">**Change**</span></span>

<span data-ttu-id="929f3-804">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` a été renommé en `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="929f3-804">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="929f3-805">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-805">**Why**</span></span>

<span data-ttu-id="929f3-806">Aligne le nom de cet événement d’avertissement avec tous les autres événements d’avertissement.</span><span class="sxs-lookup"><span data-stu-id="929f3-806">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="929f3-807">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-807">**Mitigations**</span></span>

<span data-ttu-id="929f3-808">Utilisez le nouveau nom.</span><span class="sxs-lookup"><span data-stu-id="929f3-808">Use the new name.</span></span> <span data-ttu-id="929f3-809">(Notez que le numéro d’ID événement n’a pas changé.)</span><span class="sxs-lookup"><span data-stu-id="929f3-809">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="929f3-810">Clarifier les noms de contrainte de clé étrangère dans l’API</span><span class="sxs-lookup"><span data-stu-id="929f3-810">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="929f3-811">Suivi de problème #10730</span><span class="sxs-lookup"><span data-stu-id="929f3-811">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="929f3-812">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-812">**Old behavior**</span></span>

<span data-ttu-id="929f3-813">Avant EF Core 3.0, les noms de contrainte de clé étrangère étaient désignés simplement par le terme « nom ».</span><span class="sxs-lookup"><span data-stu-id="929f3-813">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="929f3-814">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-814">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="929f3-815">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-815">**New behavior**</span></span>

<span data-ttu-id="929f3-816">À compter d’EF Core 3.0, les noms de contrainte de clé étrangère sont désignés par « noms de contrainte ».</span><span class="sxs-lookup"><span data-stu-id="929f3-816">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="929f3-817">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-817">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="929f3-818">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-818">**Why**</span></span>

<span data-ttu-id="929f3-819">Ce changement rend le nommage plus cohérent dans ce domaine. Il clarifie également le fait qu’il s’agit du nom de la contrainte de clé étrangère et pas du nom de la colonne ou de la propriété sur laquelle la clé étrangère est définie.</span><span class="sxs-lookup"><span data-stu-id="929f3-819">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="929f3-820">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-820">**Mitigations**</span></span>

<span data-ttu-id="929f3-821">Utilisez le nouveau nom.</span><span class="sxs-lookup"><span data-stu-id="929f3-821">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="929f3-822">IRelationalDatabaseCreator.HasTables/HasTablesAsync ont été rendues publiques</span><span class="sxs-lookup"><span data-stu-id="929f3-822">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="929f3-823">Suivi du problème no 15997</span><span class="sxs-lookup"><span data-stu-id="929f3-823">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="929f3-824">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-824">**Old behavior**</span></span>

<span data-ttu-id="929f3-825">Avant EF Core 3.0, ces méthodes étaient protégées.</span><span class="sxs-lookup"><span data-stu-id="929f3-825">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="929f3-826">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-826">**New behavior**</span></span>

<span data-ttu-id="929f3-827">À compter d’EF Core 3.0, ces méthodes sont publiques.</span><span class="sxs-lookup"><span data-stu-id="929f3-827">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="929f3-828">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-828">**Why**</span></span>

<span data-ttu-id="929f3-829">Ces méthodes sont utilisées par EF pour déterminer si une base de données est créée mais vide.</span><span class="sxs-lookup"><span data-stu-id="929f3-829">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="929f3-830">Cela peut également être utile depuis l’extérieur d’EF lorsque vous déterminez s’il faut appliquer des migrations ou pas.</span><span class="sxs-lookup"><span data-stu-id="929f3-830">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="929f3-831">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-831">**Mitigations**</span></span>

<span data-ttu-id="929f3-832">Modifiez l’accessibilité de n’importe quel remplacements.</span><span class="sxs-lookup"><span data-stu-id="929f3-832">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="929f3-833">Microsoft.EntityFrameworkCore.Design est désormais un package DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="929f3-833">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="929f3-834">Suivi du problème no 11506</span><span class="sxs-lookup"><span data-stu-id="929f3-834">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="929f3-835">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-835">**Old behavior**</span></span>

<span data-ttu-id="929f3-836">Avant EF Core 3.0, Microsoft.EntityFrameworkCore.Design était un package NuGet normal dont l’assembly pouvait être référencée par des projets qui en dépendaient.</span><span class="sxs-lookup"><span data-stu-id="929f3-836">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="929f3-837">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-837">**New behavior**</span></span>

<span data-ttu-id="929f3-838">À compter d’EF Core 3.0, il s’agit d’un package DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="929f3-838">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="929f3-839">Cela signifie que la dépendance ne sera pas transitive dans d’autres projets, et que vous ne pouvez plus, par défaut, référencer son assembly.</span><span class="sxs-lookup"><span data-stu-id="929f3-839">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="929f3-840">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-840">**Why**</span></span>

<span data-ttu-id="929f3-841">Ce package est uniquement destiné à être utilisé au moment du design.</span><span class="sxs-lookup"><span data-stu-id="929f3-841">This package is only intended to be used at design time.</span></span> <span data-ttu-id="929f3-842">Les applications déployées ne doivent pas y faire référence.</span><span class="sxs-lookup"><span data-stu-id="929f3-842">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="929f3-843">Le fait de faire de ce package un DevelopmentDependency renforce cette recommandation.</span><span class="sxs-lookup"><span data-stu-id="929f3-843">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="929f3-844">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-844">**Mitigations**</span></span>

<span data-ttu-id="929f3-845">Si vous avez besoin de référencer ce package pour remplacer le comportement de EF Core au moment de la conception, vous pouvez mettre à jour les métadonnées de l’élément PackageReference dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="929f3-845">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="929f3-846">Si le package est référencé de manière transitive via Microsoft.EntityFrameworkCore.Tools, vous devez ajouter un PackageReference explicite au package pour modifier ses métadonnées.</span><span class="sxs-lookup"><span data-stu-id="929f3-846">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="929f3-847">Une telle référence explicite doit être ajoutée à n’importe quel projet où les types du package sont nécessaires.</span><span class="sxs-lookup"><span data-stu-id="929f3-847">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="929f3-848">SQLitePCL.raw mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="929f3-848">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="929f3-849">Suivi du problème no 14824</span><span class="sxs-lookup"><span data-stu-id="929f3-849">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="929f3-850">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-850">**Old behavior**</span></span>

<span data-ttu-id="929f3-851">Microsoft.EntityFrameworkCore.Sqlite dépendait précédemment de la version 1.1.12 de SQLitePCL.raw.</span><span class="sxs-lookup"><span data-stu-id="929f3-851">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="929f3-852">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-852">**New behavior**</span></span>

<span data-ttu-id="929f3-853">Nous avons mis à jour notre package pour dépendre de la version 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="929f3-853">We've updated our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="929f3-854">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-854">**Why**</span></span>

<span data-ttu-id="929f3-855">La version 2.0.0 de SQLitePCL.raw cible .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="929f3-855">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="929f3-856">Elle ciblait précédemment .NET Standard 1.1 qui nécessitait une fermeture volumineuse de packages transitifs pour fonctionner.</span><span class="sxs-lookup"><span data-stu-id="929f3-856">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="929f3-857">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-857">**Mitigations**</span></span>

<span data-ttu-id="929f3-858">SQLitePCL.raw version 2.0.0 inclut des changements cassants.</span><span class="sxs-lookup"><span data-stu-id="929f3-858">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="929f3-859">Pour plus d’informations, consultez les [notes de publication](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md).</span><span class="sxs-lookup"><span data-stu-id="929f3-859">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="929f3-860">NetTopologySuite mis à jour vers la version 2.0.0</span><span class="sxs-lookup"><span data-stu-id="929f3-860">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="929f3-861">Suivi de problème no 14825</span><span class="sxs-lookup"><span data-stu-id="929f3-861">Tracking Issue #14825</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

<span data-ttu-id="929f3-862">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-862">**Old behavior**</span></span>

<span data-ttu-id="929f3-863">Les packages spatiaux dépendaient précédemment de la version 1.15.1 de NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="929f3-863">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="929f3-864">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-864">**New behavior**</span></span>

<span data-ttu-id="929f3-865">Nous avons mis à jour notre package pour dépendre de la version 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="929f3-865">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="929f3-866">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-866">**Why**</span></span>

<span data-ttu-id="929f3-867">La version 2.0.0 de NetTopologySuite vise à résoudre plusieurs problèmes de facilité d’utilisation rencontrés par les utilisateurs EF Core.</span><span class="sxs-lookup"><span data-stu-id="929f3-867">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="929f3-868">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-868">**Mitigations**</span></span>

<span data-ttu-id="929f3-869">NetTopologySuite version 2.0.0 inclut des changements cassants.</span><span class="sxs-lookup"><span data-stu-id="929f3-869">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="929f3-870">Pour plus d’informations, consultez les [notes de publication](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001).</span><span class="sxs-lookup"><span data-stu-id="929f3-870">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="929f3-871">Microsoft. Data. SqlClient est utilisé à la place de System. Data. SqlClient</span><span class="sxs-lookup"><span data-stu-id="929f3-871">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="929f3-872">#15636 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="929f3-872">Tracking Issue #15636</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

<span data-ttu-id="929f3-873">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-873">**Old behavior**</span></span>

<span data-ttu-id="929f3-874">Microsoft. EntityFrameworkCore. SqlServer était auparavant tributaire de System. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="929f3-874">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

<span data-ttu-id="929f3-875">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-875">**New behavior**</span></span>

<span data-ttu-id="929f3-876">Nous avons mis à jour notre package pour dépendre de Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="929f3-876">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="929f3-877">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-877">**Why**</span></span>

<span data-ttu-id="929f3-878">Microsoft. Data. SqlClient est le pilote d’accès aux données phare pour la SQL Server à l’avenir, et System. Data. SqlClient ne représente plus le point de vue du développement.</span><span class="sxs-lookup"><span data-stu-id="929f3-878">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="929f3-879">Certaines fonctionnalités importantes, telles que Always Encrypted, sont uniquement disponibles sur Microsoft. Data. SqlClient.</span><span class="sxs-lookup"><span data-stu-id="929f3-879">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="929f3-880">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-880">**Mitigations**</span></span>

<span data-ttu-id="929f3-881">Si votre code prend une dépendance directe sur System. Data. SqlClient, vous devez le modifier pour qu’il fasse référence à Microsoft. Data. SqlClient à la place. étant donné que les deux packages maintiennent un très haut niveau de compatibilité d’API, il ne doit y avoir qu’une simple modification de package et d’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="929f3-881">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="929f3-882">Plusieurs relations autoréférencées ambiguës doivent être configurées</span><span class="sxs-lookup"><span data-stu-id="929f3-882">Multiple ambiguous self-referencing relationships must be configured</span></span> 

[<span data-ttu-id="929f3-883">Suivi de problème no 13573</span><span class="sxs-lookup"><span data-stu-id="929f3-883">Tracking Issue #13573</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

<span data-ttu-id="929f3-884">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-884">**Old behavior**</span></span>

<span data-ttu-id="929f3-885">Un type d’entité avec plusieurs propriétés de navigation unidirectionnelle autoréférencées et les clés étrangères correspondantes a été incorrectement configuré en tant que relation unique.</span><span class="sxs-lookup"><span data-stu-id="929f3-885">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="929f3-886">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-886">For example:</span></span>

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

<span data-ttu-id="929f3-887">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-887">**New behavior**</span></span>

<span data-ttu-id="929f3-888">Ce scénario est maintenant détecté dans la génération de modèle et une exception est levée, indiquant que le modèle est ambigu.</span><span class="sxs-lookup"><span data-stu-id="929f3-888">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="929f3-889">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-889">**Why**</span></span>

<span data-ttu-id="929f3-890">Le modèle résultant est ambigu et probablement erroné dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="929f3-890">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="929f3-891">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-891">**Mitigations**</span></span>

<span data-ttu-id="929f3-892">Utilisez la configuration complète de la relation.</span><span class="sxs-lookup"><span data-stu-id="929f3-892">Use full configuration of the relationship.</span></span> <span data-ttu-id="929f3-893">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="929f3-893">For example:</span></span>

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
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="929f3-894">DbFunction. Schema étant null ou une chaîne vide, il est configuré pour être dans le schéma par défaut du modèle</span><span class="sxs-lookup"><span data-stu-id="929f3-894">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="929f3-895">#12757 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="929f3-895">Tracking Issue #12757</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

<span data-ttu-id="929f3-896">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-896">**Old behavior**</span></span>

<span data-ttu-id="929f3-897">Un DbFunction configuré avec un schéma sous forme de chaîne vide a été traité comme une fonction intégrée sans schéma.</span><span class="sxs-lookup"><span data-stu-id="929f3-897">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="929f3-898">Par exemple, le code suivant mappe `DatePart` fonction CLR à `DATEPART` fonction intégrée sur SqlServer.</span><span class="sxs-lookup"><span data-stu-id="929f3-898">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

<span data-ttu-id="929f3-899">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="929f3-899">**New behavior**</span></span>

<span data-ttu-id="929f3-900">Tous les mappages de DbFunction sont considérés comme mappés à des fonctions définies par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="929f3-900">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="929f3-901">Par conséquent, la valeur de chaîne vide placerait la fonction dans le schéma par défaut pour le modèle.</span><span class="sxs-lookup"><span data-stu-id="929f3-901">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="929f3-902">Ce peut être le schéma configuré explicitement via l’API Fluent `modelBuilder.HasDefaultSchema()` ou `dbo` dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="929f3-902">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

<span data-ttu-id="929f3-903">**Pourquoi ?**</span><span class="sxs-lookup"><span data-stu-id="929f3-903">**Why**</span></span>

<span data-ttu-id="929f3-904">Le schéma précédemment vide était un moyen de traiter que la fonction est intégrée, mais cette logique s’applique uniquement à SqlServer, où les fonctions intégrées n’appartiennent à aucun schéma.</span><span class="sxs-lookup"><span data-stu-id="929f3-904">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

<span data-ttu-id="929f3-905">**Atténuations**</span><span class="sxs-lookup"><span data-stu-id="929f3-905">**Mitigations**</span></span>

<span data-ttu-id="929f3-906">Configurez la traduction de DbFunction manuellement pour la mapper à une fonction intégrée.</span><span class="sxs-lookup"><span data-stu-id="929f3-906">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
