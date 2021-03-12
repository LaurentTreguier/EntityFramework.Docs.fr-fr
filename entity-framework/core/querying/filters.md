---
title: 'Filtres de requête globale : EF Core'
description: Utilisation de filtres de requête globaux pour filtrer les résultats avec Entity Framework Core
author: maumar
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: d28f34cd3846203675b5a03343e0211460797b0a
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023742"
---
# <a name="global-query-filters"></a>Filtres de requête globale

Les filtres de requête globaux sont des prédicats de requête LINQ appliqués aux types d’entité dans le modèle de métadonnées (généralement dans `OnModelCreating` ). Un prédicat de requête est une expression booléenne qui est généralement passée à l' `Where` opérateur de requête LINQ.  EF Core applique ces filtres automatiquement à toutes les requêtes LINQ impliquant ces types d’entités.  EF Core les applique également aux types d’entités, référencés indirectement par le biais de l’utilisation de la propriété include ou de navigation. Voici deux applications courantes de cette fonctionnalité :

* **Suppression réversible** : un type d’entité définit une `IsDeleted` propriété.
* Architecture **mutualisée : un** type d’entité définit une `TenantId` propriété.

## <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser des filtres de requête globaux pour implémenter des comportements de requête d’architecture mutualisée et de suppression réversible dans un modèle de blog simple.

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/QueryFilters) sur GitHub.

Tout d’abord définissez les entités :

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Entities.cs#Entities)]

Notez la déclaration d’un `_tenantId` champ sur l' `Blog` entité. Ce champ sera utilisé pour associer chaque instance de blog à un locataire spécifique. Également défini est une `IsDeleted` propriété sur le `Post` type d’entité. Cette propriété est utilisée pour suivre si une instance de publication a été « supprimée de manière réversible ». Autrement dit, l’instance est marquée comme supprimée sans que les données sous-jacentes soient réellement supprimées.

Ensuite, configurez les filtres de requête dans `OnModelCreating` à l’aide de l' `HasQueryFilter` API.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/BloggingContext.cs#FilterConfiguration)]

Les expressions de prédicat transmises aux `HasQueryFilter` appels seront désormais automatiquement appliquées à toutes les requêtes LINQ pour ces types.

> [!TIP]
> Notez l’utilisation d’un champ de niveau d’instance DbContext : `_tenantId` permet de définir le client en cours. Les filtres au niveau du modèle utilisent la valeur de l’instance de contexte correcte (c’est-à-dire celle qui exécute la requête).

> [!NOTE]
> Il n’est actuellement pas possible de définir plusieurs filtres de requête sur la même entité ; seul le dernier sera appliqué. Toutefois, vous pouvez définir un filtre unique avec plusieurs conditions à l’aide de l' `AND` opérateur logique ([ `&&` en C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).

## <a name="use-of-navigations"></a>Utilisation des navigations

Vous pouvez également utiliser des navigations dans la définition de filtres de requête globaux. L’utilisation de navigations dans le filtre de requête entraîne l’application de filtres de requête de manière récursive. Lorsque EF Core développe les navigations utilisées dans les filtres de requête, il applique également des filtres de requête définis sur les entités référencées.

Pour illustrer cela, configurez les filtres de requête dans `OnModelCreating` de la manière suivante : [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#NavigationInFilter)]

Ensuite, interrogez toutes les `Blog` entités : [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#QueriesNavigation)]

Cette requête produit le code SQL suivant, qui applique des filtres de requête définis pour les `Blog` `Post` entités et :

```sql
SELECT [b].[BlogId], [b].[Name], [b].[Url]
FROM [Blogs] AS [b]
WHERE (
    SELECT COUNT(*)
    FROM [Posts] AS [p]
    WHERE ([p].[Title] LIKE N'%fish%') AND ([b].[BlogId] = [p].[BlogId])) > 0
```

> [!NOTE]
> Actuellement EF Core ne détecte pas les cycles dans les définitions de filtre de requête globale. vous devez donc être prudent lorsque vous les définissez. S’ils sont spécifiés de manière incorrecte, les cycles peuvent entraîner des boucles infinies lors de la traduction de la requête.

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a>Accès à une entité avec un filtre de requête à l’aide de la navigation requise

> [!CAUTION]
> L’utilisation de la navigation requise pour accéder à l’entité qui a un filtre de requête global défini peut entraîner des résultats inattendus.

La navigation obligatoire s’attend à ce que l’entité associée soit toujours présente. Si l’entité connexe nécessaire est filtrée par le filtre de requête, l’entité parente n’est pas dans le résultat. Vous risquez de recevoir moins d’éléments que prévu dans le résultat.

Pour illustrer le problème, nous pouvons utiliser `Blog` les `Post` entités et spécifiées ci-dessus, ainsi que la `OnModelCreating` méthode suivante :

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#IncorrectFilter)]

Le modèle peut être amorcé avec les données suivantes :

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#SeedData)]

Le problème peut être observé lors de l’exécution de deux requêtes :

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#Queries)]

Avec le programme d’installation ci-dessus, la première requête retourne tous les 6 `Post` s, mais la deuxième requête retourne uniquement 3. Cette incompatibilité se produit parce que `Include` la méthode de la deuxième requête charge les `Blog` entités associées. Dans la mesure où la navigation entre `Blog` et `Post` est requise, EF Core utilise `INNER JOIN` lors de la construction de la requête :

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Posts] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE [b].[Url] LIKE N'%fish%'
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

L’utilisation des `INNER JOIN` filtres exclut tous les `Post` s dont les `Blog` s associés ont été supprimés par un filtre de requête global.

Vous pouvez le résoudre à l’aide de la navigation facultative au lieu de obligatoire.
De cette façon, la première requête reste la même qu’avant, mais la deuxième requête génère `LEFT JOIN` et retourne 6 résultats.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#OptionalNavigation)]

Une autre approche consiste à spécifier des filtres cohérents sur les `Blog` `Post` entités et.
De cette façon, les filtres de correspondance sont appliqués à `Blog` et à `Post` . `Post`qui pourraient finir par un état inattendu sont supprimés et les deux requêtes retournent 3 résultats.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#MatchingFilters)]

## <a name="disabling-filters"></a>Désactivation des filtres

Les filtres peuvent être désactivés pour des requêtes LINQ individuelles à l’aide de l’opérateur <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A>.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>Limites

Les filtres de requête globale présentent les limitations suivantes :

* Les filtres ne peuvent être définis que pour le type d’entité racine d’une hiérarchie d’héritage.
