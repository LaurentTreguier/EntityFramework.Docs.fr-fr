---
title: Concepteur TPH héritage-EF6
description: Héritage TPH dans le concepteur dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tph
ms.openlocfilehash: 39675f9533dfef0ddad1867e3b70cd13b30708ea
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073394"
---
# <a name="designer-tph-inheritance"></a><span data-ttu-id="bcbc6-103">Héritage TPH du concepteur</span><span class="sxs-lookup"><span data-stu-id="bcbc6-103">Designer TPH Inheritance</span></span>
<span data-ttu-id="bcbc6-104">Cette procédure pas à pas montre comment implémenter l’héritage TPH (table par hiérarchie) dans votre modèle conceptuel avec l’Entity Framework Designer (concepteur EF).</span><span class="sxs-lookup"><span data-stu-id="bcbc6-104">This step-by-step walkthrough shows how to implement table-per-hierarchy (TPH) inheritance in your conceptual model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="bcbc6-105">L’héritage TPH utilise une table de base de données pour conserver les données de tous les types d’entité dans une hiérarchie d’héritage.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-105">TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.</span></span>

<span data-ttu-id="bcbc6-106">Dans cette procédure pas à pas, nous allons mapper la table Person à trois types d’entité : person (type de base), Student (Derived person) et Instructor (dérive de Person).</span><span class="sxs-lookup"><span data-stu-id="bcbc6-106">In this walkthrough we will map the Person table to three entity types: Person (the base type), Student (derives from Person), and Instructor (derives from Person).</span></span> <span data-ttu-id="bcbc6-107">Nous allons créer un modèle conceptuel à partir de la base de données (Database First), puis modifier le modèle pour implémenter l’héritage TPH à l’aide du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-107">We'll create a conceptual model from the database (Database First) and then alter the model to implement the TPH inheritance using the EF Designer.</span></span>

<span data-ttu-id="bcbc6-108">Il est possible de mapper à un héritage TPH à l’aide de Model First, mais vous devez écrire votre propre flux de travail de génération de base de données, ce qui est complexe.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-108">It is possible to map to a TPH inheritance using Model First but you would have to write your own database generation workflow which is complex.</span></span> <span data-ttu-id="bcbc6-109">Vous affectez ensuite ce flux de travail à la propriété **de flux de travail de génération de base de données** dans le concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-109">You would then assign this workflow to the **Database Generation Workflow** property in the EF Designer.</span></span> <span data-ttu-id="bcbc6-110">Une alternative plus simple consiste à utiliser Code First.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-110">An easier alternative is to use Code First.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="bcbc6-111">Autres options d’héritage</span><span class="sxs-lookup"><span data-stu-id="bcbc6-111">Other Inheritance Options</span></span>

<span data-ttu-id="bcbc6-112">La table par type (TPT) est un autre type d’héritage dans lequel des tables distinctes de la base de données sont mappées à des entités qui participent à l’héritage.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-112">Table-per-Type (TPT) is another type of inheritance in which separate tables in the database are mapped to entities that participate in the inheritance.</span></span> <span data-ttu-id="bcbc6-113">Pour plus d’informations sur la façon de mapper l’héritage TPT (table par type) à l’aide du concepteur EF, consultez [EF designer TPT Inheritance](xref:ef6/modeling/designer/inheritance/tpt).</span><span class="sxs-lookup"><span data-stu-id="bcbc6-113"> For information about how to map Table-per-Type inheritance with the EF Designer, see [EF Designer TPT Inheritance](xref:ef6/modeling/designer/inheritance/tpt).</span></span>

<span data-ttu-id="bcbc6-114">L’héritage de type table par béton (TPC) et les modèles d’héritage mixte sont pris en charge par le runtime Entity Framework, mais ne sont pas pris en charge par le concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-114">Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="bcbc6-115">Si vous souhaitez utiliser l’héritage TPC ou mixte, deux options s’offrent à vous : Utilisez Code First ou modifiez manuellement le fichier EDMX.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-115">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="bcbc6-116">Si vous choisissez de travailler avec le fichier EDMX, la fenêtre Détails de mappage est placée en « mode sans échec » et vous ne pouvez pas utiliser le concepteur pour modifier les mappages.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-116">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bcbc6-117">Prérequis</span><span class="sxs-lookup"><span data-stu-id="bcbc6-117">Prerequisites</span></span>

<span data-ttu-id="bcbc6-118">Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="bcbc6-118">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="bcbc6-119">Une version récente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-119">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="bcbc6-120">[Exemple de base de données School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="bcbc6-120">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="bcbc6-121">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="bcbc6-121">Set up the Project</span></span>

-   <span data-ttu-id="bcbc6-122">Ouvrez Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-122">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="bcbc6-123">Sélectionnez **fichier- &gt; nouveau- &gt; projet**</span><span class="sxs-lookup"><span data-stu-id="bcbc6-123">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="bcbc6-124">Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-124">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="bcbc6-125">Entrez **TPHDBFirstSample**   comme nom.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-125">Enter **TPHDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="bcbc6-126">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-126">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="bcbc6-127">Création d'un modèle</span><span class="sxs-lookup"><span data-stu-id="bcbc6-127">Create a Model</span></span>

-   <span data-ttu-id="bcbc6-128">Dans Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **ajouter- &gt; nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-128">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="bcbc6-129">Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-129">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="bcbc6-130">Entrez **TPHModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-130">Enter **TPHModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="bcbc6-131">Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-131">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="bcbc6-132">Cliquez sur **nouvelle connexion**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-132">Click **New Connection**.</span></span>
    <span data-ttu-id="bcbc6-133">Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, **( \\ mssqllocaldb)**, sélectionnez la méthode d’authentification, tapez **School**   comme nom de la base de données, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-133">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="bcbc6-134">La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-134">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="bcbc6-135">Dans la boîte de dialogue choisir vos objets de base de données, sous le nœud tables, sélectionnez la table **Person** .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-135">In the Choose Your Database Objects dialog box, under the Tables node, select the **Person** table.</span></span>
-   <span data-ttu-id="bcbc6-136">Cliquez sur **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-136">Click **Finish**.</span></span>

<span data-ttu-id="bcbc6-137">Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-137">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="bcbc6-138">Tous les objets que vous avez sélectionnés dans la boîte de dialogue choisir vos objets de base de données sont ajoutés au modèle.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-138">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

<span data-ttu-id="bcbc6-139">C’est ainsi que la table **Person** apparaît dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-139">That is how the **Person** table looks in the database.</span></span>

![Table Person](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a><span data-ttu-id="bcbc6-141">Implémenter l’héritage TPH (table par hiérarchie)</span><span class="sxs-lookup"><span data-stu-id="bcbc6-141">Implement Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="bcbc6-142">La table **Person** contient la colonne de **discriminateur** , qui peut avoir l’une des deux valeurs suivantes : « Student » et « Instructor ».</span><span class="sxs-lookup"><span data-stu-id="bcbc6-142">The **Person** table has the **Discriminator** column, which can have one of two values: “Student” and “Instructor”.</span></span> <span data-ttu-id="bcbc6-143">Selon la valeur, la table **Person** est mappée à l’entité **Student** ou à l’entité **Instructor** .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-143">Depending on the value the **Person** table will be mapped to the **Student** entity or the **Instructor** entity.</span></span> <span data-ttu-id="bcbc6-144">La table **Person** possède également deux colonnes : **HireDate**   et **EnrollmentDate**, qui doivent avoir la valeur **null** , car une personne ne peut pas être un étudiant et un formateur en même temps (au moins dans cette procédure pas à pas).</span><span class="sxs-lookup"><span data-stu-id="bcbc6-144">The **Person** table also has two columns, **HireDate** and **EnrollmentDate**, which must be **nullable** because a person cannot be a student and an instructor at the same time (at least not in this walkthrough).</span></span>

### <a name="add-new-entities"></a><span data-ttu-id="bcbc6-145">Ajouter de nouvelles entités</span><span class="sxs-lookup"><span data-stu-id="bcbc6-145">Add new Entities</span></span>

-   <span data-ttu-id="bcbc6-146">Ajoutez une nouvelle entité.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-146">Add a new entity.</span></span>
    <span data-ttu-id="bcbc6-147">Pour ce faire, cliquez avec le bouton droit sur un espace vide de l’aire de conception du Entity Framework Designer, puis sélectionnez **Add- &gt; Entity**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-147">To do this, right-click on an empty space of the design surface of the Entity Framework Designer, and select **Add-&gt;Entity**.</span></span>
-   <span data-ttu-id="bcbc6-148">Tapez **Instructor**   pour le nom de l' **entité**   et sélectionnez **Person**   dans la liste déroulante du **type de base**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-148">Type **Instructor** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>
-   <span data-ttu-id="bcbc6-149">Cliquez sur  **OK**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-149">Click **OK**.</span></span>
-   <span data-ttu-id="bcbc6-150">Ajoutez une autre nouvelle entité.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-150">Add another new entity.</span></span> <span data-ttu-id="bcbc6-151">Tapez **Student**   pour le nom de l' **entité**   et sélectionnez **Person**   dans la liste déroulante du **type de base**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-151">Type **Student** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>

<span data-ttu-id="bcbc6-152">Deux nouveaux types d’entité ont été ajoutés à l’aire de conception.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-152">Two new entity types were added to the design surface.</span></span> <span data-ttu-id="bcbc6-153">Une flèche pointe des nouveaux types d’entités vers le **Person**   type d’entité Person ; cela indique que **Person**   est le type de base pour les nouveaux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-153">An arrow points from the new entity types to the **Person** entity type; this indicates that **Person** is the base type for the new entity types.</span></span>

-   <span data-ttu-id="bcbc6-154">Cliquez avec le bouton droit sur la propriété **HireDate**   de l’entité **Person**   .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-154">Right-click the **HireDate** property of the **Person** entity.</span></span> <span data-ttu-id="bcbc6-155">Sélectionnez **couper** (ou utilisez la touche Ctrl-X).</span><span class="sxs-lookup"><span data-stu-id="bcbc6-155">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="bcbc6-156">Cliquez avec le bouton droit sur l’entité **Instructor**   , puis sélectionnez **coller** (ou utilisez la touche Ctrl-V).</span><span class="sxs-lookup"><span data-stu-id="bcbc6-156">Right-click the **Instructor** entity and select **Paste** (or use the Ctrl-V key).</span></span>
-   <span data-ttu-id="bcbc6-157">Cliquez avec le bouton droit sur la propriété **HireDate**   et sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-157">Right-click the **HireDate** property and select **Properties**.</span></span>
-   <span data-ttu-id="bcbc6-158">Dans la fenêtre **Propriétés**   , affectez **Nullable**la valeur    **false**à la propriété Nullable.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-158">In the **Properties** window, set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="bcbc6-159">Cliquez avec le bouton droit sur la propriété **EnrollmentDate**   de l’entité **Person**   .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-159">Right-click the **EnrollmentDate** property of the **Person** entity.</span></span> <span data-ttu-id="bcbc6-160">Sélectionnez **couper** (ou utilisez la touche Ctrl-X).</span><span class="sxs-lookup"><span data-stu-id="bcbc6-160">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="bcbc6-161">Cliquez avec le bouton droit sur l’entité **Student** et sélectionnez **Coller (ou utilisez la touche Ctrl-V).**</span><span class="sxs-lookup"><span data-stu-id="bcbc6-161">Right-click the **Student** entity and select **Paste(or use the Ctrl-V key).**</span></span>
-   <span data-ttu-id="bcbc6-162">Sélectionnez la propriété **EnrollmentDate**   et affectez **Nullable**la valeur    **false**à la propriété Nullable.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-162">Select the **EnrollmentDate** property and set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="bcbc6-163">Sélectionnez le **Person**   type d’entité Person.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-163">Select the **Person** entity type.</span></span> <span data-ttu-id="bcbc6-164">Dans la fenêtre **Propriétés**   , affectez à sa propriété **abstract**la    **valeur true**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-164">In the **Properties** window, set its **Abstract** property to **true**.</span></span>
-   <span data-ttu-id="bcbc6-165">Supprimez la propriété de **discriminateur** de **Person**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-165">Delete the **Discriminator** property from **Person**.</span></span> <span data-ttu-id="bcbc6-166">La raison pour laquelle elle doit être supprimée est expliquée dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-166">The reason it should be deleted is explained in the following section.</span></span>

### <a name="map-the-entities"></a><span data-ttu-id="bcbc6-167">Mapper les entités</span><span class="sxs-lookup"><span data-stu-id="bcbc6-167">Map the entities</span></span>

-   <span data-ttu-id="bcbc6-168">Cliquez avec le bouton droit sur l' **instructeur** et sélectionnez **mappage de table.**</span><span class="sxs-lookup"><span data-stu-id="bcbc6-168">Right-click the **Instructor** and select **Table Mapping.**</span></span>
    <span data-ttu-id="bcbc6-169">L’entité Instructor est sélectionnée dans la fenêtre Détails de mappage.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-169">The Instructor entity is selected in the Mapping Details window.</span></span>
-   <span data-ttu-id="bcbc6-170">Cliquez sur \*\* &lt; Ajouter une table ou &gt; une vue\*\*   dans la fenêtre **Détails de mappage**   .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-170">Click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
    <span data-ttu-id="bcbc6-171">Le champ \*\* &lt; Ajouter une table ou &gt; une vue\*\*   devient une liste déroulante de tables ou de vues dans laquelle l’entité sélectionnée peut être mappée.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-171">The **&lt;Add a Table or View&gt;** field becomes a drop-down list of tables or views to which the selected entity can be mapped.</span></span>
-   <span data-ttu-id="bcbc6-172">Sélectionnez **Person**   dans la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-172">Select **Person** from the drop-down list.</span></span>
-   <span data-ttu-id="bcbc6-173">La fenêtre **Détails de mapping**   est mise à jour avec les mappages de colonnes par défaut et une option pour ajouter une condition.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-173">The **Mapping Details** window is updated with default column mappings and an option for adding a condition.</span></span>
-   <span data-ttu-id="bcbc6-174">Cliquez sur \*\* &lt; Ajouter une condition &gt; \*\*.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-174">Click on **&lt;Add a Condition&gt;**.</span></span>
    <span data-ttu-id="bcbc6-175">Le champ \*\* &lt; Ajouter une &gt; condition\*\*   devient une liste déroulante de colonnes pour lesquelles des conditions peuvent être définies.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-175">The **&lt;Add a Condition&gt;** field becomes a drop-down list of columns for which conditions can be set.</span></span>
-   <span data-ttu-id="bcbc6-176">Sélectionnez **discriminateur**   dans la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-176">Select **Discriminator** from the drop-down list.</span></span>
-   <span data-ttu-id="bcbc6-177">Dans la **Operator**   colonne opérateur de la fenêtre **Détails de mappage**   , sélectionnez = dans la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-177">In the **Operator** column of the **Mapping Details** window, select = from the drop-down list.</span></span>
-   <span data-ttu-id="bcbc6-178">Dans la colonne **valeur/propriété** , tapez **Instructor**.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-178">In the **Value/Property** column, type **Instructor**.</span></span> <span data-ttu-id="bcbc6-179">Le résultat final doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="bcbc6-179">The end result should look like this:</span></span>

    ![Détails de mappage](~/ef6/media/mappingdetails2.png)

-   <span data-ttu-id="bcbc6-181">Répétez ces étapes pour le type d’entité **Student**   , mais faites en sorte que la condition soit égale à la valeur **Student** .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-181">Repeat these steps for the **Student** entity type, but make the condition equal to **Student** value.</span></span>  
    <span data-ttu-id="bcbc6-182">*La raison pour laquelle nous voulions supprimer la propriété de **discriminateur** , c’est parce que vous ne pouvez pas mapper une colonne de table plusieurs fois. Cette colonne sera utilisée pour le mappage conditionnel. elle ne peut donc pas être utilisée pour le mappage de propriété. La seule façon de pouvoir les utiliser pour les deux, si une condition utilise une comparaison \*\*is null*\*   ou **is not null**   .\*</span><span class="sxs-lookup"><span data-stu-id="bcbc6-182">*The reason we wanted to remove the **Discriminator** property, is because you cannot map a table column more than once. This column will be used for conditional mapping, so it cannot be used for property mapping as well. The only way it can be used for both, if a condition uses an **Is Null** or **Is Not Null** comparison.*</span></span>

<span data-ttu-id="bcbc6-183">L'héritage TPH (table par hiérarchie) est maintenant implémenté.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-183">Table-per-hierarchy inheritance is now implemented.</span></span>

![TPH final](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a><span data-ttu-id="bcbc6-185">Utiliser le modèle</span><span class="sxs-lookup"><span data-stu-id="bcbc6-185">Use the Model</span></span>

<span data-ttu-id="bcbc6-186">Ouvrez le fichier **Program.cs** dans lequel la méthode **main** est définie.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-186">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="bcbc6-187">Collez le code suivant dans la fonction **main** .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-187">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="bcbc6-188">Le code exécute trois requêtes.</span><span class="sxs-lookup"><span data-stu-id="bcbc6-188">The code executes three queries.</span></span> <span data-ttu-id="bcbc6-189">La première requête rétablit tous les objets **Person** .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-189">The first query brings back all **Person** objects.</span></span> <span data-ttu-id="bcbc6-190">La deuxième requête utilise la méthode **OfType** pour retourner des objets de **formateur** .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-190">The second query uses the **OfType** method to return **Instructor** objects.</span></span> <span data-ttu-id="bcbc6-191">La troisième requête utilise la méthode **OfType** pour retourner les objets **Student** .</span><span class="sxs-lookup"><span data-stu-id="bcbc6-191">The third query uses the **OfType** method to return **Student** objects.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
