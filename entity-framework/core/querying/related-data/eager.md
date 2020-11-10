---
title: Chargement hâtif des données associées-EF Core
description: Chargement hâtif des données associées avec Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: bd9c9045c1c2707d69ee4070bea59ad8066789f3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430103"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="21be4-103">Chargement hâtif des données associées</span><span class="sxs-lookup"><span data-stu-id="21be4-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="21be4-104">Chargement hâtif</span><span class="sxs-lookup"><span data-stu-id="21be4-104">Eager loading</span></span>

<span data-ttu-id="21be4-105">Vous pouvez utiliser la méthode `Include` pour spécifier les données associées à inclure dans les résultats de la requête.</span><span class="sxs-lookup"><span data-stu-id="21be4-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="21be4-106">Dans l’exemple suivant, les blogs retournés dans les résultats auront leurs propriétés `Posts` remplies avec les billets associés.</span><span class="sxs-lookup"><span data-stu-id="21be4-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="21be4-107">Entity Framework Core corrige automatiquement les propriétés de navigation vers d’autres entités qui étaient précédemment chargées dans l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="21be4-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="21be4-108">Ainsi, même vous n’incluez pas explicitement si les données pour une propriété de navigation, la propriété peut toujours être renseignée si toutes ou une partie des entités associées ont été précédemment chargées.</span><span class="sxs-lookup"><span data-stu-id="21be4-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="21be4-109">Vous pouvez inclure des données associées provenant de plusieurs relations dans une seule requête.</span><span class="sxs-lookup"><span data-stu-id="21be4-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> <span data-ttu-id="21be4-110">Le chargement hâtif d’une collection dans une seule requête peut entraîner des problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="21be4-110">Eager loading a collection navigation in a single query may cause performance issues.</span></span> <span data-ttu-id="21be4-111">Pour plus d’informations, consultez [requêtes Single et Split](xref:core/querying/single-split-queries).</span><span class="sxs-lookup"><span data-stu-id="21be4-111">For more information, see [Single vs. split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="including-multiple-levels"></a><span data-ttu-id="21be4-112">Inclusion de plusieurs niveaux</span><span class="sxs-lookup"><span data-stu-id="21be4-112">Including multiple levels</span></span>

<span data-ttu-id="21be4-113">Vous pouvez descendre dans la hiérarchie des relations pour inclure plusieurs niveaux de données associées à l’aide de la méthode `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="21be4-113">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="21be4-114">L’exemple suivant charge tous les blogs, leurs messages associés et l’auteur de chaque publication.</span><span class="sxs-lookup"><span data-stu-id="21be4-114">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

<span data-ttu-id="21be4-115">Vous pouvez enchaîner plusieurs appels à `ThenInclude` pour continuer à inclure les niveaux de données associées suivants.</span><span class="sxs-lookup"><span data-stu-id="21be4-115">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

<span data-ttu-id="21be4-116">Vous pouvez combiner tous les appels pour inclure les données associées de plusieurs niveaux et plusieurs racines dans la même requête.</span><span class="sxs-lookup"><span data-stu-id="21be4-116">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

<span data-ttu-id="21be4-117">Vous pourriez souhaiter inclure plusieurs entités associées pour une entité qui est incluse.</span><span class="sxs-lookup"><span data-stu-id="21be4-117">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="21be4-118">Par exemple, quand vous interrogez des `Blogs`, vous incluez `Posts`, puis souhaitez inclure à la fois les `Author` et les `Tags` des `Posts`.</span><span class="sxs-lookup"><span data-stu-id="21be4-118">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="21be4-119">Pour inclure les deux, vous devez spécifier chaque chemin d’accès include commençant à la racine.</span><span class="sxs-lookup"><span data-stu-id="21be4-119">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="21be4-120">Par exemple : `Blog -> Posts -> Author` et `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="21be4-120">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="21be4-121">Cela ne signifie pas que vous obtiendrez des jointures redondantes. dans la plupart des cas, EF combinera les jointures lors de la génération de SQL.</span><span class="sxs-lookup"><span data-stu-id="21be4-121">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="filtered-include"></a><span data-ttu-id="21be4-122">Include filtré</span><span class="sxs-lookup"><span data-stu-id="21be4-122">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="21be4-123">Cette fonctionnalité est introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="21be4-123">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="21be4-124">Lorsque vous appliquez include pour charger des données associées, vous pouvez ajouter certaines opérations énumérables à la navigation de collection incluse, ce qui permet de filtrer et de trier les résultats.</span><span class="sxs-lookup"><span data-stu-id="21be4-124">When applying Include to load related data, you can add certain enumerable operations to the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="21be4-125">Les opérations prises en charge sont les suivantes : `Where` , `OrderBy` ,,,, `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` et `Take` .</span><span class="sxs-lookup"><span data-stu-id="21be4-125">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="21be4-126">Ces opérations doivent être appliquées sur la navigation dans la collection dans l’expression lambda passée à la méthode Include, comme illustré dans l’exemple ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="21be4-126">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

<span data-ttu-id="21be4-127">Chaque navigation incluse n’autorise qu’un seul ensemble d’opérations de filtre.</span><span class="sxs-lookup"><span data-stu-id="21be4-127">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="21be4-128">Dans les cas où plusieurs opérations include sont appliquées pour une navigation de collection donnée ( `blog.Posts` dans les exemples ci-dessous), les opérations de filtre peuvent uniquement être spécifiées sur l’une d’entre elles :</span><span class="sxs-lookup"><span data-stu-id="21be4-128">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="21be4-129">Au lieu de cela, des opérations identiques peuvent être appliquées pour chaque navigation qui est incluse plusieurs fois :</span><span class="sxs-lookup"><span data-stu-id="21be4-129">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="21be4-130">En cas de requêtes de suivi, les résultats de l’inclusion filtrée peuvent être inattendus en raison de la correction de la [navigation](xref:core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="21be4-130">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="21be4-131">Toutes les entités pertinentes qui ont été interrogées précédemment et qui ont été stockées dans le dispositif de suivi des modifications sont présentes dans les résultats de la requête include filtrée, même si elles ne répondent pas aux exigences du filtre.</span><span class="sxs-lookup"><span data-stu-id="21be4-131">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="21be4-132">Envisagez d’utiliser des `NoTracking` requêtes ou de recréer DbContext lors de l’utilisation de l’inclusion filtrée dans ces situations.</span><span class="sxs-lookup"><span data-stu-id="21be4-132">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="21be4-133">Exemple :</span><span class="sxs-lookup"><span data-stu-id="21be4-133">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

> [!NOTE]
> <span data-ttu-id="21be4-134">En cas de requêtes de suivi, la navigation sur laquelle l’inclusion filtrée a été appliquée est considérée comme étant chargée.</span><span class="sxs-lookup"><span data-stu-id="21be4-134">In case of tracking queries, the navigation on which filtered include was applied is considered to be loaded.</span></span> <span data-ttu-id="21be4-135">Cela signifie que EF Core n’essaiera pas de recharger ses valeurs à l’aide d’un chargement [explicite](xref:core/querying/related-data/explicit) ou d’un [chargement différé](xref:core/querying/related-data/lazy), même si certains éléments peuvent toujours être manquants.</span><span class="sxs-lookup"><span data-stu-id="21be4-135">This means that EF Core will not attempt to re-load it's values using [explicit loading](xref:core/querying/related-data/explicit) or [lazy loading](xref:core/querying/related-data/lazy), even though some elements could still be missing.</span></span>

## <a name="include-on-derived-types"></a><span data-ttu-id="21be4-136">Inclure des types dérivés</span><span class="sxs-lookup"><span data-stu-id="21be4-136">Include on derived types</span></span>

<span data-ttu-id="21be4-137">Vous pouvez inclure des données associées de la navigation définies uniquement sur un type dérivé à l’aide `Include` de et de `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="21be4-137">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="21be4-138">En partant du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="21be4-138">Given the following model:</span></span>

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

<span data-ttu-id="21be4-139">Le contenu de la navigation `School` de toutes les personnes qui sont des étudiants peut être chargé dynamiquement à l’aide d’un certain nombre de modèles :</span><span class="sxs-lookup"><span data-stu-id="21be4-139">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="21be4-140">utilisation du forçage de type</span><span class="sxs-lookup"><span data-stu-id="21be4-140">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="21be4-141">utilisation de l’opérateur `as`</span><span class="sxs-lookup"><span data-stu-id="21be4-141">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="21be4-142">utilisation de la surcharge de `Include` qui accepte les paramètres de type `string`</span><span class="sxs-lookup"><span data-stu-id="21be4-142">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
