---
title: Présentation des performances-EF Core
description: Guide des performances pour l’utilisation efficace de Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/miscellaneous/performance/index
ms.openlocfilehash: 14400d81ea3c93e2ebf40e8e585a457abf31478f
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657811"
---
# <a name="introduction-to-performance"></a>Présentation des performances

Les performances de la base de données sont un sujet vaste et complexe, couvrant une pile complète de composants : la base de données, la mise en réseau, le pilote de base de données et les couches d’accès aux données, telles que les EF Core. Bien que les couches de haut niveau et O/RMs, comme EF Core simplifier considérablement le développement d’applications et améliorer la maintenabilité, elles peuvent parfois être opaques, masquant des détails internes critiques, tels que le SQL en cours d’exécution. Cette section tente de fournir une vue d’ensemble de la façon d’obtenir de bonnes performances avec EF Core, et d’éviter les pièges courants qui peuvent dégrader les performances de l’application.

## <a name="identify-bottlenecks-and-measure-measure-measure"></a>Identifier les goulots d’étranglement et mesurer, mesurer, mesurer

Comme toujours avec les performances, il est important de ne pas vous prépasser de l’optimisation sans les données qui présentent un problème. comme c’est le cas de la bonne Knuth Donald, « optimisation prématurée est la racine de tout le mal ». La section [diagnostic des performances](xref:core/performance/performance-diagnosis) aborde les différentes façons de comprendre où votre application consacre du temps à la logique de la base de données et comment identifier les zones problématiques spécifiques. Une fois qu’une requête lente a été identifiée, les solutions peuvent être prises en compte : votre base de données n’a-t-elle pas d’index ? Devez-vous essayer d’autres modèles d’interrogation ?

Toujours évaluer votre code et vos alternatives possibles-la section diagnostic des performances contient un exemple de test avec BenchmarkDotNet, que vous pouvez utiliser comme modèle pour vos propres tests d’évaluation. Ne partez pas du principe que les bancs d’essai publics généraux s’appliquent à votre cas d’utilisation spécifique. un grand nombre de facteurs tels que la latence de la base de données, la complexité des requêtes et les quantités réelles des données dans vos tables peuvent avoir un impact important sur la solution la plus appropriée. Par exemple, de nombreux tests publics sont effectués dans des conditions de mise en réseau idéales, où la latence à la base de données est presque nulle et avec des requêtes extrêmement claires qui ne nécessitent pas de traitement (ou d’e/s de disque) côté base de données. Bien qu’elles soient utiles pour comparer les surcharges d’exécution des différentes couches d’accès aux données, les différences qu’elles révèlent sont généralement négligeables dans une application réelle, où la base de données effectue le travail réel et la latence à la base de données est un facteur de performances significatif.

## <a name="aspects-of-data-access-performance"></a>Aspects des performances d’accès aux données

Les performances globales de l’accès aux données peuvent être décomposées dans les grandes catégories suivantes :

* **Performances de base de données pures**. Avec la base de données relationnelle, EF traduit les requêtes LINQ de l’application en instructions SQL exécutées par la base de données ; ces instructions SQL elles-mêmes peuvent s’exécuter plus ou moins efficacement. L’index approprié dans le bon endroit peut faire la différence dans les performances de SQL, ou la réécriture de votre requête LINQ peut rendre EF générer une meilleure requête SQL.
* **Transfert de données réseau**. Comme pour n’importe quel système de mise en réseau, il est important de limiter la quantité de données entrantes et sortantes sur le câble. Cela vous permet de vous assurer que vous envoyez et chargez uniquement les données dont vous aurez besoin, mais également d’éviter l’effet « explosion cartésien » lors du chargement des entités associées.
* **Allers-retours réseau**. Au-delà de la quantité de données entrantes et sortantes, les boucles réseau, depuis le temps nécessaire à l’exécution d’une requête dans la base de données, peuvent être comprises entre les paquets de temps qui transitent entre votre application et votre base de données. La surcharge de bouclage dépend principalement de votre environnement. plus votre serveur de base de données est éloigné, plus la latence est élevée et sont coûteuses chaque aller-retour. Avec l’avènement du Cloud, les applications se retrouvent plus loin dans la base de données et les applications « bavardes » qui effectuent un trop grand nombre d’allers-retours avec dégradation des performances. Par conséquent, il est important de comprendre exactement quand votre application contacte la base de données, le nombre d’allers-retours qu’elle effectue et si ce nombre peut être réduit.
* **Charge du runtime EF**. Enfin, EF ajoute une surcharge de Runtime aux opérations de base de données : EF doit compiler vos requêtes à partir de LINQ to SQL (bien que cela ne soit normalement effectué qu’une seule fois), le suivi des modifications ajoute une surcharge (mais peut être désactivée), etc. Dans la pratique, la surcharge EF pour les applications réelles est susceptible d’être négligeable dans la plupart des cas, car la durée d’exécution des requêtes dans la base de données et la latence du réseau dominent la durée totale. mais il est important de comprendre ce que sont vos options et comment éviter certains pièges.

## <a name="know-whats-happening-under-the-hood"></a>Découvrez ce qui se passe en coulisses

EF permet aux développeurs de se concentrer sur la logique métier en générant SQL, en matérialisé les résultats et en effectuant d’autres tâches. Comme n’importe quelle couche ou abstraction, elle a également tendance à masquer ce qui se passe en coulisse, comme les requêtes SQL réelles en cours d’exécution. Les performances ne sont pas nécessairement un aspect essentiel de chaque application, mais dans les applications où c’est le cas, il est vital que le développeur comprenne ce que EF fait pour eux : inspecter les requêtes SQL sortantes, suivre les allers-retours pour vérifier que le problème N + 1 ne se produit pas, etc.

## <a name="cache-outside-the-database"></a>Mise en cache en dehors de la base de données

Enfin, le moyen le plus efficace d’interagir avec une base de données, est de ne pas interagir avec lui. En d’autres termes, si l’accès à la base de données s’affiche comme un goulot d’étranglement au niveau des performances dans votre application, il peut être utile de mettre en cache certains résultats en dehors de la base de données, afin de réduire les demandes. Bien que la mise en cache augmente la complexité, il s’agit d’une partie essentielle de toute application évolutive : bien que la couche application puisse être facilement mise à l’échelle en ajoutant des serveurs supplémentaires pour gérer une charge accrue, la mise à l’échelle de la couche base de données est généralement beaucoup plus compliquée.
