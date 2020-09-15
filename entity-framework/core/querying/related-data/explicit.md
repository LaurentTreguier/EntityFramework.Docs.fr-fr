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
# <a name="explicit-loading-of-related-data"></a><span data-ttu-id="b401b-103">Chargement explicite de données associées</span><span class="sxs-lookup"><span data-stu-id="b401b-103">Explicit Loading of Related Data</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b401b-104">Chargement explicite</span><span class="sxs-lookup"><span data-stu-id="b401b-104">Explicit loading</span></span>

<span data-ttu-id="b401b-105">Vous pouvez charger explicitement une propriété de navigation via l’API `DbContext.Entry(...)`.</span><span class="sxs-lookup"><span data-stu-id="b401b-105">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="b401b-106">Vous pouvez également explicitement charger une propriété de navigation en exécutant une requête distincte qui retourne les entités associées.</span><span class="sxs-lookup"><span data-stu-id="b401b-106">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="b401b-107">Si le suivi des modifications est activé, lorsque la requête matérialise une entité, EF Core définira automatiquement les propriétés de navigation de l’entité nouvellement chargée pour faire référence à toutes les entités déjà chargées, et définir les propriétés de navigation des entités déjà chargées pour faire référence à l’entité qui vient d’être chargée.</span><span class="sxs-lookup"><span data-stu-id="b401b-107">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

## <a name="querying-related-entities"></a><span data-ttu-id="b401b-108">Interrogation des entités associées</span><span class="sxs-lookup"><span data-stu-id="b401b-108">Querying related entities</span></span>

<span data-ttu-id="b401b-109">Vous pouvez également obtenir une requête LINQ qui représente le contenu d’une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="b401b-109">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="b401b-110">Elle vous permet d’appliquer des opérateurs supplémentaires sur la requête.</span><span class="sxs-lookup"><span data-stu-id="b401b-110">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="b401b-111">Exemple nombre application d’un opérateur d’agrégation sur les entités associées sans les charger en mémoire.</span><span class="sxs-lookup"><span data-stu-id="b401b-111">Foe example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="b401b-112">Vous pouvez également filtrer les entités associées qui sont chargées en mémoire.</span><span class="sxs-lookup"><span data-stu-id="b401b-112">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
