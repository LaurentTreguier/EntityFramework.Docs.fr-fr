---
title: Procédures stockées CUD du concepteur-EF6
description: Procédures stockées CUD du concepteur dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/cud
ms.openlocfilehash: fae795922980452cf062a62e3cbb47de49b49c74
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073212"
---
# <a name="designer-cud-stored-procedures"></a><span data-ttu-id="241fe-103">Procédures stockées CUD du concepteur</span><span class="sxs-lookup"><span data-stu-id="241fe-103">Designer CUD Stored Procedures</span></span>

<span data-ttu-id="241fe-104">Cette procédure pas à pas explique comment mapper les \\ opérations de création d’insertion, de mise à jour et de suppression (CUD) d’un type d’entité aux procédures stockées à l’aide de l’Entity Framework Designer (concepteur EF).</span><span class="sxs-lookup"><span data-stu-id="241fe-104">This step-by-step walkthrough show how to map the create\\insert, update, and delete (CUD) operations of an entity type to stored procedures using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="241fe-105">Par défaut, le Entity Framework génère automatiquement les instructions SQL pour les opérations CUD, mais vous pouvez également mapper des procédures stockées à ces opérations.</span><span class="sxs-lookup"><span data-stu-id="241fe-105"> By default, the Entity Framework automatically generates the SQL statements for the CUD operations, but you can also map stored procedures to these operations.</span></span>  

<span data-ttu-id="241fe-106">Notez que Code First ne prend pas en charge le mappage à des procédures stockées ou à des fonctions.</span><span class="sxs-lookup"><span data-stu-id="241fe-106">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="241fe-107">Toutefois, vous pouvez appeler des procédures stockées ou des fonctions à l’aide de la méthode System. Data. Entity. DbSet. SqlQuery.</span><span class="sxs-lookup"><span data-stu-id="241fe-107">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="241fe-108">Exemple :</span><span class="sxs-lookup"><span data-stu-id="241fe-108">For example:</span></span>

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a><span data-ttu-id="241fe-109">Considérations relatives au mappage des opérations CUD à des procédures stockées</span><span class="sxs-lookup"><span data-stu-id="241fe-109">Considerations when Mapping the CUD Operations to Stored Procedures</span></span>

<span data-ttu-id="241fe-110">Lors du mappage des opérations CUD à des procédures stockées, les considérations suivantes s’appliquent :</span><span class="sxs-lookup"><span data-stu-id="241fe-110">When mapping the CUD operations to stored procedures, the following considerations apply:</span></span>

- <span data-ttu-id="241fe-111">Si vous mappez l’une des opérations CUD à une procédure stockée, mappez-les toutes.</span><span class="sxs-lookup"><span data-stu-id="241fe-111">If you are mapping one of the CUD operations to a stored procedure, map all of them.</span></span> <span data-ttu-id="241fe-112">Si vous ne mappez pas les trois, les opérations non mappées échouent si elles sont exécutées et une **UpdateException**   est levée.</span><span class="sxs-lookup"><span data-stu-id="241fe-112">If you do not map all three, the unmapped operations will fail if executed and an **UpdateException** will be thrown.</span></span>
- <span data-ttu-id="241fe-113">Vous devez mapper chaque paramètre de la procédure stockée à des propriétés d’entité.</span><span class="sxs-lookup"><span data-stu-id="241fe-113">You must map every parameter of the stored procedure to entity properties.</span></span>
- <span data-ttu-id="241fe-114">Si le serveur génère la valeur de clé primaire pour la ligne insérée, vous devez mapper cette valeur à la propriété de clé de l’entité.</span><span class="sxs-lookup"><span data-stu-id="241fe-114">If the server generates the primary key value for the inserted row, you must map this value back to the entity's key property.</span></span> <span data-ttu-id="241fe-115">Dans l’exemple qui suit, la **InsertPerson**   procédure stockée InsertPerson retourne la clé primaire nouvellement créée dans le cadre du jeu de résultats de la procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="241fe-115">In the example that follows, the **InsertPerson** stored procedure returns the newly created primary key as part of the stored procedure's result set.</span></span> <span data-ttu-id="241fe-116">La clé primaire est mappée à la clé d’entité (**PersonID**) à l’aide de la fonctionnalité \*\* &lt; Ajouter des liaisons de &gt; résultats\*\*   du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="241fe-116">The primary key is mapped to the entity key (**PersonID**) using the **&lt;Add Result Bindings&gt;** feature of the EF Designer.</span></span>
- <span data-ttu-id="241fe-117">Les appels de procédure stockée sont mappés 1:1 avec les entités dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="241fe-117">The stored procedure calls are mapped 1:1 with the entities in the conceptual model.</span></span> <span data-ttu-id="241fe-118">Par exemple, si vous implémentez une hiérarchie d’héritage dans votre modèle conceptuel, puis mappez les procédures stockées CUD pour les entités **parent** (base) et **enfant** (dérivées), l’enregistrement des modifications **enfants** appellera uniquement les procédures stockées de l' **enfant**, mais pas les appels des procédures stockées du **parent**.</span><span class="sxs-lookup"><span data-stu-id="241fe-118">For example, if you implement an inheritance hierarchy in your conceptual model and then map the CUD stored procedures for the **Parent** (base) and the **Child** (derived) entities, saving the **Child** changes will only call the **Child**’s stored procedures, it will not trigger the **Parent**’s stored procedures calls.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="241fe-119">Prérequis</span><span class="sxs-lookup"><span data-stu-id="241fe-119">Prerequisites</span></span>

<span data-ttu-id="241fe-120">Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="241fe-120">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="241fe-121">Une version récente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="241fe-121">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="241fe-122">[Exemple de base de données School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="241fe-122">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="241fe-123">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="241fe-123">Set up the Project</span></span>

- <span data-ttu-id="241fe-124">Ouvrez Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="241fe-124">Open Visual Studio 2012.</span></span>
- <span data-ttu-id="241fe-125">Sélectionnez **fichier- &gt; nouveau- &gt; projet**</span><span class="sxs-lookup"><span data-stu-id="241fe-125">Select **File-&gt; New -&gt; Project**</span></span>
- <span data-ttu-id="241fe-126">Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .</span><span class="sxs-lookup"><span data-stu-id="241fe-126">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
- <span data-ttu-id="241fe-127">Entrez **CUDSProcsSample**   comme nom.</span><span class="sxs-lookup"><span data-stu-id="241fe-127">Enter **CUDSProcsSample** as the name.</span></span>
- <span data-ttu-id="241fe-128">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="241fe-128">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="241fe-129">Création d'un modèle</span><span class="sxs-lookup"><span data-stu-id="241fe-129">Create a Model</span></span>

- <span data-ttu-id="241fe-130">Dans Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **ajouter- &gt; nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="241fe-130">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
- <span data-ttu-id="241fe-131">Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.</span><span class="sxs-lookup"><span data-stu-id="241fe-131">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
- <span data-ttu-id="241fe-132">Entrez **CUDSProcs. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="241fe-132">Enter **CUDSProcs.edmx** for the file name, and then click **Add**.</span></span>
- <span data-ttu-id="241fe-133">Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant**.</span><span class="sxs-lookup"><span data-stu-id="241fe-133">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
- <span data-ttu-id="241fe-134">Cliquez sur **nouvelle connexion**.</span><span class="sxs-lookup"><span data-stu-id="241fe-134">Click **New Connection**.</span></span> <span data-ttu-id="241fe-135">Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, **( \\ mssqllocaldb)**, sélectionnez la méthode d’authentification, tapez **School**   comme nom de la base de données, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="241fe-135">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="241fe-136">La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="241fe-136">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
- <span data-ttu-id="241fe-137">Dans la boîte de dialogue choisir vos objets de base de données, sous le nœud **tables**   , sélectionnez la table **Person** .</span><span class="sxs-lookup"><span data-stu-id="241fe-137">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person** table.</span></span>
- <span data-ttu-id="241fe-138">En outre, sélectionnez les procédures stockées suivantes sous le nœud **procédures stockées et fonctions** : **DeletePerson**, **InsertPerson**et **UpdatePerson**.</span><span class="sxs-lookup"><span data-stu-id="241fe-138">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **DeletePerson**, **InsertPerson**, and **UpdatePerson**.</span></span>
- <span data-ttu-id="241fe-139">À compter de Visual Studio 2012, le concepteur EF prend en charge l’importation en bloc des procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="241fe-139">Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures.</span></span> <span data-ttu-id="241fe-140">L' **importation des procédures stockées et des fonctions sélectionnées dans le modèle d’entité** est activée par défaut.</span><span class="sxs-lookup"><span data-stu-id="241fe-140">The **Import selected stored procedures and functions into the entity model** is checked by default.</span></span> <span data-ttu-id="241fe-141">Étant donné que dans cet exemple nous avons des procédures stockées qui insèrent, mettent à jour et suppriment des types d’entité, nous ne voulons pas les importer et décochent cette case.</span><span class="sxs-lookup"><span data-stu-id="241fe-141">Since in this example we have stored procedures that insert, update, and delete entity types, we do not want to import them and will uncheck this checkbox.</span></span>

    ![Importer des proc. S](~/ef6/media/importsprocs.jpg)

- <span data-ttu-id="241fe-143">Cliquez sur **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="241fe-143">Click **Finish**.</span></span>
    <span data-ttu-id="241fe-144">Le concepteur EF, qui fournit une aire de conception pour la modification de votre modèle, est affiché.</span><span class="sxs-lookup"><span data-stu-id="241fe-144">The EF Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-the-person-entity-to-stored-procedures"></a><span data-ttu-id="241fe-145">Mapper l’entité Person aux procédures stockées</span><span class="sxs-lookup"><span data-stu-id="241fe-145">Map the Person Entity to Stored Procedures</span></span>

- <span data-ttu-id="241fe-146">Cliquez avec le bouton droit sur le type d’entité **Person**   et sélectionnez **mappage de procédure stockée**.</span><span class="sxs-lookup"><span data-stu-id="241fe-146">Right-click the **Person** entity type and select **Stored Procedure Mapping**.</span></span>
- <span data-ttu-id="241fe-147">Les mappages de procédure stockée s’affichent dans la fenêtre **Détails de mappage**   .</span><span class="sxs-lookup"><span data-stu-id="241fe-147">The stored procedure mappings appear in the **Mapping Details** window.</span></span>
- <span data-ttu-id="241fe-148">Cliquez sur \*\* &lt; Sélectionner une &gt; fonction d’insertion\*\*.</span><span class="sxs-lookup"><span data-stu-id="241fe-148">Click **&lt;Select Insert Function&gt;**.</span></span>
    <span data-ttu-id="241fe-149">Le champ devient une liste déroulante des procédures stockées dans le modèle de stockage qui peut être mappé aux types d'entité dans le modèle conceptuel.</span><span class="sxs-lookup"><span data-stu-id="241fe-149">The field becomes a drop-down list of the stored procedures in the storage model that can be mapped to entity types in the conceptual model.</span></span>
    <span data-ttu-id="241fe-150">Sélectionnez **InsertPerson**   dans la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="241fe-150">Select **InsertPerson** from the drop-down list.</span></span>
- <span data-ttu-id="241fe-151">Les mappages par défaut entre les paramètres des procédures stockées et les propriétés d'entité apparaissent.</span><span class="sxs-lookup"><span data-stu-id="241fe-151">Default mappings between stored procedure parameters and entity properties appear.</span></span> <span data-ttu-id="241fe-152">Notez que les flèches indiquent le sens du mappage : les valeurs des propriétés sont fournies aux paramètres des procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="241fe-152">Note that arrows indicate the mapping direction: Property values are supplied to stored procedure parameters.</span></span>
- <span data-ttu-id="241fe-153">Cliquez sur \*\* &lt; Ajouter une &gt; liaison de résultat\*\*.</span><span class="sxs-lookup"><span data-stu-id="241fe-153">Click **&lt;Add Result Binding&gt;**.</span></span>
- <span data-ttu-id="241fe-154">Tapez **NewPersonID**, le nom du paramètre retourné par la **InsertPerson**   procédure stockée InsertPerson.</span><span class="sxs-lookup"><span data-stu-id="241fe-154">Type **NewPersonID**, the name of the parameter returned by the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="241fe-155">Veillez à ne pas taper les espaces de début ou de fin.</span><span class="sxs-lookup"><span data-stu-id="241fe-155">Make sure not to type leading or trailing spaces.</span></span>
- <span data-ttu-id="241fe-156">Appuyez sur **entrée**.</span><span class="sxs-lookup"><span data-stu-id="241fe-156">Press **Enter**.</span></span>
- <span data-ttu-id="241fe-157">Par défaut, **NewPersonID**   est mappé à la clé d’entité **PersonID**.</span><span class="sxs-lookup"><span data-stu-id="241fe-157">By default, **NewPersonID** is mapped to the entity key **PersonID**.</span></span> <span data-ttu-id="241fe-158">Notez qu'une flèche indique le sens du mappage : la valeur de la colonne de résultats est fournie à la propriété.</span><span class="sxs-lookup"><span data-stu-id="241fe-158">Note that an arrow indicates the direction of the mapping: The value of the result column is supplied to the property.</span></span>

    ![Détails de mappage](~/ef6/media/mappingdetails.png)

- <span data-ttu-id="241fe-160">Cliquez sur \*\* &lt; Sélectionner une &gt; fonction de mise à jour\*\*   et sélectionnez **UpdatePerson**   dans la liste déroulante résultante.</span><span class="sxs-lookup"><span data-stu-id="241fe-160">Click **&lt;Select Update Function&gt;** and select **UpdatePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="241fe-161">Les mappages par défaut entre les paramètres des procédures stockées et les propriétés d'entité apparaissent.</span><span class="sxs-lookup"><span data-stu-id="241fe-161">Default mappings between stored procedure parameters and entity properties appear.</span></span>
- <span data-ttu-id="241fe-162">Cliquez sur \*\* &lt; Sélectionner la &gt; fonction de suppression\*\*   , puis sélectionnez **DeletePerson**   dans la liste déroulante résultante.</span><span class="sxs-lookup"><span data-stu-id="241fe-162">Click **&lt;Select Delete Function&gt;** and select **DeletePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="241fe-163">Les mappages par défaut entre les paramètres des procédures stockées et les propriétés d'entité apparaissent.</span><span class="sxs-lookup"><span data-stu-id="241fe-163">Default mappings between stored procedure parameters and entity properties appear.</span></span>

<span data-ttu-id="241fe-164">Les opérations d’insertion, de mise à jour et de suppression du type d’entité **Person**   sont maintenant mappées à des procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="241fe-164">The insert, update, and delete operations of the **Person** entity type are now mapped to stored procedures.</span></span>

<span data-ttu-id="241fe-165">Si vous souhaitez activer le contrôle d’accès concurrentiel lors de la mise à jour ou de la suppression d’une entité avec des procédures stockées, utilisez l’une des options suivantes :</span><span class="sxs-lookup"><span data-stu-id="241fe-165">If you want to enable concurrency checking when updating or deleting an entity with stored procedures, use one of the following options:</span></span>

- <span data-ttu-id="241fe-166">Utilisez un paramètre de **sortie** pour retourner le nombre de lignes affectées à partir de la procédure stockée et cochez la case **lignes affectées du paramètre**en   regard du nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="241fe-166">Use an **OUTPUT** parameter to return the number of affected rows from the stored procedure and check the **Rows Affected Parameter** checkbox next to the parameter name.</span></span> <span data-ttu-id="241fe-167">Si la valeur retournée est zéro lorsque l’opération est appelée, une  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)est   levée.</span><span class="sxs-lookup"><span data-stu-id="241fe-167">If the value returned is zero when the operation is called, an  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) will be thrown.</span></span>
- <span data-ttu-id="241fe-168">Cochez la case **utiliser la valeur d’origine** à côté d’une propriété que vous souhaitez utiliser pour la vérification de l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="241fe-168">Check the **Use Original Value** checkbox next to a property that you want to use for concurrency checking.</span></span> <span data-ttu-id="241fe-169">Lors d’une tentative de mise à jour, la valeur de la propriété qui a été lue à l’origine dans la base de données sera utilisée lors de l’écriture de données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="241fe-169">When an update is attempted, the value of the property that was originally read from the database will be used when writing data back to the database.</span></span> <span data-ttu-id="241fe-170">Si la valeur ne correspond pas à la valeur de la base de données, une **OptimisticConcurrencyException**   est levée.</span><span class="sxs-lookup"><span data-stu-id="241fe-170">If the value does not match the value in the database, an **OptimisticConcurrencyException** will be thrown.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="241fe-171">Utiliser le modèle</span><span class="sxs-lookup"><span data-stu-id="241fe-171">Use the Model</span></span>

<span data-ttu-id="241fe-172">Ouvrez le fichier **Program.cs** dans lequel la méthode **main** est définie.</span><span class="sxs-lookup"><span data-stu-id="241fe-172">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="241fe-173">Ajoutez le code suivant à la fonction main.</span><span class="sxs-lookup"><span data-stu-id="241fe-173">Add the following code into the Main function.</span></span>

<span data-ttu-id="241fe-174">Le code crée un nouvel objet **Person** , puis met à jour l’objet, puis supprime l’objet.</span><span class="sxs-lookup"><span data-stu-id="241fe-174">The code creates a new **Person** object, then updates the object, and finally deletes the object.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

- <span data-ttu-id="241fe-175">Compilez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="241fe-175">Compile and run the application.</span></span> <span data-ttu-id="241fe-176">Le programme produit la sortie suivante \*</span><span class="sxs-lookup"><span data-stu-id="241fe-176">The program produces the following output \*</span></span>

> [!NOTE]
> <span data-ttu-id="241fe-177">PersonID est généré automatiquement par le serveur. vous verrez probablement un autre numéro \*</span><span class="sxs-lookup"><span data-stu-id="241fe-177">PersonID is auto-generated by the server, so you will most likely see a different number\*</span></span>

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

<span data-ttu-id="241fe-178">Si vous utilisez la version finale de Visual Studio, vous pouvez utiliser IntelliTrace avec le débogueur pour voir les instructions SQL qui sont exécutées.</span><span class="sxs-lookup"><span data-stu-id="241fe-178">If you are working with the Ultimate version of Visual Studio, you can use Intellitrace with the debugger to see the SQL statements that get executed.</span></span>

![Déboguer avec IntelliTrace](~/ef6/media/intellitrace.png)
