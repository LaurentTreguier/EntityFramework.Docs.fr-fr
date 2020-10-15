---
title: Spatial-Code First-EF6
description: Code First spatiale dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/spatial
ms.openlocfilehash: 6942829809e37b5d62f61488ff6a190c8a38febb
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065236"
---
# <a name="spatial---code-first"></a>Code First spatial
> [!NOTE]
> **EF5 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 5. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

La vidéo et la procédure pas à pas montrent comment mapper des types spatiaux avec Entity Framework Code First. Il montre également comment utiliser une requête LINQ pour rechercher une distance entre deux emplacements.

Cette procédure pas à pas utilise Code First pour créer une base de données, mais vous pouvez également utiliser des [Code First à une base de données existante](xref:ef6/modeling/code-first/workflows/existing-database).

La prise en charge des types spatiaux a été introduite dans Entity Framework 5. Notez que pour utiliser les nouvelles fonctionnalités telles que le type spatial, les énumérations et les fonctions table, vous devez cibler .NET Framework 4,5. Visual Studio 2012 cible .NET 4,5 par défaut.

Pour utiliser des types de données spatiales, vous devez également utiliser un fournisseur de Entity Framework qui a une prise en charge spatiale. Pour plus d’informations, consultez [prise en charge des fournisseurs pour les types spatiaux](xref:ef6/fundamentals/providers/spatial-support) .

Il existe deux types de données spatiales principales : Geography et Geometry. Le type de données geography stocke des données ellipsoïdal (par exemple, des coordonnées de latitude et de longitude GPS). Le type de données geometry représente le système de coordonnées euclidienne (Flat).

## <a name="watch-the-video"></a>Regarder la vidéo
Cette vidéo montre comment mapper des types spatiaux avec Entity Framework Code First. Il montre également comment utiliser une requête LINQ pour rechercher une distance entre deux emplacements.

**Présenté par**: Julia Kornich

**Vidéo**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez avoir installé Visual Studio 2012, Ultimate, Premium, Professional ou Web Express Edition.

## <a name="set-up-the-project"></a>Configurer le projet

1.  Ouvrir Visual Studio 2012
2.  Dans le menu **fichier** , pointez sur **nouveau**, puis cliquez sur **projet** .
3.  Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **console** .
4.  Entrez **SpatialCodeFirst** comme nom du projet, puis cliquez sur **OK** .

## <a name="define-a-new-model-using-code-first"></a>Définir un nouveau modèle à l’aide de Code First

Lors de l’utilisation de Code First développement, vous commencez généralement par écrire des classes .NET Framework qui définissent votre modèle conceptuel (domaine). Le code ci-dessous définit la classe University.

La propriété Location de l’Université est du type DbGeography. Pour utiliser le type DbGeography, vous devez ajouter une référence à l’assembly System. Data. Entity et ajouter également l’instruction using System. Data. spatial.

Ouvrez le fichier Program.cs et collez les instructions using suivantes en haut du fichier :

``` csharp
using System.Data.Spatial;
```

Ajoutez la définition de classe University suivante au fichier Program.cs.

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
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

Notez qu’en plus de l’assembly EntityFramework, une référence à l’assembly System. ComponentModel. DataAnnotations est également ajoutée.

En haut du fichier Program.cs, ajoutez l’instruction using suivante :

``` csharp
using System.Data.Entity;
```

Dans Program.cs, ajoutez la définition de contexte. 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a>Conserver et récupérer des données

Ouvrez le fichier Program.cs dans lequel la méthode main est définie. Ajoutez le code suivant à la fonction main.

Le code ajoute deux nouveaux objets University au contexte. Les propriétés spatiales sont initialisées à l’aide de la méthode DbGeography. FromText. Le point géographique représenté en tant que WellKnownText est passé à la méthode. Le code enregistre ensuite les données. Ensuite, la requête LINQ qui retourne un objet University dans lequel son emplacement est le plus proche de l’emplacement spécifié est construite et exécutée.

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
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

## <a name="view-the-generated-database"></a>Afficher la base de données générée

Lorsque vous exécutez l’application la première fois, la Entity Framework crée une base de données pour vous. Étant donné que Visual Studio 2012 est installé, la base de données sera créée sur l’instance de base de données locale. Par défaut, le Entity Framework nomme la base de données après le nom qualifié complet du contexte dérivé (dans cet exemple, il s’agit de **SpatialCodeFirst. UniversityContext**). La prochaine fois que la base de données existante sera utilisée.  

Notez que si vous apportez des modifications à votre modèle après la création de la base de données, vous devez utiliser Migrations Code First pour mettre à jour le schéma de base de données. Pour obtenir un exemple d’utilisation de migrations, consultez [Code First à une nouvelle base de données](xref:ef6/modeling/code-first/workflows/new-database) .

Pour afficher la base de données et les données, procédez comme suit :

1.  Dans le menu principal de Visual Studio 2012, sélectionnez **Afficher**  - &gt; **Explorateur d’objets SQL Server**.
2.  Si la base de données locale ne figure pas dans la liste des serveurs, cliquez sur le bouton droit de la souris sur **SQL Server** et sélectionnez **ajouter SQL Server** utiliser l' **authentification Windows** par défaut pour vous connecter à l’instance de base de données locale.
3.  Développez le nœud de base de données locale
4.  Dérouler le dossier **bases de données** pour afficher la nouvelle base de données et accéder à la table **universités**
5.  Pour afficher les données, cliquez avec le bouton droit sur la table et sélectionnez **afficher les données** .

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, nous avons vu comment utiliser des types spatiaux avec Entity Framework Code First. 
