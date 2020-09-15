---
title: Fractionnement de table du concepteur-EF6
description: Fractionnement des tables du concepteur dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/table-splitting
ms.openlocfilehash: 6fdb5050ab4d3860184f19ea056a0f2257e20a3c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073108"
---
# <a name="designer-table-splitting"></a>Fractionnement des tables du concepteur
Cette procédure pas à pas montre comment mapper plusieurs types d’entité à une seule table en modifiant un modèle avec le Entity Framework Designer (concepteur EF).

L’une des raisons pour lesquelles vous pouvez souhaiter utiliser le fractionnement de table est de retarder le chargement de certaines propriétés lors de l’utilisation du chargement différé pour charger vos objets.Vous pouvez séparer les propriétés qui peuvent contenir de très grandes quantités de données dans une entité distincte et les charger uniquement lorsque cela est nécessaire.

L’illustration suivante montre les fenêtres principales qui sont utilisées lors de l’utilisation du concepteur EF.

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>Prérequis

Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :

- Une version récente de Visual Studio.
- [Exemple de base de données School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurer le projet

Cette procédure pas à pas utilise Visual Studio 2012.

-   Ouvrez Visual Studio 2012.
-   Dans le menu **Fichier** , pointez sur **Nouveau**, puis cliquez sur **Projet**.
-   Dans le volet gauche, cliquez sur Visual C \# , puis sélectionnez le modèle application console.
-   Entrez **TableSplittingSample** comme nom du projet, puis cliquez sur **OK**.

## <a name="create-a-model-based-on-the-school-database"></a>Créer un modèle basé sur la base de données School

-   Cliquez avec le bouton droit sur le nom du projet dans Explorateur de solutions, pointez sur **Ajouter**, puis cliquez sur **nouvel élément**.
-   Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.
-   Entrez **TableSplittingModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.
-   Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant.**
-   Cliquez sur nouvelle connexion. Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, **( \\ mssqllocaldb)**, sélectionnez la méthode d’authentification, tapez **School**   comme nom de la base de données, puis cliquez sur **OK**.
    La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.
-   Dans la boîte de dialogue choisir vos objets de base de données, dérouler le nœud **tables**   et vérifier la table **Person** . Cette opération ajoute la table spécifiée au modèle **School** .
-   Cliquez sur **Terminer**.

Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché. Tous les objets que vous avez sélectionnés dans la boîte **de dialogue choisir vos objets de base de données**   sont ajoutés au modèle.

## <a name="map-two-entities-to-a-single-table"></a>Mapper deux entités à une table unique

Dans cette section, vous allez fractionner l’entité **Person** en deux entités, puis les mapper à une table unique.

> [!NOTE]
> L’entité **Person** ne contient pas de propriétés pouvant contenir une grande quantité de données ; elle est utilisée à titre d’exemple.

-   Cliquez avec le bouton droit sur une zone vide de l’aire de conception, pointez sur **Ajouter nouveau**, puis cliquez sur **entité**.
    La boîte **de dialogue nouvelle entité**   s’affiche.
-   Tapez **HireInfo**   pour le nom de l' **entité** et **PersonID** pour le nom de la **propriété de clé** .
-   Cliquez sur  **OK**.
-   Un nouveau type d'entité est créé et affiché sur l'aire de conception.
-   Sélectionnez la **HireDate**   propriété HireDate du type d' **Person**   entité Person et appuyez sur les touches **CTRL + X** .
-   Sélectionnez l’entité **HireInfo**   et appuyez sur les touches **Ctrl + V** .
-   Créer une association entre **Person** et **HireInfo**. Pour ce faire, cliquez avec le bouton droit sur une zone vide de l’aire de conception, pointez sur **Ajouter nouveau**, puis cliquez sur **Association**.
-   La boîte de dialogue **Ajouter une association**   s’affiche. Le nom **PersonHireInfo** est donné par défaut.
-   Spécifiez la multiplicité **1 (un)** aux deux extrémités de la relation.
-   Appuyez sur **OK**.

L’étape suivante nécessite la fenêtre **Détails de mappage**   . Si vous ne voyez pas cette fenêtre, cliquez avec le bouton droit sur l’aire de conception, puis sélectionnez **Détails de mapping**.

-   Sélectionnez le **HireInfo**   type d’entité HireInfo, puis cliquez sur ** &lt; Ajouter une &gt; table ou une vue**   dans la fenêtre **Détails de mappage**   .
-   Sélectionnez **Person** dans la liste déroulante ** &lt; ajouter &gt; un champ de table ou de vue**   . La liste contient les tables ou les vues auxquelles l’entité sélectionnée peut être mappée.
    Les propriétés appropriées doivent être mappées par défaut.

    ![Propriétés de mappage](~/ef6/media/mapping.png)

-   Sélectionnez l’Association **PersonHireInfo** sur l’aire de conception.
-   Cliquez avec le bouton droit sur l’Association sur l’aire de conception, puis sélectionnez **Propriétés**.
-   Dans la fenêtre **Propriétés** , sélectionnez la propriété **contraintes référentielles** , puis cliquez sur le bouton de sélection.
-   Sélectionnez **Person** dans la liste déroulante **principal** .
-   Appuyez sur **OK**.

 

## <a name="use-the-model"></a>Utiliser le modèle

-   Collez le code suivant dans la méthode main.

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   Compilez et exécutez l'application.

Les instructions T-SQL suivantes ont été exécutées sur la base de données **School** suite à l’exécution de cette application. 

-   L' **instruction INSERT** suivante a été exécutée à la suite de l’exécution du contexte. SaveChanges () et combine les données des entités **Person** et **HireInfo**

    ![Insérer la combinaison des données Person et HireInfo](~/ef6/media/insert.png)

-   La commande **Select** suivante a été exécutée à la suite de l’exécution du contexte. People. FirstOrDefault () et sélectionne uniquement les colonnes mappées à **Person**

    ![Sélectionnez 1](~/ef6/media/select1.png)

-   La commande **Select** suivante a été exécutée suite à l’accès à la propriété de navigation existingPerson. Instructor et sélectionne uniquement les colonnes mappées à **HireInfo**

    ![Sélectionner 2](~/ef6/media/select2.png)
