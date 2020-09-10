---
title: Procédures stockées de requête du concepteur-EF6
description: Procédures stockées de requête du concepteur dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
uid: ef6/modeling/designer/stored-procedures/query
ms.openlocfilehash: c647516e1c6ea010c791df6857782b2304ceed1c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620353"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="af31f-103">Procédures stockées de requête du concepteur</span><span class="sxs-lookup"><span data-stu-id="af31f-103">Designer Query Stored Procedures</span></span>
<span data-ttu-id="af31f-104">Cette procédure pas à pas explique comment utiliser le Entity Framework Designer (concepteur EF) pour importer des procédures stockées dans un modèle, puis appeler les procédures stockées importées pour récupérer les résultats.</span><span class="sxs-lookup"><span data-stu-id="af31f-104">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="af31f-105">Notez que Code First ne prend pas en charge le mappage à des procédures stockées ou à des fonctions.</span><span class="sxs-lookup"><span data-stu-id="af31f-105">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="af31f-106">Toutefois, vous pouvez appeler des procédures stockées ou des fonctions à l’aide de la méthode System. Data. Entity. DbSet. SqlQuery.</span><span class="sxs-lookup"><span data-stu-id="af31f-106">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="af31f-107">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="af31f-107">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="af31f-108">Prérequis</span><span class="sxs-lookup"><span data-stu-id="af31f-108">Prerequisites</span></span>

<span data-ttu-id="af31f-109">Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="af31f-109">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="af31f-110">Une version récente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="af31f-110">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="af31f-111">[Exemple de base de données School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="af31f-111">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="af31f-112">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="af31f-112">Set up the Project</span></span>

-   <span data-ttu-id="af31f-113">Ouvrez Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="af31f-113">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="af31f-114">Sélectionnez **fichier- &gt; nouveau- &gt; projet**</span><span class="sxs-lookup"><span data-stu-id="af31f-114">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="af31f-115">Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .</span><span class="sxs-lookup"><span data-stu-id="af31f-115">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="af31f-116">Entrez **EFwithSProcsSample**   comme nom.</span><span class="sxs-lookup"><span data-stu-id="af31f-116">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="af31f-117">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="af31f-117">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="af31f-118">Création d'un modèle</span><span class="sxs-lookup"><span data-stu-id="af31f-118">Create a Model</span></span>

-   <span data-ttu-id="af31f-119">Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **ajouter- &gt; nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="af31f-119">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="af31f-120">Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.</span><span class="sxs-lookup"><span data-stu-id="af31f-120">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="af31f-121">Entrez **EFwithSProcsModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="af31f-121">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="af31f-122">Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant**.</span><span class="sxs-lookup"><span data-stu-id="af31f-122">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="af31f-123">Cliquez sur **nouvelle connexion**.</span><span class="sxs-lookup"><span data-stu-id="af31f-123">Click **New Connection**.</span></span>  
    <span data-ttu-id="af31f-124">Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, **( \\ mssqllocaldb)**, sélectionnez la méthode d’authentification, tapez **School**   comme nom de la base de données, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="af31f-124">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="af31f-125">La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="af31f-125">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="af31f-126">Dans la boîte de dialogue choisir vos objets de base de données, activez la case à cocher **tables**   pour sélectionner toutes les tables.</span><span class="sxs-lookup"><span data-stu-id="af31f-126">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="af31f-127">En outre, sélectionnez les procédures stockées suivantes sous le nœud **procédures stockées et fonctions** : **GetStudentGrades** et **GetDepartmentName**.</span><span class="sxs-lookup"><span data-stu-id="af31f-127">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![Importer des procédures stockées](~/ef6/media/import.jpg)

    <span data-ttu-id="af31f-129">*À compter de Visual Studio 2012, le concepteur EF prend en charge l’importation en bloc des procédures stockées. L' **importation des procédures stockées et des fonctions sélectionnées dans le modèle theentity** est activée par défaut.*</span><span class="sxs-lookup"><span data-stu-id="af31f-129">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="af31f-130">Cliquez sur **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="af31f-130">Click **Finish**.</span></span>

<span data-ttu-id="af31f-131">Par défaut, la forme de résultat de chaque procédure stockée ou fonction importée qui retourne plusieurs colonnes devient automatiquement un nouveau type complexe.</span><span class="sxs-lookup"><span data-stu-id="af31f-131">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="af31f-132">Dans cet exemple, nous voulons mapper les résultats de la fonction **GetStudentGrades** à l’entité **StudentGrade** et les résultats du **GetDepartmentName** à **None** (**None** est la valeur par défaut).</span><span class="sxs-lookup"><span data-stu-id="af31f-132">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="af31f-133">Pour qu’une importation de fonction retourne un type d’entité, les colonnes retournées par la procédure stockée correspondante doivent correspondre exactement aux propriétés scalaires du type d’entité retourné.</span><span class="sxs-lookup"><span data-stu-id="af31f-133">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="af31f-134">Une importation de fonction peut également retourner des collections de types simples, des types complexes ou aucune valeur.</span><span class="sxs-lookup"><span data-stu-id="af31f-134">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="af31f-135">Cliquez avec le bouton droit sur l’aire de conception, puis sélectionnez **Explorateur de modèles**.</span><span class="sxs-lookup"><span data-stu-id="af31f-135">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="af31f-136">Dans l' **Explorateur de modèles**, sélectionnez importation de **fonction**, puis double-cliquez sur la fonction **GetStudentGrades** .</span><span class="sxs-lookup"><span data-stu-id="af31f-136">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="af31f-137">Dans la boîte de dialogue Modifier l’importation de fonction, sélectionnez **entités**,   puis choisissez **StudentGrade**.</span><span class="sxs-lookup"><span data-stu-id="af31f-137">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="af31f-138">*La case **Importer une fonction peut être composable** en haut de la boîte de dialogue **importations** de fonctions, qui vous permet de mapper à des fonctions composables. Si vous activez cette case à cocher, seules les fonctions composables (fonctions table) s’affichent dans la liste déroulante nom de la **procédure stockée/fonction** . Si vous n’activez pas cette case à cocher, seules les fonctions non composables seront affichées dans la liste.*</span><span class="sxs-lookup"><span data-stu-id="af31f-138">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="af31f-139">Utiliser le modèle</span><span class="sxs-lookup"><span data-stu-id="af31f-139">Use the Model</span></span>

<span data-ttu-id="af31f-140">Ouvrez le fichier **Program.cs** dans lequel la méthode **main** est définie.</span><span class="sxs-lookup"><span data-stu-id="af31f-140">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="af31f-141">Ajoutez le code suivant à la fonction main.</span><span class="sxs-lookup"><span data-stu-id="af31f-141">Add the following code into the Main function.</span></span>

<span data-ttu-id="af31f-142">Le code appelle deux procédures stockées : **GetStudentGrades** (retourne **StudentGrades** pour le *StudentID*spécifié) et **GetDepartmentName** (retourne le nom du département dans le paramètre de sortie).</span><span class="sxs-lookup"><span data-stu-id="af31f-142">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        // Specify the Student ID.
        int studentId = 2;

        // Call GetStudentGrades and iterate through the returned collection.
        foreach (StudentGrade grade in context.GetStudentGrades(studentId))
        {
            Console.WriteLine("StudentID: {0}\tSubject={1}", studentId, grade.Subject);
            Console.WriteLine("Student grade: " + grade.Grade);
        }

        // Call GetDepartmentName.
        // Declare the name variable that will contain the value returned by the output parameter.
        ObjectParameter name = new ObjectParameter("Name", typeof(String));
        context.GetDepartmentName(1, name);
        Console.WriteLine("The department name is {0}", name.Value);

    }
```

<span data-ttu-id="af31f-143">Compilez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="af31f-143">Compile and run the application.</span></span> <span data-ttu-id="af31f-144">Le programme génère la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="af31f-144">The program produces the following output:</span></span>

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="af31f-145">Paramètres de sortie</span><span class="sxs-lookup"><span data-stu-id="af31f-145">Output Parameters</span></span>
-----------------

<span data-ttu-id="af31f-146">Si des paramètres de sortie sont utilisés, leurs valeurs ne sont pas disponibles tant que les résultats n’ont pas été entièrement lus.</span><span class="sxs-lookup"><span data-stu-id="af31f-146">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="af31f-147">Cela est dû au comportement sous-jacent de DbDataReader. pour plus d’informations, consultez [récupération de données à l’aide d’un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) .</span><span class="sxs-lookup"><span data-stu-id="af31f-147">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
