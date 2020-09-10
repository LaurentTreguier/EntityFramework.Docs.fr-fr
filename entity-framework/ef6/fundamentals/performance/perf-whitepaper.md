---
title: Considérations relatives aux performances pour EF4, EF5 et EF6-EF6
description: Considérations relatives aux performances pour les Entity Framework 4, 5 et 6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
uid: ef6/fundamentals/performance/perf-whitepaper
ms.openlocfilehash: 9d70eab61caace02f59f3c555ef416c45d4f8f45
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616175"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>Considérations relatives aux performances pour EF 4, 5 et 6
Par David Obando, Eric Dettinger et autres

Publication : 2012 avril

Dernière mise à jour : mai 2014

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. Introduction

Les infrastructures de mappage objet-relationnel sont un moyen pratique de fournir une abstraction pour l’accès aux données dans une application orientée objet. Pour les applications .NET, l’O/RM recommandé de Microsoft est Entity Framework. Malgré toute abstraction, les performances peuvent devenir un problème.

Ce livre blanc a été rédigé pour illustrer les considérations relatives aux performances lors du développement d’applications à l’aide de Entity Framework, afin de donner aux développeurs une idée des algorithmes internes Entity Framework qui peuvent affecter les performances et de fournir des conseils pour l’investigation et l’amélioration des performances dans leurs applications qui utilisent Entity Framework. Il existe un certain nombre de bonnes rubriques sur les performances déjà disponibles sur le Web et nous avons également essayé de pointer vers ces ressources dans la mesure du possible.

Les performances sont un sujet délicat. Ce livre blanc est destiné à vous aider à prendre des décisions relatives aux performances de vos applications qui utilisent Entity Framework. Nous avons inclus des mesures de test pour illustrer les performances, mais ces mesures ne sont pas conçues comme indicateurs absolus des performances que vous verrez dans votre application.

Pour des raisons pratiques, ce document suppose Entity Framework 4 s’exécute sous .NET 4,0 et Entity Framework 5 et 6 sont exécutés sous .NET 4,5. La plupart des améliorations de performances apportées à Entity Framework 5 résident dans les composants de base fournis avec .NET 4,5.

Entity Framework 6 est une version hors bande et ne dépend pas des composants Entity Framework fournis avec .NET. Entity Framework 6 fonctionnent à la fois sur .NET 4,0 et .NET 4,5, et peuvent offrir un gain de performances considérable à ceux qui n’ont pas été mis à niveau à partir de .NET 4,0, mais qui veulent les derniers Entity Framework bits dans leur application. Lorsque ce document mentionne Entity Framework 6, il fait référence à la dernière version disponible au moment de la rédaction de cet article : version 6.1.0.

## <a name="2-cold-vs-warm-query-execution"></a>2. exécution des requêtes à froid et à chaud

La première fois qu’une requête est effectuée sur un modèle donné, le Entity Framework fait beaucoup de travail en arrière-plan pour charger et valider le modèle. Nous faisons souvent référence à cette première requête sous la forme d’une requête « à froid ».Les requêtes supplémentaires sur un modèle déjà chargé sont appelées requêtes « chaudes » et sont beaucoup plus rapides.

Examinons de plus haut niveau le temps consacré à l’exécution d’une requête à l’aide de Entity Framework, et voyons où les choses s’améliorent dans Entity Framework 6.

**Exécution de la première requête-requête à froid**

| Écritures utilisateur du code                                                                                     | Action                    | Impact sur les performances de EF4                                                                                                                                                                                                                                                                                                                                                                                                        | Impact sur les performances de EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Impact sur les performances de EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Création de contexte          | Moyenne                                                                                                                                                                                                                                                                                                                                                                                                                        | Moyenne                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Création d’une expression de requête | Faible                                                                                                                                                                                                                                                                                                                                                                                                                           | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | Exécution de requêtes LINQ      | -Chargement des métadonnées : élevé mais mis en cache <br/> -Génération de vues : potentiellement très élevée mais mise en cache <br/> -Évaluation des paramètres : moyenne <br/> -Traduction de requête : moyenne <br/> -Génération de matérialise : moyenne mais mise en cache <br/> -Exécution de la requête de base de données : potentiellement élevé <br/> + Connection. Open <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Matérialisation d’objets : moyenne <br/> -Recherche d’identité : moyenne | -Chargement des métadonnées : élevé mais mis en cache <br/> -Génération de vues : potentiellement très élevée mais mise en cache <br/> -Évaluation des paramètres : faible <br/> -Traduction de requête : moyenne mais mise en cache <br/> -Génération de matérialise : moyenne mais mise en cache <br/> -Exécution des requêtes de base de données : potentiellement élevé (meilleures requêtes dans certaines situations) <br/> + Connection. Open <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Matérialisation d’objets : moyenne <br/> -Recherche d’identité : moyenne | -Chargement des métadonnées : élevé mais mis en cache <br/> -Génération de vues : moyenne mais mise en cache <br/> -Évaluation des paramètres : faible <br/> -Traduction de requête : moyenne mais mise en cache <br/> -Génération de matérialise : moyenne mais mise en cache <br/> -Exécution des requêtes de base de données : potentiellement élevé (meilleures requêtes dans certaines situations) <br/> + Connection. Open <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Matérialisation d’objets : moyenne (plus rapide que EF5) <br/> -Recherche d’identité : moyenne |
| `}`                                                                                                  | Connection. Close          | Faible                                                                                                                                                                                                                                                                                                                                                                                                                           | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**Exécution de la deuxième requête-requête à chaud**

| Écritures utilisateur du code                                                                                     | Action                    | Impact sur les performances de EF4                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Impact sur les performances de EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Impact sur les performances de EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Création de contexte          | Moyenne                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Moyenne                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Création d’une expression de requête | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | Exécution de requêtes LINQ      | -Recherche ~~du chargement~~ des métadonnées : ~~haute, mais mise en cache~~ faible <br/> -Afficher la recherche de ~~génération~~ : ~~potentiellement très élevé mais mis en cache~~ faible <br/> -Évaluation des paramètres : moyenne <br/> -Recherche de ~~traduction~~ de requête : moyenne <br/> -Recherche de la ~~génération~~ de matérialise : ~~moyenne mais mise en cache~~ faible <br/> -Exécution de la requête de base de données : potentiellement élevé <br/> + Connection. Open <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Matérialisation d’objets : moyenne <br/> -Recherche d’identité : moyenne | -Recherche ~~du chargement~~ des métadonnées : ~~haute, mais mise en cache~~ faible <br/> -Afficher la recherche de ~~génération~~ : ~~potentiellement très élevé mais mis en cache~~ faible <br/> -Évaluation des paramètres : faible <br/> -Recherche de ~~traduction~~ de requête : ~~moyenne mais mise en cache~~ faible <br/> -Recherche de la ~~génération~~ de matérialise : ~~moyenne mais mise en cache~~ faible <br/> -Exécution des requêtes de base de données : potentiellement élevé (meilleures requêtes dans certaines situations) <br/> + Connection. Open <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Matérialisation d’objets : moyenne <br/> -Recherche d’identité : moyenne | -Recherche ~~du chargement~~ des métadonnées : ~~haute, mais mise en cache~~ faible <br/> -Afficher la recherche de ~~génération~~ : ~~moyenne mais mise en cache~~ faible <br/> -Évaluation des paramètres : faible <br/> -Recherche de ~~traduction~~ de requête : ~~moyenne mais mise en cache~~ faible <br/> -Recherche de la ~~génération~~ de matérialise : ~~moyenne mais mise en cache~~ faible <br/> -Exécution des requêtes de base de données : potentiellement élevé (meilleures requêtes dans certaines situations) <br/> + Connection. Open <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Matérialisation d’objets : moyenne (plus rapide que EF5) <br/> -Recherche d’identité : moyenne |
| `}`                                                                                                  | Connection. Close          | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Faible                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


Il existe plusieurs façons de réduire le coût de performance des requêtes à froid et à chaud, et nous examinerons ces éléments dans la section suivante. Plus précisément, nous examinerons la réduction du coût du chargement de modèle dans les requêtes à froid en utilisant des vues prégénérées, ce qui devrait aider à atténuer les problèmes de performances rencontrés lors de la génération de vues. Pour les requêtes à chaud, nous allons aborder la mise en cache des plans de requête, les requêtes de suivi et les différentes options d’exécution de requête.

### <a name="21-what-is-view-generation"></a>2,1 qu’est-ce que la génération de vues ?

Pour comprendre la génération de vues, nous devons d’abord comprendre ce que sont les « vues de mappage ». Les vues de mappage sont des représentations exécutables des transformations spécifiées dans le mappage pour chaque jeu d’entités et Association. En interne, ces vues de mappage prennent la forme de CQTs (arborescences de requêtes canoniques). Il existe deux types de vues de mappage :

-   Vues des requêtes : elles représentent la transformation nécessaire pour passer du schéma de base de données au modèle conceptuel.
-   Vues de mise à jour : elles représentent la transformation nécessaire pour passer du modèle conceptuel au schéma de base de données.

Gardez à l’esprit que le modèle conceptuel peut différer du schéma de base de données de différentes façons. Par exemple, une seule table peut être utilisée pour stocker les données de deux types d’entités différents. Les mappages d’héritage et non trivial jouent un rôle dans la complexité des vues de mappage.

Le processus de calcul de ces vues en fonction de la spécification du mappage est ce que nous appelons la génération de vues. La génération de vues peut être exécutée dynamiquement lorsqu’un modèle est chargé, ou au moment de la génération, à l’aide de « vues prégénérées »; ces derniers sont sérialisés sous la forme d’instructions Entity SQL dans un \# fichier C ou VB.

Lorsque des vues sont générées, elles sont également validées. Du point de vue des performances, la grande majorité du coût de la génération de vues est la validation des vues, qui garantit que les connexions entre les entités ont un sens et qu’elles disposent de la cardinalité correcte pour toutes les opérations prises en charge.

Lorsqu’une requête sur un jeu d’entités est exécutée, la requête est associée à la vue de requête correspondante, et le résultat de cette composition est exécuté via le compilateur de plan pour créer la représentation de la requête que le magasin de stockage peut comprendre. Par SQL Server, le résultat final de cette compilation sera une instruction T-SQL SELECT. La première fois qu’une mise à jour est effectuée sur un jeu d’entités, la vue de mise à jour est exécutée à l’aide d’un processus similaire pour la transformer en instructions DML pour la base de données cible.

### <a name="22-factors-that-affect-view-generation-performance"></a>2,2 facteurs qui affectent les performances de la génération de vues

Les performances de l’étape de génération d’affichage dépendent non seulement de la taille de votre modèle, mais également de la manière dont le modèle est interconnecté. Si deux entités sont connectées via une chaîne d’héritage ou une association, elles sont dites connectées. De même, si deux tables sont connectées via une clé étrangère, elles sont connectées. À mesure que le nombre d’entités et de tables connectées dans vos schémas augmente, le coût de la génération d’affichage augmente.

L’algorithme que nous utilisons pour générer et valider des vues est exponentiel dans le pire des cas, bien que nous utilisons des optimisations pour améliorer cela. Les facteurs les plus importants qui semblent nuire aux performances sont les suivants :

-   Taille du modèle, en faisant référence au nombre d’entités et à la quantité d’associations entre ces entités.
-   Complexité du modèle, en particulier l’héritage impliquant un grand nombre de types.
-   À l’aide d’associations indépendantes, plutôt que d’associations de clé étrangère.

Pour les petits modèles simples, le coût peut être suffisamment petit pour ne pas se soucier de l’utilisation de vues prégénérées. À mesure que la taille et la complexité du modèle augmentent, plusieurs options sont disponibles pour réduire le coût de la génération et de la validation de la vue.

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2,3 utilisation de vues prégénérées pour réduire le temps de chargement du modèle

Pour plus d’informations sur l’utilisation des affichages prégénérés sur Entity Framework 6, consultez [affichages de mappage prégénérés](xref:ef6/fundamentals/performance/pre-generated-views)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 affichages prégénérés à l’aide de Entity Framework Power Tools Community Edition

Vous pouvez utiliser l' [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) pour générer des vues de modèles EDMX et code First en cliquant avec le bouton droit sur le fichier de classe de modèle et en utilisant le menu Entity Framework pour sélectionner « générer des vues ». L’édition Community d’Entity Framework Power Tools ne fonctionne que sur les contextes dérivés de DbContext.

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 Comment utiliser des vues prégénérées avec un modèle créé par EDMGen

EDMGen est un utilitaire fourni avec .NET et fonctionne avec Entity Framework 4 et 5, mais pas avec Entity Framework 6. EDMGen vous permet de générer un fichier de modèle, la couche objet et les vues à partir de la ligne de commande. L’une des sorties est un fichier de vues dans le langage de votre choix, VB ou C \# . Il s’agit d’un fichier de code contenant Entity SQL extraits de code pour chaque jeu d’entités. Pour activer les vues prégénérées, il vous suffit d’inclure le fichier dans votre projet.

Si vous apportez manuellement des modifications aux fichiers de schéma pour le modèle, vous devrez générer à nouveau le fichier de vues. Pour ce faire, exécutez EDMGen avec l’indicateur **/mode : ViewGeneration** .

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 comment utiliser des vues prégénérées avec un fichier EDMX

Vous pouvez également utiliser EDMGen pour générer des vues pour un fichier EDMX : la rubrique MSDN précédemment référencée explique comment ajouter un événement pré-build pour effectuer cette opération, mais cela est complexe et dans certains cas, il n’est pas possible. Il est généralement plus facile d’utiliser un modèle T4 pour générer les vues lorsque votre modèle se trouve dans un fichier edmx.

Le blog de l’équipe ADO.NET contient un billet qui décrit comment utiliser un modèle T4 pour la génération de vues ( \<https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation> ). Ce billet comprend un modèle qui peut être téléchargé et ajouté à votre projet. Le modèle a été écrit pour la première version de Entity Framework. il n’est donc pas garanti qu’il fonctionne avec les dernières versions de Entity Framework. Toutefois, vous pouvez télécharger un ensemble plus à jour de modèles de génération d’affichage pour Entity Framework 4 et 5from la Galerie Visual Studio :

-   VB.NET : \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C \# : \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

Si vous utilisez Entity Framework 6, vous pouvez obtenir les modèles T4 de génération de vues à partir de la Galerie Visual Studio à l’adresse \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f> .

### <a name="24-reducing-the-cost-of-view-generation"></a>2,4 réduction du coût de la génération de vues

L’utilisation de vues prégénérées déplace le coût de la génération de vues du chargement du modèle (au moment de l’exécution) au moment de la conception. Bien que cela améliore les performances de démarrage lors de l’exécution, vous serez toujours confronté à la difficulté de la génération d’affichages pendant le développement. Il existe plusieurs astuces supplémentaires qui peuvent aider à réduire le coût de la génération de vues, à la fois au moment de la compilation et au moment de l’exécution.

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 utilisation d’associations de clé étrangère pour réduire le coût de la génération de vues

Nous avons vu un certain nombre de cas dans lesquels le basculement des associations de modèles indépendants vers des associations de clé étrangère a considérablement amélioré le temps passé dans la génération de vues.

Pour illustrer cette amélioration, nous avons généré deux versions du modèle Navision à l’aide d’EDMGen. *Remarque : consultez l’annexe C pour obtenir une description du modèle Navision.* Le modèle Navision est intéressant pour cet exercice en raison de son très grand nombre d’entités et de relations entre eux.

Une version de ce modèle très volumineux a été générée avec des associations de clés étrangères et l’autre a été générée avec des associations indépendantes. Nous avons ensuite dépassé le temps nécessaire à la génération des vues pour chaque modèle. Entity Framework 5 test a utilisé la méthode GenerateViews () de la classe EntityViewGenerator pour générer les vues, tandis que le test Entity Framework 6 a utilisé la méthode GenerateViews () à partir de la classe StorageMappingItemCollection. Ceci en raison de la restructuration du code qui s’est produite dans le Entity Framework 6 code base.

À l’aide de Entity Framework 5, la génération de vues pour le modèle avec des clés étrangères a duré 65 minutes sur un ordinateur de laboratoire. Il s’agit d’un nombre inconnu de la durée nécessaire pour générer les vues du modèle qui utilisaient des associations indépendantes. Nous avons laissé le test en cours d’exécution pendant plus d’un mois avant le redémarrage de l’ordinateur dans notre laboratoire pour installer les mises à jour mensuelles.

À l’aide de Entity Framework 6, la génération de la vue pour le modèle avec des clés étrangères a pris 28 secondes dans le même ordinateur Lab. La génération d’affichage pour le modèle qui utilise des associations indépendantes a duré 58 secondes. Les améliorations apportées à Entity Framework 6 sur son code de génération d’affichage signifient que de nombreux projets n’ont pas besoin de vues prégénérées pour obtenir des temps de démarrage plus rapides.

Il est important de noter que les affichages prégénérés dans Entity Framework 4 et 5 peuvent être réalisés avec EDMGen ou les outils d’Entity Framework Power Tools. Pour la génération de Entity Framework 6, la génération peut s’effectuer via les outils Entity Framework Power Tools ou par programme, comme décrit dans les [vues de mappage prégénérées](xref:ef6/fundamentals/performance/pre-generated-views).

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 comment utiliser des clés étrangères plutôt que des associations indépendantes

Lorsque vous utilisez EDMGen ou le Entity Designer dans Visual Studio, vous recevez clés étrangères par défaut, et il n’accepte qu’un seul indicateur de case à cocher ou de ligne de commande pour basculer entre clés étrangères et IAs.

Si vous avez un modèle de Code First volumineux, l’utilisation d’associations indépendantes aura le même effet sur la génération de la vue. Vous pouvez éviter cet impact en incluant des propriétés de clé étrangère sur les classes pour vos objets dépendants, bien que certains développeurs considèrent cela comme un modèle d’objet polluant. Vous trouverez plus d’informations sur ce sujet dans \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/> .

| Quand vous utilisez      | Action                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Concepteur d'entités | Après avoir ajouté une association entre deux entités, vérifiez que vous disposez d’une contrainte référentielle. Les contraintes référentielles indiquent Entity Framework utiliser des clés étrangères plutôt que des associations indépendantes. Pour plus d’informations \<https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework> , consultez. |
| EDMGen          | Lorsque vous utilisez EDMGen pour générer vos fichiers à partir de la base de données, vos clés étrangères sont respectées et ajoutées au modèle en tant que tel. Pour plus d’informations sur les différentes options exposées par l’outil EDMGen, consultez [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx) .                           |
| Code First      | Pour plus d’informations sur l’inclusion de propriétés de clé étrangère sur les objets dépendants lors de l’utilisation de Code First, consultez la section « Convention de relation » de la rubrique [conventions de code First](xref:ef6/modeling/code-first/conventions/built-in) .                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 déplacement de votre modèle vers un assembly distinct

Lorsque votre modèle est inclus directement dans le projet de votre application et que vous générez des vues par le biais d’un modèle d’événement pré-build ou T4, la génération et la validation de la vue sont effectuées chaque fois que le projet est régénéré, même si le modèle n’a pas été modifié. Si vous déplacez le modèle vers un assembly distinct et le référencez à partir du projet de votre application, vous pouvez apporter d’autres modifications à votre application sans avoir à régénérer le projet contenant le modèle.

*Remarque :*   lors du déplacement de votre modèle vers des assemblys distincts, n’oubliez pas de copier les chaînes de connexion du modèle dans le fichier de configuration de l’application du projet client.

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 désactiver la validation d’un modèle basé sur edmx

Les modèles EDMX sont validés au moment de la compilation, même si le modèle n’est pas modifié. Si votre modèle a déjà été validé, vous pouvez supprimer la validation au moment de la compilation en affectant à la propriété « valider à la génération » la valeur false dans la fenêtre Propriétés. Lorsque vous modifiez votre mappage ou modèle, vous pouvez réactiver temporairement la validation pour vérifier vos modifications.

Notez que les améliorations des performances ont été apportées au Entity Framework Designer pour Entity Framework 6, et que le coût de la « validation sur Build » est bien plus faible que dans les versions précédentes du concepteur.

## <a name="3-caching-in-the-entity-framework"></a>3 Caching dans le Entity Framework

Entity Framework présente les formes de mise en cache intégrées suivantes :

1.  Mise en cache d’objets : le ObjectStateManager intégré à une instance ObjectContext garde le suivi en mémoire des objets qui ont été récupérés à l’aide de cette instance. Cela est également appelé cache de premier niveau.
2.  Mise en cache du plan de requête-réutilisation de la commande de stockage générée lorsqu’une requête est exécutée plusieurs fois.
3.  Mise en cache des métadonnées : partage des métadonnées pour un modèle sur différentes connexions au même modèle.

Outre les caches fournis par EF, un type spécial de fournisseur de données ADO.NET connu sous le nom de fournisseur d’encapsulation peut également être utilisé pour étendre Entity Framework avec un cache pour les résultats récupérés à partir de la base de données, également appelée mise en cache de second niveau.

### <a name="31-object-caching"></a>Mise en cache d’objets 3,1

Par défaut, lorsqu’une entité est retournée dans les résultats d’une requête, juste avant que EF ne le matérialise, ObjectContext vérifie si une entité avec la même clé a déjà été chargée dans son ObjectStateManager. Si une entité avec les mêmes clés est déjà présente, EF l’inclut dans les résultats de la requête. Bien que EF émette toujours la requête sur la base de données, ce comportement peut contourner la majeure partie du coût de la matérialisation de l’entité plusieurs fois.

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 obtention d’entités à partir du cache d’objets à l’aide de DbContext Find

Contrairement à une requête normale, la méthode Find dans DbSet (les API incluses pour la première fois dans EF 4,1) effectue une recherche dans la mémoire avant même d’émettre la requête sur la base de données. Il est important de noter que deux instances ObjectContext différentes auront deux instances ObjectStateManager différentes, ce qui signifie qu’elles ont des caches d’objets distincts.

Find utilise la valeur de clé primaire pour tenter de trouver une entité suivie par le contexte. Si l’entité n’est pas dans le contexte, une requête est exécutée et évaluée par rapport à la base de données, et la valeur null est retournée si l’entité est introuvable dans le contexte ou dans la base de données. Notez que Find retourne également les entités qui ont été ajoutées au contexte mais qui n’ont pas encore été enregistrées dans la base de données.

Vous pouvez prendre en compte les performances lors de l’utilisation de la recherche. Par défaut, les appels à cette méthode déclenchent une validation du cache d’objets afin de détecter les modifications qui sont toujours en attente de validation dans la base de données. Ce processus peut être très onéreux s’il existe un très grand nombre d’objets dans le cache d’objets ou dans un graphique d’objets volumineux qui est ajouté au cache d’objets, mais il peut également être désactivé. Dans certains cas, vous pouvez percevoir un ordre de grandeur de différence lors de l’appel de la méthode Find lorsque vous désactivez la détection automatique des modifications. Pourtant, un deuxième ordre de grandeur est perçu lorsque l’objet est réellement dans le cache par rapport au moment où l’objet doit être récupéré de la base de données. Voici un exemple de graphique avec des mesures effectuées à l’aide de quelques tests, exprimés en millisecondes, avec une charge de 5000 entités :

![Échelle logarithmique .NET 4,5](~/ef6/media/net45logscale.png ".NET 4,5-échelle logarithmique")

Exemple de recherche avec détection automatique des modifications désactivées :

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

Les éléments à prendre en compte lors de l’utilisation de la méthode Find sont les suivants :

1.  Si l’objet n’est pas dans le cache, les avantages de Find sont inversés, mais la syntaxe est toujours plus simple qu’une requête par clé.
2.  Si la détection automatique des modifications est activée, le coût de la méthode Find peut augmenter d’un ordre de grandeur, voire plus, en fonction de la complexité de votre modèle et de la quantité d’entités dans votre cache d’objets.

En outre, n’oubliez pas que la recherche ne retourne que l’entité que vous recherchez et qu’elle ne charge pas automatiquement les entités associées si elles ne se trouvent pas déjà dans le cache d’objets. Si vous devez récupérer les entités associées, vous pouvez utiliser une requête par clé avec un chargement hâtif. Pour plus d’informations, consultez **8,1 chargement différé et chargement hâtif**.

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 problèmes de performances lorsque le cache d’objets a de nombreuses entités

Le cache d’objets permet d’augmenter la réactivité globale de Entity Framework. Toutefois, lorsque le cache d’objets a une très grande quantité d’entités chargées, il peut affecter certaines opérations, telles que ajouter, supprimer, Rechercher, entrée, SaveChanges et bien plus encore. En particulier, les opérations qui déclenchent un appel à DetectChanges seront affectées par des caches d’objets très volumineux. DetectChanges synchronise le graphique d’objets avec le gestionnaire d’état d’objet et ses performances sont déterminées directement par la taille du graphique d’objets. Pour plus d’informations sur DetectChanges, consultez [suivi des modifications dans les entités POCO](https://msdn.microsoft.com/library/dd456848.aspx).

Lorsque vous utilisez Entity Framework 6, les développeurs peuvent appeler AddRange et RemoveRange directement sur un DbSet, au lieu d’effectuer une itération sur une collection et d’appeler Add once par instance. L’avantage de l’utilisation des méthodes de plage est que le coût de DetectChanges n’est payé qu’une seule fois pour l’ensemble des entités, par opposition à une seule fois pour chaque entité ajoutée.

### <a name="32-query-plan-caching"></a>3,2 mise en cache du plan de requête

La première fois qu’une requête est exécutée, elle passe par le compilateur de plan interne pour traduire la requête conceptuelle dans la commande de stockage (par exemple, T-SQL qui est exécutée lors de l’exécution sur SQL Server).Si la mise en cache du plan de requête est activée, la prochaine fois que la requête est exécutée, la commande de stockage est récupérée directement à partir du cache du plan de requête pour exécution, en ignorant le compilateur du plan.

Le cache du plan de requête est partagé entre les instances ObjectContext au sein du même AppDomain. Vous n’avez pas besoin de conserver une instance ObjectContext pour tirer parti de la mise en cache du plan de requête.

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 quelques remarques sur la mise en cache du plan de requête

-   Le cache du plan de requête est partagé pour tous les types de requêtes : les objets Entity SQL, LINQ to Entities et CompiledQuery.
-   Par défaut, la mise en cache du plan de requête est activée pour les requêtes Entity SQL, qu’elles soient exécutées via un EntityCommand ou via un ObjectQuery. Elle est également activée par défaut pour les requêtes LINQ to Entities dans Entity Framework sur .NET 4,5 et dans Entity Framework 6
    -   La mise en cache du plan de requête peut être désactivée en affectant à la propriété EnablePlanCaching (sur EntityCommand ou ObjectQuery) la valeur false. Par exemple :
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
-   Pour les requêtes paramétrables, la modification de la valeur du paramètre atteint toujours la requête mise en cache. Toutefois, la modification des facettes d’un paramètre (par exemple, la taille, la précision ou l’échelle) atteint une entrée différente dans le cache.
-   Lors de l’utilisation de Entity SQL, la chaîne de requête fait partie de la clé. La modification de la requête peut entraîner des entrées de cache différentes, même si les requêtes sont fonctionnellement équivalentes. Cela comprend les modifications apportées à la casse ou à l’espace blanc.
-   Lors de l’utilisation de LINQ, la requête est traitée pour générer une partie de la clé. La modification de l’expression LINQ générera donc une clé différente.
-   D’autres limitations techniques peuvent s’appliquer ; Pour plus d’informations, consultez requêtes autocompilées.

#### <a name="322-cache-eviction-algorithm"></a>3.2.2-algorithme d’éviction du cache

Comprendre le fonctionnement de l’algorithme interne vous aide à déterminer quand activer ou désactiver la mise en cache du plan de requête. L’algorithme de nettoyage est le suivant :

1.  Une fois que le cache contient un nombre défini d’entrées (800), nous commençons un minuteur qui balaye régulièrement le cache (une fois par minute).
2.  Pendant les nettoyages du cache, les entrées sont supprimées du cache sur une base LFRU (le moins fréquemment utilisé récemment). Cet algorithme prend en compte le nombre d’accès et l’âge pour déterminer quelles entrées sont éjectées.
3.  À la fin de chaque balayage du cache, le cache contient 800 entrées.

Toutes les entrées de cache sont traitées de manière égale lors de la détermination des entrées à supprimer. Cela signifie que la commande de stockage pour un CompiledQuery a le même risque d’éviction que la commande de stockage pour une requête de Entity SQL.

Notez que le minuteur d’éviction du cache est lancé lorsqu’il y a 800 entités dans le cache, mais que le cache n’est balayé que 60 secondes après le démarrage de ce minuteur. Cela signifie que jusqu’à 60 secondes, votre cache peut croître pour être assez volumineux.

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 métriques de test illustrant les performances de mise en cache du plan de requête

Pour illustrer l’effet de la mise en cache du plan de requête sur les performances de votre application, nous avons effectué un test sur lequel nous avons exécuté un certain nombre de requêtes Entity SQL sur le modèle Navision. Reportez-vous à l’annexe pour obtenir une description du modèle Navision et des types de requêtes qui ont été exécutés. Dans ce test, nous allons d’abord itérer au sein de la liste des requêtes et les exécuter une fois pour les ajouter au cache (si la mise en cache est activée). Cette étape n’est pas terminée. Ensuite, nous mettons en veille le thread principal pendant plus de 60 secondes pour permettre le balayage du cache. Enfin, nous parcourons la liste une deuxième fois pour exécuter les requêtes mises en cache. En outre, le cache du plan de SQL Server est vidé avant l’exécution de chaque ensemble de requêtes, de sorte que les fois que nous obtenons précisément l’avantage donné par le cache du plan de requête.

##### <a name="3231-test-results"></a>3.2.3.1 Résultats des tests

| Test                                                                   | EF5 aucun cache | EF5 mis en cache | EF6 aucun cache | EF6 mis en cache |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| Énumération de toutes les requêtes 18723                                          | 124          | 125,4      | 124,3        | 125,3      |
| Éviter le balayage (uniquement les premières requêtes 800, quelle que soit la complexité)  | 41,7         | 5.5        | 40,5         | 5.4        |
| Uniquement les requêtes AggregatingSubtotals (178 au total-qui évite le balayage) | 39,5         | 4,5        | 38,1         | 4,6        |

*Toutes les fois en secondes.*

Moral : lors de l’exécution d’un grand nombre de requêtes distinctes (par exemple, des requêtes créées dynamiquement), la mise en cache n’est pas utile et le vidage résultant du cache peut conserver les requêtes qui tireraient le meilleur parti de la mise en cache de plan pour l’utiliser en fait.

Les requêtes AggregatingSubtotals sont les plus complexes des requêtes que nous avons testées avec. Comme prévu, plus la requête est complexe, plus l’avantage que vous verrez dans la mise en cache du plan de requête est avantageux.

Étant donné qu’un CompiledQuery est en réalité une requête LINQ avec son plan mis en cache, la comparaison entre un CompiledQuery et l’équivalent Entity SQL requête doit avoir des résultats similaires. En fait, si une application possède un grand nombre de requêtes Entity SQL dynamiques, le remplissage du cache avec des requêtes entraînera également la « décompilation » de CompiledQueries lorsqu’elles seront vidées du cache. Dans ce scénario, les performances peuvent être améliorées en désactivant la mise en cache sur les requêtes dynamiques afin de hiérarchiser les CompiledQueries. Mieux encore, bien sûr, il serait de réécrire l’application pour utiliser des requêtes paramétrables au lieu de requêtes dynamiques.

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3,3 utilisation de CompiledQuery pour améliorer les performances avec des requêtes LINQ

Nos tests indiquent que l’utilisation de CompiledQuery peut apporter un avantage de 7% sur les requêtes LINQ autocompilées. Cela signifie que vous allez consacrer 7% moins de temps à exécuter du code à partir de la pile Entity Framework ; Cela ne signifie pas que votre application sera 7% plus rapide. En règle générale, le coût de l’écriture et de la gestion des objets CompiledQuery dans EF 5,0 peut ne pas poser de problème par rapport aux avantages. Votre kilométrage peut varier. vous devez donc faire preuve de cette possibilité si votre projet nécessite une transmission de type push supplémentaire. Notez que les CompiledQueries sont uniquement compatibles avec les modèles dérivés de ObjectContext et ne sont pas compatibles avec les modèles dérivés de DbContext.

Pour plus d’informations sur la création et l’appel d’un CompiledQuery, consultez [requêtes compilées (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).

Il y a deux points à prendre en compte lors de l’utilisation d’un CompiledQuery, à savoir la nécessité d’utiliser des instances statiques et les problèmes qu’ils ont avec la composabilité. Vous trouverez ci-dessous une explication détaillée de ces deux considérations.

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 utiliser des instances CompiledQuery statiques

Dans la mesure où la compilation d’une requête LINQ est un processus long, nous ne voulons pas la faire chaque fois que nous devons extraire des données de la base de données. Les instances CompiledQuery vous permettent de compiler une seule fois et de les exécuter plusieurs fois, mais vous devez faire attention et demander à réutiliser la même instance de CompiledQuery à chaque fois au lieu de la compiler à nouveau. L’utilisation de membres statiques pour stocker les instances CompiledQuery devient nécessaire ; dans le cas contraire, vous ne verrez aucun avantage.

Par exemple, supposons que votre page possède le corps de méthode suivant pour gérer l’affichage des produits pour la catégorie sélectionnée :

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

Dans ce cas, vous allez créer une nouvelle instance CompiledQuery à la volée chaque fois que la méthode est appelée. Au lieu de voir les avantages en matière de performances en récupérant la commande de stockage dans le cache du plan de requête, le CompiledQuery passera par le compilateur de plan chaque fois qu’une nouvelle instance sera créée. En fait, vous allez polluer votre cache de plan de requête avec une nouvelle entrée CompiledQuery chaque fois que la méthode est appelée.

Au lieu de cela, vous souhaitez créer une instance statique de la requête compilée, de sorte que vous appelez la même requête compilée chaque fois que la méthode est appelée. Pour ce faire, vous pouvez ajouter l’instance CompiledQuery en tant que membre du contexte de l’objet.Vous pouvez ensuite faire un petit nettoyage en accédant au CompiledQuery par le biais d’une méthode d’assistance :

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

Cette méthode d’assistance serait appelée comme suit :

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 composer sur un CompiledQuery

La possibilité de composer une requête LINQ est très utile. pour ce faire, il vous suffit d’appeler une méthode après l’objet IQueryable, par exemple *Skip ()* ou *Count ()*. Toutefois, cela retourne essentiellement un nouvel objet IQueryable. Bien qu’il n’y ait rien à vous empêcher techniquement de composer sur un CompiledQuery, cela entraînera la génération d’un nouvel objet IQueryable qui nécessitera à nouveau de passer par le compilateur de plan.

Certains composants utilisent des objets IQueryable composés pour activer les fonctionnalités avancées. Par exemple, ASP. Le GridView de NET peut être lié aux données d’un objet IQueryable via la propriété SelectMethod. Le GridView se compose alors de cet objet IQueryable pour permettre le tri et la pagination sur le modèle de données. Comme vous pouvez le voir, l’utilisation d’un CompiledQuery pour le GridView n’atteint pas la requête compilée, mais génère une nouvelle requête autocompilée.

Dans ce cas, il est possible d’ajouter des filtres progressifs à une requête. Par exemple, supposons que vous disposiez d’une page clients avec plusieurs listes déroulantes pour les filtres facultatifs (par exemple, pays et OrdersCount). Vous pouvez composer ces filtres sur les résultats IQueryable d’un CompiledQuery, mais cela entraînera le passage de la nouvelle requête par le compilateur de plan chaque fois que vous l’exécutez.

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

 Pour éviter cette nouvelle compilation, vous pouvez réécrire le CompiledQuery pour prendre en compte les filtres possibles :

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

Qui serait appelé dans l’interface utilisateur comme :

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

 Un compromis ici est que la commande de magasin générée disposera toujours des filtres avec les vérifications de valeur null, mais celles-ci doivent être assez simples pour que le serveur de base de données les optimise :

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3,4 mise en cache des métadonnées

Le Entity Framework prend également en charge la mise en cache des métadonnées. Il s’agit essentiellement d’une mise en cache des informations de type et des informations de mappage de type à base de données entre différentes connexions au même modèle. Le cache des métadonnées est unique par AppDomain.

#### <a name="341-metadata-caching-algorithm"></a>3.4.1-algorithme de mise en cache des métadonnées

1.  Les informations de métadonnées d’un modèle sont stockées dans un ItemCollection pour chaque EntityConnection.
    -   En guise de note, il existe différents objets ItemCollection pour différentes parties du modèle. Par exemple, StoreItemCollections contient les informations sur le modèle de base de données ; ObjectItemCollection contient des informations sur le modèle de données ; EdmItemCollection contient des informations sur le modèle conceptuel.

2.  Si deux connexions utilisent la même chaîne de connexion, elles partagent la même instance de ItemCollection.
3.  Fonctionnellement équivalente mais les chaînes de connexion différentes textuellement peuvent entraîner des caches de métadonnées différents. Comme nous jetons des chaînes de connexion, il suffit de modifier l’ordre des jetons pour créer des métadonnées partagées. Toutefois, deux chaînes de connexion apparemment fonctionnellement identiques peuvent ne pas être évaluées comme identiques après la création de jetons.
4.  L’utilisation de ItemCollection est régulièrement vérifiée. S’il est déterminé qu’un espace de travail n’a pas été accédé récemment, il est marqué pour nettoyage lors du prochain balayage du cache.
5.  Le simple fait de créer un EntityConnection entraîne la création d’un cache de métadonnées (bien que les collections d’éléments qu’il contient ne soient pas initialisées tant que la connexion n’est pas ouverte). Cet espace de travail reste en mémoire jusqu’à ce que l’algorithme de mise en cache détermine qu’il n’est pas « en cours d’utilisation ».

L’équipe de conseil clientèle a écrit un billet de blog qui décrit la conservation d’une référence à un ItemCollection afin d’éviter la « désapprobation » lors de l’utilisation de modèles de grande taille : \<https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services> .

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 la relation entre la mise en cache des métadonnées et la mise en cache du plan de requête

L’instance du cache du plan de requête se trouve dans l’ItemCollection des types de magasins de MetadataWorkspace. Cela signifie que les commandes de stockage mises en cache sont utilisées pour les requêtes sur tout contexte instancié à l’aide d’un MetadataWorkspace donné. Cela signifie également que si vous avez deux chaînes de connexion qui sont légèrement différentes et ne correspondent pas après la création de jetons, vous aurez des instances de cache de plan de requête différentes.

### <a name="35-results-caching"></a>3,5 mise en cache des résultats

Avec la mise en cache des résultats (également appelée « mise en cache de second niveau »), vous conservez les résultats des requêtes dans un cache local. Lors de l’émission d’une requête, vous voyez d’abord si les résultats sont disponibles localement avant d’effectuer une requête sur le magasin. Si la mise en cache des résultats n’est pas directement prise en charge par Entity Framework, il est possible d’ajouter un cache de second niveau à l’aide d’un fournisseur d’encapsulation. Un exemple de fournisseur d’encapsulation avec un cache de second niveau est le [cache de second niveau de Alachisoft Entity Framework basé sur NCache](https://www.alachisoft.com/ncache/entity-framework.html).

Cette implémentation de la mise en cache de second niveau est une fonctionnalité injectée qui a lieu une fois que l’expression LINQ a été évaluée (et funcletized) et que le plan d’exécution de la requête est calculé ou récupéré à partir du cache de premier niveau. Le cache de second niveau stocke alors uniquement les résultats bruts de la base de données, de sorte que le pipeline de matérialisation est toujours exécuté par la suite.

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 Références supplémentaires pour la mise en cache des résultats avec le fournisseur d’encapsulation

-   Julie Lerman a écrit un article « mise en cache de second niveau dans Entity Framework et Windows Azure » qui inclut la mise à jour de l’exemple de fournisseur d’encapsulation pour utiliser la mise en cache de Windows Server AppFabric : [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   Si vous utilisez Entity Framework 5, le blog de l’équipe dispose d’une publication qui décrit comment faire en sorte que les tâches s’exécutent avec le fournisseur de mise en cache pour Entity Framework 5 : \<https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider> . Il comprend également un modèle T4 pour vous aider à automatiser l’ajout de la mise en cache de 2e niveau à votre projet.

## <a name="4-autocompiled-queries"></a>4 requêtes autocompilées

Lorsqu’une requête est émise sur une base de données à l’aide d’Entity Framework, elle doit passer par une série d’étapes avant de matérialiser les résultats ; une de ces étapes est la compilation des requêtes. Entity SQL requêtes étaient connues pour avoir de bonnes performances, car elles sont automatiquement mises en cache. par conséquent, la deuxième ou la troisième fois que vous exécutez la même requête, elle peut ignorer le compilateur de plan et utiliser le plan mis en cache à la place.

Entity Framework 5 a également introduit la mise en cache automatique pour les requêtes LINQ to Entities. Dans les éditions précédentes de Entity Framework la création d’un CompiledQuery pour accélérer vos performances était une pratique courante, car cela permet de mettre en cache votre requête LINQ to Entities. Étant donné que la mise en cache s’effectue automatiquement sans l’utilisation d’un CompiledQuery, nous appelons cette fonctionnalité « requêtes autocompilées ». Pour plus d’informations sur le cache du plan de requête et ses mécanismes, consultez mise en cache des plans de requête.

Entity Framework détecte quand une requête doit être recompilée, et le fait lorsque la requête est appelée même si elle a été compilée auparavant. Les conditions courantes qui provoquent la recompilation de la requête sont les suivantes :

-   Modification du MergeOption associé à votre requête. La requête mise en cache ne sera pas utilisée, à la place, le compilateur de plan s’exécutera à nouveau et le plan nouvellement créé est mis en cache.
-   Modification de la valeur de ContextOptions. UseCSharpNullComparisonBehavior. Vous avez le même effet que la modification de MergeOption.

D’autres conditions peuvent empêcher votre requête d’utiliser le cache. Voici des exemples courants :

-   Utilisation de IEnumerable &lt; T &gt; . Contains &lt; &gt; (valeur T).
-   Utilisation de fonctions qui produisent des requêtes avec des constantes.
-   Utilisation des propriétés d’un objet non mappé.
-   Liaison de votre requête à une autre requête qui nécessite d’être recompilée.

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4,1 avec IEnumerable &lt; T &gt; . Contient &lt; t &gt; (valeur t)

Entity Framework ne met pas en cache les requêtes qui appellent IEnumerable &lt; T &gt; . Contient &lt; t &gt; (valeur t) par rapport à une collection en mémoire, étant donné que les valeurs de la collection sont considérées comme volatiles. L’exemple de requête suivant ne sera pas mis en cache et sera donc toujours traité par le compilateur de plan :

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

Notez que la taille de l’IEnumerable par rapport à laquelle est exécutée détermine la vitesse de compilation de votre requête. Les performances peuvent être considérablement affectées lors de l’utilisation de collections volumineuses, telles que celle illustrée dans l’exemple ci-dessus.

Entity Framework 6 contient des optimisations de la façon dont IEnumerable &lt; T &gt; . Contains &lt; &gt; (valeur t) fonctionne lorsque les requêtes sont exécutées. Le code SQL généré est beaucoup plus rapide à produire et plus lisible, et dans la plupart des cas, il s’exécute également plus rapidement sur le serveur.

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4,2 utilisation de fonctions qui produisent des requêtes avec des constantes

Les opérateurs LINQ Skip (), Take (), Contains () et DefautIfEmpty () ne génèrent pas de requêtes SQL avec des paramètres, mais placent les valeurs qui leur sont passées en tant que constantes. Pour cette raison, les requêtes qui peuvent sinon être identiques finissent par polluer le cache du plan de requête, à la fois sur la pile EF et sur le serveur de base de données, et ne sont pas réutilisées, sauf si les mêmes constantes sont utilisées lors de l’exécution d’une requête ultérieure. Par exemple :

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

Dans cet exemple, chaque fois que cette requête est exécutée avec une valeur différente pour ID, la requête est compilée dans un nouveau plan.

En particulier, soyez attentif à l’utilisation de Skip et Take lors de la pagination. Dans EF6, ces méthodes ont une surcharge lambda qui rend effectivement le plan de requête mis en cache réutilisable, car EF peut capturer les variables passées à ces méthodes et les traduire en SQLparameters. Cela permet également de conserver le nettoyeur de cache, car dans le cas contraire, chaque requête avec une constante différente pour Skip et Take obtient sa propre entrée de cache de plan de requête.

Considérez le code suivant, qui est non optimal, mais qui est uniquement destiné à illustrer cette classe de requêtes :

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Une version plus rapide de ce même code implique l’appel de SKIP avec une expression lambda :

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Le second extrait de code peut s’exécuter jusqu’à 11% plus rapidement, car le même plan de requête est utilisé chaque fois que la requête est exécutée, ce qui permet d’économiser du temps processeur et d’éviter de polluer le cache des requêtes. En outre, étant donné que le paramètre à ignorer se trouve dans une fermeture, le code peut également ressembler à ceci :

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4,3 utilisation des propriétés d’un objet non mappé

Quand une requête utilise les propriétés d’un type d’objet non mappé comme paramètre, la requête n’est pas mise en cache. Par exemple :

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

Dans cet exemple, supposons que la classe NonMappedType ne fait pas partie du modèle d’entité. Cette requête peut facilement être modifiée pour ne pas utiliser un type non mappé et utiliser à la place une variable locale comme paramètre de la requête :

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

Dans ce cas, la requête peut être mise en cache et tirer parti du cache du plan de requête.

### <a name="44-linking-to-queries-that-require-recompiling"></a>4,4 liaison à des requêtes qui nécessitent une recompilation

En suivant le même exemple que ci-dessus, si vous avez une deuxième requête qui s’appuie sur une requête qui doit être recompilée, la totalité de votre seconde requête sera également recompilée. Voici un exemple illustrant ce scénario :

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

L’exemple est générique, mais il illustre la façon dont la liaison à firstQuery entraîne l’impossibilité pour secondQuery de se mettre en cache. Si firstQuery n’était pas une requête nécessitant une recompilation, secondQuery aurait été mis en cache.

## <a name="5-notracking-queries"></a>5 requêtes de non-suivi

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5,1 désactivation du suivi des modifications pour réduire la surcharge de gestion de l’État

Si vous êtes dans un scénario en lecture seule et que vous souhaitez éviter la surcharge liée au chargement des objets dans le ObjectStateManager, vous pouvez émettre des requêtes « aucune suivi ».Le suivi des modifications peut être désactivé au niveau de la requête.

Notez, toutefois, que si vous désactivez le suivi des modifications, vous désactivez efficacement le cache d’objets. Lorsque vous interrogez une entité, nous ne pouvons pas ignorer la matérialisation en extrayant les résultats de requête matérialisés précédemment à partir de ObjectStateManager. Si vous interrogez à plusieurs reprises les mêmes entités sur le même contexte, vous pouvez en fait voir un avantage en matière de performances de l’activation du suivi des modifications.

Lors de l’interrogation à l’aide d’ObjectContext, les instances ObjectQuery et ObjectSet se souviennent d’un MergeOption une fois qu’il est défini, et les requêtes qui y sont composées héritent du MergeOption effectif de la requête parente. Lors de l’utilisation de DbContext, le suivi peut être désactivé en appelant le modificateur AsNoTracking () sur DbSet.

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 désactivation du suivi des modifications pour une requête lors de l’utilisation de DbContext

Vous pouvez basculer le mode d’une requête pour qu’elle soit très suivie en chaîné un appel à la méthode AsNoTracking () dans la requête. Contrairement à ObjectQuery, les classes DbSet et DbQuery de l’API DbContext n’ont pas de propriété mutable pour MergeOption.

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 désactivation du suivi des modifications au niveau de la requête à l’aide d’ObjectContext

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 désactivation du suivi des modifications pour un ensemble d’entités complet à l’aide d’ObjectContext

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5,2 métriques de test illustrant l’avantage en matière de performances des requêtes NoTracking

Dans ce test, nous examinons le coût du remplissage de la ObjectStateManager en comparant le suivi à la non-suivi des requêtes pour le modèle Navision. Reportez-vous à l’annexe pour obtenir une description du modèle Navision et des types de requêtes qui ont été exécutés. Dans ce test, nous parcourons la liste des requêtes et les exécutons une fois. Nous avons exécuté deux variantes du test, une fois avec les requêtes NoTracking et une fois avec l’option de fusion par défaut « AppendOnly ». Nous avons exécuté chaque variante 3 fois et prenons la valeur moyenne des exécutions. Entre les tests, nous effacons le cache des requêtes sur le SQL Server et réduisons la base de données tempdb en exécutant les commandes suivantes :

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE (tempdb, 0)

Résultats des tests, median sur 3 s’exécute :

|                        | AUCUN SUIVI-PLAGE DE TRAVAIL | AUCUN SUIVI – HEURE | AJOUTER UNIQUEMENT-PLAGE DE TRAVAIL | AJOUTER UNIQUEMENT – HEURE |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Entity Framework 5** | 460361728                 | 1163536 ms         | 596545536                 | 1273042 ms         |
| **Entity Framework 6** | 647127040                 | 190228 ms          | 832798720                 | 195521 ms          |

Entity Framework 5 aura un faible encombrement mémoire à la fin de l’exécution que Entity Framework 6. La mémoire supplémentaire consommée par Entity Framework 6 est le résultat de structures de mémoire et de code supplémentaires qui activent de nouvelles fonctionnalités et de meilleures performances.

Il y a également une différence évidente en matière d’encombrement mémoire lors de l’utilisation de ObjectStateManager. Entity Framework 5 a augmenté son empreinte de 30% pendant le suivi de toutes les entités que nous avons matérialisées à partir de la base de données. Entity Framework 6 a augmenté son empreinte de 28%.

En termes de temps, Entity Framework 6 s’exécute Entity Framework 5 dans ce test par une marge importante. Entity Framework 6 a terminé le test en environ 16% du temps consommé par Entity Framework 5. En outre, Entity Framework 5 prend plus de 9% de temps pour s’exécuter lorsque le ObjectStateManager est utilisé. En comparaison, Entity Framework 6 utilise 3% de temps supplémentaire lors de l’utilisation de ObjectStateManager.

## <a name="6-query-execution-options"></a>6 options d’exécution de requête

Entity Framework propose différentes façons d’interroger. Nous allons examiner les options suivantes, comparer les avantages et les inconvénients de chacun et examiner leurs caractéristiques de performances :

-   LINQ to Entities.
-   Aucune LINQ to Entities de suivi.
-   Entity SQL sur un ObjectQuery.
-   Entity SQL sur un EntityCommand.
-   ExecuteStoreQuery.
-   SqlQuery.
-   CompiledQuery.

### <a name="61-linq-to-entities-queries"></a>6,1 requêtes de LINQ to Entities

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**Avantages**

-   Adapté aux opérations CUD.
-   Objets entièrement matérialisés.
-   Plus simple à écrire avec la syntaxe intégrée dans le langage de programmation.
-   Bonnes performances.

**Inconvénients**

-   Certaines restrictions techniques, telles que :
    -   Les modèles utilisant DefaultIfEmpty pour les requêtes de jointure externe génèrent des requêtes plus complexes que les instructions de jointure externe simples dans Entity SQL.
    -   Vous ne pouvez toujours pas utiliser LIKE avec les critères spéciaux.

### <a name="62-no-tracking-linq-to-entities-queries"></a>6,2 aucune requête de LINQ to Entities de suivi

Lorsque le contexte dérive de ObjectContext :

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

Lorsque le contexte dérive DbContext :

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**Avantages**

-   Performances améliorées par rapport aux requêtes LINQ normales.
-   Objets entièrement matérialisés.
-   Plus simple à écrire avec la syntaxe intégrée dans le langage de programmation.

**Inconvénients**

-   Non adapté aux opérations CUD.
-   Certaines restrictions techniques, telles que :
    -   Les modèles utilisant DefaultIfEmpty pour les requêtes de jointure externe génèrent des requêtes plus complexes que les instructions de jointure externe simples dans Entity SQL.
    -   Vous ne pouvez toujours pas utiliser LIKE avec les critères spéciaux.

Notez que les requêtes qui projetent les propriétés scalaires ne sont pas suivies, même si le NoTracking n’est pas spécifié. Par exemple :

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

Cette requête particulière ne spécifie pas explicitement la non-suivi, mais dans la mesure où elle ne matérialisét pas un type connu du gestionnaire d’état d’objet, le résultat matérialisé n’est pas suivi.

### <a name="63-entity-sql-over-an-objectquery"></a>6,3 Entity SQL sur un ObjectQuery

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**Avantages**

-   Adapté aux opérations CUD.
-   Objets entièrement matérialisés.
-   Prend en charge la mise en cache du plan de requête.

**Inconvénients**

-   Implique des chaînes de requête textuelles qui sont plus sujettes aux erreurs des utilisateurs que les constructions de requête intégrées au langage.

### <a name="64-entity-sql-over-an-entity-command"></a>6,4 Entity SQL sur une commande d’entité

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

-   Prend en charge la mise en cache du plan de requête dans .NET 4,0 (la mise en cache du plan est prise en charge par tous les autres types de requêtes dans .NET 4,5).

**Inconvénients**

-   Implique des chaînes de requête textuelles qui sont plus sujettes aux erreurs des utilisateurs que les constructions de requête intégrées au langage.
-   Non adapté aux opérations CUD.
-   Les résultats ne sont pas matérialisés automatiquement et doivent être lus à partir du lecteur de données.

### <a name="65-sqlquery-and-executestorequery"></a>6,5 SqlQuery et ExecuteStoreQuery

SqlQuery sur la base de données :

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

SqlQuery sur DbSet :

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

-   Performances généralement plus rapides dans la mesure où le compilateur de plan est contourné.
-   Objets entièrement matérialisés.
-   Adapté aux opérations CUD en cas d’utilisation à partir du DbSet.

**Inconvénients**

-   La requête est textuelle et sujette aux erreurs.
-   La requête est liée à un serveur principal spécifique à l’aide de la sémantique de magasin au lieu de la sémantique conceptuelle.
-   Lorsque l’héritage est présent, la requête handcrafted doit tenir compte des conditions de mappage pour le type demandé.

### <a name="66-compiledquery"></a>6,6 CompiledQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**Avantages**

-   Offre une amélioration des performances pouvant atteindre 7% par rapport aux requêtes LINQ ordinaires.
-   Objets entièrement matérialisés.
-   Adapté aux opérations CUD.

**Inconvénients**

-   Complexité accrue et programmation plus lourde.
-   L’amélioration des performances est perdue lors de la composition d’une requête compilée.
-   Certaines requêtes LINQ ne peuvent pas être écrites en tant que CompiledQuery, par exemple, des projections de types anonymes.

### <a name="67-performance-comparison-of-different-query-options"></a>6,7 Comparaison des performances des différentes options de requête

Les microtests simples où la création de contexte n’a pas été chronométrée ont été placés dans le test. Nous avons mesuré l’interrogation 5000 fois pour un ensemble d’entités non mises en cache dans un environnement contrôlé. Ces valeurs doivent être prises en compte : elles ne reflètent pas les chiffres réels produits par une application, mais elles constituent une mesure très précise de la différence de performances lorsque des options d’interrogation différentes sont comparées entre les pommes et les pommes, à l’exception du coût de création d’un nouveau contexte.

| EF  | Test                                 | Temps (MS) | Mémoire   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ESQL ObjectContext                   | 2414      | 38801408 |
| EF5 | Requête LINQ ObjectContext             | 2692      | 38277120 |
| EF5 | Interrogation LINQ de DbContext non suivi     | 2818      | 41840640 |
| EF5 | Requête LINQ DbContext                 | 2930      | 41771008 |
| EF5 | ObjectContext requête LINQ aucun suivi | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ESQL ObjectContext                   | 2059      | 46039040 |
| EF6 | Requête LINQ ObjectContext             | 3074      | 45248512 |
| EF6 | Interrogation LINQ de DbContext non suivi     | 3125      | 47575040 |
| EF6 | Requête LINQ DbContext                 | 3420      | 47652864 |
| EF6 | ObjectContext requête LINQ aucun suivi | 3593      | 45260800 |

![EF5 micro tests, 5000 itérations à chaud](~/ef6/media/ef5micro5000warm.png)

![EF6 micro tests, 5000 itérations à chaud](~/ef6/media/ef6micro5000warm.png)

Les microtests sont très sensibles aux modifications mineures apportées au code. Dans ce cas, la différence entre les coûts de Entity Framework 5 et Entity Framework 6 est due à l’ajout d’une [interception](xref:ef6/fundamentals/logging-and-interception) et à des [améliorations transactionnelles](xref:ef6/saving/transactions). Toutefois, ces microtests sont une vision amplifiée d’un très petit fragment de ce que Entity Framework fait. Les scénarios réels de requêtes à chaud ne doivent pas voir une régression des performances lors de la mise à niveau de Entity Framework 5 vers Entity Framework 6.

Pour comparer les performances réelles des différentes options de requête, nous avons créé 5 variantes de test distinctes où nous utilisons une option de requête différente pour sélectionner tous les produits dont le nom de catégorie est « boissons ». Chaque itération comprend le coût de la création du contexte et le coût de la matérialisation de toutes les entités retournées. 10 itérations sont exécutées de façon ininterrompue avant d’effectuer la somme de 1000 itérations chronométrées. Les résultats affichés sont l’exécution médiane effectuée à partir de 5 exécutions de chaque test. Pour plus d’informations, consultez l’annexe B, qui comprend le code du test.

| EF  | Test                                        | Temps (MS) | Mémoire   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | Commande d’entité ObjectContext                | 621       | 39350272 |
| EF5 | Requête SQL DbContext sur la base de données             | 825       | 37519360 |
| EF5 | Requête du magasin ObjectContext                   | 878       | 39460864 |
| EF5 | ObjectContext requête LINQ aucun suivi        | 969       | 38293504 |
| EF5 | ObjectContext Entity SQL à l’aide d’une requête d’objet | 1089      | 38981632 |
| EF5 | Requête compilée ObjectContext                | 1099      | 38682624 |
| EF5 | Requête LINQ ObjectContext                    | 1152      | 38178816 |
| EF5 | Interrogation LINQ de DbContext non suivi            | 1208      | 41803776 |
| EF5 | Requête SQL DbContext sur DbSet                | 1414      | 37982208 |
| EF5 | Requête LINQ DbContext                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | Commande d’entité ObjectContext                | 480       | 47247360 |
| EF6 | Requête du magasin ObjectContext                   | 493       | 46739456 |
| EF6 | Requête SQL DbContext sur la base de données             | 614       | 41607168 |
| EF6 | ObjectContext requête LINQ aucun suivi        | 684       | 46333952 |
| EF6 | ObjectContext Entity SQL à l’aide d’une requête d’objet | 767       | 48865280 |
| EF6 | Requête compilée ObjectContext                | 788       | 48467968 |
| EF6 | Interrogation LINQ de DbContext non suivi            | 878       | 47554560 |
| EF6 | Requête LINQ ObjectContext                    | 953       | 47632384 |
| EF6 | Requête SQL DbContext sur DbSet                | 1023      | 41992192 |
| EF6 | Requête LINQ DbContext                        | 1290      | 47529984 |


![EF5 des itérations de requête à chaud 1000](~/ef6/media/ef5warmquery1000.png)

![EF6 des itérations de requête à chaud 1000](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> À des fins d’exhaustivité, nous avons inclus une variation où nous exécutons une requête Entity SQL sur un EntityCommand. Toutefois, étant donné que les résultats ne sont pas matérialisés pour ces requêtes, la comparaison n’est pas nécessairement de pommes à pommes. Le test comprend une approximation de la matérialisation pour essayer de rendre la comparaison plus équitable.

Dans ce cas de bout en bout, Entity Framework 6 est Entity Framework 5 en raison des améliorations de performances apportées sur plusieurs parties de la pile, y compris une initialisation beaucoup plus légère et des recherches MetadataCollection T plus rapides &lt; &gt; .

## <a name="7-design-time-performance-considerations"></a>7 Considérations relatives aux performances au moment de la conception

### <a name="71-inheritance-strategies"></a>7,1 stratégies d’héritage

Une autre considération en matière de performances lors de l’utilisation de Entity Framework est la stratégie d’héritage que vous utilisez. Entity Framework prend en charge 3 types de base d’héritage et leurs combinaisons :

-   Table par hiérarchie (TPH) : où chaque ensemble d’héritage est mappé à une table avec une colonne de discriminateur pour indiquer quel type particulier dans la hiérarchie est représenté dans la ligne.
-   Table par type (TPT) : où chaque type a sa propre table dans la base de données ; les tables enfants définissent uniquement les colonnes que la table parente ne contient pas.
-   Table par classe (TPC) : chaque type possède sa propre table complète dans la base de données ; les tables enfants définissent tous leurs champs, y compris ceux définis dans les types parents.

Si votre modèle utilise l’héritage TPT, les requêtes générées seront plus complexes que celles générées avec les autres stratégies d’héritage, ce qui peut entraîner des durées d’exécution plus longues sur le magasin.Elle prend généralement plus de temps pour générer des requêtes sur un modèle TPT et pour matérialiser les objets résultants.

Consultez «Considérations relatives aux performances lors de l’utilisation de TPT (table par type) dans Entity Framework le billet de blog MSDN : \<https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework> .

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 éviter les TPT dans les applications Model First ou Code First

Lorsque vous créez un modèle sur une base de données existante qui a un schéma TPT, vous ne disposez pas de nombreuses options. Toutefois, lors de la création d’une application à l’aide de Model First ou Code First, vous devez éviter l’héritage TPT pour les problèmes de performances.

Lorsque vous utilisez Model First dans l’Assistant Entity Designer, vous obtiendrez TPT pour tout héritage dans votre modèle. Si vous souhaitez basculer vers une stratégie d’héritage TPH avec Model First, vous pouvez utiliser l’option « Entity Designer génération de la base de données Power Pack » disponible à partir de la Galerie Visual Studio ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/> ).

Lorsque vous utilisez Code First pour configurer le mappage d’un modèle avec héritage, EF utilise TPH par défaut. par conséquent, toutes les entités de la hiérarchie d’héritage sont mappées à la même table. Pour plus d’informations, consultez la section « mappage avec l’API Fluent » de l’article « Code First dans Entity Framework 4.1 » dans le magazine MSDN ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx) ).

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7,2 mise à niveau à partir de EF4 pour améliorer l’heure de génération du modèle

Une amélioration spécifique à l’SQL Server de l’algorithme qui génère la couche de magasin (SSDL) du modèle est disponible dans Entity Framework 5 et 6, et en tant que mise à jour de Entity Framework 4 Lorsque Visual Studio 2010 SP1 est installé. Les résultats des tests suivants illustrent l’amélioration lors de la génération d’un modèle très volumineux, dans ce cas le modèle Navision. Pour plus d’informations à ce sujet, consultez l’annexe C.

Le modèle contient les jeux d’entités 1005 et les ensembles d’associations 4227.

| Configuration                              | Répartition du temps consommé                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010, Entity Framework 4     | Génération SSDL : 2 h 27 min. <br/> Génération de mappage : 1 seconde <br/> Génération CSDL : 1 seconde <br/> Génération ObjectLayer : 1 seconde <br/> Génération de vues : 2 h 14 min. |
| Visual Studio 2010 SP1, Entity Framework 4 | Génération SSDL : 1 seconde <br/> Génération de mappage : 1 seconde <br/> Génération CSDL : 1 seconde <br/> Génération ObjectLayer : 1 seconde <br/> Génération de vues : 1 heure 53 min.   |
| Visual Studio 2013, Entity Framework 5     | Génération SSDL : 1 seconde <br/> Génération de mappage : 1 seconde <br/> Génération CSDL : 1 seconde <br/> Génération ObjectLayer : 1 seconde <br/> Génération de vues : 65 minutes    |
| Visual Studio 2013, Entity Framework 6     | Génération SSDL : 1 seconde <br/> Génération de mappage : 1 seconde <br/> Génération CSDL : 1 seconde <br/> Génération ObjectLayer : 1 seconde <br/> Génération de vues : 28 secondes.   |


Il est à noter que lors de la génération du langage SSDL, la charge est presque entièrement dépensée sur le SQL Server, tandis que l’ordinateur de développement client attend que les résultats soient renvoyés par le serveur. Les administrateurs de bases de l’intérêt devraient particulièrement apprécier cette amélioration. Il est également intéressant de noter que l’ensemble du coût de génération de modèle a lieu dans la génération de vues.

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7,3 fractionnement des modèles volumineux avec Database First et Model First

À mesure que la taille du modèle augmente, l’aire du concepteur devient encombrée et difficile à utiliser. Nous considérons généralement un modèle avec plus de 300 entités comme trop volumineux pour utiliser efficacement le concepteur. Le billet de blog suivant décrit plusieurs options de fractionnement des modèles volumineux : \<https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2> .

La publication a été écrite pour la première version de Entity Framework, mais les étapes s’appliquent toujours.

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7,4 Considérations sur les performances avec le contrôle de source de données d’entité

Nous avons vu des cas dans des tests de performances et de contrainte multithread où les performances d’une application Web utilisant le contrôle EntityDataSource se détériorent considérablement. La cause sous-jacente est que le EntityDataSource appelle MetadataWorkspace. LoadFromAssembly à plusieurs reprises sur les assemblys référencés par l’application Web pour découvrir les types à utiliser comme entités.

La solution consiste à définir le ContextTypeName de EntityDataSource sur le nom de type de votre classe ObjectContext dérivée. Cela désactive le mécanisme qui analyse tous les assemblys référencés pour les types d’entité.

La définition du champ ContextTypeName empêche également un problème fonctionnel où le EntityDataSource dans .NET 4,0 lève une ReflectionTypeLoadException lorsqu’il ne peut pas charger un type à partir d’un assembly par le biais de la réflexion. Ce problème a été résolu dans .NET 4,5.

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7,5 entités POCO et proxys de suivi des modifications

Entity Framework vous permet d’utiliser des classes de données personnalisées avec votre modèle de données sans modifier les classes de données elles-mêmes. Cela signifie que vous pouvez utiliser des objets CLR « classiques » (ou POCO), tels que les objets de domaine existants, avec votre modèle de données. Ces classes de données POCO (également appelées objets ignorant la persistance), qui sont mappées à des entités définies dans un modèle de données, prennent en charge la plupart des mêmes comportements de requête, d’insertion, de mise à jour et de suppression que les types d’entités générés par les outils de Entity Data Model.

Entity Framework pouvez également créer des classes proxy dérivées de vos types POCO, qui sont utilisées lorsque vous souhaitez activer des fonctionnalités telles que le chargement différé et le suivi automatique des modifications sur les entités POCO. Vos classes POCO doivent répondre à certaines exigences pour permettre à Entity Framework d’utiliser des proxies, comme décrit ici : [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx) .

Les proxys de suivi des chances notifient le gestionnaire d’état d’objet chaque fois que l’une des propriétés de vos entités a une valeur modifiée, de sorte que Entity Framework connaît tout le temps de l’état réel de vos entités. Pour ce faire, ajoutez des événements de notification au corps des méthodes setter de vos propriétés et faites en sorte que le gestionnaire d’état d’objet traite de tels événements. Notez que la création d’une entité de proxy est généralement plus coûteuse que la création d’une entité POCO non-proxy en raison de l’ensemble d’événements supplémentaires créé par Entity Framework.

Lorsqu’une entité POCO n’a pas de proxy de suivi des modifications, des modifications sont détectées en comparant le contenu de vos entités à une copie d’un état enregistré précédent. Cette comparaison profonde devient un processus long lorsque vous avez de nombreuses entités dans votre contexte, ou lorsque vos entités ont une très grande quantité de propriétés, même si aucune d’elles n’a changé depuis la dernière comparaison.

En Résumé : vous payez une baisse des performances lors de la création du proxy de suivi des modifications, mais le suivi des modifications vous permet d’accélérer le processus de détection des modifications lorsque vos entités ont de nombreuses propriétés ou lorsque vous avez de nombreuses entités dans votre modèle. Pour les entités avec un petit nombre de propriétés où la quantité d’entités n’augmente pas trop, l’utilisation de proxys de suivi des modifications n’est pas très avantageuse.

## <a name="8-loading-related-entities"></a>8 chargement des entités associées

### <a name="81-lazy-loading-vs-eager-loading"></a>8,1 chargement différé et chargement hâtif

Entity Framework offre différentes manières de charger les entités associées à votre entité cible. Par exemple, lorsque vous recherchez des produits, les commandes associées sont chargées dans le gestionnaire d’état d’objet de différentes façons. Du point de vue des performances, la plus grande question à prendre en compte lors du chargement des entités associées est l’utilisation du chargement différé ou du chargement hâtif.

Lorsque vous utilisez le chargement hâtif, les entités associées sont chargées en même temps que votre jeu d’entités cible. Vous utilisez une instruction include dans votre requête pour indiquer les entités associées que vous souhaitez importer.

Lorsque vous utilisez le chargement différé, votre requête initiale ne s’insère que dans le jeu d’entités cible. Toutefois, chaque fois que vous accédez à une propriété de navigation, une autre requête est émise sur le magasin pour charger l’entité associée.

Une fois qu’une entité a été chargée, toutes les autres requêtes de l’entité la chargent directement à partir du gestionnaire d’état d’objet, que vous utilisiez le chargement différé ou le chargement hâtif.

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8,2 Comment choisir entre le chargement différé et le chargement hâtif

L’important est que vous compreniez la différence entre le chargement différé et le chargement hâtif afin que vous puissiez faire le bon choix pour votre application. Cela vous aidera à évaluer le compromis entre plusieurs demandes sur la base de données par rapport à une requête unique qui peut contenir une charge utile importante. Il peut être utile d’utiliser le chargement hâtif dans certaines parties de votre application et le chargement différé dans d’autres parties.

À titre d’exemple, supposons que vous souhaitiez interroger les clients qui vivent au Royaume-Uni et le nombre de commandes.

**Utilisation du chargement hâtif**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**Utilisation du chargement différé**

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

Lorsque vous utilisez le chargement hâtif, vous émettez une seule requête qui retourne tous les clients et toutes les commandes. La commande du Windows Store ressemble à ceci :

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

Lorsque vous utilisez le chargement différé, vous émettez initialement la requête suivante :

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

Et chaque fois que vous accédez à la propriété de navigation Orders d’un client, une autre requête semblable à la suivante est émise sur le magasin :

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

Pour plus d’informations, consultez [chargement d’objets connexes](https://msdn.microsoft.com/library/bb896272.aspx).

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 chargement différé et aide-mémoire de chargement hâtif

Il n’existe aucun moyen de choisir un chargement hâtif et un chargement paresseux, à la fois. Essayez tout d’abord de comprendre les différences entre les deux stratégies pour pouvoir prendre une décision bien éclairée. Envisagez également si votre code s’adapte à l’un des scénarios suivants :

| Scénario                                                                    | Notre suggestion                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Avez-vous besoin d’accéder à de nombreuses propriétés de navigation à partir des entités extraites ? | **Non** -les deux options vont probablement. Toutefois, si la charge de travail que votre requête est en cours d’exécution n’est pas trop importante, vous risquez d’obtenir des avantages en matière de performances en utilisant le chargement hâtif, car cela nécessite moins de boucles réseau pour matérialiser vos objets. <br/> <br/> **Oui** : Si vous devez accéder à de nombreuses propriétés de navigation à partir des entités, vous pouvez le faire à l’aide de plusieurs instructions Include dans votre requête avec un chargement hâtif. Plus vous incluez d’entités, plus la charge utile que votre requête renverra est importante. Une fois que vous avez inclus trois entités ou plus dans votre requête, envisagez de basculer vers le chargement différé. |
| Savez-vous exactement quelles données seront nécessaires au moment de l’exécution ?                   | **Le chargement** en différé est mieux adapté à vos besoins. Dans le cas contraire, vous risquez de vous retrouver dans l’interrogation des données dont vous n’aurez pas besoin. <br/> <br/> **Oui** -le chargement hâtif est probablement la meilleure solution. Cela permet de charger plus rapidement les jeux complets. Si votre requête nécessite la récupération d’une très grande quantité de données et que cette opération devient trop lente, essayez plutôt le chargement différé.                                                                                                                                                                                                                                                       |
| Votre code s’exécute-t-il loin de votre base de données ? (latence accrue du réseau)  | **Non** -lorsque la latence du réseau n’est pas un problème, l’utilisation du chargement différé peut simplifier votre code. N’oubliez pas que la topologie de votre application peut changer. par conséquent, n’accordez pas de proximité à la base de données. <br/> <br/> **Oui** -lorsque le réseau est un problème, vous seul pouvez décider de ce qui convient le mieux à votre scénario. En général, le chargement hâtif est meilleur car il nécessite moins de boucles.                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>8.2.2 problèmes de performances avec plusieurs includes

Lorsque nous entendons des questions sur les performances qui impliquent des problèmes de temps de réponse du serveur, la source du problème est souvent une requête avec plusieurs instructions include. Bien que l’inclusion d’entités associées dans une requête soit puissante, il est important de comprendre ce qui se passe en coulisses.

L’exécution d’une requête contenant plusieurs instructions Include dans le compilateur de plan interne pour générer la commande de stockage prend un temps relativement long. La majeure partie de ce temps est consacrée à la tentative d’optimisation de la requête résultante. La commande Store générée contient une jointure ou une Union externe pour chaque include, en fonction de votre mappage. Les requêtes de ce type importent des graphiques connectés volumineux à partir de votre base de données dans une seule charge utile, ce qui acerbate tous les problèmes de bande passante, en particulier lorsqu’il y a beaucoup de redondance dans la charge utile (par exemple, lorsque plusieurs niveaux de include sont utilisés pour parcourir les associations dans la direction un-à-plusieurs).

Vous pouvez vérifier les cas où vos requêtes renvoient des charges utiles excessivement volumineuses en accédant à la valeur TSQL sous-jacente pour la requête à l’aide de ToTraceString et en exécutant la commande Store dans SQL Server Management Studio pour afficher la taille de la charge utile. Dans ce cas, vous pouvez essayer de réduire le nombre d’instructions Include dans votre requête pour importer simplement les données dont vous avez besoin. Vous pouvez également décomposer votre requête en une plus petite séquence de sous-requêtes, par exemple :

**Avant de rompre la requête :**

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

**Après avoir endommagé la requête :**

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

Cela ne fonctionne que sur les requêtes suivies, car nous utilisons la capacité du contexte à effectuer automatiquement la résolution d’identité et la correction d’association.

Comme avec le chargement différé, le compromis sera plus de requêtes pour les charges utiles plus petites. Vous pouvez également utiliser des projections de propriétés individuelles pour sélectionner explicitement uniquement les données dont vous avez besoin à partir de chaque entité, mais vous ne devez pas charger d’entités dans ce cas, et les mises à jour ne seront pas prises en charge.

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>solution de contournement 8.2.3 pour bénéficier du chargement différé des propriétés

Entity Framework ne prend actuellement pas en charge le chargement différé de propriétés scalaires ou complexes. Toutefois, dans les cas où vous avez une table qui comprend un objet volumineux, tel qu’un objet BLOB, vous pouvez utiliser le fractionnement de table pour séparer les grandes propriétés dans une entité distincte. Supposons, par exemple, que vous disposiez d’une table Product qui comprend une colonne de photos varbinary. Si vous n’avez pas besoin d’accéder fréquemment à cette propriété dans vos requêtes, vous pouvez utiliser le fractionnement de table pour importer uniquement les parties de l’entité dont vous avez normalement besoin. L’entité représentant la photo de produit est chargée uniquement lorsque vous en avez besoin de manière explicite.

Une bonne ressource qui montre comment activer le fractionnement de table est le billet de blog de Gil Fink "fractionnement de table dans Entity Framework" : \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx> .

## <a name="9-other-considerations"></a>9 autres considérations

### <a name="91-server-garbage-collection"></a>Garbage collection de serveur 9,1

Certains utilisateurs peuvent rencontrer des conflits de ressources qui limitent le parallélisme qu’ils attendent quand le garbage collector n’est pas correctement configuré. Chaque fois que EF est utilisé dans un scénario multithread ou dans une application qui ressemble à un système côté serveur, veillez à activer le garbage collection de serveur. Pour cela, vous avez besoin d’un paramètre simple dans votre fichier de configuration d’application :

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

Cela devrait réduire la contention de vos threads et augmenter votre débit de 30% dans les scénarios de saturation de l’UC. En général, vous devez toujours tester le comportement de votre application à l’aide du garbage collection classique (qui est mieux adapté aux scénarios d’interface utilisateur et côté client), ainsi qu’au garbage collection de serveur.

### <a name="92-autodetectchanges"></a>9,2 AutoDetectChanges

Comme mentionné précédemment, Entity Framework peut présenter des problèmes de performances lorsque le cache d’objets a de nombreuses entités. Certaines opérations, telles que Add, Remove, Find, Entry et SaveChanges, déclenchent des appels à DetectChanges qui peuvent consommer une grande quantité d’UC en fonction de la taille du cache d’objets. Cela est dû au fait que le cache d’objets et le gestionnaire d’état d’objet essaient de rester synchronisés autant que possible sur chaque opération effectuée dans un contexte afin que les données produites soient correctes dans un vaste éventail de scénarios.

Il est généralement recommandé de conserver la détection automatique des modifications de Entity Framework activée pendant toute la durée de vie de votre application. Si votre scénario est affecté négativement par une utilisation intensive du processeur et que vos profils indiquent que le coupable est l’appel à DetectChanges, envisagez de désactiver temporairement AutoDetectChanges dans la partie sensible de votre code :

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

Avant de désactiver AutoDetectChanges, il est judicieux de comprendre qu’il est possible que Entity Framework perde sa capacité à effectuer le suivi de certaines informations sur les modifications qui ont lieu sur les entités. En cas de gestion incorrecte, cela peut entraîner une incohérence des données sur votre application. Pour plus d’informations sur la désactivation de AutoDetectChanges, consultez \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/> .

### <a name="93-context-per-request"></a>9,3 contexte par requête

Les contextes de Entity Framework sont destinés à être utilisés en tant qu’instances courtes afin de fournir une expérience de performances optimale. Les contextes sont supposés être à courte durée de vie et sont ignorés, et en tant que tels ont été implémentés pour être très légers et réutiliser les métadonnées chaque fois que cela est possible. Dans les scénarios Web, il est important de garder cela à l’esprit et de ne pas avoir de contexte plus que la durée d’une requête unique. De même, dans les scénarios non Web, le contexte doit être ignoré en fonction de votre compréhension des différents niveaux de mise en cache dans le Entity Framework. En règle générale, il est préférable d’éviter d’avoir une instance de contexte tout au long de la vie de l’application, ainsi que des contextes par thread et des contextes statiques.

### <a name="94-database-null-semantics"></a>sémantique null de la base de données 9,4

Entity Framework par défaut génère du code SQL avec une \# sémantique de comparaison C null. Prenons l'exemple de requête suivant :

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

Dans cet exemple, nous comparons un certain nombre de variables Nullable à des propriétés Nullable sur l’entité, telles que RéfFournisseur et UnitPrice. Le SQL généré pour cette requête demande si la valeur du paramètre est la même que la valeur de la colonne, ou si les valeurs du paramètre et de la colonne sont null. Cela permet de masquer la manière dont le serveur de base de données gère les valeurs NULL et fournit une expérience de type C null cohérente \# entre les différents fournisseurs de bases de données. En revanche, le code généré est un peu compliqué et peut ne pas s’exécuter correctement lorsque le nombre de comparaisons dans l’instruction WHERE de la requête augmente jusqu’à un grand nombre.

Une façon de traiter cette situation consiste à utiliser la sémantique null de la base de données. Notez que cela peut potentiellement se comporter différemment de la sémantique C null, dans la \# mesure où Entity Framework générera un SQL plus simple qui expose la manière dont le moteur de base de données gère les valeurs NULL. La sémantique null de la base de données peut être activée par contexte avec une seule ligne de configuration par rapport à la configuration du contexte :

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

Les requêtes de taille petite à moyenne n’affichent pas une amélioration notable des performances lors de l’utilisation de la sémantique null de la base de données, mais la différence est plus perceptible sur les requêtes avec un grand nombre de comparaisons de valeurs NULL potentielles.

Dans l’exemple de requête ci-dessus, la différence de performances était inférieure à 2% dans un microtest exécuté dans un environnement contrôlé.

### <a name="95-async"></a>9,5 Async

Entity Framework 6 a introduit la prise en charge des opérations asynchrones lors de l’exécution sur .NET 4,5 ou une version ultérieure. Pour l’essentiel, les applications qui ont une contention liée à l’e/s tireront le meilleur parti de l’utilisation des opérations asynchrones de requête et d’enregistrement. Si votre application ne subit pas de contention d’e/s, l’utilisation de Async va, dans les meilleurs cas, s’exécuter de façon synchrone et retourner le résultat dans le même laps de temps qu’un appel synchrone, ou dans le pire des cas, il suffit de différer l’exécution à une tâche asynchrone et d’ajouter du temps supplémentaire à la fin de votre scénario.

Pour plus d’informations sur le fonctionnement de la programmation asynchrone qui vous permet de décider si Async améliorera les performances de votre application, consultez [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx) . Pour plus d’informations sur l’utilisation des opérations asynchrones sur Entity Framework, consultez [requête Async et enregistrement](xref:ef6/fundamentals/async
).

### <a name="96-ngen"></a>9,6 NGEN

Entity Framework 6 ne figure pas dans l’installation par défaut de .NET Framework. Par conséquent, les assemblys Entity Framework ne sont pas NGEN par défaut, ce qui signifie que tous les Entity Framework code sont soumis aux mêmes coûts JIT’ing que tout autre assembly MSIL. Cela peut dégrader l’expérience F5 lors du développement et du démarrage à froid de votre application dans les environnements de production. Afin de réduire les coûts liés au processeur et à la mémoire de JIT’ing, il est recommandé de NGEN les images de Entity Framework, le cas échéant. Pour plus d’informations sur la façon d’améliorer les performances de démarrage de Entity Framework 6 avec NGEN, consultez [amélioration des performances de démarrage avec NGen](xref:ef6/fundamentals/performance/ngen).

### <a name="97-code-first-versus-edmx"></a>9,7 Code First versus EDMX

Entity Framework raisons du problème d’incompatibilité d’impédance entre la programmation orientée objet et les bases de données relationnelles en utilisant une représentation en mémoire du modèle conceptuel (les objets), le schéma de stockage (la base de données) et un mappage entre les deux. Ces métadonnées sont appelées Entity Data Model ou EDM pour Short. À partir de ce modèle EDM, Entity Framework dérivera les vues pour aller-retour des données des objets en mémoire vers la base de données et inversement.

Lorsque Entity Framework est utilisé avec un fichier EDMX qui spécifie formellement le modèle conceptuel, le schéma de stockage et le mappage, l’étape de chargement du modèle doit uniquement valider le fait que le modèle EDM est correct (par exemple, s’il n’existe pas de mappage), générer les vues, puis valider les vues et faire en sorte que ces métadonnées soient prêtes à être utilisées. Seule une requête peut être exécutée ou de nouvelles données doivent être enregistrées dans le magasin de données.

L’approche Code First est, au cœur, un générateur de Entity Data Model sophistiqué. Le Entity Framework doit générer un modèle EDM à partir du code fourni ; pour ce faire, il analyse les classes impliquées dans le modèle, en appliquant des conventions et en configurant le modèle via l’API Fluent. Une fois le modèle EDM généré, le Entity Framework se comporte essentiellement de la même façon qu’un fichier EDMX était présent dans le projet. Par conséquent, la génération du modèle à partir de Code First ajoute une complexité supplémentaire qui se traduit par un temps de démarrage plus lent pour le Entity Framework par rapport à un EDMX. Le coût dépend entièrement de la taille et de la complexité du modèle en cours de génération.

Lorsque vous choisissez d’utiliser EDMX et Code First, il est important de savoir que la flexibilité introduite par Code First augmente le coût de la création du modèle pour la première fois. Si votre application peut résister au coût de cette première charge, il est généralement Code First est la meilleure façon de procéder.

## <a name="10-investigating-performance"></a>10 examen des performances

### <a name="101-using-the-visual-studio-profiler"></a>10,1 utilisation du profileur Visual Studio

Si vous rencontrez des problèmes de performances avec la Entity Framework, vous pouvez utiliser un profileur comme celui qui est intégré à Visual Studio pour voir où votre application consacre son temps. Il s’agit de l’outil que nous avons utilisé pour générer les graphiques à secteurs dans le billet de blog « Exploring the performance of the ADO.NET Entity Framework-part 1 » (en anglais \<https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1> ) qui montre où Entity Framework passe son temps pendant les requêtes à froid et à chaud.

Le billet de blog « profilage Entity Framework à l’aide du profileur Visual Studio 2010 » écrit par l’équipe de Conseil des clients de modélisation et de données montre un exemple concret de la façon dont ils ont utilisé le profileur pour examiner un problème de   \<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler> performances. Ce billet a été écrit pour une application Windows. Si vous devez profiler une application Web, l’enregistreur de performances Windows (WPR) et les outils de l’analyseur de performances Windows (WPA) peuvent fonctionner mieux que si vous utilisiez Visual Studio. WPR et WPA font partie de Windows performance Toolkit, qui est inclus dans le kit de déploiement et d’évaluation Windows ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982) ).

### <a name="102-applicationdatabase-profiling"></a>profilage de base de données/application 10,2

Les outils tels que le profileur intégré à Visual Studio vous indiquent où votre application consacre du temps.Un autre type de profileur est disponible pour effectuer une analyse dynamique de votre application en cours d’exécution, soit en production, soit en préproduction en fonction des besoins, et recherche des pièges et des anti-modèles courants d’accès aux bases de données.

Deux profileurs disponibles dans le commerce sont le profileur Entity Framework ( \<http://efprof.com> ) et ORMProfiler ( \<http://ormprofiler.com> ).

Si votre application est une application MVC utilisant Code First, vous pouvez utiliser mini de StackExchange. Scott Hanselman décrit cet outil dans son blog à l’adresse suivante : \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx> .

Pour plus d’informations sur le profilage de l’activité de la base de données de votre application, consultez l’article du magazine MSDN de Julie Lerman intitulé [profilage de l’activité des bases de données dans le Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).

### <a name="103-database-logger"></a>enregistreur d’événements de base de données 10,3

Si vous utilisez Entity Framework 6, pensez également à utiliser la fonctionnalité de journalisation intégrée. La propriété de base de données du contexte peut être invitée à consigner son activité via une simple configuration à une seule ligne :

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

Dans cet exemple, l’activité de base de données est enregistrée dans la console, mais la propriété log peut être configurée pour appeler n’importe quel délégué de chaîne d’action &lt; &gt; .

Si vous souhaitez activer la journalisation de la base de données sans recompiler, et que vous utilisez Entity Framework 6,1 ou une version ultérieure, vous pouvez le faire en ajoutant un intercepteur dans le fichier web.config ou app.config de votre application.

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

Pour plus d’informations sur la façon d’ajouter la journalisation sans recompilation, accédez à \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/> .

## <a name="11-appendix"></a>11 Annexe

### <a name="111-a-test-environment"></a>11,1 A. environnement de test

Cet environnement utilise une configuration de 2 machines avec la base de données sur un ordinateur distinct de l’application cliente. Les machines se trouvent dans le même rack, de sorte que la latence du réseau est relativement faible, mais plus réaliste qu’un environnement à un seul ordinateur.

#### <a name="1111-app-server"></a>11.1.1 serveur d’applications

##### <a name="11111-software-environment"></a>Environnement logiciel 11.1.1.1

-   Entity Framework 4 environnement logiciel
    -   Nom du système d’exploitation : Windows Server 2008 R2 Entreprise SP1.
    -   Visual Studio 2010 – Ultimate.
    -   Visual Studio 2010 SP1 (uniquement pour certaines comparaisons).
-   Entity Framework un environnement logiciel de 5 et 6
    -   Nom du système d’exploitation : Windows 8.1 Enterprise
    -   Visual Studio 2013 – Ultimate.

##### <a name="11112-hardware-environment"></a>Environnement matériel 11.1.1.2

-   Processeur double : Intel (R) Xeon (R) CPU L5520 W3530 @ 2,27 GHz, 2261 Mhz8 GHz, 4 cœurs, 84 processeur logique (s).
-   2412 Go RamRAM.
-   lecteur 136 Go SCSI250GB SATA 7200 RPM 3 Go/s divisé en 4 partitions.

#### <a name="1112-db-server"></a>serveur 11.1.2 DB

##### <a name="11121-software-environment"></a>Environnement logiciel 11.1.2.1

-   Nom du système d’exploitation : Windows Server 2008 R 28.1 Enterprise SP1.
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>Environnement matériel 11.1.2.2

-   Processeur unique : Intel (R) Xeon (R) CPU L5520 @ 2,27 GHz, 2261 MhzES-1620 0 @ 3,60 GHz, 4 cœurs (s), 8 processeurs logiques.
-   824 Go RamRAM.
-   lecteur 465 Go ATA500GB SATA 7200 RPM 6 Go/s divisé en 4 partitions.

### <a name="112-b-query-performance-comparison-tests"></a>11,2 B. tests de comparaison des performances des requêtes

Le modèle Northwind a été utilisé pour exécuter ces tests. Il a été généré à partir de la base de données à l’aide du concepteur de Entity Framework. Ensuite, le code suivant a été utilisé pour comparer les performances des options d’exécution de la requête :

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

### <a name="113-c-navision-model"></a>11,3 C. modèle Navision

La base de données Navision est une base de données volumineuse utilisée pour la démonstration de Microsoft Dynamics-NAV. Le modèle conceptuel généré contient les jeux d’entités 1005 et les ensembles d’associations 4227. Le modèle utilisé dans le test est « Flat », aucun héritage n’a été ajouté à ce dernier.

#### <a name="1131-queries-used-for-navision-tests"></a>requêtes 11.3.1 utilisées pour les tests Navision

La liste de requêtes utilisée avec le modèle Navision contient 3 catégories de Entity SQL requêtes :

##### <a name="11311-lookup"></a>recherche 11.3.1.1

Requête de recherche simple sans agrégations

-   Nombre : 16232
-   Exemple :

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 SingleAggregating

Requête BI normale avec plusieurs agrégations, mais aucun sous-total (requête unique)

-   Nombre : 2313
-   Exemple :

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

Où MDF \_ SessionLogin \_ Time \_ Max () est défini dans le modèle de la façon suivante :

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 AggregatingSubtotals

Une requête BI avec des agrégations et des sous-totaux (par le biais de l’instruction Union All)

-   Nombre : 178
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
