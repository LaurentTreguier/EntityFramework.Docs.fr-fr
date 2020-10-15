---
title: Héritage TPT-EF6
description: Héritage de TPT dans le concepteur dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: f8e4198278fb44309916d57b44fb9af41d572108
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066172"
---
# <a name="designer-tpt-inheritance"></a>Héritage de TPT du concepteur
Cette procédure pas à pas montre comment implémenter l’héritage TPT (table par type) dans votre modèle à l’aide de l’Entity Framework Designer (concepteur EF). L'héritage TPT (table par type) utilise une table distincte de la base de données pour maintenir des données des propriétés non héritées et des propriétés de clé pour chaque type de la hiérarchie d'héritage.

Dans cette procédure pas à pas, nous allons mapper le **cours** (type de base), **OnlineCourse** (dérivé de course) et **OnsiteCourse**   (dérives de **course**) aux tables portant le même nom. Nous allons créer un modèle à partir de la base de données, puis modifier le modèle pour implémenter l’héritage TPT.

Vous pouvez également commencer par le Model First, puis générer la base de données à partir du modèle. Le concepteur EF utilise la stratégie TPT par défaut. par conséquent, tout héritage dans le modèle est mappé à des tables distinctes.

## <a name="other-inheritance-options"></a>Autres options d’héritage

TPH (table par hiérarchie) est un autre type d’héritage dans lequel une table de base de données est utilisée pour conserver les données de tous les types d’entité dans une hiérarchie d’héritage.Pour plus d’informations sur la façon de mapper l’héritage TPH (table par hiérarchie) avec le Entity Designer, consultez [EF designer TPH Inheritance](xref:ef6/modeling/designer/inheritance/tph). 

Notez que les modèles d’héritage de type table par béton (TPC) et d’héritage mixte sont pris en charge par le runtime Entity Framework, mais ne sont pas pris en charge par le concepteur EF. Si vous souhaitez utiliser l’héritage TPC ou mixte, deux options s’offrent à vous : Utilisez Code First ou modifiez manuellement le fichier EDMX. Si vous choisissez de travailler avec le fichier EDMX, la fenêtre Détails de mappage est placée en « mode sans échec » et vous ne pouvez pas utiliser le concepteur pour modifier les mappages.

## <a name="prerequisites"></a>Prérequis

Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :

- Une version récente de Visual Studio.
- [Exemple de base de données School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurer le projet

-   Ouvrez Visual Studio 2012.
-   Sélectionnez **fichier- &gt; nouveau- &gt; projet**
-   Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .
-   Entrez **TPTDBFirstSample**   comme nom.
-   Sélectionnez **OK**.

## <a name="create-a-model"></a>Création d'un modèle

-   Cliquez avec le bouton droit sur le projet dans Explorateur de solutions, puis sélectionnez **ajouter- &gt; nouvel élément**.
-   Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.
-   Entrez **TPTModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.
-   Dans la boîte de dialogue choisir le contenu du Model, sélectionnez**   générer à partir de la base de données**, puis cliquez sur **suivant**.
-   Cliquez sur **nouvelle connexion**.
    Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, **( \\ mssqllocaldb)**, sélectionnez la méthode d’authentification, tapez **School**   comme nom de la base de données, puis cliquez sur **OK**.
    La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.
-   Dans la boîte de dialogue choisir vos objets de base de données, sous le nœud tables, sélectionnez les tables **Department**, **course, OnlineCourse et OnsiteCourse** .
-   Cliquez sur **Terminer**.

Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché. Tous les objets que vous avez sélectionnés dans la boîte de dialogue choisir vos objets de base de données sont ajoutés au modèle.

## <a name="implement-table-per-type-inheritance"></a>Implémenter l’héritage TPT (table par type)

-   Dans l’aire de conception, cliquez avec le bouton droit sur le type d’entité **OnlineCourse** , puis sélectionnez **Propriétés**.
-   Dans la fenêtre **Propriétés** , définissez la propriété type de base sur **course**.
-   Cliquez avec le bouton droit sur le type d’entité **OnsiteCourse** , puis sélectionnez **Propriétés**.
-   Dans la fenêtre **Propriétés** , définissez la propriété type de base sur **course**.
-   Cliquez avec le bouton droit sur l’Association (la ligne) entre les types d’entités **OnlineCourse** et **course** .
    Sélectionnez **supprimer du modèle**.
-   Cliquez avec le bouton droit sur l’association entre les types d’entité **OnsiteCourse** et **course** .
    Sélectionnez **supprimer du modèle**.

Nous allons maintenant supprimer la propriété **CourseID** de **OnlineCourse** et **OnsiteCourse** , car ces classes héritent de **CourseID** du type de base **course** .

-   Cliquez avec le bouton droit sur la propriété **CourseID** du type d’entité **OnlineCourse** , puis sélectionnez **supprimer du modèle**.
-   Cliquez avec le bouton droit sur la propriété **CourseID** du type d’entité **OnsiteCourse** , puis sélectionnez **supprimer du modèle** .
-   L'héritage TPT (table par type) est maintenant implémenté.

![Table par type](~/ef6/media/tpt.png)

## <a name="use-the-model"></a>Utiliser le modèle

Ouvrez le fichier **Program.cs** dans lequel la méthode **main** est définie. Collez le code suivant dans la fonction **main** . Le code exécute trois requêtes. La première requête rétablit tous les **cours** liés au service spécifié. La deuxième requête utilise la méthode **OfType** pour retourner les **OnlineCourses** liés au service spécifié. La troisième requête retourne **OnsiteCourses**.

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
