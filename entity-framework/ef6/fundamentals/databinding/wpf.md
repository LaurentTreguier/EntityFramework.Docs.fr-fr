---
title: Liaison de liaison avec WPF-EF6
author: divega
ms.date: 05/19/2020
ms.assetid: e90d48e6-bea7785-47ef-b756-7b89cce4daf0
ms.openlocfilehash: baeb75b1ee386ca58013048bcc31ea4074604673
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526847"
---
# <a name="databinding-with-wpf"></a><span data-ttu-id="cd310-102">Liaison de données avec WPF</span><span class="sxs-lookup"><span data-stu-id="cd310-102">Databinding with WPF</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cd310-103">**Ce document est valide pour WPF sur la .NET Framework uniquement**</span><span class="sxs-lookup"><span data-stu-id="cd310-103">**This document is valid for WPF on the .NET Framework only**</span></span>
>
> <span data-ttu-id="cd310-104">Ce document décrit la liaison de liaison pour WPF sur le .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="cd310-104">This document describes databinding for WPF on the .NET Framework.</span></span> <span data-ttu-id="cd310-105">Pour les nouveaux projets .NET Core, nous vous recommandons d’utiliser [EF Core](/ef/core) au lieu de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="cd310-105">For new .NET Core projects, we recommend you use [EF Core](/ef/core) instead of Entity Framework 6.</span></span> <span data-ttu-id="cd310-106">La documentation relative à la liaison de la liaison de EF Core est ici : [prise en main avec WPF](/ef/core/get-started/wpf).</span><span class="sxs-lookup"><span data-stu-id="cd310-106">The documentation for databinding in EF Core is here: [Getting Started with WPF](/ef/core/get-started/wpf).</span></span>

<span data-ttu-id="cd310-107">Cette procédure pas à pas montre comment lier des types POCO à des contrôles WPF dans un formulaire « maître/détail ».</span><span class="sxs-lookup"><span data-stu-id="cd310-107">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="cd310-108">L’application utilise les API Entity Framework pour remplir les objets avec les données de la base de données, effectuer le suivi des modifications et conserver les données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="cd310-108">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="cd310-109">Le modèle définit deux types qui participent à une relation un-à-plusieurs : **Category** ( \\ principal principal) et **Product** (détail dépendante \\ ).</span><span class="sxs-lookup"><span data-stu-id="cd310-109">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="cd310-110">Les outils Visual Studio sont ensuite utilisés pour lier les types définis dans le modèle aux contrôles WPF.</span><span class="sxs-lookup"><span data-stu-id="cd310-110">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="cd310-111">L’infrastructure de liaison de données WPF permet de naviguer entre les objets connexes : la sélection de lignes dans le mode maître entraîne la mise à jour de la vue détaillée avec les données enfants correspondantes.</span><span class="sxs-lookup"><span data-stu-id="cd310-111">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="cd310-112">Les captures d’écran et les listes de codes de cette procédure pas à pas sont extraites de Visual Studio 2013 mais vous pouvez effectuer cette procédure pas à pas avec Visual Studio 2012 ou Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="cd310-112">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="cd310-113">Utiliser l’option « Object » pour créer des sources de données WPF</span><span class="sxs-lookup"><span data-stu-id="cd310-113">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="cd310-114">Avec la version précédente de Entity Framework nous avons utilisé pour recommander l’utilisation de l’option **de base de données** lors de la création d’une source de données basée sur un modèle créé à l’aide du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="cd310-114">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="cd310-115">Cela était dû au fait que le concepteur générait un contexte dérivé d’ObjectContext et des classes d’entité dérivées de EntityObject.</span><span class="sxs-lookup"><span data-stu-id="cd310-115">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="cd310-116">L’utilisation de l’option de base de données vous aidera à écrire le meilleur code pour interagir avec cette surface d’API.</span><span class="sxs-lookup"><span data-stu-id="cd310-116">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="cd310-117">Les concepteurs EF pour Visual Studio 2012 et Visual Studio 2013 générer un contexte qui dérive de DbContext avec des classes d’entité POCO simples.</span><span class="sxs-lookup"><span data-stu-id="cd310-117">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="cd310-118">Avec Visual Studio 2010, nous vous recommandons de passer à un modèle de génération de code qui utilise DbContext, comme décrit plus loin dans cette procédure pas à pas.</span><span class="sxs-lookup"><span data-stu-id="cd310-118">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="cd310-119">Lorsque vous utilisez la surface de l’API DbContext, vous devez utiliser l’option d' **objet** lors de la création d’une source de données, comme indiqué dans cette procédure pas à pas.</span><span class="sxs-lookup"><span data-stu-id="cd310-119">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="cd310-120">Si nécessaire, vous pouvez [revenir à la génération de code basé sur ObjectContext](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) pour les modèles créés à l’aide du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="cd310-120">If needed, you can [revert to ObjectContext based code generation](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="cd310-121">Prérequis</span><span class="sxs-lookup"><span data-stu-id="cd310-121">Pre-Requisites</span></span>

<span data-ttu-id="cd310-122">Pour effectuer cette procédure pas à pas, vous devez installer Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="cd310-122">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="cd310-123">Si vous utilisez Visual Studio 2010, vous devez également installer NuGet.</span><span class="sxs-lookup"><span data-stu-id="cd310-123">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="cd310-124">Pour plus d’informations, consultez [installation de NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools).</span><span class="sxs-lookup"><span data-stu-id="cd310-124">For more information, see [Installing NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="cd310-125">Création de l’application</span><span class="sxs-lookup"><span data-stu-id="cd310-125">Create the Application</span></span>

-   <span data-ttu-id="cd310-126">Ouvrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd310-126">Open Visual Studio</span></span>
-   <span data-ttu-id="cd310-127">**Fichier- &gt; nouveau- &gt; projet....**</span><span class="sxs-lookup"><span data-stu-id="cd310-127">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="cd310-128">Sélectionnez **fenêtres**   dans le volet gauche et **WPFApplication** dans le volet droit</span><span class="sxs-lookup"><span data-stu-id="cd310-128">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
-   <span data-ttu-id="cd310-129">Entrez **WPFwithEFSample**   comme nom</span><span class="sxs-lookup"><span data-stu-id="cd310-129">Enter **WPFwithEFSample** as the name</span></span>
-   <span data-ttu-id="cd310-130">Sélectionnez **OK**</span><span class="sxs-lookup"><span data-stu-id="cd310-130">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="cd310-131">Installer le package NuGet Entity Framework</span><span class="sxs-lookup"><span data-stu-id="cd310-131">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="cd310-132">Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet **WinFormswithEFSample**</span><span class="sxs-lookup"><span data-stu-id="cd310-132">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="cd310-133">Sélectionnez **gérer les packages NuGet...**</span><span class="sxs-lookup"><span data-stu-id="cd310-133">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="cd310-134">Dans la boîte de dialogue gérer les packages NuGet, sélectionnez l’onglet **en ligne** et choisissez le package **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="cd310-134">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="cd310-135">Cliquez sur **Install**.</span><span class="sxs-lookup"><span data-stu-id="cd310-135">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="cd310-136">En plus de l’assembly EntityFramework, une référence à System. ComponentModel. DataAnnotations est également ajoutée.</span><span class="sxs-lookup"><span data-stu-id="cd310-136">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="cd310-137">Si le projet a une référence à System. Data. Entity, il sera supprimé lors de l’installation du package EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="cd310-137">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="cd310-138">L’assembly System. Data. Entity n’est plus utilisé pour les applications Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="cd310-138">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="cd310-139">Définir un modèle</span><span class="sxs-lookup"><span data-stu-id="cd310-139">Define a Model</span></span>

<span data-ttu-id="cd310-140">Dans cette procédure pas à pas, vous pouvez choisir d’implémenter un modèle à l’aide de Code First ou du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="cd310-140">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="cd310-141">Suivez l’une des deux sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="cd310-141">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="cd310-142">Option 1 : définir un modèle à l’aide de Code First</span><span class="sxs-lookup"><span data-stu-id="cd310-142">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="cd310-143">Cette section montre comment créer un modèle et la base de données qui lui est associée à l’aide de Code First.</span><span class="sxs-lookup"><span data-stu-id="cd310-143">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="cd310-144">Passez à la section suivante (**option 2 : définir un modèle à l’aide de Database First)** si vous préférez utiliser Database First pour rétroconcevoir votre modèle à partir d’une base de données à l’aide du concepteur EF</span><span class="sxs-lookup"><span data-stu-id="cd310-144">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="cd310-145">Lors de l’utilisation de Code First développement, vous commencez généralement par écrire des classes .NET Framework qui définissent votre modèle conceptuel (domaine).</span><span class="sxs-lookup"><span data-stu-id="cd310-145">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="cd310-146">Ajoutez une nouvelle classe à **WPFwithEFSample :**</span><span class="sxs-lookup"><span data-stu-id="cd310-146">Add a new class to the **WPFwithEFSample:**</span></span>
    -   <span data-ttu-id="cd310-147">Cliquez avec le bouton droit sur le nom du projet</span><span class="sxs-lookup"><span data-stu-id="cd310-147">Right-click on the project name</span></span>
    -   <span data-ttu-id="cd310-148">Sélectionnez **Ajouter**, puis **nouvel élément** .</span><span class="sxs-lookup"><span data-stu-id="cd310-148">Select **Add**, then **New Item**</span></span>
    -   <span data-ttu-id="cd310-149">Sélectionner une **classe** et entrer le **produit**   pour le nom de la classe</span><span class="sxs-lookup"><span data-stu-id="cd310-149">Select **Class** and enter **Product** for the class name</span></span>
-   <span data-ttu-id="cd310-150">Remplacez la **Product**   définition de la classe Product par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="cd310-150">Replace the **Product** class definition with the following code:</span></span>

``` csharp
    namespace WPFwithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }

-   Add a **Category** class with the following definition:

    using System.Collections.ObjectModel;

    namespace WPFwithEFSample
    {
        public class Category
        {
            public Category()
            {
                this.Products = new ObservableCollection<Product>();
            }

            public int CategoryId { get; set; }
            public string Name { get; set; }

            public virtual ObservableCollection<Product> Products { get; private set; }
        }
    }
```

<span data-ttu-id="cd310-151">La propriété **Products** de la classe **Category** et la propriété **Category** de la classe **Product** sont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="cd310-151">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="cd310-152">Dans Entity Framework, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="cd310-152">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="cd310-153">En plus de définir des entités, vous devez définir une classe qui dérive de DbContext et expose les &lt; Propriétés de la tente DbSet &gt; .</span><span class="sxs-lookup"><span data-stu-id="cd310-153">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="cd310-154">Les propriétés de la tente de DbSet &lt; &gt; permettent au contexte de savoir quels types vous souhaitez inclure dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="cd310-154">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="cd310-155">Une instance du type dérivé DbContext gère les objets d’entité au moment de l’exécution, ce qui comprend le remplissage des objets avec les données d’une base de données, le suivi des modifications et la persistance des données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="cd310-155">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="cd310-156">Ajoutez une nouvelle classe **ProductContext** au projet avec la définition suivante :</span><span class="sxs-lookup"><span data-stu-id="cd310-156">Add a new **ProductContext** class to the project with the following definition:</span></span>

``` csharp
    using System.Data.Entity;

    namespace WPFwithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="cd310-157">Compilez le projet.</span><span class="sxs-lookup"><span data-stu-id="cd310-157">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="cd310-158">Option 2 : définir un modèle à l’aide de Database First</span><span class="sxs-lookup"><span data-stu-id="cd310-158">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="cd310-159">Cette section montre comment utiliser Database First pour rétroconcevoir votre modèle à partir d’une base de données à l’aide du concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="cd310-159">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="cd310-160">Si vous avez terminé la section précédente (**option 1 : définir un modèle à l’aide de code First)**, ignorez cette section et passez directement à la section **chargement différé** .</span><span class="sxs-lookup"><span data-stu-id="cd310-160">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="cd310-161">Créer une base de données existante</span><span class="sxs-lookup"><span data-stu-id="cd310-161">Create an Existing Database</span></span>

<span data-ttu-id="cd310-162">En général, lorsque vous ciblez une base de données existante, elle est déjà créée, mais pour cette procédure pas à pas, nous devons créer une base de données à laquelle accéder.</span><span class="sxs-lookup"><span data-stu-id="cd310-162">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="cd310-163">Le serveur de base de données installé avec Visual Studio diffère selon la version de Visual Studio que vous avez installée :</span><span class="sxs-lookup"><span data-stu-id="cd310-163">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="cd310-164">Si vous utilisez Visual Studio 2010, vous allez créer une base de données SQL Express.</span><span class="sxs-lookup"><span data-stu-id="cd310-164">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="cd310-165">Si vous utilisez Visual Studio 2012, vous allez créer une base de [données de base](https://msdn.microsoft.com/library/hh510202.aspx) de données locale.</span><span class="sxs-lookup"><span data-stu-id="cd310-165">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="cd310-166">Commençons par générer la base de données.</span><span class="sxs-lookup"><span data-stu-id="cd310-166">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="cd310-167">**Vue- &gt; Explorateur de serveurs**</span><span class="sxs-lookup"><span data-stu-id="cd310-167">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="cd310-168">Cliquez avec le bouton droit sur **connexions de données- &gt; Ajouter une connexion...**</span><span class="sxs-lookup"><span data-stu-id="cd310-168">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="cd310-169">Si vous n’êtes pas connecté à une base de données à partir de Explorateur de serveurs avant de devoir sélectionner Microsoft SQL Server comme source de données</span><span class="sxs-lookup"><span data-stu-id="cd310-169">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![Changer la source de données](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="cd310-171">Connectez-vous à la base de données locale ou SQL Express, en fonction de celle que vous avez installée, puis entrez **Products** comme nom de la base de données.</span><span class="sxs-lookup"><span data-stu-id="cd310-171">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![Ajouter une base de données locale de connexion](~/ef6/media/addconnectionlocaldb.png)

    ![Ajouter une connexion Express](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="cd310-174">Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="cd310-174">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![Créer une base de données](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="cd310-176">La nouvelle base de données s’affiche alors dans Explorateur de serveurs, cliquez dessus avec le bouton droit et sélectionnez **nouvelle requête** .</span><span class="sxs-lookup"><span data-stu-id="cd310-176">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="cd310-177">Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="cd310-177">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
    CREATE TABLE [dbo].[Categories] (
        [CategoryId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        CONSTRAINT [PK_dbo.Categories] PRIMARY KEY ([CategoryId])
    )

    CREATE TABLE [dbo].[Products] (
        [ProductId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        [CategoryId] [int] NOT NULL,
        CONSTRAINT [PK_dbo.Products] PRIMARY KEY ([ProductId])
    )

    CREATE INDEX [IX_CategoryId] ON [dbo].[Products]([CategoryId])

    ALTER TABLE [dbo].[Products] ADD CONSTRAINT [FK_dbo.Products_dbo.Categories_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
```

#### <a name="reverse-engineer-model"></a><span data-ttu-id="cd310-178">Modèle d’ingénierie à rebours</span><span class="sxs-lookup"><span data-stu-id="cd310-178">Reverse Engineer Model</span></span>

<span data-ttu-id="cd310-179">Nous allons utiliser Entity Framework Designer, inclus dans le cadre de Visual Studio, pour créer notre modèle.</span><span class="sxs-lookup"><span data-stu-id="cd310-179">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="cd310-180">**Projet- &gt; Ajouter un nouvel élément...**</span><span class="sxs-lookup"><span data-stu-id="cd310-180">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="cd310-181">Sélectionnez **données** dans le menu de gauche, puis **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="cd310-181">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="cd310-182">Entrez **ProductModel** comme nom et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="cd310-182">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="cd310-183">Cela lance l' **assistant Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="cd310-183">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="cd310-184">Sélectionnez **générer à partir de la base de données** , puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="cd310-184">Select **Generate from Database** and click **Next**</span></span>

    ![Choisir le contenu du modèle](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="cd310-186">Sélectionnez la connexion à la base de données que vous avez créée dans la première section, entrez **ProductContext** comme nom de la chaîne de connexion, puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="cd310-186">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![Choisir votre connexion](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="cd310-188">Cochez la case en regard de « tables » pour importer toutes les tables, puis cliquez sur « Terminer ».</span><span class="sxs-lookup"><span data-stu-id="cd310-188">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![Choisir vos objets](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="cd310-190">Une fois le processus d’ingénierie à rebours terminé, le nouveau modèle est ajouté à votre projet et vous est ouvert pour que vous l’affichez dans le Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="cd310-190">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="cd310-191">Un fichier de App.config a également été ajouté à votre projet avec les détails de connexion de la base de données.</span><span class="sxs-lookup"><span data-stu-id="cd310-191">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="cd310-192">Étapes supplémentaires dans Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="cd310-192">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="cd310-193">Si vous travaillez dans Visual Studio 2010, vous devrez mettre à jour le concepteur EF pour utiliser la génération de code EF6.</span><span class="sxs-lookup"><span data-stu-id="cd310-193">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="cd310-194">Cliquez avec le bouton droit sur une zone vide de votre modèle dans le concepteur EF, puis sélectionnez **Ajouter un élément de génération de code...**</span><span class="sxs-lookup"><span data-stu-id="cd310-194">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="cd310-195">Sélectionnez **modèles en ligne** dans le menu de gauche et recherchez **DbContext**</span><span class="sxs-lookup"><span data-stu-id="cd310-195">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="cd310-196">Sélectionnez le **Générateur de DbContext EF 6. x pour C \# ,** entrez **ProductsModel** comme nom et cliquez sur Ajouter.</span><span class="sxs-lookup"><span data-stu-id="cd310-196">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="cd310-197">Mise à jour de la génération de code pour la liaison de données</span><span class="sxs-lookup"><span data-stu-id="cd310-197">Updating code generation for data binding</span></span>

<span data-ttu-id="cd310-198">EF génère du code à partir de votre modèle à l’aide de modèles T4.</span><span class="sxs-lookup"><span data-stu-id="cd310-198">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="cd310-199">Les modèles fournis avec Visual Studio ou téléchargés à partir de la Galerie Visual Studio sont destinés à un usage général.</span><span class="sxs-lookup"><span data-stu-id="cd310-199">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="cd310-200">Cela signifie que les entités générées à partir de ces modèles ont des &lt; Propriétés ICollection T simples &gt; .</span><span class="sxs-lookup"><span data-stu-id="cd310-200">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="cd310-201">Toutefois, lors de la liaison de données à l’aide de WPF, il est préférable d’utiliser **ObservableCollection** pour les propriétés de collection afin que WPF puisse effectuer le suivi des modifications apportées aux collections.</span><span class="sxs-lookup"><span data-stu-id="cd310-201">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="cd310-202">À cette fin, nous allons modifier les modèles pour utiliser ObservableCollection.</span><span class="sxs-lookup"><span data-stu-id="cd310-202">To this end we will to modify the templates to use ObservableCollection.</span></span>

-   <span data-ttu-id="cd310-203">Ouvrir le **Explorateur de solutions** et rechercher le fichier **ProductModel. edmx**</span><span class="sxs-lookup"><span data-stu-id="cd310-203">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="cd310-204">Rechercher le fichier **ProductModel.TT** qui sera imbriqué dans le fichier ProductModel. edmx</span><span class="sxs-lookup"><span data-stu-id="cd310-204">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![Modèle de modèle de produit WPF](~/ef6/media/wpfproductmodeltemplate.png)

-   <span data-ttu-id="cd310-206">Double-cliquez sur le fichier ProductModel.tt pour l’ouvrir dans l’éditeur Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd310-206">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="cd310-207">Recherchez et remplacez les deux occurrences de «**ICollection**» par «**ObservableCollection**».</span><span class="sxs-lookup"><span data-stu-id="cd310-207">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="cd310-208">Celles-ci sont situées approximativement aux lignes 296 et 484.</span><span class="sxs-lookup"><span data-stu-id="cd310-208">These are located approximately at lines 296 and 484.</span></span>
-   <span data-ttu-id="cd310-209">Recherchez et remplacez la première occurrence de «**HashSet**» par «**ObservableCollection**».</span><span class="sxs-lookup"><span data-stu-id="cd310-209">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="cd310-210">Cette occurrence se trouve approximativement à la ligne 50.</span><span class="sxs-lookup"><span data-stu-id="cd310-210">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="cd310-211">**Ne remplacez pas** la deuxième occurrence de HashSet trouvée plus loin dans le code.</span><span class="sxs-lookup"><span data-stu-id="cd310-211">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="cd310-212">Recherchez et remplacez la seule occurrence de «**System. Collections. Generic**» par «**System. Collections. ObjectModel**».</span><span class="sxs-lookup"><span data-stu-id="cd310-212">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="cd310-213">Il se trouve approximativement à la ligne 424.</span><span class="sxs-lookup"><span data-stu-id="cd310-213">This is located approximately at line 424.</span></span>
-   <span data-ttu-id="cd310-214">Enregistrez le fichier ProductModel.tt.</span><span class="sxs-lookup"><span data-stu-id="cd310-214">Save the ProductModel.tt file.</span></span> <span data-ttu-id="cd310-215">Cela devrait entraîner la régénération du code pour les entités.</span><span class="sxs-lookup"><span data-stu-id="cd310-215">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="cd310-216">Si le code ne se régénère pas automatiquement, cliquez avec le bouton droit sur ProductModel.tt et choisissez « exécuter un outil personnalisé ».</span><span class="sxs-lookup"><span data-stu-id="cd310-216">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="cd310-217">Si vous ouvrez maintenant le fichier Category.cs (qui est imbriqué sous ProductModel.tt), vous devez voir que la collection Products a le type \*\*ObservableCollection &lt; Product &gt; \*\*.</span><span class="sxs-lookup"><span data-stu-id="cd310-217">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="cd310-218">Compilez le projet.</span><span class="sxs-lookup"><span data-stu-id="cd310-218">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="cd310-219">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="cd310-219">Lazy Loading</span></span>

<span data-ttu-id="cd310-220">La propriété **Products** de la classe **Category** et la propriété **Category** de la classe **Product** sont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="cd310-220">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="cd310-221">Dans Entity Framework, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="cd310-221">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="cd310-222">EF vous donne la possibilité de charger automatiquement les entités associées à partir de la base de données la première fois que vous accédez à la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="cd310-222">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="cd310-223">Avec ce type de chargement (appelé chargement différé), sachez que la première fois que vous accédez à chaque propriété de navigation, une requête distincte est exécutée sur la base de données si le contenu n’est pas déjà dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="cd310-223">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="cd310-224">Lorsque vous utilisez des types d’entités POCO, EF réalise un chargement différé en créant des instances de types de proxy dérivés pendant l’exécution, puis en substituant les propriétés virtuelles dans vos classes pour ajouter le raccordement de chargement.</span><span class="sxs-lookup"><span data-stu-id="cd310-224">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="cd310-225">Pour bénéficier du chargement différé d’objets connexes, vous devez déclarer les accesseurs get de propriété de navigation comme **public** et **virtuel** (**substituable** dans Visual Basic) et vous ne devez pas être **sealed** (**NotOverridable** dans Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="cd310-225">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="cd310-226">Lors de l’utilisation de Database First propriétés de navigation sont automatiquement configurées pour permettre le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="cd310-226">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="cd310-227">Dans la section Code First, nous avons choisi de rendre les propriétés de navigation virtuelles pour la même raison</span><span class="sxs-lookup"><span data-stu-id="cd310-227">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="cd310-228">Lier un objet à des contrôles</span><span class="sxs-lookup"><span data-stu-id="cd310-228">Bind Object to Controls</span></span>

<span data-ttu-id="cd310-229">Ajoutez les classes définies dans le modèle en tant que sources de données pour cette application WPF.</span><span class="sxs-lookup"><span data-stu-id="cd310-229">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

-   <span data-ttu-id="cd310-230">Double-cliquez sur **MainWindow. Xaml** dans Explorateur de solutions pour ouvrir le formulaire principal</span><span class="sxs-lookup"><span data-stu-id="cd310-230">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
-   <span data-ttu-id="cd310-231">Dans le menu principal, sélectionnez **projet- &gt; Ajouter une nouvelle source de données...**</span><span class="sxs-lookup"><span data-stu-id="cd310-231">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="cd310-232">(dans Visual Studio 2010, vous devez sélectionner **données- &gt; Ajouter une nouvelle source de données...**)</span><span class="sxs-lookup"><span data-stu-id="cd310-232">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="cd310-233">Dans la Typewindow choisir une source de données, sélectionnez **objet** , puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="cd310-233">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="cd310-234">Dans la boîte de dialogue Sélectionner les objets de données, dérouler les **WPFwithEFSample**   deux fois et sélectionner une **catégorie**</span><span class="sxs-lookup"><span data-stu-id="cd310-234">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="cd310-235">*Il n’est pas nécessaire de sélectionner la source de données du **produit** , car nous y accéderons via la propriété du **produit**sur la source de données de **catégorie***</span><span class="sxs-lookup"><span data-stu-id="cd310-235">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![Sélectionner des objets de données](~/ef6/media/selectdataobjects.png)

-   <span data-ttu-id="cd310-237">Cliquez sur **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="cd310-237">Click **Finish.**</span></span>
-   <span data-ttu-id="cd310-238">La fenêtre sources de données s’ouvre en regard de la fenêtre MainWindow. XAML \*si la fenêtre sources de données ne s’affiche pas, sélectionnez **afficher- &gt; autres &gt; sources de données Windows** \* .</span><span class="sxs-lookup"><span data-stu-id="cd310-238">The Data Sources window is opened next to the MainWindow.xaml window *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
-   <span data-ttu-id="cd310-239">Appuyez sur l’icône d’épingle pour que la fenêtre sources de données ne soit pas masquée automatiquement.</span><span class="sxs-lookup"><span data-stu-id="cd310-239">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="cd310-240">Vous devrez peut-être cliquer sur le bouton Actualiser si la fenêtre était déjà visible.</span><span class="sxs-lookup"><span data-stu-id="cd310-240">You may need to hit the refresh button if the window was already visible.</span></span>

    ![Data Sources](~/ef6/media/datasources.png)

-   <span data-ttu-id="cd310-242">Sélectionnez la source de données de **catégorie** et faites-la glisser sur le formulaire.</span><span class="sxs-lookup"><span data-stu-id="cd310-242">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="cd310-243">Voici ce qui s’est produit quand nous avons fait glisser cette source :</span><span class="sxs-lookup"><span data-stu-id="cd310-243">The following happened when we dragged this source:</span></span>

-   <span data-ttu-id="cd310-244">La ressource **categoryViewSource** et le contrôle **categoryDataGrid** ont été ajoutés au code XAML</span><span class="sxs-lookup"><span data-stu-id="cd310-244">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span> 
-   <span data-ttu-id="cd310-245">La propriété DataContext de l’élément Grid parent a été définie sur « {StaticResource **categoryViewSource** } ».</span><span class="sxs-lookup"><span data-stu-id="cd310-245">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span><span data-ttu-id="cd310-246">La ressource **categoryViewSource** sert de source de liaison pour l' \\ élément de grille parent externe.</span><span class="sxs-lookup"><span data-stu-id="cd310-246"> The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="cd310-247">Les éléments de grille interne héritent ensuite de la valeur DataContext de la grille parente (la propriété ItemsSource de categoryDataGrid est définie sur « {Binding} »)</span><span class="sxs-lookup"><span data-stu-id="cd310-247">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

``` xml
    <Window.Resources>
        <CollectionViewSource x:Key="categoryViewSource"
                                d:DesignSource="{d:DesignInstance {x:Type local:Category}, CreateList=True}"/>
    </Window.Resources>
    <Grid DataContext="{StaticResource categoryViewSource}">
        <DataGrid x:Name="categoryDataGrid" AutoGenerateColumns="False" EnableRowVirtualization="True"
                    ItemsSource="{Binding}" Margin="13,13,43,191"
                    RowDetailsVisibilityMode="VisibleWhenSelected">
            <DataGrid.Columns>
                <DataGridTextColumn x:Name="categoryIdColumn" Binding="{Binding CategoryId}"
                                    Header="Category Id" Width="SizeToHeader"/>
                <DataGridTextColumn x:Name="nameColumn" Binding="{Binding Name}"
                                    Header="Name" Width="SizeToHeader"/>
            </DataGrid.Columns>
        </DataGrid>
    </Grid>
```

## <a name="adding-a-details-grid"></a><span data-ttu-id="cd310-248">Ajout d’une grille de détails</span><span class="sxs-lookup"><span data-stu-id="cd310-248">Adding a Details Grid</span></span>

<span data-ttu-id="cd310-249">Maintenant que nous disposons d’une grille pour afficher les catégories, ajoutons une grille de détails pour afficher les produits associés.</span><span class="sxs-lookup"><span data-stu-id="cd310-249">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

-   <span data-ttu-id="cd310-250">Sélectionnez la propriété **Products** dans la source de données de **catégorie** et faites-la glisser sur le formulaire.</span><span class="sxs-lookup"><span data-stu-id="cd310-250">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
    -   <span data-ttu-id="cd310-251">La ressource **categoryProductsViewSource** et la grille **productDataGrid** sont ajoutées au code XAML</span><span class="sxs-lookup"><span data-stu-id="cd310-251">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
    -   <span data-ttu-id="cd310-252">Le chemin de liaison de cette ressource est défini sur Products</span><span class="sxs-lookup"><span data-stu-id="cd310-252">The binding path for this resource is set to Products</span></span>
    -   <span data-ttu-id="cd310-253">L’infrastructure de liaison de données WPF garantit que seuls les produits associés à la catégorie sélectionnée s’affichent dans **productDataGrid**</span><span class="sxs-lookup"><span data-stu-id="cd310-253">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
-   <span data-ttu-id="cd310-254">À partir de la boîte à outils, faites glisser le **bouton** sur le formulaire.</span><span class="sxs-lookup"><span data-stu-id="cd310-254">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="cd310-255">Affectez à la propriété **Name** la valeur **buttonSave** et à la propriété **content** la valeur **Save**.</span><span class="sxs-lookup"><span data-stu-id="cd310-255">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="cd310-256">Le formulaire doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="cd310-256">The form should look similar to this:</span></span>

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="cd310-258">Ajouter du code qui gère l’interaction des données</span><span class="sxs-lookup"><span data-stu-id="cd310-258">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="cd310-259">Il est temps d’ajouter des gestionnaires d’événements à la fenêtre principale.</span><span class="sxs-lookup"><span data-stu-id="cd310-259">It's time to add some event handlers to the main window.</span></span>

-   <span data-ttu-id="cd310-260">Dans la fenêtre XAML, cliquez sur l’élément de la \*\* &lt; fenêtre\*\* , ce qui sélectionne la fenêtre principale.</span><span class="sxs-lookup"><span data-stu-id="cd310-260">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
-   <span data-ttu-id="cd310-261">Dans la fenêtre **Propriétés** , choisissez **événements** en haut à droite, puis double-cliquez sur la zone de texte à droite de l’étiquette **chargée** .</span><span class="sxs-lookup"><span data-stu-id="cd310-261">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![Propriétés de la fenêtre principale](~/ef6/media/mainwindowproperties.png)

-   <span data-ttu-id="cd310-263">Ajoutez également l’événement **Click** pour le bouton **Enregistrer** en double-cliquant sur le bouton enregistrer dans le concepteur.</span><span class="sxs-lookup"><span data-stu-id="cd310-263">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span> 

<span data-ttu-id="cd310-264">Cela vous amène au code-behind pour le formulaire, nous allons maintenant modifier le code pour utiliser le ProductContext pour effectuer l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="cd310-264">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="cd310-265">Mettez à jour le code de MainWindow comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="cd310-265">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="cd310-266">Le code déclare une instance de longue durée de **ProductContext**.</span><span class="sxs-lookup"><span data-stu-id="cd310-266">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="cd310-267">L’objet **ProductContext** est utilisé pour interroger et enregistrer des données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="cd310-267">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="cd310-268">La méthode **dispose ()** sur l’instance **ProductContext** est ensuite appelée à partir de la méthode **OnClosing** substituée.</span><span class="sxs-lookup"><span data-stu-id="cd310-268">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span><span data-ttu-id="cd310-269">Les commentaires de code fournissent des détails sur ce que fait le code.</span><span class="sxs-lookup"><span data-stu-id="cd310-269"> The code comments provide details about what the code does.</span></span>

``` csharp
    using System.Data.Entity;
    using System.Linq;
    using System.Windows;

    namespace WPFwithEFSample
    {
        public partial class MainWindow : Window
        {
            private ProductContext _context = new ProductContext();
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                System.Windows.Data.CollectionViewSource categoryViewSource =
                    ((System.Windows.Data.CollectionViewSource)(this.FindResource("categoryViewSource")));

                // Load is an extension method on IQueryable,
                // defined in the System.Data.Entity namespace.
                // This method enumerates the results of the query,
                // similar to ToList but without creating a list.
                // When used with Linq to Entities this method
                // creates entity objects and adds them to the context.
                _context.Categories.Load();

                // After the data is loaded call the DbSet<T>.Local property
                // to use the DbSet<T> as a binding source.
                categoryViewSource.Source = _context.Categories.Local;
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                // When you delete an object from the related entities collection
                // (in this case Products), the Entity Framework doesn’t mark
                // these child entities as deleted.
                // Instead, it removes the relationship between the parent and the child
                // by setting the parent reference to null.
                // So we manually have to delete the products
                // that have a Category reference set to null.

                // The following code uses LINQ to Objects
                // against the Local collection of Products.
                // The ToList call is required because otherwise the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can use LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                _context.SaveChanges();
                // Refresh the grids so the database generated values show up.
                this.categoryDataGrid.Items.Refresh();
                this.productsDataGrid.Items.Refresh();
            }

            protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }

    }
```

## <a name="test-the-wpf-application"></a><span data-ttu-id="cd310-270">Tester l’application WPF</span><span class="sxs-lookup"><span data-stu-id="cd310-270">Test the WPF Application</span></span>

-   <span data-ttu-id="cd310-271">Compilez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="cd310-271">Compile and run the application.</span></span> <span data-ttu-id="cd310-272">Si vous avez utilisé Code First, vous verrez qu’une base de données **WPFwithEFSample. ProductContext** est créée pour vous.</span><span class="sxs-lookup"><span data-stu-id="cd310-272">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
-   <span data-ttu-id="cd310-273">Entrez un nom de catégorie dans la grille supérieure et les noms de produits dans la grille inférieure *n’entrent rien dans les colonnes d’ID, car la clé primaire est générée par la base de données* .</span><span class="sxs-lookup"><span data-stu-id="cd310-273">Enter a category name in the top grid and product names in the bottom grid *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![Fenêtre principale avec de nouvelles catégories et produits](~/ef6/media/screen1.png)

-   <span data-ttu-id="cd310-275">Appuyez sur le bouton **Enregistrer** pour enregistrer les données dans la base de données</span><span class="sxs-lookup"><span data-stu-id="cd310-275">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="cd310-276">Après l’appel de l' **argument SaveChanges de DbContext ()**, les ID sont remplis avec les valeurs générées par la base de données.</span><span class="sxs-lookup"><span data-stu-id="cd310-276">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="cd310-277">Étant donné que nous avons appelé **Refresh ()** après **SaveChanges ()** , les contrôles **DataGrid** sont également mis à jour avec les nouvelles valeurs.</span><span class="sxs-lookup"><span data-stu-id="cd310-277">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![Fenêtre principale avec des ID remplis](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="cd310-279">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cd310-279">Additional Resources</span></span>

<span data-ttu-id="cd310-280">Pour en savoir plus sur la liaison de données aux collections à l’aide de WPF, consultez [cette rubrique](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) dans la documentation WPF.</span><span class="sxs-lookup"><span data-stu-id="cd310-280">To learn more about data binding to collections using WPF, see [this topic](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
