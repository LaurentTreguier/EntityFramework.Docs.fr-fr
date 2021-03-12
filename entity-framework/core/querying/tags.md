---
title: Balises de requête - EF Core
description: Utilisation de balises de requête pour aider à identifier des requêtes spécifiques dans les messages de journal émis par Entity Framework Core
author: smitpatel
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: bd71b8ce1cdb33aae417a90e84861de92224daa3
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023768"
---
# <a name="query-tags"></a><span data-ttu-id="fb860-103">Balises de requête</span><span class="sxs-lookup"><span data-stu-id="fb860-103">Query tags</span></span>

<span data-ttu-id="fb860-104">Les balises de requête permettent de mettre en corrélation les requêtes LINQ dans le code avec les requêtes SQL générées capturées dans les journaux.</span><span class="sxs-lookup"><span data-stu-id="fb860-104">Query tags help correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="fb860-105">Vous annotez une requête LINQ à l’aide de la nouvelle méthode `TagWith()` :</span><span class="sxs-lookup"><span data-stu-id="fb860-105">You annotate a LINQ query using the new `TagWith()` method:</span></span>

> [!TIP]
> <span data-ttu-id="fb860-106">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/Tags) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="fb860-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/Tags) on GitHub.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

<span data-ttu-id="fb860-107">Cette requête LINQ est traduite dans l’instruction SQL suivante :</span><span class="sxs-lookup"><span data-stu-id="fb860-107">This LINQ query is translated to the following SQL statement:</span></span>

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="fb860-108">Il est possible d’appeler `TagWith()` plusieurs fois sur la même requête.</span><span class="sxs-lookup"><span data-stu-id="fb860-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="fb860-109">Les balises de requête sont cumulatives.</span><span class="sxs-lookup"><span data-stu-id="fb860-109">Query tags are cumulative.</span></span>
<span data-ttu-id="fb860-110">Par exemple, avec les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="fb860-110">For example, given the following methods:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

<span data-ttu-id="fb860-111">Cette requête :</span><span class="sxs-lookup"><span data-stu-id="fb860-111">The following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

<span data-ttu-id="fb860-112">Se traduit par :</span><span class="sxs-lookup"><span data-stu-id="fb860-112">Translates to:</span></span>

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="fb860-113">Il est également possible d’utiliser des chaînes multilignes comme balises de requête.</span><span class="sxs-lookup"><span data-stu-id="fb860-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="fb860-114">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="fb860-114">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

<span data-ttu-id="fb860-115">Génère l’instruction SQL suivante :</span><span class="sxs-lookup"><span data-stu-id="fb860-115">Produces the following SQL:</span></span>

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="fb860-116">Limitations connues</span><span class="sxs-lookup"><span data-stu-id="fb860-116">Known limitations</span></span>

<span data-ttu-id="fb860-117">**Les balises de requête ne sont pas paramétrables :** EF Core traite toujours les balises de requête dans la requête LINQ comme des opérateurs sur chaîne inclus dans l’instruction SQL générée.</span><span class="sxs-lookup"><span data-stu-id="fb860-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="fb860-118">Les requêtes compilées qui utilisent des balises de requête comme paramètres ne sont pas autorisées.</span><span class="sxs-lookup"><span data-stu-id="fb860-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
