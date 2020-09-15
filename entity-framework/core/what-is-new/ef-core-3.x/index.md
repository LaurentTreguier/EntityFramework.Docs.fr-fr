---
title: Nouvelles fonctionnalités d’Entity Framework Core 3.x – EF Core
description: Modifications et améliorations apportées dans Entity Framework Core 3.x.
author: divega
ms.date: 09/05/2020
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.x/index
ms.openlocfilehash: ddf1632756d0be52310a4c62d5b7335a460f66a1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620644"
---
# <a name="new-features-in-entity-framework-core-3x"></a><span data-ttu-id="f0911-103">Nouvelles fonctionnalités d’Entity Framework Core 3.x</span><span class="sxs-lookup"><span data-stu-id="f0911-103">New features in Entity Framework Core 3.x</span></span>

<span data-ttu-id="f0911-104">La liste suivante présente les nouvelles fonctionnalités majeures d’EF Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="f0911-104">The following list includes the major new features in EF Core 3.x</span></span>

<span data-ttu-id="f0911-105">La version majeure EF Core 3 contient également plusieurs [changements cassants](xref:core/what-is-new/ef-core-3.x/breaking-changes). Il s’agit d’améliorations des API pouvant avoir un impact négatif sur les applications existantes.</span><span class="sxs-lookup"><span data-stu-id="f0911-105">As a major release, EF Core 3.x also contains several [breaking changes](xref:core/what-is-new/ef-core-3.x/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>

## <a name="linq-overhaul"></a><span data-ttu-id="f0911-106">Révision de LINQ</span><span class="sxs-lookup"><span data-stu-id="f0911-106">LINQ overhaul</span></span>

<span data-ttu-id="f0911-107">LINQ permet d’écrire des requêtes de base de données dans le langage .NET de prédilection, en tirant parti de riches informations de type pour proposer IntelliSense et le contrôle de type au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="f0911-107">LINQ enables you to write database queries using your .NET language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="f0911-108">Toutefois, LINQ vous permet également d’écrire un nombre illimité de requêtes complexes contenant des expressions arbitraires (appels de méthode ou opérations).</span><span class="sxs-lookup"><span data-stu-id="f0911-108">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="f0911-109">La gestion de toutes ces combinaisons est le principal défi des fournisseurs LINQ.</span><span class="sxs-lookup"><span data-stu-id="f0911-109">How to handle all those combinations is the main challenge for LINQ providers.</span></span>

<span data-ttu-id="f0911-110">Dans EF Core 3.x, nous avons remanié notre fournisseur LINQ de façon à pouvoir traduire plus de modèles de requête en SQL, en générant des requêtes efficaces dans davantage de cas et en empêchant les requêtes inefficaces de passer inaperçues.</span><span class="sxs-lookup"><span data-stu-id="f0911-110">In EF Core 3.x, we rearchitected our LINQ provider to enable translating more query patterns into SQL, generating efficient queries in more cases, and preventing inefficient queries from going undetected.</span></span> <span data-ttu-id="f0911-111">Le nouveau fournisseur LINQ est la fondation qui nous permettra d’offrir de nouvelles capacités de requête et des améliorations de performances dans les versions futures, sans arrêter les applications et les fournisseurs de données existants.</span><span class="sxs-lookup"><span data-stu-id="f0911-111">The new LINQ provider is the foundation over which we'll be able to offer new query capabilities and performance improvements in future releases, without breaking existing applications and data providers.</span></span>

### <a name="restricted-client-evaluation"></a><span data-ttu-id="f0911-112">Évaluation restreinte du client</span><span class="sxs-lookup"><span data-stu-id="f0911-112">Restricted client evaluation</span></span>

<span data-ttu-id="f0911-113">Le changement de conception le plus important porte sur la façon dont nous manipulons les expressions LINQ qui ne peuvent pas être converties en paramètres ou traduites en SQL.</span><span class="sxs-lookup"><span data-stu-id="f0911-113">The most important design change has to do with how we handle LINQ expressions that cannot be converted to parameters or translated to SQL.</span></span>

<span data-ttu-id="f0911-114">Dans les versions précédentes, EF Core identifiait les parties d’une requête pouvant être traduites en SQL et exécutait le reste de la requête sur le client.</span><span class="sxs-lookup"><span data-stu-id="f0911-114">In previous versions, EF Core identified what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="f0911-115">Ce type d’exécution côté client est souhaitable dans certains cas, mais dans de nombreux autres cas, il peut entraîner des requêtes inefficaces.</span><span class="sxs-lookup"><span data-stu-id="f0911-115">This type of client-side execution is desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>

<span data-ttu-id="f0911-116">Par exemple, si EF Core 2.2 ne pouvait pas traduire un prédicat en appel `Where()`, il exécutait une instruction SQL sans filtre, transférait toutes les lignes depuis la base de données, puis les filtrait dans la mémoire :</span><span class="sxs-lookup"><span data-stu-id="f0911-116">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed an SQL statement without a filter, transferred all the rows from the database, and then filtered them in-memory:</span></span>

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

<span data-ttu-id="f0911-117">Cela peut être acceptable si la base de données contient un petit nombre de lignes, mais peut entraîner des problèmes de performances significatifs, voire une défaillance de l’application si la base de données contient un grand nombre de lignes.</span><span class="sxs-lookup"><span data-stu-id="f0911-117">That may be acceptable if the database contains a small number of rows but can result in significant performance issues or even application failure if the database contains a large number of rows.</span></span>

<span data-ttu-id="f0911-118">Dans EF Core 3.x, nous avons restreint l’évaluation du client à la seule projection au niveau supérieur (en somme, le dernier appel à `Select()`).</span><span class="sxs-lookup"><span data-stu-id="f0911-118">In EF Core 3.x, we've restricted client evaluation to only happen on the top-level projection (essentially, the last call to `Select()`).</span></span>
<span data-ttu-id="f0911-119">Lorsque EF Core 3.x détecte des expressions qui ne peuvent pas être traduites ailleurs dans la requête, une exception d’exécution est levée.</span><span class="sxs-lookup"><span data-stu-id="f0911-119">When EF Core 3.x detects expressions that can't be translated anywhere else in the query, it throws a runtime exception.</span></span>

<span data-ttu-id="f0911-120">Pour évaluer une condition de prédicat sur le client comme dans l’exemple précédent, les développeurs ont désormais besoin de basculer explicitement l’évaluation de la requête LINQ to Objects :</span><span class="sxs-lookup"><span data-stu-id="f0911-120">To evaluate a predicate condition on the client as in the previous example, developers now need to explicitly switch evaluation of the query to LINQ to Objects:</span></span>

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n))
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

<span data-ttu-id="f0911-121">Consultez la [documentation des changements cassants](xref:core/what-is-new/ef-core-3.x/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) pour en savoir plus sur la façon dont cela peut affecter des applications existantes.</span><span class="sxs-lookup"><span data-stu-id="f0911-121">See the [breaking changes documentation](xref:core/what-is-new/ef-core-3.x/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) for more details about how this can affect existing applications.</span></span>

### <a name="single-sql-statement-per-linq-query"></a><span data-ttu-id="f0911-122">Une seule instruction SQL par requête LINQ</span><span class="sxs-lookup"><span data-stu-id="f0911-122">Single SQL statement per LINQ query</span></span>

<span data-ttu-id="f0911-123">Autre aspect de la conception qui a beaucoup évolué dans la version 3.x, dorénavant nous créons toujours une seule instruction SQL par requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="f0911-123">Another aspect of the design that changed significantly in 3.x is that we now always generate a single SQL statement per LINQ query.</span></span> <span data-ttu-id="f0911-124">Dans les versions précédentes, nous avions l’habitude de créer plusieurs instructions SQL dans certains cas, à savoir des appels `Include()` traduits sur les propriétés de navigation de collection et des requêtes traduites qui suivaient certains modèles avec des sous-requêtes.</span><span class="sxs-lookup"><span data-stu-id="f0911-124">In previous versions, we used to generate multiple SQL statements in certain cases, translated `Include()` calls on collection navigation properties and translated queries that followed certain patterns with subqueries.</span></span> <span data-ttu-id="f0911-125">Même si c’était pratique dans certains cas, et que, pour `Include()` cela aidait même à éviter d’envoyer des données redondantes sur le réseau, l’implémentation était complexe et des comportements extrêmement inefficaces en résultaient (requêtes N+1).</span><span class="sxs-lookup"><span data-stu-id="f0911-125">Although this was in some cases convenient, and for `Include()` it even helped avoid sending redundant data over the wire, the implementation was complex, and it resulted in some extremely inefficient behaviors (N+1 queries).</span></span> <span data-ttu-id="f0911-126">Dans certaines situations, les données retournées sur plusieurs requêtes étaient potentiellement incohérentes.</span><span class="sxs-lookup"><span data-stu-id="f0911-126">There were situations in which the data returned across multiple queries was potentially inconsistent.</span></span>

<span data-ttu-id="f0911-127">Comme pour l’évaluation du client, une exception d’exécution est levée si EF Core 3.x ne peut pas traduire une requête LINQ en une seule instruction SQL.</span><span class="sxs-lookup"><span data-stu-id="f0911-127">Similarly to client evaluation, if EF Core 3.x can't translate a LINQ query into a single SQL statement, it throws a runtime exception.</span></span> <span data-ttu-id="f0911-128">Cependant, nous avons rendu EF Core capable de traduire beaucoup des modèles courants utilisés pour générer plusieurs requêtes relatives à une requête unique avec des jointures.</span><span class="sxs-lookup"><span data-stu-id="f0911-128">But we made EF Core capable of translating many of the common patterns that used to generate multiple queries to a single query with JOINs.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="f0911-129">Prise en charge de Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="f0911-129">Cosmos DB support</span></span>

<span data-ttu-id="f0911-130">Le fournisseur Cosmos DB pour EF Core permet aux développeurs qui maîtrisent le modèle de programmation d’EF de cibler facilement Azure Cosmos DB comme base de données d'application.</span><span class="sxs-lookup"><span data-stu-id="f0911-130">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span> <span data-ttu-id="f0911-131">L’objectif est de rendre certains des avantages de Cosmos DB (par exemple la distribution mondiale, la disponibilité « Always On », la scalabilité élastique et la faible latence) encore plus accessibles aux développeurs .NET.</span><span class="sxs-lookup"><span data-stu-id="f0911-131">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span> <span data-ttu-id="f0911-132">Le fournisseur propose la plupart des fonctionnalités d’EF Core, comme le suivi automatique des modifications, les conversions de valeurs et LINQ, sur l’API SQL dans Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="f0911-132">The provider enables most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

<span data-ttu-id="f0911-133">Consultez la [documentation fournisseur Cosmos DB](xref:core/providers/cosmos/index) pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="f0911-133">See the [Cosmos DB provider documentation](xref:core/providers/cosmos/index) for more details.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="f0911-134">Prise en charge de C# 8.0</span><span class="sxs-lookup"><span data-stu-id="f0911-134">C# 8.0 support</span></span>

<span data-ttu-id="f0911-135">EF Core 3.x tire parti de quelques-unes des [nouvelles fonctionnalités de C# 8.0](/dotnet/csharp/whats-new/csharp-8) :</span><span class="sxs-lookup"><span data-stu-id="f0911-135">EF Core 3.x takes advantage of a couple of the [new features in C# 8.0](/dotnet/csharp/whats-new/csharp-8):</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="f0911-136">Flux asynchrones</span><span class="sxs-lookup"><span data-stu-id="f0911-136">Asynchronous streams</span></span>

<span data-ttu-id="f0911-137">Les résultats des requêtes asynchrones sont maintenant exposés à l’aide de la nouvelle interface `IAsyncEnumerable<T>` standard et peuvent être consommés avec `await foreach`.</span><span class="sxs-lookup"><span data-stu-id="f0911-137">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

``` csharp
var orders =
    from o in context.Orders
    where o.Status == OrderStatus.Pending
    select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
    Process(o);
}
```

<span data-ttu-id="f0911-138">Consultez la rubrique [Flux asynchrones dans la documentation C#](/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="f0911-138">See the [asynchronous streams in the C# documentation](/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) for more details.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="f0911-139">Types références Nullables</span><span class="sxs-lookup"><span data-stu-id="f0911-139">Nullable reference types</span></span>

<span data-ttu-id="f0911-140">Lorsque cette nouvelle fonctionnalité est activée dans votre code, EF Core examine la possibilité de valeur Null des propriétés de type référence et l’applique aux colonnes et aux relations correspondantes dans la base de données : les propriétés de types de références sans possibilité de valeur Null sont traitées comme si elles avaient l’attribut d’annotation de données `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="f0911-140">When this new feature is enabled in your code, EF Core examines the nullability of reference type properties and applies it to corresponding columns and relationships in the database: properties of non-nullable references types are treated as if they had the `[Required]` data annotation attribute.</span></span>

<span data-ttu-id="f0911-141">Par exemple, dans la classe suivante, les propriétés marquées comme étant de type `string?` sont configurées comme facultatives, tandis que `string` est configuré comme obligatoire :</span><span class="sxs-lookup"><span data-stu-id="f0911-141">For example, in the following class, properties marked as of type `string?` will be configured as optional, whereas `string` will be configured as required:</span></span>

``` csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string? MiddleName { get; set; }
}
```

<span data-ttu-id="f0911-142">Consultez [Utilisation de types références avec possibilité de valeur Null](xref:core/miscellaneous/nullable-reference-types) dans la documentation EF Core pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="f0911-142">See [Working with nullable reference types](xref:core/miscellaneous/nullable-reference-types) in the EF Core documentation for more details.</span></span>

## <a name="interception-of-database-operations"></a><span data-ttu-id="f0911-143">Interception d’opérations de base de données</span><span class="sxs-lookup"><span data-stu-id="f0911-143">Interception of database operations</span></span>

<span data-ttu-id="f0911-144">La nouvelle API d’interception d’EF Core 3.x permet de fournir une logique personnalisée à appeler automatiquement chaque fois que des opérations de base de données de bas niveau sont effectuées dans le cadre du fonctionnement normal d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="f0911-144">The new interception API in EF Core 3.x allows providing custom logic to be invoked automatically whenever low-level database operations occur as part of the normal operation of EF Core.</span></span> <span data-ttu-id="f0911-145">Par exemple, lors de l’ouverture de connexions, de la validation de transactions ou de l’exécution de commandes.</span><span class="sxs-lookup"><span data-stu-id="f0911-145">For example, when opening connections, committing transactions, or executing commands.</span></span>

<span data-ttu-id="f0911-146">Similairement aux fonctionnalités d’interception qui existaient dans EF 6, les intercepteurs vous permettent d’intercepter des opérations avant ou après leur exécution.</span><span class="sxs-lookup"><span data-stu-id="f0911-146">Similarly to the interception features that existed in EF 6, interceptors allow you to intercept operations before or after they happen.</span></span> <span data-ttu-id="f0911-147">Lorsque vous les interceptez avant leur exécution, vous êtes autorisé à contourner l’exécution et à fournir d’autres résultats provenant de la logique d’interception.</span><span class="sxs-lookup"><span data-stu-id="f0911-147">When you intercept them before they happen, you are allowed to by-pass execution and supply alternate results from the interception logic.</span></span>

<span data-ttu-id="f0911-148">Par exemple, pour manipuler un texte de commande, vous pouvez créer un `DbCommandInterceptor` :</span><span class="sxs-lookup"><span data-stu-id="f0911-148">For example, to manipulate command text, you can create a `DbCommandInterceptor`:</span></span>

``` csharp
public class HintCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        // Manipulate the command text, etc. here...
        command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
        return result;
    }
}
```

<span data-ttu-id="f0911-149">Et l’enregistrer avec votre `DbContext` :</span><span class="sxs-lookup"><span data-stu-id="f0911-149">And register it with your `DbContext`:</span></span>

``` csharp
services.AddDbContext(b => b
    .UseSqlServer(connectionString)
    .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="f0911-150">Ingénierie à rebours des vues de base de données</span><span class="sxs-lookup"><span data-stu-id="f0911-150">Reverse engineering of database views</span></span>

<span data-ttu-id="f0911-151">Les types de requêtes qui représentent des données pouvant être lues à partir de la base de données, mais ne pouvant pas être mises à jour, ont été renommés [types d’entités sans clé](xref:core/modeling/keyless-entity-types).</span><span class="sxs-lookup"><span data-stu-id="f0911-151">Query types, which represent data that can be read from the database but not updated, have been renamed to [keyless entity types](xref:core/modeling/keyless-entity-types).</span></span>
<span data-ttu-id="f0911-152">Comme ils sont très bien adaptés au mappage d’affichages de bases de données dans la plupart des scénarios, EF Core crée désormais automatiquement des types d’entités sans clé lors de l’ingénierie à rebours d’affichages de base de données.</span><span class="sxs-lookup"><span data-stu-id="f0911-152">As they are an excellent fit for mapping database views in most scenarios, EF Core now automatically creates keyless entity types when reverse engineering database views.</span></span>

<span data-ttu-id="f0911-153">Par exemple, avec l’[outil dotnet ef command-line](xref:core/miscellaneous/cli/dotnet), vous pouvez saisir :</span><span class="sxs-lookup"><span data-stu-id="f0911-153">For example, using the [dotnet ef command-line tool](xref:core/miscellaneous/cli/dotnet) you can type:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="f0911-154">L’outil génère désormais automatiquement des types de vues et de tables sans clés :</span><span class="sxs-lookup"><span data-stu-id="f0911-154">And the tool will now automatically scaffold types for views and tables without keys:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Names>(entity =>
    {
        entity.HasNoKey();
        entity.ToView("Names");
    });

    modelBuilder.Entity<Things>(entity =>
    {
        entity.HasNoKey();
    });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="f0911-155">Les entités dépendantes qui partagent la table avec le principal sont maintenant facultatives</span><span class="sxs-lookup"><span data-stu-id="f0911-155">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="f0911-156">À partir d’EF Core 3.x, si `OrderDetails` est détenu par `Order` ou explicitement mappé à la même table, il est possible d’ajouter un `Order` sans `OrderDetails` : toutes les propriétés `OrderDetails` à l’exception de la clé primaire sont alors mappées à des colonnes pouvant accepter la valeur Null.</span><span class="sxs-lookup"><span data-stu-id="f0911-156">Starting with EF Core 3.x, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="f0911-157">Pendant l’interrogation, EF Core définit `OrderDetails` sur `null` si une de ses propriétés obligatoires n’a pas de valeur, ou s’il n’a pas de propriété obligatoire en plus de la clé primaire et que toutes les propriétés sont `null`.</span><span class="sxs-lookup"><span data-stu-id="f0911-157">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a><span data-ttu-id="f0911-158">EF 6.3 sur .NET Core</span><span class="sxs-lookup"><span data-stu-id="f0911-158">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="f0911-159">Même s’il ne s’agit pas véritablement d’une fonctionnalité d’EF Core 3.x, nous pensons qu’elle est importante pour beaucoup de nos clients actuels.</span><span class="sxs-lookup"><span data-stu-id="f0911-159">This isn't really an EF Core 3.x feature, but we think it is important to many of our current customers.</span></span>

<span data-ttu-id="f0911-160">Nous sommes conscients du fait que de nombreuses applications utilisent d’anciennes versions d’EF et qu’une migration vers EF Core dans l’unique objectif de tirer parti de .NET Core représente un effort considérable.</span><span class="sxs-lookup"><span data-stu-id="f0911-160">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can require a significant effort.</span></span>
<span data-ttu-id="f0911-161">C’est pourquoi nous avons décidé de déplacer la version la plus récente d’EF 6 de façon qu’elle s’exécute sur .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="f0911-161">For that reason, we decided to port the newest version of EF 6 to run on .NET Core 3.x.</span></span>

<span data-ttu-id="f0911-162">Pour plus d'informations, consultez les [nouveautés de la base de données dans EF 6](xref:ef6/what-is-new/index).</span><span class="sxs-lookup"><span data-stu-id="f0911-162">For more details, see [what's new in EF 6](xref:ef6/what-is-new/index).</span></span>

## <a name="postponed-features"></a><span data-ttu-id="f0911-163">Fonctionnalités différées</span><span class="sxs-lookup"><span data-stu-id="f0911-163">Postponed features</span></span>

<span data-ttu-id="f0911-164">Certaines fonctionnalités prévues à l’origine pour EF Core 3 ont été reportées pour de prochaines versions :</span><span class="sxs-lookup"><span data-stu-id="f0911-164">Some features originally planned for EF Core 3.x were postponed to future releases:</span></span>

- <span data-ttu-id="f0911-165">Capacité d’ignorer des parties d’un modèle lors de migrations, référence de suivi [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span><span class="sxs-lookup"><span data-stu-id="f0911-165">Ability to ignore parts of a model in migrations, tracked as [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="f0911-166">Entités des conteneurs de propriétés, suivies comme deux problèmes distincts : [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) pour les entités de type partagé et [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) pour le support du mappage de propriété.</span><span class="sxs-lookup"><span data-stu-id="f0911-166">Property bag entities, tracked as two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>