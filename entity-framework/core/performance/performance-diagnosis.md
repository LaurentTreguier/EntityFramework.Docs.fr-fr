---
title: Diagnostic des performances-EF Core
description: Diagnostic des performances de Entity Framework Core et identification des goulots d’étranglement
author: roji
ms.date: 12/1/2020
uid: core/performance/performance-diagnosis
ms.openlocfilehash: 85ffd1826723ad97bdcce517781f920c193e4286
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023846"
---
# <a name="performance-diagnosis"></a>Diagnostic des performances

Cette section décrit les méthodes permettant de détecter les problèmes de performances dans votre application EF, et une fois qu’une zone problématique a été identifiée, comment les analyser plus en détail pour identifier le problème racine. Il est important de diagnostiquer et d’examiner attentivement les problèmes avant de passer à des conclusions, et de ne pas supposer que la racine du problème se trouve à l’origine du problème.

## <a name="identifying-slow-database-commands-via-logging"></a>Identification des commandes de base de données lentes via la journalisation

À la fin de la journée, EF prépare et exécute des commandes à exécuter sur votre base de données ; avec une base de données relationnelle, cela signifie exécuter des instructions SQL via l’API de base de données ADO.NET. Si une requête prend trop de temps (par exemple, en raison de l’absence d’un index), elle peut être détectée en inspectant les journaux d’exécution des commandes et en observant le temps réellement nécessaire.

Avec EF, il est très facile de capturer les durées d’exécution des commandes via la [journalisation simple](xref:core/logging-events-diagnostics/simple-logging) ou [Microsoft. extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging):

### <a name="simple-logging"></a>[Journalisation simple](#tab/simple-logging)

[!code-csharp[Main](../../../samples/core/Performance/BloggingContext.cs#SimpleLogging)]

### <a name="microsoftextensionslogging"></a>[Microsoft.Extensions.Logging](#tab/microsoft-extensions-logging)

[!code-csharp[Main](../../../samples/core/Performance/ExtensionsLoggingContext.cs#ExtensionsLogging)]

***

Lorsque le niveau de journalisation est défini sur `LogLevel.Information` , EF émet un message de journal pour chaque exécution de commande avec le temps écoulé :

```log
info: 06/12/2020 09:12:36.117 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (4ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[Id], [b].[Name]
      FROM [Blogs] AS [b]
      WHERE [b].[Name] = N'foo'
```

La commande ci-dessus a pris 4 millisecondes. Si une certaine commande prend plus que prévu, vous avez trouvé une cause possible d’un problème de performances et vous pouvez maintenant vous concentrer sur la raison pour laquelle elle s’exécute lentement. La journalisation des commandes peut également révéler des cas où des allers-retours inattendus sont effectués. Cela s’affiche sous la forme de plusieurs commandes dans lesquelles une seule est attendue.

> [!WARNING]
> Le fait de laisser la journalisation de l’exécution des commandes activée dans votre environnement de production est généralement une mauvaise idée. La journalisation elle-même ralentit votre application et peut rapidement créer des fichiers journaux volumineux qui peuvent remplir le disque de votre serveur. Il est recommandé de ne garder la connexion que pendant un bref intervalle de temps pour collecter des données, tout en surveillant avec soin votre application, ou en capturant les données de journalisation sur un système de préproduction.

## <a name="correlating-database-commands-to-linq-queries"></a>Corrélation des commandes de base de données avec les requêtes LINQ

L’un des problèmes liés à la journalisation de l’exécution des commandes est qu’il est parfois difficile de mettre en corrélation les requêtes SQL et les requêtes LINQ : les commandes SQL exécutées par EF peuvent paraître très différentes des requêtes LINQ à partir desquelles elles ont été générées. Pour vous aider dans cette difficulté, vous pouvez utiliser la fonctionnalité de [balises de requête](xref:core/querying/tags) d’EF, qui vous permet d’injecter un petit commentaire d’identification dans la requête SQL :

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

La balise apparaît dans les journaux :

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

Il est souvent utile de baliser les requêtes principales d’une application de cette façon, afin de rendre les journaux d’exécution des commandes plus lisibles.

## <a name="other-interfaces-for-capturing-performance-data"></a>Autres interfaces pour la capture des données de performances

Il existe diverses alternatives à la fonctionnalité de journalisation d’EF pour capturer les durées d’exécution des commandes, ce qui peut être plus puissant. Les bases de données sont généralement associées à leurs propres outils de suivi et d’analyse des performances, qui fournissent généralement des informations plus riches et spécifiques à la base de données, au-delà des temps d’exécution simples. l’installation, les fonctionnalités et l’utilisation réelles varient considérablement d’une base de données à l’autre.

Par exemple, [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) est un client puissant qui peut se connecter à votre instance SQL Server et fournir des informations de gestion et de performances précieuses. L’objectif de cette section n’est pas d’aborder les détails, mais deux fonctionnalités méritent d’être citées : le [moniteur d’activité](/sql/relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio), qui fournit un tableau de bord dynamique de l’activité du serveur (y compris les requêtes les plus coûteuses) et la fonctionnalité [événements étendus (XEvent)](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) , qui permet de définir des sessions de capture de données arbitraires qui peuvent être adaptées à vos besoins. [La documentation SQL Server sur la surveillance](/sql/relational-databases/performance/monitor-and-tune-for-performance) fournit des informations supplémentaires sur ces fonctionnalités, ainsi que sur d’autres.

Une autre approche pour la capture des données de performances consiste à collecter les informations émises automatiquement par EF ou le pilote de base de données via l' `DiagnosticSource` interface, puis à analyser ces données ou à les afficher dans un tableau de bord. Si vous utilisez Azure, [Azure application Insights](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance) fournit une surveillance puissante prête à l’emploi, intégrant les performances de la base de données et les temps d’exécution des requêtes dans l’analyse de la vitesse à laquelle vos requêtes Web sont servies. Pour plus d’informations à ce propos, [reApplication Insights didacticiel](/azure/azure-monitor/learn/tutorial-performance)sur les performances et dans la [page Azure SQL Analytics](/azure/azure-monitor/insights/azure-sql).

## <a name="inspecting-query-execution-plans"></a>Inspection des plans d’exécution de requête

Une fois que vous avez identifié une requête problématique qui nécessite une optimisation, l’étape suivante consiste généralement à analyser le *plan d’exécution* de la requête. Lorsque les bases de données reçoivent une instruction SQL, elles produisent généralement un plan de la façon dont ce plan doit être exécuté ; Cela nécessite parfois une prise de décision compliquée basée sur les index définis, sur la quantité de données qui existent dans les tables, etc. (par ailleurs, le plan lui-même doit généralement être mis en cache sur le serveur pour des performances optimales). En règle générale, les bases de données relationnelles permettent aux utilisateurs de voir le plan de requête, ainsi que les coûts calculés pour les différentes parties de la requête. Cela est très utile pour l’amélioration de vos requêtes.

Pour commencer à utiliser SQL Server, consultez la documentation sur les [plans d’exécution de requête](/sql/relational-databases/performance/execution-plans). Le flux de travail d’analyse classique consiste à utiliser [SQL Server Management Studio](/sql/relational-databases/performance/display-an-actual-execution-plan), à coller le SQL d’une requête lente identifiée à l’aide de l’un des moyens ci-dessus et à [produire un plan d’exécution graphique](/sql/relational-databases/performance/display-an-actual-execution-plan):

![Afficher un plan d’exécution de SQL Server](_static/actualexecplan.png)

Alors que les plans d’exécution peuvent paraître compliqués au début, il est utile de consacrer un peu de temps à les connaître. Il est particulièrement important de noter les coûts associés à chaque nœud du plan et d’identifier la façon dont les index sont utilisés (ou non) dans les différents nœuds.

Bien que les informations ci-dessus soient spécifiques à SQL Server, les autres bases de données fournissent généralement le même type d’outils avec une visualisation similaire.

> [!IMPORTANT]
> Les bases de données génèrent parfois différents plans de requête en fonction des données réelles de la base de données. Par exemple, si une table contient uniquement quelques lignes, une base de données peut choisir de ne pas utiliser un index sur cette table, mais d’effectuer une analyse de table complète à la place. Si vous analysez des plans de requête sur une base de données de test, assurez-vous toujours qu’il contient des données similaires à celles de votre système de production.

## <a name="event-counters"></a>Compteurs d’événements

Les sections ci-dessus portent sur l’obtention d’informations sur vos commandes et sur l’exécution de ces commandes dans la base de données. En outre, EF expose un ensemble de compteurs d' *événements* qui fournissent des informations plus détaillées sur ce qui se passe dans EF lui-même et sur la manière dont votre application l’utilise. Ces compteurs peuvent être très utiles pour diagnostiquer des problèmes de performances spécifiques et des anomalies de performances, telles que des [problèmes de mise en cache des requêtes](xref:core/performance/advanced-performance-topics#dynamically-constructed-queries) qui provoquent une recompilation constante, des fuites de DbContext non commises et d’autres.

Pour plus d’informations, consultez la page dédiée sur les [compteurs d’événements d’EF](xref:core/logging-events-diagnostics/event-counters) .

## <a name="benchmarking-with-ef-core"></a>Évaluation avec EF Core

À la fin de la journée, vous devez parfois savoir si une façon particulière d’écrire ou d’exécuter une requête est plus rapide qu’une autre. Il est important de ne jamais supposer ou spéculer la réponse, et il est très facile de mettre en place un banc d’essai rapide pour obtenir la réponse. Lors de l’écriture d’un test d’évaluation, il est fortement recommandé d’utiliser la bibliothèque [BenchmarkDotNet](https://benchmarkdotnet.org/index.html) connue, qui gère de nombreux pièges rencontrés par les utilisateurs lors de la tentative d’écriture de leurs propres tests d’évaluation : avez-vous effectué des itérations de préparation ? Combien d’itérations votre évaluation s’exécute-t-elle réellement et pourquoi ? Jetons un coup d’œil à ce à quoi ressemble un point de référence avec EF Core.

> [!TIP]
> Le projet de test complet pour la source ci-dessous est disponible [ici](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Benchmarks/AverageBlogRanking.cs). Vous êtes encouragé à le copier et à l’utiliser comme modèle pour vos propres tests d’évaluation.

En guise de scénario de test simple, nous allons comparer les différentes méthodes de calcul du classement moyen de tous les blogs de notre base de données :

* Chargez toutes les entités, additionnez leurs classements individuels et calculez la moyenne.
* Comme ci-dessus, utilisez uniquement une requête de non-suivi. Cela devrait être plus rapide, car la résolution de l’identité n’est pas effectuée et les entités ne sont pas instantanées dans le cadre du suivi des modifications.
* Évitez de charger l’ensemble des instances d’entité de blog, en projetant uniquement le classement. Le évite de transférer les autres colonnes inutiles du type d’entité de blog.
* Calculez la moyenne dans la base de données en la faisant partie de la requête. Cela doit être le moyen le plus rapide, puisque tout est calculé dans la base de données et que seul le résultat est transféré au client.

Avec BenchmarkDotNet, vous écrivez le code à évaluer comme une méthode simple, tout comme un test unitaire-et BenchmarkDotNet exécute automatiquement chaque méthode pour un nombre suffisant d’itérations, en mesurant de manière fiable le temps nécessaire et la quantité de mémoire allouée. Voici la méthode différente ([le code d’évaluation complet peut être consulté ici](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Benchmarks/AverageBlogRanking.cs)) :

### <a name="load-entities"></a>[Charger des entités](#tab/load-entities)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntities)]

### <a name="load-entities-no-tracking"></a>[Charger des entités, pas de suivi](#tab/load-entities-no-tracking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntitiesNoTracking)]

### <a name="project-only-ranking"></a>[Classement du projet uniquement](#tab/project-only-ranking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=ProjectOnlyRanking)]

### <a name="calculate-in-database"></a>[Calculer dans la base de données](#tab/calculate-in-database)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=CalculateInDatabase)]

***

Les résultats sont affichés ci-dessous, tels qu’imprimés par BenchmarkDotNet :

|                 Méthode |       Moyenne |    Erreur |   StdDev |     Médiane | Proportions | RatioSD |    Génération 0 |   GEN 1 | Génération 2 |  Allocated |
|----------------------- |-----------:|---------:|---------:|-----------:|------:|--------:|---------:|--------:|------:|-----------:|
|           LoadEntities | 2 860,4 US | 54,31 US | 93,68 US | 2 844,5 US |  4.55 |    0,33 | 210,9375 | 70,3125 |     - | 1309,56 KO |
| LoadEntitiesNoTracking | 1 353,0 US | 21,26 US | 18,85 US | 1 355,6 US |  2.10 |    0,14 |  87,8906 |  3,9063 |     - |  540,09 KO |
|     ProjectOnlyRanking |   910,9 US | 20,91 US | 61,65 US |   892,9 US |  1,46 |    0,14 |  41,0156 |  0,9766 |     - |  252,08 KO |
|    CalculateInDatabase |   627,1 US | 14,58 US | 42,54 US |   626,4 US |  1.00 |    0,00 |   4,8828 |       - |     - |   33,27 KO |

> [!NOTE]
> À mesure que les méthodes instancient et suppriment le contexte dans la méthode, ces opérations sont comptabilisées pour le test d’évaluation, bien qu’elles ne fassent pas partie du processus d’interrogation. Cela n’a pas d’importance si l’objectif est de comparer deux alternatives les unes aux autres (puisque l’instanciation et la suppression de contexte sont les mêmes) et fournit une mesure plus holistique pour l’ensemble de l’opération.

L’une des limitations de BenchmarkDotNet est qu’il mesure les performances simples et uniques des méthodes que vous fournissez, et n’est donc pas bien adapté à l’évaluation des scénarios simultanés.

> [!IMPORTANT]
> Veillez à toujours faire en sorte que les données de votre base de données soient similaires aux données de production lors de l’évaluation. dans le cas contraire, les résultats du benchmark peuvent ne pas représenter les performances réelles en production.
