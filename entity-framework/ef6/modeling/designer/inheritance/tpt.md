---
title: Héritage TPT-EF6
description: Héritage de TPT dans le concepteur dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: efc78c31-b4ea-4ea3-a0cd-c69eb507020e
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: cb686a9f51b14651c210102845de495906318747
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620480"
---
# <a name="designer-tpt-inheritance"></a><span data-ttu-id="5e154-103">Héritage de TPT du concepteur</span><span class="sxs-lookup"><span data-stu-id="5e154-103">Designer TPT Inheritance</span></span>
<span data-ttu-id="5e154-104">Cette procédure pas à pas montre comment implémenter l’héritage TPT (table par type) dans votre modèle à l’aide de l’Entity Framework Designer (concepteur EF).</span><span class="sxs-lookup"><span data-stu-id="5e154-104">This step-by-step walkthrough shows how to implement table-per-type (TPT) inheritance in your model using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="5e154-105">L'héritage TPT (table par type) utilise une table distincte de la base de données pour maintenir des données des propriétés non héritées et des propriétés de clé pour chaque type de la hiérarchie d'héritage.</span><span class="sxs-lookup"><span data-stu-id="5e154-105">Table-per-type inheritance uses a separate table in the database to maintain data for non-inherited properties and key properties for each type in the inheritance hierarchy.</span></span>

<span data-ttu-id="5e154-106">Dans cette procédure pas à pas, nous allons mapper le **cours** (type de base), **OnlineCourse** (dérivé de course) et **OnsiteCourse**   (dérives de **course**) aux tables portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="5e154-106">In this walkthrough we will map the **Course** (base type), **OnlineCourse** (derives from Course), and **OnsiteCourse** (derives from **Course**) entities to tables with the same names.</span></span> <span data-ttu-id="5e154-107">Nous allons créer un modèle à partir de la base de données, puis modifier le modèle pour implémenter l’héritage TPT.</span><span class="sxs-lookup"><span data-stu-id="5e154-107">We'll create a model from the database and then alter the model to implement the TPT inheritance.</span></span>

<span data-ttu-id="5e154-108">Vous pouvez également commencer par le Model First, puis générer la base de données à partir du modèle.</span><span class="sxs-lookup"><span data-stu-id="5e154-108">You can also start with the Model First and then generate the database from the model.</span></span> <span data-ttu-id="5e154-109">Le concepteur EF utilise la stratégie TPT par défaut. par conséquent, tout héritage dans le modèle est mappé à des tables distinctes.</span><span class="sxs-lookup"><span data-stu-id="5e154-109">The EF Designer uses the TPT strategy by default and so any inheritance in the model will be mapped to separate tables.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="5e154-110">Autres options d’héritage</span><span class="sxs-lookup"><span data-stu-id="5e154-110">Other Inheritance Options</span></span>

<span data-ttu-id="5e154-111">TPH (table par hiérarchie) est un autre type d’héritage dans lequel une table de base de données est utilisée pour conserver les données de tous les types d’entité dans une hiérarchie d’héritage.</span><span class="sxs-lookup"><span data-stu-id="5e154-111">Table-per-Hierarchy (TPH) is another type of inheritance in which one database table is used to maintain data for all of the entity types in an inheritance hierarchy.</span></span><span data-ttu-id="5e154-112">Pour plus d’informations sur la façon de mapper l’héritage TPH (table par hiérarchie) avec le Entity Designer, consultez [EF designer TPH Inheritance](xref:ef6/modeling/designer/inheritance/tph).</span><span class="sxs-lookup"><span data-stu-id="5e154-112">  For information about how to map Table-per-Hierarchy inheritance with the Entity Designer, see [EF Designer TPH Inheritance](xref:ef6/modeling/designer/inheritance/tph).</span></span> 

<span data-ttu-id="5e154-113">Notez que les modèles d’héritage de type table par béton (TPC) et d’héritage mixte sont pris en charge par le runtime Entity Framework, mais ne sont pas pris en charge par le concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="5e154-113">Note that, the Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="5e154-114">Si vous souhaitez utiliser l’héritage TPC ou mixte, deux options s’offrent à vous : Utilisez Code First ou modifiez manuellement le fichier EDMX.</span><span class="sxs-lookup"><span data-stu-id="5e154-114">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="5e154-115">Si vous choisissez de travailler avec le fichier EDMX, la fenêtre Détails de mappage est placée en « mode sans échec » et vous ne pouvez pas utiliser le concepteur pour modifier les mappages.</span><span class="sxs-lookup"><span data-stu-id="5e154-115">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5e154-116">Prérequis</span><span class="sxs-lookup"><span data-stu-id="5e154-116">Prerequisites</span></span>

<span data-ttu-id="5e154-117">Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="5e154-117">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="5e154-118">Une version récente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5e154-118">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="5e154-119">[Exemple de base de données School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="5e154-119">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="5e154-120">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="5e154-120">Set up the Project</span></span>

-   <span data-ttu-id="5e154-121">Ouvrez Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="5e154-121">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="5e154-122">Sélectionnez **fichier- &gt; nouveau- &gt; projet**</span><span class="sxs-lookup"><span data-stu-id="5e154-122">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="5e154-123">Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .</span><span class="sxs-lookup"><span data-stu-id="5e154-123">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="5e154-124">Entrez **TPTDBFirstSample**   comme nom.</span><span class="sxs-lookup"><span data-stu-id="5e154-124">Enter **TPTDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="5e154-125">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="5e154-125">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="5e154-126">Création d'un modèle</span><span class="sxs-lookup"><span data-stu-id="5e154-126">Create a Model</span></span>

-   <span data-ttu-id="5e154-127">Cliquez avec le bouton droit sur le projet dans Explorateur de solutions, puis sélectionnez **ajouter- &gt; nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="5e154-127">Right-click the project in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="5e154-128">Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.</span><span class="sxs-lookup"><span data-stu-id="5e154-128">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="5e154-129">Entrez **TPTModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="5e154-129">Enter **TPTModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="5e154-130">Dans la boîte de dialogue choisir le contenu du Model, sélectionnez\*\*   générer à partir de la base de données**, puis cliquez sur **suivant\*\*.</span><span class="sxs-lookup"><span data-stu-id="5e154-130">In the Choose Model Contents dialog box, select** Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="5e154-131">Cliquez sur **nouvelle connexion**.</span><span class="sxs-lookup"><span data-stu-id="5e154-131">Click **New Connection**.</span></span>
    <span data-ttu-id="5e154-132">Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, **( \\ mssqllocaldb)**, sélectionnez la méthode d’authentification, tapez **School**   comme nom de la base de données, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="5e154-132">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="5e154-133">La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="5e154-133">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="5e154-134">Dans la boîte de dialogue choisir vos objets de base de données, sous le nœud tables, sélectionnez les tables **Department**, **course, OnlineCourse et OnsiteCourse** .</span><span class="sxs-lookup"><span data-stu-id="5e154-134">In the Choose Your Database Objects dialog box, under the Tables node, select the **Department**, **Course, OnlineCourse, and OnsiteCourse** tables.</span></span>
-   <span data-ttu-id="5e154-135">Cliquez sur **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="5e154-135">Click **Finish**.</span></span>

<span data-ttu-id="5e154-136">Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.</span><span class="sxs-lookup"><span data-stu-id="5e154-136">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="5e154-137">Tous les objets que vous avez sélectionnés dans la boîte de dialogue choisir vos objets de base de données sont ajoutés au modèle.</span><span class="sxs-lookup"><span data-stu-id="5e154-137">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

## <a name="implement-table-per-type-inheritance"></a><span data-ttu-id="5e154-138">Implémenter l’héritage TPT (table par type)</span><span class="sxs-lookup"><span data-stu-id="5e154-138">Implement Table-per-Type Inheritance</span></span>

-   <span data-ttu-id="5e154-139">Dans l’aire de conception, cliquez avec le bouton droit sur le type d’entité **OnlineCourse** , puis sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="5e154-139">On the design surface, right-click the **OnlineCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="5e154-140">Dans la fenêtre **Propriétés** , définissez la propriété type de base sur **course**.</span><span class="sxs-lookup"><span data-stu-id="5e154-140">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="5e154-141">Cliquez avec le bouton droit sur le type d’entité **OnsiteCourse** , puis sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="5e154-141">Right-click the **OnsiteCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="5e154-142">Dans la fenêtre **Propriétés** , définissez la propriété type de base sur **course**.</span><span class="sxs-lookup"><span data-stu-id="5e154-142">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="5e154-143">Cliquez avec le bouton droit sur l’Association (la ligne) entre les types d’entités **OnlineCourse** et **course** .</span><span class="sxs-lookup"><span data-stu-id="5e154-143">Right-click the association (the line) between the **OnlineCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="5e154-144">Sélectionnez **supprimer du modèle**.</span><span class="sxs-lookup"><span data-stu-id="5e154-144">Select **Delete from Model**.</span></span>
-   <span data-ttu-id="5e154-145">Cliquez avec le bouton droit sur l’association entre les types d’entité **OnsiteCourse** et **course** .</span><span class="sxs-lookup"><span data-stu-id="5e154-145">Right-click the association between the **OnsiteCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="5e154-146">Sélectionnez **supprimer du modèle**.</span><span class="sxs-lookup"><span data-stu-id="5e154-146">Select **Delete from Model**.</span></span>

<span data-ttu-id="5e154-147">Nous allons maintenant supprimer la propriété **CourseID** de **OnlineCourse** et **OnsiteCourse** , car ces classes héritent de **CourseID** du type de base **course** .</span><span class="sxs-lookup"><span data-stu-id="5e154-147">We will now delete the **CourseID** property from **OnlineCourse** and **OnsiteCourse** because these classes inherit **CourseID** from the **Course** base type.</span></span>

-   <span data-ttu-id="5e154-148">Cliquez avec le bouton droit sur la propriété **CourseID** du type d’entité **OnlineCourse** , puis sélectionnez **supprimer du modèle**.</span><span class="sxs-lookup"><span data-stu-id="5e154-148">Right-click the **CourseID** property of the **OnlineCourse** entity type, and then select **Delete from Model**.</span></span>
-   <span data-ttu-id="5e154-149">Cliquez avec le bouton droit sur la propriété **CourseID** du type d’entité **OnsiteCourse** , puis sélectionnez **supprimer du modèle** .</span><span class="sxs-lookup"><span data-stu-id="5e154-149">Right-click the **CourseID** property of the **OnsiteCourse** entity type, and then select **Delete from Model**</span></span>
-   <span data-ttu-id="5e154-150">L'héritage TPT (table par type) est maintenant implémenté.</span><span class="sxs-lookup"><span data-stu-id="5e154-150">Table-per-type inheritance is now implemented.</span></span>

![Table par type](~/ef6/media/tpt.png)

## <a name="use-the-model"></a><span data-ttu-id="5e154-152">Utiliser le modèle</span><span class="sxs-lookup"><span data-stu-id="5e154-152">Use the Model</span></span>

<span data-ttu-id="5e154-153">Ouvrez le fichier **Program.cs** dans lequel la méthode **main** est définie.</span><span class="sxs-lookup"><span data-stu-id="5e154-153">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="5e154-154">Collez le code suivant dans la fonction **main** .</span><span class="sxs-lookup"><span data-stu-id="5e154-154">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="5e154-155">Le code exécute trois requêtes.</span><span class="sxs-lookup"><span data-stu-id="5e154-155">The code executes three queries.</span></span> <span data-ttu-id="5e154-156">La première requête rétablit tous les **cours** liés au service spécifié.</span><span class="sxs-lookup"><span data-stu-id="5e154-156">The first query brings back all **Courses** related to the specified department.</span></span> <span data-ttu-id="5e154-157">La deuxième requête utilise la méthode **OfType** pour retourner les **OnlineCourses** liés au service spécifié.</span><span class="sxs-lookup"><span data-stu-id="5e154-157">The second query uses the **OfType** method to return **OnlineCourses** related to the specified department.</span></span> <span data-ttu-id="5e154-158">La troisième requête retourne **OnsiteCourses**.</span><span class="sxs-lookup"><span data-stu-id="5e154-158">The third query returns **OnsiteCourses**.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
