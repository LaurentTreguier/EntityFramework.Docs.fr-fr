---
title: Procédures stockées de requête du concepteur-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9554ed25-c5c1-43be-acad-5da37739697f
ms.openlocfilehash: 2e0092b526278597e8477d47eeb642598647bb91
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182483"
---
# <a name="designer-query-stored-procedures"></a>Procédures stockées de requête du concepteur
Cette procédure pas à pas explique comment utiliser le Entity Framework Designer (concepteur EF) pour importer des procédures stockées dans un modèle, puis appeler les procédures stockées importées pour récupérer les résultats. 

Notez que Code First ne prend pas en charge le mappage à des procédures stockées ou à des fonctions. Toutefois, vous pouvez appeler des procédures stockées ou des fonctions à l’aide de la méthode System. Data. Entity. DbSet. SqlQuery. Exemple :
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a>Configuration requise

Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :

- Une version récente de Visual Studio.
- [Exemple de base de données School](~/ef6/resources/school-database.md).

## <a name="set-up-the-project"></a>Configurer le projet

-   Ouvrez Visual Studio 2012.
-   Sélectionnez **fichier-&gt; nouveau-&gt; projet**
-   Dans le volet gauche, cliquez sur **Visual C\#** , puis sélectionnez le modèle **console** .
-   Entrez **EFwithSProcsSample** comme nom.
-   Sélectionnez **OK**.

## <a name="create-a-model"></a>Créer un modèle

-   Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter-&gt; nouvel élément**.
-   Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.
-   Entrez **EFwithSProcsModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.
-   Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant**.
-   Cliquez sur **nouvelle connexion**.  
    Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, (base de données locale **)\\mssqllocaldb**), sélectionnez la méthode d’authentification, tapez **School** pour le nom de la base de données, puis cliquez sur **OK**.  
    La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.
-   Dans la boîte de dialogue choisir vos objets de base de données, activez la case à cocher **Tables** pour sélectionner toutes les tables.  
    En outre, sélectionnez les procédures stockées suivantes sous le nœud **procédures stockées et fonctions** : **GetStudentGrades** et **GetDepartmentName**. 

    ![Import](~/ef6/media/import.jpg)

    *À compter de Visual Studio 2012, le concepteur EF prend en charge l’importation en bloc des procédures stockées. L' **importation des procédures stockées et des fonctions sélectionnées dans le modèle theentity** est activée par défaut.*
-   Cliquez sur **Terminer**.

Par défaut, la forme de résultat de chaque procédure stockée ou fonction importée qui retourne plusieurs colonnes devient automatiquement un nouveau type complexe. Dans cet exemple, nous voulons mapper les résultats de la fonction **GetStudentGrades** à l’entité **StudentGrade** et les résultats du **GetDepartmentName** à **None** (**None** est la valeur par défaut).

Pour qu’une importation de fonction retourne un type d’entité, les colonnes retournées par la procédure stockée correspondante doivent correspondre exactement aux propriétés scalaires du type d’entité retourné. Une importation de fonction peut également retourner des collections de types simples, des types complexes ou aucune valeur.

-   Cliquez avec le bouton droit sur l’aire de conception, puis sélectionnez **Explorateur de modèles**.
-   Dans l' **Explorateur de modèles**, sélectionnez importation de **fonction**, puis double-cliquez sur la fonction **GetStudentGrades** .
-   Dans la boîte de dialogue Modifier l’importation de fonction, sélectionnez **entités** , puis choisissez **StudentGrade**.  
    *La case **Importer une fonction peut être composable** en haut de la boîte de dialogue **importations** de fonctions, qui vous permet de mapper à des fonctions composables. Si vous activez cette case à cocher, seules les fonctions composables (fonctions table) s’affichent dans la liste déroulante nom de la **procédure stockée/fonction** . Si vous n’activez pas cette case à cocher, seules les fonctions non composables seront affichées dans la liste.*

## <a name="use-the-model"></a>Utiliser le modèle

Ouvrez le fichier **Program.cs** dans lequel la méthode **main** est définie. Ajoutez le code suivant à la fonction main.

Le code appelle deux procédures stockées : **GetStudentGrades** (retourne **StudentGrades** pour le *StudentID*spécifié) et **GetDepartmentName** (retourne le nom du département dans le paramètre de sortie).  

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        // Specify the Student ID.
        int studentId = 2;

        // Call GetStudentGrades and iterate through the returned collection.
        foreach (StudentGrade grade in context.GetStudentGrades(studentId))
        {
            Console.WriteLine("StudentID: {0}\tSubject={1}", studentId, grade.Subject);
            Console.WriteLine("Student grade: " + grade.Grade);
        }

        // Call GetDepartmentName.
        // Declare the name variable that will contain the value returned by the output parameter.
        ObjectParameter name = new ObjectParameter("Name", typeof(String));
        context.GetDepartmentName(1, name);
        Console.WriteLine("The department name is {0}", name.Value);

    }
```

Compilez et exécutez l'application. Le programme génère la sortie suivante :

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a>Paramètres de sortie
-----------------

Si des paramètres de sortie sont utilisés, leurs valeurs ne sont pas disponibles tant que les résultats n’ont pas été entièrement lus. Cela est dû au comportement sous-jacent de DbDataReader. pour plus d’informations, consultez [récupération de données à l’aide d’un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) .
