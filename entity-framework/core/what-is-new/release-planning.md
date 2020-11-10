---
title: Planification des versions de EF Core
description: Informations sur la façon dont Entity Framework Core la planification et la publication sont terminées
author: ajcvickers
ms.date: 01/28/2020
uid: core/what-is-new/release-planning
ms.openlocfilehash: f84b8cef40a74245575df6013d94fcda5738e229
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429141"
---
# <a name="release-planning-process"></a>Processus de planning des versions

On nous demande souvent comment nous choisissons les fonctionnalités qui seront intégrées dans une version donnée.
Ce document décrit le processus que nous utilisons.
Le processus évolue constamment au fur et à mesure que nous trouvons de meilleures méthodes de planification, mais les idées générales restent les mêmes.

## <a name="different-kinds-of-releases"></a>Différents genres de mises en production

Les différents types de mise en version contiennent différents genres de modifications.
Cela signifie à son tour que la planification des versions est différente pour différents types de version.

### <a name="patch-releases"></a>Mises à jour correctives

Les mises à jour correctives modifient uniquement la partie « correctif » de la version.
Par exemple, EF Core 3,1. **1** est une version qui corrige les problèmes rencontrés dans EF Core 3,1. **0**.

Les mises à jour correctives sont destinées à corriger les bogues critiques du client.
Cela signifie que les mises à jour correctives n’incluent pas de nouvelles fonctionnalités.
Les modifications d’API ne sont pas autorisées dans les versions correctives, sauf dans des circonstances particulières.

La barre pour apporter une modification dans une version de correctif est très élevée.
Cela est dû au fait qu’il est essentiel que les mises à jour correctives n’introduisent pas de nouveaux bogues.
Par conséquent, le processus de décision met l’accent sur une valeur élevée et un risque faible.

Nous sommes plus susceptibles de corriger un problème dans les cas suivants :

* Il a un impact sur plusieurs clients
* Il s’agit d’une régression à partir d’une version précédente
* L’échec provoque la corruption des données

Nous sommes moins susceptibles de corriger un problème dans les cas suivants :

* Il existe des solutions de contournement raisonnables
* Le correctif présente un risque élevé de rupture d’autre chose
* Le bogue se trouve dans un cas d’angle

Cette barre dépasse progressivement la durée de vie d’une version de [support à long terme (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) . Cela est dû au fait que les versions LTS insistent sur la stabilité.

La décision finale quant à la nécessité ou non de corriger un problème est faite par les directeurs .NET de Microsoft.

### <a name="minor-releases"></a>Versions mineures

Les versions mineures modifient uniquement la partie « mineure » de la version.
Par exemple, EF Core 3. **1**. 0 est une version qui s’améliore sur EF Core 3. **0**. 0.

Versions mineures :

* Visent à améliorer la qualité et les fonctionnalités de la version précédente
* Contiennent généralement des correctifs de bogues et de nouvelles fonctionnalités
* Ne pas inclure les modifications avec rupture intentionnelle
* Quelques préversions préliminaires sont envoyées à NuGet

### <a name="major-releases"></a>Versions majeures

Les versions majeures modifient le numéro de version d’EF « majeur ».
Par exemple, EF Core **3**. 0,0 est une version majeure qui fait un grand pas en avant sur EF Core 2.2. x.

Versions majeures :

* Visent à améliorer la qualité et les fonctionnalités de la version précédente
* Contiennent généralement des correctifs de bogues et de nouvelles fonctionnalités
  * Certaines des nouvelles fonctionnalités peuvent être des modifications fondamentales de la façon dont EF Core fonctionne
* Inclure généralement les modifications avec rupture intentionnelle
  * Les modifications importantes sont nécessaires dans le cadre de l’évolution des EF Core
  * Toutefois, nous réfléchissons très bien à la réalisation d’une modification avec rupture en raison de l’impact potentiel sur le client. Nous avons peut-être été trop ambitieux avec des modifications avec rupture dans le passé. À l’avenir, nous allons nous efforcer de réduire les modifications qui interrompent les applications et de réduire les modifications qui rompent les fournisseurs de base de données et les extensions.
* Plusieurs préversions préliminaires sont envoyées à NuGet

## <a name="planning-for-majorminor-releases"></a>Planification des versions majeures et mineures

### <a name="github-issue-tracking"></a>Suivi des problèmes GitHub

GitHub ( [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore) ) est la source de vérité pour toutes les EF Core planification.

Les problèmes sur GitHub ont :

* Un État
  * Les problèmes [ouverts](https://github.com/dotnet/efcore/issues) n’ont pas été traités.
  * Des problèmes [fermés](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed) ont été résolus.
    * Tous les problèmes qui ont été résolus sont signalés [par un indicateur fermé](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed). Un problème marqué avec l’état fermé est fixe et fusionné, mais n’a peut-être pas été publié.
    * `closed-`D’autres étiquettes indiquent d’autres raisons justifiant la fermeture d’un problème. Par exemple, les doublons sont marqués avec Closed-duplicate.
* Un type
  * Les [bogues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug) représentent des bogues.
  * Les [améliorations](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement) représentent de nouvelles fonctionnalités ou de meilleures fonctionnalités dans les fonctionnalités existantes.
* Une étape majeure
  * Les [problèmes sans jalon](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone) sont pris en compte par l’équipe. La décision relative à la marche à suivre pour le problème n’a pas encore été prise ou une modification de la décision est prise en compte.
  * Les [problèmes dans le jalon du backlog](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog) sont des éléments que l’équipe EF envisage de travailler dans une version ultérieure
    * Les problèmes dans le journal des travaux en souffrance peuvent être [marqués avec considérer comme une version suivante](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release) indiquant que cet élément de travail est élevé dans la liste de la prochaine version.
  * Les problèmes ouverts dans une étape majeure avec version sont des éléments sur lesquels l’équipe envisage de travailler dans cette version. Par exemple, il [s’agit des problèmes que nous envisageons de travailler pour EF Core 5,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0).
  * Les problèmes fermés dans une étape majeure avec version sont ceux qui sont terminés pour cette version. Notez que la version n’a peut-être pas encore été publiée. Par exemple, [il s’agit des problèmes effectués pour EF Core 3,0](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed).
* Obtenues!
  * Le vote est la meilleure façon d’indiquer qu’un problème est important pour vous.
  * Pour voter, ajoutez simplement un « pouce vers le haut » 👍 pour le problème. Par exemple, [il s’agit des problèmes les plus prononcés](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) .
  * Ajoutez également un commentaire décrivant les raisons spécifiques pour lesquelles vous avez besoin de cette fonctionnalité si vous pensez que cela ajoute une valeur. Les commentaires « + 1 » ou similaires n’ajoutent pas de valeur.

### <a name="the-planning-process"></a>Processus de planification

Le processus de planification est plus complexe que la simple utilisation des fonctionnalités les plus demandées dans le journal des travaux en souffrance.
Cela est dû au fait que nous recueillons les commentaires de plusieurs parties prenantes de plusieurs façons.
Nous mettons ensuite en forme une version basée sur :

* Entrée des clients
* Entrée d’autres parties prenantes
* Direction stratégique
* Ressources disponibles
* Planifier

Voici quelques-unes des questions que nous demandons :

1. **Combien de développeurs vont utiliser la fonctionnalité selon nous et dans quelle mesure va-t-elle améliorer leurs applications ou leur expérience ?** Pour répondre à cette question, nous recueillons les commentaires provenant de nombreuses sources (les commentaires et votes sur les problèmes en sont une). Les engagement spécifiques avec des clients importants sont un autre.

2. **Quelles sont les solutions de contournement possibles si nous n’implémentons pas encore cette fonctionnalité ?** Par exemple, de nombreux développeurs peuvent mapper une table de jointure afin de pallier l’absence de prise en charge native du mappage plusieurs-à-plusieurs. Évidemment, tous les développeurs ne peuvent pas le faire, mais beaucoup en sont capables, ce qui constitue un facteur important dans notre décision.

3. **L’implémentation de cette fonctionnalité fait-elle évoluer l’architecture d’EF Core de telle manière que l’implémentation d’autres fonctionnalités s’en voit facilitée ou plus probable ?** Nous avons tendance à privilégier les fonctionnalités qui agissent comme blocs de construction pour d’autres fonctionnalités. Par exemple, les entités de sac de propriétés peuvent faciliter le passage à la prise en charge du mappage plusieurs-à-plusieurs et les constructeurs d’entité permettent la prise en charge de notre chargement différé.

4. **La fonctionnalité est-elle un point d’extensibilité ?** Nous avons tendance aussi à favoriser les points d’extensibilité au détriment des fonctionnalités standard, car ils permettent aux développeurs de raccorder leurs propres comportements et d’obtenir ainsi une compensation pour certaines fonctionnalités manquantes.

5. **Quelle est la synergie de la fonctionnalité quand elle est utilisée conjointement avec d’autres produits ?** Nous favorisons les fonctionnalités qui permettent ou améliorent considérablement l’utilisation d’EF Core avec d’autres produits, comme .NET Core, la dernière version de Visual Studio, Microsoft Azure et ainsi de suite.

6. **Quelles sont les compétences des personnes disponibles pour travailler sur une fonctionnalité et comment tirer le meilleur parti de ces ressources ?** Chaque membre de l’équipe EF et nos collaborateurs de la Communauté ont différents niveaux d’expérience dans différents domaines, et nous devons donc planifier en conséquence. Même si nous souhaitions faire appel en même temps à toutes ces ressources pour travailler sur une fonctionnalité spécifique telle que les traductions GroupBy ou le mappage plusieurs-à-plusieurs, ce ne serait pas pratique.
