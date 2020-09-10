---
title: Spatial-Code First-EF6
description: Code First spatiale dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
uid: ef6/modeling/code-first/data-types/spatial
ms.openlocfilehash: 8c2d47a66979a228fdc4902857df999a21945547
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618164"
---
# <a name="spatial---code-first"></a><span data-ttu-id="f7ce2-103">Code First spatial</span><span class="sxs-lookup"><span data-stu-id="f7ce2-103">Spatial - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="f7ce2-104">**EF5 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="f7ce2-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="f7ce2-106">La vidéo et la procédure pas à pas montrent comment mapper des types spatiaux avec Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-106">The video and step-by-step walkthrough shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="f7ce2-107">Il montre également comment utiliser une requête LINQ pour rechercher une distance entre deux emplacements.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="f7ce2-108">Cette procédure pas à pas utilise Code First pour créer une base de données, mais vous pouvez également utiliser des [Code First à une base de données existante](xref:ef6/modeling/code-first/workflows/existing-database).</span><span class="sxs-lookup"><span data-stu-id="f7ce2-108">This walkthrough will use Code First to create a new database, but you can also use [Code First to an existing database](xref:ef6/modeling/code-first/workflows/existing-database).</span></span>

<span data-ttu-id="f7ce2-109">La prise en charge des types spatiaux a été introduite dans Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="f7ce2-110">Notez que pour utiliser les nouvelles fonctionnalités telles que le type spatial, les énumérations et les fonctions table, vous devez cibler .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="f7ce2-111">Visual Studio 2012 cible .NET 4,5 par défaut.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="f7ce2-112">Pour utiliser des types de données spatiales, vous devez également utiliser un fournisseur de Entity Framework qui a une prise en charge spatiale.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="f7ce2-113">Pour plus d’informations, consultez [prise en charge des fournisseurs pour les types spatiaux](xref:ef6/fundamentals/providers/spatial-support) .</span><span class="sxs-lookup"><span data-stu-id="f7ce2-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="f7ce2-114">Il existe deux types de données spatiales principales : Geography et Geometry.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="f7ce2-115">Le type de données geography stocke des données ellipsoïdal (par exemple, des coordonnées de latitude et de longitude GPS).</span><span class="sxs-lookup"><span data-stu-id="f7ce2-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="f7ce2-116">Le type de données geometry représente le système de coordonnées euclidienne (Flat).</span><span class="sxs-lookup"><span data-stu-id="f7ce2-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="f7ce2-117">Regarder la vidéo</span><span class="sxs-lookup"><span data-stu-id="f7ce2-117">Watch the video</span></span>
<span data-ttu-id="f7ce2-118">Cette vidéo montre comment mapper des types spatiaux avec Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-118">This video shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="f7ce2-119">Il montre également comment utiliser une requête LINQ pour rechercher une distance entre deux emplacements.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="f7ce2-120">**Présenté par**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="f7ce2-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="f7ce2-121">**Vidéo**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="f7ce2-121">**Video**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="f7ce2-122">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f7ce2-122">Pre-Requisites</span></span>

<span data-ttu-id="f7ce2-123">Pour effectuer cette procédure pas à pas, vous devez avoir installé Visual Studio 2012, Ultimate, Premium, Professional ou Web Express Edition.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="f7ce2-124">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="f7ce2-124">Set up the Project</span></span>

1.  <span data-ttu-id="f7ce2-125">Ouvrir Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="f7ce2-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="f7ce2-126">Dans le menu **fichier** , pointez sur **nouveau**, puis cliquez sur **projet** .</span><span class="sxs-lookup"><span data-stu-id="f7ce2-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="f7ce2-127">Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .</span><span class="sxs-lookup"><span data-stu-id="f7ce2-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="f7ce2-128">Entrez **SpatialCodeFirst** comme nom du projet, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="f7ce2-128">Enter **SpatialCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="f7ce2-129">Définir un nouveau modèle à l’aide de Code First</span><span class="sxs-lookup"><span data-stu-id="f7ce2-129">Define a New Model using Code First</span></span>

<span data-ttu-id="f7ce2-130">Lors de l’utilisation de Code First développement, vous commencez généralement par écrire des classes .NET Framework qui définissent votre modèle conceptuel (domaine).</span><span class="sxs-lookup"><span data-stu-id="f7ce2-130">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="f7ce2-131">Le code ci-dessous définit la classe University.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-131">The code below defines the University class.</span></span>

<span data-ttu-id="f7ce2-132">La propriété Location de l’Université est du type DbGeography.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-132">The University has the Location property of the DbGeography type.</span></span> <span data-ttu-id="f7ce2-133">Pour utiliser le type DbGeography, vous devez ajouter une référence à l’assembly System. Data. Entity et ajouter également l’instruction using System. Data. spatial.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-133">To use the DbGeography type, you must add a reference to the System.Data.Entity assembly and also add the System.Data.Spatial using statement.</span></span>

<span data-ttu-id="f7ce2-134">Ouvrez le fichier Program.cs et collez les instructions using suivantes en haut du fichier :</span><span class="sxs-lookup"><span data-stu-id="f7ce2-134">Open the Program.cs file and paste the following using statements at the top of the file:</span></span>

``` csharp
using System.Data.Spatial;
```

<span data-ttu-id="f7ce2-135">Ajoutez la définition de classe University suivante au fichier Program.cs.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-135">Add the following University class definition to the Program.cs file.</span></span>

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="f7ce2-136">Définir le type dérivé DbContext</span><span class="sxs-lookup"><span data-stu-id="f7ce2-136">Define the DbContext Derived Type</span></span>

<span data-ttu-id="f7ce2-137">En plus de définir des entités, vous devez définir une classe qui dérive de DbContext et expose les &lt; Propriétés de la tente DbSet &gt; .</span><span class="sxs-lookup"><span data-stu-id="f7ce2-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="f7ce2-138">Les propriétés de la tente de DbSet &lt; &gt; permettent au contexte de savoir quels types vous souhaitez inclure dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="f7ce2-139">Une instance du type dérivé DbContext gère les objets d’entité au moment de l’exécution, ce qui comprend le remplissage des objets avec les données d’une base de données, le suivi des modifications et la persistance des données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="f7ce2-140">Les types DbContext et DbSet sont définis dans l’assembly EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-140">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="f7ce2-141">Nous allons ajouter une référence à cette DLL à l’aide du package NuGet EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-141">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="f7ce2-142">Dans Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-142">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="f7ce2-143">Sélectionnez **gérer les packages NuGet...**</span><span class="sxs-lookup"><span data-stu-id="f7ce2-143">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="f7ce2-144">Dans la boîte de dialogue gérer les packages NuGet, sélectionnez l’onglet **en ligne** et choisissez le package **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="f7ce2-144">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="f7ce2-145">Cliquez sur **Install**.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-145">Click **Install**</span></span>

<span data-ttu-id="f7ce2-146">Notez qu’en plus de l’assembly EntityFramework, une référence à l’assembly System. ComponentModel. DataAnnotations est également ajoutée.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-146">Note, that in addition to the EntityFramework  assembly, a reference to the System.ComponentModel.DataAnnotations assembly is also added.</span></span>

<span data-ttu-id="f7ce2-147">En haut du fichier Program.cs, ajoutez l’instruction using suivante :</span><span class="sxs-lookup"><span data-stu-id="f7ce2-147">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="f7ce2-148">Dans Program.cs, ajoutez la définition de contexte.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-148">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="f7ce2-149">Conserver et récupérer des données</span><span class="sxs-lookup"><span data-stu-id="f7ce2-149">Persist and Retrieve Data</span></span>

<span data-ttu-id="f7ce2-150">Ouvrez le fichier Program.cs dans lequel la méthode main est définie.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-150">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="f7ce2-151">Ajoutez le code suivant à la fonction main.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-151">Add the following code into the Main function.</span></span>

<span data-ttu-id="f7ce2-152">Le code ajoute deux nouveaux objets University au contexte.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-152">The code adds two new University objects to the context.</span></span> <span data-ttu-id="f7ce2-153">Les propriétés spatiales sont initialisées à l’aide de la méthode DbGeography. FromText.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-153">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="f7ce2-154">Le point géographique représenté en tant que WellKnownText est passé à la méthode.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-154">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="f7ce2-155">Le code enregistre ensuite les données.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-155">The code then saves the data.</span></span> <span data-ttu-id="f7ce2-156">Ensuite, la requête LINQ qui retourne un objet University dans lequel son emplacement est le plus proche de l’emplacement spécifié est construite et exécutée.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-156">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
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

<span data-ttu-id="f7ce2-157">Compilez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-157">Compile and run the application.</span></span> <span data-ttu-id="f7ce2-158">Le programme génère la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="f7ce2-158">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a><span data-ttu-id="f7ce2-159">Afficher la base de données générée</span><span class="sxs-lookup"><span data-stu-id="f7ce2-159">View the Generated Database</span></span>

<span data-ttu-id="f7ce2-160">Lorsque vous exécutez l’application la première fois, la Entity Framework crée une base de données pour vous.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-160">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="f7ce2-161">Étant donné que Visual Studio 2012 est installé, la base de données sera créée sur l’instance de base de données locale.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-161">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="f7ce2-162">Par défaut, le Entity Framework nomme la base de données après le nom qualifié complet du contexte dérivé (dans cet exemple, il s’agit de **SpatialCodeFirst. UniversityContext**).</span><span class="sxs-lookup"><span data-stu-id="f7ce2-162">By default, the Entity Framework names the database after the fully qualified name of the derived context (in this example that is **SpatialCodeFirst.UniversityContext**).</span></span> <span data-ttu-id="f7ce2-163">La prochaine fois que la base de données existante sera utilisée.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-163">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="f7ce2-164">Notez que si vous apportez des modifications à votre modèle après la création de la base de données, vous devez utiliser Migrations Code First pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-164">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="f7ce2-165">Pour obtenir un exemple d’utilisation de migrations, consultez [Code First à une nouvelle base de données](xref:ef6/modeling/code-first/workflows/new-database) .</span><span class="sxs-lookup"><span data-stu-id="f7ce2-165">See [Code First to a New Database](xref:ef6/modeling/code-first/workflows/new-database) for an example of using Migrations.</span></span>

<span data-ttu-id="f7ce2-166">Pour afficher la base de données et les données, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="f7ce2-166">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="f7ce2-167">Dans le menu principal de Visual Studio 2012, sélectionnez **Afficher**  - &gt; **Explorateur d’objets SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-167">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="f7ce2-168">Si la base de données locale ne figure pas dans la liste des serveurs, cliquez sur le bouton droit de la souris sur **SQL Server** et sélectionnez **ajouter SQL Server** utiliser l' **authentification Windows** par défaut pour vous connecter à l’instance de base de données locale.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-168">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the the LocalDB instance</span></span>
3.  <span data-ttu-id="f7ce2-169">Développez le nœud de base de données locale</span><span class="sxs-lookup"><span data-stu-id="f7ce2-169">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="f7ce2-170">Dérouler le dossier **bases de données** pour afficher la nouvelle base de données et accéder à la table **universités**</span><span class="sxs-lookup"><span data-stu-id="f7ce2-170">Unfold the **Databases** folder to see the new database and browse to the **Universities** table</span></span>
5.  <span data-ttu-id="f7ce2-171">Pour afficher les données, cliquez avec le bouton droit sur la table et sélectionnez **afficher les données** .</span><span class="sxs-lookup"><span data-stu-id="f7ce2-171">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="f7ce2-172">Résumé</span><span class="sxs-lookup"><span data-stu-id="f7ce2-172">Summary</span></span>

<span data-ttu-id="f7ce2-173">Dans cette procédure pas à pas, nous avons vu comment utiliser des types spatiaux avec Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="f7ce2-173">In this walkthrough we looked at how to use spatial types with Entity Framework Code First.</span></span> 
