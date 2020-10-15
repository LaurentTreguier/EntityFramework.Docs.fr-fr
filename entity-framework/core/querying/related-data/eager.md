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
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="e97ad-103">Chargement hâtif des données associées</span><span class="sxs-lookup"><span data-stu-id="e97ad-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="e97ad-104">Chargement hâtif</span><span class="sxs-lookup"><span data-stu-id="e97ad-104">Eager loading</span></span>

<span data-ttu-id="e97ad-105">Vous pouvez utiliser la méthode `Include` pour spécifier les données associées à inclure dans les résultats de la requête.</span><span class="sxs-lookup"><span data-stu-id="e97ad-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="e97ad-106">Dans l’exemple suivant, les blogs retournés dans les résultats auront leurs propriétés `Posts` remplies avec les billets associés.</span><span class="sxs-lookup"><span data-stu-id="e97ad-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="e97ad-107">Entity Framework Core corrige automatiquement les propriétés de navigation vers d’autres entités qui étaient précédemment chargées dans l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="e97ad-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="e97ad-108">Ainsi, même vous n’incluez pas explicitement si les données pour une propriété de navigation, la propriété peut toujours être renseignée si toutes ou une partie des entités associées ont été précédemment chargées.</span><span class="sxs-lookup"><span data-stu-id="e97ad-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="e97ad-109">Vous pouvez inclure des données associées provenant de plusieurs relations dans une seule requête.</span><span class="sxs-lookup"><span data-stu-id="e97ad-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a><span data-ttu-id="e97ad-110">Inclusion de plusieurs niveaux</span><span class="sxs-lookup"><span data-stu-id="e97ad-110">Including multiple levels</span></span>

<span data-ttu-id="e97ad-111">Vous pouvez descendre dans la hiérarchie des relations pour inclure plusieurs niveaux de données associées à l’aide de la méthode `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="e97ad-111">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="e97ad-112">L’exemple suivant charge tous les blogs, leurs messages associés et l’auteur de chaque publication.</span><span class="sxs-lookup"><span data-stu-id="e97ad-112">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

<span data-ttu-id="e97ad-113">Vous pouvez enchaîner plusieurs appels à `ThenInclude` pour continuer à inclure les niveaux de données associées suivants.</span><span class="sxs-lookup"><span data-stu-id="e97ad-113">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

<span data-ttu-id="e97ad-114">Vous pouvez combiner tous les appels pour inclure les données associées de plusieurs niveaux et plusieurs racines dans la même requête.</span><span class="sxs-lookup"><span data-stu-id="e97ad-114">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

<span data-ttu-id="e97ad-115">Vous pourriez souhaiter inclure plusieurs entités associées pour une entité qui est incluse.</span><span class="sxs-lookup"><span data-stu-id="e97ad-115">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="e97ad-116">Par exemple, quand vous interrogez des `Blogs`, vous incluez `Posts`, puis souhaitez inclure à la fois les `Author` et les `Tags` des `Posts`.</span><span class="sxs-lookup"><span data-stu-id="e97ad-116">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="e97ad-117">Pour inclure les deux, vous devez spécifier chaque chemin d’accès include commençant à la racine.</span><span class="sxs-lookup"><span data-stu-id="e97ad-117">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="e97ad-118">Par exemple, `Blog -> Posts -> Author` et `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="e97ad-118">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="e97ad-119">Cela ne signifie pas que vous obtiendrez des jointures redondantes. dans la plupart des cas, EF combinera les jointures lors de la génération de SQL.</span><span class="sxs-lookup"><span data-stu-id="e97ad-119">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a><span data-ttu-id="e97ad-120">Requêtes simples et fractionnées</span><span class="sxs-lookup"><span data-stu-id="e97ad-120">Single and split queries</span></span>

### <a name="single-queries"></a><span data-ttu-id="e97ad-121">Requêtes uniques</span><span class="sxs-lookup"><span data-stu-id="e97ad-121">Single queries</span></span>

<span data-ttu-id="e97ad-122">Dans les bases de données relationnelles, toutes les entités associées sont chargées par défaut en introduisant des JOINTUREs :</span><span class="sxs-lookup"><span data-stu-id="e97ad-122">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="e97ad-123">Si un blog classique contient plusieurs publications connexes, les lignes de ces publications dupliqueront les informations du blog, ce qui entraînerait un problème de « explosion cartésien ».</span><span class="sxs-lookup"><span data-stu-id="e97ad-123">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="e97ad-124">À mesure que des relations un-à-plusieurs sont chargées, la quantité de données dupliquées peut croître et nuire aux performances de votre application.</span><span class="sxs-lookup"><span data-stu-id="e97ad-124">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="e97ad-125">Par défaut, EF Core émet un avertissement s’il détecte des requêtes qui chargent la collection, notamment celles qui peuvent entraîner des problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="e97ad-125">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

### <a name="split-queries"></a><span data-ttu-id="e97ad-126">Fractionner les requêtes</span><span class="sxs-lookup"><span data-stu-id="e97ad-126">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="e97ad-127">Cette fonctionnalité est introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="e97ad-127">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="e97ad-128">EF vous permet de spécifier qu’une requête LINQ donnée doit être *fractionnée* en plusieurs requêtes SQL.</span><span class="sxs-lookup"><span data-stu-id="e97ad-128">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="e97ad-129">Au lieu de JOINTUREs, les requêtes Split effectuent une requête SQL supplémentaire pour chaque navigation un-à-plusieurs incluse :</span><span class="sxs-lookup"><span data-stu-id="e97ad-129">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="e97ad-130">Il produira le code SQL suivant :</span><span class="sxs-lookup"><span data-stu-id="e97ad-130">It will produce the following SQL:</span></span>

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
> <span data-ttu-id="e97ad-131">Les entités associées à un-à-un sont toujours chargées via des JOINTUREs dans la même requête, car cela n’a aucun impact sur les performances.</span><span class="sxs-lookup"><span data-stu-id="e97ad-131">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

### <a name="enabling-split-queries-globally"></a><span data-ttu-id="e97ad-132">Activation globale des requêtes fractionnées</span><span class="sxs-lookup"><span data-stu-id="e97ad-132">Enabling split queries globally</span></span>

<span data-ttu-id="e97ad-133">Vous pouvez également configurer des requêtes fractionnées comme valeur par défaut pour le contexte de votre application :</span><span class="sxs-lookup"><span data-stu-id="e97ad-133">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="e97ad-134">Lorsque les requêtes de fractionnement sont configurées en tant que valeurs par défaut, il est toujours possible de configurer des requêtes spécifiques pour qu’elles s’exécutent en tant que requêtes uniques :</span><span class="sxs-lookup"><span data-stu-id="e97ad-134">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="e97ad-135">Si le mode de fractionnement de requête n’est pas explicitement spécifié, ni globalement, ni sur la requête, et EF Core détecte qu’une seule requête charge plusieurs collections includes, un avertissement est émis pour attirer l’attention sur les problèmes de performances potentiels résultants.</span><span class="sxs-lookup"><span data-stu-id="e97ad-135">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="e97ad-136">Le fait de définir le mode de requête sur SingleQuery entraîne la non-génération de l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="e97ad-136">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

### <a name="characteristics-of-split-queries"></a><span data-ttu-id="e97ad-137">Caractéristiques des requêtes fractionnées</span><span class="sxs-lookup"><span data-stu-id="e97ad-137">Characteristics of split queries</span></span>

<span data-ttu-id="e97ad-138">Si la requête Split évite les problèmes de performances associés aux JOINTUREs et à l’explosion cartésienne, elle présente également certains inconvénients :</span><span class="sxs-lookup"><span data-stu-id="e97ad-138">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="e97ad-139">Alors que la plupart des bases de données garantissent la cohérence des données pour les requêtes uniques, il n’existe pas de garantie de ce type pour plusieurs requêtes.</span><span class="sxs-lookup"><span data-stu-id="e97ad-139">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="e97ad-140">Si la base de données est mise à jour en même temps lors de l’exécution de vos requêtes, les données résultantes peuvent ne pas être cohérentes.</span><span class="sxs-lookup"><span data-stu-id="e97ad-140">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="e97ad-141">Vous pouvez l’atténuer en encapsulant les requêtes dans une transaction sérialisable ou d’instantané, même si cela peut entraîner des problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="e97ad-141">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="e97ad-142">Pour plus d’informations, consultez la documentation de votre base de données.</span><span class="sxs-lookup"><span data-stu-id="e97ad-142">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="e97ad-143">Chaque requête implique actuellement un aller-retour réseau supplémentaire vers votre base de données.</span><span class="sxs-lookup"><span data-stu-id="e97ad-143">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="e97ad-144">Plusieurs allers-retours sur le réseau peuvent dégrader les performances, notamment lorsque la latence est élevée pour la base de données (par exemple, les services Cloud).</span><span class="sxs-lookup"><span data-stu-id="e97ad-144">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="e97ad-145">Bien que certaines bases de données autorisent l’utilisation des résultats de plusieurs requêtes en même temps (SQL Server avec MARS, SQLite), la plupart n’autorisent l’activation que d’une seule requête à un moment donné.</span><span class="sxs-lookup"><span data-stu-id="e97ad-145">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="e97ad-146">Ainsi, tous les résultats des requêtes antérieures doivent être mis en mémoire tampon dans la mémoire de votre application avant d’exécuter des requêtes ultérieures, ce qui entraîne une augmentation des besoins en mémoire.</span><span class="sxs-lookup"><span data-stu-id="e97ad-146">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="e97ad-147">Malheureusement, il n’existe aucune stratégie pour charger des entités associées qui s’adaptent à tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="e97ad-147">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="e97ad-148">Examinez attentivement les avantages et les inconvénients des requêtes simples et fractionnées, puis sélectionnez celle qui répond à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="e97ad-148">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="e97ad-149">Include filtré</span><span class="sxs-lookup"><span data-stu-id="e97ad-149">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="e97ad-150">Cette fonctionnalité est introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="e97ad-150">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="e97ad-151">Lorsque vous appliquez include pour charger des données associées, vous pouvez appliquer certaines opérations énumérables sur la navigation de collection incluse, ce qui permet de filtrer et de trier les résultats.</span><span class="sxs-lookup"><span data-stu-id="e97ad-151">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="e97ad-152">Les opérations prises en charge sont les suivantes : `Where` , `OrderBy` ,,,, `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` et `Take` .</span><span class="sxs-lookup"><span data-stu-id="e97ad-152">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="e97ad-153">Ces opérations doivent être appliquées sur la navigation dans la collection dans l’expression lambda passée à la méthode Include, comme illustré dans l’exemple ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="e97ad-153">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

<span data-ttu-id="e97ad-154">Chaque navigation incluse n’autorise qu’un seul ensemble d’opérations de filtre.</span><span class="sxs-lookup"><span data-stu-id="e97ad-154">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="e97ad-155">Dans les cas où plusieurs opérations include sont appliquées pour une navigation de collection donnée ( `blog.Posts` dans les exemples ci-dessous), les opérations de filtre peuvent uniquement être spécifiées sur l’une d’entre elles :</span><span class="sxs-lookup"><span data-stu-id="e97ad-155">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="e97ad-156">Au lieu de cela, des opérations identiques peuvent être appliquées pour chaque navigation qui est incluse plusieurs fois :</span><span class="sxs-lookup"><span data-stu-id="e97ad-156">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="e97ad-157">En cas de requêtes de suivi, les résultats de l’inclusion filtrée peuvent être inattendus en raison de la correction de la [navigation](xref:core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="e97ad-157">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="e97ad-158">Toutes les entités pertinentes qui ont été interrogées précédemment et qui ont été stockées dans le dispositif de suivi des modifications sont présentes dans les résultats de la requête include filtrée, même si elles ne répondent pas aux exigences du filtre.</span><span class="sxs-lookup"><span data-stu-id="e97ad-158">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="e97ad-159">Envisagez d’utiliser des `NoTracking` requêtes ou de recréer DbContext lors de l’utilisation de l’inclusion filtrée dans ces situations.</span><span class="sxs-lookup"><span data-stu-id="e97ad-159">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="e97ad-160">Exemple :</span><span class="sxs-lookup"><span data-stu-id="e97ad-160">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a><span data-ttu-id="e97ad-161">Inclure des types dérivés</span><span class="sxs-lookup"><span data-stu-id="e97ad-161">Include on derived types</span></span>

<span data-ttu-id="e97ad-162">Vous pouvez inclure des données associées de la navigation définies uniquement sur un type dérivé à l’aide `Include` de et de `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="e97ad-162">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="e97ad-163">En partant du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="e97ad-163">Given the following model:</span></span>

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

<span data-ttu-id="e97ad-164">Le contenu de la navigation `School` de toutes les personnes qui sont des étudiants peut être chargé dynamiquement à l’aide d’un certain nombre de modèles :</span><span class="sxs-lookup"><span data-stu-id="e97ad-164">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="e97ad-165">utilisation du forçage de type</span><span class="sxs-lookup"><span data-stu-id="e97ad-165">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="e97ad-166">utilisation de l’opérateur `as`</span><span class="sxs-lookup"><span data-stu-id="e97ad-166">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="e97ad-167">utilisation de la surcharge de `Include` qui accepte les paramètres de type `string`</span><span class="sxs-lookup"><span data-stu-id="e97ad-167">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
