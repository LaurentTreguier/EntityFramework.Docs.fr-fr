---
title: Prise en charge d’enum-concepteur EF-EF6
description: Prise en charge d’enum-concepteur EF dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 316e8160cfd69d9912d63260471eee3df0eb58f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620515"
---
# <a name="enum-support---ef-designer"></a>Prise en charge d’enum-concepteur EF
> [!NOTE]
> **EF5 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 5. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

Cette vidéo et la procédure pas à pas montrent comment utiliser les types ENUM avec l’Entity Framework Designer. Il montre également comment utiliser des enums dans une requête LINQ.

Cette procédure pas à pas utilise Model First pour créer une base de données, mais le concepteur EF peut également être utilisé avec le flux de travail [Database First](xref:ef6/modeling/designer/workflows/database-first) pour mapper à une base de données existante.

La prise en charge des énumérations a été introduite dans Entity Framework 5. Pour utiliser les nouvelles fonctionnalités telles que les énumérations, les types de données spatiales et les fonctions table, vous devez cibler .NET Framework 4,5. Visual Studio 2012 cible .NET 4,5 par défaut.

Dans Entity Framework, une énumération peut avoir les types sous-jacents suivants : **Byte**, **Int16**, **Int32**, **Int64** ou **SByte**.

## <a name="watch-the-video"></a>Regarder la vidéo
Cette vidéo montre comment utiliser les types ENUM avec l’Entity Framework Designer. Il montre également comment utiliser des enums dans une requête LINQ.

**Présenté par**: Julia Kornich

**Vidéo**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez avoir installé Visual Studio 2012, Ultimate, Premium, Professional ou Web Express Edition.

## <a name="set-up-the-project"></a>Configurer le projet

1.  Ouvrir Visual Studio 2012
2.  Dans le menu **fichier** , pointez sur **nouveau**, puis cliquez sur **projet** .
3.  Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .
4.  Entrez **EnumEFDesigner** comme nom du projet, puis cliquez sur **OK** .

## <a name="create-a-new-model-using-the-ef-designer"></a>Créer un nouveau modèle à l’aide du concepteur EF

1.  Cliquez avec le bouton droit sur le nom du projet dans Explorateur de solutions, pointez sur **Ajouter**, puis cliquez sur **nouvel élément** .
2.  Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.
3.  Entrez **EnumTestModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter** .
4.  Dans la page Entity Data Model de l’Assistant, sélectionnez **modèle vide** dans la boîte de dialogue choisir le contenu du modèle.
5.  Cliquez sur **Terminer**

Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.

L'assistant exécute les actions suivantes :

-   Génère le fichier EnumTestModel. edmx qui définit le modèle conceptuel, le modèle de stockage et le mappage entre eux. Définit la propriété de traitement des artefacts de métadonnées du fichier. edmx à incorporer dans l’assembly de sortie afin que les fichiers de métadonnées générés soient incorporés dans l’assembly.
-   Ajoute une référence aux assemblys suivants : EntityFramework, System. ComponentModel. DataAnnotations et System. Data. Entity.
-   Crée des fichiers EnumTestModel.tt et EnumTestModel.Context.tt et les ajoute sous le fichier. edmx. Ces fichiers de modèle T4 génèrent le code qui définit le type dérivé DbContext et les types POCO qui mappent aux entités dans le modèle. edmx.

## <a name="add-a-new-entity-type"></a>Ajouter un nouveau type d’entité

1.  Cliquez avec le bouton droit sur une zone vide de l’aire de conception, sélectionnez **ajouter- &gt; entité**; la boîte de dialogue nouvelle entité s’affiche.
2.  Spécifiez **Department** comme nom de type et spécifiez **DepartmentID** pour le nom de la propriété de clé, laissez le type **Int32**
3.  Cliquez sur **OK**
4.  Cliquez avec le bouton droit sur l’entité, puis sélectionnez **Ajouter une nouvelle &gt; propriété-scalaire** .
5.  Renommez la nouvelle propriété en **Name**
6.  Remplacez le type de la nouvelle propriété par **Int32** (par défaut, la nouvelle propriété est de type chaîne) pour modifier le type, ouvrez le fenêtre Propriétés et remplacez la propriété type par **Int32** .
7.  Ajoutez une autre propriété scalaire et renommez-la **budget**, modifiez le type en **Decimal**

## <a name="add-an-enum-type"></a>Ajouter un type enum

1.  Dans la Entity Framework Designer, cliquez avec le bouton droit sur la propriété Name, sélectionnez **convertir en enum** .

    ![Convertir en enum](~/ef6/media/converttoenum.png)

2.  Dans la boîte de dialogue **Ajouter un enum** , tapez **DepartmentNames** pour le nom du type d’énumération, remplacez le type sous-jacent par **Int32**, puis ajoutez les membres suivants au type : anglais, mathématiques et économie

    ![Ajouter un type enum](~/ef6/media/addenumtype.png)

3.  Appuyez sur **OK**
4.  Enregistrer le modèle et générer le projet
    > [!NOTE]
    > Lorsque vous générez, les avertissements relatifs aux entités et associations non mappés peuvent apparaître dans le Liste d’erreurs. Vous pouvez ignorer ces avertissements, car une fois que vous avez choisi de générer la base de données à partir du modèle, les erreurs disparaissent.

Si vous examinez le Fenêtre Propriétés, vous remarquerez que le type de la propriété Name a été remplacé par **DepartmentNames** et que le type enum récemment ajouté a été ajouté à la liste des types.

Si vous basculez vers la fenêtre Explorateur de modèles, vous verrez que le type a été également ajouté au nœud types d’énumération.

![Explorateur de modèles](~/ef6/media/modelbrowser.png)

>[!NOTE]
> Vous pouvez également ajouter de nouveaux types d’énumération à partir de cette fenêtre en cliquant sur le bouton droit de la souris et en sélectionnant **Ajouter un type d’énumération**. Une fois le type créé, il apparaît dans la liste des types et vous pouvez l’associer à une propriété.

## <a name="generate-database-from-model"></a>Générer la base de données à partir du modèle

Nous pouvons maintenant générer une base de données basée sur le modèle.

1.  Cliquez avec le bouton droit sur un espace vide sur l’aire de Entity Designer et sélectionnez **générer la base de données à partir du modèle** .
2.  La boîte de dialogue choisir votre connexion de données de l’Assistant générer la base de données s’affiche. cliquez sur le bouton **nouvelle connexion** , spécifiez (base de données **) \\ mssqllocaldb** pour le nom du serveur et **EnumTest** pour la base de données, puis cliquez sur **OK** .
3.  Une boîte de dialogue vous demandant si vous souhaitez créer une nouvelle base de données s’affiche, cliquez sur **Oui**.
4.  Cliquez sur **suivant** pour que l’Assistant Création d’une base de données génère le langage de définition de données (DDL) pour la création d’une base de données. la DDL générée est affichée dans la boîte de dialogue Résumé et paramètres. Notez que le DDL ne contient pas de définition pour une table qui correspond au type d’énumération.
5.  Cliquez sur **Terminer** pour ne pas exécuter le script DDL.
6.  L’Assistant Création d’une base de données effectue les opérations suivantes : ouvre **EnumTest. edmx. SQL** dans l’éditeur T-SQL génère les sections de schéma de stockage et de mappage du fichier edmx ajoute les informations de chaîne de connexion au fichier App.config
7.  Cliquez sur le bouton droit de la souris dans l’éditeur T-SQL et sélectionnez **exécuter** la boîte de dialogue se connecter au serveur, entrez les informations de connexion de l’étape 2, puis cliquez sur **se connecter** .
8.  Pour afficher le schéma généré, cliquez avec le bouton droit sur le nom de la base de données dans Explorateur d’objets SQL Server puis sélectionnez **Actualiser** .

## <a name="persist-and-retrieve-data"></a>Conserver et récupérer des données

Ouvrez le fichier Program.cs dans lequel la méthode main est définie. Ajoutez le code suivant à la fonction main. Le code ajoute un nouvel objet Department au contexte. Il enregistre ensuite les données. Le code exécute également une requête LINQ qui retourne un service dont le nom est DepartmentNames. English.

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

Compilez et exécutez l'application. Le programme génère la sortie suivante :

```console
DepartmentID: 1 Name: English
```

Pour afficher les données de la base de données, cliquez avec le bouton droit sur le nom de la base de données dans Explorateur d’objets SQL Server, puis sélectionnez **Actualiser**. Ensuite, cliquez sur le bouton droit de la souris sur la table et sélectionnez **afficher les données**.

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, nous avons vu comment mapper des types énumération à l’aide de la Entity Framework Designer et comment utiliser des enums dans le code. 
