---
title: Modifications avec rupture dans EF Core 5,0-EF Core
description: Liste complète des modifications avec rupture introduites dans Entity Framework Core 5,0
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 3a00c2fdce51ff8b7d76b34f7003dc8ec0618830
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429284"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="697ed-103">Modifications avec rupture dans EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="697ed-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="697ed-104">Les modifications d’API et de comportement suivantes peuvent bloquer les mises à jour des applications existantes en EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="697ed-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="697ed-105">Résumé</span><span class="sxs-lookup"><span data-stu-id="697ed-105">Summary</span></span>

| <span data-ttu-id="697ed-106">**Modification critique**</span><span class="sxs-lookup"><span data-stu-id="697ed-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="697ed-107">**Impact**</span><span class="sxs-lookup"><span data-stu-id="697ed-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="697ed-108">Obligatoire sur la navigation du principal au dépendant a une sémantique différente</span><span class="sxs-lookup"><span data-stu-id="697ed-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="697ed-109">Moyenne</span><span class="sxs-lookup"><span data-stu-id="697ed-109">Medium</span></span>     |
| [<span data-ttu-id="697ed-110">La définition de la requête est remplacée par des méthodes spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="697ed-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="697ed-111">Moyenne</span><span class="sxs-lookup"><span data-stu-id="697ed-111">Medium</span></span>     |
| [<span data-ttu-id="697ed-112">Les navigations de référence non NULL ne sont pas remplacées par les requêtes</span><span class="sxs-lookup"><span data-stu-id="697ed-112">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="697ed-113">Moyenne</span><span class="sxs-lookup"><span data-stu-id="697ed-113">Medium</span></span>     |
| [<span data-ttu-id="697ed-114">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="697ed-114">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="697ed-115">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-115">Low</span></span>        |
| [<span data-ttu-id="697ed-116">Cosmos : la clé de partition est maintenant ajoutée à la clé primaire</span><span class="sxs-lookup"><span data-stu-id="697ed-116">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="697ed-117">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-117">Low</span></span>        |
| [<span data-ttu-id="697ed-118">Cosmos : `id` propriété renommée en `__id`</span><span class="sxs-lookup"><span data-stu-id="697ed-118">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="697ed-119">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-119">Low</span></span>        |
| <span data-ttu-id="697ed-120">[Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="697ed-120">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="697ed-121">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-121">Low</span></span>        |
| [<span data-ttu-id="697ed-122">Cosmos : GetPropertyName et SetPropertyName ont été renommés</span><span class="sxs-lookup"><span data-stu-id="697ed-122">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="697ed-123">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-123">Low</span></span>        |
| [<span data-ttu-id="697ed-124">Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé</span><span class="sxs-lookup"><span data-stu-id="697ed-124">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="697ed-125">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-125">Low</span></span>        |
| [<span data-ttu-id="697ed-126">IMigrationsModelDiffer utilise désormais IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="697ed-126">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="697ed-127">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-127">Low</span></span>        |
| [<span data-ttu-id="697ed-128">Les discriminateurs sont en lecture seule</span><span class="sxs-lookup"><span data-stu-id="697ed-128">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="697ed-129">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-129">Low</span></span>        |
| [<span data-ttu-id="697ed-130">EF spécifique au fournisseur. Les méthodes Functions lèvent pour le fournisseur InMemory</span><span class="sxs-lookup"><span data-stu-id="697ed-130">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="697ed-131">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-131">Low</span></span>        |
| [<span data-ttu-id="697ed-132">IndexBuilder. HasName est désormais obsolète</span><span class="sxs-lookup"><span data-stu-id="697ed-132">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="697ed-133">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-133">Low</span></span>        |
| [<span data-ttu-id="697ed-134">Un pluarlizer est maintenant inclus pour la génération de modèles automatique de modèles rétroconçus</span><span class="sxs-lookup"><span data-stu-id="697ed-134">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="697ed-135">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-135">Low</span></span>        |
| [<span data-ttu-id="697ed-136">INavigationBase remplace INavigation dans certaines API pour prendre en charge les navigations ignorées</span><span class="sxs-lookup"><span data-stu-id="697ed-136">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="697ed-137">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-137">Low</span></span>        |
| [<span data-ttu-id="697ed-138">Certaines requêtes avec une collection corrélée qui utilisent également `Distinct` ou `GroupBy` ne sont plus prises en charge</span><span class="sxs-lookup"><span data-stu-id="697ed-138">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="697ed-139">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-139">Low</span></span>        |
| [<span data-ttu-id="697ed-140">L’utilisation d’une collection de type interrogeable dans la projection n’est pas prise en charge</span><span class="sxs-lookup"><span data-stu-id="697ed-140">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="697ed-141">Faible</span><span class="sxs-lookup"><span data-stu-id="697ed-141">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="697ed-142">Modifications à moyenne impact</span><span class="sxs-lookup"><span data-stu-id="697ed-142">Medium-impact changes</span></span>

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="697ed-143">Obligatoire sur la navigation du principal au dépendant a une sémantique différente</span><span class="sxs-lookup"><span data-stu-id="697ed-143">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="697ed-144">#17286 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-144">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-145">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-145">Old behavior</span></span>

<span data-ttu-id="697ed-146">Seules les navigations vers le principal peuvent être configurées selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="697ed-146">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="697ed-147">Par conséquent, l’utilisation `RequiredAttribute` de sur la navigation vers le dépendant (l’entité qui contient la clé étrangère) créerait à la place la clé étrangère sur le type d’entité de définition.</span><span class="sxs-lookup"><span data-stu-id="697ed-147">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-148">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-148">New behavior</span></span>

<span data-ttu-id="697ed-149">Avec la prise en charge supplémentaire des dépendants requis, il est désormais possible de marquer toute navigation de référence comme étant obligatoire, ce qui signifie que dans le cas indiqué au-dessus de la clé étrangère sera défini de l’autre côté de la relation et que les propriétés ne seront pas marquées comme obligatoires.</span><span class="sxs-lookup"><span data-stu-id="697ed-149">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="697ed-150">`IsRequired`L’appel de avant la spécification de la terminaison dépendante est maintenant ambigu :</span><span class="sxs-lookup"><span data-stu-id="697ed-150">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="697ed-151">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-151">Why</span></span>

<span data-ttu-id="697ed-152">Le nouveau comportement est nécessaire pour activer la prise en charge des dépendants requis ([voir #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="697ed-152">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-153">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-153">Mitigations</span></span>

<span data-ttu-id="697ed-154">Supprimez `RequiredAttribute` de la navigation vers le dépendant et placez-le à la place sur la navigation vers le principal ou configurez la relation dans `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="697ed-154">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="697ed-155">La définition de la requête est remplacée par des méthodes spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="697ed-155">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="697ed-156">#18903 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-156">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-157">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-157">Old behavior</span></span>

<span data-ttu-id="697ed-158">Les types d’entité ont été mappés à la définition de requêtes au niveau du noyau.</span><span class="sxs-lookup"><span data-stu-id="697ed-158">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="697ed-159">Chaque fois que le type d’entité a été utilisé dans la racine de la requête du type d’entité a été remplacé par la requête de définition pour n’importe quel fournisseur.</span><span class="sxs-lookup"><span data-stu-id="697ed-159">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-160">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-160">New behavior</span></span>

<span data-ttu-id="697ed-161">Les API pour la définition de la requête sont déconseillées.</span><span class="sxs-lookup"><span data-stu-id="697ed-161">APIs for defining query are deprecated.</span></span> <span data-ttu-id="697ed-162">De nouvelles API spécifiques au fournisseur ont été introduites.</span><span class="sxs-lookup"><span data-stu-id="697ed-162">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-163">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-163">Why</span></span>

<span data-ttu-id="697ed-164">Alors que la définition de requêtes a été implémentée en tant que requête de remplacement chaque fois que la racine de la requête est utilisée dans la requête, il y a quelques problèmes :</span><span class="sxs-lookup"><span data-stu-id="697ed-164">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="697ed-165">Si la définition de la requête consiste à projeter un type d’entité à l’aide `new { ... }` de dans `Select` la méthode, vous devez ensuite identifier qu’en tant qu’entité a requis un travail supplémentaire et qu’elle est incohérente avec la façon dont EF Core traite les types nominaux dans la requête.</span><span class="sxs-lookup"><span data-stu-id="697ed-165">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="697ed-166">Pour les fournisseurs relationnels `FromSql` , il est toujours nécessaire de passer la chaîne SQL sous forme d’expression LINQ.</span><span class="sxs-lookup"><span data-stu-id="697ed-166">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="697ed-167">La définition initiale des requêtes a été introduite en tant que vues côté client à utiliser avec In-Memory fournisseur pour les entités keymoins (similaires aux vues de base de données dans les bases de données relationnelles).</span><span class="sxs-lookup"><span data-stu-id="697ed-167">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="697ed-168">Une telle définition facilite le test de l’application sur la base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="697ed-168">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="697ed-169">Par la suite, ils sont devenus largement applicables, ce qui était utile, mais incohérents et difficiles à comprendre.</span><span class="sxs-lookup"><span data-stu-id="697ed-169">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="697ed-170">Nous avons donc décidé de simplifier le concept.</span><span class="sxs-lookup"><span data-stu-id="697ed-170">So we decided to simplify the concept.</span></span> <span data-ttu-id="697ed-171">Nous avons fait en sorte que la requête de définition basée sur LINQ soit exclusive pour In-Memory fournisseur et les traite différemment.</span><span class="sxs-lookup"><span data-stu-id="697ed-171">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="697ed-172">Pour plus d’informations, [consultez ce problème](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="697ed-172">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-173">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-173">Mitigations</span></span>

<span data-ttu-id="697ed-174">Pour les fournisseurs relationnels, utilisez `ToSqlQuery` la méthode dans `OnModelCreating` et transmettez une chaîne SQL à utiliser pour le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="697ed-174">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="697ed-175">Pour le fournisseur In-Memory, utilisez la `ToInMemoryQuery` méthode dans `OnModelCreating` et transmettez une requête LINQ à utiliser pour le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="697ed-175">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="697ed-176">Les navigations de référence non NULL ne sont pas remplacées par les requêtes</span><span class="sxs-lookup"><span data-stu-id="697ed-176">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="697ed-177">#2693 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-177">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-178">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-178">Old behavior</span></span>

<span data-ttu-id="697ed-179">Dans EF Core 3,1, les navigations de référence initialisées de façon dynamique aux valeurs non null seraient parfois remplacées par des instances d’entité de la base de données, que les valeurs de clés correspondent ou non.</span><span class="sxs-lookup"><span data-stu-id="697ed-179">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="697ed-180">Toutefois, dans d’autres cas, EF Core 3,1 ferait l’inverse et laissent la valeur non null existante.</span><span class="sxs-lookup"><span data-stu-id="697ed-180">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-181">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-181">New behavior</span></span>

<span data-ttu-id="697ed-182">À compter de EF Core 5,0, les navigations de référence non NULL ne sont jamais remplacées par les instances retournées par une requête.</span><span class="sxs-lookup"><span data-stu-id="697ed-182">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="697ed-183">Notez que l’initialisation hâtif d’une _collection_ de navigation vers une collection vide est toujours prise en charge.</span><span class="sxs-lookup"><span data-stu-id="697ed-183">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-184">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-184">Why</span></span>

<span data-ttu-id="697ed-185">L’initialisation d’une propriété de navigation de référence en une instance d’entité « Empty » produit un État ambigu.</span><span class="sxs-lookup"><span data-stu-id="697ed-185">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="697ed-186">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="697ed-186">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="697ed-187">Normalement, une requête pour les blogs et les auteurs crée d’abord des `Blog` instances, puis définit les `Author` instances appropriées en fonction des données retournées par la base de données.</span><span class="sxs-lookup"><span data-stu-id="697ed-187">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="697ed-188">Toutefois, dans ce cas, chaque `Blog.Author` propriété est déjà initialisée sur un vide `Author` .</span><span class="sxs-lookup"><span data-stu-id="697ed-188">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="697ed-189">Sauf EF Core n’a aucun moyen de savoir que cette instance est « vide ».</span><span class="sxs-lookup"><span data-stu-id="697ed-189">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="697ed-190">Par conséquent, le remplacement de cette instance peut éventuellement lever une exception en silence `Author` .</span><span class="sxs-lookup"><span data-stu-id="697ed-190">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="697ed-191">Par conséquent, EF Core 5,0 ne remplace pas de manière cohérente une navigation déjà initialisée.</span><span class="sxs-lookup"><span data-stu-id="697ed-191">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="697ed-192">Ce nouveau comportement s’aligne également sur le comportement de EF6 dans la plupart des cas, bien que lors de l’investigation, nous ayons également trouvé certains cas d’incohérence dans EF6.</span><span class="sxs-lookup"><span data-stu-id="697ed-192">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>  

#### <a name="mitigations"></a><span data-ttu-id="697ed-193">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-193">Mitigations</span></span>

<span data-ttu-id="697ed-194">Si ce problème se produit, le correctif est l’arrêt de l’initialisation des propriétés de navigation de référence.</span><span class="sxs-lookup"><span data-stu-id="697ed-194">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="697ed-195">Modifications à faible impact</span><span class="sxs-lookup"><span data-stu-id="697ed-195">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="697ed-196">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="697ed-196">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="697ed-197">#14257 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-197">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-198">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-198">Old behavior</span></span>

<span data-ttu-id="697ed-199">HasGeometricDimension a été utilisé pour activer des dimensions supplémentaires (Z et M) sur les colonnes Geometry.</span><span class="sxs-lookup"><span data-stu-id="697ed-199">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="697ed-200">Toutefois, il n’a jamais été affecté par la création de bases de données.</span><span class="sxs-lookup"><span data-stu-id="697ed-200">However, it only ever affected database creation.</span></span> <span data-ttu-id="697ed-201">Il n’était pas nécessaire de le spécifier pour interroger des valeurs avec des dimensions supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="697ed-201">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="697ed-202">Elle ne fonctionnait pas non plus correctement lors de l’insertion ou de la mise à jour de valeurs avec des dimensions supplémentaires ([voir #14257](https://github.com/dotnet/efcore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="697ed-202">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-203">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-203">New behavior</span></span>

<span data-ttu-id="697ed-204">Pour permettre l’insertion et la mise à jour de valeurs géométriques avec des dimensions supplémentaires (Z et M), la dimension doit être spécifiée en tant que partie du nom du type de colonne.</span><span class="sxs-lookup"><span data-stu-id="697ed-204">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="697ed-205">Cette API correspond plus étroitement au comportement sous-jacent de la fonction AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="697ed-205">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-206">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-206">Why</span></span>

<span data-ttu-id="697ed-207">L’utilisation de HasGeometricDimension après avoir spécifié la dimension dans le type de colonne est inutile et redondante, donc nous avons supprimé HasGeometricDimension entièrement.</span><span class="sxs-lookup"><span data-stu-id="697ed-207">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-208">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-208">Mitigations</span></span>

<span data-ttu-id="697ed-209">Utilisez `HasColumnType` pour spécifier la dimension :</span><span class="sxs-lookup"><span data-stu-id="697ed-209">Use `HasColumnType` to specify the dimension:</span></span>

```csharp
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="697ed-210">Cosmos : la clé de partition est maintenant ajoutée à la clé primaire</span><span class="sxs-lookup"><span data-stu-id="697ed-210">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="697ed-211">#15289 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-211">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-212">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-212">Old behavior</span></span>

<span data-ttu-id="697ed-213">La propriété de clé de partition a été uniquement ajoutée à l’autre clé qui comprend `id` .</span><span class="sxs-lookup"><span data-stu-id="697ed-213">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-214">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-214">New behavior</span></span>

<span data-ttu-id="697ed-215">La propriété de clé de partition est maintenant également ajoutée à la clé primaire par Convention.</span><span class="sxs-lookup"><span data-stu-id="697ed-215">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-216">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-216">Why</span></span>

<span data-ttu-id="697ed-217">Cette modification rend le modèle mieux aligné avec Azure Cosmos DB sémantique et améliore les performances de `Find` et de certaines requêtes.</span><span class="sxs-lookup"><span data-stu-id="697ed-217">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-218">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-218">Mitigations</span></span>

<span data-ttu-id="697ed-219">Pour empêcher que la propriété de clé de partition soit ajoutée à la clé primaire, configurez-la dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="697ed-219">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="697ed-220">Cosmos : `id` propriété renommée en `__id`</span><span class="sxs-lookup"><span data-stu-id="697ed-220">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="697ed-221">#17751 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-221">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-222">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-222">Old behavior</span></span>

<span data-ttu-id="697ed-223">La propriété Shadow mappée à la `id` propriété JSON a également été nommée `id` .</span><span class="sxs-lookup"><span data-stu-id="697ed-223">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-224">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-224">New behavior</span></span>

<span data-ttu-id="697ed-225">La propriété Shadow créée par la Convention est maintenant nommée `__id` .</span><span class="sxs-lookup"><span data-stu-id="697ed-225">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-226">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-226">Why</span></span>

<span data-ttu-id="697ed-227">Cette modification rend moins probable la `id` propriété en conflit avec une propriété existante sur le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="697ed-227">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-228">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-228">Mitigations</span></span>

<span data-ttu-id="697ed-229">Pour revenir au comportement 3. x, configurez la `id` propriété dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="697ed-229">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="697ed-230">Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres</span><span class="sxs-lookup"><span data-stu-id="697ed-230">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="697ed-231">#17306 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-231">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-232">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-232">Old behavior</span></span>

<span data-ttu-id="697ed-233">Les propriétés de type Byte [] étaient stockées sous la forme d’un tableau de nombres.</span><span class="sxs-lookup"><span data-stu-id="697ed-233">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-234">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-234">New behavior</span></span>

<span data-ttu-id="697ed-235">Les propriétés de type Byte [] sont désormais stockées sous la forme d’une chaîne base64.</span><span class="sxs-lookup"><span data-stu-id="697ed-235">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-236">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-236">Why</span></span>

<span data-ttu-id="697ed-237">Cette représentation de Byte [] s’aligne mieux avec les attentes et est le comportement par défaut des principales bibliothèques de sérialisation JSON.</span><span class="sxs-lookup"><span data-stu-id="697ed-237">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-238">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-238">Mitigations</span></span>

<span data-ttu-id="697ed-239">Les données existantes stockées en tant que tableaux de nombres seront toujours interrogées correctement, mais il n’existe actuellement aucune méthode prise en charge pour modifier le comportement d’insertion.</span><span class="sxs-lookup"><span data-stu-id="697ed-239">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="697ed-240">Si cette limitation bloque votre scénario, commentez [-le](https://github.com/dotnet/efcore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="697ed-240">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="697ed-241">Cosmos : GetPropertyName et SetPropertyName ont été renommés</span><span class="sxs-lookup"><span data-stu-id="697ed-241">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="697ed-242">#17874 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-242">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-243">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-243">Old behavior</span></span>

<span data-ttu-id="697ed-244">Auparavant, les méthodes d’extension étaient appelées `GetPropertyName` et `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="697ed-244">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-245">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-245">New behavior</span></span>

<span data-ttu-id="697ed-246">L’ancienne API a été supprimée et de nouvelles méthodes ont été ajoutées : `GetJsonPropertyName` , `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="697ed-246">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-247">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-247">Why</span></span>

<span data-ttu-id="697ed-248">Cette modification supprime l’ambiguïté autour de la configuration de ces méthodes.</span><span class="sxs-lookup"><span data-stu-id="697ed-248">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-249">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-249">Mitigations</span></span>

<span data-ttu-id="697ed-250">Utilisez la nouvelle API.</span><span class="sxs-lookup"><span data-stu-id="697ed-250">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="697ed-251">Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé</span><span class="sxs-lookup"><span data-stu-id="697ed-251">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="697ed-252">#15289 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-252">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-253">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-253">Old behavior</span></span>

<span data-ttu-id="697ed-254">Les générateurs de valeur ont été appelés uniquement lorsque l’état de l’entité a été remplacé par ajouté.</span><span class="sxs-lookup"><span data-stu-id="697ed-254">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-255">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-255">New behavior</span></span>

<span data-ttu-id="697ed-256">Les générateurs de valeurs sont désormais appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé et que la propriété contient les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="697ed-256">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-257">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-257">Why</span></span>

<span data-ttu-id="697ed-258">Cette modification était nécessaire pour améliorer l’expérience des propriétés qui ne sont pas rendues persistantes dans le magasin de données et dont la valeur doit être générée systématiquement sur le client.</span><span class="sxs-lookup"><span data-stu-id="697ed-258">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-259">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-259">Mitigations</span></span>

<span data-ttu-id="697ed-260">Pour empêcher l’appel du générateur de valeurs, assignez une valeur non définie par défaut à la propriété avant la modification de l’État.</span><span class="sxs-lookup"><span data-stu-id="697ed-260">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="697ed-261">IMigrationsModelDiffer utilise désormais IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="697ed-261">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="697ed-262">#20305 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-262">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-263">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-263">Old behavior</span></span>

<span data-ttu-id="697ed-264">`IMigrationsModelDiffer` L’API a été définie à l’aide de `IModel` .</span><span class="sxs-lookup"><span data-stu-id="697ed-264">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-265">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-265">New behavior</span></span>

<span data-ttu-id="697ed-266">`IMigrationsModelDiffer` L’API utilise désormais `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="697ed-266">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="697ed-267">Toutefois, l’instantané de modèle contient toujours uniquement `IModel` , car ce code fait partie de l’application et Entity Framework ne peut pas le modifier sans apporter de modification plus importante.</span><span class="sxs-lookup"><span data-stu-id="697ed-267">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-268">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-268">Why</span></span>

<span data-ttu-id="697ed-269">`IRelationalModel` représentation qui vient d’être ajoutée au schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="697ed-269">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="697ed-270">Son utilisation pour trouver les différences est plus rapide et plus précise.</span><span class="sxs-lookup"><span data-stu-id="697ed-270">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-271">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-271">Mitigations</span></span>

<span data-ttu-id="697ed-272">Utilisez le code suivant pour comparer le modèle à `snapshot` l’aide du modèle à partir de `context` :</span><span class="sxs-lookup"><span data-stu-id="697ed-272">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```csharp
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

<span data-ttu-id="697ed-273">Nous envisageons d’améliorer cette expérience dans 6,0 ([voir #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="697ed-273">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="697ed-274">Les discriminateurs sont en lecture seule</span><span class="sxs-lookup"><span data-stu-id="697ed-274">Discriminators are read-only</span></span>

[<span data-ttu-id="697ed-275">#21154 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-275">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-276">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-276">Old behavior</span></span>

<span data-ttu-id="697ed-277">Il était possible de modifier la valeur du discriminateur avant d’appeler `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="697ed-277">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-278">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-278">New behavior</span></span>

<span data-ttu-id="697ed-279">Une exception sera levée dans le cas ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="697ed-279">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-280">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-280">Why</span></span>

<span data-ttu-id="697ed-281">EF ne s’attend pas à ce que le type d’entité change pendant qu’il est toujours suivi. la modification de la valeur de discriminateur laisse le contexte dans un état incohérent, ce qui peut entraîner un comportement inattendu.</span><span class="sxs-lookup"><span data-stu-id="697ed-281">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-282">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-282">Mitigations</span></span>

<span data-ttu-id="697ed-283">Si la modification de la valeur de discriminateur est nécessaire et que le contexte est supprimé immédiatement après l’appel `SaveChanges` de, le discriminateur peut devenir mutable :</span><span class="sxs-lookup"><span data-stu-id="697ed-283">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="697ed-284">EF spécifique au fournisseur. Les méthodes Functions lèvent pour le fournisseur InMemory</span><span class="sxs-lookup"><span data-stu-id="697ed-284">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="697ed-285">#20294 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-285">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-286">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-286">Old behavior</span></span>

<span data-ttu-id="697ed-287">EF spécifique au fournisseur. Les méthodes Functions contenaient une implémentation pour l’exécution du client, ce qui leur permettait d’être exécutées sur le fournisseur d’InMemory.</span><span class="sxs-lookup"><span data-stu-id="697ed-287">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="697ed-288">Par exemple, `EF.Functions.DateDiffDay` est une méthode propre à SQL Server, qui fonctionnait sur le fournisseur d’InMemory.</span><span class="sxs-lookup"><span data-stu-id="697ed-288">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-289">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-289">New behavior</span></span>

<span data-ttu-id="697ed-290">Les méthodes spécifiques au fournisseur ont été mises à jour pour lever une exception dans leur corps de méthode afin de bloquer leur évaluation côté client.</span><span class="sxs-lookup"><span data-stu-id="697ed-290">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-291">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-291">Why</span></span>

<span data-ttu-id="697ed-292">Les méthodes spécifiques au fournisseur sont mappées à une fonction de base de données.</span><span class="sxs-lookup"><span data-stu-id="697ed-292">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="697ed-293">Le calcul effectué par la fonction de base de données mappée ne peut pas toujours être répliqué côté client dans LINQ.</span><span class="sxs-lookup"><span data-stu-id="697ed-293">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="697ed-294">Cela peut provoquer la différence entre le résultat du serveur et l’exécution de la même méthode sur le client.</span><span class="sxs-lookup"><span data-stu-id="697ed-294">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="697ed-295">Étant donné que ces méthodes sont utilisées dans LINQ pour traduire des fonctions de base de données spécifiques, elles n’ont pas besoin d’être évaluées côté client.</span><span class="sxs-lookup"><span data-stu-id="697ed-295">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="697ed-296">Comme le fournisseur d’InMemory est une *base de données* différente, ces méthodes ne sont pas disponibles pour ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="697ed-296">As InMemory provider is a different *database* , these methods aren't available for this provider.</span></span> <span data-ttu-id="697ed-297">Si vous tentez de les exécuter pour le fournisseur d’InMemory ou tout autre fournisseur qui ne traduit pas ces méthodes, lève une exception.</span><span class="sxs-lookup"><span data-stu-id="697ed-297">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-298">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-298">Mitigations</span></span>

<span data-ttu-id="697ed-299">Étant donné qu’il n’existe aucun moyen de reproduire le comportement des fonctions de base de données avec précision, vous devez tester les requêtes qui les contiennent sur le même type de base de données qu’en production.</span><span class="sxs-lookup"><span data-stu-id="697ed-299">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="697ed-300">IndexBuilder. HasName est désormais obsolète</span><span class="sxs-lookup"><span data-stu-id="697ed-300">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="697ed-301">#21089 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-301">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-302">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-302">Old behavior</span></span>

<span data-ttu-id="697ed-303">Auparavant, un seul index pouvait être défini sur un ensemble de propriétés donné.</span><span class="sxs-lookup"><span data-stu-id="697ed-303">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="697ed-304">Le nom de la base de données d’un index a été configuré à l’aide de IndexBuilder. HasName.</span><span class="sxs-lookup"><span data-stu-id="697ed-304">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-305">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-305">New behavior</span></span>

<span data-ttu-id="697ed-306">Plusieurs index sont désormais autorisés sur le même jeu ou les mêmes propriétés.</span><span class="sxs-lookup"><span data-stu-id="697ed-306">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="697ed-307">Ces index sont à présent distingués par un nom dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="697ed-307">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="697ed-308">Par Convention, le nom du modèle est utilisé comme nom de la base de données ; Toutefois, il peut également être configuré indépendamment à l’aide de HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="697ed-308">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-309">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-309">Why</span></span>

<span data-ttu-id="697ed-310">À l’avenir, nous souhaitons activer à la fois les index croissants et les index décroissant avec différents classements sur le même ensemble de propriétés.</span><span class="sxs-lookup"><span data-stu-id="697ed-310">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="697ed-311">Cette modification nous déplace une autre étape dans cette direction.</span><span class="sxs-lookup"><span data-stu-id="697ed-311">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-312">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-312">Mitigations</span></span>

<span data-ttu-id="697ed-313">Tout code qui appelle précédemment IndexBuilder. HasName doit être mis à jour pour appeler HasDatabaseName à la place.</span><span class="sxs-lookup"><span data-stu-id="697ed-313">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="697ed-314">Si votre projet comprend des migrations générées avant EF Core version 2.0.0, vous pouvez ignorer en toute sécurité l’avertissement dans ces fichiers et le supprimer en ajoutant `#pragma warning disable 612, 618` .</span><span class="sxs-lookup"><span data-stu-id="697ed-314">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="697ed-315">Un pluraliseur est maintenant inclus pour la génération de modèles automatique de modèles rétroconçus</span><span class="sxs-lookup"><span data-stu-id="697ed-315">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="697ed-316">#11160 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-316">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-317">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-317">Old behavior</span></span>

<span data-ttu-id="697ed-318">Auparavant, vous deviez installer un package pluraliseur distinct afin de plurieler les noms de navigation des DbSet et des collections et de distinguer les noms de tables lors de la génération de modèles automatique de DbContext et de types d’entités en rétroconception d’un schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="697ed-318">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-319">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-319">New behavior</span></span>

<span data-ttu-id="697ed-320">EF Core comprend maintenant un pluraliseur qui utilise la bibliothèque [Humanizer](https://github.com/Humanizr/Humanizer) .</span><span class="sxs-lookup"><span data-stu-id="697ed-320">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="697ed-321">Il s’agit de la même bibliothèque que celle utilisée par Visual Studio pour recommander des noms de variables.</span><span class="sxs-lookup"><span data-stu-id="697ed-321">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-322">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-322">Why</span></span>

<span data-ttu-id="697ed-323">L’utilisation de plusieurs formes de mots pour les propriétés de collection et les formes singulières pour les types et les propriétés de référence est idiomatique dans .NET.</span><span class="sxs-lookup"><span data-stu-id="697ed-323">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-324">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-324">Mitigations</span></span>

<span data-ttu-id="697ed-325">Pour désactiver pluraliseur, utilisez l' `--no-pluralize` option sur `dotnet ef dbcontext scaffold` ou le `-NoPluralize` commutateur activé `Scaffold-DbContext` .</span><span class="sxs-lookup"><span data-stu-id="697ed-325">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="697ed-326">INavigationBase remplace INavigation dans certaines API pour prendre en charge les navigations ignorées</span><span class="sxs-lookup"><span data-stu-id="697ed-326">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="697ed-327">#2568 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-327">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="697ed-328">Ancien comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-328">Old behavior</span></span>

<span data-ttu-id="697ed-329">EF Core antérieures à 5,0 ne prenait en charge qu’une seule forme de propriété de navigation, représentée par l' `INavigation` interface.</span><span class="sxs-lookup"><span data-stu-id="697ed-329">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="697ed-330">Nouveau comportement</span><span class="sxs-lookup"><span data-stu-id="697ed-330">New behavior</span></span>

<span data-ttu-id="697ed-331">EF Core 5,0 introduit des relations plusieurs-à-plusieurs qui utilisent « ignorer les navigations ».</span><span class="sxs-lookup"><span data-stu-id="697ed-331">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="697ed-332">Celles-ci sont représentées par l' `ISkipNavigation` interface, et la plupart des fonctionnalités de `INavigation` ont été poussées vers une interface de base commune : `INavigationBase` .</span><span class="sxs-lookup"><span data-stu-id="697ed-332">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="697ed-333">Pourquoi</span><span class="sxs-lookup"><span data-stu-id="697ed-333">Why</span></span>

<span data-ttu-id="697ed-334">La plupart des fonctionnalités entre les navigations normale et ignorée sont identiques.</span><span class="sxs-lookup"><span data-stu-id="697ed-334">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="697ed-335">Toutefois, les navigations ignorées ont une relation différente avec les clés étrangères par rapport aux navigations normales, puisque les clés étrangères impliquées ne sont pas directement sur l’une ou l’autre des extrémités de la relation, mais plutôt dans l’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="697ed-335">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="697ed-336">Corrections</span><span class="sxs-lookup"><span data-stu-id="697ed-336">Mitigations</span></span>

<span data-ttu-id="697ed-337">Dans de nombreux cas, les applications peuvent basculer vers à l’aide de la nouvelle interface de base sans aucune autre modification.</span><span class="sxs-lookup"><span data-stu-id="697ed-337">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="697ed-338">Toutefois, dans les cas où la navigation est utilisée pour accéder aux propriétés de clé étrangère, le code d’application doit être restreint aux navigations normales, ou mis à jour pour effectuer la tâche appropriée à la fois pour les navigations normales et les navigations ignorées.</span><span class="sxs-lookup"><span data-stu-id="697ed-338">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="697ed-339">Certaines requêtes avec une collection corrélée qui utilisent également `Distinct` ou `GroupBy` ne sont plus prises en charge</span><span class="sxs-lookup"><span data-stu-id="697ed-339">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="697ed-340">#15873 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-340">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="697ed-341">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="697ed-341">**Old behavior**</span></span>

<span data-ttu-id="697ed-342">Auparavant, les requêtes impliquant des collections corrélées suivies de `GroupBy` , ainsi que certaines requêtes utilisant `Distinct` nous autorisant l’exécution.</span><span class="sxs-lookup"><span data-stu-id="697ed-342">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="697ed-343">Exemple GroupBy :</span><span class="sxs-lookup"><span data-stu-id="697ed-343">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="697ed-344">`Distinct` exemple : `Distinct` requêtes spécifiques où la projection de la collection interne ne contient pas la clé primaire :</span><span class="sxs-lookup"><span data-stu-id="697ed-344">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="697ed-345">Ces requêtes peuvent retourner des résultats incorrects si la collection interne contenait des doublons, mais fonctionnait correctement si tous les éléments de la collection interne étaient uniques.</span><span class="sxs-lookup"><span data-stu-id="697ed-345">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="697ed-346">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="697ed-346">**New behavior**</span></span>

<span data-ttu-id="697ed-347">Ces requêtes ne sont pas LOGREG pris en charge.</span><span class="sxs-lookup"><span data-stu-id="697ed-347">These queries are no loger suppored.</span></span> <span data-ttu-id="697ed-348">Une exception est levée et indique que nous n’avons pas assez d’informations pour générer correctement les résultats.</span><span class="sxs-lookup"><span data-stu-id="697ed-348">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="697ed-349">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="697ed-349">**Why**</span></span>

<span data-ttu-id="697ed-350">Pour les scénarios de collecte corrélée, nous avons besoin de connaître la clé primaire de l’entité pour affecter des entités de regroupement au parent approprié.</span><span class="sxs-lookup"><span data-stu-id="697ed-350">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="697ed-351">Lorsque la collection interne n’utilise pas `GroupBy` ou `Distinct` , la clé primaire manquante peut simplement être ajoutée à la projection.</span><span class="sxs-lookup"><span data-stu-id="697ed-351">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="697ed-352">Toutefois, dans le cas de `GroupBy` , `Distinct` elle ne peut pas être effectuée car elle modifie le résultat d’une `GroupBy` `Distinct` opération or.</span><span class="sxs-lookup"><span data-stu-id="697ed-352">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="697ed-353">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="697ed-353">**Mitigations**</span></span>

<span data-ttu-id="697ed-354">Réécrivez la requête de façon à ne pas utiliser les `GroupBy` `Distinct` opérations ou sur la collection interne, et effectuez ces opérations sur le client à la place.</span><span class="sxs-lookup"><span data-stu-id="697ed-354">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.GroupBy(c => c).Select(g => g.Key))
```

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.Distinct())
```

<a name="queryable-projection"></a>

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="697ed-355">L’utilisation d’une collection de type interrogeable dans la projection n’est pas prise en charge</span><span class="sxs-lookup"><span data-stu-id="697ed-355">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="697ed-356">#16314 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="697ed-356">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="697ed-357">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="697ed-357">**Old behavior**</span></span>

<span data-ttu-id="697ed-358">Auparavant, il était possible d’utiliser la collection d’un type pouvant être interrogé à l’intérieur de la projection dans certains cas, par exemple en tant qu’argument d’un `List<T>` constructeur :</span><span class="sxs-lookup"><span data-stu-id="697ed-358">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="697ed-359">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="697ed-359">**New behavior**</span></span>

<span data-ttu-id="697ed-360">Ces requêtes ne sont pas LOGREG pris en charge.</span><span class="sxs-lookup"><span data-stu-id="697ed-360">These queries are no loger suppored.</span></span> <span data-ttu-id="697ed-361">Une exception est levée, indiquant que nous ne pouvons pas créer un objet de type interrogeable et suggérer la manière dont cela peut être résolu.</span><span class="sxs-lookup"><span data-stu-id="697ed-361">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="697ed-362">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="697ed-362">**Why**</span></span>

<span data-ttu-id="697ed-363">Nous ne pouvons pas matérialiser un objet d’un type pouvant faire l’objet d’une requête. il est donc créé automatiquement à l’aide du `List<T>` type.</span><span class="sxs-lookup"><span data-stu-id="697ed-363">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="697ed-364">Cela entraînerait souvent une exception en raison d’une incompatibilité de type, ce qui n’était pas très clair et pourrait être surprenant pour certains utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="697ed-364">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="697ed-365">Nous avons décidé de reconnaître le modèle et de lever une exception plus significative.</span><span class="sxs-lookup"><span data-stu-id="697ed-365">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="697ed-366">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="697ed-366">**Mitigations**</span></span>

<span data-ttu-id="697ed-367">Ajouter `ToList()` un appel après l’objet interrogeable dans la projection :</span><span class="sxs-lookup"><span data-stu-id="697ed-367">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
