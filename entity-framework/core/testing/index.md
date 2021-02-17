---
title: Test de code qui utilise EF Core - EF Core
description: Différentes approches possibles pour tester les applications qui utilisent Entity Framework Core.
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/index
ms.openlocfilehash: 5ac7954a87697e43b68845e925e2510c027b7135
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543573"
---
# <a name="testing-code-that-uses-ef-core"></a>Test de code qui utilise EF Core

Pour tester le code qui accède à une base de données, vous devez adopter l’une des approches suivantes :

* Exécuter des requêtes et des mises à jour sur le même système de base de données que celui utilisé en production.
* Exécuter des requêtes et des mises à jour sur un autre système de base de données plus facile à gérer.
* Utiliser des doubles de test ou un autre mécanisme pour éviter d’utiliser une base de données.

Ce document décrit les compromis liés à chacun de ces choix et montre comment utiliser EF Core avec chaque approche.

> [!TIP]
> Pour obtenir du code illustrant les concepts introduits ici, consultez l’[exemple de test EF Core](xref:core/testing/testing-sample).

## <a name="all-database-providers-are-not-equal"></a>Tous les fournisseurs de base de données ne sont pas égaux

Il est très important de comprendre qu’EF Core n’est pas conçu pour effectuer l’abstraction de tous les aspects du système de base de données sous-jacent.
Au lieu de cela, EF Core est un ensemble commun de modèles et de concepts qui peuvent être utilisés avec n’importe quel système de base de données.
Les fournisseurs de base de données EF Core superposent ensuite le comportement et les fonctionnalités propres à la base de données sur ce framework commun.
Cela permet à chaque système de base de données de faire ce qu’il fait le mieux, tout en maintenant une compatibilité, le cas échéant, avec d’autres systèmes de base de données.

Fondamentalement, cela signifie que le fait de changer de fournisseur de base de données changera le comportement d’EF Core, et que l’application ne fonctionnera pas correctement à moins de tenir compte explicitement de toute différence de comportement.
Cela dit, dans de nombreux cas cela fonctionnera car il existe un degré élevé de normalisation entre les bases de données relationnelles.
C’est à la fois bien et embêtant.
Bien, car le passage d’un système de base de données à un autre peut être relativement simple.
Embêtant, car cela risque de procurer un faux sentiment de sécurité si l’application n’est pas entièrement testée sur le nouveau système de base de données.

## <a name="approach-1-production-database-system"></a>Approche 1 : système de base de données de production

Comme décrit dans la section précédente, le seul moyen d’être sûr de tester ce qui s’exécute en production consiste à utiliser le même système de base de données.
Par exemple, si l’application déployée utilise SQL Azure, les tests doivent également être exécutés par rapport à SQL Azure.

Toutefois, demander à chaque développeur d’exécuter des tests par rapport à SQL Azure tout en travaillant activement sur le code serait lent et coûteux.
Cela illustre le principal compromis lié à ces approches : quand convient-il de dévier du système de base de données de production afin d’améliorer l’efficacité des tests ?

Heureusement, dans ce cas la réponse est très facile : utilisez des serveurs SQL locaux pour les tests des développeurs.
SQL Azure et SQL Server sont extrêmement similaires ; exécuter des tests par rapport à SQL Server constitue donc généralement un compromis raisonnable.
Cela dit, il est toujours prudent d’exécuter des tests par rapport à SQL Azure lui-même avant de passer en production.

### <a name="localdb"></a>LocalDB

Tous les principaux systèmes de base de données ont une forme de « Developer Edition » pour les tests locaux.
SQL Server offre une fonctionnalité nommée [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb).
Le principal avantage de LocalDB est qu’elle fait tourner l’instance de base de données à la demande.
Cela évite d’avoir un service de base de données en cours d’exécution sur votre ordinateur même quand vous n’exécutez pas de tests.

LocalDB n’est pas sans problème :

* Elle ne prend pas en charge tout ce que [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15&preserve-view=true) prend en charge
* Elle n’est pas disponible sur Linux
* Elle peut entraîner un décalage lors de la première série de tests quand le service est lancé

Personnellement, je n’ai jamais pensé que c’était un problème d’avoir un service de base de données en cours d’exécution sur mon ordinateur de développement, et je recommande généralement d’utiliser Developer Edition à la place.
Toutefois, LocalDB peut convenir à certaines personnes, en particulier sur les ordinateurs de développement moins puissants.

[L’exécution de SQL Server](/sql/linux/quickstart-install-connect-docker) (ou de tout autre système de base de données) dans un conteneur Docker (ou similaire) est une autre façon d’éviter d’exécuter le système de base de données directement sur votre ordinateur de développement.

## <a name="approach-2-sqlite"></a>Approche 2 : SQLite

EF Core teste le fournisseur SQL Server principalement en l’exécutant sur une instance de SQL Server locale.
Ces tests exécutent des dizaines de milliers de requêtes en quelques minutes sur une machine rapide.
Cela montre que l’utilisation du véritable système de base de données peut être une solution performante.
C’est une idée fausse de penser qu’utiliser une base de données plus légère est la seule façon d’exécuter des tests rapidement.

Mais que se passe-t-il si, pour une raison quelconque, vous ne pouvez pas exécuter des tests à proximité de votre système de base de données de production ?
Dans ce cas, le meilleur choix consiste à utiliser quelque chose ayant des fonctionnalités similaires,
généralement une autre base de données relationnelle, [SQLite](https://sqlite.org/index.html) étant ici le choix évident.

SQLite est un bon choix pour les raisons suivantes :

* Il s’exécute In-process avec votre application, et a donc une faible surcharge.
* Il utilise des fichiers simples et créés automatiquement pour les bases de données, et ne nécessite donc pas de gestion de base de données.
* Il a un mode « en mémoire » qui évite même la création de fichiers.

Toutefois, n’oubliez pas que :

* SQLite ne prend inévitablement pas en charge ce que fait votre système de base de données de production.
* SQLite se comportera différemment de votre système de base de données de production pour certaines requêtes.

Par conséquent, si vous utilisez SQLite pour certains tests, veillez également à tester votre système de base de données réel.

Pour obtenir des instructions propres à EF Core, consultez [Test avec SQLite](xref:core/testing/sqlite).

## <a name="approach-3-the-ef-core-in-memory-database"></a>Approche 3 : la base de données en mémoire EF Core

EF Core est fourni avec une base de données en mémoire que nous utilisons pour les tests internes d’EF Core lui-même.
Cette base de données n’est en général **pas appropriée pour les tests des applications qui utilisent EF Core**. Plus précisément :

* Il ne s’agit pas d’une base de données relationnelle.
* Elle ne prend pas en charge les transactions.
* Elle ne peut pas exécuter de requêtes SQL brutes.
* Elle n’est pas optimisée pour les performances.

Aucun de ces éléments n’est très important lors du test des composants internes d’EF Core, car nous les utilisons spécifiquement là où la base de données n’a pas d’importance pour le test.
En revanche, ces éléments ont tendance à être très importants lors du test d’une application qui utilise EF Core.

## <a name="unit-testing"></a>Test unitaire

Pensez à tester une partie de la logique métier qui peut nécessiter l’utilisation de certaines données d’une base de données, mais qui ne teste pas fondamentalement les interactions de la base de données.
L’une des options consiste à utiliser un [double de test](https://en.wikipedia.org/wiki/Test_double), comme un test fictif.

Nous utilisons des doubles de test pour les tests internes d’EF Core.
Toutefois, nous n’essayons jamais de simuler DbContext ou IQueryable.
Ceci est difficile, fastidieux et fragile.
**Ne le faites pas.**

Au lieu de cela, nous utilisons la base de données en mémoire EF quand nous exécutons des tests unitaires sur quelque chose qui utilise DbContext.
Dans ce cas, l’utilisation de la base de données en mémoire EF est appropriée, car le test ne dépend pas du comportement de la base de données.
Mais ne procédez pas ainsi pour tester des mises à jour ou des requêtes de base de données réelles.

L’[exemple de test EF Core](xref:core/testing/testing-sample) illustre des tests avec la base de données en mémoire EF, ainsi que SQL Server et SQLite.
