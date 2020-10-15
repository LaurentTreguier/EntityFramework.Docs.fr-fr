---
title: Chargement explicite des données associées-EF Core
description: Chargement explicite de données associées avec Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 0cfc4b5304d81d5a58497857474804946d3f85d7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065717"
---
# <a name="explicit-loading-of-related-data"></a>Chargement explicite de données associées

## <a name="explicit-loading"></a>Chargement explicite

Vous pouvez charger explicitement une propriété de navigation via l’API `DbContext.Entry(...)`.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#Eager)]

Vous pouvez également explicitement charger une propriété de navigation en exécutant une requête distincte qui retourne les entités associées. Si le suivi des modifications est activé, lorsque la requête matérialise une entité, EF Core définira automatiquement les propriétés de navigation de l’entité nouvellement chargée pour faire référence à toutes les entités déjà chargées, et définir les propriétés de navigation des entités déjà chargées pour faire référence à l’entité qui vient d’être chargée.

## <a name="querying-related-entities"></a>Interrogation des entités associées

Vous pouvez également obtenir une requête LINQ qui représente le contenu d’une propriété de navigation.

Elle vous permet d’appliquer des opérateurs supplémentaires sur la requête. Par exemple, l’application d’un opérateur d’agrégation sur les entités associées sans les charger en mémoire.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryAggregate)]

Vous pouvez également filtrer les entités associées qui sont chargées en mémoire.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryFiltered)]
