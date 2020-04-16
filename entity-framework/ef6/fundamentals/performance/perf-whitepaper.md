---
title: Considérations de performance pour EF4, EF5 et EF6 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 0ece383bb5083b41c7a2636c009473333af6d3e2
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434337"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>Considérations de performance pour EF 4, 5 et 6
Par David Obando, Eric Dettinger et d’autres

Publié: Avril 2012

Dernière mise à jour : mai 2014

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. Introduction

Les cadres de cartographie relationnelle objet sont un moyen pratique de fournir une abstraction pour l’accès aux données dans une application orientée objet. Pour les applications .NET, microsoft recommandé O / RM est Entity Framework. Avec n’importe quelle abstraction cependant, la performance peut devenir une préoccupation.

Ce livre blanc a été écrit pour montrer les considérations de performance lors de l’élaboration d’applications utilisant Entity Framework, pour donner aux développeurs une idée des algorithmes internes du Cadre d’entité qui peuvent affecter les performances, et pour fournir des conseils pour l’enquête et l’amélioration des performances dans leurs applications qui utilisent Entity Framework. Il ya un certain nombre de bons sujets sur les performances déjà disponibles sur le web, et nous avons également essayé de pointer vers ces ressources dans la mesure du possible.

La performance est un sujet délicat. Ce livre blanc est conçu comme une ressource pour vous aider à prendre des décisions liées au rendement pour vos applications qui utilisent le cadre d’entité. Nous avons inclus quelques mesures de test pour démontrer les performances, mais ces mesures ne sont pas destinées à être des indicateurs absolus de la performance que vous verrez dans votre application.

À des fins pratiques, ce document suppose que le cadre d’entité 4 est exécuté sous .NET 4.0 et que le cadre d’entité 5 et 6 sont exécutés sous .NET 4.5. Bon nombre des améliorations apportées au rendement du cadre d’entité 5 se situent dans les composants de base qui expédient avec .NET 4.5.

Entity Framework 6 est une version hors bande et ne dépend pas des composants du Cadre d’entité qui expédient avec .NET. Entity Framework 6 travaille sur .NET 4.0 et .NET 4.5, et peut offrir un grand avantage de performance à ceux qui n’ont pas mis à niveau de .NET 4.0 mais veulent les derniers bits cadre d’entité dans leur application. Lorsque ce document mentionne Entity Framework 6, il fait référence à la dernière version disponible au moment de la rédaction : version 6.1.0.

## <a name="2-cold-vs-warm-query-execution"></a>2. Exécution froide vs requête chaude

La toute première fois qu’une requête est faite contre un modèle donné, le cadre d’entité fait beaucoup de travail dans les coulisses pour charger et valider le modèle. Nous nous référons souvent à cette première requête comme une requête « froide ».D’autres requêtes contre un modèle déjà chargé sont connues sous le nom de requêtes « chaudes », et sont beaucoup plus rapides.

Prenons une vue de haut niveau de l’endroit où le temps est passé lors de l’exécution d’une requête à l’aide du cadre d’entité, et voir où les choses s’améliorent dans le cadre de l’entité 6.

**Première exécution de requête - requête froide**

| Code Utilisateur écrit                                                                                     | Action                    | EF4 Performance Impact                                                                                                                                                                                                                                                                                                                                                                                                        | EF5 Performance Impact                                                                                                                                                                                                                                                                                                                                                                                                                                                    | EF6 Performance Impact                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Création contextuelle          | Moyenne                                                                                                                                                                                                                                                                                                                                                                                                                        | Moyenne                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Création d’expression de requête | Faible                                                                                                                                                                                                                                                                                                                                                                                                                           | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | Exécution de requêtes LINQ      | - Chargement des métadonnées : élevé mais mis en cache <br/> - Génération de vision : Potentiellement très élevée mais mise en cache <br/> - Évaluation des paramètres : Moyen <br/> - Traduction de requête : Moyen <br/> - Génération de matérialisation : moyenne mais mise en cache <br/> - Exécution des requêtes de base de données : potentiellement élevée <br/> Connexion.Open <br/> Command.ExecuteReader <br/> DataReader.Read <br/> Matérialisation d’objet : Moyenne <br/> - Recherche d’identité : Moyen | - Chargement des métadonnées : élevé mais mis en cache <br/> - Génération de vision : Potentiellement très élevée mais mise en cache <br/> - Évaluation des paramètres : Faible <br/> - Traduction de requête : moyen mais mis en cache <br/> - Génération de matérialisation : moyenne mais mise en cache <br/> - Exécution des requêtes de base de données : Potentiellement élevée (meilleures requêtes dans certaines situations) <br/> Connexion.Open <br/> Command.ExecuteReader <br/> DataReader.Read <br/> Matérialisation d’objet : Moyenne <br/> - Recherche d’identité : Moyen | - Chargement des métadonnées : élevé mais mis en cache <br/> - Génération de vue : moyenne mais mise en cache <br/> - Évaluation des paramètres : Faible <br/> - Traduction de requête : moyen mais mis en cache <br/> - Génération de matérialisation : moyenne mais mise en cache <br/> - Exécution des requêtes de base de données : Potentiellement élevée (meilleures requêtes dans certaines situations) <br/> Connexion.Open <br/> Command.ExecuteReader <br/> DataReader.Read <br/> Matérialisation de l’objet : moyenne (plus rapide que EF5) <br/> - Recherche d’identité : Moyen |
| `}`                                                                                                  | Connection.Close (close)          | Faible                                                                                                                                                                                                                                                                                                                                                                                                                           | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**Deuxième exécution de requête - requête chaude**

| Code Utilisateur écrit                                                                                     | Action                    | EF4 Performance Impact                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | EF5 Performance Impact                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | EF6 Performance Impact                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Création contextuelle          | Moyenne                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Moyenne                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Création d’expression de requête | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | Exécution de requêtes LINQ      | - Recherche ~~de chargement~~ de métadonnées : ~~Haute mais mise en cache~~ Low <br/> - Voir la ~~génération~~ lookup: ~~Potentiellement très élevé, mais mis en cache~~ faible <br/> - Évaluation des paramètres : Moyen <br/> - Recherche ~~de traduction~~ de requête : Moyen <br/> - Recherche de ~~génération~~ de matérialisation : ~~Moyen mais mise en cache~~ Faible <br/> - Exécution des requêtes de base de données : potentiellement élevée <br/> Connexion.Open <br/> Command.ExecuteReader <br/> DataReader.Read <br/> Matérialisation d’objet : Moyenne <br/> - Recherche d’identité : Moyen | - Recherche ~~de chargement~~ de métadonnées : ~~Haute mais mise en cache~~ Low <br/> - Voir la ~~génération~~ lookup: ~~Potentiellement très élevé, mais mis en cache~~ faible <br/> - Évaluation des paramètres : Faible <br/> - Recherche ~~de traduction~~ de requête : Moyen mais mise en ~~cache~~ Low <br/> - Recherche de ~~génération~~ de matérialisation : ~~Moyen mais mise en cache~~ Faible <br/> - Exécution des requêtes de base de données : Potentiellement élevée (meilleures requêtes dans certaines situations) <br/> Connexion.Open <br/> Command.ExecuteReader <br/> DataReader.Read <br/> Matérialisation d’objet : Moyenne <br/> - Recherche d’identité : Moyen | - Recherche ~~de chargement~~ de métadonnées : ~~Haute mais mise en cache~~ Low <br/> - Voir la ~~génération~~ lookup: ~~Moyen mais mis en cache~~ Faible <br/> - Évaluation des paramètres : Faible <br/> - Recherche ~~de traduction~~ de requête : Moyen mais mise en ~~cache~~ Low <br/> - Recherche de ~~génération~~ de matérialisation : ~~Moyen mais mise en cache~~ Faible <br/> - Exécution des requêtes de base de données : Potentiellement élevée (meilleures requêtes dans certaines situations) <br/> Connexion.Open <br/> Command.ExecuteReader <br/> DataReader.Read <br/> Matérialisation de l’objet : moyenne (plus rapide que EF5) <br/> - Recherche d’identité : Moyen |
| `}`                                                                                                  | Connection.Close (close)          | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


Il existe plusieurs façons de réduire le coût de performance des requêtes froides et chaudes, et nous allons jeter un oeil à ceux-ci dans la section suivante. Plus précisément, nous allons examiner la réduction du coût de chargement des modèles dans les requêtes à froid en utilisant des vues pré-générées, ce qui devrait aider à soulager les douleurs de performance vécues au cours de la génération de vues. Pour les requêtes chaudes, nous couvrirons la mise en cache du plan de requête, aucune requête de suivi et différentes options d’exécution de requête.

### <a name="21-what-is-view-generation"></a>2.1 Qu’est-ce que View Generation?

Afin de comprendre ce qu’est la génération de vues, nous devons d’abord comprendre ce que sont les « vues cartographiques ». Les vues cartographiques sont des représentations exécutables des transformations spécifiées dans la cartographie pour chaque ensemble d’entités et association. À l’interne, ces vues cartographiques prennent la forme de CQT (coniques d’arbres de requête). Il existe deux types de vues cartographiques :

-   Vues de requête : ceux-ci représentent la transformation nécessaire pour passer du schéma de base de données au modèle conceptuel.
-   Points de mise à jour : ceux-ci représentent la transformation nécessaire pour passer du modèle conceptuel au schéma de base de données.

Gardez à l’esprit que le modèle conceptuel peut différer du schéma de base de données de diverses façons. Par exemple, une seule table peut être utilisée pour stocker les données pour deux types d’entités différents. Les cartes d’héritage et non triviales jouent un rôle dans la complexité des vues cartographiques.

Le processus de calcul de ces points de vue basé sur la spécification de la cartographie est ce que nous appelons la génération de vues. La génération de vues peut se faire dynamiquement lorsqu’un modèle est chargé, soit au moment de la construction, en utilisant des « vues pré-générées »; ces dernières sont sérialisées sous la forme\# de déclarations de l’entité SQL à un fichier C ou VB.

Lorsque des vues sont générées, elles sont également validées. Du point de vue de la performance, la grande majorité du coût de la génération de vues est en fait la validation des points de vue qui garantit que les connexions entre les entités ont un sens et ont la cardinalité correcte pour toutes les opérations soutenues.

Lorsqu’une requête sur un ensemble d’entités est exécutée, la requête est combinée avec la vue de requête correspondante, et le résultat de cette composition est exécuté par le compilateur de plan pour créer la représentation de la requête que le magasin d’appui peut comprendre. Pour SQL Server, le résultat final de cette compilation sera une déclaration T-SQL SELECT. La première fois qu’une mise à jour sur un ensemble d’entités est effectuée, la vue de mise à jour est exécutée à travers un processus similaire pour la transformer en instructions DML pour la base de données cible.

### <a name="22-factors-that-affect-view-generation-performance"></a>2.2 Facteurs qui influent sur les performances de View Generation

L’étape de génération de vue dépend non seulement de la taille de votre modèle, mais aussi de la façon dont le modèle est interconnecté. Si deux entités sont connectées par une chaîne d’héritage ou une association, elles seraient connectées. De même, si deux tables sont connectées via une clé étrangère, elles sont connectées. À mesure que le nombre d’entités et de tableaux connectés dans vos schémas augmente, le coût de génération de vue augmente.

L’algorithme que nous utilisons pour générer et valider les vues est exponentiel dans le pire des cas, bien que nous n’utilisons quelques optimisations pour améliorer cela. Les facteurs les plus importants qui semblent avoir une incidence négative sur la performance sont les facteurs les plus importants :

-   Taille du modèle, se référant au nombre d’entités et au nombre d’associations entre ces entités.
-   Complexité du modèle, en particulier l’héritage impliquant un grand nombre de types.
-   Utilisation d’associations indépendantes, au lieu d’associations clés étrangères.

Pour les petits modèles simples, le coût peut être assez faible pour ne pas prendre la peine d’utiliser des vues pré-générées. À mesure que la taille et la complexité du modèle augmentent, plusieurs options sont disponibles pour réduire le coût de la génération et de la validation des images.

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2.3 Utilisation de vues pré-générées pour réduire le temps de chargement du modèle

Pour obtenir des informations détaillées sur la façon d’utiliser les vues pré-générées sur le cadre de l’entité 6, visitez [les vues cartographiques pré-générées](~/ef6/fundamentals/performance/pre-generated-views.md)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 Vues pré-générées à l’aide de l’entity Framework Power Tools Community Edition

Vous pouvez utiliser [l’Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pour générer des vues des modèles EDMX et Code First en cliquant à droite sur le fichier de classe modèle et en utilisant le menu Cadre d’entité pour sélectionner les « Vues de génération ». L’Entity Framework Power Tools Community Edition ne fonctionne que sur des contextes dérivés du DbContexte.

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 Comment utiliser les vues pré-générées avec un modèle créé par EDMGen

EDMGen est un utilitaire qui expédie avec .NET et travaille avec Entity Framework 4 et 5, mais pas avec Entity Framework 6. EDMGen vous permet de générer un fichier modèle, la couche d’objet et les vues de la ligne de commande. L’une des sorties sera un fichier Vues dans votre\#langue de choix, VB ou C . Il s’agit d’un fichier de code contenant des extraits d’entité SQL pour chaque ensemble d’entités. Pour activer les vues pré-générées, il vous suffit d’inclure le fichier dans votre projet.

Si vous effectuez manuellement des modifications aux fichiers schéma pour le modèle, vous devrez régénérer le fichier de vues. Vous pouvez le faire en exécutant EDMGen avec le **drapeau /mode:ViewGeneration.**

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 Comment utiliser les vues pré-générées avec un fichier EDMX

Vous pouvez également utiliser EDMGen pour générer des vues pour un fichier EDMX - le sujet MSDN précédemment référencé décrit comment ajouter un événement de pré-construction pour ce faire - mais c’est compliqué et il ya des cas où il n’est pas possible. Il est généralement plus facile d’utiliser un modèle T4 pour générer les vues lorsque votre modèle est dans un fichier edmx.

Le blog de l’équipe ADO.NET a un post qui décrit comment \< https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>)utiliser un modèle T4 pour la génération de vision ( . Ce message comprend un modèle qui peut être téléchargé et ajouté à votre projet. Le modèle a été écrit pour la première version de Entity Framework, de sorte qu’ils ne sont pas garantis de travailler avec les dernières versions de Entity Framework. Cependant, vous pouvez télécharger un ensemble plus à jour de modèles de génération de vue pour Entity Framework 4 et 5f de la Visual Studio Gallery:

-   VB.NET:\<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C\#:\<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

Si vous utilisez Entity Framework 6, vous pouvez obtenir les modèles de \< http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>la génération de vue T4 à partir de la Visual Studio Gallery à .

### <a name="24-reducing-the-cost-of-view-generation"></a>2.4 Réduire le coût de la production de vision

L’utilisation de vues pré-générées déplace le coût de la génération de vue du chargement du modèle (temps d’exécution) au temps de conception. Bien que cela améliore les performances de démarrage au moment de la course, vous aurez toujours l’expérience de la douleur de la génération de vue pendant que vous développez. Il ya plusieurs astuces supplémentaires qui peuvent aider à réduire le coût de la génération de vue, à la fois au moment de compiler et le temps d’exécution.

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 Utiliser des associations clés étrangères pour réduire les coûts de production de vision

Nous avons vu un certain nombre de cas où le passage des associations dans le modèle des associations indépendantes aux associations clés étrangères a considérablement amélioré le temps passé en vue de la génération.

Pour démontrer cette amélioration, nous avons généré deux versions du modèle Navision en utilisant EDMGen. *Remarque : voir l’annexe C pour une description du modèle Navision.* Le modèle Navision est intéressant pour cet exercice en raison de sa très grande quantité d’entités et de relations entre eux.

Une version de ce modèle très large a été générée avec foreign Keys Associations et l’autre a été générée avec des associations indépendantes. Nous avons ensuite chronométré le temps qu’il a fallu pour générer les vues pour chaque modèle. Le test du cadre 5 de l’entité a utilisé la méthode GenerateViews() de la classe EntityViewGenerator pour générer les vues, tandis que le test du cadre 6 de l’entité a utilisé la méthode GenerateViews() de la classe StorageMappingItemCollection. Cela est dû à la restructuration du code qui s’est produite dans la base de code du Cadre de l’entité 6.

À l’aide d’Entity Framework 5, la génération de vue pour le modèle avec Foreign Keys a pris 65 minutes dans une machine de laboratoire. On ne sait pas combien de temps il aurait fallu pour générer les points de vue pour le modèle qui a utilisé des associations indépendantes. Nous avons laissé le test en cours d’exécution pendant plus d’un mois avant que la machine a été redémarré dans notre laboratoire pour installer des mises à jour mensuelles.

En utilisant Entity Framework 6, la génération de vue pour le modèle avec Foreign Keys a pris 28 secondes dans la même machine de laboratoire. Voir la génération pour le modèle qui utilise des associations indépendantes a pris 58 secondes. Les améliorations apportées à Entity Framework 6 sur son code de génération de vues signifient que de nombreux projets n’aurez pas besoin de vues pré-générées pour obtenir des temps de démarrage plus rapides.

Il est important de faire remarquer que les points de vue pré-générants dans le cadre d’entité 4 et 5 peuvent être faits avec EDMGen ou les outils de puissance de cadre d’entité. Pour entity Framework 6, la génération de vues peut se faire par l’intermédiaire des outils électriques du cadre de l’entité ou de manière programmatique, comme le décrit [les vues de cartographie pré-générées.](~/ef6/fundamentals/performance/pre-generated-views.md)

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 Comment utiliser les clés étrangères au lieu des associations indépendantes

Lorsque vous utilisez EDMGen ou l’Entity Designer dans Visual Studio, vous obtenez des FK par défaut, et il suffit d’une seule case à cocher ou un drapeau de ligne de commande pour basculer entre les FK et les IA.

Si vous avez un grand modèle Code First, l’utilisation d’associations indépendantes aura le même effet sur la génération de vision. Vous pouvez éviter cet impact en incluant les propriétés Foreign Key sur les classes de vos objets dépendants, bien que certains développeurs considéreront cela comme polluant leur modèle d’objet. Vous pouvez trouver plus d’informations sur ce sujet dans \< http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>.

| Lors de l’utilisation      | Action                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Concepteur d'entités | Après l’ajout d’une association entre deux entités, assurez-vous d’avoir une contrainte référentielle. Les contraintes référentielles indiquent à Entity Framework d’utiliser des clés étrangères au lieu d’associations indépendantes. Pour plus \< https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>de détails, visitez . |
| EDMGen (en)          | Lors de l’utilisation d’EDMGen pour générer vos fichiers à partir de la base de données, vos clés étrangères seront respectées et ajoutées au modèle en tant que tel. Pour plus d’informations sur les différentes [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)options exposées par EDMGen visitez .                           |
| Code First      | Consultez la section « Convention de relations » du sujet [des conventions du Code d’abord](~/ef6/modeling/code-first/conventions/built-in.md) pour obtenir de l’information sur la façon d’inclure les propriétés clés étrangères sur les objets dépendants lors de l’utilisation du Code First.                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 Déplacer votre modèle vers un assemblage séparé

Lorsque votre modèle est inclus directement dans le projet de votre application et que vous générez des vues à travers un événement pré-construction ou un modèle T4, la génération de vues et la validation auront lieu chaque fois que le projet est reconstruit, même si le modèle n’a pas été modifié. Si vous déplacez le modèle vers un assemblage séparé et le référencez au projet de votre application, vous pouvez apporter d’autres modifications à votre application sans avoir besoin de reconstruire le projet contenant le modèle.

*Remarque :*  lorsque vous déplacez votre modèle vers des assemblages distincts, n’oubliez pas de copier les chaînes de connexion du modèle dans le fichier de configuration d’application du projet client.

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 Validation désactivée d’un modèle edmx

Les modèles EDMX sont validés au moment de la compilation, même si le modèle est inchangé. Si votre modèle a déjà été validé, vous pouvez supprimer la validation au moment de la compilation en définissant la propriété "Valider sur Construire" à faux dans la fenêtre des propriétés. Lorsque vous modifiez votre carte ou votre modèle, vous pouvez ré-activer temporairement la validation pour vérifier vos modifications.

Notez que des améliorations de performance ont été apportées au concepteur de cadre d’entité pour le cadre d’entité 6, et le coût de la "Valider sur la construction" est beaucoup plus faible que dans les versions précédentes du concepteur.

## <a name="3-caching-in-the-entity-framework"></a>3 Caching dans le cadre de l’entité

Entity Framework a les formes suivantes de mise en cache intégrée :

1.  Mise en cache d’objets - l’ObjectStateManager intégré dans une instance ObjectContext garde une trace à la mémoire des objets qui ont été récupérés à l’aide de cette instance. C’est aussi ce qu’on appelle le cache de premier niveau.
2.  Cachement de plan de requête - réutiliser la commande générée de magasin quand une requête est exécutée plus d’une fois.
3.  Mise en cache de métadonnées - partage des métadonnées pour un modèle entre différentes connexions avec le même modèle.

Outre les caches que EF fournit hors de la boîte, un type spécial de fournisseur de données ADO.NET connu sous le nom de fournisseur d’emballage peut également être utilisé pour étendre Le cadre d’entité avec un cache pour les résultats récupérés à partir de la base de données, également connu sous le nom de mise en cache de deuxième niveau.

### <a name="31-object-caching"></a>3.1 Caching objet

Par défaut, lorsqu’une entité est retournée dans les résultats d’une requête, juste avant que EF ne la matérialise, l’ObjectContext vérifiera si une entité avec la même clé a déjà été chargée dans son ObjectStateManager. Si une entité avec les mêmes clés est déjà présente EF l’inclura dans les résultats de la requête. Bien qu’EF émettra toujours la requête contre la base de données, ce comportement peut contourner une grande partie du coût de matérialisation de l’entité à plusieurs reprises.

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 Obtenir des entités à partir du cache d’objet à l’aide de DbContext Trouver

Contrairement à une requête régulière, la méthode Trouver dans DbSet (API incluse pour la première fois dans EF 4.1) effectuera une recherche dans la mémoire avant même d’émettre la requête contre la base de données. Il est important de noter que deux instances différentes ObjectContext aura deux instances ObjectStateManager différentes, ce qui signifie qu’ils ont des caches d’objets séparées.

Find utilise la valeur clé principale pour tenter de trouver une entité suivie par le contexte. Si l’entité n’est pas dans le contexte, une requête sera exécutée et évaluée par rapport à la base de données, et nulle est retournée si l’entité n’est pas trouvée dans le contexte ou dans la base de données. Notez que Find renvoie également les entités qui ont été ajoutées au contexte mais qui n’ont pas encore été enregistrées dans la base de données.

Il y a une considération de performance à prendre lors de l’utilisation de Find. Les invocations à cette méthode par défaut déclencheront une validation du cache d’objet afin de détecter les modifications qui sont encore en attente de validation à la base de données. Ce processus peut être très coûteux s’il y a un très grand nombre d’objets dans le cache d’objet ou dans un grand graphique d’objet ajouté au cache d’objet, mais il peut également être désactivé. Dans certains cas, vous pouvez percevoir sur un ordre de grandeur de différence en appelant la méthode Trouver lorsque vous désactivez les changements automatiques. Pourtant, un deuxième ordre de grandeur est perçu lorsque l’objet est réellement dans le cache par rapport au moment où l’objet doit être récupéré à partir de la base de données. Voici un graphique d’exemple avec des mesures prises à l’aide de certaines de nos marques de microbilles, exprimées en millisecondes, avec une charge de 5000 entités :

![.NET 4.5 échelle logarithmique](~/ef6/media/net45logscale.png ".NET 4.5 - échelle logarithmique")

Exemple de Trouver avec des changements auto-détection désactivé:

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

Ce que vous devez considérer lors de l’utilisation de la méthode Trouver est:

1.  Si l’objet n’est pas dans le cache les avantages de Trouver sont niés, mais la syntaxe est encore plus simple qu’une requête par clé.
2.  Si des modifications de détection automatique sont activées, le coût de la méthode Trouver peut augmenter d’un ordre de grandeur, voire plus selon la complexité de votre modèle et la quantité d’entités dans votre cache d’objet.

En outre, gardez à l’esprit que Trouver ne renvoie que l’entité que vous recherchez et il ne charge pas automatiquement ses entités associées si elles ne sont pas déjà dans le cache d’objet. Si vous avez besoin de récupérer les entités associées, vous pouvez utiliser une requête par clé avec un chargement avide. Pour plus d’informations voir **8.1 Chargement paresseux vs Chargement impatient**.

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 Problèmes de performance lorsque le cache d’objets a de nombreuses entités

Le cache d’objets permet d’augmenter la réactivité globale du cadre d’entité. Toutefois, lorsque le cache d’objets a une très grande quantité d’entités chargées, il peut affecter certaines opérations telles que Ajouter, Supprimer, Trouver, Entrée, SaveChanges et plus encore. En particulier, les opérations qui déclenchent un appel à DetectChanges seront affectées négativement par de très grandes caches d’objets. DetectChanges synchronise le graphique de l’objet avec le gestionnaire d’état de l’objet et ses performances seront déterminées directement par la taille du graphique de l’objet. Pour plus d’informations sur DetectChanges, voir [Changements de suivi dans les entités POCO](https://msdn.microsoft.com/library/dd456848.aspx).

Lors de l’utilisation de l’Entité Framework 6, les développeurs sont en mesure d’appeler AddRange et RemoveRange directement sur un DbSet, au lieu d’itérer sur une collection et d’appeler Ajouter une fois par instance. L’avantage d’utiliser les méthodes de plage est que le coût de DetectChanges n’est payé qu’une seule fois pour l’ensemble total des entités par opposition à une fois par entité ajoutée.

### <a name="32-query-plan-caching"></a>3.2 Cachement du plan de requête

La première fois qu’une requête est exécutée, elle passe par le compilateur de plan interne pour traduire la requête conceptuelle dans la commande du magasin (par exemple, le T-SQL qui est exécuté lorsqu’il est exécuté contre SQL Server).Si la mise en cache du plan de requête est activée, la prochaine fois que la requête est exécutée, la commande du magasin est récupérée directement à partir du cache du plan de requête pour l’exécution, en contournant le compilateur de plan.

Le cache du plan de requête est partagé à travers les instances ObjectContext dans la même AppDomain. Vous n’avez pas besoin de vous accrocher à une instance ObjectContext pour bénéficier de la mise en cache du plan de requête.

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 Quelques notes sur le plan de requête Caching

-   Le cache du plan de requête est partagé pour tous les types de requêtes : Entity SQL, LINQ to Entities et CompiledQuery objects.
-   Par défaut, la mise en cache du plan de requête est activée pour les requêtes De l’entité SQL, qu’elles soient exécutées par l’intermédiaire d’un agent d’entité ou par l’intermédiaire d’un ObjectQuery. Il est également activé par défaut pour LINQ aux requêtes d’entités dans entity Framework sur .NET 4.5, et dans entity Framework 6
    -   La mise en cache du plan de requête peut être désactivée en mettant la propriété EnablePlanCaching (sur EntityCommand ou ObjectQuery) à faux. Par exemple :
``` csharp
                    var query = from customer in context.Customer
                                where customer.CustomerId == id
                                select new
                                {
                                    customer.CustomerId,
                                    customer.Name
                                };
                    ObjectQuery oQuery = query as ObjectQuery;
                    oQuery.EnablePlanCaching = false;
```
-   Pour les requêtes paramétrées, la modification de la valeur du paramètre sera toujours atteinte de la requête mise en cache. Mais la modification des facettes d’un paramètre (par exemple, la taille, la précision ou l’échelle) touchera une entrée différente dans le cache.
-   Lors de l’utilisation de l’entité SQL, la chaîne de requête fait partie de la clé. Changer la requête à tous se traduira par différentes entrées de cache, même si les requêtes sont fonctionnellement équivalent. Cela comprend des modifications à l’enveloppe ou à l’espace blanc.
-   Lors de l’utilisation de LINQ, la requête est traitée pour générer une partie de la clé. Changer l’expression linQ générera donc une touche différente.
-   D’autres limitations techniques peuvent s’appliquer; voir Autocompiled Queries pour plus de détails.

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 Algorithme d’expulsion de cache

Comprendre comment fonctionne l’algorithme interne vous aidera à déterminer quand activer ou désactiver la mise en cache du plan de requête. L’algorithme de nettoyage est le suivant :

1.  Une fois que le cache contient un nombre d’entrées définies (800), nous commençons une minuterie qui balaie périodiquement (une fois par minute) balaie le cache.
2.  Pendant les balayages de cache, les entrées sont retirées du cache sur une base LFRU (moins fréquemment - récemment utilisée). Cet algorithme tient compte à la fois du nombre de coups et de l’âge lorsqu’il s’agit de décider quelles entrées sont éjectées.
3.  À la fin de chaque balayage de cache, le cache contient à nouveau 800 entrées.

Toutes les entrées de cache sont traitées de manière égale lors de la détermination des entrées à expulser. Cela signifie que la commande du magasin pour un CompiledQuery a les mêmes chances d’expulsion que la commande du magasin pour une requête De l’entité SQL.

Notez que le minuteur d’expulsion cache est lancé dans quand il ya 800 entités dans le cache, mais le cache n’est balayé que 60 secondes après cette minuterie est commencé. Cela signifie que pendant 60 secondes votre cache peut devenir assez grand.

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 Mesures d’essai démontrant le plan de requête cachant les performances

Pour démontrer l’effet de la mise en cache du plan de requête sur les performances de votre application, nous avons effectué un test où nous avons exécuté un certain nombre de requêtes d’entité SQL contre le modèle Navision. Voir l’appendice pour une description du modèle Navision et les types de requêtes qui ont été exécutées. Dans ce test, nous itérons d’abord à travers la liste des requêtes et exécutons chacun une fois pour les ajouter au cache (si la mise en cache est activée). Cette étape n’est pas chronométrée. Ensuite, nous dormons le fil principal pendant plus de 60 secondes pour permettre le balayage cache d’avoir lieu; enfin, nous itérant à travers la liste une 2ème fois pour exécuter les requêtes mises en cache. De plus, le cache du plan serveur SQL est rincé avant que chaque ensemble de requêtes ne soit exécuté afin que les délais que nous obtenons reflètent fidèlement l’avantage donné par le cache du plan de requête.

##### <a name="3231-test-results"></a>3.2.3.1 Résultats des tests

| Test                                                                   | EF5 pas de cache | EF5 mis en cache | EF6 pas de cache | EF6 mis en cache |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| Énumérer toutes les requêtes de 18723                                          | 124          | 125.4      | 124.3        | 125.3      |
| Éviter le balayage (juste les 800 premières requêtes, quelle que soit la complexité)  | 41.7         | 5.5        | 40,5         | 5.4        |
| Juste les requêtes AggregatingSubtotals (178 au total - ce qui évite le balayage) | 39.5         | 4.5        | 38.1         | 4.6        |

*Tous les temps en quelques secondes.*

Morale - lors de l’exécution de beaucoup de requêtes distinctes (par exemple, requêtes créées dynamiquement), la mise en cache n’aide pas et le rinçage résultant de la cache peut garder les requêtes qui bénéficieraient le plus de la mise en cache de plan de réellement l’utiliser.

Les requêtes AggregatingSubtotals sont les plus complexes des requêtes avec lesquelles nous avons testés. Comme prévu, plus la requête est complexe, plus vous verrez d’avantages de la mise en cache du plan de requête.

Parce qu’une requête compilée est vraiment une requête LINQ avec son plan mis en cache, la comparaison d’un CompiledQuery par rapport à l’entité équivalente SQL requête devrait avoir des résultats similaires. En fait, si une application a beaucoup de requêtes dynamiques Entity SQL, le remplissage du cache avec des requêtes fera également en sorte que CompiledQueries «se décompose» quand ils sont rincés à partir du cache. Dans ce scénario, les performances peuvent être améliorées en désactivant la mise en cache sur les requêtes dynamiques pour donner la priorité aux CompiledQueries. Mieux encore, bien sûr, serait de réécrire l’application pour utiliser des requêtes paramétrisées au lieu de requêtes dynamiques.

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3.3 Utilisation de CompiledQuery pour améliorer les performances avec les requêtes LINQ

Nos tests indiquent que l’utilisation de CompiledQuery peut apporter un avantage de 7 % par rapport aux requêtes AUTOcompilées de LINQ ; cela signifie que vous passerez 7% moins de temps à exécuter le code à partir de la pile du Cadre d’Entités; cela ne signifie pas que votre application sera 7% plus rapide. D’une manière générale, le coût d’écriture et de maintenance des objets CompiledQuery dans EF 5.0 peut ne pas valoir la peine par rapport aux avantages. Votre kilométrage peut varier, alors exercez cette option si votre projet nécessite la poussée supplémentaire. Notez que les compiledQueries ne sont compatibles qu’avec les modèles dérivés d’ObjectContext et ne sont pas compatibles avec les modèles dérivés du DbContexte.

Pour plus d’informations sur la création et l’invocation d’un CompiledQuery, voir [Questions compilées (LINQ aux entités)](https://msdn.microsoft.com/library/bb896297.aspx).

Il y a deux considérations que vous devez prendre lors de l’utilisation d’un CompiledQuery, à savoir l’obligation d’utiliser des instances statiques et les problèmes qu’ils ont avec la composition. Voici une explication approfondie de ces deux considérations.

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 Utiliser des instances static CompiledQuery

Étant donné que la compilation d’une requête LINQ est un processus long, nous ne voulons pas le faire chaque fois que nous avons besoin d’obtenir des données de la base de données. CompiledQuery instances vous permettent de compiler une fois et d’exécuter plusieurs fois, mais vous devez être prudent et se procurer de réutiliser la même instance CompiledQuery à chaque fois au lieu de le compiler encore et encore. L’utilisation de membres statiques pour stocker les instances CompiledQuery devient nécessaire; sinon vous ne verrez aucun avantage.

Supposons, par exemple, que votre page dispose de la méthode suivante pour gérer l’affichage des produits pour la catégorie sélectionnée :

``` csharp
    // Warning: this is the wrong way of using CompiledQuery
    using (NorthwindEntities context = new NorthwindEntities())
    {
        string selectedCategory = this.categoriesList.SelectedValue;

        var productsForCategory = CompiledQuery.Compile<NorthwindEntities, string, IQueryable<Product>>(
            (NorthwindEntities nwnd, string category) =>
                nwnd.Products.Where(p => p.Category.CategoryName == category)
        );

        this.productsGrid.DataSource = productsForCategory.Invoke(context, selectedCategory).ToList();
        this.productsGrid.DataBind();
    }

    this.productsGrid.Visible = true;
```

Dans ce cas, vous créerez une nouvelle instance CompiledQuery à la volée chaque fois que la méthode est appelée. Au lieu de voir les avantages de performance en récupérant la commande du magasin à partir du cache du plan de requête, le CompiledQuery passera par le compilateur de plan chaque fois qu’une nouvelle instance est créée. En fait, vous allez polluer votre cache de plan de requête avec une nouvelle entrée CompiledQuery chaque fois que la méthode est appelée.

Au lieu de cela, vous voulez créer une instance statique de la requête compilée, de sorte que vous invoquez la même requête compilée chaque fois que la méthode est appelée. Une façon de le faire est en ajoutant l’instance CompiledQuery en tant que membre de votre contexte objet.Vous pouvez ensuite rendre les choses un peu plus propres en accédant à la CompiledQuery grâce à une méthode d’aide:

``` csharp
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IEnumerable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
            );

        public IEnumerable<Product> GetProductsForCategory(string categoryName)
        {
            return productsForCategoryCQ.Invoke(this, categoryName).ToList();
        }
```

Cette méthode d’aide serait invoquée comme suit :

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 Composition sur un CompiledQuery

La capacité de composer sur n’importe quelle requête LINQ est extrêmement utile; pour ce faire, vous invoquez simplement une méthode après l’IQueryable comme *Skip()* ou *Count()*. Cependant, cela renvoie essentiellement un nouvel objet IQueryable. Bien qu’il n’y ait rien pour vous empêcher techniquement de composer sur un CompiledQuery, ce faisant provoquer la génération d’un nouvel objet IQueryable qui nécessite de passer par le compilateur de plan à nouveau.

Certains composants utiliseront des objets IQueryables composés pour activer des fonctionnalités avancées. Par exemple, ASP. GridView de NET peut être relié à des données à un objet IQueryable via la propriété SelectMethod. Le GridView composera ensuite sur cet objet IQueryable pour permettre le tri et le pagination sur le modèle de données. Comme vous pouvez le voir, l’utilisation d’un CompiledQuery pour le GridView ne serait pas frapper la requête compilée, mais générerait une nouvelle requête autocompiled.

Un endroit où vous pouvez rencontrer ce est lors de l’ajout de filtres progressifs à une requête. Supposons, par exemple, que vous aviez une page Clients avec plusieurs listes d’abandon pour les filtres optionnels (par exemple, Country et OrdersCount). Vous pouvez composer ces filtres sur les résultats IQueryable d’un CompiledQuery, mais ce faisant se traduira par la nouvelle requête en passant par le compilateur de plan chaque fois que vous l’exécutez.

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployee();

        if (this.orderCountFilterList.SelectedItem.Value != defaultFilterText)
        {
            int orderCount = int.Parse(orderCountFilterList.SelectedValue);
            myCustomers = myCustomers.Where(c => c.Orders.Count > orderCount);
        }

        if (this.countryFilterList.SelectedItem.Value != defaultFilterText)
        {
            myCustomers = myCustomers.Where(c => c.Address.Country == countryFilterList.SelectedValue);
        }

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 Pour éviter cette re-compilation, vous pouvez réécrire le CompiledQuery pour prendre en compte les filtres possibles :

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

Ce qui serait invoqué dans l’interface utilisateur comme:

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        int? countFilter = (this.orderCountFilterList.SelectedIndex == 0) ?
            (int?)null :
            int.Parse(this.orderCountFilterList.SelectedValue);

        string countryFilter = (this.countryFilterList.SelectedIndex == 0) ?
            null :
            this.countryFilterList.SelectedValue;

        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployeeWithFilters(
                countFilter, countryFilter);

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 Un compromis ici est la commande générée de magasin aura toujours les filtres avec les contrôles nuls, mais ceux-ci devraient être assez simples pour le serveur de base de données pour optimiser:

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3.4 Mise en cache de métadonnées

Le Cadre de l’entité prend également en charge la mise en cache des métadonnées. Il s’agit essentiellement de la mise en cache d’informations de type et de la cartographie de type à base de données entre différentes connexions et le même modèle. Le cache de métadonnées est unique par AppDomain.

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 Algorithme de cachage des métadonnées

1.  Les informations de métadonnées d’un modèle sont stockées dans un ItemCollection pour chaque EntityConnection.
    -   Comme une note latérale, il existe différents objets ItemCollection pour différentes parties du modèle. Par exemple, StoreItemCollections contient les informations sur le modèle de base de données; ObjectItemCollection contient des informations sur le modèle de données; EdmItemCollection contient des informations sur le modèle conceptuel.

2.  Si deux connexions utilisent la même chaîne de connexion, elles partageront la même instance ItemCollection.
3.  Des chaînes de connexion fonctionnellement équivalentes mais textuellement différentes peuvent entraîner différentes caches de métadonnées. Nous ne symbolique des chaînes de connexion, de sorte que le simple fait de changer l’ordre des jetons devrait entraîner des métadonnées partagées. Mais deux chaînes de connexion qui semblent fonctionnellement les mêmes peuvent ne pas être évaluées comme identiques après la jetonisation.
4.  L’ItemCollection est vérifié périodiquement pour utilisation. S’il est déterminé qu’un espace de travail n’a pas été consulté récemment, il sera marqué pour le nettoyage sur le prochain balayage cache.
5.  La simple création d’une EntityConnection entraînera la création d’un cache de métadonnées (bien que les collections d’objets ne seront pas parasacées tant que la connexion n’aura pas été ouverte). Cet espace de travail restera en mémoire jusqu’à ce que l’algorithme de mise en cache détermine qu’il n’est pas «utilisé».

L’équipe de conseil aux clients a écrit un billet de blog qui décrit la tenue d’une référence à un ItemCollection afin d’éviter la «dépréciation» lors de l’utilisation de grands modèles: \< https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>.

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 La relation entre Metadata Caching et Query Plan Caching

L’instance du cache du plan de requête vit dans l’articlecollection des types de magasins de l’Espace Métadonnais. Cela signifie que les commandes de magasins mises en cache seront utilisées pour les requêtes dans n’importe quel contexte instantané à l’aide d’un espace De métadonnée donné. Cela signifie également que si vous avez deux chaînes de connexions qui sont légèrement différentes et ne correspondent pas après la tokenizing, vous aurez différentes instances cache plan de requête.

### <a name="35-results-caching"></a>3.5 Mise en cache des résultats

Avec la mise en cache des résultats (également connu sous le nom de « mise en cache de deuxième niveau »), vous conservez les résultats des requêtes dans une cache locale. Lors de l’émission d’une requête, vous voyez d’abord si les résultats sont disponibles localement avant de poser des questions contre le magasin. Bien que la mise en cache des résultats ne soit pas directement prise en charge par Entity Framework, il est possible d’ajouter un cache de deuxième niveau à l’aide d’un fournisseur d’emballage. Un fournisseur d’emballage d’exemple avec un cache de deuxième niveau est Alachisoft [Entity Framework Second Level Cache basé sur NCache](https://www.alachisoft.com/ncache/entity-framework.html).

Cette mise en œuvre de la mise en cache de deuxième niveau est une fonctionnalité injectée qui a lieu après que l’expression LINQ a été évaluée (et funcletisée) et que le plan d’exécution des requêtes est calculé ou récupéré à partir du cache de premier niveau. Le cache de deuxième niveau ne stockera alors que les résultats de la base de données brutes, de sorte que le pipeline de matérialisation s’exécute toujours par la suite.

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 Références supplémentaires pour la mise en cache des résultats avec le fournisseur d’emballage

-   Julie Lerman a écrit un article MSDN de « Second-Level Caching in Entity Framework et Windows Azure » qui comprend la façon de mettre à jour le fournisseur d’emballage d’échantillons pour utiliser la mise en cache d’AppFabric de serveur Windows :[https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   Si vous travaillez avec Entity Framework 5, le blog de l’équipe a un post qui \< https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>décrit comment faire fonctionner les choses avec le fournisseur de mise en cache pour Entity Framework 5: . Il comprend également un modèle T4 pour aider à automatiser l’ajout de la mise en cache de 2ème niveau à votre projet.

## <a name="4-autocompiled-queries"></a>4 Requêtes autocompilées

Lorsqu’une requête est émise contre une base de données à l’aide du Cadre d’entité, elle doit passer par une série d’étapes avant de se matérialiser réellement les résultats; l’une de ces étapes est Query Compilation. Les requêtes de l’entité SQL étaient connues pour avoir de bonnes performances car elles sont automatiquement mises en cache, de sorte que la deuxième ou la troisième fois que vous exécutez la même requête, il peut sauter le compilateur de plan et utiliser le plan mis en cache à la place.

Entity Framework 5 a introduit la mise en cache automatique pour LINQ aux requêtes d’entités. Dans les éditions précédentes de Entity Framework la création d’un CompiledQuery pour accélérer votre performance a été une pratique courante, car cela rendrait votre LINQ à la requête entités cacheable. Puisque la mise en cache se fait maintenant automatiquement sans l’utilisation d’un CompiledQuery, nous appelons cette fonctionnalité "requêtes autocompilées". Pour plus d’informations sur le cache du plan de requête et ses mécaniciens, voir Query Plan Caching.

Le cadre de l’entité détecte quand une requête nécessite d’être recompilée, et le fait lorsque la requête est invoquée même si elle avait été compilée auparavant. Les conditions courantes qui font recompiler la requête sont les suivante :

-   Modification de la fusionOption associée à votre requête. La requête en cache ne sera pas utilisée, au lieu de cela le compilateur de plan fonctionnera à nouveau et le plan nouvellement créé est mis en cache.
-   Modification de la valeur de ContextOptions.UseCSharpNullComparisonBehavior. Vous obtenez le même effet que de changer la FusionOption.

D’autres conditions peuvent empêcher votre requête d’utiliser le cache. Voici des exemples courants :

-   Utilisation de IEnumerable&lt;T&gt;. Contient&lt;&gt;(valeur T).
-   Utilisation de fonctions qui produisent des requêtes avec des constantes.
-   Utilisation des propriétés d’un objet non cartographié.
-   Lier votre requête à une autre requête qui nécessite d’être recompilé.

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4.1 Utilisation de T&lt;&gt;IEnumerable . Contient&lt;&gt;T (valeur T)

Entity Framework ne cache pas les requêtes&lt;&gt;qui invoquent IEnumerable T . Contient&lt;&gt;T (valeur T) par rapport à une collection en mémoire, puisque les valeurs de la collection sont considérées comme volatiles. L’exemple suivant ne sera pas mis en cache, de sorte qu’il sera toujours traité par le compilateur de plan:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var query = context.MyEntities
                    .Where(entity => ids.Contains(entity.Id));

    var results = query.ToList();
    ...
}
```

Notez que la taille de l’IEnumerable contre lequel Contient est exécuté détermine à quelle vitesse ou à quelle vitesse ou à quelle vitesse votre requête est compilée. Les performances peuvent souffrir de manière significative lors de l’utilisation de grandes collections telles que celle montrée dans l’exemple ci-dessus.

Entity Framework 6 contient des optimisations à&lt;la&gt;façon dont IEnumerable T . Contient&lt;&gt;des travaux T (valeur T) lorsque les requêtes sont exécutées. Le code SQL qui est généré est beaucoup plus rapide à produire et plus lisible, et dans la plupart des cas, il exécute également plus rapidement dans le serveur.

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4.2 Utilisation de fonctions qui produisent des requêtes avec des constantes

Les opérateurs DefautIfEmpty () de LaLF(Skip), Take(), Contains() et DefautIfEmpty() ne produisent pas de requêtes SQL avec des paramètres, mais mettent plutôt les valeurs qui leur sont transmises comme des constantes. Pour cette raison, les requêtes qui pourraient autrement être identiques finissent par polluer le cache du plan de requête, à la fois sur la pile EF et sur le serveur de base de données, et ne sont pas réutilisés à moins que les mêmes constantes sont utilisées dans une exécution de requête ultérieure. Par exemple :

``` csharp
var id = 10;
...
using (var context = new MyContext())
{
    var query = context.MyEntities.Select(entity => entity.Id).Contains(id);

    var results = query.ToList();
    ...
}
```

Dans cet exemple, chaque fois que cette requête est exécutée avec une valeur différente pour id la requête sera compilée dans un nouveau plan.

En particulier prêter attention à l’utilisation de Skip and Take lors de la prise de paging. Dans EF6, ces méthodes ont une surcharge lambda qui rend effectivement le plan de requête en cache réutilisable parce que EF peut capturer les variables transmises à ces méthodes et les traduire en SQLparameters. Cela permet également de garder le cache plus propre car sinon chaque requête avec une constante différente pour Skip and Take obtiendrait sa propre entrée cache plan de requête.

Considérez le code suivant, qui est sous-optimale, mais est seulement destiné à illustrer cette classe de requêtes:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Une version plus rapide de ce même code impliquerait d’appeler Skip avec un lambda:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Le deuxième extrait peut aller jusqu’à 11% plus vite parce que le même plan de requête est utilisé chaque fois que la requête est exécutée, ce qui permet d’économiser du temps CPU et évite de polluer le cache de requête. En outre, parce que le paramètre de Skip est dans une fermeture le code pourrait aussi bien ressembler à ceci maintenant:

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4.3 Utilisation des propriétés d’un objet non cartographié

Lorsqu’une requête utilise les propriétés d’un type d’objet non cartographié comme paramètre, la requête ne sera pas mise en cache. Par exemple :

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();

    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myObject.MyProperty)
                select entity;

   var results = query.ToList();
    ...
}
```

Dans cet exemple, supposons que la classe NonMappedType ne fait pas partie du modèle d’entité. Cette requête peut facilement être modifiée pour ne pas utiliser un type non cartographié et utiliser plutôt une variable locale comme paramètre de la requête :

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();
    var myValue = myObject.MyProperty;
    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myValue)
                select entity;

    var results = query.ToList();
    ...
}
```

Dans ce cas, la requête sera en mesure d’obtenir mis en cache et bénéficiera de la cache plan de requête.

### <a name="44-linking-to-queries-that-require-recompiling"></a>4.4 Liaison aux requêtes nécessitant un recompilage

Suivant le même exemple que ci-dessus, si vous avez une deuxième requête qui repose sur une requête qui doit être recompilée, votre deuxième requête entière sera également recompilée. Voici un exemple pour illustrer ce scénario :

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var firstQuery = from entity in context.MyEntities
                        where ids.Contains(entity.Id)
                        select entity;

    var secondQuery = from entity in context.MyEntities
                        where firstQuery.Any(otherEntity => otherEntity.Id == entity.Id)
                        select entity;

    var results = secondQuery.ToList();
    ...
}
```

L’exemple est générique, mais il illustre comment lier à firstQuery est à l’origine secondQuery d’être incapable d’obtenir mis en cache. Si d’abordQuery n’avait pas été une requête qui nécessite un recompilage, alors secondQuery aurait été mis en cache.

## <a name="5-notracking-queries"></a>5 Questions NoTracking

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5.1 Désactiver le suivi des changements pour réduire les frais généraux de gestion de l’État

Si vous êtes dans un scénario de lecture seulement et que vous voulez éviter les frais généraux de chargement des objets dans l’ObjectStateManager, vous pouvez émettre des requêtes "No Tracking".Le suivi des modifications peut être désactivé au niveau de la requête.

Notez cependant qu’en désactivant le suivi des modifications, vous éteignez efficacement le cache d’objets. Lorsque vous interrogez pour une entité, nous ne pouvons pas sauter la matérialisation en tirant les résultats de requête précédemment matérialisés de l’ObjectStateManager. Si vous interrogez à plusieurs reprises pour les mêmes entités dans le même contexte, vous pourriez en fait voir un avantage de performance de permettre le suivi des modifications.

Lors de la requête en utilisant ObjectContext, ObjectQuery et ObjectSet instances se souviendront d’une FusionOption une fois qu’il est réglé, et les requêtes qui sont composées sur eux hériteront de la fusion efficaceoption de la requête parente. Lors de l’utilisation de DbContext, le suivi peut être désactivé en appelant le modificateur AsNoTracking() sur le DbSet.

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 Désactiver le suivi des modifications pour une requête lors de l’utilisation de DbContext

Vous pouvez passer le mode d’une requête à NoTracking en enchaînant un appel à la méthode AsNoTracking() dans la requête. Contrairement à ObjectQuery, les classes DbSet et DbQuery dans l’API DbContext n’ont pas de propriété mutable pour la MergeOption.

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 Suivi des modifications invalidant au niveau de la requête à l’aide d’ObjectContext

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 Suivi des modifications invalidantes pour une entité entière définie à l’aide d’ObjectContext

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5.2 Mesures de test démontrant l’avantage de performance des requêtes NoTracking

Dans ce test, nous examinons le coût de remplissage de l’ObjectStateManager en comparant tracking à NoTracking requêtes pour le modèle Navision. Voir l’appendice pour une description du modèle Navision et les types de requêtes qui ont été exécutées. Dans ce test, nous itérer à travers la liste des requêtes et exécuter chacun une fois. Nous avons couru deux variantes du test, une fois avec des requêtes NoTracking et une fois avec l’option de fusion par défaut de "AppendOnly". Nous avons couru chaque variation 3 fois et prenons la valeur moyenne des courses. Entre les tests, nous effacer le cache de requête sur le serveur SQL et rétrécir le tempdb en exécutant les commandes suivantes:

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE (tempdb, 0)

Résultats des tests, médiane sur 3 pistes :

|                        | PAS DE SUIVI - ENSEMBLE DE TRAVAIL | PAS DE SUIVI - TEMPS | APPEND SEULEMENT - ENSEMBLE DE TRAVAIL | APPEND SEULEMENT - TEMPS |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Cadre de l’entité 5** | 460361728                 | 1163536 ms         | 596545536                 | 1273042 ms         |
| **Entity Framework 6** | 647127040                 | 190228 ms          | 832798720                 | 195521 ms          |

Entity Framework 5 aura une empreinte mémoire plus faible à la fin de la course que l’entité Framework 6. La mémoire supplémentaire consommée par Entity Framework 6 est le résultat de structures de mémoire supplémentaires et de code qui permettent de nouvelles fonctionnalités et de meilleures performances.

Il ya aussi une nette différence dans l’empreinte de mémoire lors de l’utilisation de l’ObjectStateManager. Entity Framework 5 a augmenté son empreinte de 30 % en gardant une trace de toutes les entités que nous nous sommes matérialisées à partir de la base de données. Entity Framework 6 a augmenté son empreinte de 28 % à ce faire.

En termes de temps, Entity Framework 6 surpasse largement le cadre de l’entité 5 dans ce critère. Le Cadre de l’entité 6 a terminé le test dans environ 16 % du temps consommé par l’Entité Framework 5. En outre, Entity Framework 5 prend 9 % plus de temps à compléter lorsque l’ObjectStateManager est utilisé. En comparaison, Entity Framework 6 utilise 3 % plus de temps lors de l’utilisation de l’ObjectStateManager.

## <a name="6-query-execution-options"></a>6 Options d’exécution de requête

Entity Framework offre plusieurs façons différentes de poser des questions. Nous examinerons les options suivantes, comparerons les avantages et les inconvénients de chacun, et examinerons leurs caractéristiques de performance :

-   LINQ aux entités.
-   Pas de suivi LINQ aux entités.
-   Entité SQL sur un ObjectQuery.
-   Entité SQL sur un EntityCommand.
-   ExécuterStoreQuery.
-   SqlQuery.
-   CompiledQuery.

### <a name="61-linq-to-entities-queries"></a>6.1 LINQ aux requêtes d’entités

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**Avantages**

-   Convient aux opérations du CUD.
-   Objets entièrement matérialisés.
-   Plus simple à écrire avec la syntaxe intégrée dans le langage de programmation.
-   Bonne performance.

**Inconvénients**

-   Certaines restrictions techniques, telles que :
    -   Les modèles utilisant DefaultIfEmpty pour les requêtes OUTER JOIN donnent lieu à des requêtes plus complexes que de simples relevés OUTER JOIN dans Entity SQL.
    -   Vous ne pouvez toujours pas utiliser LIKE avec l’appariement des motifs généraux.

### <a name="62-no-tracking-linq-to-entities-queries"></a>6.2 Pas de suivi LINQ aux requêtes d’entités

Lorsque le contexte dérive ObjectContext:

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

Lorsque le contexte dérive DbContext:

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**Avantages**

-   Amélioration des performances par le fil des requêtes LINQ régulières.
-   Objets entièrement matérialisés.
-   Plus simple à écrire avec la syntaxe intégrée dans le langage de programmation.

**Inconvénients**

-   Non adapté aux opérations du CUD.
-   Certaines restrictions techniques, telles que :
    -   Les modèles utilisant DefaultIfEmpty pour les requêtes OUTER JOIN donnent lieu à des requêtes plus complexes que de simples relevés OUTER JOIN dans Entity SQL.
    -   Vous ne pouvez toujours pas utiliser LIKE avec l’appariement des motifs généraux.

Notez que les requêtes qui projettent des propriétés scalaires ne sont pas suivies même si le NoTracking n’est pas spécifié. Par exemple :

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

Cette requête particulière ne précise pas explicitement être NoTracking, mais comme il ne se matérialise pas un type qui est connu du gestionnaire d’état d’objet, puis le résultat matérialisé n’est pas suivi.

### <a name="63-entity-sql-over-an-objectquery"></a>6.3 Entité SQL sur un ObjectQuery

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**Avantages**

-   Convient aux opérations du CUD.
-   Objets entièrement matérialisés.
-   Soutient la mise en cache du plan de requête.

**Inconvénients**

-   Implique des chaînes de requête textuelles qui sont plus sujettes à l’erreur de l’utilisateur que les constructions de requête intégrées dans la langue.

### <a name="64-entity-sql-over-an-entity-command"></a>6.4 Entité SQL sur une commande d’entité

``` csharp
EntityCommand cmd = eConn.CreateCommand();
cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
{
    while (reader.Read())
    {
        // manually 'materialize' the product
    }
}
```

**Avantages**

-   Prend en charge la mise en cache du plan de requête en .NET 4.0 (la mise en cache du plan est prise en charge par tous les autres types de requêtes en .NET 4.5).

**Inconvénients**

-   Implique des chaînes de requête textuelles qui sont plus sujettes à l’erreur de l’utilisateur que les constructions de requête intégrées dans la langue.
-   Non adapté aux opérations du CUD.
-   Les résultats ne sont pas automatiquement matérialisés et doivent être lus par le lecteur de données.

### <a name="65-sqlquery-and-executestorequery"></a>6.5 SqlQuery et ExecuteStoreQuery

SqlQuery sur la base de données:

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

SqlQuery sur DbSet:

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

ExecyteStoreQuery:

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

**Avantages**

-   Les performances généralement les plus rapides depuis le compilateur de plan sont contournées.
-   Objets entièrement matérialisés.
-   Convient aux opérations CUD lorsqu’il est utilisé à partir du DbSet.

**Inconvénients**

-   La requête est textuelle et sujette aux erreurs.
-   La requête est liée à un backend spécifique en utilisant la sémantique de magasin au lieu de la sémantique conceptuelle.
-   Lorsque l’héritage est présent, la requête artisanale doit tenir compte des conditions de cartographie du type demandé.

### <a name="66-compiledquery"></a>6.6 CompiledQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**Avantages**

-   Fournit jusqu’à une amélioration de performance de 7 % par rapport aux requêtes régulières de LINQ.
-   Objets entièrement matérialisés.
-   Convient aux opérations du CUD.

**Inconvénients**

-   Augmentation de la complexité et des frais généraux de programmation.
-   L’amélioration des performances est perdue lors de la composition au-dessus d’une requête compilée.
-   Certaines requêtes de LINQ ne peuvent pas être écrites comme une CompiledQuery - par exemple, des projections de types anonymes.

### <a name="67-performance-comparison-of-different-query-options"></a>6.7 Comparaison des performances des différentes options de requête

De simples microbilles où la création contextuelle n’a pas été chronométrée ont été mises à l’épreuve. Nous avons mesuré la requête 5000 fois pour un ensemble d’entités non mises en cache dans un environnement contrôlé. Ces chiffres doivent être pris avec un avertissement: ils ne reflètent pas les chiffres réels produits par une application, mais au lieu de cela, ils sont une mesure très précise de la quantité d’une différence de performance il ya lorsque différentes options de requête sont comparées pommes-à-pommes, à l’exclusion du coût de créer un nouveau contexte.

| EF  | Test                                 | Temps (ms) | Mémoire   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ObjectContext ESQL                   | 2414      | 38801408 |
| EF5 | ObjectContext Linq Requête             | 2692      | 38277120 |
| EF5 | DbContext Linq Requête Pas de suivi     | 2818      | 41840640 |
| EF5 | DbContext Linq Query                 | 2930      | 41771008 |
| EF5 | ObjectContext Linq Requête No Tracking | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ObjectContext ESQL                   | 2059      | 46039040 |
| EF6 | ObjectContext Linq Requête             | 3074      | 45248512 |
| EF6 | DbContext Linq Requête Pas de suivi     | 3125      | 47575040 |
| EF6 | DbContext Linq Query                 | 3420      | 47652864 |
| EF6 | ObjectContext Linq Requête No Tracking | 3593      | 45260800 |

![EF5 micro benchmarks, 5000 itérations chaudes](~/ef6/media/ef5micro5000warm.png)

![EF6 micro benchmarks, 5000 itérations chaudes](~/ef6/media/ef6micro5000warm.png)

Les microbilles sont très sensibles aux petits changements dans le code. Dans ce cas, la différence entre les coûts du Cadre 5 de l’entité et du Cadre d’entité 6 est due à l’ajout [d’améliorations d’interception](~/ef6/fundamentals/logging-and-interception.md) et de [transaction](~/ef6/saving/transactions.md). Ces nombres de micro-marques, cependant, sont une vision amplifiée dans un très petit fragment de ce que fait le Cadre de l’Entité. Les scénarios réels de requêtes chaudes ne devraient pas voir une régression de performance lors de la mise à niveau de l’entité Framework 5 au cadre d’entité 6.

Pour comparer les performances réelles des différentes options de requête, nous avons créé 5 variantes de test distinctes où nous utilisons une option de requête différente pour sélectionner tous les produits dont le nom de catégorie est "Boissons". Chaque itération comprend le coût de création du contexte et le coût de matérialisation de toutes les entités retournées. 10 itérations sont exécutées inopportun avant de prendre la somme de 1000 itérations chronométrées. Les résultats montrés sont la course médiane prise à partir de 5 courses de chaque test. Pour plus d’informations, voir l’annexe B qui inclut le code pour le test.

| EF  | Test                                        | Temps (ms) | Mémoire   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | Commandement de l’entité ObjectContext                | 621       | 39350272 |
| EF5 | DbContext Sql Requête sur la base de données             | 825       | 37519360 |
| EF5 | ObjectContext Store Requête                   | 878       | 39460864 |
| EF5 | ObjectContext Linq Requête No Tracking        | 969       | 38293504 |
| EF5 | ObjectContext Entity Sql à l’aide de la requête objet | 1089      | 38981632 |
| EF5 | ObjectContext Compiled Requête                | 1099      | 38682624 |
| EF5 | ObjectContext Linq Requête                    | 1152      | 38178816 |
| EF5 | DbContext Linq Requête Pas de suivi            | 1208      | 41803776 |
| EF5 | DbContext Sql Query sur DbSet                | 1414      | 37982208 |
| EF5 | DbContext Linq Query                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | Commandement de l’entité ObjectContext                | 480       | 47247360 |
| EF6 | ObjectContext Store Requête                   | 493       | 46739456 |
| EF6 | DbContext Sql Requête sur la base de données             | 614       | 41607168 |
| EF6 | ObjectContext Linq Requête No Tracking        | 684       | 46333952 |
| EF6 | ObjectContext Entity Sql à l’aide de la requête objet | 767       | 48865280 |
| EF6 | ObjectContext Compiled Requête                | 788       | 48467968 |
| EF6 | DbContext Linq Requête Pas de suivi            | 878       | 47554560 |
| EF6 | ObjectContext Linq Requête                    | 953       | 47632384 |
| EF6 | DbContext Sql Query sur DbSet                | 1023      | 41992192 |
| EF6 | DbContext Linq Query                        | 1290      | 47529984 |


![EF5 requête chaude 1000 itérations](~/ef6/media/ef5warmquery1000.png)

![EF6 requête chaude 1000 itérations](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> Pour l’exhaustivité, nous avons inclus une variation où nous exécutons une requête d’entité SQL sur un EntityCommand. Cependant, parce que les résultats ne sont pas matérialisés pour de telles requêtes, la comparaison n’est pas nécessairement pommes-pommes-pommes. Le test comprend une approximation étroite à la matérialisation pour essayer de rendre la comparaison plus juste.

Dans ce cas de bout en bout, Entity Framework 6 surpasse le cadre d’entité 5 en raison des améliorations apportées au&lt;rendement&gt; sur plusieurs parties de la pile, y compris une initialisation DbContext beaucoup plus légère et des examens plus rapides De MétaadataCollection T.

## <a name="7-design-time-performance-considerations"></a>7 Considérations de performance de temps de conception

### <a name="71-inheritance-strategies"></a>7.1 Stratégies d’héritage

Une autre considération de performance lors de l’utilisation de l’entité Cadre est la stratégie de succession que vous utilisez. Entity Framework prend en charge 3 types d’héritage de base et leurs combinaisons :

-   Tableau par hiérarchie (TPH) - où chaque héritage a fixé des cartes à une table avec une colonne discriminateur pour indiquer quel type particulier dans la hiérarchie est représenté dans la rangée.
-   Tableau par type (TPT) - où chaque type a sa propre table dans la base de données; les tables pour enfants ne définissent que les colonnes que la table mère ne contient pas.
-   Tableau par classe (PTC) - où chaque type a sa propre table complète dans la base de données; les tableaux d’enfants définissent tous leurs domaines, y compris ceux définis dans les types de parents.

Si votre modèle utilise l’héritage TPT, les requêtes qui sont générées seront plus complexes que celles qui sont générées avec les autres stratégies d’héritage, ce qui peut entraîner des temps d’exécution plus longs sur le magasin.Il faudra généralement plus de temps pour générer des requêtes sur un modèle TPT, et pour matérialiser les objets résultants.

Voir les « considérations de performance lors de l’utilisation de TPT (tableau par type) Héritage dans le cadre d’entité » MSDN blog post: \< https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>.

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 Éviter le TPT dans les applications Model First ou Code First

Lorsque vous créez un modèle sur une base de données existante qui a un schéma TPT, vous n’avez pas beaucoup d’options. Mais lors de la création d’une application en utilisant Model First ou Code First, vous devez éviter l’héritage TPT pour des problèmes de performance.

Lorsque vous utilisez Model First dans l’Entité Designer Wizard, vous obtiendrez TPT pour tout héritage dans votre modèle. Si vous souhaitez passer à une stratégie d’héritage TPH avec Model First, vous pouvez utiliser \< http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)le "Entity Designer Database Generation Power Pack" disponible à la Visual Studio Gallery ( .

Lors de l’utilisation de Code First pour configurer la cartographie d’un modèle avec héritage, EF utilisera TPH par défaut, donc toutes les entités de la hiérarchie successorale seront cartographiées sur la même table. Voir la section "Mapping with the Fluent API" de l’article "Code First in [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)Entity Framework4.1" dans MSDN Magazine ( ) pour plus de détails.

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7.2 Mise à niveau de EF4 pour améliorer le temps de production de modèle

Une amélioration spécifique au serveur SQL de l’algorithme qui génère la couche de stockage (SSDL) du modèle est disponible dans Entity Framework 5 et 6, et comme une mise à jour de l’entité Framework 4 lorsque Visual Studio 2010 SP1 est installé. Les résultats des tests suivants démontrent l’amélioration lors de la génération d’un très grand modèle, dans ce cas, le modèle Navision. Voir l’annexe C pour plus de détails à ce sujet.

Le modèle contient 1005 ensembles d’entités et 4227 ensembles d’associations.

| Configuration                              | Répartition du temps consommé                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010, Cadre d’entité 4     | Génération SSDL: 2 h 27 min <br/> Génération de cartographie : 1 seconde <br/> Génération CSDL : 1 seconde <br/> Génération ObjectLayer: 1 seconde <br/> Génération de vue: 2 h 14 min |
| Visual Studio 2010 SP1, Cadre d’entité 4 | Génération SSDL : 1 seconde <br/> Génération de cartographie : 1 seconde <br/> Génération CSDL : 1 seconde <br/> Génération ObjectLayer: 1 seconde <br/> Génération de vue: 1 h 53 min   |
| Visual Studio 2013, Cadre d’entité 5     | Génération SSDL : 1 seconde <br/> Génération de cartographie : 1 seconde <br/> Génération CSDL : 1 seconde <br/> Génération ObjectLayer: 1 seconde <br/> Génération de vue : 65 minutes    |
| Visual Studio 2013, Cadre d’entité 6     | Génération SSDL : 1 seconde <br/> Génération de cartographie : 1 seconde <br/> Génération CSDL : 1 seconde <br/> Génération ObjectLayer: 1 seconde <br/> Génération de vue : 28 secondes.   |


Il est à noter que lors de la génération de la SSDL, la charge est presque entièrement dépensée sur le serveur SQL, tandis que la machine de développement client attend au ralenti que les résultats reviennent du serveur. Les DBA devraient particulièrement apprécier cette amélioration. Il est également intéressant de noter que essentiellement le coût total de la génération de modèles a lieu dans View Generation maintenant.

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7.3 Fractionner les grands modèles avec base de données d’abord et le modèle d’abord

À mesure que la taille du modèle augmente, la surface du concepteur devient encombrée et difficile à utiliser. Nous considérons généralement un modèle avec plus de 300 entités pour être trop grand pour utiliser efficacement le concepteur. Le blog suivant décrit plusieurs options pour \< https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>diviser les grands modèles: .

Le message a été écrit pour la première version de Entity Framework, mais les étapes s’appliquent toujours.

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7.4 Considérations de performance avec le contrôle des sources de données de l’entité

Nous avons vu des cas dans des tests de performance et de stress à plusieurs threads où les performances d’une application Web utilisant le contrôle d’EntityDataSource se détériore considérablement. La cause sous-jacente est que l’EntityDataSource appelle à plusieurs reprises MetadataWorkspace.LoadDeAssembly sur les assemblages référencés par l’application Web pour découvrir les types à utiliser comme entités.

La solution consiste à définir le ContextTypeName de l’EntityDataSource au nom type de votre classe ObjectContext dérivée. Cela éteint le mécanisme qui scanne tous les assemblages référencés pour les types d’entités.

La définition du champ ContextTypeName empêche également un problème fonctionnel où l’EntityDataSource en .NET 4.0 lance une reflectionTypeLoadException lorsqu’il ne peut pas charger un type à partir d’un assemblage par la réflexion. Ce problème a été corrigé en .NET 4.5.

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7.5 Entités de l’OCO et modification des procurations de suivi

Entity Framework vous permet d’utiliser des classes de données personnalisées ainsi que votre modèle de données sans apporter de modifications aux classes de données elles-mêmes. Cela signifie que vous pouvez utiliser des objets CLR « classiques » (ou POCO), tels que les objets de domaine existants, avec votre modèle de données. Ces catégories de données POCO (également connues sous le nom d’objets ignorants de persistance), qui sont cartographiées à des entités qui sont définies dans un modèle de données, prennent en charge la plupart des mêmes requêtes, insèrent, mettent à jour et suppriment les comportements en tant que types d’entités générés par les outils du modèle de données d’entité.

Entity Framework peut également créer des classes proxy dérivées de vos types POCO, qui sont utilisés lorsque vous souhaitez activer des fonctionnalités telles que le chargement paresseux et le suivi automatique des modifications sur les entités POCO. Vos classes poCO doivent répondre à certaines exigences pour permettre à [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)Entity Framework d’utiliser des procurations, comme décrit ici : .

Les procurations de suivi des chances aviseront le gestionnaire d’état d’objet chaque fois que l’une des propriétés de vos entités a changé sa valeur, de sorte que Le cadre d’entité connaît l’état réel de vos entités tout le temps. Ceci est fait en ajoutant des événements de notification au corps des méthodes de setter de vos propriétés, et ayant le gestionnaire d’état d’objet traitant de tels événements. Notez que la création d’une entité de procuration sera généralement plus coûteuse que la création d’une entité POCO non-proxy en raison de l’ensemble supplémentaire d’événements créés par Entity Framework.

Lorsqu’une entité POCO n’a pas de proxy de suivi des modifications, des modifications sont trouvées en comparant le contenu de vos entités à une copie d’un état enregistré précédent. Cette comparaison profonde deviendra un long processus lorsque vous avez de nombreuses entités dans votre contexte, ou lorsque vos entités ont une très grande quantité de propriétés, même si aucune d’entre elles n’a changé depuis la dernière comparaison a eu lieu.

En résumé: vous allez payer un succès de performance lors de la création du proxy de suivi de modification, mais le suivi des modifications vous aidera à accélérer le processus de détection de changement lorsque vos entités ont de nombreuses propriétés ou lorsque vous avez de nombreuses entités dans votre modèle. Pour les entités avec un petit nombre de propriétés où le nombre d’entités ne pousse pas trop, ayant des procurations de suivi de changement peut ne pas être d’un grand avantage.

## <a name="8-loading-related-entities"></a>8 Entités liées au chargement

### <a name="81-lazy-loading-vs-eager-loading"></a>8.1 Chargement paresseux vs Chargement impatient

Entity Framework offre plusieurs façons différentes de charger les entités qui sont liées à votre entité cible. Par exemple, lorsque vous interrogez pour des produits, il existe différentes façons dont les commandes connexes seront chargées dans le gestionnaire d’état d’objet. Du point de vue de la performance, la plus grande question à considérer lors du chargement des entités connexes sera de savoir s’il faut utiliser Le chargement paresseux ou le chargement impatient.

Lors de l’utilisation de l’utilisation de chargement d’ardem, les entités apparentées sont chargées avec votre entité cible définie. Vous utilisez une déclaration inclure dans votre requête pour indiquer les entités connexes que vous souhaitez apporter.

Lors de l’utilisation de Lazy Loading, votre requête initiale n’apporte que l’entité cible définie. Mais chaque fois que vous accédez à une propriété de navigation, une autre requête est émise contre le magasin pour charger l’entité connexe.

Une fois qu’une entité a été chargée, toute autre requête pour l’entité la chargera directement auprès du gestionnaire d’état d’objet, que vous utilisiez le chargement paresseux ou le chargement avide.

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8.2 Comment choisir entre le chargement paresseux et le chargement impatient

La chose importante est que vous compreniez la différence entre Le chargement paresseux et le chargement impatient afin que vous puissiez faire le bon choix pour votre application. Cela vous aidera à évaluer le compromis entre plusieurs demandes par rapport à la base de données par rapport à une seule demande qui peut contenir une charge utile importante. Il peut être approprié d’utiliser le chargement avide dans certaines parties de votre application et le chargement paresseux dans d’autres parties.

Comme un exemple de ce qui se passe sous le capot, supposons que vous voulez demander pour les clients qui vivent au Royaume-Uni et leur compte de commande.

**Utilisation du chargement impatient**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**Utilisation du chargement paresseux**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    context.ContextOptions.LazyLoadingEnabled = true;

    //Notice that the Include method call is missing in the query
    var ukCustomers = context.Customers.Where(c => c.Address.Country == "UK");

    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

Lorsque vous utilisez un chargement enthousiaste, vous émettrez une seule requête qui renvoie tous les clients et toutes les commandes. La commande du magasin ressemble à:

``` SQL
SELECT
[Project1].[C1] AS [C1],
[Project1].[CustomerID] AS [CustomerID],
[Project1].[CompanyName] AS [CompanyName],
[Project1].[ContactName] AS [ContactName],
[Project1].[ContactTitle] AS [ContactTitle],
[Project1].[Address] AS [Address],
[Project1].[City] AS [City],
[Project1].[Region] AS [Region],
[Project1].[PostalCode] AS [PostalCode],
[Project1].[Country] AS [Country],
[Project1].[Phone] AS [Phone],
[Project1].[Fax] AS [Fax],
[Project1].[C2] AS [C2],
[Project1].[OrderID] AS [OrderID],
[Project1].[CustomerID1] AS [CustomerID1],
[Project1].[EmployeeID] AS [EmployeeID],
[Project1].[OrderDate] AS [OrderDate],
[Project1].[RequiredDate] AS [RequiredDate],
[Project1].[ShippedDate] AS [ShippedDate],
[Project1].[ShipVia] AS [ShipVia],
[Project1].[Freight] AS [Freight],
[Project1].[ShipName] AS [ShipName],
[Project1].[ShipAddress] AS [ShipAddress],
[Project1].[ShipCity] AS [ShipCity],
[Project1].[ShipRegion] AS [ShipRegion],
[Project1].[ShipPostalCode] AS [ShipPostalCode],
[Project1].[ShipCountry] AS [ShipCountry]
FROM ( SELECT
      [Extent1].[CustomerID] AS [CustomerID],
       [Extent1].[CompanyName] AS [CompanyName],
       [Extent1].[ContactName] AS [ContactName],
       [Extent1].[ContactTitle] AS [ContactTitle],
       [Extent1].[Address] AS [Address],
       [Extent1].[City] AS [City],
       [Extent1].[Region] AS [Region],
       [Extent1].[PostalCode] AS [PostalCode],
       [Extent1].[Country] AS [Country],
       [Extent1].[Phone] AS [Phone],
       [Extent1].[Fax] AS [Fax],
      1 AS [C1],
       [Extent2].[OrderID] AS [OrderID],
       [Extent2].[CustomerID] AS [CustomerID1],
       [Extent2].[EmployeeID] AS [EmployeeID],
       [Extent2].[OrderDate] AS [OrderDate],
       [Extent2].[RequiredDate] AS [RequiredDate],
       [Extent2].[ShippedDate] AS [ShippedDate],
       [Extent2].[ShipVia] AS [ShipVia],
       [Extent2].[Freight] AS [Freight],
       [Extent2].[ShipName] AS [ShipName],
       [Extent2].[ShipAddress] AS [ShipAddress],
       [Extent2].[ShipCity] AS [ShipCity],
       [Extent2].[ShipRegion] AS [ShipRegion],
       [Extent2].[ShipPostalCode] AS [ShipPostalCode],
       [Extent2].[ShipCountry] AS [ShipCountry],
      CASE WHEN ([Extent2].[OrderID] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
      FROM  [dbo].[Customers] AS [Extent1]
      LEFT OUTER JOIN [dbo].[Orders] AS [Extent2] ON [Extent1].[CustomerID] = [Extent2].[CustomerID]
      WHERE N'UK' = [Extent1].[Country]
)  AS [Project1]
ORDER BY [Project1].[CustomerID] ASC, [Project1].[C2] ASC
```

Lorsque vous utilisez le chargement paresseux, vous émettrez la requête suivante au départ :

``` SQL
SELECT
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[CompanyName] AS [CompanyName],
[Extent1].[ContactName] AS [ContactName],
[Extent1].[ContactTitle] AS [ContactTitle],
[Extent1].[Address] AS [Address],
[Extent1].[City] AS [City],
[Extent1].[Region] AS [Region],
[Extent1].[PostalCode] AS [PostalCode],
[Extent1].[Country] AS [Country],
[Extent1].[Phone] AS [Phone],
[Extent1].[Fax] AS [Fax]
FROM [dbo].[Customers] AS [Extent1]
WHERE N'UK' = [Extent1].[Country]
```

Et chaque fois que vous accédez à la propriété de navigation Commandes d’un client une autre requête comme ce qui suit est émis contre le magasin:

``` SQL
exec sp_executesql N'SELECT
[Extent1].[OrderID] AS [OrderID],
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[EmployeeID] AS [EmployeeID],
[Extent1].[OrderDate] AS [OrderDate],
[Extent1].[RequiredDate] AS [RequiredDate],
[Extent1].[ShippedDate] AS [ShippedDate],
[Extent1].[ShipVia] AS [ShipVia],
[Extent1].[Freight] AS [Freight],
[Extent1].[ShipName] AS [ShipName],
[Extent1].[ShipAddress] AS [ShipAddress],
[Extent1].[ShipCity] AS [ShipCity],
[Extent1].[ShipRegion] AS [ShipRegion],
[Extent1].[ShipPostalCode] AS [ShipPostalCode],
[Extent1].[ShipCountry] AS [ShipCountry]
FROM [dbo].[Orders] AS [Extent1]
WHERE [Extent1].[CustomerID] = @EntityKeyValue1',N'@EntityKeyValue1 nchar(5)',@EntityKeyValue1=N'AROUT'
```

Pour plus d’informations, voir les objets liés au [chargement](https://msdn.microsoft.com/library/bb896272.aspx).

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 Chargement paresseux par rapport à la feuille de triche de chargement impatient

Il n’y a pas une telle chose comme une taille unique-fits-all à choisir le chargement avide par rapport au chargement paresseux. Essayez d’abord de comprendre les différences entre les deux stratégies afin que vous puissiez prendre une décision éclairée; aussi, considérez si votre code s’adapte à l’un des scénarios suivants :

| Scénario                                                                    | Notre suggestion                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Avez-vous besoin d’accéder à de nombreuses propriétés de navigation auprès des entités récupérées? | **Non** - Les deux options feront probablement l’affaire. Toutefois, si la charge utile que votre requête apporte n’est pas trop grande, vous pouvez éprouver des avantages de performance en utilisant le chargement eager car il faudra moins d’allers-retours réseau pour matérialiser vos objets. <br/> <br/> **Oui** - Si vous avez besoin d’accéder à de nombreuses propriétés de navigation à partir des entités, vous le feriez en utilisant plusieurs instructions inclure dans votre requête avec le chargement impatient. Plus vous incluez d’entités, plus votre requête retournera de charge utile. Une fois que vous incluez trois entités ou plus dans votre requête, envisagez de passer au chargement paresseux. |
| Savez-vous exactement quelles données seront nécessaires au moment de l’exécution?                   | **Non** - Le chargement paresseux sera meilleur pour vous. Sinon, vous pouvez finir par interroger pour des données dont vous n’aurez pas besoin. <br/> <br/> **Oui** - Le chargement impatient est probablement votre meilleur pari; il aidera à charger des ensembles entiers plus rapidement. Si votre requête nécessite d’aller chercher une très grande quantité de données, et cela devient trop lent, puis essayez le chargement paresseux à la place.                                                                                                                                                                                                                                                       |
| Votre code est-il loin de votre base de données ? (latence réseau accrue)  | **Non** - Lorsque la latence du réseau n’est pas un problème, l’utilisation du chargement paresseux peut simplifier votre code. N’oubliez pas que la topologie de votre application peut changer, alors ne prenez pas la proximité de la base de données pour acquise. <br/> <br/> **Oui** - Lorsque le réseau est un problème, vous seul pouvez décider ce qui convient le mieux à votre scénario. Typiquement Le chargement impatient sera meilleur parce qu’il nécessite moins d’allers-retours.                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>8.2.2 Préoccupations en matière de rendement avec plusieurs

Lorsque nous entendons des questions de performance qui impliquent des problèmes de temps de réponse du serveur, la source du problème est fréquemment requête avec plusieurs instructions Inclure. Bien que l’inclusion d’entités connexes dans une requête est puissante, il est important de comprendre ce qui se passe sous les couvertures.

Il faut relativement longtemps pour qu’une requête avec plusieurs déclarations incluent en elle pour passer par notre compilateur de plan interne pour produire la commande de magasin. La majorité de ce temps est consacré à essayer d’optimiser la requête résultante. La commande générée du magasin contiendra une jointure extérieure ou une union pour chaque inclure, selon votre cartographie. Des requêtes comme celle-ci apporteront de grands graphiques connectés à partir de votre base de données dans une seule charge utile, ce qui permettra d’acerbate tous les problèmes de bande passante, surtout quand il ya beaucoup de redondance dans la charge utile (par exemple, lorsque plusieurs niveaux de inclure sont utilisés pour traverser les associations dans la direction à un à plusieurs).

Vous pouvez vérifier les cas où vos requêtes retournent des charges utiles excessivement importantes en accédant à la TSQL sous-jacente pour la requête en utilisant ToTraceString et en exécutant la commande du magasin dans SQL Server Management Studio pour voir la taille de la charge utile. Dans de tels cas, vous pouvez essayer de réduire le nombre d’instructions inclure dans votre requête pour simplement apporter les données dont vous avez besoin. Ou vous pouvez être en mesure de briser votre requête en une plus petite séquence de sous-ques, par exemple:

**Avant de casser la requête:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var customers = from c in context.Customers.Include(c => c.Orders)
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

**Après avoir cassé la requête:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var orders = from o in context.Orders
                 where o.Customer.LastName.StartsWith(lastNameParameter)
                 select o;

    orders.Load();

    var customers = from c in context.Customers
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

Cela ne fonctionnera que sur les requêtes suivies, car nous utilisons automatiquement la capacité du contexte à effectuer une résolution d’identité et un correctif d’association.

Comme pour le chargement paresseux, le compromis sera plus de requêtes pour les charges utiles plus petites. Vous pouvez également utiliser des projections de propriétés individuelles pour sélectionner explicitement uniquement les données dont vous avez besoin de chaque entité, mais vous ne serez pas des entités de chargement dans ce cas, et les mises à jour ne seront pas prises en charge.

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 Solution de contournement pour obtenir le chargement paresseux des propriétés

Entity Framework ne prend actuellement pas en charge le chargement paresseux de propriétés scalaires ou complexes. Toutefois, dans les cas où vous avez un tableau qui comprend un grand objet tel qu’un BLOB, vous pouvez utiliser le fractionnement de la table pour séparer les grandes propriétés en une entité distincte. Supposons, par exemple, que vous ayez une table de produit qui comprend une colonne de photo varbinaire. Si vous n’avez pas souvent besoin d’accéder à cette propriété dans vos requêtes, vous pouvez utiliser le fractionnement de table pour apporter uniquement les parties de l’entité dont vous avez normalement besoin. L’entité représentant la photo du produit ne sera chargée que lorsque vous en aurez explicitement besoin.

Une bonne ressource qui montre comment permettre le fractionnement de table est \< http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>Gil Fink "Table Splitting in Entity Framework" blog post: .

## <a name="9-other-considerations"></a>9 Autres considérations

### <a name="91-server-garbage-collection"></a>9.1 Collecte d’ordures de serveur

Certains utilisateurs peuvent faire l’expérience d’une contention de ressources qui limite le parallélisme qu’ils attendent lorsque le collecteur d’ordures n’est pas correctement configuré. Chaque fois que EF est utilisé dans un scénario multithreaded, ou dans toute application qui ressemble à un système côté serveur, assurez-vous d’activer Server Garbage Collection. Ceci est fait via un réglage simple dans votre fichier config d’application :

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

Cela devrait diminuer votre contention de thread et augmenter votre débit jusqu’à 30% dans les scénarios saturés CPU. En termes généraux, vous devriez toujours tester comment votre application se comporte en utilisant la collection classique d’ordures (qui est mieux réglée pour l’interface utilisateur et les scénarios côté client) ainsi que la collection d’ordures de serveur.

### <a name="92-autodetectchanges"></a>9.2 AutoDetectChanges

Comme mentionné précédemment, Entity Framework peut afficher des problèmes de performance lorsque le cache d’objets a de nombreuses entités. Certaines opérations, telles que Add, Remove, Find, Entry and SaveChanges, déclenchent des appels vers DetectChanges qui pourraient consommer une grande quantité de processeur en fonction de l’ampleur de la cache d’objet est devenue. La raison en est que le cache d’objet et le gestionnaire d’état d’objet essaient de rester aussi synchronisés que possible sur chaque opération effectuée dans un contexte de sorte que les données produites sont garanties d’être correctes dans un large éventail de scénarios.

Il est généralement une bonne pratique de laisser la détection automatique des modifications d’Entity Framework activée pendant toute la durée de vie de votre application. Si votre scénario est affecté négativement par une utilisation élevée du processeur et que vos profils indiquent que le coupable est l’appel à DetectChanges, envisagez d’éteindre temporairement AutoDetectChanges dans la partie sensible de votre code :

``` csharp
try
{
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    ...
}
finally
{
    context.Configuration.AutoDetectChangesEnabled = true;
}
```

Avant d’éteindre AutoDetectChanges, il est bon de comprendre que cela pourrait faire de l’entité Cadre de perdre sa capacité à suivre certaines informations sur les changements qui ont lieu sur les entités. Si elle est mal traitée, cela peut entraîner une incohérence dans les données sur votre application. Pour plus d’informations sur l’arrêt \< http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>AutoDetectChanges, lisez .

### <a name="93-context-per-request"></a>9.3 Contexte par demande

Les contextes de Entity Framework sont censés être utilisés comme des cas de courte durée afin de fournir l’expérience de performance la plus optimale. On s’attend à ce que les contextes soient de courte durée et jetés, et en tant que tels ont été mis en œuvre pour être très légers et réutiliser les métadonnées dans la mesure du possible. Dans les scénarios Web, il est important de garder cela à l’esprit et de ne pas avoir un contexte pour plus que la durée d’une seule demande. De même, dans les scénarios non web, le contexte doit être écarté en fonction de votre compréhension des différents niveaux de mise en cache dans le Cadre d’entité. D’une manière générale, il faut éviter d’avoir un exemple de contexte tout au long de la durée de l’application, ainsi que des contextes par thread et des contextes statiques.

### <a name="94-database-null-semantics"></a>9.4 Sémantique nulle de base de données

Entity Framework par défaut générera le\# code SQL qui a la sémantique de comparaison nulle C. Prenons l'exemple de requête suivant :

``` csharp
            int? categoryId = 7;
            int? supplierId = 8;
            decimal? unitPrice = 0;
            short? unitsInStock = 100;
            short? unitsOnOrder = 20;
            short? reorderLevel = null;

            var q = from p incontext.Products
                    wherep.Category.CategoryName == "Beverages"
                          || (p.CategoryID == categoryId
                                || p.SupplierID == supplierId
                                || p.UnitPrice == unitPrice
                                || p.UnitsInStock == unitsInStock
                                || p.UnitsOnOrder == unitsOnOrder
                                || p.ReorderLevel == reorderLevel)
                    select p;

            var r = q.ToList();
```

Dans cet exemple, nous comparons un certain nombre de variables inquérables par rapport aux propriétés inquérables sur l’entité, telles que SupplierID et UnitPrice. Le SQL généré pour cette requête demandera si la valeur de paramètre est la même que la valeur de colonne, ou si le paramètre et les valeurs de colonne sont nuls. Cela cachera la façon dont le serveur de base\# de données gère les nulls et fournira une expérience cohérente C null à travers différents fournisseurs de base de données. D’autre part, le code généré est un peu alambiqué et peut ne pas fonctionner bien lorsque le nombre de comparaisons dans l’endroit où l’énoncé de la requête se développe à un grand nombre.

Une façon de faire face à cette situation est d’utiliser la sémantique nulle base de données. Notez que cela pourrait potentiellement\# se comporter différemment de la sémantique nulle C depuis maintenant Entity Framework va générer SQL plus simple qui expose la façon dont le moteur de base de données gère les valeurs nulles. La sémantique nulle de base de données peut être activée par contexte avec une seule ligne de configuration par rapport à la configuration contextuelle :

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

Les requêtes de petite et moyenne taille n’afficheront pas une amélioration perceptible des performances lors de l’utilisation de la sémantique nulle dans la base de données, mais la différence deviendra plus perceptible sur les requêtes avec un grand nombre de comparaisons nulles potentielles.

Dans l’exemple de requête ci-dessus, la différence de performance était inférieure à 2 % dans une microbille fonctionnant dans un environnement contrôlé.

### <a name="95-async"></a>9.5 Async

Entity Framework 6 a introduit le soutien des opérations async lors de l’exécution sur .NET 4.5 ou plus tard. Pour la plupart, les applications qui ont une prétention liée à l’OI bénéficieront le plus de l’utilisation de requêtes asynchrones et sauver les opérations. Si votre demande ne souffre pas de la prétention de l’OI, l’utilisation d’async s’exécutera, dans le meilleur des cas, de façon synchrone et retournera le résultat dans le même laps de temps qu’un appel synchrone, ou dans le pire des cas, il suffit de reporter l’exécution à une tâche asynchrone et ajoutera du temps supplémentaire à l’achèvement de votre scénario.

Pour plus d’informations sur le fonctionnement de la programmation asynchrone qui vous [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)aidera à décider si async améliorera les performances de votre visite d’application . Pour plus d’informations sur l’utilisation des opérations async sur Entity Framework, voir [Async Query et Save](~/ef6/fundamentals/async.md
).

### <a name="96-ngen"></a>9.6 NGEN

Entity Framework 6 ne vient pas dans l’installation par défaut du cadre .NET. En tant que tel, les assemblées du Cadre d’entité ne sont pas NGEN’d par défaut, ce qui signifie que tout le code cadre d’entité est soumis aux mêmes coûts JIT’ing que n’importe quel autre assemblage MSIL. Cela pourrait dégrader l’expérience F5 tout en développant et aussi le démarrage à froid de votre application dans les environnements de production. Afin de réduire les coûts de CPU et de mémoire de JIT’ing, il est conseillé à NGEN les images du Cadre d’entité, le cas échéant. Pour plus d’informations sur la façon d’améliorer les performances de démarrage de Entity Framework 6 avec NGEN, voir [Améliorer les performances de démarrage avec NGen](~/ef6/fundamentals/performance/ngen.md).

### <a name="97-code-first-versus-edmx"></a>9.7 Code d’abord contre EDMX

Raisons du cadre de l’entité au sujet du problème d’inadéquation d’impedance entre la programmation orientée objet et les bases de données relationnelles en ayant une représentation en mémoire du modèle conceptuel (les objets), le schéma de stockage (la base de données) et une cartographie entre les deux. Ces métadonnées sont appelées un modèle de données d’entité, ou EDM pour faire court. De ce EDM, Entity Framework tirera les vues pour aller-retour des données des objets en mémoire à la base de données et retour.

Lorsque le cadre d’entité est utilisé avec un fichier EDMX qui spécifie formellement le modèle conceptuel, le schéma de stockage et la cartographie, alors l’étape de chargement du modèle n’a qu’à valider que l’EDM est correcte (par exemple, assurez-vous qu’aucune cartographie ne manque), puis générer les vues, puis valider les vues et avoir ces métadonnées prêtes à l’emploi. Ce n’est qu’à ce moment-là qu’une requête peut être exécutée ou que de nouvelles données seront enregistrées dans le magasin de données.

L’approche Code First est, en son cœur, un générateur sophistiqué de modèle de données d’entité. Le Cadre de l’entité doit produire un EDM à partir du code fourni; il le fait en analysant les classes impliquées dans le modèle, en appliquant des conventions et en configurant le modèle via l’API fluente. Une fois le EDM construit, le Cadre d’entité se comporte essentiellement de la même façon qu’un fichier EDMX présent dans le projet. Ainsi, la construction du modèle à partir de Code First ajoute une complexité supplémentaire qui se traduit par un temps de démarrage plus lent pour le cadre d’entité par rapport à avoir un EDMX. Le coût dépend entièrement de la taille et de la complexité du modèle qui est en cours de construction.

Lorsque vous choisissez d’utiliser EDMX par rapport à Code First, il est important de savoir que la flexibilité introduite par Code First augmente le coût de construction du modèle pour la première fois. Si votre application peut supporter le coût de cette première charge, le code d’abord sera généralement la voie privilégiée pour aller.

## <a name="10-investigating-performance"></a>10 Exécution d’enquête

### <a name="101-using-the-visual-studio-profiler"></a>10.1 Utilisation du Visual Studio Profiler

Si vous rencontrez des problèmes de performance avec le cadre d’entité, vous pouvez utiliser un profileur comme celui intégré dans Visual Studio pour voir où votre application passe son temps. C’est l’outil que nous avons utilisé pour générer les cartes à secteurs dans le \< https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>) "Exploring the Performance of the ADO.NET Entity Framework - Part 1" blog post (qui montrent où Entity Framework passe son temps pendant les requêtes froides et chaudes.

Le blog "Profiling Entity Framework utilisant le Visual Studio 2010 Profiler" écrit par l’équipe consultative des clients de données et de modélisation montre un exemple concret de la façon dont ils ont utilisé le profileur pour enquêter sur un problème de performance.\<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>. Ce message a été écrit pour une application Windows. Si vous avez besoin de profiler une application Web, les outils Windows Performance Recorder (WPR) et Windows Performance Analyzer (WPA) peuvent mieux fonctionner que de travailler à partir de Visual Studio. WPR et WPA font partie de la boîte à outils de [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)performance Windows qui est inclus dans la trousse d’évaluation et de déploiement Windows ( ).

### <a name="102-applicationdatabase-profiling"></a>10.2 Profilage d’application/base de données

Des outils comme le profileur intégré dans Visual Studio vous indiquent où votre application passe du temps.Un autre type de profileur est disponible qui effectue une analyse dynamique de votre application en cours d’exécution, soit en production ou en pré-production en fonction des besoins, et recherche des pièges communs et des anti-modèles d’accès à la base de données.

Deux profileurs disponibles dans le commerce \< http://efprof.com>) sont l’Entité \< http://ormprofiler.com>)Framework Profiler ( et ORMProfiler ( .

Si votre application est une application MVC utilisant Code First, vous pouvez utiliser le MiniProfiler de StackExchange. Scott Hanselman décrit cet outil dans \< http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>son blog à: .

Pour plus d’informations sur le profilage de l’activité de base de données de votre application, consultez l’article de Julie Lerman intitulé [Profiling Database Activity in the Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).

### <a name="103-database-logger"></a>10.3 Enregistreur de base de données

Si vous utilisez Entity Framework 6, envisagez également d’utiliser la fonctionnalité d’enregistrement intégrée. La propriété de base de données du contexte peut être chargée de enregistrer son activité via une configuration simple d’une seule ligne :

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

Dans cet exemple, l’activité de base de données sera connectée à la&lt;&gt; console, mais la propriété Log peut être configurée pour appeler n’importe quel délégué à la chaîne Action.

Si vous souhaitez activer l’enregistrement de base de données sans recompilation, et que vous utilisez Entity Framework 6.1 ou plus tard, vous pouvez le faire en ajoutant un intercepteur dans le fichier web.config ou app.config de votre application.

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

Pour plus d’informations sur la façon d’ajouter la connexion sans recompiler aller à \< http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>.

## <a name="11-appendix"></a>11 Annexe

### <a name="111-a-test-environment"></a>11.1 A. Environnement d’essai

Cet environnement utilise une configuration 2 machines avec la base de données sur une machine distincte de l’application client. Les machines sont dans le même rack, de sorte que la latence réseau est relativement faible, mais plus réaliste qu’un environnement à machine unique.

#### <a name="1111-app-server"></a>11.1.1 Serveur d’application

##### <a name="11111-software-environment"></a>11.1.1.1 Environnement logiciel

-   Cadre d’entité 4 Environnement logiciel
    -   OS Nom: Windows Server 2008 R2 Enterprise SP1.
    -   Visual Studio 2010 - Ultimate.
    -   Visual Studio 2010 SP1 (seulement pour quelques comparaisons).
-   Cadre d’entité 5 et 6 Environnement logiciel
    -   Nom OS: Windows 8.1 Enterprise
    -   Visual Studio 2013 - Ultimate.

##### <a name="11112-hardware-environment"></a>11.1.1.2 Environnement matériel

-   Double processeur: Intel(R) Xeon(R) CPU L5520 W3530 - 2.27GHz, 2261 Mhz8 GHz, 4 Core(s), 84 Logical Processor(s).
-   2412 Go RamRAM.
-   136 Go SCSI250Gb SATA 7200 tr/min 3 Go/s disque divisé en 4 partitions.

#### <a name="1112-db-server"></a>11.1.2 Serveur DB

##### <a name="11121-software-environment"></a>11.1.2.1 Environnement logiciel

-   Nom OS: Windows Server 2008 R28.1 Enterprise SP1.
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>11.1.2.2 Environnement matériel

-   Processeur unique: Intel(R) Xeon(R) CPU L5520 - 2.27GHz, 2261 MhzES-1620 0 - 3.60GHz, 4 Core(s), 8 Logical Processor(s).
-   824 Go RamRAM.
-   465 Go ATA500Gb SATA 7200 tr/min 6 Go/s drive divisé en 4 partitions.

### <a name="112-b-query-performance-comparison-tests"></a>11.2 B. Tests de comparaison des performances de Requête

Le modèle Northwind a été utilisé pour exécuter ces tests. Il a été généré à partir de la base de données à l’aide du concepteur du cadre d’entité. Ensuite, le code suivant a été utilisé pour comparer les performances des options d’exécution de requête :

``` csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Data.Objects;
using System.Linq;

namespace QueryComparison
{
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
                );

        public IQueryable<Product> InvokeProductsForCategoryCQ(string categoryName)
        {
            return productsForCategoryCQ(this, categoryName);
        }
    }

    public class QueryTypePerfComparison
    {
        private static string entityConnectionStr = @"metadata=res://*/Northwind.csdl|res://*/Northwind.ssdl|res://*/Northwind.msl;provider=System.Data.SqlClient;provider connection string='data source=.;initial catalog=Northwind;integrated security=True;multipleactiveresultsets=True;App=EntityFramework'";

        public void LINQIncludingContextCreation()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTracking()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                context.Products.MergeOption = MergeOption.NoTracking;

                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void CompiledQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                var q = context.InvokeProductsForCategoryCQ("Beverages");
                q.ToList();
            }
        }

        public void ObjectQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
                products.ToList();
            }
        }

        public void EntityCommand()
        {
            using (EntityConnection eConn = new EntityConnection(entityConnectionStr))
            {
                eConn.Open();
                EntityCommand cmd = eConn.CreateCommand();
                cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

                using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
                {
                    List<Product> productsList = new List<Product>();
                    while (reader.Read())
                    {
                        DbDataRecord record = (DbDataRecord)reader.GetValue(0);

                        // 'materialize' the product by accessing each field and value. Because we are materializing products, we won't have any nested data readers or records.
                        int fieldCount = record.FieldCount;

                        // Treat all products as Product, even if they are the subtype DiscontinuedProduct.
                        Product product = new Product();  

                        product.ProductID = record.GetInt32(0);
                        product.ProductName = record.GetString(1);
                        product.SupplierID = record.GetInt32(2);
                        product.CategoryID = record.GetInt32(3);
                        product.QuantityPerUnit = record.GetString(4);
                        product.UnitPrice = record.GetDecimal(5);
                        product.UnitsInStock = record.GetInt16(6);
                        product.UnitsOnOrder = record.GetInt16(7);
                        product.ReorderLevel = record.GetInt16(8);
                        product.Discontinued = record.GetBoolean(9);

                        productsList.Add(product);
                    }
                }
            }
        }

        public void ExecuteStoreQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectResult<Product> beverages = context.ExecuteStoreQuery<Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Database.SqlQuery\<QueryComparison.DbC.Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbSet()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Products.SqlQuery(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void LINQIncludingContextCreationDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTrackingDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var q = context.Products.AsNoTracking().Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }
    }
}
```

### <a name="113-c-navision-model"></a>Modèle 11.3 C. Navision

La base de données Navision est une grande base de données utilisée pour démor microsoft Dynamics - NAV. Le modèle conceptuel généré contient 1005 ensembles d’entités et 4227 ensembles d’associations. Le modèle utilisé dans le test est "plat" - aucun héritage n’y a été ajouté.

#### <a name="1131-queries-used-for-navision-tests"></a>11.3.1 Requêtes utilisées pour les tests Navision

La liste des requêtes utilisée avec le modèle Navision contient 3 catégories de requêtes d’entité SQL :

##### <a name="11311-lookup"></a>11.3.1.1 Lookup

Une simple requête de recherche sans agrégations

-   Nombre: 16232
-   Exemple :

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 Un seul agrégatage

Une requête BI normale avec de multiples agrégations, mais pas de sous-sols (requête unique)

-   Nombre: 2313
-   Exemple :

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

Lorsque MDF\_SessionLogin\_Time\_Max () est défini dans le modèle comme:

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 Agrégateurs d’agrégation

Une requête BI avec agrégations et sous-sols (via le syndicat tous)

-   Nombre: 178
-   Exemple :

``` xml
  <Query complexity="AggregatingSubtotals">
    <CommandText>
using NavisionFK;
function AmountConsumed(entities Collection([CRONUS_International_Ltd__Zone])) as
(
    Edm.Sum(select value N.Block_Movement FROM entities as E, E.CRONUS_International_Ltd__Bin as N)
)
function AmountConsumed(P1 Edm.Int32) as
(
    AmountConsumed(select value e from NavisionFKContext.CRONUS_International_Ltd__Zone as e where e.Zone_Ranking = P1)
)
----------------------------------------------------------------------------------------------------------------------
(
    select top(10) Zone_Ranking, Cross_Dock_Bin_Zone, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking, E.Cross_Dock_Bin_Zone
)
union all
(
    select top(10) Zone_Ranking, Cast(null as Edm.Byte) as P2, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking
)
union all
{
    Row(Cast(null as Edm.Int32) as P1, Cast(null as Edm.Byte) as P2, AmountConsumed(select value E
                                                                         from NavisionFKContext.CRONUS_International_Ltd__Zone as E
                                                                         where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed))
}</CommandText>
    <Parameters>
      <Parameter Name="MinAmountConsumed" DbType="Int32" Value="10000" />
    </Parameters>
  </Query>
```
