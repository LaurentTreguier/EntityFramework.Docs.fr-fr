---
title: Raccourcis clavier Entity Framework Designer-EF6
description: Entity Framework Designer les raccourcis clavier dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: 90589419a52a198747eadee740775e2761bc757c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064885"
---
# <a name="entity-framework-designer-keyboard-shortcuts"></a>Raccourcis clavier Entity Framework Designer
Cette page fournit la liste des shorcuts de clavier disponibles dans les différents écrans de l’Entity Framework Tools pour Visual Studio.

## <a name="adonet-entity-data-model-wizard"></a>Assistant Entity Data Model ADO.NET

### <a name="step-one-choose-model-contents"></a>Étape 1 : choisir le contenu du modèle

![Assistant 1](~/ef6/media/wizardone.png)

| Raccourci  | Action                                                     | Notes                                               |
|:----------|:-----------------------------------------------------------|:----------------------------------------------------|
| **Alt + n** | Déplacer vers l’écran suivant                                        | Non disponible pour toutes les sélections du contenu du modèle. |
| **Alt + f** | Terminez l'Assistant                                              | Non disponible pour toutes les sélections du contenu du modèle. |
| **Alt + w** | Basculer le focus sur le « que doit contenir le modèle ? » . |                                                     |

### <a name="step-two-choose-your-connection"></a>Étape 2 : choisir votre connexion

![2 de l’Assistant](~/ef6/media/wizardtwo.png)

| Raccourci  | Action                                                     | Notes                                                   |
|:----------|:-----------------------------------------------------------|:--------------------------------------------------------|
| **Alt + n** | Déplacer vers l’écran suivant                                        |                                                         |
| **Alt + p** | Déplacer vers l’écran précédent                                    |                                                         |
| **Alt + w** | Basculer le focus sur le « que doit contenir le modèle ? » . |                                                         |
| **Alt + c** | Ouvrir la fenêtre « Propriétés de connexion »                    | Autorise la définition d’une nouvelle connexion de base de données. |
| **Alt + e** | Exclure les données sensibles de la chaîne de connexion          |                                                         |
| **Alt + i** | Inclure les données sensibles dans la chaîne de connexion            |                                                         |
| **Alt + s** | Activer/désactiver l’option « Enregistrer les paramètres de connexion en App.Config » |                                                         |

### <a name="step-three-choose-your-version"></a>Étape 3 : choisir votre version

![Assistant trois](~/ef6/media/wizardthree.png)

| Raccourci  | Action                                             | Notes                                                                                 |
|:----------|:---------------------------------------------------|:--------------------------------------------------------------------------------------|
| **Alt + n** | Déplacer vers l’écran suivant                                |                                                                                       |
| **Alt + p** | Déplacer vers l’écran précédent                            |                                                                                       |
| **Alt + w** | Basculer le focus sur la sélection de version Entity Framework | Permet de spécifier une version différente de Entity Framework pour une utilisation dans le projet. |

### <a name="step-four-choose-your-database-objects-and-settings"></a>Étape 4 : choisir vos objets et paramètres de base de données

![Assistant quatre](~/ef6/media/wizardfour.png)

| Raccourci  | Action                                                                                    | Notes                                                               |
|:----------|:------------------------------------------------------------------------------------------|:--------------------------------------------------------------------|
| **Alt + f** | Terminez l'Assistant                                                                             |                                                                     |
| **Alt + p** | Déplacer vers l’écran précédent                                                                   |                                                                     |
| **Alt + w** | Basculer le focus sur le volet de sélection de l’objet de base de données                                            | Permet de définir la rétroconception des objets de base de données.    |
| **Alt + s** | Activer ou désactiver l’option « pluralisme ou singulièrer les noms d’objets générés »                       |                                                                     |
| **Alt + k** | Activez ou désactivez l’option « inclure les colonnes clés étrangères dans le modèle »                              | Non disponible pour toutes les sélections du contenu du modèle.                 |
| **Alt + i** | Activez ou désactivez l’option « Importer les procédures stockées et les fonctions sélectionnées dans le modèle d’entité » | Non disponible pour toutes les sélections du contenu du modèle.                 |
| **Alt + m** | Active le champ de texte « espace de noms du modèle »                                        | Non disponible pour toutes les sélections du contenu du modèle.                 |
| **Espace** | Activer/désactiver la sélection sur l’élément                                                               | Si l’élément a des enfants, tous les éléments enfants sont également basculés |
| **Left**  | Réduire l’arborescence enfant                                                                       |                                                                     |
| **Right** | Développer l’arborescence enfant                                                                         |                                                                     |
| **Haut**    | Accéder à l’élément précédent dans l’arborescence                                                      |                                                                     |
| **Descendre**  | Naviguer vers l’élément suivant dans l’arborescence                                                          |                                                                     |

## <a name="ef-designer-surface"></a>Surface du concepteur EF

![Aire du concepteur](~/ef6/media/designersurface.png)

| Raccourci                                                                                | Action                      | Notes                                                                                                                                                                                                                               |
|:----------------------------------------------------------------------------------------|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Espace/Entrée**                                                                         | Activer/désactiver la sélection            | Active/désactive la sélection sur l’objet qui a le focus.                                                                                                                                                                                         |
| **Échap**                                                                                 | Annuler la sélection            | Annule la sélection actuelle.                                                                                                                                                                                                      |
| **Ctrl + A**                                                                            | Tout sélectionner                  | Sélectionne toutes les formes sur l’aire de conception.                                                                                                                                                                                       |
| **Flèche haut**                                                                            | Monter                     | Déplace l’entité sélectionnée d’un incrément de grille vers le haut. <br/> Si, dans une liste, se déplace vers le sous-champ frère précédent.                                                                                                                            |
| **Flèche bas**                                                                          | Descendre                   | Déplace l’entité sélectionnée d’un incrément de grille vers le dessous. <br/> Dans une liste, se déplace vers le sous-champ frère suivant.                                                                                                                              |
| **Flèche gauche**                                                                          | Déplacer vers la gauche                   | Déplace l’entité sélectionnée d’un incrément de grille vers la gauche. <br/> Si, dans une liste, se déplace vers le sous-champ frère précédent.                                                                                                                          |
| **Flèche droite**                                                                         | Déplacer vers la droite                  | Déplace l’entité sélectionnée d’un incrément de grille vers la droite. <br/> Dans une liste, se déplace vers le sous-champ frère suivant.                                                                                                                             |
| **Maj + Flèche gauche**                                                                  | Forme taille gauche             | Réduit la largeur de l’entité sélectionnée d’un incrément de grille.                                                                                                                                                                     |
| **Maj + Flèche droite**                                                                 | Ajuster la forme à droite            | Augmente la largeur de l’entité sélectionnée d’un incrément de grille.                                                                                                                                                                   |
| **Accueil**                                                                                | Premier homologue                  | Déplace le focus et la sélection vers le premier objet sur l’aire de conception au même niveau d’homologue.                                                                                                                                         |
| **Effet**                                                                                 | Dernier homologue                   | Déplace le focus et la sélection vers le dernier objet sur l’aire de conception au même niveau d’homologue.                                                                                                                                          |
| **Ctrl + Début**                                                                         | Premier homologue (Focus)          | Identique au premier homologue, mais déplace le focus au lieu de déplacer le focus et la sélection.                                                                                                                                                          |
| **Ctrl + Fin**                                                                          | Dernier homologue (Focus)           | Identique au dernier homologue, mais déplace le focus au lieu de déplacer le focus et la sélection.                                                                                                                                                           |
| **Onglet**                                                                                 | Homologue suivant                   | Déplace le focus et la sélection vers l’objet suivant sur l’aire de conception au même niveau d’homologue.                                                                                                                                          |
| **Maj + Tab**                                                                           | Homologue précédent               | Déplace le focus et la sélection vers l’objet précédent sur l’aire de conception au même niveau d’homologue.                                                                                                                                      |
| **Alt + Ctrl + Tab**                                                                        | Homologue suivant (Focus)           | Identique à l’homologue suivant, mais déplace le focus au lieu de déplacer le focus et la sélection.                                                                                                                                                           |
| **Alt + Ctrl + Maj + Tab**                                                                  | Homologue précédent (Focus)       | Identique à l’homologue précédent, mais déplace le focus au lieu de déplacer le focus et la sélection.                                                                                                                                                       |
| **&lt;**                                                                                | Z                      | Passe à l’objet suivant sur l’aire de conception, un niveau plus élevé dans la hiérarchie. S’il n’y a aucune forme au-dessus de cette forme dans la hiérarchie (autrement dit, l’objet est placé directement sur l’aire de conception), le diagramme est sélectionné. |
| **&gt;**                                                                                | Descendent                     | Passe à l’objet contenu suivant dans l’aire de conception un niveau inférieur à celui-ci dans la hiérarchie. S’il n’y a pas d’objet contenu, il s’agit d’une absence d’opération.                                                                              |
| **Ctrl + &lt;**                                                                         | Ascend (Focus)              | Identique à la commande Ascend, mais déplace le focus sans sélection.                                                                                                                                                                          |
| **Ctrl + &gt;**                                                                         | Descendant (Focus)             | Identique à la commande descendante, mais déplace le focus sans sélection.                                                                                                                                                                         |
| **Maj + fin**                                                                         | Suivre la connexion         | À partir d’une entité, se déplace vers une entité à laquelle cette entité est connectée.                                                                                                                                                               |
| **Suppr**                                                                                 | Supprimer                      | Supprimer un objet ou un connecteur du diagramme.                                                                                                                                                                                     |
| **Ceux**                                                                                 | Insérer                      | Ajoute une nouvelle propriété à une entité lorsque l’en-tête de compartiment « propriétés scalaires » ou une propriété elle-même est sélectionné.                                                                                                           |
| **PG. préc**                                                                               | Faire défiler le diagramme vers le haut           | Fait défiler l’aire de conception vers le haut, en incréments égal à 75% de la hauteur de l’aire de conception actuellement visible.                                                                                                                    |
| **PG. suiv**                                                                             | Faire défiler le diagramme vers le dessous         | Fait défiler l’aire de conception vers le dessous.                                                                                                                                                                                                    |
| **Maj + Pg. suiv**                                                                     | Faire défiler le diagramme vers la droite        | Fait défiler l’aire de conception vers la droite.                                                                                                                                                                                            |
| **Maj + Pg. préc**                                                                       | Faire défiler le diagramme vers la gauche         | Fait défiler l’aire de conception vers la gauche.                                                                                                                                                                                             |
| **F2**                                                                                  | Passer au mode Édition             | Raccourci clavier standard pour l’entrée en mode édition d’un contrôle de texte.                                                                                                                                                               |
| **Maj+F10**                                                                         | Afficher le menu contextuel       | Raccourci clavier standard permettant d’afficher le menu contextuel d’un élément sélectionné.                                                                                                                                                          |
| **Ctrl + Maj + clic gauche de la souris**  <br/> **Ctrl + Maj + MouseWheel vers l’avant**  | Zoom sémantique en cours            | Effectue un zoom avant sur la zone de la vue de diagramme sous le pointeur de la souris.                                                                                                                                                                 |
| **Ctrl + Maj + clic droit de souris** <br/> **Ctrl + Maj + MouseWheel vers l’arrière** | Zoom sémantique           | Effectue un zoom arrière à partir de la zone de la vue de diagramme sous le pointeur de la souris. Il recentre le diagramme lorsque vous faites un zoom arrière trop loin pour le centre de diagrammes actuel.                                                                          |
| **Ctrl + Maj + ' + '** <br/> **Ctrl + MouseWheel vers l’avant**                        | Zoom avant                     | Effectue un zoom avant sur le centre de la vue de diagramme.                                                                                                                                                                                         |
| **Ctrl + Maj + « - »** <br/> **Contrôle + MouseWheel en arrière**                       | Zoom arrière                    | Effectue un zoom arrière à partir de la zone sur laquelle l’utilisateur clique dans la vue de diagramme. Il recentre le diagramme lorsque vous faites un zoom arrière trop loin pour le centre de diagrammes actuel.                                                                                            |
| **Ctrl + Maj + dessin d’un rectangle avec le bouton gauche de la souris enfoncé**                  | Zone de zoom                   | Effectue un zoom avant sur la zone que vous avez sélectionnée. Quand vous maintenez enfoncées les touches Ctrl + Maj, vous voyez que le curseur se transforme en loupe, ce qui vous permet de définir la zone de zoom.                        |
| **Touche de menu contextuel + 'm'**                                                              | Ouvrir la fenêtre Détails de mappage | Ouvre la fenêtre Détails de mappage pour modifier les mappages de l’entité sélectionnée                                                                                                                                                               |

## <a name="mapping-details-window"></a>Fenêtre Détails de mappage

![Raccourcis des détails de mappage](~/ef6/media/mappingdetailsshortcuts.png)

| Raccourci                  | Action         | Notes                                                                                                                                 |
|:--------------------------|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| **Onglet**                   | Changer de contexte | Bascule entre la zone de la fenêtre principale et la barre d’outils à gauche                                                                     |
| **Touches de direction**            | Navigation     | Déplacer les lignes vers le haut et vers le haut, ou vers la droite et la gauche sur les colonnes de la zone de la fenêtre principale. Vous déplacer entre les boutons de la barre d’outils à gauche. |
| **Entrez** <br/> **Espace** | Sélectionnez         | Sélectionne un bouton dans la barre d’outils à gauche.                                                                                          |
| **Alt + flèche bas**      | Ouvrir la liste      | Dérouler une liste si une cellule est sélectionnée et contient une liste déroulante.                                                                     |
| **Entrez**                 | Liste SELECT    | Sélectionne un élément dans une liste déroulante.                                                                                               |
| **Échap**                   | Fermer la liste     | Ferme une liste déroulante.                                                                                                              |

## <a name="visual-studio-navigation"></a>Navigation dans Visual Studio

Entity Framework fournit également plusieurs actions qui peuvent avoir des raccourcis clavier personnalisés mappés (aucun raccourci n’est mappé par défaut). Pour créer ces raccourcis personnalisés, cliquez sur le menu Outils, puis sur options.Sous environnement, choisissez clavier.Faites défiler la liste vers le milieu jusqu’à ce que vous puissiez sélectionner la commande souhaitée, entrez le raccourci dans la zone de texte « appuyer sur les touches de raccourci », puis cliquez sur attribuer. Les raccourcis possibles sont les suivants :

| Raccourci                                                                                       |
|:-----------------------------------------------------------------------------------------------|
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Add. ComplexProperty. ComplexTypes**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddCodeGenerationItem**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddFunctionImport**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. AddEnumType**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. Association**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. ComplexProperty**                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. ComplexType**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. Entity**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. FunctionImport**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. Inheritance**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. NavigationProperty**               |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. ScalarProperty**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNewDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddtoDiagram**                            |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Close**                                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Collapse**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ConverttoEnum**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Diagram. réduire tout**                     |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Diagram. développer tout**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Diagram. ExportasImage**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Diagram. LayoutDiagram**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Edit**                                    |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. EntityKey**                               |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Expand**                                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. FunctionImportMapping**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GenerateDatabasefromModel**               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GoToDefinition**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Grid. ShowGrid**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Grid. SnaptoGrid**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.IncludeRelated**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MappingDetails**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ModelBrowser**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveDiagramstoSeparateFile**              |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down**                     |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down5**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToBottom**                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToTop**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up5**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MovetonewDiagram**                        |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Open**                                    |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. refactor. MovetoNewComplexType**           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Refactor.Rename**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.RemovefromDiagram**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Rename**                                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ScalarPropertyFormat. DisplayName**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ScalarPropertyFormat.DisplayNameandType** |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. BaseType**                         |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. Entity**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. Property**                         |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. SubType**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. SelectAll**                               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.SelectAssociation**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinModelBrowser**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.StoredProcedureMapping**                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. TableMapping**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.UpdateModelfromDatabase**                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Validate**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 10**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 100**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 125**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 150**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 200**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 25**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 300**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 33**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 400**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 50**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 66**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. 75**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. Custom**                             |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. Zoom**                             |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. ZoomOut**                            |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Zoom. ZoomtoFit**                          |
| **View.EntityDataModelBrowser**                                                                |
| **View.EntityDataModelMappingDetails**                                                         |
