---
title: Balises de requête - EF Core
description: Utilisation de balises de requête pour aider à identifier des requêtes spécifiques dans les messages de journal émis par Entity Framework Core
author: divega
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: 27f757f4159a36bec324cce56d74b7860e1c3741
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070989"
---
# <a name="query-tags"></a><span data-ttu-id="22c46-103">Balises de requête</span><span class="sxs-lookup"><span data-stu-id="22c46-103">Query tags</span></span>

> [!NOTE]
> <span data-ttu-id="22c46-104">Cette fonctionnalité est une nouveauté d’EF Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="22c46-104">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="22c46-105">Cette fonctionnalité simplifie la corrélation des requêtes LINQ dans le code avec des requêtes SQL générées et capturées dans des journaux.</span><span class="sxs-lookup"><span data-stu-id="22c46-105">This feature helps correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="22c46-106">Vous annotez une requête LINQ à l’aide de la nouvelle méthode `TagWith()` :</span><span class="sxs-lookup"><span data-stu-id="22c46-106">You annotate a LINQ query using the new `TagWith()` method:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="22c46-107">Cette requête LINQ est traduite dans l’instruction SQL suivante :</span><span class="sxs-lookup"><span data-stu-id="22c46-107">This LINQ query is translated to the following SQL statement:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="22c46-108">Il est possible d’appeler `TagWith()` plusieurs fois sur la même requête.</span><span class="sxs-lookup"><span data-stu-id="22c46-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="22c46-109">Les balises de requête sont cumulatives.</span><span class="sxs-lookup"><span data-stu-id="22c46-109">Query tags are cumulative.</span></span>
<span data-ttu-id="22c46-110">Par exemple, avec les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="22c46-110">For example, given the following methods:</span></span>

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

<span data-ttu-id="22c46-111">Cette requête :</span><span class="sxs-lookup"><span data-stu-id="22c46-111">The following query:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

<span data-ttu-id="22c46-112">Se traduit par :</span><span class="sxs-lookup"><span data-stu-id="22c46-112">Translates to:</span></span>

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="22c46-113">Il est également possible d’utiliser des chaînes multilignes comme balises de requête.</span><span class="sxs-lookup"><span data-stu-id="22c46-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="22c46-114">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22c46-114">For example:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

<span data-ttu-id="22c46-115">Génère l’instruction SQL suivante :</span><span class="sxs-lookup"><span data-stu-id="22c46-115">Produces the following SQL:</span></span>

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="22c46-116">Limitations connues</span><span class="sxs-lookup"><span data-stu-id="22c46-116">Known limitations</span></span>

<span data-ttu-id="22c46-117">**Les balises de requête ne sont pas paramétrables :** EF Core traite toujours les balises de requête dans la requête LINQ comme des opérateurs sur chaîne inclus dans l’instruction SQL générée.</span><span class="sxs-lookup"><span data-stu-id="22c46-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="22c46-118">Les requêtes compilées qui utilisent des balises de requête comme paramètres ne sont pas autorisées.</span><span class="sxs-lookup"><span data-stu-id="22c46-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
