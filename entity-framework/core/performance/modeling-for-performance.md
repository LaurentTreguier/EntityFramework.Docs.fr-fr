---
title: Modélisation des performances-EF Core
description: Modélisation efficace lors de l’utilisation de Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/modeling-for-performance
ms.openlocfilehash: fc16ec67c3865aa7b7a95519463ca7493a2709b0
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657822"
---
# <a name="modeling-for-performance"></a>Modélisation des performances

Dans de nombreux cas, la façon dont vous modélisez le modèle peut avoir un impact important sur les performances de votre application. Bien qu’un modèle correctement normalisé et « correct » soit généralement un bon point de départ, dans des applications réelles, certaines compromissions pragmatiques peuvent être longues pour obtenir de bonnes performances. Étant donné qu’il est assez difficile de modifier votre modèle une fois qu’une application s’exécute en production, il est utile de garder à l’esprit les performances lors de la création du modèle initial.

## <a name="denormalization-and-caching"></a>Dénormalisation et mise en cache

La *dénormalisation* est la pratique consistant à ajouter des données redondantes à votre schéma, généralement afin d’éliminer les jointures lors de l’interrogation. Par exemple, pour un modèle avec des blogs et des billets, où chaque publication a une évaluation, vous pouvez être amené à afficher fréquemment l’évaluation moyenne du blog. L’approche la plus simple consiste à regrouper les publications par blog et à calculer la moyenne dans le cadre de la requête. mais cela nécessite une jointure coûteuse entre les deux tables. La dénormalisation ajoute la moyenne calculée de toutes les publications à une nouvelle colonne sur le blog, afin qu’elle soit immédiatement accessible, sans jointure ou calcul.

L’exemple ci-dessus peut être considéré comme une forme de *mise en cache* : les informations agrégées des publications sont mises en cache sur leur blog. et comme avec n’importe quelle mise en cache, le problème est de maintenir à jour la valeur mise en cache avec les données qu’elle met en cache. Dans de nombreux cas, il est OK que les données mises en cache se décalent un peu ; par exemple, dans l’exemple ci-dessus, il est généralement raisonnable que l’évaluation moyenne du blog ne soit pas complètement à jour à un moment donné. Si c’est le cas, vous pouvez le recalculer à chaque instant, puis dans le cas contraire, un système plus élaboré doit être configuré pour maintenir à jour les valeurs mises en cache.

Les informations suivantes décrivent certaines techniques de dénormalisation et de mise en cache dans EF Core, et pointent vers les sections pertinentes de la documentation.

### <a name="stored-computed-columns"></a>Colonnes calculées stockées

Si les données à mettre en cache sont un produit d’autres colonnes de la même table, une [colonne calculée stockée](xref:core/modeling/generated-properties#computed-columns) peut être une solution parfaite. Par exemple, un `Customer` peut avoir `FirstName` des `LastName` colonnes et, mais il peut être nécessaire de rechercher le *nom complet* du client. Une colonne calculée stockée est gérée automatiquement par la base de données, qui la recalcule à chaque modification de la ligne. vous pouvez même définir un index sur celle-ci pour accélérer les requêtes.

### <a name="update-cache-columns-when-inputs-change"></a>Mettre à jour les colonnes du cache lors de la modification des entrées

Si votre colonne mise en cache doit référencer des entrées provenant de l’extérieur de la ligne de la table, vous ne pouvez pas utiliser de colonnes calculées. Toutefois, il est toujours possible de recalculer la colonne chaque fois que son entrée change ; par exemple, vous pouvez recalculer la notation moyenne du blog chaque fois qu’une publication est modifiée, ajoutée ou supprimée. Veillez à identifier les conditions exactes lorsque le recalcul est nécessaire. sinon, la valeur mise en cache ne sera plus synchronisée.

Pour ce faire, vous pouvez procéder à la mise à jour par le biais de l’API de EF Core régulière. `SaveChanges`Les [événements](xref:core/logging-events-diagnostics/events) ou les [intercepteurs](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) peuvent être utilisés pour vérifier automatiquement si des publications sont en cours de mise à jour et pour effectuer le recalcul de cette façon. Notez que cela implique généralement des allers-retours de base de données supplémentaires, car des commandes supplémentaires doivent être envoyées.

Pour d’autres applications sensibles aux performances, les déclencheurs de base de données peuvent être définis pour effectuer automatiquement le recalcul dans la base de données. Cela enregistre les allers-retours de base de données supplémentaires, se produit automatiquement dans la même transaction que la mise à jour principale et peut être plus simple à configurer. EF ne fournit pas d’API spécifique pour la création ou la gestion des déclencheurs, mais il est parfait de [créer une migration vide et d’ajouter la définition du déclencheur par le biais de SQL brut](xref:core/managing-schemas/migrations/managing#arbitrary-changes-via-raw-sql).

### <a name="materialized-views"></a>Vues matérialisées

Les vues matérialisées sont similaires aux vues régulières, à ceci près que leurs données sont stockées sur le disque (« matérialisé »), plutôt que calculées chaque fois que la vue est interrogée. Cet outil est utile lorsque vous ne souhaitez pas simplement ajouter une seule colonne de cache à une base de données existante, mais que vous souhaitez plutôt mettre en cache l’ensemble du jeu de résultats des résultats d’une requête complexe et coûteuse, comme s’il s’agissait d’une table normale. Ces résultats peuvent ensuite être interrogés de façon très économique sans calcul ou jointures. Contrairement aux colonnes calculées, les vues matérialisées ne sont pas automatiquement mises à jour lorsque leurs tables sous-jacentes changent. elles doivent être actualisées manuellement. Si les données mises en cache peuvent avoir un décalage, l’actualisation de la vue peut être effectuée via un minuteur. une autre option consiste à configurer des déclencheurs de base de données pour passer en revue une vue matérialisée lorsque certains événements de base de données se produisent.

EF ne fournit actuellement aucune API spécifique pour la création ou la gestion d’affichages, matérialisés ou autres. mais il est tout à fait parfait de [créer une migration vide et d’ajouter la définition de la vue par le biais de SQL brut](xref:core/managing-schemas/migrations/managing#arbitrary-changes-via-raw-sql).

## <a name="inheritance-mapping"></a>Mappage de l’héritage

Avant de poursuivre cette section, il est recommandé de lire [la page dédiée sur l’héritage](xref:core/modeling/inheritance) .

EF Core prend actuellement en charge deux techniques pour mapper un modèle d’héritage à une base de données relationnelle :

* TPH ( **table par hiérarchie** ), dans laquelle une hiérarchie .net entière de classes est mappée à une table de base de données unique
* **Table par type** (TPT), dans laquelle chaque type de la hiérarchie .net est mappé à une table différente dans la base de données.

Le choix de la technique de mappage de l’héritage peut avoir un impact considérable sur les performances de l’application. il est recommandé de mesurer soigneusement avant de procéder à un choix.

Les gens choisissent parfois TPT, car il s’agit d’une technique de « nettoyage ». une table distincte pour chaque type .NET rend le schéma de base de données similaire à la hiérarchie des types .NET. En outre, dans la mesure où TPH doit représenter l’intégralité de la hiérarchie dans une seule table, les lignes ont *toutes les* colonnes, quel que soit le type réellement détenu dans la ligne, et les colonnes non liées sont toujours vides et inutilisées. Hormis le fait qu’il s’agit d’une technique de mappage « sans nettoyage », beaucoup pensent que ces colonnes vides occupent de l’espace considérable dans la base de données et peuvent également nuire aux performances.

Toutefois, la mesure montre que TPT est dans la plupart des cas la technique de mappage inférieure du point de vue des performances ; Lorsque toutes les données de TPH proviennent d’une seule table, les requêtes TPT doivent rejoindre ensemble plusieurs tables, et les jointures sont l’une des principales sources de problèmes de performances dans les bases de données relationnelles. Les bases de données ont généralement tendance à gérer correctement les colonnes vides, et les fonctionnalités telles que les [SQL Server les colonnes éparses](/sql/relational-databases/tables/use-sparse-columns) peuvent encore réduire cette surcharge.

Pour obtenir un exemple concret, [consultez ce test d’évaluation](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/Inheritance.cs) qui définit un modèle simple avec une hiérarchie de type 7. 5000 lignes sont amorcées pour chaque type-total 35000 lignes-et l’évaluation charge simplement toutes les lignes de la base de données :

| Méthode |     Moyenne |   Erreur |  StdDev |     Génération 0 |     GEN 1 |     Génération 2 | Allocated |
|------- |---------:|--------:|--------:|----------:|----------:|----------:|----------:|
|    HIÉRARCHIE | 132,3 ms | 2,29 ms | 2,03 MS | 8000,0000 | 3000,0000 | 1250,0000 |  44,49 MO |
|    TPT | 201,3 ms | 3,32 ms | 3,10 ms | 9000,0000 | 4000,0000 |         - |  61,84 MO |

Comme vous pouvez le voir, TPH est beaucoup plus efficace que TPT pour ce scénario. Notez que les résultats réels dépendent toujours de la requête spécifique en cours d’exécution et du nombre de tables dans la hiérarchie, afin que les autres requêtes puissent présenter un écart de performances différent. vous êtes encouragé à utiliser ce code d’évaluation comme modèle pour tester d’autres requêtes.
