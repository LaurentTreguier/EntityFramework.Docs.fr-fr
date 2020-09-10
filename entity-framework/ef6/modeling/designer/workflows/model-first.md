---
title: Model First EF6
description: Model First dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: e1b9c319-bb8a-4417-ac94-7890f257e7f6
uid: ef6/modeling/designer/workflows/model-first
ms.openlocfilehash: e995072f0f4ac23b755acc193719e5571e18f544
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620411"
---
# <a name="model-first"></a>Model First
Cette vidéo et la procédure pas à pas fournissent une introduction au développement Model First à l’aide de Entity Framework. Model First vous permet de créer un nouveau modèle à l’aide du Entity Framework Designer puis de générer un schéma de base de données à partir du modèle. Le modèle est stocké dans un fichier EDMX (extension. edmx) et peut être affiché et modifié dans le Entity Framework Designer. Les classes avec lesquelles vous interagissez dans votre application sont générées automatiquement à partir du fichier EDMX.

## <a name="watch-the-video"></a>Regarder la vidéo
Cette vidéo et la procédure pas à pas fournissent une introduction au développement Model First à l’aide de Entity Framework. Model First vous permet de créer un nouveau modèle à l’aide du Entity Framework Designer puis de générer un schéma de base de données à partir du modèle. Le modèle est stocké dans un fichier EDMX (extension. edmx) et peut être affiché et modifié dans le Entity Framework Designer. Les classes avec lesquelles vous interagissez dans votre application sont générées automatiquement à partir du fichier EDMX.

**Présentée par** : [Rowan Miller](https://romiller.com/)

**Vidéo**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv)  |  [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez avoir installé Visual Studio 2010 ou Visual Studio 2012.

Si vous utilisez Visual Studio 2010, [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) doit également être installé.

## <a name="1-create-the-application"></a>1. créer l’application

Pour simplifier les choses, nous allons créer une application console de base qui utilise le Model First pour effectuer l’accès aux données :

-   Ouvrez Visual Studio.
-   **Fichier- &gt; nouveau- &gt; projet...**
-   Sélectionnez **Windows** dans le menu de gauche et dans l' **application console** .
-   Entrez **ModelFirstSample** comme nom
-   Sélectionnez **OK**.

## <a name="2-create-model"></a>2. créer un modèle

Nous allons utiliser Entity Framework Designer, inclus dans le cadre de Visual Studio, pour créer notre modèle.

-   **Projet- &gt; Ajouter un nouvel élément...**
-   Sélectionnez **données** dans le menu de gauche, puis **ADO.NET Entity Data Model**
-   Entrez **BloggingModel** comme nom et cliquez sur **OK**pour lancer l’Assistant Entity Data Model
-   Sélectionnez **modèle vide** , puis cliquez sur **Terminer**

    ![Créer un modèle vide](~/ef6/media/createemptymodel.png)

Le Entity Framework Designer est ouvert avec un modèle vide. Nous pouvons maintenant commencer à ajouter des entités, des propriétés et des associations au modèle.

-   Cliquez avec le bouton droit sur l’aire de conception et sélectionnez **Propriétés** .
-   Dans la fenêtre Propriétés remplacez le **nom du conteneur d’entités** par **BloggingContext**. 
     *il s’agit du nom du contexte dérivé qui sera généré pour vous, le contexte représente une session avec la base de données, ce qui nous permet d’interroger et d’enregistrer les données* .
-   Cliquez avec le bouton droit sur l’aire de conception et sélectionnez **Ajouter une nouvelle &gt; entité...**
-   Entrez **blog** comme nom de l’entité et **BlogId** comme nom de clé, puis cliquez sur **OK** .

    ![Ajouter une entité de blog](~/ef6/media/addblogentity.png)

-   Cliquez avec le bouton droit sur la nouvelle entité sur l’aire de conception et sélectionnez **Ajouter une nouvelle &gt; propriété-scalaire**, puis entrez **nom** comme nom de la propriété.
-   Répétez cette procédure pour ajouter une propriété **URL** .
-   Cliquez avec le bouton droit sur la propriété **URL** sur l’aire de conception, puis sélectionnez **Propriétés**. dans la fenêtre Propriétés modifiez le paramètre **Nullable** en **true**. 
     *cela nous permet d’enregistrer un blog dans la base de données sans l’affecter à une URL*
-   À l’aide des techniques que vous venez d’apprendre, ajoutez une entité de **publication** avec une propriété de clé **PostId**
-   Ajouter des propriétés scalaires de **titre** et de **contenu** à l’entité de **publication**

Maintenant que nous avons deux entités, il est temps d’ajouter une association (ou une relation) entre elles.

-   Cliquez avec le bouton droit sur l’aire de conception et sélectionnez **Ajouter une nouvelle &gt; Association...**
-   Faire une extrémité du point de relation au **blog** avec une multiplicité d’un **et l'** autre point de terminaison à **publier** avec une multiplicité de **nombreux** 
     *cela signifie qu’un blog contient de nombreuses publications et qu’un billet appartient à un blog*
-   Vérifiez que la case **Ajouter les propriétés de clé étrangère à l’entité « poster »** est cochée, puis cliquez sur **OK** .

    ![Ajouter une association MF](~/ef6/media/addassociationmf.png)

Nous disposons à présent d’un modèle simple qui nous permet de générer une base de données et de l’utiliser pour lire et écrire des données.

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

## <a name="3-generating-the-database"></a>3. génération de la base de données

Étant donné notre modèle, Entity Framework pouvez calculer un schéma de base de données qui nous permettra de stocker et de récupérer des données à l’aide du modèle.

Le serveur de base de données installé avec Visual Studio diffère selon la version de Visual Studio que vous avez installée :

-   Si vous utilisez Visual Studio 2010, vous allez créer une base de données SQL Express.
-   Si vous utilisez Visual Studio 2012, vous allez créer une base de [données de base](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) de données locale.

Commençons par générer la base de données.

-   Cliquez avec le bouton droit sur l’aire de conception et sélectionnez **générer la base de données à partir du modèle...**
-   Cliquez sur **nouvelle connexion...** et spécifiez la base de données locale ou SQL Express, en fonction de la version de Visual Studio que vous utilisez, entrez **ModelFirst. blog** comme nom de la base de données.

    ![Connexion de base de données locale MF](~/ef6/media/localdbconnectionmf.png)

    ![Connexion SQL Express MF](~/ef6/media/sqlexpressconnectionmf.png)

-   Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .
-   Sélectionnez **suivant** pour que le Entity Framework designer calcule un script pour créer le schéma de base de données.
-   Une fois le script affiché, cliquez sur **Terminer** pour ajouter le script à votre projet et l’ouvrir.
-   Cliquez avec le bouton droit sur le script et sélectionnez **exécuter**. vous serez invité à spécifier la base de données à laquelle vous connecter, spécifiez la base de données locale ou la SQL Server Express, selon la version de Visual Studio que vous utilisez.

## <a name="4-reading--writing-data"></a>4. lecture & écriture de données

Maintenant que nous avons un modèle, il est temps de l’utiliser pour accéder à certaines données. Les classes que nous allons utiliser pour accéder aux données sont générées automatiquement pour vous en fonction du fichier EDMX.

*Cette capture d’écran provient de Visual Studio 2012, si vous utilisez Visual Studio 2010, les fichiers BloggingModel.tt et BloggingModel.Context.tt sont directement sous votre projet plutôt que imbriqués sous le fichier EDMX.*

![Classes générées](~/ef6/media/generatedclasses.png)

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

## <a name="5-dealing-with-model-changes"></a>5. traitement des modifications de modèle

À présent, il est temps d’apporter des modifications à notre modèle, lorsque nous effectuons ces modifications, nous devons également mettre à jour le schéma de base de données.

Nous allons commencer par ajouter une nouvelle entité utilisateur à notre modèle.

-   Ajoutez un nouveau nom d’entité **utilisateur** avec nom **d'** utilisateur comme nom de clé et **chaîne** comme type de propriété pour la clé.

    ![Ajouter une entité utilisateur](~/ef6/media/adduserentity.png)

-   Cliquez avec le bouton droit sur la propriété **username** sur l’aire de conception, puis sélectionnez **Propriétés**. dans la fenêtre Propriétés modifiez le paramètre **MaxLength** sur **50**. 
     *cela limite les données pouvant être stockées dans le nom d’utilisateur à 50 caractères* .
-   Ajouter une propriété scalaire **DisplayName** à l’entité **User**

Nous disposons désormais d’un modèle mis à jour et nous sommes prêts à mettre à jour la base de données pour qu’elle s’adapte à notre nouveau type d’entité utilisateur.

-   Cliquez avec le bouton droit sur l’aire de conception, puis sélectionnez **générer la base de données à partir du modèle...**, Entity Framework calcule un script pour recréer un schéma basé sur le modèle mis à jour.
-   Cliquez sur **Terminer**
-   Vous pouvez recevoir des avertissements concernant le remplacement du script DDL existant et des parties de mappage et de stockage du modèle. cliquez sur **Oui** pour ces deux avertissements.
-   Le script SQL mis à jour pour créer la base de données s’ouvre pour vous  
    *Le script généré supprimera toutes les tables existantes, puis recréera le schéma à partir de zéro. Cela peut fonctionner pour le développement local, mais n’est pas une solution viable pour envoyer des modifications à une base de données qui a déjà été déployée. Si vous avez besoin de publier des modifications dans une base de données qui a déjà été déployée, vous devez modifier le script ou utiliser un outil de comparaison de schémas pour calculer un script de migration.*
-   Cliquez avec le bouton droit sur le script et sélectionnez **exécuter**. vous serez invité à spécifier la base de données à laquelle vous connecter, spécifiez la base de données locale ou la SQL Server Express, selon la version de Visual Studio que vous utilisez.

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, nous avons examiné Model First développement, ce qui nous a permis de créer un modèle dans le concepteur EF, puis de générer une base de données à partir de ce modèle. Nous avons ensuite utilisé le modèle pour lire et écrire des données de la base de données. Enfin, nous avons mis à jour le modèle, puis recréé le schéma de base de données pour qu’il corresponde au modèle.
