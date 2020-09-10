---
title: Définition de Query-EF designer-EF6
description: Définition de la requête-concepteur EF dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: e1962a80c3a94fb17bacf28969629ce949b117a0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616834"
---
# <a name="defining-query---ef-designer"></a><span data-ttu-id="3247b-103">Définition de la requête-concepteur EF</span><span class="sxs-lookup"><span data-stu-id="3247b-103">Defining Query - EF Designer</span></span>
<span data-ttu-id="3247b-104">Cette procédure pas à pas montre comment ajouter une requête de définition et un type d’entité correspondant à un modèle à l’aide du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="3247b-104">This walkthrough demonstrates how to add a defining query and a corresponding entity type to a model using the EF Designer.</span></span> <span data-ttu-id="3247b-105">Une requête de définition est couramment utilisée pour fournir des fonctionnalités semblables à celles fournies par une vue de base de données, mais la vue est définie dans le modèle, et non dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="3247b-105">A defining query is commonly used to provide functionality similar to that provided by a database view, but the view is defined in the model, not the database.</span></span> <span data-ttu-id="3247b-106">Une requête de définition vous permet d’exécuter une instruction SQL qui est spécifiée dans l’élément **DefiningQuery**   d’un fichier. edmx.</span><span class="sxs-lookup"><span data-stu-id="3247b-106">A defining query allows you to execute a SQL statement that is specified in the **DefiningQuery** element of an .edmx file.</span></span> <span data-ttu-id="3247b-107">Pour plus d’informations, consultez **DefiningQuery** dans la [spécification SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span><span class="sxs-lookup"><span data-stu-id="3247b-107">For more information, see **DefiningQuery** in the [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="3247b-108">Lorsque vous utilisez la définition de requêtes, vous devez également définir un type d’entité dans votre modèle.</span><span class="sxs-lookup"><span data-stu-id="3247b-108">When using defining queries, you also have to define an entity type in your model.</span></span> <span data-ttu-id="3247b-109">Le type d’entité est utilisé pour exposer les données exposées par la requête de définition.</span><span class="sxs-lookup"><span data-stu-id="3247b-109">The entity type is used to surface data exposed by the defining query.</span></span> <span data-ttu-id="3247b-110">Notez que les données exposées par le biais de ce type d’entité sont en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="3247b-110">Note that data surfaced through this entity type is read-only.</span></span>

<span data-ttu-id="3247b-111">Les requêtes paramétrées ne peuvent pas être exécutées en tant que requêtes de définition.</span><span class="sxs-lookup"><span data-stu-id="3247b-111">Parameterized queries cannot be executed as defining queries.</span></span> <span data-ttu-id="3247b-112">Toutefois, les données peuvent être mises à jour en mappant les fonctions d'insertion, de mise à jour et de suppression du type d'entité qui surface les données aux procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="3247b-112">However, the data can be updated by mapping the insert, update, and delete functions of the entity type that surfaces the data to stored procedures.</span></span> <span data-ttu-id="3247b-113">Pour plus d’informations, consultez [Insert, Update et Delete with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/cud).</span><span class="sxs-lookup"><span data-stu-id="3247b-113">For more information, see [Insert, Update, and Delete with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/cud).</span></span>

<span data-ttu-id="3247b-114">Cette rubrique montre comment effectuer les tâches suivantes.</span><span class="sxs-lookup"><span data-stu-id="3247b-114">This topic shows how to perform the following tasks.</span></span>

-   <span data-ttu-id="3247b-115">Ajouter une requête de définition</span><span class="sxs-lookup"><span data-stu-id="3247b-115">Add a Defining Query</span></span>
-   <span data-ttu-id="3247b-116">Ajouter un type d’entité au modèle</span><span class="sxs-lookup"><span data-stu-id="3247b-116">Add an Entity Type to the Model</span></span>
-   <span data-ttu-id="3247b-117">Mapper la requête de définition au type d’entité</span><span class="sxs-lookup"><span data-stu-id="3247b-117">Map the Defining Query to the Entity Type</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3247b-118">Prérequis</span><span class="sxs-lookup"><span data-stu-id="3247b-118">Prerequisites</span></span>

<span data-ttu-id="3247b-119">Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="3247b-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="3247b-120">Une version récente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3247b-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="3247b-121">[Exemple de base de données School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="3247b-121">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="3247b-122">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="3247b-122">Set up the Project</span></span>

<span data-ttu-id="3247b-123">Cette procédure pas à pas utilise Visual Studio 2012 ou une version plus récente.</span><span class="sxs-lookup"><span data-stu-id="3247b-123">This walkthrough is using Visual Studio 2012 or newer.</span></span>

-   <span data-ttu-id="3247b-124">Ouvrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3247b-124">Open Visual Studio.</span></span>
-   <span data-ttu-id="3247b-125">Dans le menu **Fichier** , pointez sur **Nouveau**, puis cliquez sur **Projet**.</span><span class="sxs-lookup"><span data-stu-id="3247b-125">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="3247b-126">Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **application console** .</span><span class="sxs-lookup"><span data-stu-id="3247b-126">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="3247b-127">Entrez **DefiningQuerySample** comme nom du projet, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="3247b-127">Enter **DefiningQuerySample** as the name of the project and click **OK**.</span></span>

 

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="3247b-128">Créer un modèle basé sur la base de données School</span><span class="sxs-lookup"><span data-stu-id="3247b-128">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="3247b-129">Cliquez avec le bouton droit sur le nom du projet dans Explorateur de solutions, pointez sur **Ajouter**, puis cliquez sur **nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="3247b-129">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="3247b-130">Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.</span><span class="sxs-lookup"><span data-stu-id="3247b-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="3247b-131">Entrez **DefiningQueryModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="3247b-131">Enter **DefiningQueryModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="3247b-132">Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant**.</span><span class="sxs-lookup"><span data-stu-id="3247b-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="3247b-133">Cliquez sur nouvelle connexion.</span><span class="sxs-lookup"><span data-stu-id="3247b-133">Click New Connection.</span></span> <span data-ttu-id="3247b-134">Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, **( \\ mssqllocaldb)**, sélectionnez la méthode d’authentification, tapez **School**   comme nom de la base de données, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="3247b-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="3247b-135">La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="3247b-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="3247b-136">Dans la boîte de dialogue choisir vos objets de base de données, vérifiez le nœud **tables**   .</span><span class="sxs-lookup"><span data-stu-id="3247b-136">In the Choose Your Database Objects dialog box, check the **Tables** node.</span></span> <span data-ttu-id="3247b-137">Cette opération ajoute toutes les tables au modèle **School** .</span><span class="sxs-lookup"><span data-stu-id="3247b-137">This will add all the tables to the **School** model.</span></span>
-   <span data-ttu-id="3247b-138">Cliquez sur **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="3247b-138">Click **Finish**.</span></span>
-   <span data-ttu-id="3247b-139">Dans Explorateur de solutions, cliquez avec le bouton droit sur le fichier **DefiningQueryModel. edmx** , puis sélectionnez **Ouvrir avec...**.</span><span class="sxs-lookup"><span data-stu-id="3247b-139">In Solution Explorer, right-click the **DefiningQueryModel.edmx** file and select **Open With…**.</span></span>
-   <span data-ttu-id="3247b-140">Sélectionnez **éditeur XML (texte)**.</span><span class="sxs-lookup"><span data-stu-id="3247b-140">Select **XML (Text) Editor**.</span></span>

    ![Éditeur XML](~/ef6/media/xmleditor.png)

-   <span data-ttu-id="3247b-142">Cliquez sur **Oui** si le message suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="3247b-142">Click **Yes** if prompted with the following message:</span></span>

    ![AVERTISSEMENT 2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a><span data-ttu-id="3247b-144">Ajouter une requête de définition</span><span class="sxs-lookup"><span data-stu-id="3247b-144">Add a Defining Query</span></span>

<span data-ttu-id="3247b-145">Dans cette étape, nous allons utiliser l’éditeur XML pour ajouter une requête de définition et un type d’entité à la section SSDL du fichier. edmx.</span><span class="sxs-lookup"><span data-stu-id="3247b-145">In this step we will use the XML Editor to add a defining query and an entity type to the SSDL section of the .edmx file.</span></span> 

-   <span data-ttu-id="3247b-146">Ajoutez un élément **EntitySet**   à la section SSDL du fichier. edmx (ligne 5 à 13).</span><span class="sxs-lookup"><span data-stu-id="3247b-146">Add an **EntitySet** element to the SSDL section of the .edmx file (line 5 thru 13).</span></span> <span data-ttu-id="3247b-147">Spécifiez les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="3247b-147">Specify the following:</span></span>
    -   <span data-ttu-id="3247b-148">Seuls les attributs **Name**   et **EntityType**   de l’élément **EntitySet**   sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="3247b-148">Only the **Name** and **EntityType** attributes of the **EntitySet** element are specified.</span></span>
    -   <span data-ttu-id="3247b-149">Le nom qualifié complet du type d’entité est utilisé dans l’attribut **EntityType**   .</span><span class="sxs-lookup"><span data-stu-id="3247b-149">The fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="3247b-150">L’instruction SQL à exécuter est spécifiée dans l’élément **DefiningQuery**   .</span><span class="sxs-lookup"><span data-stu-id="3247b-150">The SQL statement to be executed is specified in the **DefiningQuery** element.</span></span>

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   <span data-ttu-id="3247b-151">Ajoutez l’élément **EntityType** à la section SSDL du fichier. edmx.</span><span class="sxs-lookup"><span data-stu-id="3247b-151">Add the **EntityType** element to the SSDL section of the .edmx.</span></span> <span data-ttu-id="3247b-152">fichier comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3247b-152">file as shown below.</span></span> <span data-ttu-id="3247b-153">Notez les points suivants :</span><span class="sxs-lookup"><span data-stu-id="3247b-153">Note the following:</span></span>
    -   <span data-ttu-id="3247b-154">La valeur de l’attribut **Name** correspond à la valeur de l’attribut **EntityType** dans l’élément **EntitySet** ci-dessus, même si le nom complet du type d’entité est utilisé dans l’attribut **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="3247b-154">The value of the **Name** attribute corresponds to the value of the **EntityType** attribute in the **EntitySet** element above, although the fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="3247b-155">Les noms de propriété correspondent aux noms de colonnes retournés par l’instruction SQL dans l’élément **DefiningQuery** (ci-dessus).</span><span class="sxs-lookup"><span data-stu-id="3247b-155">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element (above).</span></span>
    -   <span data-ttu-id="3247b-156">Dans cet exemple, la clé d'entité est composée de trois propriétés pour garantir le caractère unique de la valeur de la clé.</span><span class="sxs-lookup"><span data-stu-id="3247b-156">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> <span data-ttu-id="3247b-157">Si vous exécutez ultérieurement la boîte de dialogue de l' **Assistant Mise à jour du modèle** , toutes les modifications apportées au modèle de stockage, y compris la définition des requêtes, seront remplacées.</span><span class="sxs-lookup"><span data-stu-id="3247b-157">If later you run the **Update Model Wizard** dialog, any changes made to the storage model, including defining queries, will be overwritten.</span></span>

 

## <a name="add-an-entity-type-to-the-model"></a><span data-ttu-id="3247b-158">Ajouter un type d’entité au modèle</span><span class="sxs-lookup"><span data-stu-id="3247b-158">Add an Entity Type to the Model</span></span>

<span data-ttu-id="3247b-159">Dans cette étape, nous allons ajouter le type d’entité au modèle conceptuel à l’aide du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="3247b-159">In this step we will add the entity type to the conceptual model using the EF Designer.</span></span> <span data-ttu-id="3247b-160">Notez les points suivants :</span><span class="sxs-lookup"><span data-stu-id="3247b-160"> Note the following:</span></span>

-   <span data-ttu-id="3247b-161">Le **nom** de l’entité correspond à la valeur de l’attribut **EntityType** dans l’élément **EntitySet** ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="3247b-161">The **Name** of the entity corresponds to the value of the **EntityType** attribute in the **EntitySet** element above.</span></span>
-   <span data-ttu-id="3247b-162">Les noms de propriété correspondent aux noms de colonnes retournés par l’instruction SQL dans l’élément **DefiningQuery** ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="3247b-162">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element above.</span></span>
-   <span data-ttu-id="3247b-163">Dans cet exemple, la clé d'entité est composée de trois propriétés pour garantir le caractère unique de la valeur de la clé.</span><span class="sxs-lookup"><span data-stu-id="3247b-163">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

<span data-ttu-id="3247b-164">Ouvrez le modèle dans le concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="3247b-164">Open the model in the EF Designer.</span></span>

-   <span data-ttu-id="3247b-165">Double-cliquez sur DefiningQueryModel. edmx.</span><span class="sxs-lookup"><span data-stu-id="3247b-165">Double-click the DefiningQueryModel.edmx.</span></span>
-   <span data-ttu-id="3247b-166">Disons **Oui** au message suivant :</span><span class="sxs-lookup"><span data-stu-id="3247b-166">Say **Yes** to the following message:</span></span>

    ![AVERTISSEMENT 2](~/ef6/media/warning2.png)

 

<span data-ttu-id="3247b-168">Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.</span><span class="sxs-lookup"><span data-stu-id="3247b-168">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

-   <span data-ttu-id="3247b-169">Cliquez avec le bouton droit sur l’aire du concepteur et sélectionnez **Ajouter une nouvelle** - &gt; **entité.**...</span><span class="sxs-lookup"><span data-stu-id="3247b-169">Right-click the designer surface and select **Add New**-&gt;**Entity…**.</span></span>
-   <span data-ttu-id="3247b-170">Spécifiez **GradeReport** pour le nom d’entité et le **CourseID** pour la **propriété de clé**.</span><span class="sxs-lookup"><span data-stu-id="3247b-170">Specify **GradeReport** for the entity name and **CourseID** for the **Key Property**.</span></span>
-   <span data-ttu-id="3247b-171">Cliquez avec le bouton droit sur l’entité **GradeReport** et sélectionnez **Ajouter une nouvelle** - &gt; **propriété scalaire**.</span><span class="sxs-lookup"><span data-stu-id="3247b-171">Right-click the **GradeReport** entity and select **Add New**-&gt; **Scalar Property**.</span></span>
-   <span data-ttu-id="3247b-172">Remplacez le nom par défaut de la propriété par **FirstName**.</span><span class="sxs-lookup"><span data-stu-id="3247b-172">Change the default name of the property to **FirstName**.</span></span>
-   <span data-ttu-id="3247b-173">Ajoutez une autre propriété scalaire et spécifiez **LastName** comme nom.</span><span class="sxs-lookup"><span data-stu-id="3247b-173">Add another scalar property and specify **LastName** for the name.</span></span>
-   <span data-ttu-id="3247b-174">Ajoutez une autre propriété scalaire et spécifiez le nom de la **classe** .</span><span class="sxs-lookup"><span data-stu-id="3247b-174">Add another scalar property and specify **Grade** for the name.</span></span>
-   <span data-ttu-id="3247b-175">Dans la fenêtre **Propriétés** , affectez à la propriété **type** de la **classe**la valeur **décimale**.</span><span class="sxs-lookup"><span data-stu-id="3247b-175">In the **Properties** window, change the **Grade**’s **Type** property to **Decimal**.</span></span>
-   <span data-ttu-id="3247b-176">Sélectionnez les propriétés **FirstName** et **LastName** .</span><span class="sxs-lookup"><span data-stu-id="3247b-176">Select the **FirstName** and **LastName** properties.</span></span>
-   <span data-ttu-id="3247b-177">Dans la fenêtre **Propriétés** , remplacez la valeur de la propriété **EntityKey** par **true**.</span><span class="sxs-lookup"><span data-stu-id="3247b-177">In the **Properties** window, change the **EntityKey** property value to **True**.</span></span>

<span data-ttu-id="3247b-178">Par conséquent, les éléments suivants ont été ajoutés à la section **CSDL** du fichier. edmx.</span><span class="sxs-lookup"><span data-stu-id="3247b-178">As a result, the following elements were added to the **CSDL** section of the .edmx file.</span></span>

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a><span data-ttu-id="3247b-179">Mapper la requête de définition au type d’entité</span><span class="sxs-lookup"><span data-stu-id="3247b-179">Map the Defining Query to the Entity Type</span></span>

<span data-ttu-id="3247b-180">Dans cette étape, nous allons utiliser la fenêtre Détails de mappage pour mapper les types d’entité conceptuels et de stockage.</span><span class="sxs-lookup"><span data-stu-id="3247b-180">In this step, we will use the Mapping Details window to map the conceptual and storage entity types.</span></span>

-   <span data-ttu-id="3247b-181">Cliquez avec le bouton droit sur l’entité **GradeReport** sur l’aire de conception, puis sélectionnez **mappage de table**.</span><span class="sxs-lookup"><span data-stu-id="3247b-181">Right-click the **GradeReport** entity on the design surface and select **Table Mapping**.</span></span>  
    <span data-ttu-id="3247b-182">La fenêtre **Détails de mappage** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="3247b-182">The **Mapping Details** window is displayed.</span></span>
-   <span data-ttu-id="3247b-183">Sélectionnez **GradeReport** dans la liste déroulante \*\* &lt; ajouter &gt; une table ou une vue\*\* (située sous la **table**s).</span><span class="sxs-lookup"><span data-stu-id="3247b-183">Select **GradeReport** from the **&lt;Add a Table or View&gt;** dropdown list (located under **Table**s).</span></span>  
    <span data-ttu-id="3247b-184">Les mappages par défaut entre le type d’entité conceptuel et stockage **GradeReport** s’affichent.</span><span class="sxs-lookup"><span data-stu-id="3247b-184">Default mappings between the conceptual and storage **GradeReport** entity type appear.</span></span>  
    <span data-ttu-id="3247b-185">![Mappage de Details3](~/ef6/media/mappingdetails.png)</span><span class="sxs-lookup"><span data-stu-id="3247b-185">![Mapping Details3](~/ef6/media/mappingdetails.png)</span></span>

<span data-ttu-id="3247b-186">Par conséquent, l’élément **EntitySetMapping**   est ajouté à la section Mapping du fichier. edmx.</span><span class="sxs-lookup"><span data-stu-id="3247b-186">As a result, the **EntitySetMapping** element is added to the mapping section of the .edmx file.</span></span> 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   <span data-ttu-id="3247b-187">Compilez l’application.</span><span class="sxs-lookup"><span data-stu-id="3247b-187">Compile the application.</span></span>

 

## <a name="call-the-defining-query-in-your-code"></a><span data-ttu-id="3247b-188">Appeler la requête de définition dans votre code</span><span class="sxs-lookup"><span data-stu-id="3247b-188">Call the Defining Query in your Code</span></span>

<span data-ttu-id="3247b-189">Vous pouvez maintenant exécuter la requête de définition à l’aide du type d’entité **GradeReport** .</span><span class="sxs-lookup"><span data-stu-id="3247b-189">You can now execute the defining query by using the **GradeReport** entity type.</span></span> 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
