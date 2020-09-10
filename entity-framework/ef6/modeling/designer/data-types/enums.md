---
title: Prise en charge d’enum-concepteur EF-EF6
description: Prise en charge d’enum-concepteur EF dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 316e8160cfd69d9912d63260471eee3df0eb58f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620515"
---
# <a name="enum-support---ef-designer"></a><span data-ttu-id="03971-103">Prise en charge d’enum-concepteur EF</span><span class="sxs-lookup"><span data-stu-id="03971-103">Enum Support - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="03971-104">**EF5 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="03971-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="03971-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="03971-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="03971-106">Cette vidéo et la procédure pas à pas montrent comment utiliser les types ENUM avec l’Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="03971-106">This video and step-by-step walkthrough shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="03971-107">Il montre également comment utiliser des enums dans une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="03971-107">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="03971-108">Cette procédure pas à pas utilise Model First pour créer une base de données, mais le concepteur EF peut également être utilisé avec le flux de travail [Database First](xref:ef6/modeling/designer/workflows/database-first) pour mapper à une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="03971-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="03971-109">La prise en charge des énumérations a été introduite dans Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="03971-109">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="03971-110">Pour utiliser les nouvelles fonctionnalités telles que les énumérations, les types de données spatiales et les fonctions table, vous devez cibler .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="03971-110">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="03971-111">Visual Studio 2012 cible .NET 4,5 par défaut.</span><span class="sxs-lookup"><span data-stu-id="03971-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="03971-112">Dans Entity Framework, une énumération peut avoir les types sous-jacents suivants : **Byte**, **Int16**, **Int32**, **Int64** ou **SByte**.</span><span class="sxs-lookup"><span data-stu-id="03971-112">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="03971-113">Regarder la vidéo</span><span class="sxs-lookup"><span data-stu-id="03971-113">Watch the Video</span></span>
<span data-ttu-id="03971-114">Cette vidéo montre comment utiliser les types ENUM avec l’Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="03971-114">This video shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="03971-115">Il montre également comment utiliser des enums dans une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="03971-115">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="03971-116">**Présenté par**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="03971-116">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="03971-117">**Vidéo**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span><span class="sxs-lookup"><span data-stu-id="03971-117">**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="03971-118">Prérequis</span><span class="sxs-lookup"><span data-stu-id="03971-118">Pre-Requisites</span></span>

<span data-ttu-id="03971-119">Pour effectuer cette procédure pas à pas, vous devez avoir installé Visual Studio 2012, Ultimate, Premium, Professional ou Web Express Edition.</span><span class="sxs-lookup"><span data-stu-id="03971-119">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="03971-120">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="03971-120">Set up the Project</span></span>

1.  <span data-ttu-id="03971-121">Ouvrir Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="03971-121">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="03971-122">Dans le menu **fichier** , pointez sur **nouveau**, puis cliquez sur **projet** .</span><span class="sxs-lookup"><span data-stu-id="03971-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="03971-123">Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .</span><span class="sxs-lookup"><span data-stu-id="03971-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="03971-124">Entrez **EnumEFDesigner** comme nom du projet, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="03971-124">Enter **EnumEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="03971-125">Créer un nouveau modèle à l’aide du concepteur EF</span><span class="sxs-lookup"><span data-stu-id="03971-125">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="03971-126">Cliquez avec le bouton droit sur le nom du projet dans Explorateur de solutions, pointez sur **Ajouter**, puis cliquez sur **nouvel élément** .</span><span class="sxs-lookup"><span data-stu-id="03971-126">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="03971-127">Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.</span><span class="sxs-lookup"><span data-stu-id="03971-127">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="03971-128">Entrez **EnumTestModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="03971-128">Enter **EnumTestModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="03971-129">Dans la page Entity Data Model de l’Assistant, sélectionnez **modèle vide** dans la boîte de dialogue choisir le contenu du modèle.</span><span class="sxs-lookup"><span data-stu-id="03971-129">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="03971-130">Cliquez sur **Terminer**</span><span class="sxs-lookup"><span data-stu-id="03971-130">Click **Finish**</span></span>

<span data-ttu-id="03971-131">Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.</span><span class="sxs-lookup"><span data-stu-id="03971-131">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="03971-132">L'assistant exécute les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="03971-132">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="03971-133">Génère le fichier EnumTestModel. edmx qui définit le modèle conceptuel, le modèle de stockage et le mappage entre eux.</span><span class="sxs-lookup"><span data-stu-id="03971-133">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="03971-134">Définit la propriété de traitement des artefacts de métadonnées du fichier. edmx à incorporer dans l’assembly de sortie afin que les fichiers de métadonnées générés soient incorporés dans l’assembly.</span><span class="sxs-lookup"><span data-stu-id="03971-134">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="03971-135">Ajoute une référence aux assemblys suivants : EntityFramework, System. ComponentModel. DataAnnotations et System. Data. Entity.</span><span class="sxs-lookup"><span data-stu-id="03971-135">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="03971-136">Crée des fichiers EnumTestModel.tt et EnumTestModel.Context.tt et les ajoute sous le fichier. edmx.</span><span class="sxs-lookup"><span data-stu-id="03971-136">Creates EnumTestModel.tt and EnumTestModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="03971-137">Ces fichiers de modèle T4 génèrent le code qui définit le type dérivé DbContext et les types POCO qui mappent aux entités dans le modèle. edmx.</span><span class="sxs-lookup"><span data-stu-id="03971-137">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model.</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="03971-138">Ajouter un nouveau type d’entité</span><span class="sxs-lookup"><span data-stu-id="03971-138">Add a New Entity Type</span></span>

1.  <span data-ttu-id="03971-139">Cliquez avec le bouton droit sur une zone vide de l’aire de conception, sélectionnez **ajouter- &gt; entité**; la boîte de dialogue nouvelle entité s’affiche.</span><span class="sxs-lookup"><span data-stu-id="03971-139">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="03971-140">Spécifiez **Department** comme nom de type et spécifiez **DepartmentID** pour le nom de la propriété de clé, laissez le type **Int32**</span><span class="sxs-lookup"><span data-stu-id="03971-140">Specify **Department** for the type name and specify **DepartmentID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="03971-141">Cliquez sur **OK**</span><span class="sxs-lookup"><span data-stu-id="03971-141">Click **OK**</span></span>
4.  <span data-ttu-id="03971-142">Cliquez avec le bouton droit sur l’entité, puis sélectionnez **Ajouter une nouvelle &gt; propriété-scalaire** .</span><span class="sxs-lookup"><span data-stu-id="03971-142">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="03971-143">Renommez la nouvelle propriété en **Name**</span><span class="sxs-lookup"><span data-stu-id="03971-143">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="03971-144">Remplacez le type de la nouvelle propriété par **Int32** (par défaut, la nouvelle propriété est de type chaîne) pour modifier le type, ouvrez le fenêtre Propriétés et remplacez la propriété type par **Int32** .</span><span class="sxs-lookup"><span data-stu-id="03971-144">Change the type of the new property to **Int32** (by default, the new property is of String type) To change the type, open the Properties window and change the Type property to **Int32**</span></span>
7.  <span data-ttu-id="03971-145">Ajoutez une autre propriété scalaire et renommez-la **budget**, modifiez le type en **Decimal**</span><span class="sxs-lookup"><span data-stu-id="03971-145">Add another scalar property and rename it to **Budget**, change the type to **Decimal**</span></span>

## <a name="add-an-enum-type"></a><span data-ttu-id="03971-146">Ajouter un type enum</span><span class="sxs-lookup"><span data-stu-id="03971-146">Add an Enum Type</span></span>

1.  <span data-ttu-id="03971-147">Dans la Entity Framework Designer, cliquez avec le bouton droit sur la propriété Name, sélectionnez **convertir en enum** .</span><span class="sxs-lookup"><span data-stu-id="03971-147">In the Entity Framework Designer, right-click the Name property, select **Convert to enum**</span></span>

    ![Convertir en enum](~/ef6/media/converttoenum.png)

2.  <span data-ttu-id="03971-149">Dans la boîte de dialogue **Ajouter un enum** , tapez **DepartmentNames** pour le nom du type d’énumération, remplacez le type sous-jacent par **Int32**, puis ajoutez les membres suivants au type : anglais, mathématiques et économie</span><span class="sxs-lookup"><span data-stu-id="03971-149">In the **Add Enum** dialog box type **DepartmentNames** for the Enum Type Name, change the Underlying Type to **Int32**, and then add the following members to the type: English, Math, and Economics</span></span>

    ![Ajouter un type enum](~/ef6/media/addenumtype.png)

3.  <span data-ttu-id="03971-151">Appuyez sur **OK**</span><span class="sxs-lookup"><span data-stu-id="03971-151">Press **OK**</span></span>
4.  <span data-ttu-id="03971-152">Enregistrer le modèle et générer le projet</span><span class="sxs-lookup"><span data-stu-id="03971-152">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="03971-153">Lorsque vous générez, les avertissements relatifs aux entités et associations non mappés peuvent apparaître dans le Liste d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="03971-153">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="03971-154">Vous pouvez ignorer ces avertissements, car une fois que vous avez choisi de générer la base de données à partir du modèle, les erreurs disparaissent.</span><span class="sxs-lookup"><span data-stu-id="03971-154">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

<span data-ttu-id="03971-155">Si vous examinez le Fenêtre Propriétés, vous remarquerez que le type de la propriété Name a été remplacé par **DepartmentNames** et que le type enum récemment ajouté a été ajouté à la liste des types.</span><span class="sxs-lookup"><span data-stu-id="03971-155">If you look at the Properties window, you will notice that the type of the Name property was changed to **DepartmentNames** and the newly added enum type was added to the list of types.</span></span>

<span data-ttu-id="03971-156">Si vous basculez vers la fenêtre Explorateur de modèles, vous verrez que le type a été également ajouté au nœud types d’énumération.</span><span class="sxs-lookup"><span data-stu-id="03971-156">If you switch to the Model Browser window, you will see that the type was also added to the Enum Types node.</span></span>

![Explorateur de modèles](~/ef6/media/modelbrowser.png)

>[!NOTE]
> <span data-ttu-id="03971-158">Vous pouvez également ajouter de nouveaux types d’énumération à partir de cette fenêtre en cliquant sur le bouton droit de la souris et en sélectionnant **Ajouter un type d’énumération**.</span><span class="sxs-lookup"><span data-stu-id="03971-158">You can also add new enum types from this window by clicking the right mouse button and selecting **Add Enum Type**.</span></span> <span data-ttu-id="03971-159">Une fois le type créé, il apparaît dans la liste des types et vous pouvez l’associer à une propriété.</span><span class="sxs-lookup"><span data-stu-id="03971-159">Once the type is created it will appear in the list of types and you would be able to associate with a property</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="03971-160">Générer la base de données à partir du modèle</span><span class="sxs-lookup"><span data-stu-id="03971-160">Generate Database from Model</span></span>

<span data-ttu-id="03971-161">Nous pouvons maintenant générer une base de données basée sur le modèle.</span><span class="sxs-lookup"><span data-stu-id="03971-161">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="03971-162">Cliquez avec le bouton droit sur un espace vide sur l’aire de Entity Designer et sélectionnez **générer la base de données à partir du modèle** .</span><span class="sxs-lookup"><span data-stu-id="03971-162">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="03971-163">La boîte de dialogue choisir votre connexion de données de l’Assistant générer la base de données s’affiche. cliquez sur le bouton **nouvelle connexion** , spécifiez (base de données **) \\ mssqllocaldb** pour le nom du serveur et **EnumTest** pour la base de données, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="03971-163">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **EnumTest** for the database and click **OK**</span></span>
3.  <span data-ttu-id="03971-164">Une boîte de dialogue vous demandant si vous souhaitez créer une nouvelle base de données s’affiche, cliquez sur **Oui**.</span><span class="sxs-lookup"><span data-stu-id="03971-164">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="03971-165">Cliquez sur **suivant** pour que l’Assistant Création d’une base de données génère le langage de définition de données (DDL) pour la création d’une base de données. la DDL générée est affichée dans la boîte de dialogue Résumé et paramètres. Notez que le DDL ne contient pas de définition pour une table qui correspond au type d’énumération.</span><span class="sxs-lookup"><span data-stu-id="03971-165">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="03971-166">Cliquez sur **Terminer** pour ne pas exécuter le script DDL.</span><span class="sxs-lookup"><span data-stu-id="03971-166">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="03971-167">L’Assistant Création d’une base de données effectue les opérations suivantes : ouvre **EnumTest. edmx. SQL** dans l’éditeur T-SQL génère les sections de schéma de stockage et de mappage du fichier edmx ajoute les informations de chaîne de connexion au fichier App.config</span><span class="sxs-lookup"><span data-stu-id="03971-167">The Create Database Wizard does the following: Opens the **EnumTest.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="03971-168">Cliquez sur le bouton droit de la souris dans l’éditeur T-SQL et sélectionnez **exécuter** la boîte de dialogue se connecter au serveur, entrez les informations de connexion de l’étape 2, puis cliquez sur **se connecter** .</span><span class="sxs-lookup"><span data-stu-id="03971-168">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="03971-169">Pour afficher le schéma généré, cliquez avec le bouton droit sur le nom de la base de données dans Explorateur d’objets SQL Server puis sélectionnez **Actualiser** .</span><span class="sxs-lookup"><span data-stu-id="03971-169">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="03971-170">Conserver et récupérer des données</span><span class="sxs-lookup"><span data-stu-id="03971-170">Persist and Retrieve Data</span></span>

<span data-ttu-id="03971-171">Ouvrez le fichier Program.cs dans lequel la méthode main est définie.</span><span class="sxs-lookup"><span data-stu-id="03971-171">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="03971-172">Ajoutez le code suivant à la fonction main.</span><span class="sxs-lookup"><span data-stu-id="03971-172">Add the following code into the Main function.</span></span> <span data-ttu-id="03971-173">Le code ajoute un nouvel objet Department au contexte.</span><span class="sxs-lookup"><span data-stu-id="03971-173">The code adds a new Department object to the context.</span></span> <span data-ttu-id="03971-174">Il enregistre ensuite les données.</span><span class="sxs-lookup"><span data-stu-id="03971-174">It then saves the data.</span></span> <span data-ttu-id="03971-175">Le code exécute également une requête LINQ qui retourne un service dont le nom est DepartmentNames. English.</span><span class="sxs-lookup"><span data-stu-id="03971-175">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="03971-176">Compilez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="03971-176">Compile and run the application.</span></span> <span data-ttu-id="03971-177">Le programme génère la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="03971-177">The program produces the following output:</span></span>

```console
DepartmentID: 1 Name: English
```

<span data-ttu-id="03971-178">Pour afficher les données de la base de données, cliquez avec le bouton droit sur le nom de la base de données dans Explorateur d’objets SQL Server, puis sélectionnez **Actualiser**.</span><span class="sxs-lookup"><span data-stu-id="03971-178">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="03971-179">Ensuite, cliquez sur le bouton droit de la souris sur la table et sélectionnez **afficher les données**.</span><span class="sxs-lookup"><span data-stu-id="03971-179">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="03971-180">Résumé</span><span class="sxs-lookup"><span data-stu-id="03971-180">Summary</span></span>

<span data-ttu-id="03971-181">Dans cette procédure pas à pas, nous avons vu comment mapper des types énumération à l’aide de la Entity Framework Designer et comment utiliser des enums dans le code.</span><span class="sxs-lookup"><span data-stu-id="03971-181">In this walkthrough we looked at how to map enum types using the Entity Framework Designer and how to use enums in code.</span></span> 
