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
# <a name="query-tags"></a>Balises de requête

Les balises de requête permettent de mettre en corrélation les requêtes LINQ dans le code avec les requêtes SQL générées capturées dans les journaux.
Vous annotez une requête LINQ à l’aide de la nouvelle méthode `TagWith()` :

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/Tags) sur GitHub.

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

Cette requête LINQ est traduite dans l’instruction SQL suivante :

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

Il est possible d’appeler `TagWith()` plusieurs fois sur la même requête.
Les balises de requête sont cumulatives.
Par exemple, avec les méthodes suivantes :

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

Cette requête :

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

Se traduit par :

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

Il est également possible d’utiliser des chaînes multilignes comme balises de requête.
Par exemple :

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

Génère l’instruction SQL suivante :

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>Limitations connues

**Les balises de requête ne sont pas paramétrables :** EF Core traite toujours les balises de requête dans la requête LINQ comme des opérateurs sur chaîne inclus dans l’instruction SQL générée.
Les requêtes compilées qui utilisent des balises de requête comme paramètres ne sont pas autorisées.
