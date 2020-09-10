---
title: Types complexes-concepteur EF-EF6
description: Types complexes-concepteur EF dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a8228ef-acfd-4575-860d-769d2c0e18a1
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: 87b62c119894486cbb54adfc8d3af6208a6618a8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620605"
---
# <a name="complex-types---ef-designer"></a>Types complexes-concepteur EF
Cette rubrique montre comment mapper des types complexes avec le Entity Framework Designer (concepteur EF) et comment interroger des entités qui contiennent des propriétés de type complexe.

L’illustration suivante montre les fenêtres principales qui sont utilisées lors de l’utilisation du concepteur EF.

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> Lorsque vous générez le modèle conceptuel, des avertissements sur les entités et les associations non mappées peuvent s'afficher dans la Liste d'erreurs. Vous pouvez ignorer ces avertissements, car une fois que vous avez choisi de générer la base de données à partir du modèle, les erreurs disparaissent.

## <a name="what-is-a-complex-type"></a>Qu’est-ce qu’un type complexe ?

Les types complexes sont les propriétés non scalaires des types d'entités qui permettent d'organiser les propriétés scalaires au sein des entités. À l'instar des entités, les types complexes regroupent des propriétés scalaires ou d'autres propriétés de type complexe.

Lorsque vous travaillez avec des objets qui représentent des types complexes, tenez compte des éléments suivants :

-   Les types complexes n’ont pas de clés et, par conséquent, ne peuvent pas exister indépendamment. Les types complexes peuvent uniquement exister en tant que propriétés de types d'entités ou d'autres types complexes.
-   Les types complexes ne peuvent pas participer aux associations et ne peuvent pas contenir de propriétés de navigation.
-   Les propriétés de type complexe ne peuvent pas avoir la **valeur null**. Une **exception InvalidOperationException **se produit lorsque **DbContext. SaveChanges**   est appelé et qu’un objet complexe null est rencontré. Les propriétés scalaires des objets complexes peuvent avoir la **valeur null**.
-   Les types complexes ne peuvent pas hériter d'autres types complexes.
-   Vous devez définir le type complexe en tant que **classe**. 
-   EF détecte les modifications apportées aux membres sur un objet de type complexe lorsque **DbContext. DetectChanges** est appelé. Entity Framework appelle **DetectChanges** automatiquement lorsque les membres suivants sont appelés : **DbSet. Find**, **DbSet. local**, **DbSet. Remove**, **DbSet. Add**, **DbSet. Attach**, **DbContext. SaveChanges**, **DbContext. GetValidationErrors**, **DbContext. Entry**, **DbChangeTracker.** Entries.

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a>Refactoriser les propriétés d’une entité dans un nouveau type complexe

Si vous disposez déjà d’une entité dans votre modèle conceptuel, vous souhaiterez peut-être Refactoriser certaines des propriétés dans une propriété de type complexe.

Sur l’aire du concepteur, sélectionnez une ou plusieurs propriétés (à l’exception des propriétés de navigation) d’une entité, puis cliquez avec le bouton droit et sélectionnez **Refactoriser- &gt; déplacer vers un nouveau type complexe**.

![Refactoriser dans un nouveau type complexe](~/ef6/media/refactor.png)

Un nouveau type complexe avec les propriétés sélectionnées est ajouté à l' **Explorateur de modèles**. Un nom par défaut est affecté au type complexe.

Une propriété complexe du type récemment créé remplace les propriétés sélectionnées. Tous les mappages de propriété sont conservés.

![Refactorisation dans un nouveau type complexe 2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a>Créer un type complexe

Vous pouvez également créer un type complexe qui ne contient pas les propriétés d’une entité existante.

Cliquez avec le bouton droit sur le dossier **types complexes** dans l’Explorateur de modèles, pointez sur **AddNew Complex Type...**. Vous pouvez également sélectionner le dossier **types complexes** et appuyer sur la touche **insertion**   de votre clavier.

![Ajouter un nouveau type complexe](~/ef6/media/addnewcomplextype.png)

Un nouveau type complexe est ajouté au dossier avec un nom par défaut. Vous pouvez maintenant ajouter des propriétés au type.

## <a name="add-properties-to-a-complex-type"></a>Ajouter des propriétés à un type complexe

Les propriétés d'un type complexe peuvent être des types scalaires ou des types complexes existants. Toutefois, les propriétés de type complexe ne peuvent pas avoir de références circulaires. Par exemple, un type complexe **OnsiteCourseDetails**   ne peut pas avoir une propriété de type complexe **OnsiteCourseDetails**.

Vous pouvez ajouter une propriété à un type complexe en appliquant l'une des méthodes répertoriées ci-dessous.

-   Cliquez avec le bouton droit sur un type complexe dans l’Explorateur de modèles, pointez sur **Ajouter**, sur **propriété scalaire**   ou sur **propriété complexe**, puis sélectionnez le type de propriété souhaité. Vous pouvez également sélectionner un type complexe, puis appuyer sur la touche **insertion**   de votre clavier.  

    ![Ajouter des propriétés à un type complexe](~/ef6/media/addpropertiestocomplextype.png)

    Une nouvelle propriété est ajoutée au type complexe avec un nom par défaut.

- - OU -

-   Cliquez avec le bouton droit sur une propriété d’entité sur l’aire du **Concepteur EF** , sélectionnez **copier**, cliquez avec le bouton droit sur le type complexe dans l' **Explorateur de modèles** , puis sélectionnez **coller**.

## <a name="rename-a-complex-type"></a>Renommer un type complexe

Lorsque vous renommez un type complexe, toutes les références au type sont mises à jour dans tout le projet.

-   Double-cliquez lentement sur un type complexe dans l' **Explorateur de modèles**.
    Le nom est sélectionné et passe en mode édition.

- - OU -

-   Cliquez avec le bouton droit sur un type complexe dans l' **Explorateur de modèles** et sélectionnez **Renommer**.

- - OU -

-   Sélectionnez un type complexe dans l'Explorateur de modèles, puis appuyez sur la touche F2.

- - OU -

-   Cliquez avec le bouton droit sur un type complexe dans l' **Explorateur de modèles** et sélectionnez **Propriétés**. Modifiez le nom dans la fenêtre **Propriétés**   .

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a>Ajouter un type complexe existant à une entité et mapper ses propriétés aux colonnes de la table

1.  Cliquez avec le bouton droit sur une entité, pointez sur **Ajouter nouveau**, puis sélectionnez **propriété complexe**.
    Une propriété de type complexe avec un nom par défaut est ajoutée à l'entité. Un type par défaut (sélectionné dans les types complexes existants) est affecté à la propriété.
2.  Assignez le type souhaité à la propriété dans la fenêtre **Propriétés**   .
    Après avoir ajouté une propriété de type complexe à une entité, vous devez mapper ses propriétés aux colonnes de la table.
3.  Cliquez avec le bouton droit sur un type d’entité sur l’aire de conception ou dans l' **Explorateur de modèles**   et sélectionnez **mappages de tables**.
    Les mappages de table s’affichent dans la fenêtre **Détails de mappage**   .
4.  Développez le nœud **mappages au &lt; nom &gt; de la table**   .
    Un nœud **mappage de colonnes**   s’affiche.
5.  Développez le nœud **mappages de colonnes**   .
    Une liste de toutes les colonnes de la table s'affiche. Les propriétés par défaut (le cas échéant) sur lesquelles le mappage de colonnes est répertorié sont répertoriées sous l’en-tête **valeur/propriété**   .
6.  Sélectionnez la colonne que vous souhaitez mapper, puis cliquez avec le bouton droit sur le champ **valeur/propriété**correspondant   .
    Une liste déroulante de toutes les propriétés scalaires s'affiche.
7.  Sélectionnez la propriété appropriée.

    ![Type de mappage complexe](~/ef6/media/mapcomplextype.png)

8.  Répétez les étapes 6 et 7 pour chaque colonne de la table.

>[!NOTE]
> Pour supprimer un mappage de colonne, sélectionnez la colonne que vous souhaitez mapper, puis cliquez sur le champ **valeur/propriété**   . Ensuite, sélectionnez **supprimer** dans la liste déroulante.

## <a name="map-a-function-import-to-a-complex-type"></a>Mapper une importation de fonction à un type complexe

Les importations de fonction sont basées sur les procédures stockées. Pour mapper une importation de fonction à un type complexe, les colonnes retournées par la procédure stockée correspondante doivent correspondre aux propriétés du type complexe en nombre et doivent avoir des types de stockage qui sont compatibles avec les types de propriété.

-   Double-cliquez sur une fonction importée que vous souhaitez mapper à un type complexe.

    ![Importations de fonctions](~/ef6/media/functionimports.png)

-   Définissez les paramètres de la nouvelle importation de fonction comme suit :
    -   Spécifiez la procédure stockée pour laquelle vous créez une importation de fonction dans le champ nom de la **procédure stockée**   . Ce champ est une liste déroulante qui affiche toutes les procédures stockées dans le modèle de stockage.
    -   Spécifiez le nom de l’importation de fonction dans le champ nom de l' **importation de fonction**   .
    -   Sélectionnez **complexe**   comme type de retour, puis spécifiez le type de retour complexe spécifique en choisissant le type approprié dans la liste déroulante.

        ![Modifier l’importation de fonction](~/ef6/media/editfunctionimport.png)

-   Cliquez sur  **OK**.
    L'entrée function import est créée dans le modèle conceptuel.

### <a name="customize-column-mapping-for-function-import"></a>Personnaliser le mappage de colonnes pour l’importation de fonction

-   Cliquez avec le bouton droit sur l’importation de fonction dans l’Explorateur de modèles et sélectionnez **mappage d’importation de fonction**.
    La fenêtre **Détails de mappage**   apparaît et affiche le mappage par défaut pour l’importation de fonction. Les flèches indiquent les mappages entre valeurs de colonne et valeurs de propriété. Par défaut, les noms de colonne sont supposés être les mêmes que ceux de la propriété du type complexe. Les noms de colonne par défaut s'affichent en gris.
-   Si nécessaire, changez les noms des colonnes pour qu'ils correspondent aux noms des colonnes retournés par la procédure stockée correspondant à l'importation de fonction.

## <a name="delete-a-complex-type"></a>Supprimer un type complexe

Lorsque vous supprimez un type complexe, le type est supprimé du modèle conceptuel, et les mappages pour toutes les instances du type sont supprimés. Toutefois, les références au type ne sont pas mises à jour. Par exemple, si une entité a une propriété de type complexe de type ComplexType1 et que ComplexType1 est supprimé dans l' **Explorateur de modèles**, la propriété d’entité correspondante n’est pas mise à jour. Le modèle ne sera pas validé, car il contient une entité qui référence un type complexe supprimé. Vous pouvez mettre à jour ou supprimer des références à des types complexes supprimés à l'aide du Concepteur d'entités.

-   Cliquez avec le bouton droit sur un type complexe dans l’Explorateur de modèles, puis sélectionnez **supprimer**.

- - OU -

-   Sélectionnez un type complexe dans l'Explorateur de modèles, puis appuyez sur la touche Suppr de votre clavier.

## <a name="query-for-entities-containing-properties-of-complex-type"></a>Requête pour les entités contenant des propriétés de type complexe

Le code suivant montre comment exécuter une requête qui retourne une collection d’objets de type d’entité qui contiennent une propriété de type complexe.

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```
