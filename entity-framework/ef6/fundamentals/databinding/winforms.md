---
title: Liaison de liaison avec WinForms-EF6
description: Liaison de liaison avec WinForms dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
uid: ef6/fundamentals/databinding/winforms
ms.openlocfilehash: 934ea2ae488a7df128746a0607037d85e9b23771
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616340"
---
# <a name="databinding-with-winforms"></a>Liaison de données avec WinForms
Cette procédure pas à pas montre comment lier des types POCO à des contrôles Windows Forms (WinForms) dans un formulaire maître/détail. L’application utilise Entity Framework pour remplir les objets avec les données de la base de données, effectuer le suivi des modifications et conserver les données dans la base de données.

Le modèle définit deux types qui participent à une relation un-à-plusieurs : Category ( \\ principal principal) et Product (détail dépendante \\ ). Les outils Visual Studio sont ensuite utilisés pour lier les types définis dans le modèle aux contrôles WinForms. L’infrastructure de liaison de données WinForms active la navigation entre les objets connexes : la sélection de lignes dans le mode maître entraîne la mise à jour de la vue détaillée avec les données enfants correspondantes.

Les captures d’écran et les listes de codes de cette procédure pas à pas sont extraites de Visual Studio 2013 mais vous pouvez effectuer cette procédure pas à pas avec Visual Studio 2012 ou Visual Studio 2010.

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez installer Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010.

Si vous utilisez Visual Studio 2010, vous devez également installer NuGet. Pour plus d’informations, consultez [installation de NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

## <a name="create-the-application"></a>Création de l’application

-   Ouvrez Visual Studio.
-   **Fichier- &gt; nouveau- &gt; projet....**
-   Sélectionnez **fenêtres** dans le volet gauche et **Windows FormsApplication** dans le volet droit
-   Entrez **WinFormswithEFSample** comme nom
-   Sélectionnez **OK**.

## <a name="install-the-entity-framework-nuget-package"></a>Installer le package NuGet Entity Framework

-   Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet **WinFormswithEFSample**
-   Sélectionnez **gérer les packages NuGet...**
-   Dans la boîte de dialogue gérer les packages NuGet, sélectionnez l’onglet **en ligne** et choisissez le package **EntityFramework** .
-   Cliquez sur **Install**.  
    > [!NOTE]
    > En plus de l’assembly EntityFramework, une référence à System. ComponentModel. DataAnnotations est également ajoutée. Si le projet a une référence à System. Data. Entity, il sera supprimé lors de l’installation du package EntityFramework. L’assembly System. Data. Entity n’est plus utilisé pour les applications Entity Framework 6.

## <a name="implementing-ilistsource-for-collections"></a>Implémentation de IListSource pour les collections

Les propriétés de collection doivent implémenter l’interface IListSource pour activer la liaison de données bidirectionnelle avec le tri lors de l’utilisation de Windows Forms. Pour ce faire, nous allons étendre ObservableCollection pour ajouter la fonctionnalité IListSource.

-   Ajoutez une classe **ObservableListSource** au projet :
    -   Cliquez avec le bouton droit sur le nom du projet
    -   Sélectionner **Ajouter un &gt; nouvel élément**
    -   Sélectionnez **classe** et entrez **ObservableListSource** pour le nom de la classe
-   Remplacez le code généré par défaut par le code suivant :

*Cette classe active la liaison de données bidirectionnelle et le tri. La classe dérive de ObservableCollection &lt; T &gt; et ajoute une implémentation explicite de IListSource. La méthode GetList () de IListSource est implémentée pour retourner une implémentation de IBindingList qui reste synchronisée avec ObservableCollection. L’implémentation IBindingList générée par ToBindingList prend en charge le tri. La méthode d’extension ToBindingList est définie dans l’assembly EntityFramework.*

``` csharp
    using System.Collections;
    using System.Collections.Generic;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    using System.Diagnostics.CodeAnalysis;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public class ObservableListSource<T> : ObservableCollection<T>, IListSource
            where T : class
        {
            private IBindingList _bindingList;

            bool IListSource.ContainsListCollection { get { return false; } }

            IList IListSource.GetList()
            {
                return _bindingList ?? (_bindingList = this.ToBindingList());
            }
        }
    }
```

## <a name="define-a-model"></a>Définir un modèle

Dans cette procédure pas à pas, vous pouvez choisir d’implémenter un modèle à l’aide de Code First ou du concepteur EF. Suivez l’une des deux sections suivantes.

### <a name="option-1-define-a-model-using-code-first"></a>Option 1 : définir un modèle à l’aide de Code First

Cette section montre comment créer un modèle et la base de données qui lui est associée à l’aide de Code First. Passez à la section suivante (**option 2 : définir un modèle à l’aide de Database First)** si vous préférez utiliser Database First pour rétroconcevoir votre modèle à partir d’une base de données à l’aide du concepteur EF

Lors de l’utilisation de Code First développement, vous commencez généralement par écrire des classes .NET Framework qui définissent votre modèle conceptuel (domaine).

-   Ajouter une nouvelle classe de **produit** au projet
-   Remplacez le code généré par défaut par le code suivant :

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }
```

-   Ajoutez une classe **Category** au projet.
-   Remplacez le code généré par défaut par le code suivant :

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Category
        {
            private readonly ObservableListSource<Product> _products =
                    new ObservableListSource<Product>();

            public int CategoryId { get; set; }
            public string Name { get; set; }
            public virtual ObservableListSource<Product> Products { get { return _products; } }
        }
    }
```

En plus de définir des entités, vous devez définir une classe qui dérive de **DbContext** et expose les propriétés de la ** &lt; &gt; tente DbSet** . Les propriétés **DbSet** permettent au contexte de savoir quels types vous souhaitez inclure dans le modèle. Les types **DbContext** et **DbSet** sont définis dans l’assembly EntityFramework.

Une instance du type dérivé DbContext gère les objets d’entité au moment de l’exécution, ce qui comprend le remplissage des objets avec les données d’une base de données, le suivi des modifications et la persistance des données dans la base de données.

-   Ajoutez une nouvelle classe **ProductContext** au projet.
-   Remplacez le code généré par défaut par le code suivant :

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;
    using System.Text;

    namespace WinFormswithEFSample
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

-   Si vous utilisez Visual Studio 2010, vous allez créer une base de données SQL Express.
-   Si vous utilisez Visual Studio 2012, vous allez créer une base de [données de base](https://msdn.microsoft.com/library/hh510202.aspx) de données locale.

Commençons par générer la base de données.

-   **Vue- &gt; Explorateur de serveurs**
-   Cliquez avec le bouton droit sur **connexions de données- &gt; Ajouter une connexion...**
-   Si vous n’êtes pas connecté à une base de données à partir de Explorateur de serveurs avant de devoir sélectionner Microsoft SQL Server comme source de données

    ![Changer la source de données](~/ef6/media/changedatasource.png)

-   Connectez-vous à la base de données locale ou SQL Express, en fonction de celle que vous avez installée, puis entrez **Products** comme nom de la base de données.

    ![Ajouter une base de données locale de connexion](~/ef6/media/addconnectionlocaldb.png)

    ![Ajouter une connexion Express](~/ef6/media/addconnectionexpress.png)

-   Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .

    ![Créer une base de données](~/ef6/media/createdatabase.png)

-   La nouvelle base de données s’affiche alors dans Explorateur de serveurs, cliquez dessus avec le bouton droit et sélectionnez **nouvelle requête** .
-   Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .

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

-   **Projet- &gt; Ajouter un nouvel élément...**
-   Sélectionnez **données** dans le menu de gauche, puis **ADO.NET Entity Data Model**
-   Entrez **ProductModel** comme nom et cliquez sur **OK** .
-   Cela lance l' **assistant Entity Data Model**
-   Sélectionnez **générer à partir de la base de données** , puis cliquez sur **suivant** .

    ![Choisir le contenu du modèle](~/ef6/media/choosemodelcontents.png)

-   Sélectionnez la connexion à la base de données que vous avez créée dans la première section, entrez **ProductContext** comme nom de la chaîne de connexion, puis cliquez sur **suivant** .

    ![Choisir votre connexion](~/ef6/media/chooseyourconnection.png)

-   Cochez la case en regard de « tables » pour importer toutes les tables, puis cliquez sur « Terminer ».

    ![Choisir vos objets](~/ef6/media/chooseyourobjects.png)

Une fois le processus d’ingénierie à rebours terminé, le nouveau modèle est ajouté à votre projet et vous est ouvert pour que vous l’affichez dans le Entity Framework Designer. Un fichier de App.config a également été ajouté à votre projet avec les détails de connexion de la base de données.

#### <a name="additional-steps-in-visual-studio-2010"></a>Étapes supplémentaires dans Visual Studio 2010

Si vous travaillez dans Visual Studio 2010, vous devrez mettre à jour le concepteur EF pour utiliser la génération de code EF6.

-   Cliquez avec le bouton droit sur une zone vide de votre modèle dans le concepteur EF, puis sélectionnez **Ajouter un élément de génération de code...**
-   Sélectionnez **modèles en ligne** dans le menu de gauche et recherchez **DbContext**
-   Sélectionnez le **Générateur de DbContext EF 6. x pour C \# ,** entrez **ProductsModel** comme nom et cliquez sur Ajouter.

#### <a name="updating-code-generation-for-data-binding"></a>Mise à jour de la génération de code pour la liaison de données

EF génère du code à partir de votre modèle à l’aide de modèles T4. Les modèles fournis avec Visual Studio ou téléchargés à partir de la Galerie Visual Studio sont destinés à un usage général. Cela signifie que les entités générées à partir de ces modèles ont des &lt; Propriétés ICollection T simples &gt; . Toutefois, lors de la liaison de données, il est souhaitable d’avoir des propriétés de collection qui implémentent IListSource. C’est la raison pour laquelle nous avons créé la classe ObservableListSource ci-dessus et nous allons maintenant modifier les modèles pour utiliser cette classe.

-   Ouvrir le **Explorateur de solutions** et rechercher le fichier **ProductModel. edmx**
-   Rechercher le fichier **ProductModel.TT** qui sera imbriqué dans le fichier ProductModel. edmx

    ![Modèle de modèle de produit](~/ef6/media/productmodeltemplate.png)

-   Double-cliquez sur le fichier ProductModel.tt pour l’ouvrir dans l’éditeur Visual Studio.
-   Recherchez et remplacez les deux occurrences de «**ICollection**» par «**ObservableListSource**». Celles-ci se trouvent sur des lignes d’environ 296 et 484.
-   Recherchez et remplacez la première occurrence de «**HashSet**» par «**ObservableListSource**». Cette occurrence se trouve à environ la ligne 50. **Ne remplacez pas** la deuxième occurrence de HashSet trouvée plus loin dans le code.
-   Enregistrez le fichier ProductModel.tt. Cela devrait entraîner la régénération du code pour les entités. Si le code ne se régénère pas automatiquement, cliquez avec le bouton droit sur ProductModel.tt et choisissez « exécuter un outil personnalisé ».

Si vous ouvrez maintenant le fichier Category.cs (qui est imbriqué sous ProductModel.tt), vous devez voir que la collection Products a le type **ObservableListSource &lt; Product &gt; **.

Compilez le projet.

## <a name="lazy-loading"></a>Chargement différé

La propriété **Products** de la classe **Category** et la propriété **Category** de la classe **Product** sont des propriétés de navigation. Dans Entity Framework, les propriétés de navigation offrent un moyen de naviguer dans une relation entre deux types d’entités.

EF vous donne la possibilité de charger automatiquement les entités associées à partir de la base de données la première fois que vous accédez à la propriété de navigation. Avec ce type de chargement (appelé chargement différé), sachez que la première fois que vous accédez à chaque propriété de navigation, une requête distincte est exécutée sur la base de données si le contenu n’est pas déjà dans le contexte.

Lorsque vous utilisez des types d’entités POCO, EF réalise un chargement différé en créant des instances de types de proxy dérivés pendant l’exécution, puis en substituant les propriétés virtuelles dans vos classes pour ajouter le raccordement de chargement. Pour bénéficier du chargement différé d’objets connexes, vous devez déclarer les accesseurs get de propriété de navigation comme **public** et **virtuel** (**substituable** dans Visual Basic) et vous ne devez pas être **sealed** (**NotOverridable** dans Visual Basic). Lors de l’utilisation de Database First propriétés de navigation sont automatiquement configurées pour permettre le chargement différé. Dans la section Code First, nous avons choisi de rendre les propriétés de navigation virtuelles pour la même raison

## <a name="bind-object-to-controls"></a>Lier un objet à des contrôles

Ajoutez les classes définies dans le modèle en tant que sources de données pour cette application WinForms.

-   Dans le menu principal, sélectionnez **projet- &gt; Ajouter une nouvelle source de données...**
    (dans Visual Studio 2010, vous devez sélectionner **données- &gt; Ajouter une nouvelle source de données...**)
-   Dans la fenêtre choisir un type de source de données, sélectionnez **objet** , puis cliquez sur **suivant** .
-   Dans la boîte de dialogue Sélectionner les objets de données, dérouler les **WinFormswithEFSample** deux fois et sélectionner une **catégorie** il n’est pas nécessaire de sélectionner la source de données du produit, car nous y accéderons via la propriété du produit sur la source de données de catégorie.

    ![source de données](~/ef6/media/datasource.png)

-   Cliquez sur **Terminer.**
    Si la fenêtre sources de données ne s’affiche pas, sélectionnez **afficher- &gt; autres &gt; sources de données Windows**
-   Appuyez sur l’icône d’épingle pour que la fenêtre sources de données ne soit pas masquée automatiquement. Vous devrez peut-être cliquer sur le bouton Actualiser si la fenêtre était déjà visible.

    ![Source de données 2](~/ef6/media/datasource2.png)

-   Dans Explorateur de solutions, double-cliquez sur le fichier **Form1.cs** pour ouvrir le formulaire principal dans le concepteur.
-   Sélectionnez la source de données de **catégorie** et faites-la glisser sur le formulaire. Par défaut, un nouveau DataGridView (**categoryDataGridView**) et des contrôles de barre d’outils de navigation sont ajoutés au concepteur. Ces contrôles sont liés aux composants BindingSource (**categoryBindingSource**) et du navigateur de liaison (**categoryBindingNavigator**) qui sont également créés.
-   Modifiez les colonnes sur le **categoryDataGridView**. Nous souhaitons définir la colonne **CategoryID** en lecture seule. La valeur de la propriété **CategoryID** est générée par la base de données après l’enregistrement des données.
    -   Cliquez avec le bouton droit sur le contrôle DataGridView et sélectionnez Modifier les colonnes...
    -   Sélectionnez la colonne CategoryId et affectez la valeur true à ReadOnly.
    -   Appuyez sur OK
-   Sélectionnez produits dans la source de données catégorie et faites-le glisser sur le formulaire. Les productDataGridView et productBindingSource sont ajoutés au formulaire.
-   Modifiez les colonnes sur le productDataGridView. Nous souhaitons masquer les colonnes CategoryId et Category et définir ProductId en lecture seule. La valeur de la propriété ProductId est générée par la base de données après l’enregistrement des données.
    -   Cliquez avec le bouton droit sur le contrôle DataGridView et sélectionnez **modifier les colonnes...**.
    -   Sélectionnez la colonne **ProductID** et affectez la valeur **true**à **ReadOnly** .
    -   Sélectionnez la colonne **CategoryID** et appuyez sur le bouton **supprimer** . Faites de même avec la colonne **Category** .
    -   Appuyez sur **OK**.

    Jusqu’à présent, nous avons associé nos contrôles DataGridView à des composants BindingSource dans le concepteur. Dans la section suivante, nous allons ajouter du code au code-behind pour définir categoryBindingSource. DataSource sur la collection d’entités actuellement suivies par DbContext. Lorsque nous avons fait glisser les produits de la catégorie sous la catégorie, le WinForms s’est engagé à configurer la propriété productsBindingSource. DataSource sur categoryBindingSource et la propriété productsBindingSource. DataMember sur Products. En raison de cette liaison, seuls les produits appartenant à la catégorie actuellement sélectionnée seront affichés dans le productDataGridView.
-   Activez le bouton **Enregistrer** dans la barre d’outils de navigation en cliquant sur le bouton droit de la souris et en sélectionnant **activé**.

    ![Concepteur de formulaire 1](~/ef6/media/form1-designer.png)

-   Ajoutez le gestionnaire d’événements pour le bouton enregistrer en double-cliquant sur le bouton. Cette opération ajoute le gestionnaire d’événements et vous permet d’afficher le code-behind du formulaire. Le code du gestionnaire d’événements **categoryBindingNavigatorSaveItem \_ Click** sera ajouté dans la section suivante.

## <a name="add-the-code-that-handles-data-interaction"></a>Ajouter le code qui gère l’interaction des données

Nous allons maintenant ajouter le code permettant d’utiliser le ProductContext pour accéder aux données. Mettez à jour le code de la fenêtre principale du formulaire comme indiqué ci-dessous.

Le code déclare une instance de longue durée de ProductContext. L’objet ProductContext est utilisé pour interroger et enregistrer des données dans la base de données. La méthode Dispose () sur l’instance ProductContext est ensuite appelée à partir de la méthode OnClosing substituée. Les commentaires de code fournissent des détails sur ce que fait le code.

``` csharp
    using System;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Data;
    using System.Drawing;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows.Forms;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public partial class Form1 : Form
        {
            ProductContext _context;
            public Form1()
            {
                InitializeComponent();
            }

            protected override void OnLoad(EventArgs e)
            {
                base.OnLoad(e);
                _context = new ProductContext();

                // Call the Load method to get the data for the given DbSet
                // from the database.
                // The data is materialized as entities. The entities are managed by
                // the DbContext instance.
                _context.Categories.Load();

                // Bind the categoryBindingSource.DataSource to
                // all the Unchanged, Modified and Added Category objects that
                // are currently tracked by the DbContext.
                // Note that we need to call ToBindingList() on the
                // ObservableCollection<TEntity> returned by
                // the DbSet.Local property to get the BindingList<T>
                // in order to facilitate two-way binding in WinForms.
                this.categoryBindingSource.DataSource =
                    _context.Categories.Local.ToBindingList();
            }

            private void categoryBindingNavigatorSaveItem_Click(object sender, EventArgs e)
            {
                this.Validate();

                // Currently, the Entity Framework doesn’t mark the entities
                // that are removed from a navigation property (in our example the Products)
                // as deleted in the context.
                // The following code uses LINQ to Objects against the Local collection
                // to find all products and marks any that do not have
                // a Category reference as deleted.
                // The ToList call is required because otherwise
                // the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can do LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                // Save the changes to the database.
                this._context.SaveChanges();

                // Refresh the controls to show the values         
                // that were generated by the database.
                this.categoryDataGridView.Refresh();
                this.productsDataGridView.Refresh();
            }

            protected override void OnClosing(CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }
    }
```

## <a name="test-the-windows-forms-application"></a>Tester l’application Windows Forms

-   Compilez et exécutez l’application, et vous pouvez tester la fonctionnalité.

    ![Formulaire 1 avant l’enregistrement](~/ef6/media/form1beforesave.png)

-   Une fois les clés générées par le magasin enregistrées, elles s’affichent à l’écran.

    ![Formulaire 1 après enregistrement](~/ef6/media/form1aftersave.png)

-   Si vous avez utilisé Code First, vous verrez également qu’une base de données **WinFormswithEFSample. ProductContext** est créée pour vous.

    ![Explorateur d’objets serveur](~/ef6/media/serverobjexplorer.png)
