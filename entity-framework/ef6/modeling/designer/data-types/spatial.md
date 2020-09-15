---
title: Spatial-EF designer-EF6
description: Concepteur spatial-EF dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 6524e3d2be6d5028ab12d6bfd7c65ce3a232a07a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073082"
---
# <a name="spatial---ef-designer"></a><span data-ttu-id="faa11-103">Concepteur spatial-EF</span><span class="sxs-lookup"><span data-stu-id="faa11-103">Spatial - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="faa11-104">**EF5 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="faa11-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="faa11-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="faa11-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="faa11-106">La vidéo et la procédure pas à pas montrent comment mapper des types spatiaux avec l’Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="faa11-106">The video and step-by-step walkthrough shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="faa11-107">Il montre également comment utiliser une requête LINQ pour rechercher une distance entre deux emplacements.</span><span class="sxs-lookup"><span data-stu-id="faa11-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="faa11-108">Cette procédure pas à pas utilise Model First pour créer une base de données, mais le concepteur EF peut également être utilisé avec le flux de travail [Database First](xref:ef6/modeling/designer/workflows/database-first) pour mapper à une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="faa11-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="faa11-109">La prise en charge des types spatiaux a été introduite dans Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="faa11-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="faa11-110">Notez que pour utiliser les nouvelles fonctionnalités telles que le type spatial, les énumérations et les fonctions table, vous devez cibler .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="faa11-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="faa11-111">Visual Studio 2012 cible .NET 4,5 par défaut.</span><span class="sxs-lookup"><span data-stu-id="faa11-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="faa11-112">Pour utiliser des types de données spatiales, vous devez également utiliser un fournisseur de Entity Framework qui a une prise en charge spatiale.</span><span class="sxs-lookup"><span data-stu-id="faa11-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="faa11-113">Pour plus d’informations, consultez [prise en charge des fournisseurs pour les types spatiaux](xref:ef6/fundamentals/providers/spatial-support) .</span><span class="sxs-lookup"><span data-stu-id="faa11-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="faa11-114">Il existe deux types de données spatiales principales : Geography et Geometry.</span><span class="sxs-lookup"><span data-stu-id="faa11-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="faa11-115">Le type de données geography stocke des données ellipsoïdal (par exemple, des coordonnées de latitude et de longitude GPS).</span><span class="sxs-lookup"><span data-stu-id="faa11-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="faa11-116">Le type de données geometry représente le système de coordonnées euclidienne (Flat).</span><span class="sxs-lookup"><span data-stu-id="faa11-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="faa11-117">Regarder la vidéo</span><span class="sxs-lookup"><span data-stu-id="faa11-117">Watch the video</span></span>
<span data-ttu-id="faa11-118">Cette vidéo montre comment mapper des types spatiaux avec l’Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="faa11-118">This video shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="faa11-119">Il montre également comment utiliser une requête LINQ pour rechercher une distance entre deux emplacements.</span><span class="sxs-lookup"><span data-stu-id="faa11-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="faa11-120">**Présenté par**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="faa11-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="faa11-121">**Vidéo**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span><span class="sxs-lookup"><span data-stu-id="faa11-121">**Video**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="faa11-122">Prérequis</span><span class="sxs-lookup"><span data-stu-id="faa11-122">Pre-Requisites</span></span>

<span data-ttu-id="faa11-123">Pour effectuer cette procédure pas à pas, vous devez avoir installé Visual Studio 2012, Ultimate, Premium, Professional ou Web Express Edition.</span><span class="sxs-lookup"><span data-stu-id="faa11-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="faa11-124">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="faa11-124">Set up the Project</span></span>

1.  <span data-ttu-id="faa11-125">Ouvrir Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="faa11-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="faa11-126">Dans le menu **fichier** , pointez sur **nouveau**, puis cliquez sur **projet** .</span><span class="sxs-lookup"><span data-stu-id="faa11-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="faa11-127">Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .</span><span class="sxs-lookup"><span data-stu-id="faa11-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="faa11-128">Entrez **SpatialEFDesigner** comme nom du projet, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="faa11-128">Enter **SpatialEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="faa11-129">Créer un nouveau modèle à l’aide du concepteur EF</span><span class="sxs-lookup"><span data-stu-id="faa11-129">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="faa11-130">Cliquez avec le bouton droit sur le nom du projet dans Explorateur de solutions, pointez sur **Ajouter**, puis cliquez sur **nouvel élément** .</span><span class="sxs-lookup"><span data-stu-id="faa11-130">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="faa11-131">Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.</span><span class="sxs-lookup"><span data-stu-id="faa11-131">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="faa11-132">Entrez **UniversityModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="faa11-132">Enter **UniversityModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="faa11-133">Dans la page Entity Data Model de l’Assistant, sélectionnez **modèle vide** dans la boîte de dialogue choisir le contenu du modèle.</span><span class="sxs-lookup"><span data-stu-id="faa11-133">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="faa11-134">Cliquez sur **Terminer**</span><span class="sxs-lookup"><span data-stu-id="faa11-134">Click **Finish**</span></span>

<span data-ttu-id="faa11-135">Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.</span><span class="sxs-lookup"><span data-stu-id="faa11-135">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="faa11-136">L'assistant exécute les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="faa11-136">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="faa11-137">Génère le fichier EnumTestModel. edmx qui définit le modèle conceptuel, le modèle de stockage et le mappage entre eux.</span><span class="sxs-lookup"><span data-stu-id="faa11-137">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="faa11-138">Définit la propriété de traitement des artefacts de métadonnées du fichier. edmx à incorporer dans l’assembly de sortie afin que les fichiers de métadonnées générés soient incorporés dans l’assembly.</span><span class="sxs-lookup"><span data-stu-id="faa11-138">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="faa11-139">Ajoute une référence aux assemblys suivants : EntityFramework, System. ComponentModel. DataAnnotations et System. Data. Entity.</span><span class="sxs-lookup"><span data-stu-id="faa11-139">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="faa11-140">Crée des fichiers UniversityModel.tt et UniversityModel.Context.tt et les ajoute sous le fichier. edmx.</span><span class="sxs-lookup"><span data-stu-id="faa11-140">Creates UniversityModel.tt and UniversityModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="faa11-141">Ces fichiers de modèle T4 génèrent le code qui définit le type dérivé DbContext et les types POCO qui mappent aux entités dans le modèle. edmx</span><span class="sxs-lookup"><span data-stu-id="faa11-141">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="faa11-142">Ajouter un nouveau type d’entité</span><span class="sxs-lookup"><span data-stu-id="faa11-142">Add a New Entity Type</span></span>

1.  <span data-ttu-id="faa11-143">Cliquez avec le bouton droit sur une zone vide de l’aire de conception, sélectionnez **ajouter- &gt; entité**; la boîte de dialogue nouvelle entité s’affiche.</span><span class="sxs-lookup"><span data-stu-id="faa11-143">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="faa11-144">Spécifiez **University** comme nom de type et spécifiez **UniversityID** pour le nom de la propriété de clé, laissez le type **Int32**</span><span class="sxs-lookup"><span data-stu-id="faa11-144">Specify **University** for the type name and specify **UniversityID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="faa11-145">Cliquez sur **OK**</span><span class="sxs-lookup"><span data-stu-id="faa11-145">Click **OK**</span></span>
4.  <span data-ttu-id="faa11-146">Cliquez avec le bouton droit sur l’entité, puis sélectionnez **Ajouter une nouvelle &gt; propriété-scalaire** .</span><span class="sxs-lookup"><span data-stu-id="faa11-146">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="faa11-147">Renommez la nouvelle propriété en **Name**</span><span class="sxs-lookup"><span data-stu-id="faa11-147">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="faa11-148">Ajoutez une autre propriété scalaire et renommez-la **emplacement** ouvrir le fenêtre Propriétés et remplacez le type de la nouvelle propriété par **Geography**</span><span class="sxs-lookup"><span data-stu-id="faa11-148">Add another scalar property and rename it to **Location** Open the Properties window and change the type of the new property to **Geography**</span></span>
7.  <span data-ttu-id="faa11-149">Enregistrer le modèle et générer le projet</span><span class="sxs-lookup"><span data-stu-id="faa11-149">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="faa11-150">Lorsque vous générez, les avertissements relatifs aux entités et associations non mappés peuvent apparaître dans le Liste d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="faa11-150">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="faa11-151">Vous pouvez ignorer ces avertissements, car une fois que vous avez choisi de générer la base de données à partir du modèle, les erreurs disparaissent.</span><span class="sxs-lookup"><span data-stu-id="faa11-151">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="faa11-152">Générer la base de données à partir du modèle</span><span class="sxs-lookup"><span data-stu-id="faa11-152">Generate Database from Model</span></span>

<span data-ttu-id="faa11-153">Nous pouvons maintenant générer une base de données basée sur le modèle.</span><span class="sxs-lookup"><span data-stu-id="faa11-153">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="faa11-154">Cliquez avec le bouton droit sur un espace vide sur l’aire de Entity Designer et sélectionnez **générer la base de données à partir du modèle** .</span><span class="sxs-lookup"><span data-stu-id="faa11-154">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="faa11-155">La boîte de dialogue choisir votre connexion de données de l’Assistant générer la base de données s’affiche. cliquez sur le bouton **nouvelle connexion** , spécifiez (base de données **) \\ mssqllocaldb** pour le nom du serveur et l' **Université** pour la base de données, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="faa11-155">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **University** for the database and click **OK**</span></span>
3.  <span data-ttu-id="faa11-156">Une boîte de dialogue vous demandant si vous souhaitez créer une nouvelle base de données s’affiche, cliquez sur **Oui**.</span><span class="sxs-lookup"><span data-stu-id="faa11-156">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="faa11-157">Cliquez sur **suivant** pour que l’Assistant Création d’une base de données génère le langage de définition de données (DDL) pour la création d’une base de données. la DDL générée est affichée dans la boîte de dialogue Résumé et paramètres. Notez que le DDL ne contient pas de définition pour une table qui correspond au type d’énumération.</span><span class="sxs-lookup"><span data-stu-id="faa11-157">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="faa11-158">Cliquez sur **Terminer** pour ne pas exécuter le script DDL.</span><span class="sxs-lookup"><span data-stu-id="faa11-158">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="faa11-159">L’Assistant Création d’une base de données effectue les opérations suivantes : ouvre **UniversityModel. edmx. SQL** dans l’éditeur T-SQL génère les sections de schéma de stockage et de mappage du fichier edmx ajoute les informations de chaîne de connexion au fichier App.config</span><span class="sxs-lookup"><span data-stu-id="faa11-159">The Create Database Wizard does the following: Opens the **UniversityModel.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="faa11-160">Cliquez sur le bouton droit de la souris dans l’éditeur T-SQL et sélectionnez **exécuter** la boîte de dialogue se connecter au serveur, entrez les informations de connexion de l’étape 2, puis cliquez sur **se connecter** .</span><span class="sxs-lookup"><span data-stu-id="faa11-160">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="faa11-161">Pour afficher le schéma généré, cliquez avec le bouton droit sur le nom de la base de données dans Explorateur d’objets SQL Server puis sélectionnez **Actualiser** .</span><span class="sxs-lookup"><span data-stu-id="faa11-161">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="faa11-162">Conserver et récupérer des données</span><span class="sxs-lookup"><span data-stu-id="faa11-162">Persist and Retrieve Data</span></span>

<span data-ttu-id="faa11-163">Ouvrez le fichier Program.cs dans lequel la méthode main est définie.</span><span class="sxs-lookup"><span data-stu-id="faa11-163">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="faa11-164">Ajoutez le code suivant à la fonction main.</span><span class="sxs-lookup"><span data-stu-id="faa11-164">Add the following code into the Main function.</span></span>

<span data-ttu-id="faa11-165">Le code ajoute deux nouveaux objets University au contexte.</span><span class="sxs-lookup"><span data-stu-id="faa11-165">The code adds two new University objects to the context.</span></span> <span data-ttu-id="faa11-166">Les propriétés spatiales sont initialisées à l’aide de la méthode DbGeography. FromText.</span><span class="sxs-lookup"><span data-stu-id="faa11-166">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="faa11-167">Le point géographique représenté en tant que WellKnownText est passé à la méthode.</span><span class="sxs-lookup"><span data-stu-id="faa11-167">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="faa11-168">Le code enregistre ensuite les données.</span><span class="sxs-lookup"><span data-stu-id="faa11-168">The code then saves the data.</span></span> <span data-ttu-id="faa11-169">Ensuite, la requête LINQ qui retourne un objet University dans lequel son emplacement est le plus proche de l’emplacement spécifié est construite et exécutée.</span><span class="sxs-lookup"><span data-stu-id="faa11-169">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
    {
        Name = "School of Fine Art",
        Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
    });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                                orderby u.Location.Distance(myLocation)
                                select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

<span data-ttu-id="faa11-170">Compilez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="faa11-170">Compile and run the application.</span></span> <span data-ttu-id="faa11-171">Le programme génère la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="faa11-171">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

<span data-ttu-id="faa11-172">Pour afficher les données de la base de données, cliquez avec le bouton droit sur le nom de la base de données dans Explorateur d’objets SQL Server, puis sélectionnez **Actualiser**.</span><span class="sxs-lookup"><span data-stu-id="faa11-172">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="faa11-173">Ensuite, cliquez sur le bouton droit de la souris sur la table et sélectionnez **afficher les données**.</span><span class="sxs-lookup"><span data-stu-id="faa11-173">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="faa11-174">Résumé</span><span class="sxs-lookup"><span data-stu-id="faa11-174">Summary</span></span>

<span data-ttu-id="faa11-175">Dans cette procédure pas à pas, nous avons vu comment mapper des types spatiaux à l’aide de la Entity Framework Designer et comment utiliser des types spatiaux dans le code.</span><span class="sxs-lookup"><span data-stu-id="faa11-175">In this walkthrough we looked at how to map spatial types using the Entity Framework Designer and how to use spatial types in code.</span></span> 
