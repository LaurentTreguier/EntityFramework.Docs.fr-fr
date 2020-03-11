---
title: Fractionnement d’entité du concepteur-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
ms.openlocfilehash: ba1895ae491cec909ff88a8784eea82f1876f595
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418461"
---
# <a name="designer-entity-splitting"></a>Fractionnement d’entité du concepteur
Cette procédure pas à pas montre comment mapper un type d’entité à deux tables en modifiant un modèle avec le Entity Framework Designer (concepteur EF). Vous pouvez mapper une entité à plusieurs tables quand les tables partagent une clé commune. Les concepts qui s’appliquent au mappage d’un type d’entité à deux tables sont facilement étendus pour le mappage d’un type d’entité à plus de deux tables.

L’illustration suivante montre les fenêtres principales qui sont utilisées lors de l’utilisation du concepteur EF.

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>Conditions préalables requises

Visual Studio 2012 ou Visual Studio 2010, édition intégrale, Premium, Professional ou Web Express.

## <a name="create-the-database"></a>Créer la base de données

Le serveur de base de données installé avec Visual Studio diffère selon la version de Visual Studio que vous avez installée :

-   Si vous utilisez Visual Studio 2012, vous allez créer une base de données de base de données locale.
-   Si vous utilisez Visual Studio 2010, vous allez créer une base de données SQL Express.

Tout d’abord, nous allons créer une base de données avec deux tables que nous allons combiner dans une entité unique.

-   Ouvrez Visual Studio.
-   **Vue-&gt; Explorateur de serveurs**
-   Cliquez avec le bouton droit sur **connexions de données-&gt; ajouter une connexion...**
-   Si vous n’êtes pas connecté à une base de données à partir de Explorateur de serveurs avant de devoir sélectionner **Microsoft SQL Server** comme source de données
-   Connectez-vous à la base de données locale ou SQL Express, en fonction de celle que vous avez installée
-   Entrez **EntitySplitting** comme nom de la base de données
-   Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .
-   La nouvelle base de données s’affiche à présent dans Explorateur de serveurs
-   Si vous utilisez Visual Studio 2012
    -   Dans Explorateur de serveurs, cliquez avec le bouton droit sur la base de données, puis sélectionnez **nouvelle requête** .
    -   Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .
-   Si vous utilisez Visual Studio 2010
    -   Sélectionner **les données-&gt; l’éditeur Transact SQL-&gt; nouvelle connexion à la requête...**
    -   Entrez **.\\SQLExpress** comme nom de serveur, puis cliquez sur **OK** .
    -   Sélectionnez la base de données **EntitySplitting** dans la liste déroulante en haut de l’éditeur de requête.
    -   Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **Exécuter SQL** .

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a>Création du projet

-   Dans le menu **Fichier** , pointez sur **Nouveau**, puis cliquez sur **Projet**.
-   Dans le volet gauche, cliquez sur **Visual C\#** , puis sélectionnez le modèle **application console** .
-   Entrez **MapEntityToTablesSample** comme nom du projet, puis cliquez sur **OK**.
-   Cliquez sur **non** si vous êtes invité à enregistrer la requête SQL créée dans la première section.

## <a name="create-a-model-based-on-the-database"></a>Créer un modèle basé sur la base de données

-   Cliquez avec le bouton droit sur le nom du projet dans Explorateur de solutions, pointez sur **Ajouter**, puis cliquez sur **nouvel élément**.
-   Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.
-   Entrez **MapEntityToTablesModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.
-   Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant.**
-   Sélectionnez la connexion **EntitySplitting** dans la liste déroulante, puis cliquez sur **suivant**.
-   Dans la boîte de dialogue choisir vos objets de base de données, activez la case à cocher en regard du nœud **Tables** .
    Cette opération ajoute toutes les tables de la base de données **EntitySplitting** au modèle.
-   Cliquez sur **Terminer**.

Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.

## <a name="map-an-entity-to-two-tables"></a>Mapper une entité à deux tables

Dans cette étape, nous allons mettre à jour le type d’entité **Person** pour combiner les données des tables **Person** et **PersonInfo** .

-   Sélectionnez les de **messagerie** et les propriétés de **téléphone** de l’entité **PersonInfo **, puis appuyez sur les touches **CTRL + X** .
-   Sélectionnez l’entité **Person **et appuyez sur les touches **Ctrl + V** .
-   Sur l’aire de conception, sélectionnez l’entité **PersonInfo** et appuyez sur la touche **Suppr** du clavier.
-   Cliquez sur **non** lorsque vous y êtes invité si vous souhaitez supprimer la table **PersonInfo** du modèle, nous sommes sur le sujet de la mapper à l’entité **Person** .

    ![Supprimer des tables](~/ef6/media/deletetables.png)

Les étapes suivantes nécessitent la fenêtre **Détails de mappage** . Si vous ne voyez pas cette fenêtre, cliquez avec le bouton droit sur l’aire de conception, puis sélectionnez **Détails de mapping**.

-   Sélectionnez la **personne** type d’entité, puis cliquez sur **&lt;ajouter une table ou une vue&gt;**  dans la fenêtre des **Détails de mappage** .
-   Sélectionnez **PersonInfo ** dans la liste déroulante.
    La fenêtre de des **Détails de mappage** est mise à jour avec les mappages de colonnes par défaut, ce qui est parfait pour notre scénario.

La **personne** type d’entité est maintenant mappé aux tables **Person** et **PersonInfo** .

![Mappage 2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a>Utiliser le modèle

-   Collez le code suivant dans la méthode main.

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   Compilez et exécutez l'application.

Les instructions T-SQL suivantes ont été exécutées sur la base de données suite à l’exécution de cette application. 

-   Les deux instructions **Insert** suivantes ont été exécutées à la suite de l’exécution du contexte. SaveChanges (). Ils prennent les données de l’entité **Person** et les fractionnent entre les tables **Person** et **PersonInfo** .

    ![Insérer 1](~/ef6/media/insert1.png)

    ![Insérer 2](~/ef6/media/insert2.png)
-   La commande **Select** suivante a été exécutée suite à l’énumération des personnes dans la base de données. Il combine les données de la table **Person** et **PersonInfo** .

    ![Sélectionnez](~/ef6/media/select.png)
