---
title: Fractionnement de table du concepteur-EF6
description: Fractionnement des tables du concepteur dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
uid: ef6/modeling/designer/table-splitting
ms.openlocfilehash: 55642d7c3b4efc7467e8a40e68b4144728df270f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620386"
---
# <a name="designer-table-splitting"></a><span data-ttu-id="1b27e-103">Fractionnement des tables du concepteur</span><span class="sxs-lookup"><span data-stu-id="1b27e-103">Designer Table Splitting</span></span>
<span data-ttu-id="1b27e-104">Cette procédure pas à pas montre comment mapper plusieurs types d’entité à une seule table en modifiant un modèle avec le Entity Framework Designer (concepteur EF).</span><span class="sxs-lookup"><span data-stu-id="1b27e-104">This walkthrough shows how to map multiple entity types to a single table by modifying a model with the Entity Framework Designer (EF Designer).</span></span>

<span data-ttu-id="1b27e-105">L’une des raisons pour lesquelles vous pouvez souhaiter utiliser le fractionnement de table est de retarder le chargement de certaines propriétés lors de l’utilisation du chargement différé pour charger vos objets.</span><span class="sxs-lookup"><span data-stu-id="1b27e-105">One reason you may want to use table splitting is delaying the loading of some properties when using lazy loading to load your objects.</span></span><span data-ttu-id="1b27e-106">Vous pouvez séparer les propriétés qui peuvent contenir de très grandes quantités de données dans une entité distincte et les charger uniquement lorsque cela est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="1b27e-106"> You can separate the properties that might contain very large amount of data into a separate entity and only load it when required.</span></span>

<span data-ttu-id="1b27e-107">L’illustration suivante montre les fenêtres principales qui sont utilisées lors de l’utilisation du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="1b27e-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="1b27e-109">Prérequis</span><span class="sxs-lookup"><span data-stu-id="1b27e-109">Prerequisites</span></span>

<span data-ttu-id="1b27e-110">Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1b27e-110">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="1b27e-111">Une version récente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1b27e-111">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="1b27e-112">[Exemple de base de données School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="1b27e-112">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="1b27e-113">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="1b27e-113">Set up the Project</span></span>

<span data-ttu-id="1b27e-114">Cette procédure pas à pas utilise Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="1b27e-114">This walkthrough is using Visual Studio 2012.</span></span>

-   <span data-ttu-id="1b27e-115">Ouvrez Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="1b27e-115">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="1b27e-116">Dans le menu **Fichier** , pointez sur **Nouveau**, puis cliquez sur **Projet**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-116">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="1b27e-117">Dans le volet gauche, cliquez sur Visual C \# , puis sélectionnez le modèle application console.</span><span class="sxs-lookup"><span data-stu-id="1b27e-117">In the left pane, click Visual C\#, and then select the Console Application template.</span></span>
-   <span data-ttu-id="1b27e-118">Entrez **TableSplittingSample** comme nom du projet, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-118">Enter **TableSplittingSample** as the name of the project and click **OK**.</span></span>

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="1b27e-119">Créer un modèle basé sur la base de données School</span><span class="sxs-lookup"><span data-stu-id="1b27e-119">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="1b27e-120">Cliquez avec le bouton droit sur le nom du projet dans Explorateur de solutions, pointez sur **Ajouter**, puis cliquez sur **nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-120">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="1b27e-121">Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.</span><span class="sxs-lookup"><span data-stu-id="1b27e-121">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="1b27e-122">Entrez **TableSplittingModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-122">Enter **TableSplittingModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="1b27e-123">Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant.**</span><span class="sxs-lookup"><span data-stu-id="1b27e-123">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="1b27e-124">Cliquez sur nouvelle connexion.</span><span class="sxs-lookup"><span data-stu-id="1b27e-124">Click New Connection.</span></span> <span data-ttu-id="1b27e-125">Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, **( \\ mssqllocaldb)**, sélectionnez la méthode d’authentification, tapez **School**   comme nom de la base de données, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-125">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="1b27e-126">La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="1b27e-126">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="1b27e-127">Dans la boîte de dialogue choisir vos objets de base de données, dérouler le nœud **tables**   et vérifier la table **Person** .</span><span class="sxs-lookup"><span data-stu-id="1b27e-127">In the Choose Your Database Objects dialog box, unfold the **Tables** node and check the **Person** table.</span></span> <span data-ttu-id="1b27e-128">Cette opération ajoute la table spécifiée au modèle **School** .</span><span class="sxs-lookup"><span data-stu-id="1b27e-128">This will add the specified table to the **School** model.</span></span>
-   <span data-ttu-id="1b27e-129">Cliquez sur **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-129">Click **Finish**.</span></span>

<span data-ttu-id="1b27e-130">Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.</span><span class="sxs-lookup"><span data-stu-id="1b27e-130">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="1b27e-131">Tous les objets que vous avez sélectionnés dans la boîte **de dialogue choisir vos objets de base de données**   sont ajoutés au modèle.</span><span class="sxs-lookup"><span data-stu-id="1b27e-131">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>

## <a name="map-two-entities-to-a-single-table"></a><span data-ttu-id="1b27e-132">Mapper deux entités à une table unique</span><span class="sxs-lookup"><span data-stu-id="1b27e-132">Map Two Entities to a Single Table</span></span>

<span data-ttu-id="1b27e-133">Dans cette section, vous allez fractionner l’entité **Person** en deux entités, puis les mapper à une table unique.</span><span class="sxs-lookup"><span data-stu-id="1b27e-133">In this section you will split the **Person** entity into two entities and then map them to a single table.</span></span>

> [!NOTE]
> <span data-ttu-id="1b27e-134">L’entité **Person** ne contient pas de propriétés pouvant contenir une grande quantité de données ; elle est utilisée à titre d’exemple.</span><span class="sxs-lookup"><span data-stu-id="1b27e-134">The **Person** entity does not contain any properties that may contain large amount of data; it is just used as an example.</span></span>

-   <span data-ttu-id="1b27e-135">Cliquez avec le bouton droit sur une zone vide de l’aire de conception, pointez sur **Ajouter nouveau**, puis cliquez sur **entité**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-135">Right-click an empty area of the design surface, point to **Add New**, and click **Entity**.</span></span>
    <span data-ttu-id="1b27e-136">La boîte **de dialogue nouvelle entité**   s’affiche.</span><span class="sxs-lookup"><span data-stu-id="1b27e-136">The **New Entity** dialog box appears.</span></span>
-   <span data-ttu-id="1b27e-137">Tapez **HireInfo**   pour le nom de l' **entité** et **PersonID** pour le nom de la **propriété de clé** .</span><span class="sxs-lookup"><span data-stu-id="1b27e-137">Type **HireInfo** for the **Entity name** and **PersonID** for the **Key Property** name.</span></span>
-   <span data-ttu-id="1b27e-138">Cliquez sur  **OK**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-138">Click **OK**.</span></span>
-   <span data-ttu-id="1b27e-139">Un nouveau type d'entité est créé et affiché sur l'aire de conception.</span><span class="sxs-lookup"><span data-stu-id="1b27e-139">A new entity type is created and displayed on the design surface.</span></span>
-   <span data-ttu-id="1b27e-140">Sélectionnez la **HireDate**   propriété HireDate du type d' **Person**   entité Person et appuyez sur les touches **CTRL + X** .</span><span class="sxs-lookup"><span data-stu-id="1b27e-140">Select the **HireDate** property of the **Person** entity type and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="1b27e-141">Sélectionnez l’entité **HireInfo**   et appuyez sur les touches **Ctrl + V** .</span><span class="sxs-lookup"><span data-stu-id="1b27e-141">Select the **HireInfo** entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="1b27e-142">Créer une association entre **Person** et **HireInfo**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-142">Create an association between **Person** and **HireInfo**.</span></span> <span data-ttu-id="1b27e-143">Pour ce faire, cliquez avec le bouton droit sur une zone vide de l’aire de conception, pointez sur **Ajouter nouveau**, puis cliquez sur **Association**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-143">To do this, right-click an empty area of the design surface, point to **Add New**, and click **Association**.</span></span>
-   <span data-ttu-id="1b27e-144">La boîte de dialogue **Ajouter une association**   s’affiche.</span><span class="sxs-lookup"><span data-stu-id="1b27e-144">The **Add Association** dialog box appears.</span></span> <span data-ttu-id="1b27e-145">Le nom **PersonHireInfo** est donné par défaut.</span><span class="sxs-lookup"><span data-stu-id="1b27e-145">The **PersonHireInfo** name is given by default.</span></span>
-   <span data-ttu-id="1b27e-146">Spécifiez la multiplicité **1 (un)** aux deux extrémités de la relation.</span><span class="sxs-lookup"><span data-stu-id="1b27e-146">Specify multiplicity **1(One)** on both ends of the relationship.</span></span>
-   <span data-ttu-id="1b27e-147">Appuyez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-147">Press **OK**.</span></span>

<span data-ttu-id="1b27e-148">L’étape suivante nécessite la fenêtre **Détails de mappage**   .</span><span class="sxs-lookup"><span data-stu-id="1b27e-148">The next step requires the **Mapping Details** window.</span></span> <span data-ttu-id="1b27e-149">Si vous ne voyez pas cette fenêtre, cliquez avec le bouton droit sur l’aire de conception, puis sélectionnez **Détails de mapping**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-149">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="1b27e-150">Sélectionnez le **HireInfo**   type d’entité HireInfo, puis cliquez sur \*\* &lt; Ajouter une &gt; table ou une vue\*\*   dans la fenêtre **Détails de mappage**   .</span><span class="sxs-lookup"><span data-stu-id="1b27e-150">Select the **HireInfo** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="1b27e-151">Sélectionnez **Person** dans la liste déroulante \*\* &lt; ajouter &gt; un champ de table ou de vue\*\*   .</span><span class="sxs-lookup"><span data-stu-id="1b27e-151">Select **Person** from the **&lt;Add a Table or View&gt;** field drop-down list.</span></span> <span data-ttu-id="1b27e-152">La liste contient les tables ou les vues auxquelles l’entité sélectionnée peut être mappée.</span><span class="sxs-lookup"><span data-stu-id="1b27e-152">The list contains tables or views to which the selected entity can be mapped.</span></span>
    <span data-ttu-id="1b27e-153">Les propriétés appropriées doivent être mappées par défaut.</span><span class="sxs-lookup"><span data-stu-id="1b27e-153">The appropriate properties should be mapped by default.</span></span>

    ![Propriétés de mappage](~/ef6/media/mapping.png)

-   <span data-ttu-id="1b27e-155">Sélectionnez l’Association **PersonHireInfo** sur l’aire de conception.</span><span class="sxs-lookup"><span data-stu-id="1b27e-155">Select the **PersonHireInfo** association on the design surface.</span></span>
-   <span data-ttu-id="1b27e-156">Cliquez avec le bouton droit sur l’Association sur l’aire de conception, puis sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-156">Right-click the association on the design surface and select **Properties**.</span></span>
-   <span data-ttu-id="1b27e-157">Dans la fenêtre **Propriétés** , sélectionnez la propriété **contraintes référentielles** , puis cliquez sur le bouton de sélection.</span><span class="sxs-lookup"><span data-stu-id="1b27e-157">In the **Properties** window, select the **Referential Constraints** property and click the ellipses button.</span></span>
-   <span data-ttu-id="1b27e-158">Sélectionnez **Person** dans la liste déroulante **principal** .</span><span class="sxs-lookup"><span data-stu-id="1b27e-158">Select **Person** from the **Principal** drop-down list.</span></span>
-   <span data-ttu-id="1b27e-159">Appuyez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="1b27e-159">Press **OK**.</span></span>

 

## <a name="use-the-model"></a><span data-ttu-id="1b27e-160">Utiliser le modèle</span><span class="sxs-lookup"><span data-stu-id="1b27e-160">Use the Model</span></span>

-   <span data-ttu-id="1b27e-161">Collez le code suivant dans la méthode main.</span><span class="sxs-lookup"><span data-stu-id="1b27e-161">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   <span data-ttu-id="1b27e-162">Compilez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="1b27e-162">Compile and run the application.</span></span>

<span data-ttu-id="1b27e-163">Les instructions T-SQL suivantes ont été exécutées sur la base de données **School** suite à l’exécution de cette application.</span><span class="sxs-lookup"><span data-stu-id="1b27e-163">The following T-SQL statements were executed against the **School** database as a result of running this application.</span></span> 

-   <span data-ttu-id="1b27e-164">L' **instruction INSERT** suivante a été exécutée à la suite de l’exécution du contexte. SaveChanges () et combine les données des entités **Person** et **HireInfo**</span><span class="sxs-lookup"><span data-stu-id="1b27e-164">The following **INSERT** was executed as a result of executing context.SaveChanges() and combines data from the **Person** and **HireInfo** entities</span></span>

    ![Insérer la combinaison des données Person et HireInfo](~/ef6/media/insert.png)

-   <span data-ttu-id="1b27e-166">La commande **Select** suivante a été exécutée à la suite de l’exécution du contexte. People. FirstOrDefault () et sélectionne uniquement les colonnes mappées à **Person**</span><span class="sxs-lookup"><span data-stu-id="1b27e-166">The following **SELECT** was executed as a result of executing context.People.FirstOrDefault() and selects just the columns mapped to **Person**</span></span>

    ![Sélectionnez 1](~/ef6/media/select1.png)

-   <span data-ttu-id="1b27e-168">La commande **Select** suivante a été exécutée suite à l’accès à la propriété de navigation existingPerson. Instructor et sélectionne uniquement les colonnes mappées à **HireInfo**</span><span class="sxs-lookup"><span data-stu-id="1b27e-168">The following **SELECT** was executed as a result of accessing the navigation property existingPerson.Instructor and selects just the columns mapped to **HireInfo**</span></span>

    ![Sélectionner 2](~/ef6/media/select2.png)
