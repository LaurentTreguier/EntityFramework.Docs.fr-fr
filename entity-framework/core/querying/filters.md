---
title: 'Filtres de requête globale : EF Core'
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: f6c59bcbab31edcbed22079a1320c060ce08c6f7
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664128"
---
# <a name="global-query-filters"></a>Filtres de requête globale

> [!NOTE]
> Cette fonctionnalité date de la publication d’EF Core 2.0.

Les filtres de requête globale permet aux prédicats de requête LINQ (expression booléenne généralement passée à l’opérateur de requête LINQ *Where*) d’être appliqués sur des types d’entité dans le modèle de métadonnées (généralement dans *OnModelCreating*). Ces filtres sont automatiquement appliqués à toutes les requêtes LINQ impliquant ces types d’entités, y compris ceux référencés indirectement, par exemple à l’aide d’Include ou de références de propriété de navigation directe. Voici deux applications courantes de cette fonctionnalité :

* **Suppression réversible** : un type d’entité définit une propriété *IsDeleted*.
* Architecture **mutualisée : un** type d’entité définit une propriété *TenantId* .

## <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser les filtres de requête globale pour implémenter des comportements de suppression réversible et d’architecture multilocataire dans un modèle de création de blogs simple.

> [!TIP]
> Vous pouvez afficher un [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) d’architecture mutualisée et des [exemples à l’aide des navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) sur GitHub. 

Tout d’abord définissez les entités :

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

Notez la déclaration d’un champ _tenantId_ sur l’entité _Blog_. Celui-ci doit servir à associer chaque instance de blog à un client spécifique. Une propriété _IsDeleted_ est également définie sur le type d’entité _Post_. Elle est utilisée pour déterminer si une instance _Post_ a été « supprimée de façon réversible ». Autrement dit, l’instance est marquée comme supprimée sans que les données sous-jacentes soient réellement supprimées.

Ensuite, configurez les filtres de requête dans _OnModelCreating_ à l’aide de l’API `HasQueryFilter`.

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

Les expressions de prédicat passées aux appels _HasQueryFilter_ seront désormais automatiquement appliquées à toutes les requêtes LINQ pour ces types.

> [!TIP]
> Notez l’utilisation d’un champ de niveau d’instance DbContext : `_tenantId` permet de définir le client en cours. Les filtres au niveau du modèle utilisent la valeur de l’instance de contexte correcte (c’est-à-dire celle qui exécute la requête).

> [!NOTE]
> Il n’est actuellement pas possible de définir plusieurs filtres de requête sur la même entité ; seul le dernier sera appliqué. Toutefois, vous pouvez définir un filtre unique avec plusieurs conditions à l’aide de l’opérateur _and_ logique ([ `&&` en C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).

## <a name="use-of-navigations"></a>Utilisation des navigations

Les navigations peuvent être utilisées lors de la définition de filtres de requête globaux. Elles sont appliquées de manière récursive : lorsque les navigations utilisées dans les filtres de requête sont traduites, les filtres de requête définis sur les entités référencées sont également appliqués, ce qui peut ajouter des navigations supplémentaires.

> [!NOTE]
> Actuellement EF Core ne détecte pas les cycles dans les définitions de filtre de requête globale. vous devez donc être prudent lorsque vous les définissez. Si elle est spécifiée de manière incorrecte, cela peut entraîner des boucles infinies lors de la traduction de la requête.

## <a name="accessing-entity-with-query-filter-using-reqiured-navigation"></a>Accès à une entité avec un filtre de requête à l’aide de la navigation reqiured

> [!CAUTION]
> L’utilisation de la navigation requise pour accéder à l’entité qui a un filtre de requête global défini peut entraîner des résultats inattendus. 

La navigation obligatoire s’attend à ce que l’entité associée soit toujours présente. Si l’entité associée requise est filtrée par le filtre de requête, l’entité parente peut se retrouver dans un état inattendu. Cela peut entraîner le retour de moins d’éléments que prévu. 

Pour illustrer le problème, nous pouvons utiliser `Blog` les `Post` entités et spécifiées ci-dessus, ainsi que la méthode _OnModelCreating_ suivante :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

Le modèle peut être amorcé avec les données suivantes :

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

Le problème peut être observé lors de l’exécution de deux requêtes :

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

Avec cette configuration, la première requête retourne tous les 6 `Post` s, mais la deuxième requête retourne uniquement 3. Cela se produit parce que la méthode _include_ de la deuxième requête charge les `Blog` entités associées. Dans la mesure où la navigation entre `Blog` et `Post` est requise, EF Core utilise `INNER JOIN` lors de la construction de la requête :

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

L’utilisation des `INNER JOIN` filtres exclut tous les `Post` s dont les `Blog` s associés ont été supprimés par un filtre de requête global. 

Vous pouvez le résoudre à l’aide de la navigation facultative au lieu de obligatoire. De cette façon, la première requête reste la même qu’avant, mais la deuxième requête génère `LEFT JOIN` et retourne 6 résultats.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

Une autre approche consiste à spécifier des filtres cohérents sur les `Blog` `Post` entités et.
De cette façon, les filtres de correspondance sont appliqués à `Blog` et à `Post` . `Post`qui pourraient finir par un état inattendu sont supprimés et les deux requêtes retournent 3 résultats. 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a>Désactivation des filtres

Les filtres peuvent être désactivés pour des requêtes LINQ individuelles à l’aide de l’opérateur `IgnoreQueryFilters()`.

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>Limites

Les filtres de requête globale présentent les limitations suivantes :

* Les filtres ne peuvent être définis que pour le type d’entité racine d’une hiérarchie d’héritage.
