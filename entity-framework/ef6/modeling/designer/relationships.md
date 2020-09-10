---
title: Relations-concepteur EF-EF6
description: Relations-concepteur EF dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: f7c801dd8f8ae81cfe44283b7575cfe869ce26d3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620422"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="e281c-103">Relations-concepteur EF</span><span class="sxs-lookup"><span data-stu-id="e281c-103">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="e281c-104">Cette page fournit des informations sur la configuration des relations dans votre modèle à l’aide du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="e281c-104">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="e281c-105">Pour obtenir des informations générales sur les relations dans EF et sur la manière d’accéder aux données et de les manipuler à l’aide de relations, consultez [relations & les propriétés de navigation](xref:ef6/fundamentals/relationships).</span><span class="sxs-lookup"><span data-stu-id="e281c-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>

<span data-ttu-id="e281c-106">Les associations définissent les relations entre les types d’entité dans un modèle.</span><span class="sxs-lookup"><span data-stu-id="e281c-106">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="e281c-107">Cette rubrique montre comment mapper des associations avec l’Entity Framework Designer (concepteur EF).</span><span class="sxs-lookup"><span data-stu-id="e281c-107">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="e281c-108">L’illustration suivante montre les fenêtres principales qui sont utilisées lors de l’utilisation du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="e281c-108">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="e281c-110">Lorsque vous générez le modèle conceptuel, des avertissements sur les entités et les associations non mappées peuvent s'afficher dans la Liste d'erreurs.</span><span class="sxs-lookup"><span data-stu-id="e281c-110">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="e281c-111">Vous pouvez ignorer ces avertissements, car une fois que vous avez choisi de générer la base de données à partir du modèle, les erreurs disparaissent.</span><span class="sxs-lookup"><span data-stu-id="e281c-111">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="e281c-112">Vue d’ensemble des associations</span><span class="sxs-lookup"><span data-stu-id="e281c-112">Associations Overview</span></span>

<span data-ttu-id="e281c-113">Lorsque vous concevez votre modèle à l’aide du concepteur EF, un fichier. edmx représente votre modèle.</span><span class="sxs-lookup"><span data-stu-id="e281c-113">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="e281c-114">Dans le fichier. edmx, un élément **Association** définit une relation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="e281c-114">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="e281c-115">Une association doit spécifier les types d'entités impliqués dans la relation et le nombre possible de types d'entités à chaque terminaison de la relation, appelé « multiplicité ».</span><span class="sxs-lookup"><span data-stu-id="e281c-115">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="e281c-116">La multiplicité d’une terminaison d’association peut avoir la valeur un (1), zéro ou un (0.. 1), ou plusieurs ( \* ).</span><span class="sxs-lookup"><span data-stu-id="e281c-116">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="e281c-117">Ces informations sont spécifiées dans deux éléments **finaux** enfants.</span><span class="sxs-lookup"><span data-stu-id="e281c-117">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="e281c-118">Au moment de l’exécution, les instances de type d’entité à une terminaison d’une association sont accessibles via les propriétés de navigation ou les clés étrangères (si vous choisissez d’exposer des clés étrangères dans vos entités).</span><span class="sxs-lookup"><span data-stu-id="e281c-118">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="e281c-119">Avec les clés étrangères exposées, la relation entre les entités est gérée à l’aide d’un élément **ReferentialConstraint** (élément enfant de l’élément **Association** ).</span><span class="sxs-lookup"><span data-stu-id="e281c-119">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="e281c-120">Il est recommandé de toujours exposer les clés étrangères pour les relations dans vos entités.</span><span class="sxs-lookup"><span data-stu-id="e281c-120">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="e281c-121">Dans plusieurs-à-plusieurs ( \* : \* ), vous ne pouvez pas ajouter de clés étrangères aux entités.</span><span class="sxs-lookup"><span data-stu-id="e281c-121">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="e281c-122">Dans une \* relation de : \* , les informations d’association sont gérées avec un objet indépendant.</span><span class="sxs-lookup"><span data-stu-id="e281c-122">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="e281c-123">Pour plus d’informations sur les éléments CSDL (**ReferentialConstraint**, **Association**, etc.), consultez la [spécification CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span><span class="sxs-lookup"><span data-stu-id="e281c-123">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="e281c-124">Créer et supprimer des associations</span><span class="sxs-lookup"><span data-stu-id="e281c-124">Create and Delete Associations</span></span>

<span data-ttu-id="e281c-125">La création d’une association avec le concepteur EF met à jour le contenu du modèle du fichier. edmx.</span><span class="sxs-lookup"><span data-stu-id="e281c-125">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="e281c-126">Après avoir créé une association, vous devez créer les mappages pour l’Association (abordé plus loin dans cette rubrique).</span><span class="sxs-lookup"><span data-stu-id="e281c-126">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="e281c-127">Cette section part du principe que vous avez déjà ajouté les entités pour lesquelles vous souhaitez créer une association entre et votre modèle.</span><span class="sxs-lookup"><span data-stu-id="e281c-127">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="e281c-128">Pour créer une association</span><span class="sxs-lookup"><span data-stu-id="e281c-128">To create an association</span></span>

1.  <span data-ttu-id="e281c-129">Cliquez avec le bouton droit sur une zone vide de l’aire de conception, pointez sur **Ajouter nouveau**, puis sélectionnez **Association...**.</span><span class="sxs-lookup"><span data-stu-id="e281c-129">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="e281c-130">Renseignez les paramètres de l’Association dans la boîte de dialogue **Ajouter une association** .</span><span class="sxs-lookup"><span data-stu-id="e281c-130">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![Ajouter une association](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="e281c-132">Vous pouvez choisir de ne pas ajouter de propriétés de navigation ou de clé étrangère aux entités situées aux terminaisons de l’Association en désactivant les cases à cocher de la **propriété de navigation **et **Ajouter les propriétés de clé étrangère aux cases d' &lt; &gt; entité nom du type d’entité **.</span><span class="sxs-lookup"><span data-stu-id="e281c-132">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="e281c-133">Si vous ajoutez une seule propriété de navigation, l'association n'est parcourable que dans une seule direction.</span><span class="sxs-lookup"><span data-stu-id="e281c-133">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="e281c-134">Si vous n'ajoutez pas de propriétés de navigation, vous devez ajouter des propriétés de clé étrangère pour accéder aux entités au niveau des terminaisons de l'association.</span><span class="sxs-lookup"><span data-stu-id="e281c-134">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="e281c-135">Cliquez sur  **OK**.</span><span class="sxs-lookup"><span data-stu-id="e281c-135">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="e281c-136">Pour supprimer une association</span><span class="sxs-lookup"><span data-stu-id="e281c-136">To delete an association</span></span>

<span data-ttu-id="e281c-137">Pour supprimer une association, effectuez l’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="e281c-137">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="e281c-138">Cliquez avec le bouton droit sur l’Association dans l’aire du concepteur EF, puis sélectionnez **supprimer**.</span><span class="sxs-lookup"><span data-stu-id="e281c-138">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="e281c-139">- OU -</span><span class="sxs-lookup"><span data-stu-id="e281c-139">OR -</span></span>

-   <span data-ttu-id="e281c-140">Sélectionnez une ou plusieurs associations et appuyez sur la touche SUPPR.</span><span class="sxs-lookup"><span data-stu-id="e281c-140">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="e281c-141">Inclure des propriétés de clé étrangère dans vos entités (contraintes référentielles)</span><span class="sxs-lookup"><span data-stu-id="e281c-141">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="e281c-142">Il est recommandé de toujours exposer les clés étrangères pour les relations dans vos entités.</span><span class="sxs-lookup"><span data-stu-id="e281c-142">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="e281c-143">Entity Framework utilise une contrainte référentielle pour identifier qu’une propriété joue le rôle de clé étrangère pour une relation.</span><span class="sxs-lookup"><span data-stu-id="e281c-143">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="e281c-144">Si vous avez activé la case à cocher ***Ajouter les propriétés de clé étrangère à la case d' &lt; &gt; entité nom du type d’entité*** lors de la création d’une relation, cette contrainte référentielle a été ajoutée pour vous.</span><span class="sxs-lookup"><span data-stu-id="e281c-144">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="e281c-145">Lorsque vous utilisez le concepteur EF pour ajouter ou modifier une contrainte référentielle, le concepteur EF ajoute ou modifie un élément **ReferentialConstraint**   dans le contenu CSDL du fichier. edmx.</span><span class="sxs-lookup"><span data-stu-id="e281c-145">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="e281c-146">Double-cliquez sur l'association à modifier.</span><span class="sxs-lookup"><span data-stu-id="e281c-146">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="e281c-147">La boîte de dialogue **contrainte référentielle**   s’affiche.</span><span class="sxs-lookup"><span data-stu-id="e281c-147">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="e281c-148">Dans la **Principal**   liste déroulante principal, sélectionnez l’entité principale dans la contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="e281c-148">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="e281c-149">Les propriétés de clé de l’entité sont ajoutées **Principal Key**à la   liste de clés principales dans la boîte de dialogue.</span><span class="sxs-lookup"><span data-stu-id="e281c-149">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="e281c-150">Dans la liste déroulante **dépendante**   , sélectionnez l’entité dépendante dans la contrainte référentielle.</span><span class="sxs-lookup"><span data-stu-id="e281c-150">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="e281c-151">Pour chaque clé principale qui a une clé dépendante, sélectionnez une clé dépendante correspondante dans les listes déroulantes de la colonne **clé dépendante**   .</span><span class="sxs-lookup"><span data-stu-id="e281c-151">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Contrainte Ref](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="e281c-153">Cliquez sur  **OK**.</span><span class="sxs-lookup"><span data-stu-id="e281c-153">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="e281c-154">créer et modifier des mappages d'association</span><span class="sxs-lookup"><span data-stu-id="e281c-154">Create and Edit Association Mappings</span></span>

<span data-ttu-id="e281c-155">Vous pouvez spécifier la façon dont une association est mappée à la base de données dans la fenêtre **Détails de mappage**   du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="e281c-155">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="e281c-156">Vous ne pouvez mapper que les détails des associations pour lesquelles aucune contrainte référentielle n’est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="e281c-156">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="e281c-157">Si une contrainte référentielle est spécifiée, une propriété de clé étrangère est incluse dans l’entité et vous pouvez utiliser les détails de mappage de l’entité pour contrôler la colonne sur laquelle la clé étrangère est mappée.</span><span class="sxs-lookup"><span data-stu-id="e281c-157">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="e281c-158">Créer un mappage d’association</span><span class="sxs-lookup"><span data-stu-id="e281c-158">Create an association mapping</span></span>

-   <span data-ttu-id="e281c-159">Cliquez avec le bouton droit sur une association dans l’aire de conception, puis sélectionnez **mappage de table**.</span><span class="sxs-lookup"><span data-stu-id="e281c-159">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="e281c-160">Cela permet d’afficher le mappage d’association dans la fenêtre **Détails de mappage**   .</span><span class="sxs-lookup"><span data-stu-id="e281c-160">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="e281c-161">Cliquez sur **Ajouter une table ou une vue**.</span><span class="sxs-lookup"><span data-stu-id="e281c-161">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="e281c-162">Une liste déroulante s'affiche. Elle contient toutes les tables du modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="e281c-162">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="e281c-163">Sélectionnez la table à laquelle l'association doit être mappée.</span><span class="sxs-lookup"><span data-stu-id="e281c-163">Select the table to which the association will map.</span></span>
    <span data-ttu-id="e281c-164">La fenêtre **Détails de mapping**   affiche les deux terminaisons de l’Association et les propriétés de clé pour le type d’entité à chaque **extrémité**.</span><span class="sxs-lookup"><span data-stu-id="e281c-164">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="e281c-165">Pour chaque propriété de clé, cliquez sur le champ de **colonne**   , puis sélectionnez la colonne dans laquelle la propriété sera mappée.</span><span class="sxs-lookup"><span data-stu-id="e281c-165">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![Détails du mappage 4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="e281c-167">Modifier un mappage d’association</span><span class="sxs-lookup"><span data-stu-id="e281c-167">Edit an association mapping</span></span>

-   <span data-ttu-id="e281c-168">Cliquez avec le bouton droit sur une association dans l’aire de conception, puis sélectionnez **mappage de table**.</span><span class="sxs-lookup"><span data-stu-id="e281c-168">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="e281c-169">Cela permet d’afficher le mappage d’association dans la fenêtre **Détails de mappage**   .</span><span class="sxs-lookup"><span data-stu-id="e281c-169">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="e281c-170">Cliquez sur **mappe au &lt; nom &gt; de la table**.</span><span class="sxs-lookup"><span data-stu-id="e281c-170">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="e281c-171">Une liste déroulante s'affiche. Elle contient toutes les tables du modèle de stockage.</span><span class="sxs-lookup"><span data-stu-id="e281c-171">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="e281c-172">Sélectionnez la table à laquelle l'association doit être mappée.</span><span class="sxs-lookup"><span data-stu-id="e281c-172">Select the table to which the association will map.</span></span>
    <span data-ttu-id="e281c-173">La fenêtre **Détails de mapping**   affiche les deux terminaisons de l’Association et les propriétés de clé pour le type d’entité à chaque extrémité.</span><span class="sxs-lookup"><span data-stu-id="e281c-173">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="e281c-174">Pour chaque propriété de clé, cliquez sur le champ de **colonne**   , puis sélectionnez la colonne dans laquelle la propriété sera mappée.</span><span class="sxs-lookup"><span data-stu-id="e281c-174">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="e281c-175">Modifier et supprimer des propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="e281c-175">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="e281c-176">Les propriétés de navigation sont des propriétés de raccourci qui permettent de rechercher les entités situées aux terminaisons d’une association dans un modèle.</span><span class="sxs-lookup"><span data-stu-id="e281c-176">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="e281c-177">Il est possible de créer des propriétés de navigation lorsque vous créez une association entre deux types d'entité.</span><span class="sxs-lookup"><span data-stu-id="e281c-177">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="e281c-178">Pour modifier les propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="e281c-178">To edit navigation properties</span></span>

-   <span data-ttu-id="e281c-179">Sélectionnez une propriété de navigation sur l’aire du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="e281c-179">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="e281c-180">Les informations relatives à la propriété de navigation s’affichent dans la fenêtre **Propriétés**de Visual Studio   .</span><span class="sxs-lookup"><span data-stu-id="e281c-180">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="e281c-181">Modifiez les paramètres de propriété dans la fenêtre **Propriétés**   .</span><span class="sxs-lookup"><span data-stu-id="e281c-181">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="e281c-182">Pour supprimer des propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="e281c-182">To delete navigation properties</span></span>

-   <span data-ttu-id="e281c-183">Si les clés étrangères ne sont pas exposées sur les types d'entité dans le modèle conceptuel, le fait de supprimer une propriété de navigation peut rendre l'association correspondante parcourable dans une seule direction ou pas parcourable du tout.</span><span class="sxs-lookup"><span data-stu-id="e281c-183">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="e281c-184">Cliquez avec le bouton droit sur une propriété de navigation sur l’aire du concepteur EF, puis sélectionnez **supprimer**.</span><span class="sxs-lookup"><span data-stu-id="e281c-184">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
