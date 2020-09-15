---
title: Liaison de liaison avec WPF-EF6
description: Liaison de liaison avec WPF dans Entity Framework 6
author: divega
ms.date: 05/19/2020
uid: ef6/fundamentals/databinding/wpf
ms.openlocfilehash: 85aea308d89241f1da9633ef60f4bc8c3997561d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072978"
---
# <a name="databinding-with-wpf"></a>Liaison de données avec WPF

> [!IMPORTANT]
> **Ce document est valide pour WPF sur la .NET Framework uniquement**
>
> Ce document décrit la liaison de liaison pour WPF sur le .NET Framework. Pour les nouveaux projets .NET Core, nous vous recommandons d’utiliser [EF Core](xref:core/index) au lieu de Entity Framework 6. La documentation relative à la liaison de la liaison de EF Core est ici : [prise en main avec WPF](xref:core/get-started/wpf).

Cette procédure pas à pas montre comment lier des types POCO à des contrôles WPF dans un formulaire « maître/détail ». L’application utilise les API Entity Framework pour remplir les objets avec les données de la base de données, effectuer le suivi des modifications et conserver les données dans la base de données.

Le modèle définit deux types qui participent à une relation un-à-plusieurs : **Category** ( \\ principal principal) et **Product** (détail dépendante \\ ). Les outils Visual Studio sont ensuite utilisés pour lier les types définis dans le modèle aux contrôles WPF. L’infrastructure de liaison de données WPF permet de naviguer entre les objets connexes : la sélection de lignes dans le mode maître entraîne la mise à jour de la vue détaillée avec les données enfants correspondantes.

Les captures d’écran et les listes de codes de cette procédure pas à pas sont extraites de Visual Studio 2013 mais vous pouvez effectuer cette procédure pas à pas avec Visual Studio 2012 ou Visual Studio 2010.

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>Utiliser l’option « Object » pour créer des sources de données WPF

Avec la version précédente de Entity Framework nous avons utilisé pour recommander l’utilisation de l’option **de base de données** lors de la création d’une source de données basée sur un modèle créé à l’aide du concepteur EF. Cela était dû au fait que le concepteur générait un contexte dérivé d’ObjectContext et des classes d’entité dérivées de EntityObject. L’utilisation de l’option de base de données vous aidera à écrire le meilleur code pour interagir avec cette surface d’API.

Les concepteurs EF pour Visual Studio 2012 et Visual Studio 2013 générer un contexte qui dérive de DbContext avec des classes d’entité POCO simples. Avec Visual Studio 2010, nous vous recommandons de passer à un modèle de génération de code qui utilise DbContext, comme décrit plus loin dans cette procédure pas à pas.

Lorsque vous utilisez la surface de l’API DbContext, vous devez utiliser l’option d' **objet** lors de la création d’une source de données, comme indiqué dans cette procédure pas à pas.

Si nécessaire, vous pouvez [revenir à la génération de code basé sur ObjectContext](xref:ef6/modeling/designer/codegen/legacy-objectcontext) pour les modèles créés à l’aide du concepteur EF.

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez installer Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010.

Si vous utilisez Visual Studio 2010, vous devez également installer NuGet. Pour plus d’informations, consultez [installation de NuGet](/nuget/install-nuget-client-tools).  

## <a name="create-the-application"></a>Création de l’application

- Ouvrir Visual Studio
- **Fichier- &gt; nouveau- &gt; projet....**
- Sélectionnez **fenêtres**   dans le volet gauche et **WPFApplication** dans le volet droit
- Entrez **WPFwithEFSample**   comme nom
- Sélectionnez **OK**

## <a name="install-the-entity-framework-nuget-package"></a>Installer le package NuGet Entity Framework

- Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet **WinFormswithEFSample**
- Sélectionnez **gérer les packages NuGet...**
- Dans la boîte de dialogue gérer les packages NuGet, sélectionnez l’onglet **en ligne** et choisissez le package **EntityFramework** .
- Cliquez sur **Install**.  
    >[!NOTE]
    > En plus de l’assembly EntityFramework, une référence à System. ComponentModel. DataAnnotations est également ajoutée. Si le projet a une référence à System. Data. Entity, il sera supprimé lors de l’installation du package EntityFramework. L’assembly System. Data. Entity n’est plus utilisé pour les applications Entity Framework 6.

## <a name="define-a-model"></a>Définir un modèle

Dans cette procédure pas à pas, vous pouvez choisir d’implémenter un modèle à l’aide de Code First ou du concepteur EF. Suivez l’une des deux sections suivantes.

### <a name="option-1-define-a-model-using-code-first"></a>Option 1 : définir un modèle à l’aide de Code First

Cette section montre comment créer un modèle et la base de données qui lui est associée à l’aide de Code First. Passez à la section suivante (**option 2 : définir un modèle à l’aide de Database First)** si vous préférez utiliser Database First pour rétroconcevoir votre modèle à partir d’une base de données à l’aide du concepteur EF

Lors de l’utilisation de Code First développement, vous commencez généralement par écrire des classes .NET Framework qui définissent votre modèle conceptuel (domaine).

- Ajoutez une nouvelle classe à **WPFwithEFSample :**
  - Cliquez avec le bouton droit sur le nom du projet
  - Sélectionnez **Ajouter**, puis **nouvel élément** .
  - Sélectionner une **classe** et entrer le **produit**   pour le nom de la classe
- Remplacez la **Product**   définition de la classe Product par le code suivant :

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

- Add a **Category** class with the following definition:

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

La propriété **Products** de la classe **Category** et la propriété **Category** de la classe **Product** sont des propriétés de navigation. Dans Entity Framework, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités.

En plus de définir des entités, vous devez définir une classe qui dérive de DbContext et expose les &lt; Propriétés de la tente DbSet &gt; . Les propriétés de la tente de DbSet &lt; &gt; permettent au contexte de savoir quels types vous souhaitez inclure dans le modèle.

Une instance du type dérivé DbContext gère les objets d’entité au moment de l’exécution, ce qui comprend le remplissage des objets avec les données d’une base de données, le suivi des modifications et la persistance des données dans la base de données.

- Ajoutez une nouvelle classe **ProductContext** au projet avec la définition suivante :

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

Compilez le projet.

### <a name="option-2-define-a-model-using-database-first"></a>Option 2 : définir un modèle à l’aide de Database First

Cette section montre comment utiliser Database First pour rétroconcevoir votre modèle à partir d’une base de données à l’aide du concepteur EF. Si vous avez terminé la section précédente (**option 1 : définir un modèle à l’aide de code First)**, ignorez cette section et passez directement à la section **chargement différé** .

#### <a name="create-an-existing-database"></a>Créer une base de données existante

En général, lorsque vous ciblez une base de données existante, elle est déjà créée, mais pour cette procédure pas à pas, nous devons créer une base de données à laquelle accéder.

Le serveur de base de données installé avec Visual Studio diffère selon la version de Visual Studio que vous avez installée :

- Si vous utilisez Visual Studio 2010, vous allez créer une base de données SQL Express.
- Si vous utilisez Visual Studio 2012, vous allez créer une base de [données de base](https://msdn.microsoft.com/library/hh510202.aspx) de données locale.

Commençons par générer la base de données.

- **Vue- &gt; Explorateur de serveurs**
- Cliquez avec le bouton droit sur **connexions de données- &gt; Ajouter une connexion...**
- Si vous n’êtes pas connecté à une base de données à partir de Explorateur de serveurs avant de devoir sélectionner Microsoft SQL Server comme source de données

    ![Changer la source de données](~/ef6/media/changedatasource.png)

- Connectez-vous à la base de données locale ou SQL Express, en fonction de celle que vous avez installée, puis entrez **Products** comme nom de la base de données.

    ![Ajouter une base de données locale de connexion](~/ef6/media/addconnectionlocaldb.png)

    ![Ajouter une connexion Express](~/ef6/media/addconnectionexpress.png)

- Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .

    ![Créer une base de données](~/ef6/media/createdatabase.png)

- La nouvelle base de données s’affiche alors dans Explorateur de serveurs, cliquez dessus avec le bouton droit et sélectionnez **nouvelle requête** .
- Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .

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

#### <a name="reverse-engineer-model"></a>Modèle d’ingénierie à rebours

Nous allons utiliser Entity Framework Designer, inclus dans le cadre de Visual Studio, pour créer notre modèle.

- **Projet- &gt; Ajouter un nouvel élément...**
- Sélectionnez **données** dans le menu de gauche, puis **ADO.NET Entity Data Model**
- Entrez **ProductModel** comme nom et cliquez sur **OK** .
- Cela lance l' **assistant Entity Data Model**
- Sélectionnez **générer à partir de la base de données** , puis cliquez sur **suivant** .

    ![Choisir le contenu du modèle](~/ef6/media/choosemodelcontents.png)

- Sélectionnez la connexion à la base de données que vous avez créée dans la première section, entrez **ProductContext** comme nom de la chaîne de connexion, puis cliquez sur **suivant** .

    ![Choisir votre connexion](~/ef6/media/chooseyourconnection.png)

- Cochez la case en regard de « tables » pour importer toutes les tables, puis cliquez sur « Terminer ».

    ![Choisir vos objets](~/ef6/media/chooseyourobjects.png)

Une fois le processus d’ingénierie à rebours terminé, le nouveau modèle est ajouté à votre projet et vous est ouvert pour que vous l’affichez dans le Entity Framework Designer. Un fichier de App.config a également été ajouté à votre projet avec les détails de connexion de la base de données.

#### <a name="additional-steps-in-visual-studio-2010"></a>Étapes supplémentaires dans Visual Studio 2010

Si vous travaillez dans Visual Studio 2010, vous devrez mettre à jour le concepteur EF pour utiliser la génération de code EF6.

- Cliquez avec le bouton droit sur une zone vide de votre modèle dans le concepteur EF, puis sélectionnez **Ajouter un élément de génération de code...**
- Sélectionnez **modèles en ligne** dans le menu de gauche et recherchez **DbContext**
- Sélectionnez le **Générateur de DbContext EF 6. x pour C \# ,** entrez **ProductsModel** comme nom et cliquez sur Ajouter.

#### <a name="updating-code-generation-for-data-binding"></a>Mise à jour de la génération de code pour la liaison de données

EF génère du code à partir de votre modèle à l’aide de modèles T4. Les modèles fournis avec Visual Studio ou téléchargés à partir de la Galerie Visual Studio sont destinés à un usage général. Cela signifie que les entités générées à partir de ces modèles ont des &lt; Propriétés ICollection T simples &gt; . Toutefois, lors de la liaison de données à l’aide de WPF, il est préférable d’utiliser **ObservableCollection** pour les propriétés de collection afin que WPF puisse effectuer le suivi des modifications apportées aux collections. À cette fin, nous allons modifier les modèles pour utiliser ObservableCollection.

- Ouvrir le **Explorateur de solutions** et rechercher le fichier **ProductModel. edmx**
- Rechercher le fichier **ProductModel.TT** qui sera imbriqué dans le fichier ProductModel. edmx

    ![Modèle de modèle de produit WPF](~/ef6/media/wpfproductmodeltemplate.png)

- Double-cliquez sur le fichier ProductModel.tt pour l’ouvrir dans l’éditeur Visual Studio.
- Recherchez et remplacez les deux occurrences de «**ICollection**» par «**ObservableCollection**». Celles-ci sont situées approximativement aux lignes 296 et 484.
- Recherchez et remplacez la première occurrence de «**HashSet**» par «**ObservableCollection**». Cette occurrence se trouve approximativement à la ligne 50. **Ne remplacez pas** la deuxième occurrence de HashSet trouvée plus loin dans le code.
- Recherchez et remplacez la seule occurrence de «**System. Collections. Generic**» par «**System. Collections. ObjectModel**». Il se trouve approximativement à la ligne 424.
- Enregistrez le fichier ProductModel.tt. Cela devrait entraîner la régénération du code pour les entités. Si le code ne se régénère pas automatiquement, cliquez avec le bouton droit sur ProductModel.tt et choisissez « exécuter un outil personnalisé ».

Si vous ouvrez maintenant le fichier Category.cs (qui est imbriqué sous ProductModel.tt), vous devez voir que la collection Products a le type **ObservableCollection &lt; Product &gt; **.

Compilez le projet.

## <a name="lazy-loading"></a>Chargement différé

La propriété **Products** de la classe **Category** et la propriété **Category** de la classe **Product** sont des propriétés de navigation. Dans Entity Framework, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités.

EF vous donne la possibilité de charger automatiquement les entités associées à partir de la base de données la première fois que vous accédez à la propriété de navigation. Avec ce type de chargement (appelé chargement différé), sachez que la première fois que vous accédez à chaque propriété de navigation, une requête distincte est exécutée sur la base de données si le contenu n’est pas déjà dans le contexte.

Lorsque vous utilisez des types d’entités POCO, EF réalise un chargement différé en créant des instances de types de proxy dérivés pendant l’exécution, puis en substituant les propriétés virtuelles dans vos classes pour ajouter le raccordement de chargement. Pour bénéficier du chargement différé d’objets connexes, vous devez déclarer les accesseurs get de propriété de navigation comme **public** et **virtuel** (**substituable** dans Visual Basic) et vous ne devez pas être **sealed** (**NotOverridable** dans Visual Basic). Lors de l’utilisation de Database First propriétés de navigation sont automatiquement configurées pour permettre le chargement différé. Dans la section Code First, nous avons choisi de rendre les propriétés de navigation virtuelles pour la même raison

## <a name="bind-object-to-controls"></a>Lier un objet à des contrôles

Ajoutez les classes définies dans le modèle en tant que sources de données pour cette application WPF.

- Double-cliquez sur **MainWindow. Xaml** dans Explorateur de solutions pour ouvrir le formulaire principal
- Dans le menu principal, sélectionnez **projet- &gt; Ajouter une nouvelle source de données...**
    (dans Visual Studio 2010, vous devez sélectionner **données- &gt; Ajouter une nouvelle source de données...**)
- Dans la Typewindow choisir une source de données, sélectionnez **objet** , puis cliquez sur **suivant** .
- Dans la boîte de dialogue Sélectionner les objets de données, dérouler les **WPFwithEFSample**   deux fois et sélectionner une **catégorie**  
    *Il n’est pas nécessaire de sélectionner la source de données du **produit** , car nous y accéderons via la propriété du **produit**sur la source de données de **catégorie***  

    ![Sélectionner des objets de données](~/ef6/media/selectdataobjects.png)

- Cliquez sur **Terminer.**
- La fenêtre sources de données s’ouvre en regard de la fenêtre MainWindow. XAML *si la fenêtre sources de données ne s’affiche pas, sélectionnez **afficher- &gt; autres &gt; sources de données Windows** * .
- Appuyez sur l’icône d’épingle pour que la fenêtre sources de données ne soit pas masquée automatiquement. Vous devrez peut-être cliquer sur le bouton Actualiser si la fenêtre était déjà visible.

    ![Sources de données](~/ef6/media/datasources.png)

- Sélectionnez la source de données de **catégorie** et faites-la glisser sur le formulaire.

Voici ce qui s’est produit quand nous avons fait glisser cette source :

- La ressource **categoryViewSource** et le contrôle **categoryDataGrid** ont été ajoutés au code XAML
- La propriété DataContext de l’élément Grid parent a été définie sur « {StaticResource **categoryViewSource** } ».La ressource **categoryViewSource** sert de source de liaison pour l' \\ élément de grille parent externe. Les éléments de grille interne héritent ensuite de la valeur DataContext de la grille parente (la propriété ItemsSource de categoryDataGrid est définie sur « {Binding} »)

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

## <a name="adding-a-details-grid"></a>Ajout d’une grille de détails

Maintenant que nous disposons d’une grille pour afficher les catégories, ajoutons une grille de détails pour afficher les produits associés.

- Sélectionnez la propriété **Products** dans la source de données de **catégorie** et faites-la glisser sur le formulaire.
  - La ressource **categoryProductsViewSource** et la grille **productDataGrid** sont ajoutées au code XAML
  - Le chemin de liaison de cette ressource est défini sur Products
  - L’infrastructure de liaison de données WPF garantit que seuls les produits associés à la catégorie sélectionnée s’affichent dans **productDataGrid**
- À partir de la boîte à outils, faites glisser le **bouton** sur le formulaire. Affectez à la propriété **Name** la valeur **buttonSave** et à la propriété **content** la valeur **Save**.

Le formulaire doit ressembler à ceci :

![Formulaire du concepteur](~/ef6/media/designer.png)

## <a name="add-code-that-handles-data-interaction"></a>Ajouter du code qui gère l’interaction des données

Il est temps d’ajouter des gestionnaires d’événements à la fenêtre principale.

- Dans la fenêtre XAML, cliquez sur l’élément de la ** &lt; fenêtre** , ce qui sélectionne la fenêtre principale.
- Dans la fenêtre **Propriétés** , choisissez **événements** en haut à droite, puis double-cliquez sur la zone de texte à droite de l’étiquette **chargée** .

    ![Propriétés de la fenêtre principale](~/ef6/media/mainwindowproperties.png)

- Ajoutez également l’événement **Click** pour le bouton **Enregistrer** en double-cliquant sur le bouton enregistrer dans le concepteur.

Cela vous amène au code-behind pour le formulaire, nous allons maintenant modifier le code pour utiliser le ProductContext pour effectuer l’accès aux données. Mettez à jour le code de MainWindow comme indiqué ci-dessous.

Le code déclare une instance de longue durée de **ProductContext**. L’objet **ProductContext** est utilisé pour interroger et enregistrer des données dans la base de données. La méthode **dispose ()** sur l’instance **ProductContext** est ensuite appelée à partir de la méthode **OnClosing** substituée.Les commentaires de code fournissent des détails sur ce que fait le code.

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

## <a name="test-the-wpf-application"></a>Tester l’application WPF

- Compilez et exécutez l'application. Si vous avez utilisé Code First, vous verrez qu’une base de données **WPFwithEFSample. ProductContext** est créée pour vous.
- Entrez un nom de catégorie dans la grille supérieure et les noms de produits dans la grille inférieure *n’entrent rien dans les colonnes d’ID, car la clé primaire est générée par la base de données* .

    ![Fenêtre principale avec de nouvelles catégories et produits](~/ef6/media/screen1.png)

- Appuyez sur le bouton **Enregistrer** pour enregistrer les données dans la base de données

Après l’appel de l' **argument SaveChanges de DbContext ()**, les ID sont remplis avec les valeurs générées par la base de données. Étant donné que nous avons appelé **Refresh ()** après **SaveChanges ()** , les contrôles **DataGrid** sont également mis à jour avec les nouvelles valeurs.

![Fenêtre principale avec des ID remplis](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour en savoir plus sur la liaison de données aux collections à l’aide de WPF, consultez [cette rubrique](/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) dans la documentation WPF.  
