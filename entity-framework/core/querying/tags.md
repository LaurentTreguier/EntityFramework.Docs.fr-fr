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
# <a name="query-tags"></a>Balises de requête

> [!NOTE]
> Cette fonctionnalité est une nouveauté d’EF Core 2.2.

Cette fonctionnalité simplifie la corrélation des requêtes LINQ dans le code avec des requêtes SQL générées et capturées dans des journaux.
Vous annotez une requête LINQ à l’aide de la nouvelle méthode `TagWith()` :

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Cette requête LINQ est traduite dans l’instruction SQL suivante :

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

Il est possible d’appeler `TagWith()` plusieurs fois sur la même requête.
Les balises de requête sont cumulatives.
Par exemple, avec les méthodes suivantes :

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

Cette requête :

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

Se traduit par :

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

Il est également possible d’utiliser des chaînes multilignes comme balises de requête.
Exemple :

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

Génère l’instruction SQL suivante :

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>Limitations connues

**Les balises de requête ne sont pas paramétrables :** EF Core traite toujours les balises de requête dans la requête LINQ comme des opérateurs sur chaîne inclus dans l’instruction SQL générée.
Les requêtes compilées qui utilisent des balises de requête comme paramètres ne sont pas autorisées.
