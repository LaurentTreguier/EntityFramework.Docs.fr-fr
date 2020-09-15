---
title: Procédures stockées CUD du concepteur-EF6
description: Procédures stockées CUD du concepteur dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/cud
ms.openlocfilehash: fae795922980452cf062a62e3cbb47de49b49c74
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073212"
---
# <a name="designer-cud-stored-procedures"></a>Procédures stockées CUD du concepteur

Cette procédure pas à pas explique comment mapper les \\ opérations de création d’insertion, de mise à jour et de suppression (CUD) d’un type d’entité aux procédures stockées à l’aide de l’Entity Framework Designer (concepteur EF). Par défaut, le Entity Framework génère automatiquement les instructions SQL pour les opérations CUD, mais vous pouvez également mapper des procédures stockées à ces opérations.  

Notez que Code First ne prend pas en charge le mappage à des procédures stockées ou à des fonctions. Toutefois, vous pouvez appeler des procédures stockées ou des fonctions à l’aide de la méthode System. Data. Entity. DbSet. SqlQuery. Exemple :

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a>Considérations relatives au mappage des opérations CUD à des procédures stockées

Lors du mappage des opérations CUD à des procédures stockées, les considérations suivantes s’appliquent :

- Si vous mappez l’une des opérations CUD à une procédure stockée, mappez-les toutes. Si vous ne mappez pas les trois, les opérations non mappées échouent si elles sont exécutées et une **UpdateException**   est levée.
- Vous devez mapper chaque paramètre de la procédure stockée à des propriétés d’entité.
- Si le serveur génère la valeur de clé primaire pour la ligne insérée, vous devez mapper cette valeur à la propriété de clé de l’entité. Dans l’exemple qui suit, la **InsertPerson**   procédure stockée InsertPerson retourne la clé primaire nouvellement créée dans le cadre du jeu de résultats de la procédure stockée. La clé primaire est mappée à la clé d’entité (**PersonID**) à l’aide de la fonctionnalité ** &lt; Ajouter des liaisons de &gt; résultats**   du concepteur EF.
- Les appels de procédure stockée sont mappés 1:1 avec les entités dans le modèle conceptuel. Par exemple, si vous implémentez une hiérarchie d’héritage dans votre modèle conceptuel, puis mappez les procédures stockées CUD pour les entités **parent** (base) et **enfant** (dérivées), l’enregistrement des modifications **enfants** appellera uniquement les procédures stockées de l' **enfant**, mais pas les appels des procédures stockées du **parent**.

## <a name="prerequisites"></a>Prérequis

Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :

- Une version récente de Visual Studio.
- [Exemple de base de données School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurer le projet

- Ouvrez Visual Studio 2012.
- Sélectionnez **fichier- &gt; nouveau- &gt; projet**
- Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .
- Entrez **CUDSProcsSample**   comme nom.
- Sélectionnez **OK**.

## <a name="create-a-model"></a>Création d'un modèle

- Dans Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **ajouter- &gt; nouvel élément**.
- Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.
- Entrez **CUDSProcs. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.
- Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant**.
- Cliquez sur **nouvelle connexion**. Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, **( \\ mssqllocaldb)**, sélectionnez la méthode d’authentification, tapez **School**   comme nom de la base de données, puis cliquez sur **OK**.
    La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.
- Dans la boîte de dialogue choisir vos objets de base de données, sous le nœud **tables**   , sélectionnez la table **Person** .
- En outre, sélectionnez les procédures stockées suivantes sous le nœud **procédures stockées et fonctions** : **DeletePerson**, **InsertPerson**et **UpdatePerson**.
- À compter de Visual Studio 2012, le concepteur EF prend en charge l’importation en bloc des procédures stockées. L' **importation des procédures stockées et des fonctions sélectionnées dans le modèle d’entité** est activée par défaut. Étant donné que dans cet exemple nous avons des procédures stockées qui insèrent, mettent à jour et suppriment des types d’entité, nous ne voulons pas les importer et décochent cette case.

    ![Importer des proc. S](~/ef6/media/importsprocs.jpg)

- Cliquez sur **Terminer**.
    Le concepteur EF, qui fournit une aire de conception pour la modification de votre modèle, est affiché.

## <a name="map-the-person-entity-to-stored-procedures"></a>Mapper l’entité Person aux procédures stockées

- Cliquez avec le bouton droit sur le type d’entité **Person**   et sélectionnez **mappage de procédure stockée**.
- Les mappages de procédure stockée s’affichent dans la fenêtre **Détails de mappage**   .
- Cliquez sur ** &lt; Sélectionner une &gt; fonction d’insertion**.
    Le champ devient une liste déroulante des procédures stockées dans le modèle de stockage qui peut être mappé aux types d'entité dans le modèle conceptuel.
    Sélectionnez **InsertPerson**   dans la liste déroulante.
- Les mappages par défaut entre les paramètres des procédures stockées et les propriétés d'entité apparaissent. Notez que les flèches indiquent le sens du mappage : les valeurs des propriétés sont fournies aux paramètres des procédures stockées.
- Cliquez sur ** &lt; Ajouter une &gt; liaison de résultat**.
- Tapez **NewPersonID**, le nom du paramètre retourné par la **InsertPerson**   procédure stockée InsertPerson. Veillez à ne pas taper les espaces de début ou de fin.
- Appuyez sur **entrée**.
- Par défaut, **NewPersonID**   est mappé à la clé d’entité **PersonID**. Notez qu'une flèche indique le sens du mappage : la valeur de la colonne de résultats est fournie à la propriété.

    ![Détails de mappage](~/ef6/media/mappingdetails.png)

- Cliquez sur ** &lt; Sélectionner une &gt; fonction de mise à jour**   et sélectionnez **UpdatePerson**   dans la liste déroulante résultante.
- Les mappages par défaut entre les paramètres des procédures stockées et les propriétés d'entité apparaissent.
- Cliquez sur ** &lt; Sélectionner la &gt; fonction de suppression**   , puis sélectionnez **DeletePerson**   dans la liste déroulante résultante.
- Les mappages par défaut entre les paramètres des procédures stockées et les propriétés d'entité apparaissent.

Les opérations d’insertion, de mise à jour et de suppression du type d’entité **Person**   sont maintenant mappées à des procédures stockées.

Si vous souhaitez activer le contrôle d’accès concurrentiel lors de la mise à jour ou de la suppression d’une entité avec des procédures stockées, utilisez l’une des options suivantes :

- Utilisez un paramètre de **sortie** pour retourner le nombre de lignes affectées à partir de la procédure stockée et cochez la case **lignes affectées du paramètre**en   regard du nom du paramètre. Si la valeur retournée est zéro lorsque l’opération est appelée, une  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)est   levée.
- Cochez la case **utiliser la valeur d’origine** à côté d’une propriété que vous souhaitez utiliser pour la vérification de l’accès concurrentiel. Lors d’une tentative de mise à jour, la valeur de la propriété qui a été lue à l’origine dans la base de données sera utilisée lors de l’écriture de données dans la base de données. Si la valeur ne correspond pas à la valeur de la base de données, une **OptimisticConcurrencyException**   est levée.

## <a name="use-the-model"></a>Utiliser le modèle

Ouvrez le fichier **Program.cs** dans lequel la méthode **main** est définie. Ajoutez le code suivant à la fonction main.

Le code crée un nouvel objet **Person** , puis met à jour l’objet, puis supprime l’objet.

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

- Compilez et exécutez l'application. Le programme produit la sortie suivante *

> [!NOTE]
> PersonID est généré automatiquement par le serveur. vous verrez probablement un autre numéro *

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

Si vous utilisez la version finale de Visual Studio, vous pouvez utiliser IntelliTrace avec le débogueur pour voir les instructions SQL qui sont exécutées.

![Déboguer avec IntelliTrace](~/ef6/media/intellitrace.png)
