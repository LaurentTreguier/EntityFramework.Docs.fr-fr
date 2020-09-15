---
title: Prise en charge d’enum-Code First-EF6
description: Prise en charge d’enum-Code First dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/enums
ms.openlocfilehash: e9e1412afb097cc4c944be63130dba0496a80f14
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073975"
---
# <a name="enum-support---code-first"></a>Prise en charge d’enum-Code First
> [!NOTE]
> **EF5 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 5. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

Cette vidéo et la procédure pas à pas expliquent comment utiliser les types ENUM avec Entity Framework Code First. Il montre également comment utiliser des enums dans une requête LINQ.

Cette procédure pas à pas utilise Code First pour créer une base de données, mais vous pouvez également utiliser des [Code First pour mapper à une base de données existante](xref:ef6/modeling/code-first/workflows/existing-database).

La prise en charge des énumérations a été introduite dans Entity Framework 5. Pour utiliser les nouvelles fonctionnalités telles que les énumérations, les types de données spatiales et les fonctions table, vous devez cibler .NET Framework 4,5. Visual Studio 2012 cible .NET 4,5 par défaut.

Dans Entity Framework, une énumération peut avoir les types sous-jacents suivants : **Byte**, **Int16**, **Int32**, **Int64** ou **SByte**.

## <a name="watch-the-video"></a>Regarder la vidéo
Cette vidéo montre comment utiliser les types ENUM avec Entity Framework Code First. Il montre également comment utiliser des enums dans une requête LINQ.

**Présenté par**: Julia Kornich

**Vidéo**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez avoir installé Visual Studio 2012, Ultimate, Premium, Professional ou Web Express Edition.

 

## <a name="set-up-the-project"></a>Configurer le projet

1.  Ouvrir Visual Studio 2012
2.  Dans le menu **fichier** , pointez sur **nouveau**, puis cliquez sur **projet** .
3.  Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .
4.  Entrez **EnumCodeFirst** comme nom du projet, puis cliquez sur **OK** .

## <a name="define-a-new-model-using-code-first"></a>Définir un nouveau modèle à l’aide de Code First

Lors de l’utilisation de Code First développement, vous commencez généralement par écrire des classes .NET Framework qui définissent votre modèle conceptuel (domaine). Le code ci-dessous définit la classe Department.

Le code définit également l’énumération DepartmentNames. Par défaut, l’énumération est de type **int** . La propriété Name de la classe Department est du type DepartmentNames.

Ouvrez le fichier Program.cs et collez les définitions de classe suivantes.

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
}
```
 

## <a name="define-the-dbcontext-derived-type"></a>Définir le type dérivé DbContext

En plus de définir des entités, vous devez définir une classe qui dérive de DbContext et expose les &lt; Propriétés de la tente DbSet &gt; . Les propriétés de la tente de DbSet &lt; &gt; permettent au contexte de savoir quels types vous souhaitez inclure dans le modèle.

Une instance du type dérivé DbContext gère les objets d’entité au moment de l’exécution, ce qui comprend le remplissage des objets avec les données d’une base de données, le suivi des modifications et la persistance des données dans la base de données.

Les types DbContext et DbSet sont définis dans l’assembly EntityFramework. Nous allons ajouter une référence à cette DLL à l’aide du package NuGet EntityFramework.

1.  Dans Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet.
2.  Sélectionnez **gérer les packages NuGet...**
3.  Dans la boîte de dialogue gérer les packages NuGet, sélectionnez l’onglet **en ligne** et choisissez le package **EntityFramework** .
4.  Cliquez sur **Install**.

Notez qu’en plus de l’assembly EntityFramework, les références aux assemblys System. ComponentModel. DataAnnotations et System. Data. Entity sont également ajoutées.

En haut du fichier Program.cs, ajoutez l’instruction using suivante :

``` csharp
using System.Data.Entity;
```

Dans Program.cs, ajoutez la définition de contexte. 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a>Conserver et récupérer des données

Ouvrez le fichier Program.cs dans lequel la méthode main est définie. Ajoutez le code suivant à la fonction main. Le code ajoute un nouvel objet Department au contexte. Il enregistre ensuite les données. Le code exécute également une requête LINQ qui retourne un service dont le nom est DepartmentNames. English.

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

Compilez et exécutez l'application. Le programme génère la sortie suivante :

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a>Afficher la base de données générée

Lorsque vous exécutez l’application la première fois, la Entity Framework crée une base de données pour vous. Étant donné que Visual Studio 2012 est installé, la base de données sera créée sur l’instance de base de données locale. Par défaut, le Entity Framework nomme la base de données après le nom qualifié complet du contexte dérivé (pour cet exemple, il s’agit de **EnumCodeFirst. EnumTestContext**). La prochaine fois que la base de données existante sera utilisée.  

Notez que si vous apportez des modifications à votre modèle après la création de la base de données, vous devez utiliser Migrations Code First pour mettre à jour le schéma de base de données. Pour obtenir un exemple d’utilisation de migrations, consultez [Code First à une nouvelle base de données](xref:ef6/modeling/code-first/workflows/new-database) .

Pour afficher la base de données et les données, procédez comme suit :

1.  Dans le menu principal de Visual Studio 2012, sélectionnez **Afficher**  - &gt; **Explorateur d’objets SQL Server**.
2.  Si la base de données locale ne figure pas dans la liste des serveurs, cliquez sur le bouton droit de la souris sur **SQL Server** et sélectionnez **ajouter SQL Server** utiliser l' **authentification Windows** par défaut pour vous connecter à l’instance de base de données locale.
3.  Développez le nœud de base de données locale
4.  Dérouler le dossier **bases de données** pour afficher la nouvelle base de données et accéder à la table **Department** , qui code First ne crée pas de table mappée au type d’énumération
5.  Pour afficher les données, cliquez avec le bouton droit sur la table et sélectionnez **afficher les données** .

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, nous avons vu comment utiliser les types ENUM avec Entity Framework Code First. 
