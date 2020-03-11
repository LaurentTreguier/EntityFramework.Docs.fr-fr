---
title: Nouveautés d’EF Core 2.0 - EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: 83f6b819409d502dba17a678d44a0746a4a77f4b
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417495"
---
# <a name="new-features-in-ef-core-20"></a><span data-ttu-id="62e99-102">Nouvelles fonctionnalités d’EF Core 2.0</span><span class="sxs-lookup"><span data-stu-id="62e99-102">New features in EF Core 2.0</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="62e99-103">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="62e99-103">.NET Standard 2.0</span></span>

<span data-ttu-id="62e99-104">EF Core cible désormais .NET Standard 2.0, ce qui signifie qu’il peut fonctionner avec .NET Core 2.0, .NET Framework 4.6.1 et d’autres bibliothèques qui implémentent .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="62e99-104">EF Core now targets .NET Standard 2.0, which means it can work with .NET Core 2.0, .NET Framework 4.6.1, and other libraries that implement .NET Standard 2.0.</span></span>
<span data-ttu-id="62e99-105">Pour plus d’informations sur ce qui est pris en charge, consultez [Implémentations .NET prises en charge](../platforms/index.md).</span><span class="sxs-lookup"><span data-stu-id="62e99-105">See [Supported .NET Implementations](../platforms/index.md) for more details on what is supported.</span></span>

## <a name="modeling"></a><span data-ttu-id="62e99-106">Modélisation</span><span class="sxs-lookup"><span data-stu-id="62e99-106">Modeling</span></span>

### <a name="table-splitting"></a><span data-ttu-id="62e99-107">Fractionnement de table</span><span class="sxs-lookup"><span data-stu-id="62e99-107">Table splitting</span></span>

<span data-ttu-id="62e99-108">Vous pouvez désormais mapper deux ou plusieurs types d’entité à la même table, où la ou les colonnes de clé primaire sont partagées et chaque ligne correspond à deux ou plusieurs entités.</span><span class="sxs-lookup"><span data-stu-id="62e99-108">It is now possible to map two or more entity types to the same table where the primary key column(s) will be shared and each row will correspond to two or more entities.</span></span>

<span data-ttu-id="62e99-109">Pour utiliser le fractionnement de table, vous devez configurer une relation d’identification (où les propriétés de clé étrangère forment la clé primaire) entre tous les types d’entité partageant la table :</span><span class="sxs-lookup"><span data-stu-id="62e99-109">To use table splitting an identifying relationship (where foreign key properties form the primary key) must be configured between all of the entity types sharing the table:</span></span>

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

<span data-ttu-id="62e99-110">Pour plus d’informations sur cette fonctionnalité, lisez la [section sur le fractionnement de table](xref:core/modeling/table-splitting).</span><span class="sxs-lookup"><span data-stu-id="62e99-110">Read the [section on table splitting](xref:core/modeling/table-splitting) for more information on this feature.</span></span>

### <a name="owned-types"></a><span data-ttu-id="62e99-111">Types détenus</span><span class="sxs-lookup"><span data-stu-id="62e99-111">Owned types</span></span>

<span data-ttu-id="62e99-112">Un type d’entité détenu peut partager le même type .NET avec un autre type d’entité détenu, mais étant donné qu’il ne peut pas être identifié uniquement par le type .NET, il doit y avoir une navigation à partir d’un autre type d’entité.</span><span class="sxs-lookup"><span data-stu-id="62e99-112">An owned entity type can share the same .NET type with another owned entity type, but since it cannot be identified just by the .NET type there must be a navigation to it from another entity type.</span></span> <span data-ttu-id="62e99-113">L’entité qui contient la navigation définie est le propriétaire.</span><span class="sxs-lookup"><span data-stu-id="62e99-113">The entity containing the defining navigation is the owner.</span></span> <span data-ttu-id="62e99-114">Quand le propriétaire fait l’objet d’une interrogation, les types détenus sont inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="62e99-114">When querying the owner the owned types will be included by default.</span></span>

<span data-ttu-id="62e99-115">Par convention, une clé primaire cachée est créée pour le type détenu et est mappée à la même table que le propriétaire à l’aide du fractionnement de table.</span><span class="sxs-lookup"><span data-stu-id="62e99-115">By convention a shadow primary key will be created for the owned type and it will be mapped to the same table as the owner by using table splitting.</span></span> <span data-ttu-id="62e99-116">Cela permet d’utiliser des types détenus à l’image des types complexes dans EF6 :</span><span class="sxs-lookup"><span data-stu-id="62e99-116">This allows to use owned types similarly to how complex types are used in EF6:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, cb =>
    {
        cb.OwnsOne(c => c.BillingAddress);
        cb.OwnsOne(c => c.ShippingAddress);
    });

public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```

<span data-ttu-id="62e99-117">Pour plus d’informations sur cette fonctionnalité, lisez la [section sur les types d’entités détenus](xref:core/modeling/owned-entities).</span><span class="sxs-lookup"><span data-stu-id="62e99-117">Read the [section on owned entity types](xref:core/modeling/owned-entities) for more information on this feature.</span></span>

### <a name="model-level-query-filters"></a><span data-ttu-id="62e99-118">Filtres de requête au niveau du modèle</span><span class="sxs-lookup"><span data-stu-id="62e99-118">Model-level query filters</span></span>

<span data-ttu-id="62e99-119">EF Core 2.0 inclut une nouvelle fonctionnalité que nous appelons « filtres de requête au niveau du modèle ».</span><span class="sxs-lookup"><span data-stu-id="62e99-119">EF Core 2.0 includes a new feature we call Model-level query filters.</span></span> <span data-ttu-id="62e99-120">Cette fonctionnalité permet aux prédicats de requête LINQ (expression booléenne généralement passée à l’opérateur de requête LINQ Where) d’être définis directement sur des types d’entité dans le modèle de métadonnées (généralement dans OnModelCreating).</span><span class="sxs-lookup"><span data-stu-id="62e99-120">This feature allows LINQ query predicates (a boolean expression typically passed to the LINQ Where query operator) to be defined directly on Entity Types in the metadata model (usually in OnModelCreating).</span></span> <span data-ttu-id="62e99-121">Ces filtres sont automatiquement appliqués à toutes les requêtes LINQ impliquant ces types d’entités, y compris ceux référencés indirectement, par exemple à l’aide d’Include ou de références de propriété de navigation directe.</span><span class="sxs-lookup"><span data-stu-id="62e99-121">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="62e99-122">Voici deux applications courantes de cette fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="62e99-122">Some common applications of this feature are:</span></span>

- <span data-ttu-id="62e99-123">Suppression réversible : un type d’entité définit une propriété IsDeleted.</span><span class="sxs-lookup"><span data-stu-id="62e99-123">Soft delete - An Entity Types defines an IsDeleted property.</span></span>
- <span data-ttu-id="62e99-124">Architecture multilocataire : un type d’entité définit une propriété TenantId.</span><span class="sxs-lookup"><span data-stu-id="62e99-124">Multi-tenancy - An Entity Type defines a TenantId property.</span></span>

<span data-ttu-id="62e99-125">Voici un exemple simple illustrant la fonctionnalité pour les deux scénarios répertoriés ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="62e99-125">Here is a simple example demonstrating the feature for the two scenarios listed above:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    public int TenantId { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>().HasQueryFilter(
            p => !p.IsDeleted
            && p.TenantId == this.TenantId);
    }
}
```

<span data-ttu-id="62e99-126">Nous définissons un filtre au niveau du modèle qui implémente une architecture multilocataire et une suppression réversible pour des instances du type d’entité `Post`.</span><span class="sxs-lookup"><span data-stu-id="62e99-126">We define a model-level filter that implements multi-tenancy and soft-delete for instances of the `Post` Entity Type.</span></span> <span data-ttu-id="62e99-127">Notez l’utilisation d’un `DbContext` propriété au niveau de l’instance : `TenantId`.</span><span class="sxs-lookup"><span data-stu-id="62e99-127">Note the use of a `DbContext` instance-level property: `TenantId`.</span></span> <span data-ttu-id="62e99-128">Les filtres au niveau du modèle utilisent la valeur de l’instance de contexte correcte (c’est-à-dire celle qui exécute la requête).</span><span class="sxs-lookup"><span data-stu-id="62e99-128">Model-level filters will use the value from the correct context instance (that is, the context instance that is executing the query).</span></span>

<span data-ttu-id="62e99-129">Les filtres peuvent être désactivés pour des requêtes LINQ individuelles à l’aide de l’opérateur IgnoreQueryFilters().</span><span class="sxs-lookup"><span data-stu-id="62e99-129">Filters may be disabled for individual LINQ queries using the IgnoreQueryFilters() operator.</span></span>

#### <a name="limitations"></a><span data-ttu-id="62e99-130">Limites</span><span class="sxs-lookup"><span data-stu-id="62e99-130">Limitations</span></span>

- <span data-ttu-id="62e99-131">Les références de navigation ne sont pas autorisées.</span><span class="sxs-lookup"><span data-stu-id="62e99-131">Navigation references are not allowed.</span></span> <span data-ttu-id="62e99-132">Cette fonctionnalité peut être ajoutée en fonction de vos commentaires.</span><span class="sxs-lookup"><span data-stu-id="62e99-132">This feature may be added based on feedback.</span></span>
- <span data-ttu-id="62e99-133">Les filtres ne peuvent être définis que sur le type d’entité racine d’une hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="62e99-133">Filters can only be defined on the root Entity Type of a hierarchy.</span></span>

### <a name="database-scalar-function-mapping"></a><span data-ttu-id="62e99-134">Mappage de fonctions scalaires de base de données</span><span class="sxs-lookup"><span data-stu-id="62e99-134">Database scalar function mapping</span></span>

<span data-ttu-id="62e99-135">EF Core 2.0 inclut une contribution importante de [Paul Middleton](https://github.com/pmiddleton) qui permet de mapper les fonctions scalaires de base de données à des stubs de méthode en vue de les utiliser dans des requêtes LINQ et de les convertir en SQL.</span><span class="sxs-lookup"><span data-stu-id="62e99-135">EF Core 2.0 includes an important contribution from [Paul Middleton](https://github.com/pmiddleton) which enables mapping database scalar functions to method stubs so that they can be used in LINQ queries and translated to SQL.</span></span>

<span data-ttu-id="62e99-136">Voici une brève description de l’utilisation de la fonctionnalité :</span><span class="sxs-lookup"><span data-stu-id="62e99-136">Here is a brief description of how the feature can be used:</span></span>

<span data-ttu-id="62e99-137">Déclarez une méthode statique sur votre `DbContext` et annotez-la avec `DbFunctionAttribute` :</span><span class="sxs-lookup"><span data-stu-id="62e99-137">Declare a static method on your `DbContext` and annotate it with `DbFunctionAttribute`:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new NotImplementedException();
    }
}
```

<span data-ttu-id="62e99-138">Les méthodes telles que celle-ci sont inscrites automatiquement.</span><span class="sxs-lookup"><span data-stu-id="62e99-138">Methods like this are automatically registered.</span></span> <span data-ttu-id="62e99-139">Une fois inscrits, les appels à la méthode dans une requête LINQ peuvent être traduits en appels de fonction dans SQL :</span><span class="sxs-lookup"><span data-stu-id="62e99-139">Once registered, calls to the method in a LINQ query can be translated to function calls in SQL:</span></span>

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

<span data-ttu-id="62e99-140">Points à noter :</span><span class="sxs-lookup"><span data-stu-id="62e99-140">A few things to note:</span></span>

- <span data-ttu-id="62e99-141">Par Convention, le nom de la méthode est utilisé comme nom d’une fonction (dans ce cas, une fonction définie par l’utilisateur) lors de la génération du SQL, mais vous pouvez remplacer le nom et le schéma lors de l’inscription de la méthode.</span><span class="sxs-lookup"><span data-stu-id="62e99-141">By convention the name of the method is used as the name of a function (in this case a user-defined function) when generating the SQL, but you can override the name and schema during method registration.</span></span>
- <span data-ttu-id="62e99-142">Actuellement, seules les fonctions scalaires sont prises en charge.</span><span class="sxs-lookup"><span data-stu-id="62e99-142">Currently only scalar functions are supported.</span></span>
- <span data-ttu-id="62e99-143">Vous devez créer la fonction mappée dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="62e99-143">You must create the mapped function in the database.</span></span> <span data-ttu-id="62e99-144">EF Core migrations ne prend pas en charge la création.</span><span class="sxs-lookup"><span data-stu-id="62e99-144">EF Core migrations will not take care of creating it.</span></span>

### <a name="self-contained-type-configuration-for-code-first"></a><span data-ttu-id="62e99-145">Configuration du type autonome pour Code First</span><span class="sxs-lookup"><span data-stu-id="62e99-145">Self-contained type configuration for code first</span></span>

<span data-ttu-id="62e99-146">Dans EF6, il était possible d’encapsuler la configuration Code First d’un type d’entité spécifique en effectuant une dérivation à partir *d’EntityTypeConfiguration*.</span><span class="sxs-lookup"><span data-stu-id="62e99-146">In EF6 it was possible to encapsulate the code first configuration of a specific entity type by deriving from *EntityTypeConfiguration*.</span></span> <span data-ttu-id="62e99-147">Dans EF Core 2.0, nous reprenons ce modèle :</span><span class="sxs-lookup"><span data-stu-id="62e99-147">In EF Core 2.0 we are bringing this pattern back:</span></span>

``` csharp
class CustomerConfiguration : IEntityTypeConfiguration<Customer>
{
    public void Configure(EntityTypeBuilder<Customer> builder)
    {
        builder.HasKey(c => c.AlternateKey);
        builder.Property(c => c.Name).HasMaxLength(200);
    }
}

...
// OnModelCreating
builder.ApplyConfiguration(new CustomerConfiguration());
```

## <a name="high-performance"></a><span data-ttu-id="62e99-148">Hautes performances</span><span class="sxs-lookup"><span data-stu-id="62e99-148">High Performance</span></span>

### <a name="dbcontext-pooling"></a><span data-ttu-id="62e99-149">Regroupement DbContext</span><span class="sxs-lookup"><span data-stu-id="62e99-149">DbContext pooling</span></span>

<span data-ttu-id="62e99-150">En règle générale, le modèle de base pour l’utilisation d’EF Core dans une application ASP.NET Core implique l’inscription d’un type DbContext personnalisé dans le système d’injection de dépendances, puis l’obtention d’instances de ce type par le biais de paramètres de constructeur dans les contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="62e99-150">The basic pattern for using EF Core in an ASP.NET Core application usually involves registering a custom DbContext type into the dependency injection system and later obtaining instances of that type through constructor parameters in controllers.</span></span> <span data-ttu-id="62e99-151">Cela signifie qu’une instance de DbContext est créée pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="62e99-151">This means a new instance of the DbContext is created for each requests.</span></span>

<span data-ttu-id="62e99-152">La version 2.0 offre une nouvelle façon d’inscrire des types DbContext personnalisés dans l’injection de dépendances, qui introduit en toute transparence un groupe d’instances de DbContext réutilisables.</span><span class="sxs-lookup"><span data-stu-id="62e99-152">In version 2.0 we are introducing a new way to register custom DbContext types in dependency injection which transparently introduces a pool of reusable DbContext instances.</span></span> <span data-ttu-id="62e99-153">Pour utiliser le regroupement DbContext, utilisez `AddDbContextPool` au lieu d’`AddDbContext` durant l’inscription des services :</span><span class="sxs-lookup"><span data-stu-id="62e99-153">To use DbContext pooling, use the `AddDbContextPool` instead of `AddDbContext` during service registration:</span></span>

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="62e99-154">Si cette méthode est utilisée, au moment où une instance de DbContext est demandée par un contrôleur, nous vérifions d’abord s’il existe une instance disponible dans le pool.</span><span class="sxs-lookup"><span data-stu-id="62e99-154">If this method is used, at the time a DbContext instance is requested by a controller we will first check if there is an instance available in the pool.</span></span> <span data-ttu-id="62e99-155">Une fois finalisé le traitement de la demande, tout état sur l’instance est réinitialisé et l’instance elle-même est retournée au pool.</span><span class="sxs-lookup"><span data-stu-id="62e99-155">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="62e99-156">Ce regroupement est conceptuellement semblable au regroupement de connexions dans les fournisseurs ADO.NET et présente l’avantage de réduire les coûts d’initialisation de l’instance de DbContext.</span><span class="sxs-lookup"><span data-stu-id="62e99-156">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of DbContext instance.</span></span>

### <a name="limitations"></a><span data-ttu-id="62e99-157">Limites</span><span class="sxs-lookup"><span data-stu-id="62e99-157">Limitations</span></span>

<span data-ttu-id="62e99-158">La nouvelle méthode présente quelques limitations quant à ce qui peut être effectué dans la méthode `OnConfiguring()` de DbContext.</span><span class="sxs-lookup"><span data-stu-id="62e99-158">The new method introduces a few limitations on what can be done in the `OnConfiguring()` method of the DbContext.</span></span>

> [!WARNING]  
> <span data-ttu-id="62e99-159">Évitez d’utiliser le regroupement DbContext si vous gérez votre propre état (par exemple, champs privés) dans votre classe DbContext dérivée qui ne doit pas être partagée entre les requêtes.</span><span class="sxs-lookup"><span data-stu-id="62e99-159">Avoid using DbContext Pooling if you maintain your own state (for example, private fields) in your derived DbContext class that should not be shared across requests.</span></span> <span data-ttu-id="62e99-160">EF Core réinitialise uniquement l’état dont il a connaissance avant d’ajouter une instance de DbContext au pool.</span><span class="sxs-lookup"><span data-stu-id="62e99-160">EF Core will only reset the state that is aware of before adding a DbContext instance to the pool.</span></span>

### <a name="explicitly-compiled-queries"></a><span data-ttu-id="62e99-161">Requêtes compilées explicitement</span><span class="sxs-lookup"><span data-stu-id="62e99-161">Explicitly compiled queries</span></span>

<span data-ttu-id="62e99-162">Il s’agit de la deuxième fonctionnalité de gain de performance à utiliser au choix conçue pour offrir des avantages dans les scénarios à grande échelle.</span><span class="sxs-lookup"><span data-stu-id="62e99-162">This is the second opt-in performance feature designed to offer benefits in high-scale scenarios.</span></span>

<span data-ttu-id="62e99-163">Les API de requête manuelles ou compilées explicitement étaient disponibles dans les versions précédentes d’EF, ainsi que dans LINQ to SQL, pour permettre aux applications de mettre en cache la conversion des requêtes afin qu’elles puissent être calculées une seule fois et exécutées plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="62e99-163">Manual or explicitly compiled query APIs have been available in previous versions of EF and also in LINQ to SQL to allow applications to cache the translation of queries so that they can be computed only once and executed many times.</span></span>

<span data-ttu-id="62e99-164">Bien qu’en général EF Core puisse automatiquement compiler et mettre en cache les requêtes en fonction d’une représentation hachée des expressions de requête, ce mécanisme peut être utilisé pour obtenir un petit gain de performances en contournant le calcul du hachage et la recherche dans le cache, ce qui permet à l’application d’utiliser une requête déjà compilée par le biais de l’appel d’un délégué.</span><span class="sxs-lookup"><span data-stu-id="62e99-164">Although in general EF Core can automatically compile and cache queries based on a hashed representation of the query expressions, this mechanism can be used to obtain a small performance gain by bypassing the computation of the hash and the cache lookup, allowing the application to use an already compiled query through the invocation of a delegate.</span></span>

``` csharp
// Create an explicitly compiled query
private static Func<CustomerContext, int, Customer> _customerById =
    EF.CompileQuery((CustomerContext db, int id) =>
        db.Customers
            .Include(c => c.Address)
            .Single(c => c.Id == id));

// Use the compiled query by invoking it
using (var db = new CustomerContext())
{
   var customer = _customerById(db, 147);
}
```

## <a name="change-tracking"></a><span data-ttu-id="62e99-165">Suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="62e99-165">Change Tracking</span></span>

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a><span data-ttu-id="62e99-166">Attach peut suivre un graphique des entités nouvelles et existantes</span><span class="sxs-lookup"><span data-stu-id="62e99-166">Attach can track a graph of new and existing entities</span></span>

<span data-ttu-id="62e99-167">EF Core prend en charge la génération automatique des valeurs de clés par le biais d’une variété de mécanismes.</span><span class="sxs-lookup"><span data-stu-id="62e99-167">EF Core supports automatic generation of key values through a variety of mechanisms.</span></span> <span data-ttu-id="62e99-168">Quand vous utilisez cette fonctionnalité, une valeur est générée si la propriété de clé est la valeur par défaut du CLR, généralement zéro ou null.</span><span class="sxs-lookup"><span data-stu-id="62e99-168">When using this feature, a value is generated if the key property is the CLR default--usually zero or null.</span></span> <span data-ttu-id="62e99-169">Cela signifie qu’un graphique d’entités peut être transmis à `DbContext.Attach` ou `DbSet.Attach` et qu’EF Core marque les entités qui ont déjà une clé définie comme `Unchanged` tandis que les entités qui n’ont pas de clé définie sont marquées comme `Added`.</span><span class="sxs-lookup"><span data-stu-id="62e99-169">This means that a graph of entities can be passed to `DbContext.Attach` or `DbSet.Attach` and EF Core will mark those entities that have a key already set as `Unchanged` while those entities that do not have a key set will be marked as `Added`.</span></span> <span data-ttu-id="62e99-170">Il est ainsi plus facile de joindre un graphique regroupant des entités nouvelles et existantes quand des clés générées sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="62e99-170">This makes it easy to attach a graph of mixed new and existing entities when using generated keys.</span></span> <span data-ttu-id="62e99-171">`DbContext.Update` et `DbSet.Update` fonctionnent de la même manière, à la différence que les entités ayant une clé définie sont marquées comme `Modified` au lieu de `Unchanged`.</span><span class="sxs-lookup"><span data-stu-id="62e99-171">`DbContext.Update` and `DbSet.Update` work in the same way, except that entities with a key set are marked as `Modified` instead of `Unchanged`.</span></span>

## <a name="query"></a><span data-ttu-id="62e99-172">Requête</span><span class="sxs-lookup"><span data-stu-id="62e99-172">Query</span></span>

### <a name="improved-linq-translation"></a><span data-ttu-id="62e99-173">Traduction LINQ améliorée</span><span class="sxs-lookup"><span data-stu-id="62e99-173">Improved LINQ translation</span></span>

<span data-ttu-id="62e99-174">Permet l’exécution de davantage de requêtes, avec davantage de logique évaluée dans la base de données (plutôt qu’en mémoire) et moins de données inutilement récupérées de la base de données.</span><span class="sxs-lookup"><span data-stu-id="62e99-174">Enables more queries to successfully execute, with more logic being evaluated in the database (rather than in-memory) and less data unnecessarily being retrieved from the database.</span></span>

### <a name="groupjoin-improvements"></a><span data-ttu-id="62e99-175">Améliorations apportées aux jonctions de groupes</span><span class="sxs-lookup"><span data-stu-id="62e99-175">GroupJoin improvements</span></span>

<span data-ttu-id="62e99-176">Le SQL généré pour les jonctions de groupes est amélioré.</span><span class="sxs-lookup"><span data-stu-id="62e99-176">This work improves the SQL that is generated for group joins.</span></span> <span data-ttu-id="62e99-177">Les jonctions de groupes sont souvent le résultat de sous-requêtes sur des propriétés de navigation facultatives.</span><span class="sxs-lookup"><span data-stu-id="62e99-177">Group joins are most often a result of sub-queries on optional navigation properties.</span></span>

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a><span data-ttu-id="62e99-178">Interpolation de chaîne dans FromSql et ExecuteSqlCommand</span><span class="sxs-lookup"><span data-stu-id="62e99-178">String interpolation in FromSql and ExecuteSqlCommand</span></span>

<span data-ttu-id="62e99-179">C# 6 a introduit l’interpolation de chaîne, fonctionnalité qui permet d’incorporer les expressions C# directement dans des littéraux de chaîne, offrant ainsi un moyen agréable de générer des chaînes au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="62e99-179">C# 6 introduced String Interpolation, a feature that allows C# expressions to be directly embedded in string literals, providing a nice way of building strings at runtime.</span></span> <span data-ttu-id="62e99-180">Dans EF Core 2.0, nous avons ajouté une prise en charge spéciale pour les chaînes interpolées à nos deux API principales qui acceptent des chaînes SQL brutes : `FromSql` et `ExecuteSqlCommand`.</span><span class="sxs-lookup"><span data-stu-id="62e99-180">In EF Core 2.0 we added special support for interpolated strings to our two primary APIs that accept raw SQL strings: `FromSql` and `ExecuteSqlCommand`.</span></span> <span data-ttu-id="62e99-181">Cette nouvelle prise en C# charge permet d’utiliser l’interpolation de chaîne en mode « sécurisé ».</span><span class="sxs-lookup"><span data-stu-id="62e99-181">This new support allows C# string interpolation to be used in a "safe" manner.</span></span> <span data-ttu-id="62e99-182">c’est-à-dire d’une façon qui protège contre les erreurs d’injection SQL courantes pouvant se produire pendant la construction dynamique du code SQL au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="62e99-182">That is, in a way that protects against common SQL injection mistakes that can occur when dynamically constructing SQL at runtime.</span></span>

<span data-ttu-id="62e99-183">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="62e99-183">Here is an example:</span></span>

``` csharp
var city = "London";
var contactTitle = "Sales Representative";

using (var context = CreateContext())
{
    context.Set<Customer>()
        .FromSql($@"
            SELECT *
            FROM ""Customers""
            WHERE ""City"" = {city} AND
                ""ContactTitle"" = {contactTitle}")
            .ToArray();
  }
```

<span data-ttu-id="62e99-184">Dans cet exemple, il existe deux variables incorporées dans la chaîne de format SQL.</span><span class="sxs-lookup"><span data-stu-id="62e99-184">In this example, there are two variables embedded in the SQL format string.</span></span> <span data-ttu-id="62e99-185">EF Core génère le code SQL suivant :</span><span class="sxs-lookup"><span data-stu-id="62e99-185">EF Core will produce the following SQL:</span></span>

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a><span data-ttu-id="62e99-186">EF.Functions.Like()</span><span class="sxs-lookup"><span data-stu-id="62e99-186">EF.Functions.Like()</span></span>

<span data-ttu-id="62e99-187">Nous avons ajouté la propriété EF.Functions qui peut être utilisée par EF Core ou des fournisseurs pour définir des méthodes mappées à des fonctions ou des opérateurs de base de données afin qu’elles puissent être appelées dans les requêtes LINQ.</span><span class="sxs-lookup"><span data-stu-id="62e99-187">We have added the EF.Functions property which can be used by EF Core or providers to define methods that map to database functions or operators so that those can be invoked in LINQ queries.</span></span> <span data-ttu-id="62e99-188">Le premier exemple de ce type de méthode est Like() :</span><span class="sxs-lookup"><span data-stu-id="62e99-188">The first example of such a method is Like():</span></span>

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

<span data-ttu-id="62e99-189">Notez que Like() est fourni avec une implémentation en mémoire, ce qui peut être pratique quand vous utilisez une base de données en mémoire ou que l’évaluation du prédicat doit se produire côté client.</span><span class="sxs-lookup"><span data-stu-id="62e99-189">Note that Like() comes with an in-memory implementation, which can be handy when working against an in-memory database or when evaluation of the predicate needs to occur on the client side.</span></span>

## <a name="database-management"></a><span data-ttu-id="62e99-190">Gestion de bases de données</span><span class="sxs-lookup"><span data-stu-id="62e99-190">Database management</span></span>

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a><span data-ttu-id="62e99-191">Crochet de pluralisation pour la génération de modèles de DbContext</span><span class="sxs-lookup"><span data-stu-id="62e99-191">Pluralization hook for DbContext scaffolding</span></span>

<span data-ttu-id="62e99-192">EF Core 2.0 introduit un nouveau service *IPluralizer* qui est utilisé pour singulariser les noms des types d’entité et pluraliser les noms DbSet.</span><span class="sxs-lookup"><span data-stu-id="62e99-192">EF Core 2.0 introduces a new *IPluralizer* service that is used to singularize entity type names and pluralize DbSet names.</span></span> <span data-ttu-id="62e99-193">L’implémentation par défaut étant l’absence d’opération, il s’agit simplement d’un dispositif permettant à l’utilisateur d’incorporer facilement son propre pluraliseur.</span><span class="sxs-lookup"><span data-stu-id="62e99-193">The default implementation is a no-op, so this is just a hook where folks can easily plug in their own pluralizer.</span></span>

<span data-ttu-id="62e99-194">Voici à quoi cela ressemble dans le cas d’un développeur souhaitant mettre en place son propre pluraliseur :</span><span class="sxs-lookup"><span data-stu-id="62e99-194">Here is what it looks like for a developer to hook in their own pluralizer:</span></span>

``` csharp
public class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
    {
        services.AddSingleton<IPluralizer, MyPluralizer>();
    }
}

public class MyPluralizer : IPluralizer
{
    public string Pluralize(string name)
    {
        return Inflector.Inflector.Pluralize(name) ?? name;
    }

    public string Singularize(string name)
    {
        return Inflector.Inflector.Singularize(name) ?? name;
    }
}
```

## <a name="others"></a><span data-ttu-id="62e99-195">Autres</span><span class="sxs-lookup"><span data-stu-id="62e99-195">Others</span></span>

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a><span data-ttu-id="62e99-196">Déplacement du fournisseur ADO.NET SQLite vers SQLitePCL.raw</span><span class="sxs-lookup"><span data-stu-id="62e99-196">Move ADO.NET SQLite provider to SQLitePCL.raw</span></span>

<span data-ttu-id="62e99-197">Nous disposons ainsi d’une solution plus fiable dans Microsoft.Data.Sqlite pour distribuer des fichiers binaires SQLite natifs sur différentes plateformes.</span><span class="sxs-lookup"><span data-stu-id="62e99-197">This gives us a more robust solution in Microsoft.Data.Sqlite for distributing native SQLite binaries on different platforms.</span></span>

### <a name="only-one-provider-per-model"></a><span data-ttu-id="62e99-198">Un seul fournisseur par modèle</span><span class="sxs-lookup"><span data-stu-id="62e99-198">Only one provider per model</span></span>

<span data-ttu-id="62e99-199">Renforce considérablement les possibilités d’interaction des fournisseurs avec le modèle et simplifie le fonctionnement des conventions, annotations et API Fluent avec les différents fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="62e99-199">Significantly augments how providers can interact with the model and simplifies how conventions, annotations and fluent APIs work with different providers.</span></span>

<span data-ttu-id="62e99-200">EF Core 2.0 génère désormais un [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) différent par fournisseur utilisé.</span><span class="sxs-lookup"><span data-stu-id="62e99-200">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="62e99-201">Cela est généralement transparent pour l’application.</span><span class="sxs-lookup"><span data-stu-id="62e99-201">This is usually transparent to the application.</span></span> <span data-ttu-id="62e99-202">Il en résulte une simplification des API de métadonnées de niveau inférieur, au point que tout accès aux *concepts de métadonnées relationnelles communs* est toujours établi par le biais d’un appel à `.Relational` au lieu de `.SqlServer`, `.Sqlite`, etc.</span><span class="sxs-lookup"><span data-stu-id="62e99-202">This has facilitated a simplification of lower-level metadata APIs such that any access to *common relational metadata concepts* is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc.</span></span>

### <a name="consolidated-logging-and-diagnostics"></a><span data-ttu-id="62e99-203">Journalisation et diagnostics consolidés</span><span class="sxs-lookup"><span data-stu-id="62e99-203">Consolidated logging and diagnostics</span></span>

<span data-ttu-id="62e99-204">Les mécanismes de journalisation (basés sur ILogger) et de diagnostics (basés sur DiagnosticSource) partagent désormais plus de code.</span><span class="sxs-lookup"><span data-stu-id="62e99-204">Logging (based on ILogger) and Diagnostics (based on DiagnosticSource) mechanisms now share more code.</span></span>

<span data-ttu-id="62e99-205">Les ID d’événement pour les messages envoyés à un ILogger ont été changés dans la version 2.0.</span><span class="sxs-lookup"><span data-stu-id="62e99-205">The event IDs for messages sent to an ILogger have changed in 2.0.</span></span> <span data-ttu-id="62e99-206">Les ID d’événement sont maintenant uniques dans le code EF Core.</span><span class="sxs-lookup"><span data-stu-id="62e99-206">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="62e99-207">En outre, ces messages suivent désormais le modèle standard de la journalisation structurée utilisé, par exemple, par le modèle MVC.</span><span class="sxs-lookup"><span data-stu-id="62e99-207">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="62e99-208">Les catégories d’enregistreurs d’événements ont également changé.</span><span class="sxs-lookup"><span data-stu-id="62e99-208">Logger categories have also changed.</span></span> <span data-ttu-id="62e99-209">Il existe désormais un jeu connu de catégories accessibles par le biais de [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).</span><span class="sxs-lookup"><span data-stu-id="62e99-209">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="62e99-210">Les événements DiagnosticSource utilisent désormais les mêmes noms d’ID d’événement que les messages `ILogger` correspondants.</span><span class="sxs-lookup"><span data-stu-id="62e99-210">DiagnosticSource events now use the same event ID names as the corresponding `ILogger` messages.</span></span>
