---
title: Procédure pas à pas des entités de suivi automatique-EF6
description: Procédure pas à pas pour les entités de suivi automatique pour Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: b21207c9-1d95-4aa3-ae05-bc5fe300dab0
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough
ms.openlocfilehash: 942baae158d89acec98e70c391f677349148068c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616142"
---
# <a name="self-tracking-entities-walkthrough"></a>Procédure pas à pas des entités de suivi automatique
> [!IMPORTANT]
> Nous ne recommandons plus d’utiliser le modèle des entités de suivi automatique. Il reste disponible uniquement pour prendre en charge les applications existantes. Si votre application doit utiliser des graphiques d’entités déconnectés, envisagez d’autres alternatives comme les [Entités traçables](https://trackableentities.github.io/), qui présentent une technologie similaire aux entités de suivi automatique, mais développée de manière plus active par la communauté, ou bien l’écriture de code personnalisé à l’aide des API de suivi de changements de bas niveau.

Cette procédure pas à pas illustre le scénario dans lequel un service Windows Communication Foundation (WCF) expose une opération qui retourne un graphique d’entité. Ensuite, une application cliente manipule ce graphique et soumet les modifications à une opération de service qui valide et enregistre les mises à jour dans une base de données à l’aide de Entity Framework.

Avant d’effectuer cette procédure pas à pas, veillez à lire la page [entités de suivi automatique](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) .

Cette procédure pas à pas effectue les actions suivantes :

-   Crée une base de données à laquelle accéder.
-   Crée une bibliothèque de classes qui contient le modèle.
-   Bascule vers le modèle générateur d’entité de suivi automatique.
-   Déplace les classes d’entité vers un projet distinct.
-   Crée un service WCF qui expose des opérations pour interroger et enregistrer des entités.
-   Crée des applications clientes (console et WPF) qui consomment le service.

Nous allons utiliser Database First dans cette procédure pas à pas, mais les mêmes techniques s’appliquent également à Model First.

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous avez besoin d’une version récente de Visual Studio.

## <a name="create-a-database"></a>Créer une base de données

Le serveur de base de données installé avec Visual Studio diffère selon la version de Visual Studio que vous avez installée :

-   Si vous utilisez Visual Studio 2012, vous allez créer une base de données de base de données locale.
-   Si vous utilisez Visual Studio 2010, vous allez créer une base de données SQL Express.

Commençons par générer la base de données.

-   Ouvrez Visual Studio.
-   **Vue- &gt; Explorateur de serveurs**
-   Cliquez avec le bouton droit sur **connexions de données- &gt; Ajouter une connexion...**
-   Si vous n’êtes pas connecté à une base de données à partir de Explorateur de serveurs avant de devoir sélectionner **Microsoft SQL Server** comme source de données
-   Connectez-vous à la base de données locale ou SQL Express, en fonction de celle que vous avez installée
-   Entrez **STESample** comme nom de la base de données
-   Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .
-   La nouvelle base de données s’affiche à présent dans Explorateur de serveurs
-   Si vous utilisez Visual Studio 2012
    -   Cliquez avec le bouton de droite sur la base de données dans l’Explorateur de serveurs et sélectionnez l’option **Nouvelle requête**.
    -   Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .
-   Si vous utilisez Visual Studio 2010
    -   Sélectionner **des données- &gt; éditeur Transact SQL- &gt; nouvelle connexion à la requête...**
    -   Entrez **. \\ SQLEXPRESS** comme nom de serveur, puis cliquez sur **OK**
    -   Sélectionnez la base de données **STESample** dans la liste déroulante en haut de l’éditeur de requête.
    -   Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **Exécuter SQL** .

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

## <a name="create-the-model"></a>Créer le modèle

Tout d’abord, nous avons besoin d’un projet dans lequel placer le modèle.

-   **Fichier- &gt; nouveau- &gt; projet...**
-   Sélectionnez **Visual C \# ** dans le volet gauche, puis **bibliothèque de classes**
-   Entrez **STESample** comme nom et cliquez sur **OK** .

Nous allons maintenant créer un modèle simple dans le concepteur EF pour accéder à la base de données :

-   **Projet- &gt; Ajouter un nouvel élément...**
-   Dans le volet gauche, sélectionnez **données** , puis **ADO.NET Entity Data Model**
-   Entrez **BloggingModel** comme nom et cliquez sur **OK** .
-   Sélectionnez **générer à partir de la base de données** , puis cliquez sur **suivant** .
-   Entrez les informations de connexion pour la base de données que vous avez créée dans la section précédente.
-   Entrez **BloggingContext** comme nom de la chaîne de connexion, puis cliquez sur **suivant** .
-   Cochez la case en regard de **tables** , puis cliquez sur **Terminer** .

## <a name="swap-to-ste-code-generation"></a>Basculer vers la génération de code STE

Maintenant, nous devons désactiver la génération de code et l’échange par défaut pour les entités de suivi automatique.

### <a name="if-you-are-using-visual-studio-2012"></a>Si vous utilisez Visual Studio 2012

-   Développez **BloggingModel. edmx** dans **Explorateur de solutions** et supprimez le **BloggingModel.TT** et le **BloggingModel.Context.TT** 
     *la génération de code par défaut est désactivée* .
-   Cliquez avec le bouton droit sur une zone vide de l’aire du concepteur EF, puis sélectionnez **Ajouter un élément de génération de code...**
-   Sélectionnez **en ligne** dans le volet gauche et recherchez le **Générateur Ste**
-   Sélectionnez le **Générateur Ste pour le \# modèle C** , entrez **STETemplate** comme nom et cliquez sur **Ajouter** .
-   Les fichiers **STETemplate.TT** et **STETemplate.Context.TT** sont ajoutés imbriqués sous le fichier BloggingModel. edmx

### <a name="if-you-are-using-visual-studio-2010"></a>Si vous utilisez Visual Studio 2010

-   Cliquez avec le bouton droit sur une zone vide de l’aire du concepteur EF, puis sélectionnez **Ajouter un élément de génération de code...**
-   Sélectionnez **code** dans le volet gauche, puis **Générateur d’entité de suivi automatique ADO.net**
-   Entrez **STETemplate** comme nom et cliquez sur **Ajouter** .
-   Les fichiers **STETemplate.TT** et **STETemplate.Context.TT** sont ajoutés directement à votre projet

## <a name="move-entity-types-into-separate-project"></a>Déplacer les types d’entités dans un projet distinct

Pour utiliser les entités de suivi automatique, notre application cliente a besoin d’accéder aux classes d’entité générées à partir de notre modèle. Étant donné que nous ne souhaitons pas exposer l’ensemble du modèle à l’application cliente, nous allons déplacer les classes d’entité dans un projet distinct.

La première étape consiste à arrêter la génération de classes d’entité dans le projet existant :

-   Cliquez avec le bouton droit sur **STETemplate.TT** dans **Explorateur de solutions** et sélectionnez **Propriétés** .
-   Dans la fenêtre **Propriétés** , effacez **TextTemplatingFileGenerator** de la propriété **CustomTool**
-   Développez **STETemplate.TT** dans **Explorateur de solutions** et supprimez tous les fichiers imbriqués sous celui-ci.

Ensuite, nous allons ajouter un nouveau projet et générer les classes d’entité qu’il contient.

-   **Fichier- &gt; ajouter- &gt; projet...**
-   Sélectionnez **Visual C \# ** dans le volet gauche, puis **bibliothèque de classes**
-   Entrez **STESample. Entities** comme nom et cliquez sur **OK** .
-   **Projet- &gt; Ajouter un élément existant...**
-   Accédez au dossier du projet **STESample**
-   Sélectionnez cette option pour afficher **tous les fichiers ( \* . \* )**
-   Sélectionner le fichier **STETemplate.TT**
-   Cliquez sur la flèche déroulante à côté du bouton **Ajouter** et sélectionnez **Ajouter en tant que lien**

    ![Ajouter un modèle lié](~/ef6/media/addlinkedtemplate.png)

Nous allons également nous assurer que les classes d’entité sont générées dans le même espace de noms que le contexte. Cela réduit simplement le nombre d’instructions using que nous devons ajouter dans notre application.

-   Cliquez avec le bouton droit sur le **STETemplate.TT** lié dans **Explorateur de solutions** et sélectionnez **Propriétés** .
-   Dans la fenêtre **Propriétés** , définissez espace de noms de l' **outil personnalisé** sur **STESample**

Le code généré par le modèle STE aura besoin d’une référence à **System. Runtime. Serialization** pour pouvoir être compilé. Cette bibliothèque est nécessaire pour les attributs **DataContract** et **DataMember** WCF utilisés sur les types d’entités sérialisables.

-   Cliquez avec le bouton droit sur le projet **STESample. Entities** dans **Explorateur de solutions** puis sélectionnez **Ajouter une référence...**
    -   Dans Visual Studio 2012, activez la case à cocher en regard de **System. Runtime. Serialization** , puis cliquez sur **OK** .
    -   Dans Visual Studio 2010, sélectionnez **System. Runtime. Serialization** , puis cliquez sur **OK** .

Enfin, le projet avec notre contexte doit avoir une référence aux types d’entité.

-   Cliquez avec le bouton droit sur le projet **STESample** dans **Explorateur de solutions** puis sélectionnez **Ajouter une référence...**
    -   Dans Visual Studio 2012-sélectionnez **solution** dans le volet gauche, cochez la case en regard de **STESample. Entities** , puis cliquez sur **OK** .
    -   Dans Visual Studio 2010-sélectionnez l’onglet **projets** , sélectionnez **STESample. Entities** , puis cliquez sur **OK** .

>[!NOTE]
> Une autre option pour déplacer les types d’entités vers un projet distinct consiste à déplacer le fichier de modèle au lieu de le lier à partir de son emplacement par défaut. Si vous procédez ainsi, vous devrez mettre à jour la variable **FichierEntrée** dans le modèle pour fournir le chemin d’accès relatif au fichier edmx (dans cet exemple, il s’agit de **. \\ BloggingModel. edmx**).

## <a name="create-a-wcf-service"></a>Créer un service WCF

Maintenant, il est temps d’ajouter un service WCF pour exposer nos données, nous allons commencer par créer le projet.

-   **Fichier- &gt; ajouter- &gt; projet...**
-   Sélectionnez **Visual C \# ** dans le volet gauche, puis **application de service WCF** .
-   Entrez **STESample. service** comme nom et cliquez sur **OK** .
-   Ajouter une référence à l’assembly **System. Data. Entity**
-   Ajouter une référence aux projets **STESample** et **STESample. Entities**

Nous devons copier la chaîne de connexion EF dans ce projet afin qu’elle soit trouvée lors de l’exécution.

-   Ouvrez le fichier **App.Config** pour le projet **STESample **et copiez l’élément **connectionStrings**
-   Collez l’élément **connectionStrings** en tant qu’élément enfant de l’élément **configuration** du fichier **Web.Config** dans le projet **STESample. service.**

Il est maintenant temps de mettre en œuvre le service réel.

-   Ouvrez **IService1.cs** et remplacez le contenu par le code suivant :

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

-   Ouvrez **Service1. svc** et remplacez le contenu par le code suivant :

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

## <a name="consume-the-service-from-a-console-application"></a>Utiliser le service à partir d’une application console

Nous allons créer une application console qui utilise notre service.

-   **Fichier- &gt; nouveau- &gt; projet...**
-   Sélectionnez **Visual C \# ** dans le volet gauche, puis **application console** .
-   Entrez **STESample. ConsoleTest** comme nom et cliquez sur **OK** .
-   Ajouter une référence au projet **STESample. Entities**

Nous avons besoin d’une référence de service à notre service WCF

-   Cliquez avec le bouton droit sur le projet **STESample. ConsoleTest** dans **Explorateur de solutions** puis sélectionnez **Ajouter une référence de service...**
-   Cliquez sur **découvrir**
-   Entrez **BloggingService** comme espace de noms, puis cliquez sur **OK** .

Nous pouvons maintenant écrire du code pour consommer le service.

-   Ouvrez **Program.cs** et remplacez le contenu par le code suivant.

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

Vous pouvez à présent exécuter l’application pour la voir en action.

-   Cliquez avec le bouton droit sur le projet **STESample. ConsoleTest** dans **Explorateur de solutions** puis sélectionnez **Déboguer- &gt; Démarrer une nouvelle instance**

La sortie suivante s’affiche lors de l’exécution de l’application.

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

## <a name="consume-the-service-from-a-wpf-application"></a>Utiliser le service à partir d’une application WPF

Nous allons créer une application WPF qui utilise notre service.

-   **Fichier- &gt; nouveau- &gt; projet...**
-   Sélectionnez **Visual C \# ** dans le volet gauche, puis **application WPF** .
-   Entrez **STESample. WPFTest** comme nom et cliquez sur **OK** .
-   Ajouter une référence au projet **STESample. Entities**

Nous avons besoin d’une référence de service à notre service WCF

-   Cliquez avec le bouton droit sur le projet **STESample. WPFTest** dans **Explorateur de solutions** puis sélectionnez **Ajouter une référence de service...**
-   Cliquez sur **découvrir**
-   Entrez **BloggingService** comme espace de noms, puis cliquez sur **OK** .

Nous pouvons maintenant écrire du code pour consommer le service.

-   Ouvrez **MainWindow. Xaml** et remplacez le contenu par le code suivant.

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

-   Ouvrez le code-behind pour MainWindow (**MainWindow.Xaml.cs**) et remplacez le contenu par le code suivant :

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

Vous pouvez à présent exécuter l’application pour la voir en action.

-   Cliquez avec le bouton droit sur le projet **STESample. WPFTest** dans **Explorateur de solutions** puis sélectionnez **Déboguer- &gt; Démarrer une nouvelle instance**
-   Vous pouvez manipuler les données à l’aide de l’écran et les enregistrer par le biais du service à l’aide du bouton **Enregistrer** .

![Fenêtre principale WPF](~/ef6/media/wpf.png)
