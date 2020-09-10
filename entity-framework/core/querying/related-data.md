---
title: 'Chargement des données associées : EF Core'
description: Différentes stratégies de chargement des données associées avec Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 43b8cbac1e36a37bc85c953319407b3814d7d327
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618919"
---
# <a name="loading-related-data"></a><span data-ttu-id="14143-103">Chargement des données associées</span><span class="sxs-lookup"><span data-stu-id="14143-103">Loading Related Data</span></span>

<span data-ttu-id="14143-104">Entity Framework Core vous permet d’utiliser les propriétés de navigation dans votre modèle pour charger des entités associées.</span><span class="sxs-lookup"><span data-stu-id="14143-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="14143-105">Il existe trois modèles O/RM communs utilisés pour charger les données associées.</span><span class="sxs-lookup"><span data-stu-id="14143-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="14143-106">Le **Chargement hâtif** signifie que les données associées sont chargées à partir de la base de données dans le cadre de la requête initiale.</span><span class="sxs-lookup"><span data-stu-id="14143-106">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="14143-107">Le **Chargement explicite** signifie que les données associées sont explicitement chargées à partir de la base de données à un moment ultérieur.</span><span class="sxs-lookup"><span data-stu-id="14143-107">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="14143-108">Le **Chargement différé** signifie que les données associées sont chargées de façon transparente à partir de la base de données lors de l’accès à la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="14143-108">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="14143-109">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="14143-109">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="14143-110">Chargement hâtif</span><span class="sxs-lookup"><span data-stu-id="14143-110">Eager loading</span></span>

<span data-ttu-id="14143-111">Vous pouvez utiliser la méthode `Include` pour spécifier les données associées à inclure dans les résultats de la requête.</span><span class="sxs-lookup"><span data-stu-id="14143-111">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="14143-112">Dans l’exemple suivant, les blogs retournés dans les résultats auront leurs propriétés `Posts` remplies avec les billets associés.</span><span class="sxs-lookup"><span data-stu-id="14143-112">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="14143-113">Entity Framework Core corrige automatiquement les propriétés de navigation vers d’autres entités qui étaient précédemment chargées dans l’instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="14143-113">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="14143-114">Ainsi, même vous n’incluez pas explicitement si les données pour une propriété de navigation, la propriété peut toujours être renseignée si toutes ou une partie des entités associées ont été précédemment chargées.</span><span class="sxs-lookup"><span data-stu-id="14143-114">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="14143-115">Vous pouvez inclure des données associées provenant de plusieurs relations dans une seule requête.</span><span class="sxs-lookup"><span data-stu-id="14143-115">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="14143-116">Inclusion de plusieurs niveaux</span><span class="sxs-lookup"><span data-stu-id="14143-116">Including multiple levels</span></span>

<span data-ttu-id="14143-117">Vous pouvez descendre dans la hiérarchie des relations pour inclure plusieurs niveaux de données associées à l’aide de la méthode `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="14143-117">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="14143-118">L’exemple suivant charge tous les blogs, leurs messages associés et l’auteur de chaque publication.</span><span class="sxs-lookup"><span data-stu-id="14143-118">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="14143-119">Vous pouvez enchaîner plusieurs appels à `ThenInclude` pour continuer à inclure les niveaux de données associées suivants.</span><span class="sxs-lookup"><span data-stu-id="14143-119">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="14143-120">Vous pouvez combiner tous les appels pour inclure les données associées de plusieurs niveaux et plusieurs racines dans la même requête.</span><span class="sxs-lookup"><span data-stu-id="14143-120">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="14143-121">Vous pourriez souhaiter inclure plusieurs entités associées pour une entité qui est incluse.</span><span class="sxs-lookup"><span data-stu-id="14143-121">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="14143-122">Par exemple, quand vous interrogez des `Blogs`, vous incluez `Posts`, puis souhaitez inclure à la fois les `Author` et les `Tags` des `Posts`.</span><span class="sxs-lookup"><span data-stu-id="14143-122">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="14143-123">Pour inclure les deux, vous devez spécifier chaque chemin d’accès include commençant à la racine.</span><span class="sxs-lookup"><span data-stu-id="14143-123">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="14143-124">Par exemple, `Blog -> Posts -> Author` et `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="14143-124">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="14143-125">Cela ne signifie pas que vous obtiendrez des jointures redondantes. dans la plupart des cas, EF combinera les jointures lors de la génération de SQL.</span><span class="sxs-lookup"><span data-stu-id="14143-125">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="single-and-split-queries"></a><span data-ttu-id="14143-126">Requêtes simples et fractionnées</span><span class="sxs-lookup"><span data-stu-id="14143-126">Single and split queries</span></span>

#### <a name="single-queries"></a><span data-ttu-id="14143-127">Requêtes uniques</span><span class="sxs-lookup"><span data-stu-id="14143-127">Single queries</span></span>

<span data-ttu-id="14143-128">Dans les bases de données relationnelles, toutes les entités associées sont chargées par défaut en introduisant des JOINTUREs :</span><span class="sxs-lookup"><span data-stu-id="14143-128">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="14143-129">Si un blog classique contient plusieurs publications connexes, les lignes de ces publications dupliqueront les informations du blog, ce qui entraînerait un problème de « explosion cartésien ».</span><span class="sxs-lookup"><span data-stu-id="14143-129">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="14143-130">À mesure que des relations un-à-plusieurs sont chargées, la quantité de données dupliquées peut croître et nuire aux performances de votre application.</span><span class="sxs-lookup"><span data-stu-id="14143-130">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="14143-131">Par défaut, EF Core émet un avertissement s’il détecte des requêtes qui chargent la collection, notamment celles qui peuvent entraîner des problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="14143-131">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

#### <a name="split-queries"></a><span data-ttu-id="14143-132">Fractionner les requêtes</span><span class="sxs-lookup"><span data-stu-id="14143-132">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="14143-133">Cette fonctionnalité est introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="14143-133">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="14143-134">EF vous permet de spécifier qu’une requête LINQ donnée doit être *fractionnée* en plusieurs requêtes SQL.</span><span class="sxs-lookup"><span data-stu-id="14143-134">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="14143-135">Au lieu de JOINTUREs, les requêtes Split effectuent une requête SQL supplémentaire pour chaque navigation un-à-plusieurs incluse :</span><span class="sxs-lookup"><span data-stu-id="14143-135">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="14143-136">Il produira le code SQL suivant :</span><span class="sxs-lookup"><span data-stu-id="14143-136">It will produce the following SQL:</span></span>

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
> <span data-ttu-id="14143-137">Les entités associées à un-à-un sont toujours chargées via des JOINTUREs dans la même requête, car cela n’a aucun impact sur les performances.</span><span class="sxs-lookup"><span data-stu-id="14143-137">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

#### <a name="enabling-split-queries-globally"></a><span data-ttu-id="14143-138">Activation globale des requêtes fractionnées</span><span class="sxs-lookup"><span data-stu-id="14143-138">Enabling split queries globally</span></span>

<span data-ttu-id="14143-139">Vous pouvez également configurer des requêtes fractionnées comme valeur par défaut pour le contexte de votre application :</span><span class="sxs-lookup"><span data-stu-id="14143-139">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="14143-140">Lorsque les requêtes de fractionnement sont configurées en tant que valeurs par défaut, il est toujours possible de configurer des requêtes spécifiques pour qu’elles s’exécutent en tant que requêtes uniques :</span><span class="sxs-lookup"><span data-stu-id="14143-140">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="14143-141">Si le mode de fractionnement de requête n’est pas explicitement spécifié, ni globalement, ni sur la requête, et EF Core détecte qu’une seule requête charge plusieurs collections includes, un avertissement est émis pour attirer l’attention sur les problèmes de performances potentiels résultants.</span><span class="sxs-lookup"><span data-stu-id="14143-141">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="14143-142">Le fait de définir le mode de requête sur SingleQuery entraîne la non-génération de l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="14143-142">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

#### <a name="characteristics-of-split-queries"></a><span data-ttu-id="14143-143">Caractéristiques des requêtes fractionnées</span><span class="sxs-lookup"><span data-stu-id="14143-143">Characteristics of split queries</span></span>

<span data-ttu-id="14143-144">Si la requête Split évite les problèmes de performances associés aux JOINTUREs et à l’explosion cartésienne, elle présente également certains inconvénients :</span><span class="sxs-lookup"><span data-stu-id="14143-144">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="14143-145">Alors que la plupart des bases de données garantissent la cohérence des données pour les requêtes uniques, il n’existe pas de garantie de ce type pour plusieurs requêtes.</span><span class="sxs-lookup"><span data-stu-id="14143-145">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="14143-146">Si la base de données est mise à jour en même temps lors de l’exécution de vos requêtes, les données résultantes peuvent ne pas être cohérentes.</span><span class="sxs-lookup"><span data-stu-id="14143-146">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="14143-147">Vous pouvez l’atténuer en encapsulant les requêtes dans une transaction sérialisable ou d’instantané, même si cela peut entraîner des problèmes de performances.</span><span class="sxs-lookup"><span data-stu-id="14143-147">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="14143-148">Pour plus d’informations, consultez la documentation de votre base de données.</span><span class="sxs-lookup"><span data-stu-id="14143-148">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="14143-149">Chaque requête implique actuellement un aller-retour réseau supplémentaire vers votre base de données.</span><span class="sxs-lookup"><span data-stu-id="14143-149">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="14143-150">Plusieurs allers-retours sur le réseau peuvent dégrader les performances, notamment lorsque la latence est élevée pour la base de données (par exemple, les services Cloud).</span><span class="sxs-lookup"><span data-stu-id="14143-150">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="14143-151">Bien que certaines bases de données autorisent l’utilisation des résultats de plusieurs requêtes en même temps (SQL Server avec MARS, SQLite), la plupart n’autorisent l’activation que d’une seule requête à un moment donné.</span><span class="sxs-lookup"><span data-stu-id="14143-151">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="14143-152">Ainsi, tous les résultats des requêtes antérieures doivent être mis en mémoire tampon dans la mémoire de votre application avant d’exécuter des requêtes ultérieures, ce qui entraîne une augmentation des besoins en mémoire.</span><span class="sxs-lookup"><span data-stu-id="14143-152">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="14143-153">Malheureusement, il n’existe aucune stratégie pour charger des entités associées qui s’adaptent à tous les scénarios.</span><span class="sxs-lookup"><span data-stu-id="14143-153">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="14143-154">Examinez attentivement les avantages et les inconvénients des requêtes simples et fractionnées, puis sélectionnez celle qui répond à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="14143-154">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="14143-155">Include filtré</span><span class="sxs-lookup"><span data-stu-id="14143-155">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="14143-156">Cette fonctionnalité est introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="14143-156">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="14143-157">Lorsque vous appliquez include pour charger des données associées, vous pouvez appliquer certaines opérations énumérables sur la navigation de collection incluse, ce qui permet de filtrer et de trier les résultats.</span><span class="sxs-lookup"><span data-stu-id="14143-157">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="14143-158">Les opérations prises en charge sont les suivantes : `Where` , `OrderBy` ,,,, `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` et `Take` .</span><span class="sxs-lookup"><span data-stu-id="14143-158">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="14143-159">Ces opérations doivent être appliquées sur la navigation dans la collection dans l’expression lambda passée à la méthode Include, comme illustré dans l’exemple ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="14143-159">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="14143-160">Chaque navigation incluse n’autorise qu’un seul ensemble d’opérations de filtre.</span><span class="sxs-lookup"><span data-stu-id="14143-160">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="14143-161">Dans les cas où plusieurs opérations include sont appliquées pour une navigation de collection donnée ( `blog.Posts` dans les exemples ci-dessous), les opérations de filtre peuvent uniquement être spécifiées sur l’une d’entre elles :</span><span class="sxs-lookup"><span data-stu-id="14143-161">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="14143-162">Au lieu de cela, des opérations identiques peuvent être appliquées pour chaque navigation qui est incluse plusieurs fois :</span><span class="sxs-lookup"><span data-stu-id="14143-162">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="14143-163">En cas de requêtes de suivi, les résultats de l’inclusion filtrée peuvent être inattendus en raison de la correction de la [navigation](xref:core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="14143-163">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="14143-164">Toutes les entités pertinentes qui ont été interrogées précédemment et qui ont été stockées dans le dispositif de suivi des modifications sont présentes dans les résultats de la requête include filtrée, même si elles ne répondent pas aux exigences du filtre.</span><span class="sxs-lookup"><span data-stu-id="14143-164">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="14143-165">Envisagez d’utiliser des `NoTracking` requêtes ou de recréer DbContext lors de l’utilisation de l’inclusion filtrée dans ces situations.</span><span class="sxs-lookup"><span data-stu-id="14143-165">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="14143-166">Exemple :</span><span class="sxs-lookup"><span data-stu-id="14143-166">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="14143-167">Inclure des types dérivés</span><span class="sxs-lookup"><span data-stu-id="14143-167">Include on derived types</span></span>

<span data-ttu-id="14143-168">Vous pouvez inclure des données associées de la navigation définies uniquement sur un type dérivé à l’aide `Include` de et de `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="14143-168">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="14143-169">En partant du modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="14143-169">Given the following model:</span></span>

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

<span data-ttu-id="14143-170">Le contenu de la navigation `School` de toutes les personnes qui sont des étudiants peut être chargé dynamiquement à l’aide d’un certain nombre de modèles :</span><span class="sxs-lookup"><span data-stu-id="14143-170">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="14143-171">utilisation du forçage de type</span><span class="sxs-lookup"><span data-stu-id="14143-171">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="14143-172">utilisation de l’opérateur `as`</span><span class="sxs-lookup"><span data-stu-id="14143-172">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="14143-173">utilisation de la surcharge de `Include` qui accepte les paramètres de type `string`</span><span class="sxs-lookup"><span data-stu-id="14143-173">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="14143-174">Chargement explicite</span><span class="sxs-lookup"><span data-stu-id="14143-174">Explicit loading</span></span>

<span data-ttu-id="14143-175">Vous pouvez charger explicitement une propriété de navigation via l’API `DbContext.Entry(...)`.</span><span class="sxs-lookup"><span data-stu-id="14143-175">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="14143-176">Vous pouvez également explicitement charger une propriété de navigation en exécutant une requête distincte qui retourne les entités associées.</span><span class="sxs-lookup"><span data-stu-id="14143-176">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="14143-177">Si le suivi des modifications est activé, lorsque la requête matérialise une entité, EF Core définira automatiquement les propriétés de navigation de l’entité nouvellement chargée pour faire référence à toutes les entités déjà chargées, et définir les propriétés de navigation des entités déjà chargées pour faire référence à l’entité qui vient d’être chargée.</span><span class="sxs-lookup"><span data-stu-id="14143-177">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="14143-178">Interrogation des entités associées</span><span class="sxs-lookup"><span data-stu-id="14143-178">Querying related entities</span></span>

<span data-ttu-id="14143-179">Vous pouvez également obtenir une requête LINQ qui représente le contenu d’une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="14143-179">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="14143-180">Elle vous permet d’appliquer des opérateurs supplémentaires sur la requête.</span><span class="sxs-lookup"><span data-stu-id="14143-180">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="14143-181">Exemple nombre application d’un opérateur d’agrégation sur les entités associées sans les charger en mémoire.</span><span class="sxs-lookup"><span data-stu-id="14143-181">Foe example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="14143-182">Vous pouvez également filtrer les entités associées qui sont chargées en mémoire.</span><span class="sxs-lookup"><span data-stu-id="14143-182">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="14143-183">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="14143-183">Lazy loading</span></span>

<span data-ttu-id="14143-184">La façon la plus simple d’utiliser le chargement différé est d’installer le package [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) et de l’activer avec un appel à `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="14143-184">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="14143-185">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="14143-185">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="14143-186">Ou bien, lors de l’utilisation d’AddDbContext :</span><span class="sxs-lookup"><span data-stu-id="14143-186">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="14143-187">EF Core active ensuite le chargement différé pour n’importe quelle propriété de navigation qui peut être substituée, c’est-à-dire qui doit être `virtual` et sur une classe qui peut être héritée.</span><span class="sxs-lookup"><span data-stu-id="14143-187">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="14143-188">Par exemple, dans les entités suivantes, les propriétés de navigation `Post.Blog` et `Blog.Posts` seront chargées en différé.</span><span class="sxs-lookup"><span data-stu-id="14143-188">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="14143-189">Chargement différé sans proxy</span><span class="sxs-lookup"><span data-stu-id="14143-189">Lazy loading without proxies</span></span>

<span data-ttu-id="14143-190">Les proxys à chargement différé fonctionnent en injectant le service `ILazyLoader` dans une entité, comme décrit dans [Constructeurs de type d’entité](xref:core/modeling/constructors).</span><span class="sxs-lookup"><span data-stu-id="14143-190">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](xref:core/modeling/constructors).</span></span> <span data-ttu-id="14143-191">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="14143-191">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="14143-192">Cette méthode ne requiert pas l’héritage des types d’entités ou des propriétés de navigation virtuelles, et permet aux instances d’entité créées avec la `new` valeur de chargement différé une fois attachées à un contexte.</span><span class="sxs-lookup"><span data-stu-id="14143-192">This method doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="14143-193">Toutefois, il requiert une référence au service `ILazyLoader`, qui est défini dans le package [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/).</span><span class="sxs-lookup"><span data-stu-id="14143-193">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="14143-194">Ce package contient un ensemble minimal de types afin qu’il n’y ait qu’un faible impact en fonction de celui-ci.</span><span class="sxs-lookup"><span data-stu-id="14143-194">This package contains a minimal set of types so that there is little impact in depending on it.</span></span> <span data-ttu-id="14143-195">Toutefois, pour éviter complètement en fonction des packages d’EF Core dans les types d’entité, il est possible d’injecter la `ILazyLoader.Load` méthode en tant que délégué.</span><span class="sxs-lookup"><span data-stu-id="14143-195">However, to completely avoid depending on any EF Core packages in the entity types, it's possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="14143-196">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="14143-196">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="14143-197">Le code ci-dessus utilise une méthode d'extension `Load` pour rendre l’utilisation du délégué un peu plus propre :</span><span class="sxs-lookup"><span data-stu-id="14143-197">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]
> <span data-ttu-id="14143-198">Le paramètre de constructeur pour le délégué à chargement différé doit être appelé « lazyLoader ».</span><span class="sxs-lookup"><span data-stu-id="14143-198">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="14143-199">La configuration permettant d’utiliser un nom différent est prévue pour une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="14143-199">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="14143-200">Données associées et sérialisation</span><span class="sxs-lookup"><span data-stu-id="14143-200">Related data and serialization</span></span>

<span data-ttu-id="14143-201">Étant donné que EF Core effectue automatiquement une correction des propriétés de navigation, vous pouvez vous retrouver avec des cycles dans votre graphique d’objets.</span><span class="sxs-lookup"><span data-stu-id="14143-201">Because EF Core automatically does fix-up of navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="14143-202">Par exemple, le chargement d’un blog et de ses billets associés aboutit à un objet de blog qui fait référence à une collection de billets.</span><span class="sxs-lookup"><span data-stu-id="14143-202">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="14143-203">Chacun de ces billets aura une référence vers le blog.</span><span class="sxs-lookup"><span data-stu-id="14143-203">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="14143-204">Certaines infrastructures de sérialisation n’autorisent pas de tels cycles.</span><span class="sxs-lookup"><span data-stu-id="14143-204">Some serialization frameworks don't allow such cycles.</span></span> <span data-ttu-id="14143-205">Par exemple, Json.NET lèvera l’exception suivante si un cycle est trouvé.</span><span class="sxs-lookup"><span data-stu-id="14143-205">For example, Json.NET will throw the following exception if a cycle is found.</span></span>

> <span data-ttu-id="14143-206">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span><span class="sxs-lookup"><span data-stu-id="14143-206">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="14143-207">Si vous utilisez ASP.NET Core, vous pouvez configurer Json.NET pour ignorer les cycles qu’il trouve dans le graphique d’objets.</span><span class="sxs-lookup"><span data-stu-id="14143-207">If you're using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="14143-208">Cette configuration s’effectue dans la `ConfigureServices(...)` méthode de `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="14143-208">This configuration is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="14143-209">Vous pouvez aussi décorer une des propriétés de navigation avec l’attribut `[JsonIgnore]`, ce qui indique à Json.NET de ne pas parcourir cette propriété de navigation lors de la sérialisation.</span><span class="sxs-lookup"><span data-stu-id="14143-209">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
