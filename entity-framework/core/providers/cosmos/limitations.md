---
title: Fournisseur de Azure Cosmos DB-limitations-EF Core
description: Limitations du fournisseur Entity Framework Core Azure Cosmos DB par rapport à d’autres fournisseurs
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: e4d1c38da14d1e722797f8543313a69c7fb088cc
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064035"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>Limitations des fournisseurs de EF Core Azure Cosmos DB

Le fournisseur Cosmos présente un certain nombre de limitations. La plupart de ces limitations résultent de limitations dans le moteur de base de données Cosmos sous-jacent et ne sont pas spécifiques à EF. Mais la plupart [n’ont pas encore été implémentées](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).

## <a name="temporary-limitations"></a>Limitations temporaires

- Même s’il existe un seul type d’entité sans héritage mappé à un conteneur, il possède toujours une propriété de discriminateur.
- Les types d’entités avec des clés de partition ne fonctionnent pas correctement dans certains scénarios
- `Include` les appels ne sont pas pris en charge
- `Join` les appels ne sont pas pris en charge

## <a name="azure-cosmos-db-sdk-limitations"></a>Limitations du kit de développement logiciel Azure Cosmos DB

- Seules les méthodes Async sont fournies

> [!WARNING]
> Étant donné qu’il n’existe aucune version de synchronisation des méthodes de bas niveau EF Core s’appuie sur, la fonctionnalité correspondante est actuellement implémentée en appelant `.Wait()` sur le retourné `Task` . Cela signifie que l’utilisation de méthodes telles que `SaveChanges` , ou à `ToList` la place de leurs équivalents Async, peut entraîner un blocage dans votre application

## <a name="azure-cosmos-db-limitations"></a>Limitations de Azure Cosmos DB

Vous pouvez voir la vue d’ensemble complète des [fonctionnalités prises en charge par Azure Cosmos DB](/azure/cosmos-db/modeling-data). il s’agit des différences les plus notables par rapport à une base de données relationnelle :

- Les transactions initiées par le client ne sont pas prises en charge
- Certaines requêtes de plusieurs partitions ne sont pas prises en charge ou sont beaucoup plus lentes en fonction des opérateurs impliqués
