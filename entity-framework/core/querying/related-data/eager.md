---
title: Chargement hâtif des données associées-EF Core
description: Chargement hâtif des données associées avec Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 97ec45a0f8bfecce4d4a59e5d1c36c0268d96052
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062574"
---
# <a name="eager-loading-of-related-data"></a>Chargement hâtif des données associées

## <a name="eager-loading"></a>Chargement hâtif

Vous pouvez utiliser la méthode `Include` pour spécifier les données associées à inclure dans les résultats de la requête. Dans l’exemple suivant, les blogs retournés dans les résultats auront leurs propriétés `Posts` remplies avec les billets associés.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core corrige automatiquement les propriétés de navigation vers d’autres entités qui étaient précédemment chargées dans l’instance de contexte. Ainsi, même vous n’incluez pas explicitement si les données pour une propriété de navigation, la propriété peut toujours être renseignée si toutes ou une partie des entités associées ont été précédemment chargées.

Vous pouvez inclure des données associées provenant de plusieurs relations dans une seule requête.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a>Inclusion de plusieurs niveaux

Vous pouvez descendre dans la hiérarchie des relations pour inclure plusieurs niveaux de données associées à l’aide de la méthode `ThenInclude`. L’exemple suivant charge tous les blogs, leurs messages associés et l’auteur de chaque publication.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

Vous pouvez enchaîner plusieurs appels à `ThenInclude` pour continuer à inclure les niveaux de données associées suivants.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

Vous pouvez combiner tous les appels pour inclure les données associées de plusieurs niveaux et plusieurs racines dans la même requête.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

Vous pourriez souhaiter inclure plusieurs entités associées pour une entité qui est incluse. Par exemple, quand vous interrogez des `Blogs`, vous incluez `Posts`, puis souhaitez inclure à la fois les `Author` et les `Tags` des `Posts`. Pour inclure les deux, vous devez spécifier chaque chemin d’accès include commençant à la racine. Par exemple, `Blog -> Posts -> Author` et `Blog -> Posts -> Tags`. Cela ne signifie pas que vous obtiendrez des jointures redondantes. dans la plupart des cas, EF combinera les jointures lors de la génération de SQL.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a>Requêtes simples et fractionnées

### <a name="single-queries"></a>Requêtes uniques

Dans les bases de données relationnelles, toutes les entités associées sont chargées par défaut en introduisant des JOINTUREs :

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Si un blog classique contient plusieurs publications connexes, les lignes de ces publications dupliqueront les informations du blog, ce qui entraînerait un problème de « explosion cartésien ». À mesure que des relations un-à-plusieurs sont chargées, la quantité de données dupliquées peut croître et nuire aux performances de votre application. Par défaut, EF Core émet un avertissement s’il détecte des requêtes qui chargent la collection, notamment celles qui peuvent entraîner des problèmes de performances.

### <a name="split-queries"></a>Fractionner les requêtes

> [!NOTE]
> Cette fonctionnalité est introduite dans EF Core 5,0.

EF vous permet de spécifier qu’une requête LINQ donnée doit être *fractionnée* en plusieurs requêtes SQL. Au lieu de JOINTUREs, les requêtes Split effectuent une requête SQL supplémentaire pour chaque navigation un-à-plusieurs incluse :

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

Il produira le code SQL suivant :

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> Les entités associées à un-à-un sont toujours chargées via des JOINTUREs dans la même requête, car cela n’a aucun impact sur les performances.

### <a name="enabling-split-queries-globally"></a>Activation globale des requêtes fractionnées

Vous pouvez également configurer des requêtes fractionnées comme valeur par défaut pour le contexte de votre application :

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

Lorsque les requêtes de fractionnement sont configurées en tant que valeurs par défaut, il est toujours possible de configurer des requêtes spécifiques pour qu’elles s’exécutent en tant que requêtes uniques :

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

Si le mode de fractionnement de requête n’est pas explicitement spécifié, ni globalement, ni sur la requête, et EF Core détecte qu’une seule requête charge plusieurs collections includes, un avertissement est émis pour attirer l’attention sur les problèmes de performances potentiels résultants. Le fait de définir le mode de requête sur SingleQuery entraîne la non-génération de l’avertissement.

### <a name="characteristics-of-split-queries"></a>Caractéristiques des requêtes fractionnées

Si la requête Split évite les problèmes de performances associés aux JOINTUREs et à l’explosion cartésienne, elle présente également certains inconvénients :

* Alors que la plupart des bases de données garantissent la cohérence des données pour les requêtes uniques, il n’existe pas de garantie de ce type pour plusieurs requêtes. Si la base de données est mise à jour en même temps lors de l’exécution de vos requêtes, les données résultantes peuvent ne pas être cohérentes. Vous pouvez l’atténuer en encapsulant les requêtes dans une transaction sérialisable ou d’instantané, même si cela peut entraîner des problèmes de performances. Pour plus d’informations, consultez la documentation de votre base de données.
* Chaque requête implique actuellement un aller-retour réseau supplémentaire vers votre base de données. Plusieurs allers-retours sur le réseau peuvent dégrader les performances, notamment lorsque la latence est élevée pour la base de données (par exemple, les services Cloud).
* Bien que certaines bases de données autorisent l’utilisation des résultats de plusieurs requêtes en même temps (SQL Server avec MARS, SQLite), la plupart n’autorisent l’activation que d’une seule requête à un moment donné. Ainsi, tous les résultats des requêtes antérieures doivent être mis en mémoire tampon dans la mémoire de votre application avant d’exécuter des requêtes ultérieures, ce qui entraîne une augmentation des besoins en mémoire.

Malheureusement, il n’existe aucune stratégie pour charger des entités associées qui s’adaptent à tous les scénarios. Examinez attentivement les avantages et les inconvénients des requêtes simples et fractionnées, puis sélectionnez celle qui répond à vos besoins.

## <a name="filtered-include"></a>Include filtré

> [!NOTE]
> Cette fonctionnalité est introduite dans EF Core 5,0.

Lorsque vous appliquez include pour charger des données associées, vous pouvez appliquer certaines opérations énumérables sur la navigation de collection incluse, ce qui permet de filtrer et de trier les résultats.

Les opérations prises en charge sont les suivantes : `Where` , `OrderBy` ,,,, `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` et `Take` .

Ces opérations doivent être appliquées sur la navigation dans la collection dans l’expression lambda passée à la méthode Include, comme illustré dans l’exemple ci-dessous :

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

Chaque navigation incluse n’autorise qu’un seul ensemble d’opérations de filtre. Dans les cas où plusieurs opérations include sont appliquées pour une navigation de collection donnée ( `blog.Posts` dans les exemples ci-dessous), les opérations de filtre peuvent uniquement être spécifiées sur l’une d’entre elles :

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

Au lieu de cela, des opérations identiques peuvent être appliquées pour chaque navigation qui est incluse plusieurs fois :

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> En cas de requêtes de suivi, les résultats de l’inclusion filtrée peuvent être inattendus en raison de la correction de la [navigation](xref:core/querying/tracking). Toutes les entités pertinentes qui ont été interrogées précédemment et qui ont été stockées dans le dispositif de suivi des modifications sont présentes dans les résultats de la requête include filtrée, même si elles ne répondent pas aux exigences du filtre. Envisagez d’utiliser des `NoTracking` requêtes ou de recréer DbContext lors de l’utilisation de l’inclusion filtrée dans ces situations.

Exemple :

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a>Inclure des types dérivés

Vous pouvez inclure des données associées de la navigation définies uniquement sur un type dérivé à l’aide `Include` de et de `ThenInclude` .

En partant du modèle suivant :

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

Le contenu de la navigation `School` de toutes les personnes qui sont des étudiants peut être chargé dynamiquement à l’aide d’un certain nombre de modèles :

* utilisation du forçage de type

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* utilisation de l’opérateur `as`

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* utilisation de la surcharge de `Include` qui accepte les paramètres de type `string`

  ```csharp
  context.People.Include("School").ToList()
  ```
