---
title: Nouveautés d’EF Core 1.0 - EF Core
description: Modifications et améliorations dans Entity Framework Core 1,0
author: divega
ms.date: 10/27/2016
ms.assetid: 20A25111-AEBE-4BC2-83A5-3F651952DF72
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: adde26095a11016796f6bd616978c3ccc0fe4c18
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618852"
---
# <a name="features-included-in-ef-core-10"></a><span data-ttu-id="5a654-103">Fonctionnalités incluses dans EF Core 1.0</span><span class="sxs-lookup"><span data-stu-id="5a654-103">Features included in EF Core 1.0</span></span>

## <a name="platforms"></a><span data-ttu-id="5a654-104">Plateformes</span><span class="sxs-lookup"><span data-stu-id="5a654-104">Platforms</span></span>

### <a name="net-framework-451"></a><span data-ttu-id="5a654-105">.NET Framework 4.5.1</span><span class="sxs-lookup"><span data-stu-id="5a654-105">.NET Framework 4.5.1</span></span>

<span data-ttu-id="5a654-106">Inclut la console, WPF, WinForms, ASP.NET 4, etc.</span><span class="sxs-lookup"><span data-stu-id="5a654-106">Includes Console, WPF, WinForms, ASP.NET 4, etc.</span></span>

### <a name="net-standard-13"></a><span data-ttu-id="5a654-107">.NET Standard 1.3</span><span class="sxs-lookup"><span data-stu-id="5a654-107">.NET Standard 1.3</span></span>

<span data-ttu-id="5a654-108">Inclut ASP.NET Core ciblant à la fois .NET Framework et .NET Core sur Windows, OSX et Linux.</span><span class="sxs-lookup"><span data-stu-id="5a654-108">Including ASP.NET Core targeting both .NET Framework and .NET Core on Windows, OSX, and Linux.</span></span>

## <a name="modelling"></a><span data-ttu-id="5a654-109">Modélisation</span><span class="sxs-lookup"><span data-stu-id="5a654-109">Modelling</span></span>

### <a name="basic-modelling"></a><span data-ttu-id="5a654-110">Modélisation de base</span><span class="sxs-lookup"><span data-stu-id="5a654-110">Basic modelling</span></span>

<span data-ttu-id="5a654-111">Basée sur les entités OCT avec des propriétés get/set de types scalaires communs (`int`, `string`, etc.).</span><span class="sxs-lookup"><span data-stu-id="5a654-111">Based on POCO entities with get/set properties of common scalar types (`int`, `string`, etc.).</span></span>

### <a name="relationships-and-navigation-properties"></a><span data-ttu-id="5a654-112">Relations et propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="5a654-112">Relationships and navigation properties</span></span>

<span data-ttu-id="5a654-113">Les relations un-à-plusieurs et un-à-zéro-ou un-à-un peuvent être spécifiées dans le modèle en fonction d’une clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="5a654-113">One-to-many and One-to-zero-or-one relationships can be specified in the model based on a foreign key.</span></span> <span data-ttu-id="5a654-114">Les propriétés de navigation de types de collection ou de référence simples peuvent être associées à ces relations.</span><span class="sxs-lookup"><span data-stu-id="5a654-114">Navigation properties of simple collection or reference types can be associated with these relationships.</span></span>

### <a name="built-in-conventions"></a><span data-ttu-id="5a654-115">Conventions intégrées</span><span class="sxs-lookup"><span data-stu-id="5a654-115">Built-in conventions</span></span>

<span data-ttu-id="5a654-116">Ces conventions génèrent un modèle initial basé sur la forme des classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="5a654-116">These build an initial model based on the shape of the entity classes.</span></span>

### <a name="fluent-api"></a><span data-ttu-id="5a654-117">API Fluent</span><span class="sxs-lookup"><span data-stu-id="5a654-117">Fluent API</span></span>

<span data-ttu-id="5a654-118">Permet de remplacer la méthode `OnModelCreating` sur votre contexte pour configurer davantage le modèle détecté par convention.</span><span class="sxs-lookup"><span data-stu-id="5a654-118">Allows you to override the `OnModelCreating` method on your context to further configure the model that was discovered by convention.</span></span>

### <a name="data-annotations"></a><span data-ttu-id="5a654-119">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="5a654-119">Data annotations</span></span>

<span data-ttu-id="5a654-120">Il s’agit d’attributs qui peuvent être ajoutés à vos propriétés/classes d’entité et qui influenceront le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="5a654-120">Are attributes that can be added to your entity classes/properties and will influence the EF model.</span></span> <span data-ttu-id="5a654-121">Par exemple, l’ajout de la mention `[Required]` informera EF qu’une propriété est obligatoire.</span><span class="sxs-lookup"><span data-stu-id="5a654-121">For example, adding `[Required]` will let EF know that a property is required.</span></span>

### <a name="relational-table-mapping"></a><span data-ttu-id="5a654-122">Mappage de tables relationnelles</span><span class="sxs-lookup"><span data-stu-id="5a654-122">Relational Table mapping</span></span>

<span data-ttu-id="5a654-123">Permet de mapper des entités à des tables ou des colonnes.</span><span class="sxs-lookup"><span data-stu-id="5a654-123">Allows entities to be mapped to tables/columns.</span></span>

### <a name="key-value-generation"></a><span data-ttu-id="5a654-124">Génération de valeur de clé</span><span class="sxs-lookup"><span data-stu-id="5a654-124">Key value generation</span></span>

<span data-ttu-id="5a654-125">Inclut la génération côté client et la génération de base de données.</span><span class="sxs-lookup"><span data-stu-id="5a654-125">Including client-side generation and database generation.</span></span>

### <a name="database-generated-values"></a><span data-ttu-id="5a654-126">Valeurs générées de base de données</span><span class="sxs-lookup"><span data-stu-id="5a654-126">Database generated values</span></span>

<span data-ttu-id="5a654-127">Permet à la base de données de générer des valeurs par insertion (valeurs par défaut) ou par mise à jour (colonnes calculées).</span><span class="sxs-lookup"><span data-stu-id="5a654-127">Allows for values to be generated by the database on insert (default values) or update (computed columns).</span></span>

### <a name="sequences-in-sql-server"></a><span data-ttu-id="5a654-128">Séquences dans SQL Server</span><span class="sxs-lookup"><span data-stu-id="5a654-128">Sequences in SQL Server</span></span>

<span data-ttu-id="5a654-129">Permet de définir des objets de la séquence dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="5a654-129">Allows for sequence objects to be defined in the model.</span></span>

### <a name="unique-constraints"></a><span data-ttu-id="5a654-130">Contraintes uniques</span><span class="sxs-lookup"><span data-stu-id="5a654-130">Unique constraints</span></span>

<span data-ttu-id="5a654-131">Permet de définir d’autres clés ainsi que les relations ciblant ces clés.</span><span class="sxs-lookup"><span data-stu-id="5a654-131">Allows for the definition of alternate keys and the ability to define relationships that target that key.</span></span>

### <a name="indexes"></a><span data-ttu-id="5a654-132">Index</span><span class="sxs-lookup"><span data-stu-id="5a654-132">Indexes</span></span>

<span data-ttu-id="5a654-133">La définition d’index dans le modèle introduit automatiquement les index dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5a654-133">Defining indexes in the model automatically introduces indexes in the database.</span></span> <span data-ttu-id="5a654-134">Les index uniques sont également pris en charge.</span><span class="sxs-lookup"><span data-stu-id="5a654-134">Unique indexes are also supported.</span></span>

### <a name="shadow-state-properties"></a><span data-ttu-id="5a654-135">Propriétés d’état de clichés instantanés</span><span class="sxs-lookup"><span data-stu-id="5a654-135">Shadow state properties</span></span>

<span data-ttu-id="5a654-136">Permet de définir dans le modèle des propriétés qui ne sont pas déclarées ni stockées dans la classe .NET, mais qui peuvent être suivies et mises à jour par EF Core.</span><span class="sxs-lookup"><span data-stu-id="5a654-136">Allows for properties to be defined in the model that are not declared and are not stored in the .NET class but can be tracked and updated by EF Core.</span></span> <span data-ttu-id="5a654-137">Elles sont couramment utilisées pour les propriétés de clé étrangère quand l’exposition de ces dernières dans l’objet n’est pas souhaitée.</span><span class="sxs-lookup"><span data-stu-id="5a654-137">Commonly used for foreign key properties when exposing these in the object is not desired.</span></span>

### <a name="table-per-hierarchy-inheritance-pattern"></a><span data-ttu-id="5a654-138">Modèle d'héritage de table par hiérarchie</span><span class="sxs-lookup"><span data-stu-id="5a654-138">Table-Per-Hierarchy inheritance pattern</span></span>

<span data-ttu-id="5a654-139">Permet d’enregistrer les entités d’une hiérarchie d’héritage dans une table unique à l’aide d’une colonne de discriminateur pour identifier le type d’entité pour un enregistrement donné dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5a654-139">Allows entities in an inheritance hierarchy to be saved to a single table using a discriminator column to identify they entity type for a given record in the database.</span></span>

### <a name="model-validation"></a><span data-ttu-id="5a654-140">Validation du modèle</span><span class="sxs-lookup"><span data-stu-id="5a654-140">Model validation</span></span>

<span data-ttu-id="5a654-141">Détecte les modèles non valides dans le modèle et fournit des messages d’erreur utiles.</span><span class="sxs-lookup"><span data-stu-id="5a654-141">Detects invalid patterns in the model and provides helpful error messages.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="5a654-142">Suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="5a654-142">Change tracking</span></span>

### <a name="snapshot-change-tracking"></a><span data-ttu-id="5a654-143">Suivi des modifications par instantané</span><span class="sxs-lookup"><span data-stu-id="5a654-143">Snapshot change tracking</span></span>

<span data-ttu-id="5a654-144">Permet de détecter automatiquement les modifications apportées aux entités en comparant l’état actuel avec une copie (instantané) de l’état d’origine.</span><span class="sxs-lookup"><span data-stu-id="5a654-144">Allows changes in entities to be detected automatically by comparing current state against a copy (snapshot) of the original state.</span></span>

### <a name="notification-change-tracking"></a><span data-ttu-id="5a654-145">Suivi des modifications par notification</span><span class="sxs-lookup"><span data-stu-id="5a654-145">Notification change tracking</span></span>

<span data-ttu-id="5a654-146">Permet aux entités d’avertir le traceur de modifications dès que des valeurs de propriété sont modifiées.</span><span class="sxs-lookup"><span data-stu-id="5a654-146">Allows your entities to notify the change tracker when property values are modified.</span></span>

### <a name="accessing-tracked-state"></a><span data-ttu-id="5a654-147">État du suivi de l’accès</span><span class="sxs-lookup"><span data-stu-id="5a654-147">Accessing tracked state</span></span>

<span data-ttu-id="5a654-148">Via `DbContext.Entry` et `DbContext.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="5a654-148">Via `DbContext.Entry` and `DbContext.ChangeTracker`.</span></span>

### <a name="attaching-detached-entitiesgraphs"></a><span data-ttu-id="5a654-149">Attachement d’entités/graphes détachés</span><span class="sxs-lookup"><span data-stu-id="5a654-149">Attaching detached entities/graphs</span></span>

<span data-ttu-id="5a654-150">La nouvelle API `DbContext.AttachGraph` permet de rattacher des entités à un contexte pour pouvoir enregistrer des entités modifiées ou de nouvelles entités.</span><span class="sxs-lookup"><span data-stu-id="5a654-150">The new `DbContext.AttachGraph` API helps re-attach entities to a context in order to save new/modified entities.</span></span>

## <a name="saving-data"></a><span data-ttu-id="5a654-151">Enregistrement des données</span><span class="sxs-lookup"><span data-stu-id="5a654-151">Saving data</span></span>

### <a name="basic-save-functionality"></a><span data-ttu-id="5a654-152">Fonctionnalité d’enregistrement de base</span><span class="sxs-lookup"><span data-stu-id="5a654-152">Basic save functionality</span></span>

<span data-ttu-id="5a654-153">Permet de conserver les modifications apportées aux instances dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5a654-153">Allows changes to entity instances to be persisted to the database.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="5a654-154">Accès concurrentiel optimiste</span><span class="sxs-lookup"><span data-stu-id="5a654-154">Optimistic Concurrency</span></span>

<span data-ttu-id="5a654-155">Évite le remplacement de modifications apportées par un autre utilisateur, sachant que les données ont été extraites de la base de données.</span><span class="sxs-lookup"><span data-stu-id="5a654-155">Protects against overwriting changes made by another user since data was fetched from the database.</span></span>

### <a name="async-savechanges"></a><span data-ttu-id="5a654-156">SaveChanges asynchrone</span><span class="sxs-lookup"><span data-stu-id="5a654-156">Async SaveChanges</span></span>

<span data-ttu-id="5a654-157">Peut libérer le thread actuel pour traiter d’autres requêtes pendant que la base de données traite les commandes émises à partir de `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="5a654-157">Can free up the current thread to process other requests while the database processes the commands issued from `SaveChanges`.</span></span>

### <a name="database-transactions"></a><span data-ttu-id="5a654-158">Transactions de base de données</span><span class="sxs-lookup"><span data-stu-id="5a654-158">Database Transactions</span></span>

<span data-ttu-id="5a654-159">Signifie que `SaveChanges` est toujours atomique (en d’autres termes, soit sa réussite est complète, soit aucune modification n’est apportée à la base de données).</span><span class="sxs-lookup"><span data-stu-id="5a654-159">Means that `SaveChanges` is always atomic (meaning it either completely succeeds, or no changes are made to the database).</span></span> <span data-ttu-id="5a654-160">Il existe également des API liées aux transactions pour autoriser le partage de transactions entre des instances de contexte, etc.</span><span class="sxs-lookup"><span data-stu-id="5a654-160">There are also transaction related APIs to allow sharing transactions between context instances etc.</span></span>

### <a name="relational-batching-of-statements"></a><span data-ttu-id="5a654-161">Relationnel : traitement par lot d’instructions</span><span class="sxs-lookup"><span data-stu-id="5a654-161">Relational: Batching of statements</span></span>

<span data-ttu-id="5a654-162">Offre de meilleures performances en regroupant les différentes commandes INSERT/UPDATE/DELETE dans une seule boucle pour la base de données.</span><span class="sxs-lookup"><span data-stu-id="5a654-162">Provides better performance by batching up multiple INSERT/UPDATE/DELETE commands into a single roundtrip to the database.</span></span>

## <a name="query"></a><span data-ttu-id="5a654-163">Requête</span><span class="sxs-lookup"><span data-stu-id="5a654-163">Query</span></span>

### <a name="basic-linq-support"></a><span data-ttu-id="5a654-164">Prise en charge de base de LINQ</span><span class="sxs-lookup"><span data-stu-id="5a654-164">Basic LINQ support</span></span>

<span data-ttu-id="5a654-165">Offre la possibilité d’utiliser LINQ pour récupérer des données à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="5a654-165">Provides the ability to use LINQ to retrieve data from the database.</span></span>

### <a name="mixed-clientserver-evaluation"></a><span data-ttu-id="5a654-166">Évaluation du client/serveur mixte</span><span class="sxs-lookup"><span data-stu-id="5a654-166">Mixed client/server evaluation</span></span>

<span data-ttu-id="5a654-167">Permet aux requêtes de contenir la logique qui ne peut pas être évaluée dans la base de données et qui doit par conséquent être évaluée une fois les données récupérées dans la mémoire.</span><span class="sxs-lookup"><span data-stu-id="5a654-167">Enables queries to contain logic that cannot be evaluated in the database, and must therefore be evaluated after the data is retrieved into memory.</span></span>

### <a name="notracking"></a><span data-ttu-id="5a654-168">NoTracking</span><span class="sxs-lookup"><span data-stu-id="5a654-168">NoTracking</span></span>

<span data-ttu-id="5a654-169">Permet d’accélérer l’exécution des requêtes quand le contexte n’a pas besoin de surveiller les changements apportés aux instances d’entité (cela s’avère utile si les résultats en lecture seule).</span><span class="sxs-lookup"><span data-stu-id="5a654-169">Queries enables quicker query execution when the context does not need to monitor for changes to the entity instances (this is useful if the results are read-only).</span></span>

### <a name="eager-loading"></a><span data-ttu-id="5a654-170">Chargement hâtif</span><span class="sxs-lookup"><span data-stu-id="5a654-170">Eager loading</span></span>

<span data-ttu-id="5a654-171">Fournit les méthodes `Include` et `ThenInclude` pour identifier les données associées qui doivent également être extraites durant l’interrogation.</span><span class="sxs-lookup"><span data-stu-id="5a654-171">Provides the `Include` and `ThenInclude` methods to identify related data that should also be fetched when querying.</span></span>

### <a name="async-query"></a><span data-ttu-id="5a654-172">Requête asynchrone</span><span class="sxs-lookup"><span data-stu-id="5a654-172">Async query</span></span>

<span data-ttu-id="5a654-173">Peut libérer le thread actuel (et ses ressources associées) pour traiter d’autres requêtes pendant que la base de données traite la requête.</span><span class="sxs-lookup"><span data-stu-id="5a654-173">Can free up the current thread (and it's associated resources) to process other requests while the database processes the query.</span></span>

### <a name="raw-sql-queries"></a><span data-ttu-id="5a654-174">Requêtes SQL brutes</span><span class="sxs-lookup"><span data-stu-id="5a654-174">Raw SQL queries</span></span>

<span data-ttu-id="5a654-175">Fournit la méthode `DbSet.FromSql` pour utiliser des requêtes SQL brutes pour extraire des données.</span><span class="sxs-lookup"><span data-stu-id="5a654-175">Provides the `DbSet.FromSql` method to use raw SQL queries to fetch data.</span></span> <span data-ttu-id="5a654-176">Ces requêtes peuvent également être composées à l’aide de LINQ.</span><span class="sxs-lookup"><span data-stu-id="5a654-176">These queries can also be composed on using LINQ.</span></span>

## <a name="database-schema-management"></a><span data-ttu-id="5a654-177">Gestion du schéma de base de données</span><span class="sxs-lookup"><span data-stu-id="5a654-177">Database schema management</span></span>

### <a name="database-creationdeletion-apis"></a><span data-ttu-id="5a654-178">API de création/suppression de base de données</span><span class="sxs-lookup"><span data-stu-id="5a654-178">Database creation/deletion APIs</span></span>

<span data-ttu-id="5a654-179">Elles sont principalement conçues tester l’emplacement où vous souhaitez créer/supprimer rapidement la base de données sans utiliser de migrations.</span><span class="sxs-lookup"><span data-stu-id="5a654-179">Are mostly designed for testing where you want to quickly create/delete the database without using migrations.</span></span>

### <a name="relational-database-migrations"></a><span data-ttu-id="5a654-180">Migrations de base de données relationnelle</span><span class="sxs-lookup"><span data-stu-id="5a654-180">Relational database migrations</span></span>

<span data-ttu-id="5a654-181">Permet à un schéma de base de données relationnelle d’évoluer à travers le temps au fur et à mesure que votre modèle change.</span><span class="sxs-lookup"><span data-stu-id="5a654-181">Allow a relational database schema to evolve overtime as your model changes.</span></span>

### <a name="reverse-engineer-from-database"></a><span data-ttu-id="5a654-182">Ingénierie à rebours à partir de la base de données</span><span class="sxs-lookup"><span data-stu-id="5a654-182">Reverse engineer from database</span></span>

<span data-ttu-id="5a654-183">Permet de générer automatiquement un modèle EF basé sur un schéma de base de données relationnelle existante.</span><span class="sxs-lookup"><span data-stu-id="5a654-183">Scaffolds an EF model based on an existing relational database schema.</span></span>

## <a name="database-providers"></a><span data-ttu-id="5a654-184">Fournisseurs de bases de données</span><span class="sxs-lookup"><span data-stu-id="5a654-184">Database providers</span></span>

### <a name="sql-server"></a><span data-ttu-id="5a654-185">SQL Server</span><span class="sxs-lookup"><span data-stu-id="5a654-185">SQL Server</span></span>

<span data-ttu-id="5a654-186">Se connecte à Microsoft SQL Server 2008 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="5a654-186">Connects to Microsoft SQL Server 2008 onwards.</span></span>

### <a name="sqlite"></a><span data-ttu-id="5a654-187">SQLite</span><span class="sxs-lookup"><span data-stu-id="5a654-187">SQLite</span></span>

<span data-ttu-id="5a654-188">Se connecte à une base de données SQLite 3.</span><span class="sxs-lookup"><span data-stu-id="5a654-188">Connects to a SQLite 3 database.</span></span>

### <a name="in-memory"></a><span data-ttu-id="5a654-189">En mémoire</span><span class="sxs-lookup"><span data-stu-id="5a654-189">In-Memory</span></span>

<span data-ttu-id="5a654-190">Fonctionnalité conçue pour tester facilement sans vous connecter à une base de données réelle.</span><span class="sxs-lookup"><span data-stu-id="5a654-190">Is designed to easily enable testing without connecting to a real database.</span></span>

### <a name="3rd-party-providers"></a><span data-ttu-id="5a654-191">Fournisseurs tiers</span><span class="sxs-lookup"><span data-stu-id="5a654-191">3rd party providers</span></span>

<span data-ttu-id="5a654-192">Plusieurs fournisseurs sont disponibles pour d’autres moteurs de base de données.</span><span class="sxs-lookup"><span data-stu-id="5a654-192">Several providers are available for other database engines.</span></span> <span data-ttu-id="5a654-193">Consultez [Fournisseurs de bases de données](xref:core/providers/index) pour en obtenir la liste complète.</span><span class="sxs-lookup"><span data-stu-id="5a654-193">See [Database Providers](xref:core/providers/index) for a complete list.</span></span>
