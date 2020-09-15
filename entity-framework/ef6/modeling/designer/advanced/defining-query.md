---
title: Définition de Query-EF designer-EF6
description: Définition de la requête-concepteur EF dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: b5d24b9f6c047eb8681a0343c768d1ffa70ca6b8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070339"
---
# <a name="defining-query---ef-designer"></a>Définition de la requête-concepteur EF
Cette procédure pas à pas montre comment ajouter une requête de définition et un type d’entité correspondant à un modèle à l’aide du concepteur EF. Une requête de définition est couramment utilisée pour fournir des fonctionnalités semblables à celles fournies par une vue de base de données, mais la vue est définie dans le modèle, et non dans la base de données. Une requête de définition vous permet d’exécuter une instruction SQL qui est spécifiée dans l’élément **DefiningQuery**   d’un fichier. edmx. Pour plus d’informations, consultez **DefiningQuery** dans la [spécification SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).

Lorsque vous utilisez la définition de requêtes, vous devez également définir un type d’entité dans votre modèle. Le type d’entité est utilisé pour exposer les données exposées par la requête de définition. Notez que les données exposées par le biais de ce type d’entité sont en lecture seule.

Les requêtes paramétrées ne peuvent pas être exécutées en tant que requêtes de définition. Toutefois, les données peuvent être mises à jour en mappant les fonctions d'insertion, de mise à jour et de suppression du type d'entité qui surface les données aux procédures stockées. Pour plus d’informations, consultez [Insert, Update et Delete with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/cud).

Cette rubrique montre comment effectuer les tâches suivantes.

-   Ajouter une requête de définition
-   Ajouter un type d’entité au modèle
-   Mapper la requête de définition au type d’entité

## <a name="prerequisites"></a>Prérequis

Pour exécuter cette procédure pas à pas, vous avez besoin des éléments suivants :

- Une version récente de Visual Studio.
- [Exemple de base de données School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurer le projet

Cette procédure pas à pas utilise Visual Studio 2012 ou une version plus récente.

-   Ouvrez Visual Studio.
-   Dans le menu **Fichier** , pointez sur **Nouveau**, puis cliquez sur **Projet**.
-   Dans le volet gauche, cliquez sur **Visual \# C**, puis sélectionnez le modèle **application console** .
-   Entrez **DefiningQuerySample** comme nom du projet, puis cliquez sur **OK**.

 

## <a name="create-a-model-based-on-the-school-database"></a>Créer un modèle basé sur la base de données School

-   Cliquez avec le bouton droit sur le nom du projet dans Explorateur de solutions, pointez sur **Ajouter**, puis cliquez sur **nouvel élément**.
-   Sélectionnez **données** dans le menu de gauche, puis sélectionnez **ADO.NET Entity Data Model** dans le volet modèles.
-   Entrez **DefiningQueryModel. edmx** comme nom de fichier, puis cliquez sur **Ajouter**.
-   Dans la boîte de dialogue choisir le contenu du Model, sélectionnez **générer à partir de la base de données**, puis cliquez sur **suivant**.
-   Cliquez sur nouvelle connexion. Dans la boîte de dialogue Propriétés de connexion, entrez le nom du serveur (par exemple, **( \\ mssqllocaldb)**, sélectionnez la méthode d’authentification, tapez **School**   comme nom de la base de données, puis cliquez sur **OK**.
    La boîte de dialogue choisir votre connexion de données est mise à jour avec votre paramètre de connexion à la base de données.
-   Dans la boîte de dialogue choisir vos objets de base de données, vérifiez le nœud **tables**   . Cette opération ajoute toutes les tables au modèle **School** .
-   Cliquez sur **Terminer**.
-   Dans Explorateur de solutions, cliquez avec le bouton droit sur le fichier **DefiningQueryModel. edmx** , puis sélectionnez **Ouvrir avec...**.
-   Sélectionnez **éditeur XML (texte)**.

    ![Éditeur XML](~/ef6/media/xmleditor.png)

-   Cliquez sur **Oui** si le message suivant s’affiche :

    ![AVERTISSEMENT 2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a>Ajouter une requête de définition

Dans cette étape, nous allons utiliser l’éditeur XML pour ajouter une requête de définition et un type d’entité à la section SSDL du fichier. edmx. 

-   Ajoutez un élément **EntitySet**   à la section SSDL du fichier. edmx (ligne 5 à 13). Spécifiez les éléments suivants :
    -   Seuls les attributs **Name**   et **EntityType**   de l’élément **EntitySet**   sont spécifiés.
    -   Le nom qualifié complet du type d’entité est utilisé dans l’attribut **EntityType**   .
    -   L’instruction SQL à exécuter est spécifiée dans l’élément **DefiningQuery**   .

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   Ajoutez l’élément **EntityType** à la section SSDL du fichier. edmx. fichier comme indiqué ci-dessous. Notez les points suivants :
    -   La valeur de l’attribut **Name** correspond à la valeur de l’attribut **EntityType** dans l’élément **EntitySet** ci-dessus, même si le nom complet du type d’entité est utilisé dans l’attribut **EntityType** .
    -   Les noms de propriété correspondent aux noms de colonnes retournés par l’instruction SQL dans l’élément **DefiningQuery** (ci-dessus).
    -   Dans cet exemple, la clé d'entité est composée de trois propriétés pour garantir le caractère unique de la valeur de la clé.

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> Si vous exécutez ultérieurement la boîte de dialogue de l' **Assistant Mise à jour du modèle** , toutes les modifications apportées au modèle de stockage, y compris la définition des requêtes, seront remplacées.

 

## <a name="add-an-entity-type-to-the-model"></a>Ajouter un type d’entité au modèle

Dans cette étape, nous allons ajouter le type d’entité au modèle conceptuel à l’aide du concepteur EF. Notez les points suivants :

-   Le **nom** de l’entité correspond à la valeur de l’attribut **EntityType** dans l’élément **EntitySet** ci-dessus.
-   Les noms de propriété correspondent aux noms de colonnes retournés par l’instruction SQL dans l’élément **DefiningQuery** ci-dessus.
-   Dans cet exemple, la clé d'entité est composée de trois propriétés pour garantir le caractère unique de la valeur de la clé.

Ouvrez le modèle dans le concepteur EF.

-   Double-cliquez sur DefiningQueryModel. edmx.
-   Disons **Oui** au message suivant :

    ![AVERTISSEMENT 2](~/ef6/media/warning2.png)

 

Le Entity Designer, qui fournit une aire de conception pour la modification de votre modèle, est affiché.

-   Cliquez avec le bouton droit sur l’aire du concepteur et sélectionnez **Ajouter une nouvelle** - &gt; **entité.**...
-   Spécifiez **GradeReport** pour le nom d’entité et le **CourseID** pour la **propriété de clé**.
-   Cliquez avec le bouton droit sur l’entité **GradeReport** et sélectionnez **Ajouter une nouvelle** - &gt; **propriété scalaire**.
-   Remplacez le nom par défaut de la propriété par **FirstName**.
-   Ajoutez une autre propriété scalaire et spécifiez **LastName** comme nom.
-   Ajoutez une autre propriété scalaire et spécifiez le nom de la **classe** .
-   Dans la fenêtre **Propriétés** , affectez à la propriété **type** de la **classe**la valeur **décimale**.
-   Sélectionnez les propriétés **FirstName** et **LastName** .
-   Dans la fenêtre **Propriétés** , remplacez la valeur de la propriété **EntityKey** par **true**.

Par conséquent, les éléments suivants ont été ajoutés à la section **CSDL** du fichier. edmx.

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a>Mapper la requête de définition au type d’entité

Dans cette étape, nous allons utiliser la fenêtre Détails de mappage pour mapper les types d’entité conceptuels et de stockage.

-   Cliquez avec le bouton droit sur l’entité **GradeReport** sur l’aire de conception, puis sélectionnez **mappage de table**.  
    La fenêtre **Détails de mappage** s’affiche.
-   Sélectionnez **GradeReport** dans la liste déroulante ** &lt; ajouter &gt; une table ou une vue** (située sous la **table**s).  
    Les mappages par défaut entre le type d’entité conceptuel et stockage **GradeReport** s’affichent.  
    ![Mappage de Details3](~/ef6/media/mappingdetails.png)

Par conséquent, l’élément **EntitySetMapping**   est ajouté à la section Mapping du fichier. edmx. 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   Compilez l’application.

 

## <a name="call-the-defining-query-in-your-code"></a>Appeler la requête de définition dans votre code

Vous pouvez maintenant exécuter la requête de définition à l’aide du type d’entité **GradeReport** . 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
