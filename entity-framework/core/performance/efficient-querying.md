---
title: Interrogation efficace-EF Core
description: Guide des performances pour l’interrogation efficace à l’aide de Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-querying
ms.openlocfilehash: acd5388745e74a42925c8500ce610aef83e75384
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657807"
---
# <a name="efficient-querying"></a>Interrogation efficace

L’interrogation efficace est un vaste sujet qui couvre des sujets aussi larges que des index, des stratégies de chargement d’entités associées et bien d’autres. Cette section détaille certains thèmes courants pour accélérer l’exécution de vos requêtes et les pièges que les utilisateurs rencontrent généralement.

## <a name="use-indexes-properly"></a>Utiliser correctement les index

Le principal facteur déterminant si une requête s’exécute rapidement ou non est qu’elle utilise correctement les index quand cela est approprié : les bases de données sont généralement utilisées pour stocker de grandes quantités de données, et les requêtes qui traversent des tables entières sont généralement des sources de problèmes de performances graves. Les problèmes d’indexation ne sont pas faciles à repérer, car il n’est pas immédiatement évident qu’une requête donnée utilise ou non un index. Par exemple :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Indexes)]

Une bonne façon de détecter les problèmes d’indexation consiste à identifier d’abord une requête lente, puis à examiner son plan de requête via l’outil favori de votre base de données. Pour plus d’informations sur la façon de procéder, consultez la page [diagnostic des performances](xref:core/performance/performance-diagnosis) . Le plan de requête indique si la requête parcourt la table entière ou utilise un index.

En règle générale, il n’existe pas de connaissance d’EF spéciale pour utiliser des index ou diagnostiquer des problèmes de performances associés. la connaissance générale des bases de données liées aux index s’applique aux applications EF en ce qui concerne les applications qui n’utilisent pas EF. La liste suivante répertorie certaines recommandations générales à prendre en compte lors de l’utilisation d’index :

* Alors que les index accélèrent les requêtes, ils ralentissent également les mises à jour, car ils doivent être tenus à jour. Évitez de définir des index qui ne sont pas nécessaires et envisagez d’utiliser des [filtres d’index](xref:core/modeling/indexes#index-filter) pour limiter l’index à un sous-ensemble de lignes, réduisant ainsi cette surcharge.
* Les index composites peuvent accélérer les requêtes qui filtrent sur plusieurs colonnes, mais ils peuvent également accélérer les requêtes qui ne filtrent pas sur toutes les colonnes de l’index, en fonction de l’ordre. Par exemple, un index sur les colonnes A et B accélère les requêtes en filtrant par A et B, ainsi que les requêtes de filtrage uniquement par un, mais il n’accélère pas le filtrage des requêtes sur uniquement par B.
* Si une requête filtre par une expression sur une colonne (par exemple `price / 2` ,), un index simple ne peut pas être utilisé. Toutefois, vous pouvez définir une [colonne persistante stockée](xref:core/modeling/generated-properties#computed-columns) pour votre expression et créer un index sur cette colonne. Certaines bases de données prennent également en charge les index d’expressions, qui peuvent être utilisés directement pour accélérer le filtrage des requêtes par toute expression.
* Les différentes bases de données permettent de configurer des index de différentes manières, et dans de nombreux cas EF Core fournisseurs les exposent via l’API Fluent. Par exemple, le fournisseur de SQL Server vous permet de configurer si un index est [en cluster](xref:core/providers/sql-server/indexes#clustering)ou de définir son [facteur de remplissage](xref:core/providers/sql-server/indexes#fill-factor). Pour plus d’informations, consultez la documentation de votre fournisseur.

## <a name="project-only-properties-you-need"></a>Propriétés du projet uniquement dont vous avez besoin

EF Core facilite grandement l’interrogation des instances d’entité, puis l’utilisation de ces instances dans le code. Toutefois, l’interrogation des instances d’entité peut souvent extraire plus de données que nécessaire de votre base de données. Tenez compte des éléments suivants :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectEntities)]

Bien que ce code ait uniquement besoin de la propriété de chaque blog `Url` , la totalité de l’entité de blog est extraite et les colonnes inutiles sont transférées à partir de la base de données :

```sql
SELECT [b].[BlogId], [b].[CreationDate], [b].[Name], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
```

Vous pouvez l’optimiser en utilisant `Select` pour indiquer à EF les colonnes à projeter :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectSingleProperty)]

Le SQL obtenu extrait uniquement les colonnes nécessaires :

```csharp
SELECT [b].[Url]
FROM [Blogs] AS [b]
```

Si vous devez projeter plusieurs colonnes, projetez dans un type anonyme C# avec les propriétés de votre choix.

Notez que cette technique est très utile pour les requêtes en lecture seule, mais les choses sont plus compliquées si vous devez *mettre à jour* les blogs extraits, puisque le suivi des modifications d’EF ne fonctionne qu’avec des instances d’entité. Il est possible d’effectuer des mises à jour sans avoir à charger des entités entières en attachant une instance de blog modifiée et en indiquant à EF les propriétés qui ont changé, mais il s’agit d’une technique plus avancée qui peut ne pas en être intéressante.

## <a name="limit-the-resultset-size"></a>Limiter la taille du jeu de résultats

Par défaut, une requête retourne toutes les lignes qui correspondent à ses filtres :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NoLimit)]

Étant donné que le nombre de lignes retournées dépend de données réelles dans votre base de données, il est impossible de connaître la quantité de données qui sera chargée à partir de la base de données, la quantité de mémoire qui sera prise en charge par les résultats et la quantité de charge supplémentaire générée lors du traitement de ces résultats (par exemple, en les envoyant à un navigateur d’utilisateur sur le réseau De manière cruciale, les bases de données de test contiennent souvent peu de données, de sorte que tout fonctionne bien pendant les tests, mais que des problèmes de performances apparaissent soudainement lorsque l’exécution de la requête commence sur des données réelles et que de nombreuses lignes sont retournées.

Par conséquent, il est généralement intéressant de penser à limiter le nombre de résultats :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Limit25)]

Au minimum, votre interface utilisateur peut afficher un message indiquant que des lignes supplémentaires peuvent exister dans la base de données (et autoriser leur récupération d’une autre manière). Une solution complète implémente la *pagination*, où votre interface utilisateur n’affiche qu’un certain nombre de lignes à la fois, et permet aux utilisateurs d’avancer jusqu’à la page suivante, si nécessaire. Cela combine généralement les <xref:System.Linq.Enumerable.Take%2A> <xref:System.Linq.Enumerable.Skip%2A> opérateurs et pour sélectionner chaque fois une plage spécifique dans le jeu de résultats.

## <a name="avoid-cartesian-explosion-when-loading-related-entities"></a>Éviter l’explosion cartésiene lors du chargement des entités associées

Dans les bases de données relationnelles, toutes les entités associées sont chargées en introduisant des JOINTUREs dans une requête unique.

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Si un blog classique contient plusieurs publications connexes, les lignes de ces publications dupliqueront les informations du blog. Cette duplication porte sur le problème de « explosion cartésien ». À mesure que des relations un-à-plusieurs sont chargées, la quantité de données dupliquées peut croître et nuire aux performances de votre application.

EF permet d’éviter cet effet via l’utilisation de « requêtes fractionnées », qui chargent les entités associées par le biais de requêtes distinctes. Pour plus d’informations, consultez [la documentation sur les requêtes Split et Single](xref:core/querying/single-split-queries).

> [!NOTE]
> L’implémentation actuelle des [requêtes fractionnées](xref:core/querying/single-split-queries) exécute un aller-retour pour chaque requête. Nous prévoyons d’améliorer cette amélioration à l’avenir et d’exécuter toutes les requêtes en un seul aller-retour.

## <a name="load-related-entities-eagerly-when-possible"></a>Charger les entités associées de façon dynamique lorsque cela est possible

Avant de poursuivre cette section, il est recommandé de lire [la page dédiée sur les entités associées](xref:core/querying/related-data) .

En ce qui concerne les entités associées, nous savons généralement à l’avance ce que nous devons charger : un exemple classique est le chargement d’un certain ensemble de blogs, ainsi que l’ensemble de leurs publications. Dans ces scénarios, il est toujours préférable d’utiliser le [chargement hâtif](xref:core/querying/related-data/eager), afin qu’EF puisse extraire toutes les données requises dans un seul aller-retour. La fonctionnalité [include filtrée](xref:core/querying/related-data/eager#filtered-include) , introduite dans EF Core 5,0, vous permet également de limiter les entités associées que vous souhaitez charger, tout en conservant le processus de chargement hâtif et donc réalisable dans un seul aller-retour :

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

Dans d’autres scénarios, il est possible que nous ne sachions pas quelle entité associée nous allons avoir besoin avant d’obtenir son entité principale. Par exemple, lors du chargement d’un blog, nous pouvons être amenés à consulter d’autres sources de données, éventuellement un WebService, afin de savoir si nous sommes intéressés par les publications de ce blog. Dans ce cas, le chargement [explicite](xref:core/querying/related-data/explicit) ou [différé](xref:core/querying/related-data/lazy) peut être utilisé pour extraire séparément les entités associées et remplir la navigation des publications du blog. Notez que dans la mesure où ces méthodes ne sont pas impatientes, elles nécessitent des allers-retours supplémentaires vers la base de données, qui est source de ralentissement. selon votre scénario spécifique, il peut être plus efficace de toujours charger toutes les publications, plutôt que d’exécuter les allers-retours supplémentaires et d’obtenir uniquement les publications dont vous avez besoin.

### <a name="beware-of-lazy-loading"></a>Méfiez-vous du chargement différé

Le [chargement différé](xref:core/querying/related-data/lazy) semble souvent un moyen très utile d’écrire la logique de la base de données, car EF Core charge automatiquement les entités associées à partir de la base de données à mesure que votre code y accède. Cela évite le chargement des entités associées qui ne sont pas nécessaires (par exemple, le [chargement explicite](xref:core/querying/related-data/explicit)) et, apparemment, évite au programmeur d’avoir à gérer complètement les entités associées. Toutefois, le chargement différé est particulièrement sujet à la génération de boucles supplémentaires inutiles qui peuvent ralentir l’application.

Tenez compte des éléments suivants :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NPlusOne)]

Ce morceau de code apparemment inoffensif parcourt tous les blogs et leurs publications, en les imprimant. L’activation de la [journalisation des instructions](xref:core/logging-events-diagnostics/index) de EF Core révèle ce qui suit :

```console
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[BlogId], [b].[Rating], [b].[Url]
      FROM [Blogs] AS [b]
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (5ms) [Parameters=[@__p_0='1'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='2'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='3'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0

... and so on
```

Comment cela se fait-il ? Pourquoi toutes ces requêtes sont-elles envoyées pour les boucles simples ci-dessus ? Avec le chargement différé, les publications d’un blog sont chargées uniquement (différées) lors de l’accès à la propriété Posts. par conséquent, chaque itération dans le foreach interne déclenche une requête de base de données supplémentaire, dans son propre aller-retour. Par conséquent, une fois que la requête initiale a chargé tous les blogs, nous avons une autre requête *par blog*, en chargeant toutes ses publications. C’est ce que l’on appelle parfois le problème *N + 1* et cela peut entraîner des problèmes de performances très importants.

En supposant que nous aurons besoin de tous les billets de blog, il est judicieux d’utiliser le chargement hâtif ici à la place. Nous pouvons utiliser l’opérateur [include](xref:core/querying/related-data/eager#eager-loading) pour effectuer le chargement, mais puisque nous avons uniquement besoin des URL des blogs (et nous devrions uniquement [charger ce qui est nécessaire](xref:core/performance/efficient-updating#project-only-properties-you-need)). Nous allons donc utiliser une projection à la place :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#EagerlyLoadRelatedAndProject)]

Ainsi, EF Core récupérer tous les blogs, ainsi que leurs publications, en une seule requête. Dans certains cas, il peut également être utile d’éviter les effets d’éclatement cartésien en utilisant des [requêtes fractionnées](xref:core/querying/single-split-queries).

> [!WARNING]
> Étant donné que le chargement différé permet de déclencher très facilement le problème N + 1 par inadvertance, il est recommandé de l’éviter. Le chargement hâtif ou explicite rend très clair dans le code source lorsqu’un aller-retour de base de données se produit.

## <a name="buffering-and-streaming"></a>Mise en mémoire tampon et diffusion en continu

La mise en mémoire tampon fait référence au chargement de tous les résultats de votre requête dans la mémoire, tandis que la diffusion en continu signifie que EF envoie un résultat unique à l’application à chaque fois, et ne contient jamais l’intégralité du jeu de résultats en mémoire. En principe, les besoins en mémoire d’une requête de streaming sont fixes : ils sont les mêmes si la requête retourne 1 ligne ou 1000 ; en revanche, une requête de mise en mémoire tampon requiert plus de mémoire, plus les lignes sont retournées. Pour les requêtes qui génèrent des jeux de résultats volumineux, il peut s’agir d’un facteur de performance important.

La façon dont les mémoires tampons ou les flux de requête dépendent de la façon dont elles sont évaluées :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#BufferingAndStreaming)]

Si vos requêtes renvoient simplement quelques résultats, vous n’avez probablement pas à vous soucier de cela. Toutefois, si votre requête peut retourner un grand nombre de lignes, il est utile de réfléchir à la diffusion en continu au lieu de la mise en mémoire tampon.

> [!NOTE]
> Évitez d' <xref:System.Linq.Enumerable.ToList%2A> <xref:System.Linq.Enumerable.ToArray%2A> utiliser ou si vous envisagez d’utiliser un autre opérateur LINQ sur le résultat, cela entraînera inutilement la mise en mémoire tampon de tous les résultats. Utilisez <xref:System.Linq.Enumerable.AsEnumerable%2A> à la place.

### <a name="internal-buffering-by-ef"></a>Mise en mémoire tampon interne par EF

Dans certaines situations, EF met lui-même en mémoire tampon le ResultSet en interne, quelle que soit la façon dont vous évaluez votre requête. Les deux cas où cela se produit sont les suivants :

* Lorsqu’une stratégie de nouvelle tentative d’exécution est en place. Cela permet de s’assurer que les mêmes résultats sont retournés si la requête est retentée ultérieurement.
* Lorsque la [requête Split](xref:core/querying/single-split-queries) est utilisée, les jeux de résultats de toutes les requêtes sauf la dernière sont mis en mémoire tampon, sauf si mars est activé sur SQL Server. Cela est dû au fait qu’il est généralement impossible d’avoir plusieurs jeux de résultats de requête actifs en même temps.

Notez que cette mise en mémoire tampon interne se produit en plus de la mise en mémoire tampon que vous provoquez via les opérateurs LINQ. Par exemple, si vous utilisez <xref:System.Linq.Enumerable.ToList%2A> sur une requête et qu’une stratégie de nouvelle tentative d’exécution est en place, le jeu de résultats est chargé en mémoire *deux fois*: en interne par EF, et une fois par <xref:System.Linq.Enumerable.ToList%2A> .

## <a name="tracking-no-tracking-and-identity-resolution"></a>Suivi, sans suivi et résolution d’identité

Avant de poursuivre cette section, il est recommandé de lire [la page dédiée sur le suivi et le non-suivi](xref:core/querying/tracking) .

EF effectue le suivi des instances d’entité par défaut, afin que les modifications qui s’y appliquent soient détectées et rendues persistantes quand <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé. Un autre effet des requêtes de suivi est que EF détecte si une instance a déjà été chargée pour vos données et retourne automatiquement cette instance suivie plutôt que d’en retourner une nouvelle. C’est ce que l’on appelle la *résolution d’identité*. Du point de vue des performances, le suivi des modifications signifie ce qui suit :

* EF gère en interne un dictionnaire d’instances suivies. Lorsque de nouvelles données sont chargées, EF vérifie le dictionnaire pour déterminer si une instance est déjà suivie pour la clé de cette entité (résolution d’identité). La maintenance du dictionnaire et les recherches prennent un certain temps lors du chargement des résultats de la requête.
* Avant de transmettre une instance chargée à l’application, EF *capture les instantanés* de cette instance et conserve l’instantané en interne. Lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé, l’instance de l’application est comparée à celle de l’instantané pour détecter les modifications à rendre persistantes. L’instantané prend plus de mémoire et le processus d’instantané prend du temps. Il est parfois possible de spécifier un comportement d’interblocage différent, éventuellement plus efficace par le biais de [comparateurs de valeur](xref:core/modeling/value-comparers), ou d’utiliser des proxys de suivi des modifications pour contourner complètement le processus d’instantané (bien que cela soit fourni avec son propre ensemble de inconvénients).

Dans les scénarios en lecture seule où les modifications ne sont pas enregistrées dans la base de données, les retards ci-dessus peuvent être évités en utilisant des [requêtes sans suivi](xref:core/querying/tracking#no-tracking-queries). Toutefois, étant donné que les requêtes sans suivi n’effectuent pas de résolution d’identité, une ligne de base de données référencée par plusieurs autres lignes chargées est matérialisée comme des instances différentes.

Pour illustrer cela, supposons que nous chargeons un grand nombre de publications à partir de la base de données, ainsi que le blog référencé par chaque publication. Si 100 publications se produisent pour faire référence au même blog, une requête de suivi le détecte via la résolution d’identité et toutes les instances post font référence à la même instance de blog dédupliquée. Une requête sans suivi, en revanche, duplique les mêmes heures du blog 100 et le code d’application doit être écrit en conséquence.

Voici les résultats d’un test comparatif comparaison entre le suivi et le non-suivi pour une requête chargeant 10 blogs avec 20 publications chacun. [Le code source est disponible ici. n'](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/QueryTrackingBehavior.cs)hésitez pas à l’utiliser comme base pour vos propres mesures.

|       Méthode | NumBlogs | NumPostsPerBlog |       Moyenne |    Erreur |   StdDev |     Médiane | Proportions | RatioSD |   Génération 0 |   GEN 1 | Génération 2 | Allocated |
|------------- |--------- |---------------- |-----------:|---------:|---------:|-----------:|------:|--------:|--------:|--------:|------:|----------:|
|   AsTracking |       10 |              20 | 1 414,7 US | 27,20 US | 45,44 US | 1 405,5 US |  1.00 |    0,00 | 60,5469 | 13,6719 |     - | 380,11 KO |
| AsNoTracking |       10 |              20 |   993,3 US | 24,04 US | 65,40 US |   966,2 US |  0.71 |    0,05 | 37,1094 |  6,8359 |     - | 232,89 KO |

Enfin, il est possible d’effectuer des mises à jour sans la surcharge du suivi des modifications, en utilisant une requête de non-suivi, puis en attachant l’instance retournée au contexte, en spécifiant les modifications à apporter. Cela transfère la charge du suivi des modifications d’EF à l’utilisateur et ne doit être tentée que si la surcharge de suivi des modifications a été révélée inacceptable par le biais du profilage ou de l’évaluation.

## <a name="using-raw-sql"></a>Utilisation du SQL brut

Dans certains cas, il existe un SQL plus optimisé pour votre requête, qu’EF ne génère pas. Cela peut se produire lorsque la construction SQL est une extension spécifique à votre base de données qui n’est pas prise en charge, ou simplement parce que EF ne la convertit pas encore. Dans ce cas, l’écriture de SQL à la main peut améliorer considérablement les performances et EF prend en charge plusieurs façons de procéder.

* Utilisez le SQL brut [directement dans votre requête](xref:core/querying/raw-sql), par exemple via <xref:Microsoft.EntityFrameworkCore.RelationalQueryableExtensions.FromSqlRaw%2A> . EF vous permet même de composer le SQL brut à l’aide de requêtes LINQ classiques, ce qui vous permet d’exprimer uniquement une partie de la requête dans le SQL brut. Il s’agit d’une bonne technique lorsque le SQL brut ne doit être utilisé que dans une seule requête dans votre base de code.
* Définissez une [fonction définie par l’utilisateur](xref:core/querying/database-functions) (UDF), puis appelez-la à partir de vos requêtes. Notez que depuis 5,0, EF permet aux fonctions définies par l’utilisateur de retourner des jeux de résultats complets. ces fonctions sont connues sous le nom de fonctions table (TVF). elle permet également de mapper une `DbSet` à une fonction, en faisant en sorte qu’elle ressemble simplement à une autre table.
* Définissez une vue de base de données et interrogez-la dans vos requêtes. Notez que contrairement aux fonctions, les vues ne peuvent pas accepter de paramètres.

> [!NOTE]
> Le SQL brut doit généralement être utilisé en dernier recours, après avoir vérifié que EF ne peut pas générer le SQL souhaité, et lorsque les performances sont suffisamment importantes pour que la requête donnée les justifie. L’utilisation de SQL brut apporte des inconvénients importants en matière de maintenance.

## <a name="asynchronous-programming"></a>Programmation asynchrone

En règle générale, pour que votre application soit évolutive, il est important de toujours utiliser des API asynchrones plutôt que synchrones (par exemple, <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> plutôt que <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ). Les API synchrones bloquent le thread pendant la durée des e/s de base de données, ce qui améliore le besoin de threads et le nombre de commutateurs de contexte de thread qui doivent se produire.

Pour plus d’informations, consultez la page sur la [programmation asynchrone](xref:core/miscellaneous/async).

> [!WARNING]
> Évitez de mélanger du code synchrone et asynchrone au sein d’une même application. il est très facile de déclencher par inadvertance des problèmes de privation subtils de pool de threads.
