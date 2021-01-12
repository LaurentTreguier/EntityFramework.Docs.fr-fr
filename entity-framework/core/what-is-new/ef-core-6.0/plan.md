---
title: Planifier Entity Framework Core 6,0
description: Thèmes et fonctionnalités prévus pour EF Core 6,0
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: 612461bc6ad30778baa5c6d10dda5cabac91dcb2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129783"
---
# <a name="plan-for-entity-framework-core-60"></a>Planifier Entity Framework Core 6,0

Comme décrit dans le [processus de planification](xref:core/what-is-new/release-planning), nous avons rassemblé Entity Framework Core l’entrée des parties prenantes dans un plan pour la version 6,0 (EF Core).

Contrairement aux versions précédentes, ce plan ne tente pas de couvrir tout le travail pour la version 6,0. Au lieu de cela, il indique où et comment nous avons l’intention d’investir dans cette version, mais avec une certaine flexibilité pour ajuster l’étendue ou insérer un nouveau travail au fur et à mesure que nous recueillons des commentaires et que vous apprenez à travailler sur la version.

> [!IMPORTANT]
> Ce plan n’est pas un engagement. C’est un point de départ qui évoluera à mesure que nous en apprendrons davantage. Certains éléments qui ne sont pas actuellement planifiés pour 6,0 peuvent être extraits. Certains éléments actuellement planifiés pour 6,0 peuvent devenir punted.

## <a name="general-information"></a>Informations générales

### <a name="version-number-and-release-date"></a>Numéro de version et date de publication

EF Core 6,0 est la prochaine version après EF Core 5,0 et est actuellement planifiée en version finale en novembre 2021 en même temps que .NET 6.

### <a name="supported-platforms"></a>Plateformes prises en charge

EF Core 6,0 cible actuellement .NET 5. Cela sera probablement mis à jour vers .NET 6 comme nous l’avons fait près de la version. EF Core 6,0 ne cible aucune version de .NET Standard ; Pour plus d’informations, consultez [l’avenir de .NET standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).

EF Core 6,0 ne s’exécutera pas sur .NET Framework.

EF Core 6,0 s’alignera sur .NET 6 comme [version de support à long terme (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="breaking-changes"></a>Changements cassants

EF Core 6,0 contiendra un petit nombre de [modifications avec rupture](xref:core/what-is-new/ef-core-6.0/breaking-changes) à mesure que nous continuons à faire évoluer à la fois EF Core et la plateforme .net. Notre objectif est de permettre la mise à jour sans interruption de la grande majorité des applications.

## <a name="themes"></a>Thèmes

Les zones suivantes constituent la base des investissements importants en EF Core 6,0.

## <a name="highly-requested-features"></a>Fonctionnalités hautement demandées

Comme toujours, une entrée majeure dans le [processus de planification](xref:core/what-is-new/release-planning) provient du [vote ( 👍 ) des fonctionnalités sur GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc). Pour EF Core 6,0, nous prévoyons de travailler sur les fonctionnalités hautement demandées suivantes :

### <a name="sql-server-temporal-tables"></a>SQL Server les tables temporelles

Suivi par [#4693](https://github.com/dotnet/efcore/issues/4693)

État : non démarré

Taille de T-shirt : grande

Les tables temporelles prennent en charge les requêtes pour les données stockées dans la table à _tout_ moment, par opposition aux données les plus récentes stockées comme c’est le cas pour les tables normales. EF Core 6,0 autorise la création de tables temporelles via des migrations, ainsi que l’autorisation de l’accès aux données via des requêtes LINQ.

Ce travail est initialement étendu comme [décrit dans le problème](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974). Nous pouvons intégrer un support supplémentaire en fonction des commentaires au cours de la version.

### <a name="json-columns"></a>Colonnes JSON

Suivi par [#4021](https://github.com/dotnet/efcore/issues/4021)

État : non démarré

Taille de T-shirt : moyenne

Cette fonctionnalité introduira un mécanisme commun et des modèles pour la prise en charge de JSON qui peuvent être implémentés par n’importe quel fournisseur de base de données. Nous travaillons avec la communauté pour aligner les implémentations existantes pour [npgsql](https://github.com/npgsql/efcore.pg) et [pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), ainsi que pour ajouter la prise en charge de SQL Server et SQLite.

### <a name="columnattributeorder"></a>ColumnAttribute. Order

Suivi par [#10059](https://github.com/dotnet/efcore/issues/10059)

État : non démarré

Taille de T-shirt : petite

Cette fonctionnalité permet un classement arbitraire des colonnes lors de **la création d’une table** avec des migrations ou `EnsureCreated` . Notez que la modification de l’ordre des colonnes dans les tables existantes nécessite la reconstruction de la table, et ce n’est pas un point que nous prévoyons de prendre en charge dans une version EF Core.

## <a name="performance"></a>Performances

Bien que EF Core soit généralement plus rapide que EF6, il existe toujours des zones où des améliorations significatives des performances sont possibles. Nous prévoyons de traiter plusieurs de ces domaines dans EF Core 6,0, tout en améliorant également notre infrastructure de performances et vos tests.

Ce thème implique un grand nombre d’investigations itératives, qui indiquent où nous mettons en avant les ressources. Nous prévoyons de commencer par :

### <a name="performance-infrastructure-and-new-tests"></a>Infrastructure de performances et nouveaux tests

État : non démarré

Taille de T-shirt : moyenne

Le code base EF Core contient déjà un ensemble de tests de performances qui sont exécutés tous les jours. Pour 6,0, nous prévoyons d’améliorer l’infrastructure pour ces tests et d’ajouter de nouveaux tests. Nous allons également profiler les scénarios de performances de la principale et corriger tout fruit faiblement trouvé.

### <a name="compiled-models"></a>Modèles compilés

Suivi par [#1906](https://github.com/dotnet/efcore/issues/1906)

État : non démarré

Taille de T-shirt : X-large

Les modèles compilés autorisent la génération d’une forme compilée du modèle EF. Cela permet d’améliorer les performances de démarrage, ainsi que de meilleures performances lors de l’accès au modèle.

### <a name="techempower-fortunes"></a>TechEmpower fortune

Suivi par [#23611](https://github.com/dotnet/efcore/issues/23611)

État : non démarré

Taille de T-shirt : X-large

Nous avons exécuté les [bancs d’essai TechEmpower](https://www.techempower.com/benchmarks/) standard sur .net sur une base de données PostgreSQL depuis plusieurs années. Le [test d’évaluation de fortune](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) est particulièrement pertinent pour les scénarios EF. Nous avons plusieurs variantes de ce test, notamment :

- Implémentation de qui utilise directement ADO.NET. Il s’agit de l’implémentation la plus rapide des trois répertoriés ici.
- Implémentation de qui utilise [dapper](https://www.nuget.org/packages/Dapper/). Cela est plus lent que l’utilisation directe de ADO.NET, mais toujours rapide.
- Implémentation de qui utilise EF Core. Il s’agit actuellement de l’implémentation la plus lente des trois.

L’objectif de EF Core 6,0 est de faire en sorte que les performances de la EF Core correspondent à celles de dapper sur le banc d’essai TechEmpower fortune. (Il s’agit d’un défi important, mais nous allons faire de notre mieux pour être aussi proche que possible.)

### <a name="linkeraot"></a>Éditeur de liens/AOA

Suivi par [#10963](https://github.com/dotnet/efcore/issues/10963)

État : non démarré

Taille de T-shirt : moyenne

EF Core effectue une grande quantité de génération de code d’exécution. Cela est difficile pour les modèles d’application qui dépendent du tremblement de l’arborescence de l’éditeur de liens, tels que Xamarin et éblouissant, et les plateformes qui n’autorisent pas la compilation dynamique, comme iOS. Nous continuons à examiner cet espace dans le cadre de EF Core 6,0 et à apporter des améliorations ciblées. Toutefois, nous ne pensons pas que l’intervalle est entièrement proche dans le laps de temps 6,0.

## <a name="migrations-and-deployment"></a>Migrations et déploiement

Après les [investigations effectuées pour EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), nous prévoyons d’introduire une prise en charge améliorée pour la gestion des migrations et le déploiement de bases de données. Cela comprend deux domaines majeurs :

### <a name="migrations-bundles"></a>Offres groupées de migrations

Suivi par [#19693](https://github.com/dotnet/efcore/issues/19693)

État : non démarré

Taille de T-shirt : moyenne

Une offre groupée de migrations est un exécutable autonome qui applique des migrations à une base de données de production. Le comportement est le même `dotnet ef database update` , mais il est préférable de faire en sorte que le déploiement du SSH/de l’ancrage/PowerShell soit beaucoup plus facile, car tout ce qui est nécessaire est contenu dans le seul exécutable.

### <a name="managing-migrations"></a>Gestion des migrations

Suivi par [#22945](https://github.com/dotnet/efcore/issues/22945)

État : non démarré

Taille de T-shirt : grande

Le nombre de migrations créées pour une application peut croître pour devenir un fardeau. En outre, ces migrations sont souvent déployées avec l’application, même si cela n’est pas nécessaire. Dans EF Core 6,0, nous prévoyons d’améliorer cela grâce à de meilleurs outils et à la gestion de projet/assembly. Deux problèmes spécifiques que nous envisageons de traiter sont [l’écrasement de nombreuses migrations](https://github.com/dotnet/efcore/issues/2174) et [la régénération d’un instantané de modèle propre](https://github.com/dotnet/efcore/issues/18557).

## <a name="improve-existing-features-and-fix-bugs"></a>Améliorer les fonctionnalités existantes et corriger les bogues

Tout [problème ou bogue affecté à l’étape de jalon 6.0.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) est actuellement planifié pour cette version. Cela comprend de nombreuses petites améliorations et des correctifs de bogues.

### <a name="ef6-query-parity"></a>Parité de requête EF6

Suivi par des [problèmes étiquetés avec « EF6-Parity » et dans le jalon 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)

État : non démarré

Taille de T-shirt : grande

EF Core 5,0 prend en charge la plupart des modèles de requête pris en charge par EF6, en plus des modèles non pris en charge dans EF6. Pour EF Core 6,0, nous prévoyons de fermer l’intervalle et de prendre en charge EF Core interroge un sur-ensemble réel des requêtes EF6 prises en charge. Cette opération est motivée par l’examen des lacunes, mais elle comprend déjà des problèmes de type GroupBy, tels que [translate GroupBy suivi de FirstOrDefault](https://github.com/dotnet/efcore/issues/12088)et des requêtes SQL brutes pour les types [primitifs](https://github.com/dotnet/efcore/issues/11624) et non [mappés](https://github.com/dotnet/efcore/issues/10753) .  

### <a name="value-objects"></a>Objets de valeur

Suivi par [#9906](https://github.com/dotnet/efcore/issues/9906)

État : non démarré

Taille de T-shirt : moyenne

C’était auparavant la vue d’équipe qui possédait des entités, destinées à la [prise en charge](https://www.martinfowler.com/bliki/DDD_Aggregate.html)de l’agrégation, serait également une approximation raisonnable des [objets de valeur](https://www.martinfowler.com/bliki/ValueObject.html). L’expérience a montré que cela ne serait pas le cas. Par conséquent, dans EF Core 6,0, nous prévoyons d’introduire une meilleure expérience axée sur les besoins des objets de valeur dans la conception pilotée par le domaine. Cette approche est basée sur les convertisseurs de valeur plutôt que sur les entités détenues.

Ce travail est initialement étendu pour autoriser [les convertisseurs de valeurs qui sont mappés à plusieurs colonnes](https://github.com/dotnet/efcore/issues/13947). Nous pouvons intégrer un support supplémentaire en fonction des commentaires au cours de la version.

### <a name="cosmos-database-provider"></a>Fournisseur de base de données Cosmos

Suivi par des [problèmes étiquetés avec « Area-Cosmos » et dans le jalon 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)

État : non démarré

Taille de T-shirt : grande

Nous recueillons activement des commentaires sur les améliorations apportées au fournisseur Cosmos dans EF Core 6,0. Nous mettrons à jour ce document au fur et à mesure que nous en apprendrons davantage. Pour l’instant, assurez-vous de voter ( 👍 ) pour les fonctionnalités Cosmos dont vous avez besoin.

### <a name="expose-model-building-conventions-to-applications"></a>Exposer les conventions de génération de modèle aux applications

Suivi par [#214](https://github.com/dotnet/efcore/issues/214)

État : non démarré

Taille de T-shirt : moyenne

EF Core utilise un ensemble de conventions pour créer un modèle à partir de types .NET. Ces conventions sont actuellement contrôlées par le fournisseur de base de données. Dans EF Core 6,0, nous avons l’intention d’autoriser les applications à se raccorder à et à modifier ces conventions.

### <a name="zero-bug-balance-zbb"></a>Aucun déséquilibre de bogue (ZBB)

Suivi par des [problèmes étiquetés avec `type-bug` dans le jalon 6,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)

État : en cours

Taille de T-shirt : grande

Nous prévoyons de corriger tous les bogues en attente pendant la période de EF Core 6,0. Voici quelques points à prendre en compte :

- Cela s’applique spécifiquement aux problèmes étiquetés [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).
- Il y aura des exceptions, par exemple lorsque le bogue nécessite une modification de conception ou une nouvelle fonctionnalité pour corriger correctement. Ces problèmes sont signalés par l' `blocked` étiquette.
- Nous chargeur des bogues en fonction du risque lorsque cela est normal, car nous sommes proches d’une version GA/RTM.

### <a name="miscellaneous-features"></a>Fonctionnalités diverses

Suivi par des [problèmes étiquetés avec `type-enhancement` dans le jalon 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)

État : en cours

Taille de T-shirt : grande

Les fonctionnalités diverses prévues pour EF 6,0 incluent, mais ne sont pas limitées à :

- [Fractionner la requête pour les collections non de navigation](https://github.com/dotnet/efcore/issues/21234)
- [Détecter les tables de jointures simples dans l’ingénierie à rebours et créer des relations plusieurs-à-plusieurs](https://github.com/dotnet/efcore/issues/22475)
- [Effectuer une recherche en texte intégral/libre sur SQLite et SQL Server](https://github.com/dotnet/efcore/issues/4823)
- [SQL Server les index spatiaux](https://github.com/dotnet/efcore/issues/12538)
- [Mécanisme/API permettant de spécifier une conversion par défaut pour n’importe quelle propriété d’un type donné dans le modèle](https://github.com/dotnet/efcore/issues/10784)
- [Utiliser la nouvelle API de traitement par lot de ADO.NET](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a>.NET (intégration)

L’équipe EF Core travaille également sur plusieurs technologies connexes, mais indépendantes. En particulier, nous prévoyons de travailler sur :

### <a name="enhancements-to-systemdata"></a>Améliorations apportées à System. Data

Suivi par des [problèmes dans le référentiel dotnet\runtime étiqueté avec `area-System.Data` dans le jalon 6,0](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)

État : non démarré

Taille de T-shirt : grande

Ce travail comprend les éléments suivants :

- Implémentation de la nouvelle [API de traitement par lot](https://github.com/dotnet/runtime/issues/28633).
- Continuez à travailler avec d’autres équipes .NET et la communauté pour comprendre et faire évoluer ADO.NET.
- [Normaliser sur DiagnosticSource pour le suivi des composants System. Data. *](https://github.com/dotnet/runtime/issues/22336).

### <a name="enhancements-to-microsoftdatasqlite"></a>Améliorations apportées à Microsoft. Data. sqlite

Suivi par des [problèmes étiquetés avec `type-enhancement` et `area-adonet-sqlite` dans le jalon 6,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)

État : en cours

Taille de T-shirt : moyenne

Plusieurs petites améliorations sont planifiées pour Microsoft. Data. sqlite, notamment le [regroupement de connexions](https://github.com/dotnet/efcore/issues/13837) et les [instructions préparées](https://github.com/dotnet/efcore/issues/14044) pour les performances.

### <a name="nullable-reference-types"></a>Types références Nullables

Suivi par [#14150](https://github.com/dotnet/efcore/issues/14150)

État : en cours

Taille de T-shirt : grande

Nous allons annoter le code EF Core pour utiliser des [types de référence Nullable](/dotnet/csharp/nullable-references).

## <a name="experiments-and-investigations"></a>Expériences et investigations

L’équipe EF envisage de consacrer du temps au cours de la période de EF Core 6,0 de l’expérimentation et de l’examen dans deux domaines. Il s’agit d’un processus d’apprentissage, car aucun livrable concret n’est prévu pour la version 6,0.

### <a name="sqlservercore"></a>SqlServer. Core

Suivi dans le [laboratoire de données .net référentiel](https://github.com/dotnet/datalab/)

État : non démarré

Taille de T-shirt : en cours

[Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) est un fournisseur de base de données ADO.net complet pour SQL Server. Il prend en charge un large éventail de fonctionnalités de SQL Server à la fois sur .NET Core et .NET Framework. Toutefois, il s’agit également d’un code base volumineux et ancien avec de nombreuses interactions complexes entre ses comportements. Cela complique l’étude des gains potentiels qui peuvent être réalisés à l’aide des fonctionnalités .NET Core plus récentes. Par conséquent, nous commençons une expérience en collaboration avec la communauté pour déterminer le potentiel d’un pilote de SQL Server très performant pour .NET.

> [!IMPORTANT]
> L’investissement dans Microsoft. Data. SqlClient ne change pas. Elle sera toujours la méthode recommandée pour se connecter à SQL Server et SQL Azure, avec et sans EF Core. Il continuera à prendre en charge les nouvelles fonctionnalités de SQL Server à mesure qu’elles seront introduites.

### <a name="graphql"></a>GraphQL

État : non démarré

Taille de T-shirt : en cours

[GraphQL](https://graphql.org/) a gagné en traction au cours des dernières années sur une variété de plateformes. Nous prévoyons d’examiner l’espace et de trouver des moyens d’améliorer l’expérience avec .NET. Cela implique l’utilisation de la communauté pour comprendre et prendre en charge l’écosystème existant. Il peut également impliquer un investissement spécifique de Microsoft, sous la forme de contributions à un travail existant ou en développement de pièces complémentaires dans la pile Microsoft.

### <a name="dataverse-formerly-common-data-services"></a>DataVerse (anciennement Data Services courantes)

État : non démarré

Taille de T-shirt : en cours

[DataVerse](/powerapps/maker/data-platform/data-platform-intro) est un magasin de données basé sur des colonnes conçu pour le développement rapide d’applications métier. Il gère automatiquement des types de données complexes tels que des objets binaires (BLOB) et des entités et des relations intégrées, telles que les organisations et les contacts. Un kit de développement logiciel (SDK) existe, mais les développeurs peuvent tirer parti de la présence d’un fournisseur EF Core pour utiliser des requêtes LINQ avancées, tirer parti de l’unité de travail et disposer d’une API d’accès aux données cohérente. L’équipe prendra en compte différentes façons d’améliorer l’expérience du développeur .NET en se connectant à DataVerse.

## <a name="below-the-cut-line"></a>En dessous du trait de coupe

Suivi par des [problèmes étiquetés `consider-for-current-release` avec](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)

Ce sont des correctifs de bogues et des améliorations qui **ne sont pas** actuellement planifiés pour la version 6,0, mais nous examinerons en fonction des commentaires dans la version, ainsi que de la progression de la tâche ci-dessus. Ces problèmes peuvent être tirés dans EF Core 6,0 et devenir automatiquement des candidats pour la prochaine version.

En outre, nous considérons toujours les [problèmes les plus votés](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) lors de la planification. La réduction de l’un de ces problèmes à partir d’une version est toujours pénible, mais nous avons besoin d’un plan réaliste pour les ressources dont nous disposons. Vérifiez que vous avez voté ( 👍 ) pour les fonctionnalités dont vous avez besoin.

## <a name="suggestions"></a>Suggestions

Vos commentaires sur la planification sont importants. La meilleure façon d’indiquer l’importance d’un problème est de voter ( 👍 ) pour ce problème sur GitHub. Ces données sont ensuite chargées dans le [processus de planification](xref:core/what-is-new/release-planning) de la prochaine version.
