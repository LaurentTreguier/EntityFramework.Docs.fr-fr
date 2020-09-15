---
title: Fonctions table (TVF)-EF6
description: Fonctions table (TVF) dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/tvfs
ms.openlocfilehash: 727c6c7ff5947e78bf8eb489194e355e2fd157c9
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070159"
---
# <a name="table-valued-functions-tvfs"></a><span data-ttu-id="7b45a-103">Fonctions table (TVF)</span><span class="sxs-lookup"><span data-stu-id="7b45a-103">Table-Valued Functions (TVFs)</span></span>
> [!NOTE]
> <span data-ttu-id="7b45a-104">**EF5 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="7b45a-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="7b45a-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="7b45a-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="7b45a-106">La vidéo et la procédure pas à pas montrent comment mapper des fonctions table (TVF) à l’aide de l’Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="7b45a-106">The video and step-by-step walkthrough shows how to map table-valued functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="7b45a-107">Il montre également comment appeler une fonction TVF à partir d’une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="7b45a-107">It also demonstrates how to call a TVF from a LINQ query.</span></span>

<span data-ttu-id="7b45a-108">Les TVF sont actuellement pris en charge uniquement dans le flux de travail Database First.</span><span class="sxs-lookup"><span data-stu-id="7b45a-108">TVFs are currently only supported in the Database First workflow.</span></span>

<span data-ttu-id="7b45a-109">La prise en charge de TVF a été introduite dans Entity Framework version 5.</span><span class="sxs-lookup"><span data-stu-id="7b45a-109">TVF support was introduced in Entity Framework version 5.</span></span> <span data-ttu-id="7b45a-110">Notez que pour utiliser les nouvelles fonctionnalités telles que les fonctions table, les énumérations et les types spatiaux, vous devez cibler .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="7b45a-110">Note that to use the new features like table-valued functions, enums, and spatial types you must target .NET Framework 4.5.</span></span> <span data-ttu-id="7b45a-111">Visual Studio 2012 cible .NET 4,5 par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b45a-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="7b45a-112">Les TVF sont très similaires aux procédures stockées avec une différence clé : le résultat d’une fonction TVF est composable.</span><span class="sxs-lookup"><span data-stu-id="7b45a-112">TVFs are very similar to stored procedures with one key difference: the result of a TVF is composable.</span></span> <span data-ttu-id="7b45a-113">Cela signifie que les résultats d’une fonction TVF peuvent être utilisés dans une requête LINQ alors que les résultats d’une procédure stockée ne le peuvent pas.</span><span class="sxs-lookup"><span data-stu-id="7b45a-113">That means the results from a TVF can be used in a LINQ query while the results of a stored procedure cannot.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="7b45a-114">Regarder la vidéo</span><span class="sxs-lookup"><span data-stu-id="7b45a-114">Watch the video</span></span>

<span data-ttu-id="7b45a-115">**Présenté par**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="7b45a-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="7b45a-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv)  |  [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span><span class="sxs-lookup"><span data-stu-id="7b45a-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="7b45a-117">Prérequis</span><span class="sxs-lookup"><span data-stu-id="7b45a-117">Pre-Requisites</span></span>

<span data-ttu-id="7b45a-118">Pour effectuer cette procédure pas à pas, vous devez :</span><span class="sxs-lookup"><span data-stu-id="7b45a-118">To complete this walkthrough, you need to:</span></span>

- <span data-ttu-id="7b45a-119">Installez la [base de données School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="7b45a-119">Install the [School database](xref:ef6/resources/school-database).</span></span>

- <span data-ttu-id="7b45a-120">Disposer d’une version récente de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b45a-120">Have a recent version of Visual Studio</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="7b45a-121">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="7b45a-121">Set up the Project</span></span>

1.  <span data-ttu-id="7b45a-122">Ouvrir Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7b45a-122">Open Visual Studio</span></span>
2.  <span data-ttu-id="7b45a-123">Dans le menu **fichier** , pointez sur **nouveau**, puis cliquez sur **projet** .</span><span class="sxs-lookup"><span data-stu-id="7b45a-123">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="7b45a-124">Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .</span><span class="sxs-lookup"><span data-stu-id="7b45a-124">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="7b45a-125">Entrez **TVF** comme nom du projet, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="7b45a-125">Enter **TVF** as the name of the project and click **OK**</span></span>

## <a name="add-a-tvf-to-the-database"></a><span data-ttu-id="7b45a-126">Ajouter une TVF à la base de données</span><span class="sxs-lookup"><span data-stu-id="7b45a-126">Add a TVF to the Database</span></span>

-   <span data-ttu-id="7b45a-127">Sélectionner **un affichage- &gt; Explorateur d’objets SQL Server**</span><span class="sxs-lookup"><span data-stu-id="7b45a-127">Select **View -&gt; SQL Server Object Explorer**</span></span>
-   <span data-ttu-id="7b45a-128">Si la base de données locale ne figure pas dans la liste des serveurs : cliquez avec le bouton droit sur **SQL Server** et sélectionnez **ajouter SQL Server** utiliser l' **authentification Windows** par défaut pour la connexion au serveur de base de données locale</span><span class="sxs-lookup"><span data-stu-id="7b45a-128">If LocalDB is not in the list of servers: Right-click on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB server</span></span>
-   <span data-ttu-id="7b45a-129">Développez le nœud de base de données locale</span><span class="sxs-lookup"><span data-stu-id="7b45a-129">Expand the LocalDB node</span></span>
-   <span data-ttu-id="7b45a-130">Sous le nœud bases de données, cliquez avec le bouton droit sur le nœud de la base de données School, puis sélectionnez **nouvelle requête...**</span><span class="sxs-lookup"><span data-stu-id="7b45a-130">Under the Databases node, right-click the School database node and select **New Query…**</span></span>
-   <span data-ttu-id="7b45a-131">Dans l’éditeur T-SQL, collez la définition de TVF suivante.</span><span class="sxs-lookup"><span data-stu-id="7b45a-131">In T-SQL Editor, paste the following TVF definition</span></span>

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   <span data-ttu-id="7b45a-132">Cliquez sur le bouton droit de la souris dans l’éditeur T-SQL, puis sélectionnez **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="7b45a-132">Click the right mouse button on the T-SQL editor and select **Execute**</span></span>
-   <span data-ttu-id="7b45a-133">La fonction GetStudentGradesForCourse est ajoutée à la base de données School.</span><span class="sxs-lookup"><span data-stu-id="7b45a-133">The GetStudentGradesForCourse function is added to the School database</span></span>

 

## <a name="create-a-model"></a><span data-ttu-id="7b45a-134">Création d'un modèle</span><span class="sxs-lookup"><span data-stu-id="7b45a-134">Create a Model</span></span>

1.  <span data-ttu-id="7b45a-135">Cliquez avec le bouton droit sur le nom du projet dans Explorateur de solutions, pointez sur **Ajouter**, puis cliquez sur **nouvel élément** .</span><span class="sxs-lookup"><span data-stu-id="7b45a-135">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="7b45a-136">Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet **modèles** .</span><span class="sxs-lookup"><span data-stu-id="7b45a-136">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the **Templates** pane</span></span>
3.  <span data-ttu-id="7b45a-137">Entrez **TVFModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="7b45a-137">Enter **TVFModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="7b45a-138">Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="7b45a-138">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**</span></span>
5.  <span data-ttu-id="7b45a-139">Cliquez sur **nouvelle connexion** entrée **( \\ mssqllocaldb)** dans la zone de texte Nom du serveur, entrez **School**   comme nom de la base de données, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="7b45a-139">Click **New Connection** Enter **(localdb)\\mssqllocaldb** in the Server name text box Enter **School** for the database name Click **OK**</span></span>
6.  <span data-ttu-id="7b45a-140">Dans la boîte de dialogue choisir vos objets de base de données, sous le nœud **tables**   , sélectionnez les tables **Person**, **StudentGrade**et **course**   .</span><span class="sxs-lookup"><span data-stu-id="7b45a-140">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person**, **StudentGrade**, and **Course** tables</span></span>
7.  <span data-ttu-id="7b45a-141">Sélectionnez la fonction **GetStudentGradesForCourse** située sous la note du nœud **procédures stockées et fonctions**   , qui commence par Visual Studio 2012, le Entity designer vous permet d’importer par lot vos procédures stockées et fonctions</span><span class="sxs-lookup"><span data-stu-id="7b45a-141">Select the **GetStudentGradesForCourse** function located under the **Stored Procedures and Functions** node Note, that starting with Visual Studio 2012, the Entity Designer allows you to batch import your Stored Procedures and Functions</span></span>
8.  <span data-ttu-id="7b45a-142">Cliquez sur **Terminer**</span><span class="sxs-lookup"><span data-stu-id="7b45a-142">Click **Finish**</span></span>
9.  <span data-ttu-id="7b45a-143">Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.</span><span class="sxs-lookup"><span data-stu-id="7b45a-143">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="7b45a-144">Tous les objets que vous avez sélectionnés dans la boîte **de dialogue choisir vos objets de base de données**   sont ajoutés au modèle.</span><span class="sxs-lookup"><span data-stu-id="7b45a-144">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>
10. <span data-ttu-id="7b45a-145">Par défaut, la forme de résultat de chaque fonction ou procédure stockée importée devient automatiquement un nouveau type complexe dans votre modèle d’entité.</span><span class="sxs-lookup"><span data-stu-id="7b45a-145">By default, the result shape of each imported stored procedure or function will automatically become a new complex type in your entity model.</span></span> <span data-ttu-id="7b45a-146">Mais nous voulons mapper les résultats de la fonction GetStudentGradesForCourse à l’entité StudentGrade : cliquez avec le bouton droit sur l’aire de conception, sélectionnez **Explorateur de modèles** dans l’Explorateur de modèles, sélectionnez **importations de fonction**, puis double-cliquez sur la fonction **GetStudentGradesForCourse** dans la boîte de dialogue Modifier l’importation de fonction, sélectionnez **entités**,   puis choisissez **StudentGrade**</span><span class="sxs-lookup"><span data-stu-id="7b45a-146">But we want to map the results of the GetStudentGradesForCourse function to the StudentGrade entity: Right-click the design surface and select **Model Browser** In Model Browser, select **Function Imports**, and then double-click the **GetStudentGradesForCourse** function In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="7b45a-147">Conserver et récupérer des données</span><span class="sxs-lookup"><span data-stu-id="7b45a-147">Persist and Retrieve Data</span></span>

<span data-ttu-id="7b45a-148">Ouvrez le fichier dans lequel la méthode main est définie.</span><span class="sxs-lookup"><span data-stu-id="7b45a-148">Open the file where the Main method is defined.</span></span> <span data-ttu-id="7b45a-149">Ajoutez le code suivant à la fonction main.</span><span class="sxs-lookup"><span data-stu-id="7b45a-149">Add the following code into the Main function.</span></span>

<span data-ttu-id="7b45a-150">Le code suivant montre comment générer une requête qui utilise une fonction table.</span><span class="sxs-lookup"><span data-stu-id="7b45a-150">The following code demonstrates how to build a query that uses a Table-valued Function.</span></span> <span data-ttu-id="7b45a-151">La requête projette les résultats dans un type anonyme qui contient le titre du cours associé et les étudiants associés avec un niveau supérieur ou égal à 3,5.</span><span class="sxs-lookup"><span data-stu-id="7b45a-151">The query projects the results into an anonymous type that contains the related Course title and related students with a grade greater or equal to 3.5.</span></span>

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

<span data-ttu-id="7b45a-152">Compilez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="7b45a-152">Compile and run the application.</span></span> <span data-ttu-id="7b45a-153">Le programme génère la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="7b45a-153">The program produces the following output:</span></span>

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a><span data-ttu-id="7b45a-154">Résumé</span><span class="sxs-lookup"><span data-stu-id="7b45a-154">Summary</span></span>

<span data-ttu-id="7b45a-155">Dans cette procédure pas à pas, nous avons vu comment mapper des fonctions table (TVF) à l’aide de l’Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="7b45a-155">In this walkthrough we looked at how to map Table-valued Functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="7b45a-156">Elle a également démontré comment appeler une TVF à partir d’une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="7b45a-156">It also demonstrated how to call a TVF from a LINQ query.</span></span>
