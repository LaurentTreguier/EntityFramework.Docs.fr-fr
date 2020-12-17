---
title: Rubriques avancées sur les performances
description: Rubriques avancées sur les performances de Entity Framework Core
author: rick-anderson
ms.author: riande
ms.date: 12/9/2020
uid: core/performance/advanced-performance-topics
ms.openlocfilehash: 3c0340e1b36cbbb96d23db0633cb2eebc04dd970
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657831"
---
# <a name="advanced-performance-topics"></a>Rubriques avancées sur les performances

## <a name="dbcontext-pooling"></a>Regroupement DbContext

`AddDbContextPool` active le regroupement d' `DbContext` instances. Le regroupement de contexte peut augmenter le débit dans des scénarios à grande échelle tels que les serveurs Web en réutilisant des instances de contexte, plutôt que de créer des instances pour chaque demande.

Le modèle typique d’une application ASP.NET Core utilisant EF Core implique l’inscription d’un type personnalisé dans <xref:Microsoft.EntityFrameworkCore.DbContext> le conteneur d' [injection de dépendances](/aspnet/core/fundamentals/dependency-injection) et l’obtention d’instances de ce type par le biais de paramètres de constructeur dans les contrôleurs ou les Razor pages. À l’aide de l’injection de constructeur, une nouvelle instance de contexte est créée pour chaque requête.

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> active un pool d’instances de contexte réutilisables. Pour utiliser le regroupement de contexte, utilisez la `AddDbContextPool` méthode au lieu de lors de l' `AddDbContext` inscription du service :

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Lorsque `AddDbContextPool` est utilisé, au moment où une instance de contexte est demandée, EF vérifie d’abord si une instance est disponible dans le pool. Une fois finalisé le traitement de la demande, tout état sur l’instance est réinitialisé et l’instance elle-même est retournée au pool.

Ce concept est similaire à celui de l’utilisation du regroupement de connexions dans les fournisseurs ADO.NET et présente l’avantage d’enregistrer un certain coût d’initialisation de l’instance de contexte.

Le `poolSize` paramètre de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> définit le nombre maximal d’instances conservées par le pool. Une fois que `poolSize` est dépassé, les nouvelles instances de contexte ne sont pas mises en cache et EF revient au comportement de non-regroupement de la création d’instances à la demande.

### <a name="limitations"></a>Limites

Les applications doivent être profilées et testées pour montrer que l’initialisation du contexte est un coût significatif.

`AddDbContextPool` présente quelques limitations sur ce qui peut être fait dans la `OnConfiguring` méthode du contexte.

> [!WARNING]
> Évitez d’utiliser le regroupement de contexte dans les applications qui maintiennent l’État. Par exemple, les champs privés dans le contexte qui ne doivent pas être partagés entre les requêtes. EF Core réinitialise uniquement l’État qu’il reconnaît avant d’ajouter une instance de contexte au pool.

Le regroupement de contexte fonctionne en réutilisant la même instance de contexte entre les requêtes. Cela signifie qu’elle est enregistrée en tant que [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) en termes de l’instance elle-même afin de pouvoir être persistante.

Le regroupement de contexte est destiné aux scénarios dans lesquels la configuration de contexte, qui comprend les services résolus, est fixée entre les demandes. Dans les cas où des services [délimités](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) sont requis ou si la configuration doit être modifiée, n’utilisez pas le regroupement. Le gain de performances par rapport au regroupement est généralement négligeable, sauf dans les scénarios hautement optimisés.

## <a name="query-caching-and-parameterization"></a>Mise en cache et paramétrage des requêtes

Quand EF reçoit une arborescence de requêtes LINQ pour l’exécution, il doit d’abord « compiler » cette arborescence dans une requête SQL. Étant donné qu’il s’agit d’un processus lourd, EF met en cache les requêtes en fonction de la *forme* d’arborescence de requête : les requêtes avec la même structure réutilisent les sorties de compilation mises en cache en interne et peuvent ignorer la compilation répétée. Les différentes requêtes peuvent toujours faire référence à des *valeurs* différentes, mais tant que ces valeurs sont paramétrées correctement, la structure est la même et la mise en cache fonctionne correctement.

Considérez les deux requêtes suivantes :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithConstants)]

Étant donné que les arborescences d’expressions contiennent des constantes différentes, l’arborescence d’expression diffère et chacune de ces requêtes est compilée séparément par EF Core. En outre, chaque requête produit une commande SQL légèrement différente :

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog1'

SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog2'
```

Étant donné que le SQL diffère, votre serveur de base de données devra probablement créer un plan de requête pour les deux requêtes, plutôt que de réutiliser le même plan.

Une petite modification de vos requêtes peut modifier considérablement les choses :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithParameterization)]

Étant donné que le nom du blog est maintenant *paramétré*, les deux requêtes ont la même forme d’arborescence et EF ne doit être compilé qu’une seule fois. Le SQL produit est également paramétré, ce qui permet à la base de données de réutiliser le même plan de requête :

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = @__blogName_0
```

Notez qu’il n’est pas nécessaire de paramétrer chaque requête. il est tout à fait parfait d’avoir certaines requêtes avec des constantes, et en effet, les bases de données (et EF) peuvent parfois effectuer une certaine optimisation autour des constantes, ce qui n’est pas possible lorsque la requête est paramétrée. Consultez la section sur les [requêtes construites dynamiquement](#dynamically-constructed-queries) pour obtenir un exemple où le paramétrage approprié est essentiel.

> [!NOTE]
> Les [compteurs d’événements](xref:core/logging-events-diagnostics/event-counters) de EF Core signalent le taux d’accès au cache des requêtes. Dans une application normale, ce compteur atteint 100% juste après le démarrage du programme, une fois que la plupart des requêtes ont été exécutées au moins une fois. Si ce compteur reste stable sous 100%, cela indique que votre application peut effectuer une opération qui met un terme au cache des requêtes. il est donc judicieux de l’étudier.

> [!NOTE]
> La façon dont la base de données gère les plans de requête de cache dépend de la base de données. Par exemple, SQL Server gère implicitement un cache de plan de requête LRU, alors que PostgreSQL ne le fait pas (mais les instructions préparées peuvent produire un effet de fin très similaire). Pour plus d’informations, consultez la documentation de votre base de données.

## <a name="dynamically-constructed-queries"></a>Requêtes construites dynamiquement

Dans certains cas, il est nécessaire de construire dynamiquement des requêtes LINQ au lieu de les spécifier dans le code source. Cela peut se produire, par exemple, dans un site Web qui reçoit des détails de requête arbitraires d’un client, avec des opérateurs de requête en cours d’achèvement (tri, filtrage, pagination...). En principe, si vous avez terminé correctement, les requêtes construites dynamiquement peuvent être aussi efficaces que les requêtes régulières (bien qu’il ne soit pas possible d’utiliser l’optimisation de requête compilée avec des requêtes dynamiques). Toutefois, dans la pratique, il s’agit souvent de la source de problèmes de performances, car il est facile de produire accidentellement des arborescences d’expressions avec des formes qui diffèrent à chaque fois.

L’exemple suivant utilise deux techniques pour construire dynamiquement une requête ; Nous ajoutons un `Where` opérateur à la requête uniquement si le paramètre donné n’a pas la valeur null. Notez qu’il ne s’agit pas d’un bon cas d’utilisation pour la construction dynamique d’une requête, mais que nous l’utilisons pour des raisons de simplicité :

### <a name="with-constant"></a>[Avec constante](#tab/with-constant)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithConstant&highlight=14-24)]

### <a name="with-parameter"></a>[Avec le paramètre](#tab/with-parameter)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithParameter&highlight=14)]

***

L’évaluation de ces deux techniques donne les résultats suivants :

|        Méthode |       Moyenne |    Erreur |    StdDev |   Génération 0 |  GEN 1 | Génération 2 | Allocated |
|-------------- |-----------:|---------:|----------:|--------:|-------:|------:|----------:|
|  WithConstant | 1 096,7 US | 12,54 US |  11,12 US | 13,6719 | 1,9531 |     - |  83,91 KO |
| WithParameter |   570,8 US | 42,43 US | 124,43 US |  5,8594 |      - |     - |  37,16 KO |

Même si la différence des sous-millisecondes semble petite, gardez à l’esprit que la version constante pollue en permanence le cache et provoque la recompilation d’autres requêtes, en les ralentissant également.

> [!NOTE]
> Évitez de créer des requêtes avec l’API d’arborescence d’expression, sauf si vous en avez vraiment besoin. Hormis la complexité de l’API, il est très facile de provoquer par inadvertance de sérieux problèmes de performances lors de leur utilisation.
