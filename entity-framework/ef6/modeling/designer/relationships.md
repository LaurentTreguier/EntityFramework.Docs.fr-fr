---
title: Relations-concepteur EF-EF6
description: Relations-concepteur EF dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: f7c801dd8f8ae81cfe44283b7575cfe869ce26d3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620422"
---
# <a name="relationships---ef-designer"></a>Relations-concepteur EF
> [!NOTE]
> Cette page fournit des informations sur la configuration des relations dans votre modèle à l’aide du concepteur EF. Pour obtenir des informations générales sur les relations dans EF et sur la manière d’accéder aux données et de les manipuler à l’aide de relations, consultez [relations & les propriétés de navigation](xref:ef6/fundamentals/relationships).

Les associations définissent les relations entre les types d’entité dans un modèle. Cette rubrique montre comment mapper des associations avec l’Entity Framework Designer (concepteur EF). L’illustration suivante montre les fenêtres principales qui sont utilisées lors de l’utilisation du concepteur EF.

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> Lorsque vous générez le modèle conceptuel, des avertissements sur les entités et les associations non mappées peuvent s'afficher dans la Liste d'erreurs. Vous pouvez ignorer ces avertissements, car une fois que vous avez choisi de générer la base de données à partir du modèle, les erreurs disparaissent.

## <a name="associations-overview"></a>Vue d’ensemble des associations

Lorsque vous concevez votre modèle à l’aide du concepteur EF, un fichier. edmx représente votre modèle. Dans le fichier. edmx, un élément **Association** définit une relation entre deux types d’entités. Une association doit spécifier les types d'entités impliqués dans la relation et le nombre possible de types d'entités à chaque terminaison de la relation, appelé « multiplicité ». La multiplicité d’une terminaison d’association peut avoir la valeur un (1), zéro ou un (0.. 1), ou plusieurs ( \* ). Ces informations sont spécifiées dans deux éléments **finaux** enfants.

Au moment de l’exécution, les instances de type d’entité à une terminaison d’une association sont accessibles via les propriétés de navigation ou les clés étrangères (si vous choisissez d’exposer des clés étrangères dans vos entités). Avec les clés étrangères exposées, la relation entre les entités est gérée à l’aide d’un élément **ReferentialConstraint** (élément enfant de l’élément **Association** ). Il est recommandé de toujours exposer les clés étrangères pour les relations dans vos entités.

> [!NOTE]
> Dans plusieurs-à-plusieurs ( \* : \* ), vous ne pouvez pas ajouter de clés étrangères aux entités. Dans une \* relation de : \* , les informations d’association sont gérées avec un objet indépendant.

Pour plus d’informations sur les éléments CSDL (**ReferentialConstraint**, **Association**, etc.), consultez la [spécification CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).

## <a name="create-and-delete-associations"></a>Créer et supprimer des associations

La création d’une association avec le concepteur EF met à jour le contenu du modèle du fichier. edmx. Après avoir créé une association, vous devez créer les mappages pour l’Association (abordé plus loin dans cette rubrique).

> [!NOTE]
> Cette section part du principe que vous avez déjà ajouté les entités pour lesquelles vous souhaitez créer une association entre et votre modèle.

### <a name="to-create-an-association"></a>Pour créer une association

1.  Cliquez avec le bouton droit sur une zone vide de l’aire de conception, pointez sur **Ajouter nouveau**, puis sélectionnez **Association...**.
2.  Renseignez les paramètres de l’Association dans la boîte de dialogue **Ajouter une association** .

    ![Ajouter une association](~/ef6/media/addassociation.png)

    > [!NOTE]
    > Vous pouvez choisir de ne pas ajouter de propriétés de navigation ou de clé étrangère aux entités situées aux terminaisons de l’Association en désactivant les cases à cocher de la **propriété de navigation **et **Ajouter les propriétés de clé étrangère aux cases d' &lt; &gt; entité nom du type d’entité **. Si vous ajoutez une seule propriété de navigation, l'association n'est parcourable que dans une seule direction. Si vous n'ajoutez pas de propriétés de navigation, vous devez ajouter des propriétés de clé étrangère pour accéder aux entités au niveau des terminaisons de l'association.
    
3.  Cliquez sur  **OK**.

### <a name="to-delete-an-association"></a>Pour supprimer une association

Pour supprimer une association, effectuez l’une des opérations suivantes :

-   Cliquez avec le bouton droit sur l’Association dans l’aire du concepteur EF, puis sélectionnez **supprimer**.

- - OU -

-   Sélectionnez une ou plusieurs associations et appuyez sur la touche SUPPR.

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a>Inclure des propriétés de clé étrangère dans vos entités (contraintes référentielles)

Il est recommandé de toujours exposer les clés étrangères pour les relations dans vos entités. Entity Framework utilise une contrainte référentielle pour identifier qu’une propriété joue le rôle de clé étrangère pour une relation.

Si vous avez activé la case à cocher ***Ajouter les propriétés de clé étrangère à la case d' &lt; &gt; entité nom du type d’entité*** lors de la création d’une relation, cette contrainte référentielle a été ajoutée pour vous.

Lorsque vous utilisez le concepteur EF pour ajouter ou modifier une contrainte référentielle, le concepteur EF ajoute ou modifie un élément **ReferentialConstraint**   dans le contenu CSDL du fichier. edmx.

-   Double-cliquez sur l'association à modifier.
    La boîte de dialogue **contrainte référentielle**   s’affiche.
-   Dans la **Principal**   liste déroulante principal, sélectionnez l’entité principale dans la contrainte référentielle.
    Les propriétés de clé de l’entité sont ajoutées **Principal Key**à la   liste de clés principales dans la boîte de dialogue.
-   Dans la liste déroulante **dépendante**   , sélectionnez l’entité dépendante dans la contrainte référentielle.
-   Pour chaque clé principale qui a une clé dépendante, sélectionnez une clé dépendante correspondante dans les listes déroulantes de la colonne **clé dépendante**   .

    ![Contrainte Ref](~/ef6/media/refconstraint.png)

-   Cliquez sur  **OK**.

## <a name="create-and-edit-association-mappings"></a>créer et modifier des mappages d'association

Vous pouvez spécifier la façon dont une association est mappée à la base de données dans la fenêtre **Détails de mappage**   du concepteur EF.

> [!NOTE]
> Vous ne pouvez mapper que les détails des associations pour lesquelles aucune contrainte référentielle n’est spécifiée. Si une contrainte référentielle est spécifiée, une propriété de clé étrangère est incluse dans l’entité et vous pouvez utiliser les détails de mappage de l’entité pour contrôler la colonne sur laquelle la clé étrangère est mappée.

### <a name="create-an-association-mapping"></a>Créer un mappage d’association

-   Cliquez avec le bouton droit sur une association dans l’aire de conception, puis sélectionnez **mappage de table**.
    Cela permet d’afficher le mappage d’association dans la fenêtre **Détails de mappage**   .
-   Cliquez sur **Ajouter une table ou une vue**.
    Une liste déroulante s'affiche. Elle contient toutes les tables du modèle de stockage.
-   Sélectionnez la table à laquelle l'association doit être mappée.
    La fenêtre **Détails de mapping**   affiche les deux terminaisons de l’Association et les propriétés de clé pour le type d’entité à chaque **extrémité**.
-   Pour chaque propriété de clé, cliquez sur le champ de **colonne**   , puis sélectionnez la colonne dans laquelle la propriété sera mappée.

    ![Détails du mappage 4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a>Modifier un mappage d’association

-   Cliquez avec le bouton droit sur une association dans l’aire de conception, puis sélectionnez **mappage de table**.
    Cela permet d’afficher le mappage d’association dans la fenêtre **Détails de mappage**   .
-   Cliquez sur **mappe au &lt; nom &gt; de la table**.
    Une liste déroulante s'affiche. Elle contient toutes les tables du modèle de stockage.
-   Sélectionnez la table à laquelle l'association doit être mappée.
    La fenêtre **Détails de mapping**   affiche les deux terminaisons de l’Association et les propriétés de clé pour le type d’entité à chaque extrémité.
-   Pour chaque propriété de clé, cliquez sur le champ de **colonne**   , puis sélectionnez la colonne dans laquelle la propriété sera mappée.

## <a name="edit-and-delete-navigation-properties"></a>Modifier et supprimer des propriétés de navigation

Les propriétés de navigation sont des propriétés de raccourci qui permettent de rechercher les entités situées aux terminaisons d’une association dans un modèle. Il est possible de créer des propriétés de navigation lorsque vous créez une association entre deux types d'entité.

#### <a name="to-edit-navigation-properties"></a>Pour modifier les propriétés de navigation

-   Sélectionnez une propriété de navigation sur l’aire du concepteur EF.
    Les informations relatives à la propriété de navigation s’affichent dans la fenêtre **Propriétés**de Visual Studio   .
-   Modifiez les paramètres de propriété dans la fenêtre **Propriétés**   .

#### <a name="to-delete-navigation-properties"></a>Pour supprimer des propriétés de navigation

-   Si les clés étrangères ne sont pas exposées sur les types d'entité dans le modèle conceptuel, le fait de supprimer une propriété de navigation peut rendre l'association correspondante parcourable dans une seule direction ou pas parcourable du tout.
-   Cliquez avec le bouton droit sur une propriété de navigation sur l’aire du concepteur EF, puis sélectionnez **supprimer**.
