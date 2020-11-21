---
title: Requêtes Single et Split-EF Core
description: Traduction de la requête en requêtes Single et Split dans SQL avec Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: ba282a0c5242b2eb87d681906571036d4751f6ac
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003560"
---
# <a name="single-vs-split-queries"></a>Requêtes Single et Split

## <a name="single-queries"></a>Requêtes uniques

Dans les bases de données relationnelles, toutes les entités associées sont chargées en introduisant des JOINTUREs dans une requête unique.

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Si un blog classique contient plusieurs publications connexes, les lignes de ces publications dupliqueront les informations du blog. Cette duplication porte sur le problème de « explosion cartésien ». À mesure que des relations un-à-plusieurs sont chargées, la quantité de données dupliquées peut croître et nuire aux performances de votre application.

## <a name="split-queries"></a>Fractionner des requêtes

> [!NOTE]
> Cette fonctionnalité a été introduite dans EF Core 5,0. Elle fonctionne uniquement lorsque vous utilisez `Include` . [Ce problème concerne le](https://github.com/dotnet/efcore/issues/21234) suivi de la prise en charge des requêtes fractionnées lors du chargement des données associées dans la projection sans `Include` .

EF vous permet de spécifier qu’une requête LINQ donnée doit être *fractionnée* en plusieurs requêtes SQL. Au lieu de JOINTUREs, les requêtes Split génèrent une requête SQL supplémentaire pour chaque navigation dans la collection incluse :

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

Il produira le code SQL suivant :

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
> Les entités associées à un-à-un sont toujours chargées via des JOINTUREs dans la même requête, car elle n’a aucun impact sur les performances.

## <a name="enabling-split-queries-globally"></a>Activation globale des requêtes fractionnées

Vous pouvez également configurer des requêtes fractionnées comme valeur par défaut pour le contexte de votre application :

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

Lorsque les requêtes de fractionnement sont configurées en tant que valeurs par défaut, il est toujours possible de configurer des requêtes spécifiques pour qu’elles s’exécutent en tant que requêtes uniques :

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

EF Core utilise le mode de requête unique par défaut en l’absence de configuration. Dans la mesure où cela peut entraîner des problèmes de performances, EF Core génère un avertissement chaque fois que les conditions suivantes sont remplies :

- EF Core détecte que la requête charge plusieurs collections.
- L’utilisateur n’a pas configuré le mode de fractionnement de requête globalement.
- L’utilisateur n’a pas utilisé `AsSingleQuery` / `AsSplitQuery` l’opérateur sur la requête.

Pour désactiver l’avertissement, configurez le mode de fractionnement des requêtes globalement ou au niveau de la requête avec une valeur appropriée.

## <a name="characteristics-of-split-queries"></a>Caractéristiques des requêtes fractionnées

Si la requête Split évite les problèmes de performances associés aux JOINTUREs et à l’explosion cartésienne, elle présente également certains inconvénients :

- Alors que la plupart des bases de données garantissent la cohérence des données pour les requêtes uniques, il n’existe pas de garantie de ce type pour plusieurs requêtes. Si la base de données est mise à jour en même temps lors de l’exécution de vos requêtes, les données résultantes peuvent ne pas être cohérentes. Vous pouvez l’atténuer en encapsulant les requêtes dans une transaction sérialisable ou d’instantané, même si cela peut entraîner des problèmes de performances. Pour plus d’informations, consultez la documentation de votre base de données.
- Chaque requête implique actuellement un aller-retour réseau supplémentaire vers votre base de données. Plusieurs allers-retours sur le réseau peuvent dégrader les performances, notamment lorsque la latence est élevée pour la base de données (par exemple, les services Cloud).
- Bien que certaines bases de données autorisent l’utilisation des résultats de plusieurs requêtes en même temps (SQL Server avec MARS, SQLite), la plupart n’autorisent l’activation que d’une seule requête à un moment donné. Ainsi, tous les résultats des requêtes antérieures doivent être mis en mémoire tampon dans la mémoire de votre application avant d’exécuter des requêtes ultérieures, ce qui entraîne une augmentation des besoins en mémoire.

Malheureusement, il n’existe aucune stratégie pour charger des entités associées qui s’adaptent à tous les scénarios. Examinez attentivement les avantages et les inconvénients des requêtes simples et fractionnées pour sélectionner celle qui répond à vos besoins.
