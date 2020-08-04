---
title: Prise en main de WPF-EF Core
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 958fc579a72a9bf3c97c5551d55777df6c32293f
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535634"
---
# <a name="getting-started-with-wpf"></a><span data-ttu-id="7b5b0-102">Bien démarrer avec WPF</span><span class="sxs-lookup"><span data-stu-id="7b5b0-102">Getting Started with WPF</span></span>

<span data-ttu-id="7b5b0-103">Cette procédure pas à pas montre comment lier des types POCO à des contrôles WPF dans un formulaire « principal-détail ».</span><span class="sxs-lookup"><span data-stu-id="7b5b0-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="7b5b0-104">L’application utilise les API Entity Framework pour remplir les objets avec les données de la base de données, effectuer le suivi des modifications et conserver les données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="7b5b0-105">Le modèle définit deux types qui participent à une relation un-à-plusieurs : **Category** ( \\ principal principal) et **Product** (détail dépendante \\ ).</span><span class="sxs-lookup"><span data-stu-id="7b5b0-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="7b5b0-106">L’infrastructure de liaison de données WPF permet de naviguer entre les objets connexes : la sélection de lignes dans le mode maître entraîne la mise à jour de la vue détaillée avec les données enfants correspondantes.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-106">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="7b5b0-107">Les captures d’écran et les listes de code de cette procédure pas à pas sont extraites de Visual Studio 2019 16.6.5.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-107">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="7b5b0-108">Vous pouvez consulter l’exemple de cet article [sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span><span class="sxs-lookup"><span data-stu-id="7b5b0-108">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="7b5b0-109">Prérequis</span><span class="sxs-lookup"><span data-stu-id="7b5b0-109">Pre-Requisites</span></span>

* <span data-ttu-id="7b5b0-110">Vous devez avoir installé Visual Studio 2019 16,3 ou version ultérieure avec la **charge de travail de bureau .net** sélectionnée pour effectuer cette procédure pas à pas.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-110">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="7b5b0-111">Pour plus d’informations sur l’installation de la dernière version de Visual Studio, consultez [installer Visual Studio](/visualstudio/install/install-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="7b5b0-111">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="7b5b0-112">Création de l’application</span><span class="sxs-lookup"><span data-stu-id="7b5b0-112">Create the Application</span></span>

1. <span data-ttu-id="7b5b0-113">Ouvrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-113">Open Visual Studio</span></span>
2. <span data-ttu-id="7b5b0-114">Dans la fenêtre de démarrage, choisissez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-114">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="7b5b0-115">Recherchez « WPF », choisissez **application WPF (.net Core)** , puis choisissez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-115">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="7b5b0-116">Dans l’écran suivant, donnez un nom au projet, par exemple, **GetStartedWPF**, puis choisissez **créer.**</span><span class="sxs-lookup"><span data-stu-id="7b5b0-116">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="7b5b0-117">Installer les packages NuGet Entity Framework</span><span class="sxs-lookup"><span data-stu-id="7b5b0-117">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="7b5b0-118">Cliquez avec le bouton droit sur la solution et choisissez **gérer les packages NuGet pour la solution...**</span><span class="sxs-lookup"><span data-stu-id="7b5b0-118">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![Gérer les packages NuGet](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="7b5b0-120">Tapez `entityframeworkcore.sqlite` dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-120">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="7b5b0-121">Sélectionnez le package **Microsoft. EntityFrameworkCore. sqlite** .</span><span class="sxs-lookup"><span data-stu-id="7b5b0-121">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="7b5b0-122">Vérifiez le projet dans le volet droit, puis cliquez sur **installer** .</span><span class="sxs-lookup"><span data-stu-id="7b5b0-122">Check the project in the right pane and click **Install**</span></span>

    ![Package SQLite](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="7b5b0-124">Répétez les étapes pour rechercher `entityframeworkcore.proxies` et installer **Microsoft. EntityFrameworkCore. proxies**.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-124">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="7b5b0-125">Lorsque vous avez installé le package SQLite, il a automatiquement retiré le package de base **Microsoft. EntityFrameworkCore** associé.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-125">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="7b5b0-126">Le package **Microsoft. EntityFrameworkCore. proxies** prend en charge les données de « chargement différé ».</span><span class="sxs-lookup"><span data-stu-id="7b5b0-126">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="7b5b0-127">Cela signifie que lorsque vous avez des entités avec des entités enfants, seuls les parents sont extraits lors de la charge initiale.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-127">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="7b5b0-128">Les proxies détectent quand une tentative d’accès aux entités enfants est effectuée et les charge automatiquement à la demande.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-128">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="7b5b0-129">Définir un modèle</span><span class="sxs-lookup"><span data-stu-id="7b5b0-129">Define a Model</span></span>

<span data-ttu-id="7b5b0-130">Dans cette procédure pas à pas, vous allez implémenter un modèle à l’aide de « code First ».</span><span class="sxs-lookup"><span data-stu-id="7b5b0-130">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="7b5b0-131">Cela signifie que EF Core créera les tables et le schéma de base de données en fonction des classes C# que vous définissez.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-131">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="7b5b0-132">Ajoutez une nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-132">Add a new class.</span></span> <span data-ttu-id="7b5b0-133">Donnez-lui le nom : `Product.cs` et renseignez-le comme suit :</span><span class="sxs-lookup"><span data-stu-id="7b5b0-133">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="7b5b0-134">Ensuite, ajoutez une classe nommée `Category.cs` et remplissez-la avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="7b5b0-134">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="7b5b0-135">La propriété **Products** de la classe **Category** et la propriété **Category** de la classe **Product** sont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-135">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="7b5b0-136">Dans Entity Framework, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-136">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="7b5b0-137">En plus de définir des entités, vous devez définir une classe qui dérive de DbContext et expose les &lt; Propriétés de la tente DbSet &gt; .</span><span class="sxs-lookup"><span data-stu-id="7b5b0-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="7b5b0-138">Les propriétés de la tente de DbSet &lt; &gt; permettent au contexte de savoir quels types vous souhaitez inclure dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="7b5b0-139">Une instance du type dérivé DbContext gère les objets d’entité au moment de l’exécution, ce qui comprend le remplissage des objets avec les données d’une base de données, le suivi des modifications et la persistance des données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="7b5b0-140">Ajoutez une nouvelle `ProductContext.cs` classe au projet avec la définition suivante :</span><span class="sxs-lookup"><span data-stu-id="7b5b0-140">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="7b5b0-141">`DbSet`Informe EF Core les entités C# qui doivent être mappées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-141">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="7b5b0-142">Il existe plusieurs façons de configurer le EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="7b5b0-142">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="7b5b0-143">Vous pouvez en savoir plus à ce sujet dans : [configuration d’un DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7b5b0-143">You can read about them in: [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="7b5b0-144">Cet exemple utilise le `OnConfiguring` remplacement pour spécifier un fichier de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-144">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="7b5b0-145">L' `UseLazyLoadingProxies` appel indique à EF Core d’implémenter le chargement différé, de sorte que les entités enfants sont chargées automatiquement lorsqu’elles sont accessibles à partir du parent.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-145">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="7b5b0-146">Appuyez sur **Ctrl + Maj + B** ou accédez à **générer générer la &gt; solution** pour compiler le projet.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-146">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="7b5b0-147">Découvrez les différences qui consistaient à maintenir la synchronisation de votre base de données et de vos modèles de EF Core : la [gestion des schémas de base de données](/ef/core/managing-schemas).</span><span class="sxs-lookup"><span data-stu-id="7b5b0-147">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](/ef/core/managing-schemas).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="7b5b0-148">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="7b5b0-148">Lazy Loading</span></span>

<span data-ttu-id="7b5b0-149">La propriété **Products** de la classe **Category** et la propriété **Category** de la classe **Product** sont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-149">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="7b5b0-150">Dans Entity Framework Core, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-150">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="7b5b0-151">EF Core vous donne la possibilité de charger automatiquement des entités associées à partir de la base de données la première fois que vous accédez à la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-151">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="7b5b0-152">Avec ce type de chargement (appelé chargement différé), sachez que la première fois que vous accédez à chaque propriété de navigation, une requête distincte est exécutée sur la base de données si le contenu n’est pas déjà dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-152">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="7b5b0-153">Lors de l’utilisation des types d’entités POCO (Plain Old C# Object), EF Core réalise un chargement différé en créant des instances de types de proxy dérivés au cours de l’exécution, puis en substituant les propriétés virtuelles dans vos classes pour ajouter le raccordement de chargement.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-153">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="7b5b0-154">Pour bénéficier du chargement différé d’objets connexes, vous devez déclarer les accesseurs get de propriété de navigation comme **public** et **virtuel** (**substituable** dans Visual Basic) et votre classe ne doit pas être **sealed** (**NotOverridable** dans Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="7b5b0-154">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="7b5b0-155">Lorsque vous utilisez Database First, les propriétés de navigation sont automatiquement configurées pour permettre le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-155">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="7b5b0-156">Lier un objet à des contrôles</span><span class="sxs-lookup"><span data-stu-id="7b5b0-156">Bind Object to Controls</span></span>

<span data-ttu-id="7b5b0-157">Ajoutez les classes définies dans le modèle en tant que sources de données pour cette application WPF.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-157">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="7b5b0-158">Double-cliquez sur **MainWindow. Xaml** dans Explorateur de solutions pour ouvrir le formulaire principal</span><span class="sxs-lookup"><span data-stu-id="7b5b0-158">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="7b5b0-159">Choisissez l’onglet **XAML** pour modifier le code XAML.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-159">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="7b5b0-160">Immédiatement après la `Window` balise d’ouverture, ajoutez les sources suivantes pour vous connecter aux entités EF Core.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-160">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="7b5b0-161">Cela configure la source pour les catégories « parent » et la deuxième source pour les produits « Detail ».</span><span class="sxs-lookup"><span data-stu-id="7b5b0-161">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="7b5b0-162">Ensuite, ajoutez le balisage suivant à votre code XAML après la `Window.Resources` balise de fermeture.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-162">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="7b5b0-163">Notez que `CategoryId` a la valeur `ReadOnly` , car elle est affectée par la base de données et ne peut pas être modifiée.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-163">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="7b5b0-164">Ajout d’une grille de détails</span><span class="sxs-lookup"><span data-stu-id="7b5b0-164">Adding a Details Grid</span></span>

<span data-ttu-id="7b5b0-165">Maintenant que la grille existe pour afficher les catégories, la grille détails peut être ajoutée pour afficher les produits.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-165">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="7b5b0-166">Enfin, ajoutez un `Save` bouton et un fil dans l’événement Click à `Button_Click` .</span><span class="sxs-lookup"><span data-stu-id="7b5b0-166">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="7b5b0-167">Votre mode Design doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="7b5b0-167">Your design view should look like this:</span></span>

![Capture d’écran du Concepteur WPF](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="7b5b0-169">Ajouter du code qui gère l’interaction des données</span><span class="sxs-lookup"><span data-stu-id="7b5b0-169">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="7b5b0-170">Il est temps d’ajouter des gestionnaires d’événements à la fenêtre principale.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-170">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="7b5b0-171">Dans la fenêtre XAML, cliquez sur l’élément de \*\* &lt; fenêtre &gt; \*\* pour sélectionner la fenêtre principale.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-171">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="7b5b0-172">Dans la fenêtre **Propriétés** , choisissez **événements** en haut à droite, puis double-cliquez sur la zone de texte à droite de l’étiquette **chargée** .</span><span class="sxs-lookup"><span data-stu-id="7b5b0-172">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![Propriétés de la fenêtre principale](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="7b5b0-174">Cela vous amène au code-behind pour le formulaire, nous allons maintenant modifier le code pour utiliser le `ProductContext` pour effectuer l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-174">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="7b5b0-175">Mettez à jour le code comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-175">Update the code as shown below.</span></span>

<span data-ttu-id="7b5b0-176">Le code déclare une instance de longue durée de `ProductContext` .</span><span class="sxs-lookup"><span data-stu-id="7b5b0-176">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="7b5b0-177">L' `ProductContext` objet est utilisé pour interroger et enregistrer des données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-177">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="7b5b0-178">La `Dispose()` méthode sur l' `ProductContext` instance est ensuite appelée à partir de la `OnClosing` méthode substituée.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-178">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="7b5b0-179">Les commentaires de code expliquent ce que fait chaque étape.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-179"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="7b5b0-180">Le code utilise un appel à `EnsureCreated()` pour générer la base de données lors de la première exécution.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-180">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="7b5b0-181">Cela est acceptable pour les démonstrations, mais dans les applications de production, vous devez examiner les [migrations](/ef/core/managing-schemas/migrations/) pour gérer votre schéma.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-181">This is acceptable for demos, but in production apps you should look at [migrations](/ef/core/managing-schemas/migrations/) to manage your schema.</span></span> <span data-ttu-id="7b5b0-182">Le code s’exécute également de façon synchrone, car il utilise une base de données SQLite locale.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-182">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="7b5b0-183">Pour les scénarios de production qui impliquent généralement un serveur distant, envisagez d’utiliser les versions asynchrones des `Load` `SaveChanges` méthodes et.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-183">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="7b5b0-184">Tester l’application WPF</span><span class="sxs-lookup"><span data-stu-id="7b5b0-184">Test the WPF Application</span></span>

<span data-ttu-id="7b5b0-185">Compilez et exécutez l’application en appuyant sur **F5** ou en choisissant **Déboguer &gt; Démarrer le débogage**.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-185">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="7b5b0-186">La base de données doit être créée automatiquement avec un fichier nommé `products.db` .</span><span class="sxs-lookup"><span data-stu-id="7b5b0-186">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="7b5b0-187">Entrez un nom de catégorie et appuyez sur entrée, puis ajoutez des produits à la grille inférieure.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-187">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="7b5b0-188">Cliquez sur enregistrer et observez l’actualisation de la grille avec les ID fournis par la base de données.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-188">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="7b5b0-189">Mettez en surbrillance une ligne et appuyez sur **supprimer** pour supprimer la ligne.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-189">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="7b5b0-190">L’entité sera supprimée lorsque vous cliquerez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-190">The entity will be deleted when you click **Save**.</span></span>

![Application en cours d’exécution](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="7b5b0-192">Notification de modification de propriété</span><span class="sxs-lookup"><span data-stu-id="7b5b0-192">Property Change Notification</span></span>

<span data-ttu-id="7b5b0-193">Cet exemple s’appuie sur quatre étapes pour synchroniser les entités avec l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-193">This example relies on four steps to synchronize the entities with the UI.</span></span> 

1. <span data-ttu-id="7b5b0-194">L’appel initial `_context.Categories.Load()` charge les données de catégories.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-194">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="7b5b0-195">Les proxys de chargement différé chargent les données des produits dépendants.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-195">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="7b5b0-196">Le suivi des modifications intégré de EF Core apporte les modifications nécessaires aux entités, y compris les insertions et les suppressions, lorsque `_context.SaveChanges()` est appelé.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-196">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="7b5b0-197">Appelle pour `DataGridView.Items.Refresh()` forcer un rechargement avec les ID nouvellement générés.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-197">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="7b5b0-198">Cela fonctionne pour notre exemple de prise en main, mais vous pouvez avoir besoin de code supplémentaire pour d’autres scénarios.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-198">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="7b5b0-199">Les contrôles WPF affichent l’interface utilisateur en lisant les champs et les propriétés sur vos entités.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-199">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="7b5b0-200">Lorsque vous modifiez une valeur dans l’interface utilisateur (IU), cette valeur est transmise à votre entité.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-200">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="7b5b0-201">Lorsque vous modifiez la valeur d’une propriété directement sur votre entité, telle que son chargement à partir de la base de données, WPF ne reflète pas immédiatement les modifications apportées à l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-201">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="7b5b0-202">Le moteur de rendu doit être averti des modifications.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-202">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="7b5b0-203">Le projet a fait cela en appelant manuellement `Refresh()` .</span><span class="sxs-lookup"><span data-stu-id="7b5b0-203">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="7b5b0-204">Une façon simple d’automatiser cette notification consiste à implémenter l’interface [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) .</span><span class="sxs-lookup"><span data-stu-id="7b5b0-204">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="7b5b0-205">Les composants WPF détectent automatiquement l’interface et s’inscrivent pour les événements de modification.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-205">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="7b5b0-206">L’entité est chargée de déclencher ces événements.</span><span class="sxs-lookup"><span data-stu-id="7b5b0-206">The entity is responsible for raising these events.</span></span> 

> [!TIP]
> <span data-ttu-id="7b5b0-207">Pour en savoir plus sur la gestion des modifications, consultez [comment implémenter la notification de modification de propriété](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span><span class="sxs-lookup"><span data-stu-id="7b5b0-207">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="7b5b0-208">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="7b5b0-208">Next Steps</span></span>

<span data-ttu-id="7b5b0-209">En savoir plus sur [la configuration d’un DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7b5b0-209">Learn more about [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>