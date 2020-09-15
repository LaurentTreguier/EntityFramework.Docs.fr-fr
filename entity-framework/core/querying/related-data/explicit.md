---
title: Chargement explicite des données associées-EF Core
description: Chargement explicite de données associées avec Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 4cfc11237b498f5357476ee4ad96fdc279cd3fee
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078952"
---
# <a name="explicit-loading-of-related-data"></a>Chargement explicite de données associées

## <a name="explicit-loading"></a>Chargement explicite

Vous pouvez charger explicitement une propriété de navigation via l’API `DbContext.Entry(...)`.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

Vous pouvez également explicitement charger une propriété de navigation en exécutant une requête distincte qui retourne les entités associées. Si le suivi des modifications est activé, lorsque la requête matérialise une entité, EF Core définira automatiquement les propriétés de navigation de l’entité nouvellement chargée pour faire référence à toutes les entités déjà chargées, et définir les propriétés de navigation des entités déjà chargées pour faire référence à l’entité qui vient d’être chargée.

## <a name="querying-related-entities"></a>Interrogation des entités associées

Vous pouvez également obtenir une requête LINQ qui représente le contenu d’une propriété de navigation.

Elle vous permet d’appliquer des opérateurs supplémentaires sur la requête. Exemple nombre application d’un opérateur d’agrégation sur les entités associées sans les charger en mémoire.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

Vous pouvez également filtrer les entités associées qui sont chargées en mémoire.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
