---
title: Chargement hâtif des données associées-EF Core
description: Chargement hâtif des données associées avec Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 66956fcd85bb21a08c69fa93b93c12382bbfc8eb
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003573"
---
# <a name="eager-loading-of-related-data"></a>Chargement hâtif des données associées

## <a name="eager-loading"></a>Chargement hâtif

Vous pouvez utiliser la méthode `Include` pour spécifier les données associées à inclure dans les résultats de la requête. Dans l’exemple suivant, les blogs retournés dans les résultats auront leurs propriétés `Posts` remplies avec les billets associés.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core corrige automatiquement les propriétés de navigation vers d’autres entités qui étaient précédemment chargées dans l’instance de contexte. Ainsi, même vous n’incluez pas explicitement si les données pour une propriété de navigation, la propriété peut toujours être renseignée si toutes ou une partie des entités associées ont été précédemment chargées.

Vous pouvez inclure des données associées provenant de plusieurs relations dans une seule requête.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> Le chargement hâtif d’une collection dans une seule requête peut entraîner des problèmes de performances. Pour plus d’informations, consultez [requêtes Single et Split](xref:core/querying/single-split-queries).

## <a name="including-multiple-levels"></a>Inclusion de plusieurs niveaux

Vous pouvez descendre dans la hiérarchie des relations pour inclure plusieurs niveaux de données associées à l’aide de la méthode `ThenInclude`. L’exemple suivant charge tous les blogs, leurs messages associés et l’auteur de chaque publication.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

Vous pouvez enchaîner plusieurs appels à `ThenInclude` pour continuer à inclure les niveaux de données associées suivants.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

Vous pouvez combiner tous les appels pour inclure les données associées de plusieurs niveaux et plusieurs racines dans la même requête.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

Vous pourriez souhaiter inclure plusieurs entités associées pour une entité qui est incluse. Par exemple, quand vous interrogez des `Blogs`, vous incluez `Posts`, puis souhaitez inclure à la fois les `Author` et les `Tags` des `Posts`. Pour inclure les deux, vous devez spécifier chaque chemin d’accès include commençant à la racine. Par exemple : `Blog -> Posts -> Author` et `Blog -> Posts -> Tags`. Cela ne signifie pas que vous obtiendrez des jointures redondantes. dans la plupart des cas, EF combinera les jointures lors de la génération de SQL.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

> [!TIP]
> Vous pouvez également charger plusieurs navigations à l’aide d’une `Include` méthode unique. Cela est possible pour les « chaînes » de navigation qui sont toutes des références, ou lorsqu’elles se terminent par une collection unique.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeMultipleNavigationsWithSingleInclude)]

## <a name="filtered-include"></a>Include filtré

> [!NOTE]
> Cette fonctionnalité a été introduite dans EF Core 5,0.

Lorsque vous appliquez include pour charger des données associées, vous pouvez ajouter certaines opérations énumérables à la navigation de collection incluse, ce qui permet de filtrer et de trier les résultats.

Les opérations prises en charge sont les suivantes : `Where` , `OrderBy` ,,,, `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` et `Take` .

Ces opérations doivent être appliquées sur la navigation dans la collection dans l’expression lambda passée à la méthode Include, comme illustré dans l’exemple ci-dessous :

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

Chaque navigation incluse n’autorise qu’un seul ensemble d’opérations de filtre. Dans les cas où plusieurs opérations include sont appliquées pour une navigation de collection donnée ( `blog.Posts` dans les exemples ci-dessous), les opérations de filtre peuvent uniquement être spécifiées sur l’une d’entre elles :

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

Au lieu de cela, des opérations identiques peuvent être appliquées pour chaque navigation qui est incluse plusieurs fois :

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> En cas de requêtes de suivi, les résultats de l’inclusion filtrée peuvent être inattendus en raison de la correction de la [navigation](xref:core/querying/tracking). Toutes les entités pertinentes qui ont été interrogées précédemment et qui ont été stockées dans le dispositif de suivi des modifications sont présentes dans les résultats de la requête include filtrée, même si elles ne répondent pas aux exigences du filtre. Envisagez d’utiliser des `NoTracking` requêtes ou de recréer DbContext lors de l’utilisation de l’inclusion filtrée dans ces situations.

Exemple :

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

> [!NOTE]
> En cas de requêtes de suivi, la navigation sur laquelle l’inclusion filtrée a été appliquée est considérée comme étant chargée. Cela signifie que EF Core n’essaiera pas de recharger ses valeurs à l’aide d’un chargement [explicite](xref:core/querying/related-data/explicit) ou d’un [chargement différé](xref:core/querying/related-data/lazy), même si certains éléments peuvent toujours être manquants.

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
