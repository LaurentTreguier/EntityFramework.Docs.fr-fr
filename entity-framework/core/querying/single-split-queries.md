---
title: Requêtes Single et Split-EF Core
description: Traduction de la requête en requêtes Single et Split dans SQL avec Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 1857e05d45979c5530a349b9c7470407e7574fdf
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983428"
---
# <a name="split-queries"></a><span data-ttu-id="de22d-103">Fractionner des requêtes</span><span class="sxs-lookup"><span data-stu-id="de22d-103">Split queries</span></span>

## <a name="single-queries"></a><span data-ttu-id="de22d-104">Requêtes uniques</span><span class="sxs-lookup"><span data-stu-id="de22d-104">Single queries</span></span>

<span data-ttu-id="de22d-105">Dans les bases de données relationnelles, toutes les entités associées sont chargées en introduisant des JOINTUREs dans une requête unique.</span><span class="sxs-lookup"><span data-stu-id="de22d-105">In relational databases, all related entities are loaded by introducing JOINs in single query.</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="de22d-106">Si un blog classique contient plusieurs publications connexes, les lignes de ces publications dupliqueront les informations du blog.</span><span class="sxs-lookup"><span data-stu-id="de22d-106">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information.</span></span> <span data-ttu-id="de22d-107">Cette duplication porte sur le problème de « explosion cartésien ».</span><span class="sxs-lookup"><span data-stu-id="de22d-107">This duplication leads to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="de22d-108">À mesure que des relations un-à-plusieurs sont chargées, la quantité de données dupliquées peut croître et nuire aux performances de votre application.</span><span class="sxs-lookup"><span data-stu-id="de22d-108">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span>

## <a name="split-queries"></a><span data-ttu-id="de22d-109">Fractionner des requêtes</span><span class="sxs-lookup"><span data-stu-id="de22d-109">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="de22d-110">Cette fonctionnalité a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="de22d-110">This feature was introduced in EF Core 5.0.</span></span> <span data-ttu-id="de22d-111">Elle fonctionne uniquement lorsque vous utilisez `Include` .</span><span class="sxs-lookup"><span data-stu-id="de22d-111">It only works when using `Include`.</span></span> <span data-ttu-id="de22d-112">[Ce problème concerne le](https://github.com/dotnet/efcore/issues/21234) suivi de la prise en charge des requêtes fractionnées lors du chargement des données associées dans la projection sans `Include` .</span><span class="sxs-lookup"><span data-stu-id="de22d-112">[This issue](https://github.com/dotnet/efcore/issues/21234) is tracking support for split query when loading related data in projection without `Include`.</span></span>

<span data-ttu-id="de22d-113">EF vous permet de spécifier qu’une requête LINQ donnée doit être *fractionnée* en plusieurs requêtes SQL.</span><span class="sxs-lookup"><span data-stu-id="de22d-113">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="de22d-114">Au lieu de JOINTUREs, les requêtes Split génèrent une requête SQL supplémentaire pour chaque navigation dans la collection incluse :</span><span class="sxs-lookup"><span data-stu-id="de22d-114">Instead of JOINs, split queries generate an additional SQL query for each included collection navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="de22d-115">Il produira le code SQL suivant :</span><span class="sxs-lookup"><span data-stu-id="de22d-115">It will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> <span data-ttu-id="de22d-116">Les entités associées à un-à-un sont toujours chargées via des JOINTUREs dans la même requête, car elle n’a aucun impact sur les performances.</span><span class="sxs-lookup"><span data-stu-id="de22d-116">One-to-one related entities are always loaded via JOINs in the same query, as it has no performance impact.</span></span>

## <a name="enabling-split-queries-globally"></a><span data-ttu-id="de22d-117">Activation globale des requêtes fractionnées</span><span class="sxs-lookup"><span data-stu-id="de22d-117">Enabling split queries globally</span></span>

<span data-ttu-id="de22d-118">Vous pouvez également configurer des requêtes fractionnées comme valeur par défaut pour le contexte de votre application :</span><span class="sxs-lookup"><span data-stu-id="de22d-118">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="de22d-119">Lorsque les requêtes de fractionnement sont configurées en tant que valeurs par défaut, il est toujours possible de configurer des requêtes spécifiques pour qu’elles s’exécutent en tant que requêtes uniques :</span><span class="sxs-lookup"><span data-stu-id="de22d-119">When split queries are configured as the default, it's still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="de22d-120">EF Core utilise le mode de requête unique par défaut en l’absence de configuration.</span><span class="sxs-lookup"><span data-stu-id="de22d-120">EF Core uses single query mode by default in the absence of any configuration.</span></span> <span data-ttu-id="de22d-121">Dans la mesure où cela peut entraîner des problèmes de performances, EF Core génère un avertissement chaque fois que les conditions suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="de22d-121">Since it may cause performance issues, EF Core generates a warning whenever following conditions are met:</span></span>

- <span data-ttu-id="de22d-122">EF Core détecte que la requête charge plusieurs collections.</span><span class="sxs-lookup"><span data-stu-id="de22d-122">EF Core detects that the query loads multiple collections.</span></span>
- <span data-ttu-id="de22d-123">L’utilisateur n’a pas configuré le mode de fractionnement de requête globalement.</span><span class="sxs-lookup"><span data-stu-id="de22d-123">User hasn't configured query splitting mode globally.</span></span>
- <span data-ttu-id="de22d-124">L’utilisateur n’a pas utilisé `AsSingleQuery` / `AsSplitQuery` l’opérateur sur la requête.</span><span class="sxs-lookup"><span data-stu-id="de22d-124">User hasn't used `AsSingleQuery`/`AsSplitQuery` operator on the query.</span></span>

<span data-ttu-id="de22d-125">Pour désactiver l’avertissement, configurez le mode de fractionnement des requêtes globalement ou au niveau de la requête avec une valeur appropriée.</span><span class="sxs-lookup"><span data-stu-id="de22d-125">To turn off the warning, configure query splitting mode globally or at the query level to an appropriate value.</span></span>

## <a name="characteristics-of-split-queries"></a><span data-ttu-id="de22d-126">Caractéristiques des requêtes fractionnées</span><span class="sxs-lookup"><span data-stu-id="de22d-126">Characteristics of split queries</span></span>

<span data-ttu-id="de22d-127">Si la requête Split évite les problèmes de performances associés aux JOINTUREs et à l’explosion cartésienne, elle présente également certains inconvénients :</span><span class="sxs-lookup"><span data-stu-id="de22d-127">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

- <span data-ttu-id="de22d-128">Alors que la plupart des bases de données garantissent la cohérence des données pour les requêtes uniques, il n’existe pas de garantie de ce type pour plusieurs requêtes.</span><span class="sxs-lookup"><span data-stu-id="de22d-128">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="de22d-129">Si la base de données est mise à jour en même temps lors de l’exécution de vos requêtes, les données résultantes peuvent ne pas être cohérentes.</span><span class="sxs-lookup"><span data-stu-id="de22d-129">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="de22d-130">Vous pouvez l’atténuer en encapsulant les requêtes dans une transaction sérialisable ou d’instantané, même si cela peut entraîner des problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="de22d-130">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="de22d-131">Pour plus d’informations, consultez la documentation de votre base de données.</span><span class="sxs-lookup"><span data-stu-id="de22d-131">For more information, see your database's documentation.</span></span>
- <span data-ttu-id="de22d-132">Chaque requête implique actuellement un aller-retour réseau supplémentaire vers votre base de données.</span><span class="sxs-lookup"><span data-stu-id="de22d-132">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="de22d-133">Plusieurs allers-retours réseau peuvent dégrader les performances, en particulier lorsque la latence est élevée pour la base de données (par exemple, les services Cloud).</span><span class="sxs-lookup"><span data-stu-id="de22d-133">Multiple network roundtrips can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
- <span data-ttu-id="de22d-134">Bien que certaines bases de données autorisent l’utilisation des résultats de plusieurs requêtes en même temps (SQL Server avec MARS, SQLite), la plupart n’autorisent l’activation que d’une seule requête à un moment donné.</span><span class="sxs-lookup"><span data-stu-id="de22d-134">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="de22d-135">Ainsi, tous les résultats des requêtes antérieures doivent être mis en mémoire tampon dans la mémoire de votre application avant d’exécuter des requêtes ultérieures, ce qui entraîne une augmentation des besoins en mémoire.</span><span class="sxs-lookup"><span data-stu-id="de22d-135">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="de22d-136">Malheureusement, il n’existe aucune stratégie pour charger des entités associées qui s’adaptent à tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="de22d-136">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="de22d-137">Examinez attentivement les avantages et les inconvénients des requêtes simples et fractionnées pour sélectionner celle qui répond à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="de22d-137">Carefully consider the advantages and disadvantages of single and split queries to select the one that fits your needs.</span></span>
