---
title: Model First EF6
description: Model First dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/model-first
ms.openlocfilehash: b47c5805a63a1ea94baad4fb5eb137b633f6be1d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066029"
---
# <a name="model-first"></a><span data-ttu-id="25ac1-103">Model First</span><span class="sxs-lookup"><span data-stu-id="25ac1-103">Model First</span></span>
<span data-ttu-id="25ac1-104">Cette vidéo et la procédure pas à pas fournissent une introduction au développement Model First à l’aide de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="25ac1-104">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="25ac1-105">Model First vous permet de créer un nouveau modèle à l’aide du Entity Framework Designer puis de générer un schéma de base de données à partir du modèle.</span><span class="sxs-lookup"><span data-stu-id="25ac1-105">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="25ac1-106">Le modèle est stocké dans un fichier EDMX (extension. edmx) et peut être affiché et modifié dans le Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="25ac1-106">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="25ac1-107">Les classes avec lesquelles vous interagissez dans votre application sont générées automatiquement à partir du fichier EDMX.</span><span class="sxs-lookup"><span data-stu-id="25ac1-107">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="25ac1-108">Regarder la vidéo</span><span class="sxs-lookup"><span data-stu-id="25ac1-108">Watch the video</span></span>
<span data-ttu-id="25ac1-109">Cette vidéo et la procédure pas à pas fournissent une introduction au développement Model First à l’aide de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="25ac1-109">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="25ac1-110">Model First vous permet de créer un nouveau modèle à l’aide du Entity Framework Designer puis de générer un schéma de base de données à partir du modèle.</span><span class="sxs-lookup"><span data-stu-id="25ac1-110">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="25ac1-111">Le modèle est stocké dans un fichier EDMX (extension. edmx) et peut être affiché et modifié dans le Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="25ac1-111">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="25ac1-112">Les classes avec lesquelles vous interagissez dans votre application sont générées automatiquement à partir du fichier EDMX.</span><span class="sxs-lookup"><span data-stu-id="25ac1-112">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="25ac1-113">**Présentée par** : [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="25ac1-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="25ac1-114">**Vidéo**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv)  |  [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span><span class="sxs-lookup"><span data-stu-id="25ac1-114">**Video**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="25ac1-115">Prérequis</span><span class="sxs-lookup"><span data-stu-id="25ac1-115">Pre-Requisites</span></span>

<span data-ttu-id="25ac1-116">Pour effectuer cette procédure pas à pas, vous devez avoir installé Visual Studio 2010 ou Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="25ac1-116">You will need to have Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="25ac1-117">Si vous utilisez Visual Studio 2010, [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) doit également être installé.</span><span class="sxs-lookup"><span data-stu-id="25ac1-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="25ac1-118">1. créer l’application</span><span class="sxs-lookup"><span data-stu-id="25ac1-118">1. Create the Application</span></span>

<span data-ttu-id="25ac1-119">Pour simplifier les choses, nous allons créer une application console de base qui utilise le Model First pour effectuer l’accès aux données :</span><span class="sxs-lookup"><span data-stu-id="25ac1-119">To keep things simple we’re going to build a basic console application that uses the Model First to perform data access:</span></span>

-   <span data-ttu-id="25ac1-120">Ouvrir Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25ac1-120">Open Visual Studio</span></span>
-   <span data-ttu-id="25ac1-121">**Fichier- &gt; nouveau- &gt; projet...**</span><span class="sxs-lookup"><span data-stu-id="25ac1-121">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="25ac1-122">Sélectionnez **Windows** dans le menu de gauche et dans l' **application console** .</span><span class="sxs-lookup"><span data-stu-id="25ac1-122">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="25ac1-123">Entrez **ModelFirstSample** comme nom</span><span class="sxs-lookup"><span data-stu-id="25ac1-123">Enter **ModelFirstSample** as the name</span></span>
-   <span data-ttu-id="25ac1-124">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="25ac1-124">Select **OK**</span></span>

## <a name="2-create-model"></a><span data-ttu-id="25ac1-125">2. créer un modèle</span><span class="sxs-lookup"><span data-stu-id="25ac1-125">2. Create Model</span></span>

<span data-ttu-id="25ac1-126">Nous allons utiliser Entity Framework Designer, inclus dans le cadre de Visual Studio, pour créer notre modèle.</span><span class="sxs-lookup"><span data-stu-id="25ac1-126">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="25ac1-127">**Projet- &gt; Ajouter un nouvel élément...**</span><span class="sxs-lookup"><span data-stu-id="25ac1-127">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="25ac1-128">Sélectionnez **données** dans le menu de gauche, puis **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="25ac1-128">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="25ac1-129">Entrez **BloggingModel** comme nom et cliquez sur **OK**pour lancer l’Assistant Entity Data Model</span><span class="sxs-lookup"><span data-stu-id="25ac1-129">Enter **BloggingModel** as the name and click **OK**, this launches the Entity Data Model Wizard</span></span>
-   <span data-ttu-id="25ac1-130">Sélectionnez **modèle vide** , puis cliquez sur **Terminer**</span><span class="sxs-lookup"><span data-stu-id="25ac1-130">Select **Empty Model** and click **Finish**</span></span>

    ![Créer un modèle vide](~/ef6/media/createemptymodel.png)

<span data-ttu-id="25ac1-132">Le Entity Framework Designer est ouvert avec un modèle vide.</span><span class="sxs-lookup"><span data-stu-id="25ac1-132">The Entity Framework Designer is opened with a blank model.</span></span> <span data-ttu-id="25ac1-133">Nous pouvons maintenant commencer à ajouter des entités, des propriétés et des associations au modèle.</span><span class="sxs-lookup"><span data-stu-id="25ac1-133">Now we can start adding entities, properties and associations to the model.</span></span>

-   <span data-ttu-id="25ac1-134">Cliquez avec le bouton droit sur l’aire de conception et sélectionnez **Propriétés** .</span><span class="sxs-lookup"><span data-stu-id="25ac1-134">Right-click on the design surface and select **Properties**</span></span>
-   <span data-ttu-id="25ac1-135">Dans la fenêtre Propriétés remplacez le **nom du conteneur d’entités** par **BloggingContext**. 
     *il s’agit du nom du contexte dérivé qui sera généré pour vous, le contexte représente une session avec la base de données, ce qui nous permet d’interroger et d’enregistrer les données* .</span><span class="sxs-lookup"><span data-stu-id="25ac1-135">In the Properties window change the **Entity Container Name** to **BloggingContext**
*This is the name of the derived context that will be generated for you, the context represents a session with the database, allowing us to query and save data*</span></span>
-   <span data-ttu-id="25ac1-136">Cliquez avec le bouton droit sur l’aire de conception et sélectionnez **Ajouter une nouvelle &gt; entité...**</span><span class="sxs-lookup"><span data-stu-id="25ac1-136">Right-click on the design surface and select **Add New -&gt; Entity…**</span></span>
-   <span data-ttu-id="25ac1-137">Entrez **blog** comme nom de l’entité et **BlogId** comme nom de clé, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="25ac1-137">Enter **Blog** as the entity name and **BlogId** as the key name and click **OK**</span></span>

    ![Ajouter une entité de blog](~/ef6/media/addblogentity.png)

-   <span data-ttu-id="25ac1-139">Cliquez avec le bouton droit sur la nouvelle entité sur l’aire de conception et sélectionnez **Ajouter une nouvelle &gt; propriété-scalaire**, puis entrez **nom** comme nom de la propriété.</span><span class="sxs-lookup"><span data-stu-id="25ac1-139">Right-click on the new entity on the design surface and select **Add New -&gt; Scalar Property**, enter **Name** as the name of the property.</span></span>
-   <span data-ttu-id="25ac1-140">Répétez cette procédure pour ajouter une propriété **URL** .</span><span class="sxs-lookup"><span data-stu-id="25ac1-140">Repeat this process to add a **Url** property.</span></span>
-   <span data-ttu-id="25ac1-141">Cliquez avec le bouton droit sur la propriété **URL** sur l’aire de conception, puis sélectionnez **Propriétés**. dans la fenêtre Propriétés modifiez le paramètre **Nullable** en **true**. 
     *cela nous permet d’enregistrer un blog dans la base de données sans l’affecter à une URL*</span><span class="sxs-lookup"><span data-stu-id="25ac1-141">Right-click on the **Url** property on the design surface and select **Properties**, in the Properties window change the **Nullable** setting to **True**
*This allows us to save a Blog to the database without assigning it a Url*</span></span>
-   <span data-ttu-id="25ac1-142">À l’aide des techniques que vous venez d’apprendre, ajoutez une entité de **publication** avec une propriété de clé **PostId**</span><span class="sxs-lookup"><span data-stu-id="25ac1-142">Using the techniques you just learnt, add a **Post** entity with a **PostId** key property</span></span>
-   <span data-ttu-id="25ac1-143">Ajouter des propriétés scalaires de **titre** et de **contenu** à l’entité de **publication**</span><span class="sxs-lookup"><span data-stu-id="25ac1-143">Add **Title** and **Content** scalar properties to the **Post** entity</span></span>

<span data-ttu-id="25ac1-144">Maintenant que nous avons deux entités, il est temps d’ajouter une association (ou une relation) entre elles.</span><span class="sxs-lookup"><span data-stu-id="25ac1-144">Now that we have a couple of entities, it’s time to add an association (or relationship) between them.</span></span>

-   <span data-ttu-id="25ac1-145">Cliquez avec le bouton droit sur l’aire de conception et sélectionnez **Ajouter une nouvelle &gt; Association...**</span><span class="sxs-lookup"><span data-stu-id="25ac1-145">Right-click on the design surface and select **Add New -&gt; Association…**</span></span>
-   <span data-ttu-id="25ac1-146">Faire une extrémité du point de relation au **blog** avec une multiplicité d’un **et l'** autre point de terminaison à **publier** avec une multiplicité de **nombreux** 
     *cela signifie qu’un blog contient de nombreuses publications et qu’un billet appartient à un blog*</span><span class="sxs-lookup"><span data-stu-id="25ac1-146">Make one end of the relationship point to **Blog** with a multiplicity of **One** and the other end point to **Post** with a multiplicity of **Many**
*This means that a Blog has many Posts and a Post belongs to one Blog*</span></span>
-   <span data-ttu-id="25ac1-147">Vérifiez que la case **Ajouter les propriétés de clé étrangère à l’entité « poster »** est cochée, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="25ac1-147">Ensure the **Add foreign key properties to 'Post' Entity** box is checked and click **OK**</span></span>

    ![Ajouter une association MF](~/ef6/media/addassociationmf.png)

<span data-ttu-id="25ac1-149">Nous disposons à présent d’un modèle simple qui nous permet de générer une base de données et de l’utiliser pour lire et écrire des données.</span><span class="sxs-lookup"><span data-stu-id="25ac1-149">We now have a simple model that we can generate a database from and use to read and write data.</span></span>

![Modèle initial](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="25ac1-151">Étapes supplémentaires dans Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="25ac1-151">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="25ac1-152">Si vous travaillez dans Visual Studio 2010, vous devez suivre certaines étapes supplémentaires pour effectuer une mise à niveau vers la dernière version de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="25ac1-152">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="25ac1-153">La mise à niveau est importante, car elle vous permet d’accéder à une surface d’API améliorée, qui est beaucoup plus facile à utiliser, ainsi que les derniers correctifs de bogues.</span><span class="sxs-lookup"><span data-stu-id="25ac1-153">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="25ac1-154">Tout d’abord, nous devons récupérer la dernière version de Entity Framework à partir de NuGet.</span><span class="sxs-lookup"><span data-stu-id="25ac1-154">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="25ac1-155">**Projet – &gt; Gérer les packages NuGet...** 
     \*Si vous ne disposez pas de l’option **gérer les packages NuGet,** vous devez installer la [dernière version de NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) \*</span><span class="sxs-lookup"><span data-stu-id="25ac1-155">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="25ac1-156">Sélectionner l’onglet **en ligne**</span><span class="sxs-lookup"><span data-stu-id="25ac1-156">Select the **Online** tab</span></span>
-   <span data-ttu-id="25ac1-157">Sélectionner le package **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="25ac1-157">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="25ac1-158">Cliquez sur **Install**.</span><span class="sxs-lookup"><span data-stu-id="25ac1-158">Click **Install**</span></span>

<span data-ttu-id="25ac1-159">Ensuite, nous devons permuter notre modèle pour générer le code qui utilise l’API DbContext, qui a été introduite dans les versions ultérieures de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="25ac1-159">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="25ac1-160">Cliquez avec le bouton droit sur une zone vide de votre modèle dans le concepteur EF, puis sélectionnez **Ajouter un élément de génération de code...**</span><span class="sxs-lookup"><span data-stu-id="25ac1-160">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="25ac1-161">Sélectionnez **modèles en ligne** dans le menu de gauche et recherchez **DbContext**</span><span class="sxs-lookup"><span data-stu-id="25ac1-161">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="25ac1-162">Sélectionnez le **Générateur de DBCONTEXT EF 5. x pour \# C**, entrez **BloggingModel** comme nom et cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="25ac1-162">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![DbContext (modèle)](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a><span data-ttu-id="25ac1-164">3. génération de la base de données</span><span class="sxs-lookup"><span data-stu-id="25ac1-164">3. Generating the Database</span></span>

<span data-ttu-id="25ac1-165">Étant donné notre modèle, Entity Framework pouvez calculer un schéma de base de données qui nous permettra de stocker et de récupérer des données à l’aide du modèle.</span><span class="sxs-lookup"><span data-stu-id="25ac1-165">Given our model, Entity Framework can calculate a database schema that will allow us to store and retrieve data using the model.</span></span>

<span data-ttu-id="25ac1-166">Le serveur de base de données installé avec Visual Studio diffère selon la version de Visual Studio que vous avez installée :</span><span class="sxs-lookup"><span data-stu-id="25ac1-166">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="25ac1-167">Si vous utilisez Visual Studio 2010, vous allez créer une base de données SQL Express.</span><span class="sxs-lookup"><span data-stu-id="25ac1-167">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="25ac1-168">Si vous utilisez Visual Studio 2012, vous allez créer une base de [données de base](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) de données locale.</span><span class="sxs-lookup"><span data-stu-id="25ac1-168">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

<span data-ttu-id="25ac1-169">Commençons par générer la base de données.</span><span class="sxs-lookup"><span data-stu-id="25ac1-169">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="25ac1-170">Cliquez avec le bouton droit sur l’aire de conception et sélectionnez **générer la base de données à partir du modèle...**</span><span class="sxs-lookup"><span data-stu-id="25ac1-170">Right-click on the design surface and select **Generate Database from Model…**</span></span>
-   <span data-ttu-id="25ac1-171">Cliquez sur **nouvelle connexion...**</span><span class="sxs-lookup"><span data-stu-id="25ac1-171">Click **New Connection…**</span></span> <span data-ttu-id="25ac1-172">et spécifiez la base de données locale ou SQL Express, en fonction de la version de Visual Studio que vous utilisez, entrez **ModelFirst. blog** comme nom de la base de données.</span><span class="sxs-lookup"><span data-stu-id="25ac1-172">and specify either LocalDB or SQL Express, depending on which version of Visual Studio you are using, enter **ModelFirst.Blogging** as the database name.</span></span>

    ![Connexion de base de données locale MF](~/ef6/media/localdbconnectionmf.png)

    ![Connexion SQL Express MF](~/ef6/media/sqlexpressconnectionmf.png)

-   <span data-ttu-id="25ac1-175">Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="25ac1-175">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="25ac1-176">Sélectionnez **suivant** pour que le Entity Framework designer calcule un script pour créer le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="25ac1-176">Select **Next** and the Entity Framework Designer will calculate a script to create the database schema</span></span>
-   <span data-ttu-id="25ac1-177">Une fois le script affiché, cliquez sur **Terminer** pour ajouter le script à votre projet et l’ouvrir.</span><span class="sxs-lookup"><span data-stu-id="25ac1-177">Once the script is displayed, click **Finish** and the script will be added to your project and opened</span></span>
-   <span data-ttu-id="25ac1-178">Cliquez avec le bouton droit sur le script et sélectionnez **exécuter**. vous serez invité à spécifier la base de données à laquelle vous connecter, spécifiez la base de données locale ou la SQL Server Express, selon la version de Visual Studio que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="25ac1-178">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="25ac1-179">4. lecture & écriture de données</span><span class="sxs-lookup"><span data-stu-id="25ac1-179">4. Reading & Writing Data</span></span>

<span data-ttu-id="25ac1-180">Maintenant que nous avons un modèle, il est temps de l’utiliser pour accéder à certaines données.</span><span class="sxs-lookup"><span data-stu-id="25ac1-180">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="25ac1-181">Les classes que nous allons utiliser pour accéder aux données sont générées automatiquement pour vous en fonction du fichier EDMX.</span><span class="sxs-lookup"><span data-stu-id="25ac1-181">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="25ac1-182">*Cette capture d’écran provient de Visual Studio 2012, si vous utilisez Visual Studio 2010, les fichiers BloggingModel.tt et BloggingModel.Context.tt sont directement sous votre projet plutôt que imbriqués sous le fichier EDMX.*</span><span class="sxs-lookup"><span data-stu-id="25ac1-182">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![Classes générées](~/ef6/media/generatedclasses.png)

<span data-ttu-id="25ac1-184">Implémentez la méthode main dans Program.cs comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="25ac1-184">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="25ac1-185">Ce code crée une nouvelle instance de notre contexte, puis l’utilise pour insérer un nouveau blog.</span><span class="sxs-lookup"><span data-stu-id="25ac1-185">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="25ac1-186">Il utilise ensuite une requête LINQ pour récupérer tous les blogs de la base de données classée par ordre alphabétique par titre.</span><span class="sxs-lookup"><span data-stu-id="25ac1-186">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="25ac1-187">Vous pouvez maintenant exécuter l’application et la tester.</span><span class="sxs-lookup"><span data-stu-id="25ac1-187">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="25ac1-188">5. traitement des modifications de modèle</span><span class="sxs-lookup"><span data-stu-id="25ac1-188">5. Dealing with Model Changes</span></span>

<span data-ttu-id="25ac1-189">À présent, il est temps d’apporter des modifications à notre modèle, lorsque nous effectuons ces modifications, nous devons également mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="25ac1-189">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span>

<span data-ttu-id="25ac1-190">Nous allons commencer par ajouter une nouvelle entité utilisateur à notre modèle.</span><span class="sxs-lookup"><span data-stu-id="25ac1-190">We’ll start by adding a new User entity to our model.</span></span>

-   <span data-ttu-id="25ac1-191">Ajoutez un nouveau nom d’entité **utilisateur** avec nom **d'** utilisateur comme nom de clé et **chaîne** comme type de propriété pour la clé.</span><span class="sxs-lookup"><span data-stu-id="25ac1-191">Add a new **User** entity name with **Username** as the key name and **String** as the property type for the key</span></span>

    ![Ajouter une entité utilisateur](~/ef6/media/adduserentity.png)

-   <span data-ttu-id="25ac1-193">Cliquez avec le bouton droit sur la propriété **username** sur l’aire de conception, puis sélectionnez **Propriétés**. dans la fenêtre Propriétés modifiez le paramètre **MaxLength** sur **50**. 
     *cela limite les données pouvant être stockées dans le nom d’utilisateur à 50 caractères* .</span><span class="sxs-lookup"><span data-stu-id="25ac1-193">Right-click on the **Username** property on the design surface and select **Properties**, In the Properties window change the **MaxLength** setting to **50**
*This restricts the data that can be stored in username to 50 characters*</span></span>
-   <span data-ttu-id="25ac1-194">Ajouter une propriété scalaire **DisplayName** à l’entité **User**</span><span class="sxs-lookup"><span data-stu-id="25ac1-194">Add a **DisplayName** scalar property to the **User** entity</span></span>

<span data-ttu-id="25ac1-195">Nous disposons désormais d’un modèle mis à jour et nous sommes prêts à mettre à jour la base de données pour qu’elle s’adapte à notre nouveau type d’entité utilisateur.</span><span class="sxs-lookup"><span data-stu-id="25ac1-195">We now have an updated model and we are ready to update the database to accommodate our new User entity type.</span></span>

-   <span data-ttu-id="25ac1-196">Cliquez avec le bouton droit sur l’aire de conception, puis sélectionnez **générer la base de données à partir du modèle...**, Entity Framework calcule un script pour recréer un schéma basé sur le modèle mis à jour.</span><span class="sxs-lookup"><span data-stu-id="25ac1-196">Right-click on the design surface and select **Generate Database from Model…**, Entity Framework will calculate a script to recreate a schema based on the updated model.</span></span>
-   <span data-ttu-id="25ac1-197">Cliquez sur **Terminer**</span><span class="sxs-lookup"><span data-stu-id="25ac1-197">Click **Finish**</span></span>
-   <span data-ttu-id="25ac1-198">Vous pouvez recevoir des avertissements concernant le remplacement du script DDL existant et des parties de mappage et de stockage du modèle. cliquez sur **Oui** pour ces deux avertissements.</span><span class="sxs-lookup"><span data-stu-id="25ac1-198">You may receive warnings about overwriting the existing DDL script and the mapping and storage parts of the model, click **Yes** for both these warnings</span></span>
-   <span data-ttu-id="25ac1-199">Le script SQL mis à jour pour créer la base de données s’ouvre pour vous</span><span class="sxs-lookup"><span data-stu-id="25ac1-199">The updated SQL script to create the database is opened for you</span></span>  
    <span data-ttu-id="25ac1-200">*Le script généré supprimera toutes les tables existantes, puis recréera le schéma à partir de zéro. Cela peut fonctionner pour le développement local, mais n’est pas une solution viable pour envoyer des modifications à une base de données qui a déjà été déployée. Si vous avez besoin de publier des modifications dans une base de données qui a déjà été déployée, vous devez modifier le script ou utiliser un outil de comparaison de schémas pour calculer un script de migration.*</span><span class="sxs-lookup"><span data-stu-id="25ac1-200">*The script that is generated will drop all existing tables and then recreate the schema from scratch. This may work for local development but is not a viable for pushing changes to a database that has already been deployed. If you need to publish changes to a database that has already been deployed, you will need to edit the script or use a schema compare tool to calculate a migration script.*</span></span>
-   <span data-ttu-id="25ac1-201">Cliquez avec le bouton droit sur le script et sélectionnez **exécuter**. vous serez invité à spécifier la base de données à laquelle vous connecter, spécifiez la base de données locale ou la SQL Server Express, selon la version de Visual Studio que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="25ac1-201">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="summary"></a><span data-ttu-id="25ac1-202">Résumé</span><span class="sxs-lookup"><span data-stu-id="25ac1-202">Summary</span></span>

<span data-ttu-id="25ac1-203">Dans cette procédure pas à pas, nous avons examiné Model First développement, ce qui nous a permis de créer un modèle dans le concepteur EF, puis de générer une base de données à partir de ce modèle.</span><span class="sxs-lookup"><span data-stu-id="25ac1-203">In this walkthrough we looked at Model First development, which allowed us to create a model in the EF Designer and then generate a database from that model.</span></span> <span data-ttu-id="25ac1-204">Nous avons ensuite utilisé le modèle pour lire et écrire des données de la base de données.</span><span class="sxs-lookup"><span data-stu-id="25ac1-204">We then used the model to read and write some data from the database.</span></span> <span data-ttu-id="25ac1-205">Enfin, nous avons mis à jour le modèle, puis recréé le schéma de base de données pour qu’il corresponde au modèle.</span><span class="sxs-lookup"><span data-stu-id="25ac1-205">Finally, we updated the model and then recreated the database schema to match the model.</span></span>
