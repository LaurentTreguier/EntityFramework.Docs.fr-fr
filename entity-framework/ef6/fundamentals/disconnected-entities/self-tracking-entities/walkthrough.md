---
title: Procédure pas à pas des entités de suivi automatique-EF6
description: Procédure pas à pas pour les entités de suivi automatique pour Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough
ms.openlocfilehash: 398be11d330f5a7413f5a84424217ea3eda446ef
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072796"
---
# <a name="self-tracking-entities-walkthrough"></a><span data-ttu-id="878ea-103">Procédure pas à pas des entités de suivi automatique</span><span class="sxs-lookup"><span data-stu-id="878ea-103">Self-Tracking Entities Walkthrough</span></span>
> [!IMPORTANT]
> <span data-ttu-id="878ea-104">Nous ne recommandons plus d’utiliser le modèle des entités de suivi automatique.</span><span class="sxs-lookup"><span data-stu-id="878ea-104">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="878ea-105">Il reste disponible uniquement pour prendre en charge les applications existantes.</span><span class="sxs-lookup"><span data-stu-id="878ea-105">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="878ea-106">Si votre application doit utiliser des graphiques d’entités déconnectés, envisagez d’autres alternatives comme les [Entités traçables](https://trackableentities.github.io/), qui présentent une technologie similaire aux entités de suivi automatique, mais développée de manière plus active par la communauté, ou bien l’écriture de code personnalisé à l’aide des API de suivi de changements de bas niveau.</span><span class="sxs-lookup"><span data-stu-id="878ea-106">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](https://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>

<span data-ttu-id="878ea-107">Cette procédure pas à pas illustre le scénario dans lequel un service Windows Communication Foundation (WCF) expose une opération qui retourne un graphique d’entité.</span><span class="sxs-lookup"><span data-stu-id="878ea-107">This walkthrough demonstrates the scenario in which a Windows Communication Foundation (WCF) service exposes an operation that returns an entity graph.</span></span> <span data-ttu-id="878ea-108">Ensuite, une application cliente manipule ce graphique et soumet les modifications à une opération de service qui valide et enregistre les mises à jour dans une base de données à l’aide de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="878ea-108">Next, a client application manipulates that graph and submits the modifications to a service operation that validates and saves the updates to a database using Entity Framework.</span></span>

<span data-ttu-id="878ea-109">Avant d’effectuer cette procédure pas à pas, veillez à lire la page [entités de suivi automatique](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) .</span><span class="sxs-lookup"><span data-stu-id="878ea-109">Before completing this walkthrough make sure you read the [Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) page.</span></span>

<span data-ttu-id="878ea-110">Cette procédure pas à pas effectue les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="878ea-110">This walkthrough completes the following actions:</span></span>

-   <span data-ttu-id="878ea-111">Crée une base de données à laquelle accéder.</span><span class="sxs-lookup"><span data-stu-id="878ea-111">Creates a database to access.</span></span>
-   <span data-ttu-id="878ea-112">Crée une bibliothèque de classes qui contient le modèle.</span><span class="sxs-lookup"><span data-stu-id="878ea-112">Creates a class library that contains the model.</span></span>
-   <span data-ttu-id="878ea-113">Bascule vers le modèle générateur d’entité de suivi automatique.</span><span class="sxs-lookup"><span data-stu-id="878ea-113">Swaps to the Self-Tracking Entity Generator template.</span></span>
-   <span data-ttu-id="878ea-114">Déplace les classes d’entité vers un projet distinct.</span><span class="sxs-lookup"><span data-stu-id="878ea-114">Moves the entity classes to a separate project.</span></span>
-   <span data-ttu-id="878ea-115">Crée un service WCF qui expose des opérations pour interroger et enregistrer des entités.</span><span class="sxs-lookup"><span data-stu-id="878ea-115">Creates a WCF service that exposes operations to query and save entities.</span></span>
-   <span data-ttu-id="878ea-116">Crée des applications clientes (console et WPF) qui consomment le service.</span><span class="sxs-lookup"><span data-stu-id="878ea-116">Creates client applications (Console and WPF) that consume the service.</span></span>

<span data-ttu-id="878ea-117">Nous allons utiliser Database First dans cette procédure pas à pas, mais les mêmes techniques s’appliquent également à Model First.</span><span class="sxs-lookup"><span data-stu-id="878ea-117">We'll use Database First in this walkthrough but the same techniques apply equally to Model First.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="878ea-118">Prérequis</span><span class="sxs-lookup"><span data-stu-id="878ea-118">Pre-Requisites</span></span>

<span data-ttu-id="878ea-119">Pour effectuer cette procédure pas à pas, vous avez besoin d’une version récente de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="878ea-119">To complete this walkthrough you will need a recent version of Visual Studio.</span></span>

## <a name="create-a-database"></a><span data-ttu-id="878ea-120">Créer une base de données</span><span class="sxs-lookup"><span data-stu-id="878ea-120">Create a Database</span></span>

<span data-ttu-id="878ea-121">Le serveur de base de données installé avec Visual Studio diffère selon la version de Visual Studio que vous avez installée :</span><span class="sxs-lookup"><span data-stu-id="878ea-121">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="878ea-122">Si vous utilisez Visual Studio 2012, vous allez créer une base de données de base de données locale.</span><span class="sxs-lookup"><span data-stu-id="878ea-122">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="878ea-123">Si vous utilisez Visual Studio 2010, vous allez créer une base de données SQL Express.</span><span class="sxs-lookup"><span data-stu-id="878ea-123">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="878ea-124">Commençons par générer la base de données.</span><span class="sxs-lookup"><span data-stu-id="878ea-124">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="878ea-125">Ouvrir Visual Studio</span><span class="sxs-lookup"><span data-stu-id="878ea-125">Open Visual Studio</span></span>
-   <span data-ttu-id="878ea-126">**Vue- &gt; Explorateur de serveurs**</span><span class="sxs-lookup"><span data-stu-id="878ea-126">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="878ea-127">Cliquez avec le bouton droit sur **connexions de données- &gt; Ajouter une connexion...**</span><span class="sxs-lookup"><span data-stu-id="878ea-127">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="878ea-128">Si vous n’êtes pas connecté à une base de données à partir de Explorateur de serveurs avant de devoir sélectionner **Microsoft SQL Server** comme source de données</span><span class="sxs-lookup"><span data-stu-id="878ea-128">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="878ea-129">Connectez-vous à la base de données locale ou SQL Express, en fonction de celle que vous avez installée</span><span class="sxs-lookup"><span data-stu-id="878ea-129">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="878ea-130">Entrez **STESample** comme nom de la base de données</span><span class="sxs-lookup"><span data-stu-id="878ea-130">Enter **STESample** as the database name</span></span>
-   <span data-ttu-id="878ea-131">Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="878ea-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="878ea-132">La nouvelle base de données s’affiche à présent dans Explorateur de serveurs</span><span class="sxs-lookup"><span data-stu-id="878ea-132">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="878ea-133">Si vous utilisez Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="878ea-133">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="878ea-134">Cliquez avec le bouton de droite sur la base de données dans l’Explorateur de serveurs et sélectionnez l’option **Nouvelle requête**.</span><span class="sxs-lookup"><span data-stu-id="878ea-134">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="878ea-135">Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="878ea-135">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="878ea-136">Si vous utilisez Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="878ea-136">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="878ea-137">Sélectionner **des données- &gt; éditeur Transact SQL- &gt; nouvelle connexion à la requête...**</span><span class="sxs-lookup"><span data-stu-id="878ea-137">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="878ea-138">Entrez **. \\ SQLEXPRESS** comme nom de serveur, puis cliquez sur **OK**</span><span class="sxs-lookup"><span data-stu-id="878ea-138">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="878ea-139">Sélectionnez la base de données **STESample** dans la liste déroulante en haut de l’éditeur de requête.</span><span class="sxs-lookup"><span data-stu-id="878ea-139">Select the **STESample** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="878ea-140">Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **Exécuter SQL** .</span><span class="sxs-lookup"><span data-stu-id="878ea-140">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

``` SQL
    CREATE TABLE [dbo].[Blogs] (
        [BlogId] INT IDENTITY (1, 1) NOT NULL,
        [Name] NVARCHAR (200) NULL,
        [Url]  NVARCHAR (200) NULL,
        CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
    );

    CREATE TABLE [dbo].[Posts] (
        [PostId] INT IDENTITY (1, 1) NOT NULL,
        [Title] NVARCHAR (200) NULL,
        [Content] NTEXT NULL,
        [BlogId] INT NOT NULL,
        CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
        CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
    );

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a><span data-ttu-id="878ea-141">Créer le modèle</span><span class="sxs-lookup"><span data-stu-id="878ea-141">Create the Model</span></span>

<span data-ttu-id="878ea-142">Tout d’abord, nous avons besoin d’un projet dans lequel placer le modèle.</span><span class="sxs-lookup"><span data-stu-id="878ea-142">First up, we need a project to put the model in.</span></span>

-   <span data-ttu-id="878ea-143">**Fichier- &gt; nouveau- &gt; projet...**</span><span class="sxs-lookup"><span data-stu-id="878ea-143">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="878ea-144">Sélectionnez \*\*Visual C \# \*\* dans le volet gauche, puis **bibliothèque de classes**</span><span class="sxs-lookup"><span data-stu-id="878ea-144">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="878ea-145">Entrez **STESample** comme nom et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-145">Enter **STESample** as the name and click **OK**</span></span>

<span data-ttu-id="878ea-146">Nous allons maintenant créer un modèle simple dans le concepteur EF pour accéder à la base de données :</span><span class="sxs-lookup"><span data-stu-id="878ea-146">Now we'll create a simple model in the EF Designer to access our database:</span></span>

-   <span data-ttu-id="878ea-147">**Projet- &gt; Ajouter un nouvel élément...**</span><span class="sxs-lookup"><span data-stu-id="878ea-147">**Project -&gt; Add New Item...**</span></span>
-   <span data-ttu-id="878ea-148">Dans le volet gauche, sélectionnez **données** , puis **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="878ea-148">Select **Data** from the left pane and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="878ea-149">Entrez **BloggingModel** comme nom et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-149">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="878ea-150">Sélectionnez **générer à partir de la base de données** , puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="878ea-150">Select **Generate from database** and click **Next**</span></span>
-   <span data-ttu-id="878ea-151">Entrez les informations de connexion pour la base de données que vous avez créée dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="878ea-151">Enter the connection information for the database that you created in the previous section</span></span>
-   <span data-ttu-id="878ea-152">Entrez **BloggingContext** comme nom de la chaîne de connexion, puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="878ea-152">Enter **BloggingContext** as the name for the connection string and click **Next**</span></span>
-   <span data-ttu-id="878ea-153">Cochez la case en regard de **tables** , puis cliquez sur **Terminer** .</span><span class="sxs-lookup"><span data-stu-id="878ea-153">Check the box next to **Tables** and click **Finish**</span></span>

## <a name="swap-to-ste-code-generation"></a><span data-ttu-id="878ea-154">Basculer vers la génération de code STE</span><span class="sxs-lookup"><span data-stu-id="878ea-154">Swap to STE Code Generation</span></span>

<span data-ttu-id="878ea-155">Maintenant, nous devons désactiver la génération de code et l’échange par défaut pour les entités de suivi automatique.</span><span class="sxs-lookup"><span data-stu-id="878ea-155">Now we need to disable the default code generation and swap to Self-Tracking Entities.</span></span>

### <a name="if-you-are-using-visual-studio-2012"></a><span data-ttu-id="878ea-156">Si vous utilisez Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="878ea-156">If you are using Visual Studio 2012</span></span>

-   <span data-ttu-id="878ea-157">Développez **BloggingModel. edmx** dans **Explorateur de solutions** et supprimez le **BloggingModel.TT** et le **BloggingModel.Context.TT** 
     *la génération de code par défaut est désactivée* .</span><span class="sxs-lookup"><span data-stu-id="878ea-157">Expand **BloggingModel.edmx** in **Solution Explorer** and delete the **BloggingModel.tt** and **BloggingModel.Context.tt**
*This will disable the default code generation*</span></span>
-   <span data-ttu-id="878ea-158">Cliquez avec le bouton droit sur une zone vide de l’aire du concepteur EF, puis sélectionnez **Ajouter un élément de génération de code...**</span><span class="sxs-lookup"><span data-stu-id="878ea-158">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="878ea-159">Sélectionnez **en ligne** dans le volet gauche et recherchez le **Générateur Ste**</span><span class="sxs-lookup"><span data-stu-id="878ea-159">Select **Online** from the left pane and search for **STE Generator**</span></span>
-   <span data-ttu-id="878ea-160">Sélectionnez le **Générateur Ste pour le \# modèle C** , entrez **STETemplate** comme nom et cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="878ea-160">Select the **STE Generator for C\#** template, enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="878ea-161">Les fichiers **STETemplate.TT** et **STETemplate.Context.TT** sont ajoutés imbriqués sous le fichier BloggingModel. edmx</span><span class="sxs-lookup"><span data-stu-id="878ea-161">The **STETemplate.tt** and **STETemplate.Context.tt** files are added nested under the BloggingModel.edmx file</span></span>

### <a name="if-you-are-using-visual-studio-2010"></a><span data-ttu-id="878ea-162">Si vous utilisez Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="878ea-162">If you are using Visual Studio 2010</span></span>

-   <span data-ttu-id="878ea-163">Cliquez avec le bouton droit sur une zone vide de l’aire du concepteur EF, puis sélectionnez **Ajouter un élément de génération de code...**</span><span class="sxs-lookup"><span data-stu-id="878ea-163">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="878ea-164">Sélectionnez **code** dans le volet gauche, puis **Générateur d’entité de suivi automatique ADO.net**</span><span class="sxs-lookup"><span data-stu-id="878ea-164">Select **Code** from the left pane and then **ADO.NET Self-Tracking Entity Generator**</span></span>
-   <span data-ttu-id="878ea-165">Entrez **STETemplate** comme nom et cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="878ea-165">Enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="878ea-166">Les fichiers **STETemplate.TT** et **STETemplate.Context.TT** sont ajoutés directement à votre projet</span><span class="sxs-lookup"><span data-stu-id="878ea-166">The **STETemplate.tt** and **STETemplate.Context.tt** files are added directly to your project</span></span>

## <a name="move-entity-types-into-separate-project"></a><span data-ttu-id="878ea-167">Déplacer les types d’entités dans un projet distinct</span><span class="sxs-lookup"><span data-stu-id="878ea-167">Move Entity Types into Separate Project</span></span>

<span data-ttu-id="878ea-168">Pour utiliser les entités de suivi automatique, notre application cliente a besoin d’accéder aux classes d’entité générées à partir de notre modèle.</span><span class="sxs-lookup"><span data-stu-id="878ea-168">To use Self-Tracking Entities our client application needs access to the entity classes generated from our model.</span></span> <span data-ttu-id="878ea-169">Étant donné que nous ne souhaitons pas exposer l’ensemble du modèle à l’application cliente, nous allons déplacer les classes d’entité dans un projet distinct.</span><span class="sxs-lookup"><span data-stu-id="878ea-169">Because we don't want to expose the whole model to the client application we're going to move the entity classes into a separate project.</span></span>

<span data-ttu-id="878ea-170">La première étape consiste à arrêter la génération de classes d’entité dans le projet existant :</span><span class="sxs-lookup"><span data-stu-id="878ea-170">The first step is to stop generating entity classes in the existing project:</span></span>

-   <span data-ttu-id="878ea-171">Cliquez avec le bouton droit sur **STETemplate.TT** dans **Explorateur de solutions** et sélectionnez **Propriétés** .</span><span class="sxs-lookup"><span data-stu-id="878ea-171">Right-click on **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="878ea-172">Dans la fenêtre **Propriétés** , effacez **TextTemplatingFileGenerator** de la propriété **CustomTool**</span><span class="sxs-lookup"><span data-stu-id="878ea-172">In the **Properties** window clear **TextTemplatingFileGenerator** from the **CustomTool** property</span></span>
-   <span data-ttu-id="878ea-173">Développez **STETemplate.TT** dans **Explorateur de solutions** et supprimez tous les fichiers imbriqués sous celui-ci.</span><span class="sxs-lookup"><span data-stu-id="878ea-173">Expand **STETemplate.tt** in **Solution Explorer** and delete all files nested under it</span></span>

<span data-ttu-id="878ea-174">Ensuite, nous allons ajouter un nouveau projet et générer les classes d’entité qu’il contient.</span><span class="sxs-lookup"><span data-stu-id="878ea-174">Next, we are going to add a new project and generate the entity classes in it</span></span>

-   <span data-ttu-id="878ea-175">**Fichier- &gt; ajouter- &gt; projet...**</span><span class="sxs-lookup"><span data-stu-id="878ea-175">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="878ea-176">Sélectionnez \*\*Visual C \# \*\* dans le volet gauche, puis **bibliothèque de classes**</span><span class="sxs-lookup"><span data-stu-id="878ea-176">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="878ea-177">Entrez **STESample. Entities** comme nom et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-177">Enter **STESample.Entities** as the name and click **OK**</span></span>
-   <span data-ttu-id="878ea-178">**Projet- &gt; Ajouter un élément existant...**</span><span class="sxs-lookup"><span data-stu-id="878ea-178">**Project -&gt; Add Existing Item...**</span></span>
-   <span data-ttu-id="878ea-179">Accédez au dossier du projet **STESample**</span><span class="sxs-lookup"><span data-stu-id="878ea-179">Navigate to the **STESample** project folder</span></span>
-   <span data-ttu-id="878ea-180">Sélectionnez cette option pour afficher **tous les fichiers ( \* . \* )**</span><span class="sxs-lookup"><span data-stu-id="878ea-180">Select to view **All Files (\*.\*)**</span></span>
-   <span data-ttu-id="878ea-181">Sélectionner le fichier **STETemplate.TT**</span><span class="sxs-lookup"><span data-stu-id="878ea-181">Select the **STETemplate.tt** file</span></span>
-   <span data-ttu-id="878ea-182">Cliquez sur la flèche déroulante à côté du bouton **Ajouter** et sélectionnez **Ajouter en tant que lien**</span><span class="sxs-lookup"><span data-stu-id="878ea-182">Click on the drop down arrow next to the **Add** button and select **Add As Link**</span></span>

    ![Ajouter un modèle lié](~/ef6/media/addlinkedtemplate.png)

<span data-ttu-id="878ea-184">Nous allons également nous assurer que les classes d’entité sont générées dans le même espace de noms que le contexte.</span><span class="sxs-lookup"><span data-stu-id="878ea-184">We're also going to make sure the entity classes get generated in the same namespace as the context.</span></span> <span data-ttu-id="878ea-185">Cela réduit simplement le nombre d’instructions using que nous devons ajouter dans notre application.</span><span class="sxs-lookup"><span data-stu-id="878ea-185">This just reduces the number of using statements we need to add throughout our application.</span></span>

-   <span data-ttu-id="878ea-186">Cliquez avec le bouton droit sur le **STETemplate.TT** lié dans **Explorateur de solutions** et sélectionnez **Propriétés** .</span><span class="sxs-lookup"><span data-stu-id="878ea-186">Right-click on the linked **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="878ea-187">Dans la fenêtre **Propriétés** , définissez espace de noms de l' **outil personnalisé** sur **STESample**</span><span class="sxs-lookup"><span data-stu-id="878ea-187">In the **Properties** window set **Custom Tool Namespace** to **STESample**</span></span>

<span data-ttu-id="878ea-188">Le code généré par le modèle STE aura besoin d’une référence à **System. Runtime. Serialization** pour pouvoir être compilé.</span><span class="sxs-lookup"><span data-stu-id="878ea-188">The code generated by the STE template will need a reference to **System.Runtime.Serialization** in order to compile.</span></span> <span data-ttu-id="878ea-189">Cette bibliothèque est nécessaire pour les attributs **DataContract** et **DataMember** WCF utilisés sur les types d’entités sérialisables.</span><span class="sxs-lookup"><span data-stu-id="878ea-189">This library is needed for the WCF **DataContract** and **DataMember** attributes that are used on the serializable entity types.</span></span>

-   <span data-ttu-id="878ea-190">Cliquez avec le bouton droit sur le projet **STESample. Entities** dans **Explorateur de solutions** puis sélectionnez **Ajouter une référence...**</span><span class="sxs-lookup"><span data-stu-id="878ea-190">Right click on the **STESample.Entities** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="878ea-191">Dans Visual Studio 2012, activez la case à cocher en regard de **System. Runtime. Serialization** , puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-191">In Visual Studio 2012 - check the box next to **System.Runtime.Serialization** and click **OK**</span></span>
    -   <span data-ttu-id="878ea-192">Dans Visual Studio 2010, sélectionnez **System. Runtime. Serialization** , puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-192">In Visual Studio 2010 - select **System.Runtime.Serialization** and click **OK**</span></span>

<span data-ttu-id="878ea-193">Enfin, le projet avec notre contexte doit avoir une référence aux types d’entité.</span><span class="sxs-lookup"><span data-stu-id="878ea-193">Finally, the project with our context in it will need a reference to the entity types.</span></span>

-   <span data-ttu-id="878ea-194">Cliquez avec le bouton droit sur le projet **STESample** dans **Explorateur de solutions** puis sélectionnez **Ajouter une référence...**</span><span class="sxs-lookup"><span data-stu-id="878ea-194">Right click on the **STESample** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="878ea-195">Dans Visual Studio 2012-sélectionnez **solution** dans le volet gauche, cochez la case en regard de **STESample. Entities** , puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-195">In Visual Studio 2012 - select **Solution** from the left pane, check the box next to **STESample.Entities** and click **OK**</span></span>
    -   <span data-ttu-id="878ea-196">Dans Visual Studio 2010-sélectionnez l’onglet **projets** , sélectionnez **STESample. Entities** , puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-196">In Visual Studio 2010 - select the **Projects** tab, select **STESample.Entities** and click **OK**</span></span>

>[!NOTE]
> <span data-ttu-id="878ea-197">Une autre option pour déplacer les types d’entités vers un projet distinct consiste à déplacer le fichier de modèle au lieu de le lier à partir de son emplacement par défaut.</span><span class="sxs-lookup"><span data-stu-id="878ea-197">Another option for moving the entity types to a separate project is to move the template file, rather than linking it from its default location.</span></span> <span data-ttu-id="878ea-198">Si vous procédez ainsi, vous devrez mettre à jour la variable **FichierEntrée** dans le modèle pour fournir le chemin d’accès relatif au fichier edmx (dans cet exemple, il s’agit de **. \\ BloggingModel. edmx**).</span><span class="sxs-lookup"><span data-stu-id="878ea-198">If you do this, you will need to update the **inputFile** variable in the template to provide the relative path to the edmx file (in this example that would be **..\\BloggingModel.edmx**).</span></span>

## <a name="create-a-wcf-service"></a><span data-ttu-id="878ea-199">Créer un service WCF</span><span class="sxs-lookup"><span data-stu-id="878ea-199">Create a WCF Service</span></span>

<span data-ttu-id="878ea-200">Maintenant, il est temps d’ajouter un service WCF pour exposer nos données, nous allons commencer par créer le projet.</span><span class="sxs-lookup"><span data-stu-id="878ea-200">Now it's time to add a WCF Service to expose our data, we'll start by creating the project.</span></span>

-   <span data-ttu-id="878ea-201">**Fichier- &gt; ajouter- &gt; projet...**</span><span class="sxs-lookup"><span data-stu-id="878ea-201">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="878ea-202">Sélectionnez \*\*Visual C \# \*\* dans le volet gauche, puis **application de service WCF** .</span><span class="sxs-lookup"><span data-stu-id="878ea-202">Select **Visual C\#** from the left pane and then **WCF Service Application**</span></span>
-   <span data-ttu-id="878ea-203">Entrez **STESample. service** comme nom et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-203">Enter **STESample.Service** as the name and click **OK**</span></span>
-   <span data-ttu-id="878ea-204">Ajouter une référence à l’assembly **System. Data. Entity**</span><span class="sxs-lookup"><span data-stu-id="878ea-204">Add a reference to the **System.Data.Entity** assembly</span></span>
-   <span data-ttu-id="878ea-205">Ajouter une référence aux projets **STESample** et **STESample. Entities**</span><span class="sxs-lookup"><span data-stu-id="878ea-205">Add a reference to the **STESample** and **STESample.Entities** projects</span></span>

<span data-ttu-id="878ea-206">Nous devons copier la chaîne de connexion EF dans ce projet afin qu’elle soit trouvée lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="878ea-206">We need to copy the EF connection string to this project so that it is found at runtime.</span></span>

-   <span data-ttu-id="878ea-207">Ouvrez le fichier **App.Config** pour le projet **STESample **et copiez l’élément **connectionStrings**</span><span class="sxs-lookup"><span data-stu-id="878ea-207">Open the **App.Config** file for the **STESample **project and copy the **connectionStrings** element</span></span>
-   <span data-ttu-id="878ea-208">Collez l’élément **connectionStrings** en tant qu’élément enfant de l’élément **configuration** du fichier **Web.Config** dans le projet **STESample. service.**</span><span class="sxs-lookup"><span data-stu-id="878ea-208">Paste the **connectionStrings** element as a child element of the **configuration** element of the **Web.Config** file in the **STESample.Service** project</span></span>

<span data-ttu-id="878ea-209">Il est maintenant temps de mettre en œuvre le service réel.</span><span class="sxs-lookup"><span data-stu-id="878ea-209">Now it's time to implement the actual service.</span></span>

-   <span data-ttu-id="878ea-210">Ouvrez **IService1.cs** et remplacez le contenu par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="878ea-210">Open **IService1.cs** and replace the contents with the following code</span></span>

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   <span data-ttu-id="878ea-211">Ouvrez **Service1. svc** et remplacez le contenu par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="878ea-211">Open **Service1.svc** and replace the contents with the following code</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a><span data-ttu-id="878ea-212">Utiliser le service à partir d’une application console</span><span class="sxs-lookup"><span data-stu-id="878ea-212">Consume the Service from a Console Application</span></span>

<span data-ttu-id="878ea-213">Nous allons créer une application console qui utilise notre service.</span><span class="sxs-lookup"><span data-stu-id="878ea-213">Let's create a console application that uses our service.</span></span>

-   <span data-ttu-id="878ea-214">**Fichier- &gt; nouveau- &gt; projet...**</span><span class="sxs-lookup"><span data-stu-id="878ea-214">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="878ea-215">Sélectionnez \*\*Visual C \# \*\* dans le volet gauche, puis **application console** .</span><span class="sxs-lookup"><span data-stu-id="878ea-215">Select **Visual C\#** from the left pane and then **Console Application**</span></span>
-   <span data-ttu-id="878ea-216">Entrez **STESample. ConsoleTest** comme nom et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-216">Enter **STESample.ConsoleTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="878ea-217">Ajouter une référence au projet **STESample. Entities**</span><span class="sxs-lookup"><span data-stu-id="878ea-217">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="878ea-218">Nous avons besoin d’une référence de service à notre service WCF</span><span class="sxs-lookup"><span data-stu-id="878ea-218">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="878ea-219">Cliquez avec le bouton droit sur le projet **STESample. ConsoleTest** dans **Explorateur de solutions** puis sélectionnez **Ajouter une référence de service...**</span><span class="sxs-lookup"><span data-stu-id="878ea-219">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="878ea-220">Cliquez sur **découvrir**</span><span class="sxs-lookup"><span data-stu-id="878ea-220">Click **Discover**</span></span>
-   <span data-ttu-id="878ea-221">Entrez **BloggingService** comme espace de noms, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-221">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="878ea-222">Nous pouvons maintenant écrire du code pour consommer le service.</span><span class="sxs-lookup"><span data-stu-id="878ea-222">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="878ea-223">Ouvrez **Program.cs** et remplacez le contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="878ea-223">Open **Program.cs** and replace the contents with the following code.</span></span>

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

<span data-ttu-id="878ea-224">Vous pouvez à présent exécuter l’application pour la voir en action.</span><span class="sxs-lookup"><span data-stu-id="878ea-224">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="878ea-225">Cliquez avec le bouton droit sur le projet **STESample. ConsoleTest** dans **Explorateur de solutions** puis sélectionnez **Déboguer- &gt; Démarrer une nouvelle instance**</span><span class="sxs-lookup"><span data-stu-id="878ea-225">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>

<span data-ttu-id="878ea-226">La sortie suivante s’affiche lors de l’exécution de l’application.</span><span class="sxs-lookup"><span data-stu-id="878ea-226">You'll see the following output when the application executes.</span></span>

```console
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a><span data-ttu-id="878ea-227">Utiliser le service à partir d’une application WPF</span><span class="sxs-lookup"><span data-stu-id="878ea-227">Consume the Service from a WPF Application</span></span>

<span data-ttu-id="878ea-228">Nous allons créer une application WPF qui utilise notre service.</span><span class="sxs-lookup"><span data-stu-id="878ea-228">Let's create a WPF application that uses our service.</span></span>

-   <span data-ttu-id="878ea-229">**Fichier- &gt; nouveau- &gt; projet...**</span><span class="sxs-lookup"><span data-stu-id="878ea-229">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="878ea-230">Sélectionnez \*\*Visual C \# \*\* dans le volet gauche, puis **application WPF** .</span><span class="sxs-lookup"><span data-stu-id="878ea-230">Select **Visual C\#** from the left pane and then **WPF Application**</span></span>
-   <span data-ttu-id="878ea-231">Entrez **STESample. WPFTest** comme nom et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-231">Enter **STESample.WPFTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="878ea-232">Ajouter une référence au projet **STESample. Entities**</span><span class="sxs-lookup"><span data-stu-id="878ea-232">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="878ea-233">Nous avons besoin d’une référence de service à notre service WCF</span><span class="sxs-lookup"><span data-stu-id="878ea-233">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="878ea-234">Cliquez avec le bouton droit sur le projet **STESample. WPFTest** dans **Explorateur de solutions** puis sélectionnez **Ajouter une référence de service...**</span><span class="sxs-lookup"><span data-stu-id="878ea-234">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="878ea-235">Cliquez sur **découvrir**</span><span class="sxs-lookup"><span data-stu-id="878ea-235">Click **Discover**</span></span>
-   <span data-ttu-id="878ea-236">Entrez **BloggingService** comme espace de noms, puis cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="878ea-236">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="878ea-237">Nous pouvons maintenant écrire du code pour consommer le service.</span><span class="sxs-lookup"><span data-stu-id="878ea-237">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="878ea-238">Ouvrez **MainWindow. Xaml** et remplacez le contenu par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="878ea-238">Open **MainWindow.xaml** and replace the contents with the following code.</span></span>

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   <span data-ttu-id="878ea-239">Ouvrez le code-behind pour MainWindow (**MainWindow.Xaml.cs**) et remplacez le contenu par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="878ea-239">Open the code behind for MainWindow (**MainWindow.xaml.cs**) and replace the contents with the following code</span></span>

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

<span data-ttu-id="878ea-240">Vous pouvez à présent exécuter l’application pour la voir en action.</span><span class="sxs-lookup"><span data-stu-id="878ea-240">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="878ea-241">Cliquez avec le bouton droit sur le projet **STESample. WPFTest** dans **Explorateur de solutions** puis sélectionnez **Déboguer- &gt; Démarrer une nouvelle instance**</span><span class="sxs-lookup"><span data-stu-id="878ea-241">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>
-   <span data-ttu-id="878ea-242">Vous pouvez manipuler les données à l’aide de l’écran et les enregistrer par le biais du service à l’aide du bouton **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="878ea-242">You can manipulate the data using the screen and save it via the service using the **Save** button</span></span>

![Fenêtre principale WPF](~/ef6/media/wpf.png)
