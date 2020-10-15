---
title: Spatial-EF designer-EF6
description: Concepteur spatial-EF dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 298666e558ad5bb531948e6a0bbcf3d71141df0b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064911"
---
# <a name="spatial---ef-designer"></a>Concepteur spatial-EF
> [!NOTE]
> **EF5 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 5. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

La vidéo et la procédure pas à pas montrent comment mapper des types spatiaux avec l’Entity Framework Designer. Il montre également comment utiliser une requête LINQ pour rechercher une distance entre deux emplacements.

Cette procédure pas à pas utilise Model First pour créer une base de données, mais le concepteur EF peut également être utilisé avec le flux de travail [Database First](xref:ef6/modeling/designer/workflows/database-first) pour mapper à une base de données existante.

La prise en charge des types spatiaux a été introduite dans Entity Framework 5. Notez que pour utiliser les nouvelles fonctionnalités telles que le type spatial, les énumérations et les fonctions table, vous devez cibler .NET Framework 4,5. Visual Studio 2012 cible .NET 4,5 par défaut.

Pour utiliser des types de données spatiales, vous devez également utiliser un fournisseur de Entity Framework qui a une prise en charge spatiale. Pour plus d’informations, consultez [prise en charge des fournisseurs pour les types spatiaux](xref:ef6/fundamentals/providers/spatial-support) .

Il existe deux types de données spatiales principales : Geography et Geometry. Le type de données geography stocke des données ellipsoïdal (par exemple, des coordonnées de latitude et de longitude GPS). Le type de données geometry représente le système de coordonnées euclidienne (Flat).

## <a name="watch-the-video"></a>Regarder la vidéo
Cette vidéo montre comment mapper des types spatiaux avec l’Entity Framework Designer. Il montre également comment utiliser une requête LINQ pour rechercher une distance entre deux emplacements.

**Présenté par**: Julia Kornich

**Vidéo**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez avoir installé Visual Studio 2012, Ultimate, Premium, Professional ou Web Express Edition.

## <a name="set-up-the-project"></a>Configurer le projet

1.  Ouvrir Visual Studio 2012
2.  Dans le menu **fichier** , pointez sur **nouveau**, puis cliquez sur **projet** .
3.  Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .
4.  Entrez **SpatialEFDesigner** comme nom du projet, puis cliquez sur **OK** .

## <a name="create-a-new-model-using-the-ef-designer"></a>Créer un nouveau modèle à l’aide du concepteur EF

1.  Cliquez avec le bouton droit sur le nom du projet dans Explorateur de solutions, pointez sur **Ajouter**, puis cliquez sur **nouvel élément** .
2.  Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.
3.  Entrez **UniversityModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter** .
4.  Dans la page Entity Data Model de l’Assistant, sélectionnez **modèle vide** dans la boîte de dialogue choisir le contenu du modèle.
5.  Cliquez sur **Terminer**

Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.

L'assistant exécute les actions suivantes :

-   Génère le fichier EnumTestModel. edmx qui définit le modèle conceptuel, le modèle de stockage et le mappage entre eux. Définit la propriété de traitement des artefacts de métadonnées du fichier. edmx à incorporer dans l’assembly de sortie afin que les fichiers de métadonnées générés soient incorporés dans l’assembly.
-   Ajoute une référence aux assemblys suivants : EntityFramework, System. ComponentModel. DataAnnotations et System. Data. Entity.
-   Crée des fichiers UniversityModel.tt et UniversityModel.Context.tt et les ajoute sous le fichier. edmx. Ces fichiers de modèle T4 génèrent le code qui définit le type dérivé DbContext et les types POCO qui mappent aux entités dans le modèle. edmx

## <a name="add-a-new-entity-type"></a>Ajouter un nouveau type d’entité

1.  Cliquez avec le bouton droit sur une zone vide de l’aire de conception, sélectionnez **ajouter- &gt; entité**; la boîte de dialogue nouvelle entité s’affiche.
2.  Spécifiez **University** comme nom de type et spécifiez **UniversityID** pour le nom de la propriété de clé, laissez le type **Int32**
3.  Cliquez sur **OK**
4.  Cliquez avec le bouton droit sur l’entité, puis sélectionnez **Ajouter une nouvelle &gt; propriété-scalaire** .
5.  Renommez la nouvelle propriété en **Name**
6.  Ajoutez une autre propriété scalaire et renommez-la **emplacement** ouvrir le fenêtre Propriétés et remplacez le type de la nouvelle propriété par **Geography**
7.  Enregistrer le modèle et générer le projet
    > [!NOTE]
    > Lorsque vous générez, les avertissements relatifs aux entités et associations non mappés peuvent apparaître dans le Liste d’erreurs. Vous pouvez ignorer ces avertissements, car une fois que vous avez choisi de générer la base de données à partir du modèle, les erreurs disparaissent.

## <a name="generate-database-from-model"></a>Générer la base de données à partir du modèle

Nous pouvons maintenant générer une base de données basée sur le modèle.

1.  Cliquez avec le bouton droit sur un espace vide sur l’aire de Entity Designer et sélectionnez **générer la base de données à partir du modèle** .
2.  La boîte de dialogue choisir votre connexion de données de l’Assistant générer la base de données s’affiche. cliquez sur le bouton **nouvelle connexion** , spécifiez (base de données **) \\ mssqllocaldb** pour le nom du serveur et l' **Université** pour la base de données, puis cliquez sur **OK** .
3.  Une boîte de dialogue vous demandant si vous souhaitez créer une nouvelle base de données s’affiche, cliquez sur **Oui**.
4.  Cliquez sur **suivant** pour que l’Assistant Création d’une base de données génère le langage de définition de données (DDL) pour la création d’une base de données. la DDL générée est affichée dans la boîte de dialogue Résumé et paramètres. Notez que le DDL ne contient pas de définition pour une table qui correspond au type d’énumération.
5.  Cliquez sur **Terminer** pour ne pas exécuter le script DDL.
6.  L’Assistant Création d’une base de données effectue les opérations suivantes : ouvre **UniversityModel. edmx. SQL** dans l’éditeur T-SQL génère les sections de schéma de stockage et de mappage du fichier edmx ajoute les informations de chaîne de connexion au fichier App.config
7.  Cliquez sur le bouton droit de la souris dans l’éditeur T-SQL et sélectionnez **exécuter** la boîte de dialogue se connecter au serveur, entrez les informations de connexion de l’étape 2, puis cliquez sur **se connecter** .
8.  Pour afficher le schéma généré, cliquez avec le bouton droit sur le nom de la base de données dans Explorateur d’objets SQL Server puis sélectionnez **Actualiser** .

## <a name="persist-and-retrieve-data"></a>Conserver et récupérer des données

Ouvrez le fichier Program.cs dans lequel la méthode main est définie. Ajoutez le code suivant à la fonction main.

Le code ajoute deux nouveaux objets University au contexte. Les propriétés spatiales sont initialisées à l’aide de la méthode DbGeography. FromText. Le point géographique représenté en tant que WellKnownText est passé à la méthode. Le code enregistre ensuite les données. Ensuite, la requête LINQ qui retourne un objet University dans lequel son emplacement est le plus proche de l’emplacement spécifié est construite et exécutée.

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
    {
        Name = "School of Fine Art",
        Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
    });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                                orderby u.Location.Distance(myLocation)
                                select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

Compilez et exécutez l'application. Le programme génère la sortie suivante :

```console
The closest University to you is: School of Fine Art.
```

Pour afficher les données de la base de données, cliquez avec le bouton droit sur le nom de la base de données dans Explorateur d’objets SQL Server, puis sélectionnez **Actualiser**. Ensuite, cliquez sur le bouton droit de la souris sur la table et sélectionnez **afficher les données**.

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, nous avons vu comment mapper des types spatiaux à l’aide de la Entity Framework Designer et comment utiliser des types spatiaux dans le code. 
