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
# <a name="explicit-loading-of-related-data"></a><span data-ttu-id="c4879-103">Chargement explicite de données associées</span><span class="sxs-lookup"><span data-stu-id="c4879-103">Explicit Loading of Related Data</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="c4879-104">Chargement explicite</span><span class="sxs-lookup"><span data-stu-id="c4879-104">Explicit loading</span></span>

<span data-ttu-id="c4879-105">Vous pouvez charger explicitement une propriété de navigation via l’API `DbContext.Entry(...)`.</span><span class="sxs-lookup"><span data-stu-id="c4879-105">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#Eager)]

<span data-ttu-id="c4879-106">Vous pouvez également explicitement charger une propriété de navigation en exécutant une requête distincte qui retourne les entités associées.</span><span class="sxs-lookup"><span data-stu-id="c4879-106">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="c4879-107">Si le suivi des modifications est activé, lorsque la requête matérialise une entité, EF Core définira automatiquement les propriétés de navigation de l’entité nouvellement chargée pour faire référence à toutes les entités déjà chargées, et définir les propriétés de navigation des entités déjà chargées pour faire référence à l’entité qui vient d’être chargée.</span><span class="sxs-lookup"><span data-stu-id="c4879-107">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

## <a name="querying-related-entities"></a><span data-ttu-id="c4879-108">Interrogation des entités associées</span><span class="sxs-lookup"><span data-stu-id="c4879-108">Querying related entities</span></span>

<span data-ttu-id="c4879-109">Vous pouvez également obtenir une requête LINQ qui représente le contenu d’une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="c4879-109">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="c4879-110">Elle vous permet d’appliquer des opérateurs supplémentaires sur la requête.</span><span class="sxs-lookup"><span data-stu-id="c4879-110">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="c4879-111">Par exemple, l’application d’un opérateur d’agrégation sur les entités associées sans les charger en mémoire.</span><span class="sxs-lookup"><span data-stu-id="c4879-111">For example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryAggregate)]

<span data-ttu-id="c4879-112">Vous pouvez également filtrer les entités associées qui sont chargées en mémoire.</span><span class="sxs-lookup"><span data-stu-id="c4879-112">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryFiltered)]
