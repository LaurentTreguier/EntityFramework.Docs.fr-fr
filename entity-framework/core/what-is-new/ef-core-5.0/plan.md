---
title: Planifier Entity Framework Core 5,0
description: Les fonctionnalités prévues pour Entity Framework Core 5,0
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: ba56a5f3c79dacc25b3402be114c57067da49433
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129042"
---
# <a name="plan-for-entity-framework-core-50"></a>Planifier Entity Framework Core 5,0

> [!IMPORTANT]
> EF Core 5,0 [est désormais disponible](xref:core/what-is-new/index). Cette page reste un enregistrement historique du plan.

Comme décrit dans le [processus de planification](xref:core/what-is-new/release-planning), nous avons rassemblé les entrées des parties prenantes dans un plan provisoire pour la version EF Core 5,0.

> [!IMPORTANT]
> Ce plan est toujours un travail en cours. Rien ici n’est un engagement. Ce plan est un point de départ qui évoluera à mesure que nous en apprendrons davantage. Certains éléments qui ne sont pas actuellement planifiés pour 5,0 peuvent être extraits. Certains éléments actuellement planifiés pour 5,0 peuvent devenir punted.

## <a name="general-information"></a>Informations générales

### <a name="version-number-and-release-date"></a>Numéro de version et date de publication

La version de EF Core 5,0 est actuellement planifiée en [même temps que .net 5,0](https://devblogs.microsoft.com/dotnet/introducing-net-5/). La version « 5,0 » a été choisie pour s’aligner sur .NET 5,0.

### <a name="supported-platforms"></a>Plateformes prises en charge

EF Core 5,0 est planifiée pour s’exécuter sur toute plateforme .NET Standard 2,1, y compris .NET 5,0. Cela fait partie de la convergence .NET plus générale [des plateformes à .net Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).

EF Core 5,0 ne s’exécutera pas sur .NET Framework.

### <a name="breaking-changes"></a>Changements cassants

EF Core 5,0 contiendra des [modifications avec rupture](xref:core/what-is-new/ef-core-5.0/breaking-changes), mais celles-ci seront bien moins sévères que dans le cas de EF Core 3,0. Notre objectif est de permettre la mise à jour sans interruption de la grande majorité des applications.

Il est prévu qu’il y ait des modifications avec rupture pour les fournisseurs de base de données, en particulier pour la prise en charge de TPT. Toutefois, nous pensons que le travail de mise à jour d’un fournisseur pour 5,0 sera inférieur à la nécessité de mettre à jour 3,0.

## <a name="themes"></a>Thèmes

Nous avons extrait quelques principaux domaines ou thèmes qui constitueront la base des investissements importants en EF Core 5,0.

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a>Mappage de plusieurs-à-plusieurs entièrement transparent par Convention

Développeurs en chef : @smitpatel , @AndriySvyryd et @lajones

Suivi par [#10508](https://github.com/dotnet/efcore/issues/10508)

Taille de T-shirt : L

État : terminé

Plusieurs-à-plusieurs est la [fonctionnalité la plus demandée](https://github.com/dotnet/efcore/issues/1368) (environ 506 votes) sur le backlog github.

La prise en charge des relations plusieurs-à-plusieurs peut être divisée en trois domaines principaux :

* Ignorer les propriétés de navigation--couvertes par le thème suivant.
* Types d’entité de conteneur de propriétés. Celles-ci permettent l’utilisation d’un type CLR standard (par exemple, `Dictionary` ) pour les instances d’entité, de sorte qu’un type CLR explicite n’est pas nécessaire pour chaque type d’entité. Suivi par [#9914](https://github.com/dotnet/efcore/issues/9914).
* Sucre pour une configuration facile des relations plusieurs-à-plusieurs.

En plus de la prise en charge de la navigation par omission, nous allons maintenant extraire ces autres zones de plusieurs-à-plusieurs dans EF Core 5,0 afin de fournir une expérience complète.

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a>Propriétés de navigation plusieurs-à-plusieurs (a. k. a "ignorer les navigations")

Développeurs en chef : @smitpatel et @AndriySvyryd

Suivi par [#19003](https://github.com/dotnet/efcore/issues/19003)

Taille de T-shirt : L

État : terminé

Comme décrit dans le premier thème, la prise en charge de plusieurs-à-plusieurs a plusieurs aspects.
Ce thème suit spécifiquement l’utilisation des navigations ignorées.
Nous pensons que le bloqueur le plus significatif pour ceux qui souhaitent une prise en charge de type plusieurs-à-plusieurs ne peut pas utiliser les relations « naturelles », sans faire référence à la table de jointure, dans une logique métier telle que des requêtes.
Le type d’entité de la table de jointure peut encore exister, mais il ne doit pas être dans la logique métier.

## <a name="table-per-type-tpt-inheritance-mapping"></a>Mappage de l’héritage TPT (table par type) (TPT)

Développeur principal : @AndriySvyryd et @smitpatel

Suivi par [#2266](https://github.com/dotnet/efcore/issues/2266)

Taille de T-shirt : XL

État : terminé

Nous faisons TPT parce qu’il s’agit d’une fonctionnalité hautement demandée (environ 289 votes ; 3e au total) et qu’elle nécessite des modifications de bas niveau que nous pensons à la nature fondamentale du plan .NET 5 global. Nous pensons que cela entraînera des modifications avec rupture pour les fournisseurs de base de données, bien que ceux-ci soient nettement moins sévères que les modifications requises pour 3,0.

## <a name="filtered-include"></a>Include filtré

Développeur principal : @maumar

Suivi par [#1833](https://github.com/dotnet/efcore/issues/1833)

Taille de T-shirt : M

État : terminé

Le modèle include filtré est une fonctionnalité très demandée (environ 376 votes ; 2e au total) qui n’est pas une énorme quantité de travail, et que nous pensons autoriser à débloquer ou à faciliter de nombreux scénarios qui nécessitent actuellement des filtres au niveau du modèle ou des requêtes plus complexes.

## <a name="split-include"></a>Fractionner inclure

Développeur principal : @smitpatel

Suivi par [#20892](https://github.com/dotnet/efcore/issues/20892)

Taille de T-shirt : L

État : terminé

EF Core 3,0 a modifié le comportement par défaut pour créer une requête SQL unique pour une requête LINQ donnée.
Cela entraînait des régressions de performances importantes pour les requêtes qui utilisent include pour plusieurs collections.

Dans EF Core 5,0, nous conservons le nouveau comportement par défaut.
Toutefois, EF Core 5,0 permet désormais la génération de plusieurs requêtes pour la collection, notamment lorsque l’utilisation d’une seule requête entraîne des performances incorrectes.

## <a name="required-one-to-one-dependents"></a>Dépendants un-à-un requis

Développeurs en chef : @AndriySvyryd et @smitpatel

Suivi par [#12100](https://github.com/dotnet/efcore/issues/12100)

Taille de T-shirt : M

État : terminé

Dans EF Core 3,0, tous les dépendants, y compris les types détenus, sont facultatifs (par exemple, Person. Address peut avoir la valeur null). Dans EF Core 5,0, les dépendants peuvent être configurés en fonction des besoins.

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a>Rationaliser ToTable, ToQuery, ToView, FromSql, etc.

Développeurs en chef : @AndriySvyryd et @smitpatel

Suivi par [#17270](https://github.com/dotnet/efcore/issues/17270)

Taille de T-shirt : L

État : terminé

Nous avons progressé dans les versions précédentes jusqu’à la prise en charge de SQL brut, de types sans clé et de zones associées. Toutefois, il existe des lacunes et des incohérences dans la façon dont tout fonctionne ensemble dans son ensemble. L’objectif de 5,0 est de les corriger et de créer une bonne expérience pour la définition, la migration et l’utilisation de différents types d’entités et de leurs requêtes et artefacts de base de données associés. Cela peut également impliquer des mises à jour de l’API de requête compilée.

Notez que cet élément peut entraîner des modifications avec rupture au niveau de l’application, car certaines des fonctionnalités dont nous disposons actuellement sont trop permissive, ce qui permet à l’utilisateur de mener rapidement des gens sur des problèmes. Nous nous contenterons de bloquer certaines de ces fonctionnalités, ainsi que des conseils sur la marche à suivre.

## <a name="general-query-enhancements"></a>Améliorations générales relatives aux requêtes

Développeurs en chef : @smitpatel et @maumar

Suivi par des [problèmes étiquetés avec `area-query` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)

Taille de T-shirt : XL

État : terminé

Le code de traduction de la requête a été largement réécrit pour EF Core 3,0. Le code de requête est généralement dans un état bien plus robuste. Pour 5,0, nous ne prévoyons pas de modifier les requêtes majeures, en dehors de celles qui sont nécessaires pour prendre en charge les propriétés de navigation TPT et Skip. Toutefois, il y a toujours un travail important nécessaire pour résoudre une dette technique à partir de la révision 3,0. Nous prévoyons également de résoudre de nombreux bogues et d’implémenter de petites améliorations pour améliorer l’expérience globale des requêtes.

## <a name="migrations-and-deployment-experience"></a>Migrations et expérience de déploiement

Développeurs en chef : @bricelam

Suivi par [#19587](https://github.com/dotnet/efcore/issues/19587)

Taille de T-shirt : L

État : étendues/terminées

Étendue : la [fonctionnalité de lots de migrations](https://github.com/dotnet/efcore/issues/19693) a été différée jusqu’à la fin de la EF Core version 5,0. Toutefois, plusieurs autres [améliorations ciblées relatives aux migrations](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) seront incluses dans EF Core 5,0

Actuellement, de nombreux développeurs migrent leurs bases de données au moment du démarrage de l’application. C’est facile, mais cela n’est pas recommandé car :

* Plusieurs threads/processus/serveurs peuvent tenter de migrer la base de données simultanément
* Les applications peuvent tenter d’accéder à un état incohérent alors que cela se produit
* En général, les autorisations de base de données pour modifier le schéma ne doivent pas être accordées pour l’exécution de l’application
* Il est difficile de revenir à un état propre si un problème se pose

Nous souhaitons offrir une meilleure expérience ici, qui permet de migrer facilement la base de données au moment du déploiement. Cela doit :

* Travailler sur Linux, Mac et Windows
* Soyez une bonne expérience sur la ligne de commande
* Scénarios de prise en charge avec conteneurs
* Travailler avec des outils/flux de déploiement réels couramment utilisés
* Intégrer dans au moins Visual Studio

Le résultat est probablement de nombreuses améliorations de EF Core (par exemple, de meilleures migrations sur SQLite), ainsi que des conseils et des collaborations à long terme avec d’autres équipes pour améliorer les expériences de bout en bout qui vont au-delà d’EF.

## <a name="ef-core-platforms-experience"></a>Expérience des plates-formes EF Core

Développeurs en chef : @roji et @bricelam

Suivi par [#19588](https://github.com/dotnet/efcore/issues/19588)

Taille de T-shirt : L

État : étendue/terminé

Portée : des conseils et des exemples de plateforme sont publiés pour éblouissant, Xamarin, WinForms et WPF. Xamarin et d’autres travaux de l’AOA/éditeur de liens sont maintenant prévus pour EF Core 6,0.

Nous avons de bonnes recommandations pour l’utilisation de EF Core dans des applications Web classiques basées sur MVC. Des conseils pour d’autres plateformes et modèles d’application sont manquants ou obsolètes. Pour EF Core 5,0, nous prévoyons d’examiner, d’améliorer et de documenter l’expérience d’utilisation de EF Core avec :

* Blazor
* Xamarin, y compris l’utilisation de l’AOA/histoire de l’éditeur de liens
* WinForms/WPF/WinUI et éventuellement d’autres U.I. frameworks

Il s’agit probablement de nombreuses améliorations de EF Core, ainsi que de conseils et de collaborations à long terme avec d’autres équipes pour améliorer les expériences de bout en bout qui vont au-delà d’EF.

Voici quelques-uns des éléments que nous prévoyons de consulter :

* Déploiement, y compris l’expérience d’utilisation des outils EF comme pour les migrations
* Modèles d’application, notamment Xamarin et éblouissant, et probablement d’autres
* Expériences SQLite, y compris l’expérience spatiale et les reconstructions de tables
* AOA et liaison d’expériences
* Intégration des diagnostics, y compris les compteurs de performances

## <a name="performance"></a>Performances

Développeur principal : @roji

Suivi par des [problèmes étiquetés avec `area-perf` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)

Taille de T-shirt : L

État : étendues/terminées

Portée : les améliorations de performances majeures dans le fournisseur npgsql sont terminées. D’autres tâches de performances sont désormais prévues pour EF Core 6,0.

Par EF Core, nous prévoyons d’améliorer notre suite de tests de performances et d’améliorer les performances de l’exécution. En outre, nous prévoyons de terminer la nouvelle API de traitement par lot ADO.NET qui a été prototypée durant le cycle de publication de 3,0. En outre, au niveau de la couche ADO.NET, nous prévoyons des améliorations de performances supplémentaires pour le fournisseur npgsql.

Dans le cadre de ce travail, nous prévoyons également d’ajouter des compteurs de performances ADO.NET/EF Core et d’autres diagnostics appropriés.

## <a name="architecturalcontributor-documentation"></a>Documentation architecturale/contributeur

Documentation en chef : @ajcvickers

Suivi par [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)

Taille de T-shirt : L

État : couper

L’idée ici est de faciliter la compréhension de ce qui se passe dans les éléments internes de EF Core. Cela peut être utile pour toute personne utilisant EF Core, mais la motivation principale est de faciliter la tâche pour les utilisateurs externes :

* Contribuer au code EF Core
* Créer des fournisseurs de base de données
* Créer d’autres extensions

Mise à jour : Malheureusement, ce plan était trop ambitieux.
Nous pensons toujours qu’il s’agit d’un élément important, mais ce n’est malheureusement pas le EF Core 5,0.

## <a name="microsoftdatasqlite-documentation"></a>Documentation de Microsoft. Data. sqlite

Documentation en chef : @bricelam

Suivi par [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)

Taille de T-shirt : M

État : terminé. La nouvelle documentation est [en ligne sur le site Microsoft docs](/dotnet/standard/data/sqlite/?tabs=netcore-cli).

L’équipe EF possède également le fournisseur ADO.NET de Microsoft. Data. sqlite. Nous prévoyons de documenter entièrement ce fournisseur dans le cadre de la version 5,0.

## <a name="general-documentation"></a>Documentation générale

Documentation en chef : @ajcvickers

Suivi par des [problèmes dans la documentation référentiel dans le jalon 5,0](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)

Taille de T-shirt : L

État : en cours

Nous sommes déjà en train de mettre à jour la documentation pour les versions 3,0 et 3,1. Nous travaillons également sur :

* Une révision des documents de prise en main pour les rendre plus simples et plus faciles à suivre
* Réorganisation de docs pour faciliter la recherche et ajouter des références croisées
* Ajout de détails et de clarifications à des documents existants
* Mise à jour des exemples et ajout d’autres exemples

## <a name="fixing-bugs"></a>Correction des bogues

Suivi par des [problèmes étiquetés avec `type-bug` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)

Développeurs : @roji , @maumar ,,, @bricelam @smitpatel @AndriySvyryd , @ajcvickers

Taille de T-shirt : L

État : en cours

Au moment de la rédaction de ce document, nous avons 135 bogues en triage pour qu’ils soient corrigés dans la version 5,0 (avec 62 déjà corrigé), mais il y a un chevauchement significatif avec la section sur les _améliorations générales des requêtes_ ci-dessus.

Le taux entrant (les problèmes qui se terminent par un travail dans une étape majeure) était d’environ 23 problèmes par mois au cours de la version 3,0. Ces derniers ne doivent pas nécessairement être corrigés dans 5,0. En guise d’estimation approximative, nous prévoyons de résoudre les problèmes 150 supplémentaires dans le délai de 5,0.

## <a name="small-enhancements"></a>Petites améliorations

Suivi par des [problèmes étiquetés avec `type-enhancement` dans le jalon 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)

Développeurs : @roji , @maumar ,,, @bricelam @smitpatel @AndriySvyryd , @ajcvickers

Taille de T-shirt : L

État : terminé

En plus des fonctionnalités plus grandes décrites ci-dessus, nous avons également de nombreuses améliorations plus petites, planifiées pour 5,0, afin de corriger les « coupes papier ». Notez que la plupart de ces améliorations sont également couvertes par les thèmes plus généraux décrits ci-dessus.

## <a name="below-the-line"></a>En dessous de la ligne

Suivi par des [problèmes étiquetés `consider-for-next-release` avec](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)

Ce sont des correctifs de bogues et des améliorations qui **ne sont pas** actuellement planifiés pour la version 5,0, mais nous examinerons les objectifs d’étirement en fonction de la progression de la tâche ci-dessus.

En outre, nous considérons toujours les [problèmes les plus votés](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) lors de la planification. La réduction de l’un de ces problèmes à partir d’une version est toujours pénible, mais nous avons besoin d’un plan réaliste pour les ressources dont nous disposons.

## <a name="suggestions"></a>Suggestions

Vos commentaires sur la planification sont importants. La meilleure façon d’indiquer l’importance d’un problème est de voter (pouce vers le haut) pour ce problème sur GitHub. Ces données sont ensuite chargées dans le [processus de planification](xref:core/what-is-new/release-planning) de la prochaine version.
