---
title: Plusieurs diagrammes par modèle-EF6
description: Plusieurs diagrammes par modèle dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/multiple-diagrams
ms.openlocfilehash: 625cb932d23805f6bd22b658e4cddab1a487751c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066211"
---
# <a name="multiple-diagrams-per-model"></a>Plusieurs diagrammes par modèle
> [!NOTE]
> **EF5 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 5. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

Cette vidéo et cette page montrent comment fractionner un modèle en plusieurs diagrammes à l’aide du Entity Framework Designer (concepteur EF). Vous souhaiterez peut-être utiliser cette fonctionnalité lorsque votre modèle devient trop volumineux pour être affiché ou modifié.

Dans les versions antérieures du concepteur EF, vous ne pouviez avoir qu’un seul diagramme par fichier EDMX. À compter de Visual Studio 2012, vous pouvez utiliser le concepteur EF pour fractionner votre fichier EDMX en plusieurs diagrammes.

## <a name="watch-the-video"></a>Regarder la vidéo
Cette vidéo montre comment fractionner un modèle en plusieurs diagrammes à l’aide du Entity Framework Designer (concepteur EF). Vous souhaiterez peut-être utiliser cette fonctionnalité lorsque votre modèle devient trop volumineux pour être affiché ou modifié.

**Présenté par**: Julia Kornich

**Vidéo**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv)  |  [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)

## <a name="ef-designer-overview"></a>Vue d’ensemble du concepteur EF

Lorsque vous créez un modèle à l’aide de l’Assistant Entity Data Model du concepteur EF, un fichier. edmx est créé et ajouté à votre solution. Ce fichier définit la forme de vos entités et la façon dont elles sont mappées à la base de données.

Le concepteur EF est constitué des composants suivants :

-   Aire de conception visuelle pour la modification du modèle. Vous pouvez créer, modifier ou supprimer des entités et des associations.
-    **Model Browser**   Fenêtre de l’Explorateur de modèles qui fournit des arborescences du modèle.Les entités et leurs associations se trouvent sous le dossier * \[ modelname \] * . Les tables et contraintes de base de données se trouvent sous le * \[ modelname \] *. Dossier de stockage.
-   Fenêtre **Détails de mappage** pour afficher et modifier les mappages. Vous pouvez mapper des types d'entités ou des associations à des tables, colonnes ou procédures stockées de base de données. 

La fenêtre aire de conception visuelle s’ouvre automatiquement à la fin de l’Assistant Entity Data Model. Si l’Explorateur de modèles n’est pas visible, cliquez avec le bouton droit sur l’aire de conception principale et sélectionnez **Explorateur de modèles**.

La capture d’écran suivante montre un fichier. edmx ouvert dans le concepteur EF. La capture d’écran montre l’aire de conception visuelle (à gauche) et la fenêtre de l' **Explorateur de modèles**   (à droite).

![Concepteur EF 2](~/ef6/media/efdesigner2.png)

Pour annuler une opération effectuée dans le concepteur EF, cliquez sur Ctrl + Z.

## <a name="working-with-diagrams"></a>Utilisation des diagrammes

Par défaut, le concepteur EF crée un diagramme appelé Diagram1. Si vous avez un diagramme avec un grand nombre d’entités et d’associations, vous souhaiterez probablement les fractionner logiquement. À compter de Visual Studio 2012, vous pouvez afficher votre modèle conceptuel dans plusieurs diagrammes.   

Quand vous ajoutez de nouveaux diagrammes, ils s’affichent sous le dossier diagrammes dans la fenêtre Explorateur de modèles. Pour renommer un diagramme : sélectionnez le schéma dans la fenêtre Explorateur de modèles, cliquez une fois sur le nom, puis tapez le nouveau nom. Vous pouvez également cliquer avec le bouton droit sur le nom du diagramme et sélectionner **Renommer**.

Le nom du diagramme s’affiche à côté du nom du fichier. edmx dans l’éditeur Visual Studio. Par exemple, Model1. edmx \[ Diagram1 \] .

![Nom du diagramme](~/ef6/media/diagramname.png)

Le contenu des diagrammes (forme et couleur des entités et des associations) est stocké dans le fichier. edmx. Diagram. Pour afficher ce fichier, sélectionnez Explorateur de solutions et dérouler le fichier. edmx. 

![Fichiers de diagramme](~/ef6/media/diagramfiles.png)

Vous ne devez pas modifier manuellement le fichier. edmx. Diagram, le contenu de ce fichier peut être remplacé par le concepteur EF.
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a>Fractionnement des entités et des associations en un nouveau diagramme

Vous pouvez sélectionner des entités sur le diagramme existant (maintenez la touche Maj enfoncée pour sélectionner plusieurs entités). Cliquez sur le bouton droit de la souris et sélectionnez **déplacer vers un nouveau diagramme**. Le nouveau diagramme est créé et les entités sélectionnées et leurs associations sont déplacées vers le diagramme.

Vous pouvez également cliquer avec le bouton droit sur le dossier diagrammes dans l’Explorateur de modèles et sélectionner **Ajouter un nouveau diagramme.** Vous pouvez ensuite glisser-déplacer des entités à partir du dossier types d’entités dans l’Explorateur de modèles vers l’aire de conception.

Vous pouvez également couper ou copier des entités (à l’aide des touches Ctrl-X ou Ctrl-C) à partir d’un diagramme et coller (à l’aide de la touche Ctrl-V) sur l’autre. Si le diagramme dans lequel vous collez une entité contient déjà une entité portant le même nom, une nouvelle entité est créée et ajoutée au modèle.Par exemple : Diagram2 contient l’entité Department. Vous collez ensuite un autre service sur Diagram2. L’entité Department1 est créée et ajoutée au modèle conceptuel.   

Pour inclure des entités connexes dans un diagramme, Rick-cliquez sur l’entité et sélectionnez inclure les éléments **associés**. Cela fera une copie des entités et des associations associées dans le diagramme spécifié.

## <a name="changing-the-color-of-entities"></a>Modification de la couleur des entités

En plus de fractionner un modèle en plusieurs diagrammes, vous pouvez également modifier les couleurs de vos entités.

Pour modifier la couleur, sélectionnez une entité (ou plusieurs entités) sur l’aire de conception. Cliquez ensuite sur le bouton droit de la souris et sélectionnez **Propriétés**. Dans le Fenêtre Propriétés, sélectionnez la propriété **couleur de remplissage** . Spécifiez la couleur à l’aide d’un nom de couleur valide (par exemple, rouge) ou d’une valeur RVB valide (par exemple, 255, 128, 128). 

![Modifier la couleur](~/ef6/media/color.png)

## <a name="summary"></a>Résumé

Dans cette rubrique, nous avons vu comment fractionner un modèle en plusieurs diagrammes et comment spécifier une couleur différente pour une entité à l’aide de l’Entity Framework Designer. 
