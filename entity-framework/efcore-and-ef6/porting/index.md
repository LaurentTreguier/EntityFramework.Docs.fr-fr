---
title: Portage depuis EF6 vers EF Core - EF
description: Informations générales sur le portage d’une application d’Entity Framework 6 à Entity Framework Core.
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: d50def47e65455c8cf5242cad4386f157148c0bc
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429206"
---
# <a name="porting-from-ef6-to-ef-core"></a><span data-ttu-id="fc1b9-103">Portage depuis EF6 vers EF Core</span><span class="sxs-lookup"><span data-stu-id="fc1b9-103">Porting from EF6 to EF Core</span></span>

<span data-ttu-id="fc1b9-104">En raison des modifications importantes apportées à EF Core, nous vous déconseillons de migrer une application EF6 vers EF Core, à moins d’avoir une raison justifiant réellement ce changement.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-104">Because of the fundamental changes in EF Core we do not recommend attempting to move an EF6 application to EF Core unless you have a compelling reason to make the change.</span></span>
<span data-ttu-id="fc1b9-105">Il est préférable de considérer la migration d’EF6 vers EF Core comme un portage plutôt qu’une mise à niveau.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-105">You should view the move from EF6 to EF Core as a port rather than an upgrade.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fc1b9-106">Avant de commencer le processus de portage, il est important de vérifier qu’EF Core répond aux exigences d’accès aux données de votre application.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-106">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="fc1b9-107">Fonctionnalités manquantes</span><span class="sxs-lookup"><span data-stu-id="fc1b9-107">Missing features</span></span>

<span data-ttu-id="fc1b9-108">Assurez-vous qu’EF Core dispose de toutes les fonctionnalités que vous devez utiliser dans votre application.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-108">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="fc1b9-109">Consultez [Comparaison des fonctionnalités](xref:efcore-and-ef6/index) pour une comparaison détaillée de la façon dont la fonctionnalité définie dans EF Core est comparée à EF6.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-109">See [Feature Comparison](xref:efcore-and-ef6/index) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="fc1b9-110">Si des fonctionnalités requises sont manquantes, assurez-vous que vous pouvez compenser leur absence avant de procéder au portage vers EF Core.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-110">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="fc1b9-111">Changements de comportement</span><span class="sxs-lookup"><span data-stu-id="fc1b9-111">Behavior changes</span></span>

<span data-ttu-id="fc1b9-112">Il s’agit d’une liste non exhaustive de certaines modifications de comportement entre EF6 et EF Core.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-112">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="fc1b9-113">Il est important de les garder à l’esprit lors du port de votre application, car elles peuvent modifier son comportement, mais ne s’afficheront pas en tant qu’erreurs de compilation après le passage à EF Core.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-113">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="fc1b9-114">DbSet. Add/Attach et le comportement du graphique</span><span class="sxs-lookup"><span data-stu-id="fc1b9-114">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="fc1b9-115">Dans EF6, appeler `DbSet.Add()` sur une entité entraîne une recherche récursive pour toutes les entités référencées dans ses propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-115">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="fc1b9-116">Toutes les entités qui sont détectées et ne sont pas déjà suivies par le contexte sont également marquées comme ajoutées.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-116">Any entities that are found, and are not already tracked by the context, are also marked as added.</span></span> <span data-ttu-id="fc1b9-117">`DbSet.Attach()` se comporte de la même manière, sauf que toutes les entités sont marquées comme inchangées.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-117">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="fc1b9-118">**EF Core effectue une recherche récursive similaire, mais avec des règles légèrement différentes.**</span><span class="sxs-lookup"><span data-stu-id="fc1b9-118">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

* <span data-ttu-id="fc1b9-119">L’entité racine est toujours dans l’état demandé (ajouté pour `DbSet.Add` et inchangé pour `DbSet.Attach`).</span><span class="sxs-lookup"><span data-stu-id="fc1b9-119">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>
* <span data-ttu-id="fc1b9-120">**Pour les entités qui sont détectées lors de la recherche récursive des propriétés de navigation :**</span><span class="sxs-lookup"><span data-stu-id="fc1b9-120">**For entities that are found during the recursive search of navigation properties:**</span></span>
  * <span data-ttu-id="fc1b9-121">**Si la clé primaire de l’entité est générée par le magasin**</span><span class="sxs-lookup"><span data-stu-id="fc1b9-121">**If the primary key of the entity is store generated**</span></span>
    * <span data-ttu-id="fc1b9-122">Si la clé primaire n’est pas définie sur une valeur, l’état est défini sur ajouté.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-122">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="fc1b9-123">La valeur de clé primaire est considérée comme « non définie » si elle est affectée à la valeur CLR par défaut pour le type de propriété (par exemple, `0` pour `int`, `null` pour `string`, etc.).</span><span class="sxs-lookup"><span data-stu-id="fc1b9-123">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (for example, `0` for `int`, `null` for `string`, etc.).</span></span>
    * <span data-ttu-id="fc1b9-124">Si la clé primaire n’est pas définie sur une valeur, l’état est défini sur inchangé.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-124">If the primary key is set to a value, the state is set to unchanged.</span></span>
  * <span data-ttu-id="fc1b9-125">Si la clé primaire n’est pas générée par la base de données, l’entité est mise dans le même état que la racine.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-125">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="fc1b9-126">Initialisation de la base de données Code First</span><span class="sxs-lookup"><span data-stu-id="fc1b9-126">Code First database initialization</span></span>

<span data-ttu-id="fc1b9-127">**EF6 dispose d’un grand nombre de commandes magic exécutées dans le contexte de la sélection de la connexion de base de données et de l’initialisation de la base de données. Voici quelques-unes de ces règles :**</span><span class="sxs-lookup"><span data-stu-id="fc1b9-127">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="fc1b9-128">Si aucune configuration n’est effectuée, EF6 sélectionne une base de données sur SQL Express ou sur une base de données locale.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-128">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="fc1b9-129">Si une chaîne de connexion portant le même nom que le contexte se trouve dans le fichier `App/Web.config` des applications, cette connexion est utilisée.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-129">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="fc1b9-130">Si la base données n’existe pas, elle est créée.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-130">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="fc1b9-131">Si aucune des tables du modèle n’existe dans la base de données, le schéma du modèle actuel est ajouté à la base de données.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-131">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="fc1b9-132">Si les migrations sont activées, elles sont utilisées pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-132">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="fc1b9-133">Si la base de données existe et qu’EF6 a créé le schéma précédemment, la compatibilité du schéma avec le modèle actuel est vérifiée.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-133">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="fc1b9-134">Une exception est levée si le modèle a changé depuis la création du schéma.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-134">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="fc1b9-135">**EF Core n’exécute aucun de ces magics.**</span><span class="sxs-lookup"><span data-stu-id="fc1b9-135">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="fc1b9-136">La connexion de base de données doit être configurée de manière explicite dans le code.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-136">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="fc1b9-137">Aucune initialisation n’est effectuée.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-137">No initialization is performed.</span></span> <span data-ttu-id="fc1b9-138">Vous devez utiliser `DbContext.Database.Migrate()` pour appliquer des migrations (ou `DbContext.Database.EnsureCreated()` et `EnsureDeleted()` pour créer/supprimer la base de données sans utiliser les migrations).</span><span class="sxs-lookup"><span data-stu-id="fc1b9-138">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="fc1b9-139">Convention d’affectation de noms de la table Code First</span><span class="sxs-lookup"><span data-stu-id="fc1b9-139">Code First table naming convention</span></span>

<span data-ttu-id="fc1b9-140">EF6 exécute le nom de la classe d’entité via un service de pluralisation pour calculer le nom de la table par défaut à laquelle l’entité est mappée.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-140">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="fc1b9-141">EF Core utilise le nom de la propriété `DbSet` dans laquelle l’entité est exposée dans le contexte dérivé.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-141">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="fc1b9-142">Si l’entité n’a pas de propriété `DbSet`, le nom de la classe est utilisé.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-142">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
