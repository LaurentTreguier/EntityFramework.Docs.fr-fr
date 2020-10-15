---
title: Database First EF6
description: Database First dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/database-first
ms.openlocfilehash: 4046144ff3f1ecb4a3c9e7d4cb5d6c462c3782c7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066107"
---
# <a name="database-first"></a>Database First
Cette vidéo et la procédure pas à pas fournissent une introduction au développement Database First à l’aide de Entity Framework. Database First vous permet de rétroconcevoir un modèle à partir d’une base de données existante. Le modèle est stocké dans un fichier EDMX (extension. edmx) et peut être affiché et modifié dans le Entity Framework Designer. Les classes avec lesquelles vous interagissez dans votre application sont générées automatiquement à partir du fichier EDMX.

## <a name="watch-the-video"></a>Regarder la vidéo
Cette vidéo fournit une introduction au développement Database First à l’aide de Entity Framework. Database First vous permet de rétroconcevoir un modèle à partir d’une base de données existante. Le modèle est stocké dans un fichier EDMX (extension. edmx) et peut être affiché et modifié dans le Entity Framework Designer. Les classes avec lesquelles vous interagissez dans votre application sont générées automatiquement à partir du fichier EDMX.

**Présentée par** : [Rowan Miller](https://romiller.com/)

**Vidéo**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv)  |  [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez avoir au moins Visual Studio 2010 ou Visual Studio 2012 installé.

Si vous utilisez Visual Studio 2010, [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) doit également être installé.

 

## <a name="1-create-an-existing-database"></a>1. créer une base de données existante

En général, lorsque vous ciblez une base de données existante, elle est déjà créée, mais pour cette procédure pas à pas, nous devons créer une base de données à laquelle accéder.

Le serveur de base de données installé avec Visual Studio diffère selon la version de Visual Studio que vous avez installée :

-   Si vous utilisez Visual Studio 2010, vous allez créer une base de données SQL Express.
-   Si vous utilisez Visual Studio 2012, vous allez créer une base de [données de base](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) de données locale.

 

Commençons par générer la base de données.

-   Ouvrir Visual Studio
-   **Vue- &gt; Explorateur de serveurs**
-   Cliquez avec le bouton droit sur **connexions de données- &gt; Ajouter une connexion...**
-   Si vous n’êtes pas connecté à une base de données à partir de Explorateur de serveurs avant de devoir sélectionner Microsoft SQL Server comme source de données

    ![Sélectionner une source de données](~/ef6/media/selectdatasource.png)

-   Connectez-vous à la base de données locale ou SQL Express, en fonction de celle que vous avez installée, puis entrez **DatabaseFirst. blog** comme nom de la base de données.

    ![Connexion SQL Express DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![Connexion de base de données locale DF](~/ef6/media/localdbconnectiondf.png)

-   Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .

    ![Boîte de dialogue créer une base de données](~/ef6/media/createdatabasedialog.png)

-   La nouvelle base de données s’affiche alors dans Explorateur de serveurs, cliquez dessus avec le bouton droit et sélectionnez **nouvelle requête** .
-   Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .

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
```

## <a name="2-create-the-application"></a>2. créer l’application

Pour simplifier les choses, nous allons créer une application console de base qui utilise le Database First pour effectuer l’accès aux données :

-   Ouvrir Visual Studio
-   **Fichier- &gt; nouveau- &gt; projet...**
-   Sélectionnez **Windows** dans le menu de gauche et dans l' **application console** .
-   Entrez **DatabaseFirstSample** comme nom
-   Sélectionnez **OK**.

 

## <a name="3-reverse-engineer-model"></a>3. modèle d’ingénierie à rebours

Nous allons utiliser Entity Framework Designer, inclus dans le cadre de Visual Studio, pour créer notre modèle.

-   **Projet- &gt; Ajouter un nouvel élément...**
-   Sélectionnez **données** dans le menu de gauche, puis **ADO.NET Entity Data Model**
-   Entrez **BloggingModel** comme nom et cliquez sur **OK** .
-   Cela lance l' **assistant Entity Data Model**
-   Sélectionnez **générer à partir de la base de données** , puis cliquez sur **suivant** .

    ![Étape 1 de l’Assistant](~/ef6/media/wizardstep1.png)

-   Sélectionnez la connexion à la base de données que vous avez créée dans la première section, entrez **BloggingContext** comme nom de la chaîne de connexion, puis cliquez sur **suivant** .

    ![Étape 2 de l’Assistant](~/ef6/media/wizardstep2.png)

-   Cochez la case en regard de « tables » pour importer toutes les tables, puis cliquez sur « Terminer ».

    ![Étape 3 de l’Assistant](~/ef6/media/wizardstep3.png)

 

Une fois le processus d’ingénierie à rebours terminé, le nouveau modèle est ajouté à votre projet et vous est ouvert pour que vous l’affichez dans le Entity Framework Designer. Un fichier de App.config a également été ajouté à votre projet avec les détails de connexion de la base de données.

![Modèle initial](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Étapes supplémentaires dans Visual Studio 2010

Si vous travaillez dans Visual Studio 2010, vous devez suivre certaines étapes supplémentaires pour effectuer une mise à niveau vers la dernière version de Entity Framework. La mise à niveau est importante, car elle vous permet d’accéder à une surface d’API améliorée, qui est beaucoup plus facile à utiliser, ainsi que les derniers correctifs de bogues.

Tout d’abord, nous devons récupérer la dernière version de Entity Framework à partir de NuGet.

-   **Projet – &gt; Gérer les packages NuGet...** 
     *Si vous ne disposez pas de l’option **gérer les packages NuGet,** vous devez installer la [dernière version de NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) *
-   Sélectionner l’onglet **en ligne**
-   Sélectionner le package **EntityFramework**
-   Cliquez sur **Install**.

Ensuite, nous devons permuter notre modèle pour générer le code qui utilise l’API DbContext, qui a été introduite dans les versions ultérieures de Entity Framework.

-   Cliquez avec le bouton droit sur une zone vide de votre modèle dans le concepteur EF, puis sélectionnez **Ajouter un élément de génération de code...**
-   Sélectionnez **modèles en ligne** dans le menu de gauche et recherchez **DbContext**
-   Sélectionnez le **Générateur de DBCONTEXT EF 5. x pour \# C**, entrez **BloggingModel** comme nom et cliquez sur **Ajouter** .

    ![DbContext (modèle)](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a>4. lecture & écriture de données

Maintenant que nous avons un modèle, il est temps de l’utiliser pour accéder à certaines données. Les classes que nous allons utiliser pour accéder aux données sont générées automatiquement pour vous en fonction du fichier EDMX.

*Cette capture d’écran provient de Visual Studio 2012, si vous utilisez Visual Studio 2010, les fichiers BloggingModel.tt et BloggingModel.Context.tt sont directement sous votre projet plutôt que imbriqués sous le fichier EDMX.*

![Classes générées DF](~/ef6/media/generatedclassesdf.png)

 

Implémentez la méthode main dans Program.cs comme indiqué ci-dessous. Ce code crée une nouvelle instance de notre contexte, puis l’utilise pour insérer un nouveau blog. Il utilise ensuite une requête LINQ pour récupérer tous les blogs de la base de données classée par ordre alphabétique par titre.

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

Vous pouvez maintenant exécuter l’application et la tester.

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a>5. traitement des modifications de la base de données

À présent, il est temps d’apporter des modifications à notre schéma de base de données, lorsque nous effectuons ces modifications, nous devons également mettre à jour notre modèle pour refléter ces modifications.

La première étape consiste à apporter des modifications au schéma de la base de données. Nous allons ajouter une table users au schéma.

-   Cliquez avec le bouton droit sur la base de données **DatabaseFirst. blogs** dans Explorateur de serveurs puis sélectionnez **nouvelle requête** .
-   Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

Maintenant que le schéma est mis à jour, il est temps de mettre à jour le modèle avec ces modifications.

-   Cliquez avec le bouton droit sur une zone vide de votre modèle dans le concepteur EF et sélectionnez mettre à jour le modèle à partir de la base de données. cette opération lance l’Assistant Mise à jour.
-   Dans l’onglet ajouter de l’Assistant Mise à jour, activez la case à cocher en regard de tables. cela indique que nous souhaitons ajouter de nouvelles tables à partir du schéma.
    *L’onglet actualiser affiche toutes les tables existantes dans le modèle dont les modifications sont recherchées pendant la mise à jour. Les onglets supprimer affichent toutes les tables qui ont été supprimées du schéma et sont également supprimées du modèle dans le cadre de la mise à jour. Les informations de ces deux onglets sont automatiquement détectées et sont fournies à titre d’information uniquement, vous ne pouvez pas modifier les paramètres.*

    ![Actualiser l’Assistant](~/ef6/media/refreshwizard.png)

-   Cliquez sur terminer dans l’Assistant Mise à jour

 

Le modèle est maintenant mis à jour pour inclure une nouvelle entité utilisateur qui est mappée à la table users que nous avons ajoutée à la base de données.

![Modèle mis à jour](~/ef6/media/modelupdated.png)

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, nous avons examiné Database First développement, ce qui nous a permis de créer un modèle dans le concepteur EF basé sur une base de données existante. Nous avons ensuite utilisé ce modèle pour lire et écrire des données de la base de données. Enfin, nous avons mis à jour le modèle pour refléter les modifications que nous avons apportées au schéma de base de données.
