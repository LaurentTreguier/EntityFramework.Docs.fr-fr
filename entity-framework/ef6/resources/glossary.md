---
title: Glossaire Entity Framework-EF6
description: Glossaire Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
uid: ef6/resources/glossary
ms.openlocfilehash: 19d5e9e3a480337c2bcb93be5f989cc622b67dad
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620202"
---
# <a name="entity-framework-glossary"></a><span data-ttu-id="6a779-103">Glossaire Entity Framework</span><span class="sxs-lookup"><span data-stu-id="6a779-103">Entity Framework Glossary</span></span>
## <a name="code-first"></a><span data-ttu-id="6a779-104">Code First</span><span class="sxs-lookup"><span data-stu-id="6a779-104">Code First</span></span>
<span data-ttu-id="6a779-105">Création d’un modèle de Entity Framework à l’aide de code.</span><span class="sxs-lookup"><span data-stu-id="6a779-105">Creating an Entity Framework model using code.</span></span> <span data-ttu-id="6a779-106">Le modèle peut cibler une base de données existante ou une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="6a779-106">The model can target an existing database or a new database.</span></span>

## <a name="context"></a><span data-ttu-id="6a779-107">Contexte</span><span class="sxs-lookup"><span data-stu-id="6a779-107">Context</span></span>
<span data-ttu-id="6a779-108">Classe qui représente une session avec la base de données, ce qui vous permet d’interroger et d’enregistrer des données.</span><span class="sxs-lookup"><span data-stu-id="6a779-108">A class that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="6a779-109">Un contexte dérive de la classe DbContext ou ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="6a779-109">A context derives from the DbContext or ObjectContext class.</span></span>

## <a name="convention-code-first"></a><span data-ttu-id="6a779-110">Convention (Code First)</span><span class="sxs-lookup"><span data-stu-id="6a779-110">Convention (Code First)</span></span>
<span data-ttu-id="6a779-111">Règle que Entity Framework utilise pour déduire la forme de votre modèle à partir de vos classes.</span><span class="sxs-lookup"><span data-stu-id="6a779-111">A rule that Entity Framework uses to infer the shape of you model from your classes.</span></span>

## <a name="database-first"></a><span data-ttu-id="6a779-112">Database First</span><span class="sxs-lookup"><span data-stu-id="6a779-112">Database First</span></span>
<span data-ttu-id="6a779-113">Création d’un modèle de Entity Framework, à l’aide du concepteur EF, ciblant une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="6a779-113">Creating an Entity Framework model, using the EF Designer, that targets an existing database.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="6a779-114">Chargement hâtif</span><span class="sxs-lookup"><span data-stu-id="6a779-114">Eager loading</span></span>
<span data-ttu-id="6a779-115">Modèle de chargement de données associées dans laquelle une requête pour un type d’entité charge également des entités associées dans le cadre de la requête.</span><span class="sxs-lookup"><span data-stu-id="6a779-115">A pattern of loading related data where a query for one type of entity also loads related entities as part of the query.</span></span>

## <a name="ef-designer"></a><span data-ttu-id="6a779-116">EF Designer</span><span class="sxs-lookup"><span data-stu-id="6a779-116">EF Designer</span></span>
<span data-ttu-id="6a779-117">Concepteur visuel dans Visual Studio qui vous permet de créer un modèle de Entity Framework à l’aide de zones et de lignes.</span><span class="sxs-lookup"><span data-stu-id="6a779-117">A visual designer in Visual Studio that allows you to create an Entity Framework model using boxes and lines.</span></span>

## <a name="entity"></a><span data-ttu-id="6a779-118">Entité</span><span class="sxs-lookup"><span data-stu-id="6a779-118">Entity</span></span>
<span data-ttu-id="6a779-119">Classe ou objet qui représente des données d'application, telles que des clients, des produits et des commandes.</span><span class="sxs-lookup"><span data-stu-id="6a779-119">A class or object that represents application data such as customers, products, and orders.</span></span>

## <a name="entity-data-model"></a><span data-ttu-id="6a779-120">Entity Data Model</span><span class="sxs-lookup"><span data-stu-id="6a779-120">Entity Data Model</span></span>
<span data-ttu-id="6a779-121">Modèle qui décrit les entités et les relations entre eux.</span><span class="sxs-lookup"><span data-stu-id="6a779-121">A model that describes entities and the relationships between them.</span></span> <span data-ttu-id="6a779-122">EF utilise le modèle EDM pour décrire le modèle conceptuel sur lequel les programmes de développement sont utilisés.</span><span class="sxs-lookup"><span data-stu-id="6a779-122">EF uses EDM to describe the conceptual model against which the developer programs.</span></span> <span data-ttu-id="6a779-123">EDM s’appuie sur le modèle de relation d’entité introduit par Dr. Peter Chen.</span><span class="sxs-lookup"><span data-stu-id="6a779-123">EDM builds on the Entity Relationship model introduced by Dr. Peter Chen.</span></span> <span data-ttu-id="6a779-124">L’EDM a été développé à l’origine avec l’objectif principal de devenir le modèle de données commun à travers une suite de technologies de développement et de serveur de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6a779-124">The EDM was originally developed with the primary goal of becoming the common data model across a suite of developer and server technologies from Microsoft.</span></span> <span data-ttu-id="6a779-125">Le modèle EDM est également utilisé dans le cadre du protocole OData.</span><span class="sxs-lookup"><span data-stu-id="6a779-125">EDM is also used as part of the OData protocol.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="6a779-126">Chargement explicite</span><span class="sxs-lookup"><span data-stu-id="6a779-126">Explicit loading</span></span>
<span data-ttu-id="6a779-127">Modèle de chargement de données associées où les objets connexes sont chargés en appelant une API.</span><span class="sxs-lookup"><span data-stu-id="6a779-127">A pattern of loading related data where related objects are loaded by calling an API.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="6a779-128">API Fluent</span><span class="sxs-lookup"><span data-stu-id="6a779-128">Fluent API</span></span>
<span data-ttu-id="6a779-129">API qui peut être utilisée pour configurer un modèle de Code First.</span><span class="sxs-lookup"><span data-stu-id="6a779-129">An API that can be used to configure a Code First model.</span></span>

## <a name="foreign-key-association"></a><span data-ttu-id="6a779-130">Association de clé étrangère</span><span class="sxs-lookup"><span data-stu-id="6a779-130">Foreign key association</span></span>
<span data-ttu-id="6a779-131">Association entre des entités où une propriété qui représente la clé étrangère est incluse dans la classe de l’entité dépendante.</span><span class="sxs-lookup"><span data-stu-id="6a779-131">An association between entities where a property that represents the foreign key is included in the class of the dependent entity.</span></span> <span data-ttu-id="6a779-132">Par exemple, Product contient une propriété CategoryId.</span><span class="sxs-lookup"><span data-stu-id="6a779-132">For example, Product contains a CategoryId property.</span></span>

## <a name="identifying-relationship"></a><span data-ttu-id="6a779-133">Relation d’identification</span><span class="sxs-lookup"><span data-stu-id="6a779-133">Identifying relationship</span></span>
<span data-ttu-id="6a779-134">Relation où la clé primaire de l'entité principale fait également partie de la clé primaire de l'entité dépendante.</span><span class="sxs-lookup"><span data-stu-id="6a779-134">A relationship where the primary key of the principal entity is part of the primary key of the dependent entity.</span></span> <span data-ttu-id="6a779-135">Dans ce type de relation, l'entité dépendante ne peut pas exister dans l'entité principale.</span><span class="sxs-lookup"><span data-stu-id="6a779-135">In this kind of relationship, the dependent entity cannot exist without the principal entity.</span></span>

## <a name="independent-association"></a><span data-ttu-id="6a779-136">Association indépendante</span><span class="sxs-lookup"><span data-stu-id="6a779-136">Independent association</span></span>
<span data-ttu-id="6a779-137">Association entre des entités où il n’y a aucune propriété représentant la clé étrangère dans la classe de l’entité dépendante.</span><span class="sxs-lookup"><span data-stu-id="6a779-137">An association between entities where there is no property representing the foreign key in the class of the dependent entity.</span></span> <span data-ttu-id="6a779-138">Par exemple, une classe Product contient une relation à Category, mais aucune propriété CategoryId.</span><span class="sxs-lookup"><span data-stu-id="6a779-138">For example, a Product class contains a relationship to Category but no CategoryId property.</span></span> <span data-ttu-id="6a779-139">Entity Framework effectue le suivi de l’état de l’Association indépendamment de l’état des entités aux terminaisons des deux associations.</span><span class="sxs-lookup"><span data-stu-id="6a779-139">Entity Framework tracks the state of the association independently of the state of the entities at the two association ends.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="6a779-140">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="6a779-140">Lazy loading</span></span>
<span data-ttu-id="6a779-141">Modèle de chargement de données associées où les objets connexes sont chargés automatiquement lors de l’accès à une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="6a779-141">A pattern of loading related data where related objects are automatically loaded when a navigation property is accessed.</span></span>

## <a name="model-first"></a><span data-ttu-id="6a779-142">Model First</span><span class="sxs-lookup"><span data-stu-id="6a779-142">Model First</span></span>
<span data-ttu-id="6a779-143">Création d’un modèle de Entity Framework à l’aide du concepteur EF, qui est ensuite utilisé pour créer une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="6a779-143">Creating an Entity Framework model, using the EF Designer, that is then used to create a new database.</span></span>

## <a name="navigation-property"></a><span data-ttu-id="6a779-144">Propriété de navigation</span><span class="sxs-lookup"><span data-stu-id="6a779-144">Navigation property</span></span>
<span data-ttu-id="6a779-145">Propriété d’une entité qui référence une autre entité.</span><span class="sxs-lookup"><span data-stu-id="6a779-145">A property of an entity that references another entity.</span></span> <span data-ttu-id="6a779-146">Par exemple, Product contient une propriété de navigation Category et Category contient une propriété de navigation Products.</span><span class="sxs-lookup"><span data-stu-id="6a779-146">For example, Product contains a Category navigation property and Category contains a Products navigation property.</span></span>

## <a name="poco"></a><span data-ttu-id="6a779-147">POCO</span><span class="sxs-lookup"><span data-stu-id="6a779-147">POCO</span></span>
<span data-ttu-id="6a779-148">Acronyme de Plain-Old CLR Object.</span><span class="sxs-lookup"><span data-stu-id="6a779-148">Acronym for Plain-Old CLR Object.</span></span> <span data-ttu-id="6a779-149">Classe d’utilisateur simple qui n’a pas de dépendances avec n’importe quelle infrastructure.</span><span class="sxs-lookup"><span data-stu-id="6a779-149">A simple user class that has no dependencies with any framework.</span></span> <span data-ttu-id="6a779-150">Dans le contexte d’EF, une classe d’entité qui ne dérive pas de EntityObject, implémente toutes les interfaces ou transporte tous les attributs définis dans EF.</span><span class="sxs-lookup"><span data-stu-id="6a779-150">In the context of EF, an entity class that does not derive from EntityObject, implements any interfaces or carries any attributes defined in EF.</span></span> <span data-ttu-id="6a779-151">De telles classes d’entité qui sont découplées de l’infrastructure de persistance sont également dites « ignorant la persistance ».</span><span class="sxs-lookup"><span data-stu-id="6a779-151">Such entity classes that are decoupled from the persistence framework are also said to be "persistence ignorant".</span></span>  

## <a name="relationship-inverse"></a><span data-ttu-id="6a779-152">Relation inverse</span><span class="sxs-lookup"><span data-stu-id="6a779-152">Relationship inverse</span></span>
<span data-ttu-id="6a779-153">L’extrémité opposée d’une relation, par exemple Product. Catégorie et catégorie. Production.</span><span class="sxs-lookup"><span data-stu-id="6a779-153">The opposite end of a relationship, for example, product.Category and category.Product.</span></span>

## <a name="self-tracking-entity"></a><span data-ttu-id="6a779-154">Entité de suivi automatique</span><span class="sxs-lookup"><span data-stu-id="6a779-154">Self-tracking entity</span></span>
<span data-ttu-id="6a779-155">Entité générée à partir d’un modèle de génération de code qui permet le développement multicouche.</span><span class="sxs-lookup"><span data-stu-id="6a779-155">An entity built from a code generation template that helps with N-Tier development.</span></span>

## <a name="table-per-concrete-type-tpc"></a><span data-ttu-id="6a779-156">Type de table par béton (TPC)</span><span class="sxs-lookup"><span data-stu-id="6a779-156">Table-per-concrete type (TPC)</span></span>
<span data-ttu-id="6a779-157">Méthode de mappage de l’héritage où chaque type non abstrait de la hiérarchie est mappé à une table distincte dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="6a779-157">A method of mapping the inheritance where each non-abstract type in the hierarchy is mapped to separate table in the database.</span></span>

## <a name="table-per-hierarchy-tph"></a><span data-ttu-id="6a779-158">TPH (table par hiérarchie)</span><span class="sxs-lookup"><span data-stu-id="6a779-158">Table-per-hierarchy (TPH)</span></span>
<span data-ttu-id="6a779-159">Méthode de mappage de l’héritage dans lequel tous les types de la hiérarchie sont mappés à la même table dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="6a779-159">A method of mapping the inheritance where all types in the hierarchy are mapped to the same table in the database.</span></span> <span data-ttu-id="6a779-160">Une ou plusieurs colonnes de discriminateur sont utilisées pour identifier le type auquel chaque ligne est associée.</span><span class="sxs-lookup"><span data-stu-id="6a779-160">A discriminator column(s) is used to identify what type each row is associated with.</span></span>

## <a name="table-per-type-tpt"></a><span data-ttu-id="6a779-161">Table par type (TPT)</span><span class="sxs-lookup"><span data-stu-id="6a779-161">Table-per-type (TPT)</span></span>
<span data-ttu-id="6a779-162">Méthode de mappage de l’héritage où les propriétés communes de tous les types de la hiérarchie sont mappées à la même table dans la base de données, mais les propriétés propres à chaque type sont mappées à une table distincte.</span><span class="sxs-lookup"><span data-stu-id="6a779-162">A method of mapping the inheritance where the common properties of all types in the hierarchy are mapped to the same table in the database, but properties unique to each type are mapped to a separate table.</span></span>

## <a name="type-discovery"></a><span data-ttu-id="6a779-163">Découverte du type</span><span class="sxs-lookup"><span data-stu-id="6a779-163">Type discovery</span></span>
<span data-ttu-id="6a779-164">Processus d’identification des types qui doivent faire partie d’un modèle de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="6a779-164">The process of identifying the types that should be part of an Entity Framework model.</span></span>
