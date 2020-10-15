---
title: Modifications avec rupture dans EF Core 5,0-EF Core
description: Liste complète des modifications avec rupture introduites dans Entity Framework Core 5,0
author: bricelam
ms.date: 09/24/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e64f2b387d236e96d0451f3d55b3241daaba32d8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065639"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="8a021-103">Modifications avec rupture dans EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="8a021-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="8a021-104">Les modifications d’API et de comportement suivantes peuvent bloquer les mises à jour des applications existantes en EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="8a021-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="8a021-105">Résumé</span><span class="sxs-lookup"><span data-stu-id="8a021-105">Summary</span></span>

| <span data-ttu-id="8a021-106">**Modification critique**</span><span class="sxs-lookup"><span data-stu-id="8a021-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="8a021-107">**Impact**</span><span class="sxs-lookup"><span data-stu-id="8a021-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="8a021-108">Obligatoire sur la navigation du principal au dépendant a une sémantique différente</span><span class="sxs-lookup"><span data-stu-id="8a021-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="8a021-109">Medium</span><span class="sxs-lookup"><span data-stu-id="8a021-109">Medium</span></span>     |
| [<span data-ttu-id="8a021-110">La définition de la requête est remplacée par des méthodes spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="8a021-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="8a021-111">Medium</span><span class="sxs-lookup"><span data-stu-id="8a021-111">Medium</span></span>     |
| [<span data-ttu-id="8a021-112">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="8a021-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="8a021-113">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-113">Low</span></span>        |
| [<span data-ttu-id="8a021-114">Cosmos : la clé de partition est maintenant ajoutée à la clé primaire</span><span class="sxs-lookup"><span data-stu-id="8a021-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="8a021-115">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-115">Low</span></span>        |
| [<span data-ttu-id="8a021-116">Cosmos : `id` propriété renommée en `__id`</span><span class="sxs-lookup"><span data-stu-id="8a021-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="8a021-117">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-117">Low</span></span>        |
| <span data-ttu-id="8a021-118">[Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="8a021-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="8a021-119">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-119">Low</span></span>        |
| [<span data-ttu-id="8a021-120">Cosmos : GetPropertyName et SetPropertyName ont été renommés</span><span class="sxs-lookup"><span data-stu-id="8a021-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="8a021-121">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-121">Low</span></span>        |
| [<span data-ttu-id="8a021-122">Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé</span><span class="sxs-lookup"><span data-stu-id="8a021-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="8a021-123">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-123">Low</span></span>        |
| [<span data-ttu-id="8a021-124">IMigrationsModelDiffer utilise désormais IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="8a021-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="8a021-125">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-125">Low</span></span>        |
| [<span data-ttu-id="8a021-126">Les discriminateurs sont en lecture seule</span><span class="sxs-lookup"><span data-stu-id="8a021-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="8a021-127">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-127">Low</span></span>        |
| [<span data-ttu-id="8a021-128">EF spécifique au fournisseur. Les méthodes Functions lèvent pour le fournisseur InMemory</span><span class="sxs-lookup"><span data-stu-id="8a021-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="8a021-129">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-129">Low</span></span>        |
| [<span data-ttu-id="8a021-130">IndexBuilder. HasName est désormais obsolète</span><span class="sxs-lookup"><span data-stu-id="8a021-130">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="8a021-131">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-131">Low</span></span>        |
| [<span data-ttu-id="8a021-132">Un pluarlizer est maintenant inclus pour la génération de modèles automatique de modèles rétroconçus</span><span class="sxs-lookup"><span data-stu-id="8a021-132">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="8a021-133">Faible</span><span class="sxs-lookup"><span data-stu-id="8a021-133">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="8a021-134">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="8a021-134">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="8a021-135">#14257 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-135">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="8a021-136">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-136">**Old behavior**</span></span>

<span data-ttu-id="8a021-137">HasGeometricDimension a été utilisé pour activer des dimensions supplémentaires (Z et M) sur les colonnes Geometry.</span><span class="sxs-lookup"><span data-stu-id="8a021-137">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="8a021-138">Toutefois, il n’a jamais été affecté par la création de bases de données.</span><span class="sxs-lookup"><span data-stu-id="8a021-138">However, it only ever affected database creation.</span></span> <span data-ttu-id="8a021-139">Il n’était pas nécessaire de le spécifier pour interroger des valeurs avec des dimensions supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="8a021-139">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="8a021-140">Elle ne fonctionnait pas non plus correctement lors de l’insertion ou de la mise à jour de valeurs avec des dimensions supplémentaires ([voir #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="8a021-140">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="8a021-141">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-141">**New behavior**</span></span>

<span data-ttu-id="8a021-142">Pour permettre l’insertion et la mise à jour de valeurs géométriques avec des dimensions supplémentaires (Z et M), la dimension doit être spécifiée en tant que partie du nom du type de colonne.</span><span class="sxs-lookup"><span data-stu-id="8a021-142">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="8a021-143">Cette API correspond plus étroitement au comportement sous-jacent de la fonction AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="8a021-143">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="8a021-144">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-144">**Why**</span></span>

<span data-ttu-id="8a021-145">L’utilisation de HasGeometricDimension après avoir spécifié la dimension dans le type de colonne est inutile et redondante, donc nous avons supprimé HasGeometricDimension entièrement.</span><span class="sxs-lookup"><span data-stu-id="8a021-145">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="8a021-146">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-146">**Mitigations**</span></span>

<span data-ttu-id="8a021-147">Utilisez `HasColumnType` pour spécifier la dimension :</span><span class="sxs-lookup"><span data-stu-id="8a021-147">Use `HasColumnType` to specify the dimension:</span></span>

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

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="8a021-148">Obligatoire sur la navigation du principal au dépendant a une sémantique différente</span><span class="sxs-lookup"><span data-stu-id="8a021-148">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="8a021-149">#17286 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-149">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="8a021-150">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-150">**Old behavior**</span></span>

<span data-ttu-id="8a021-151">Seules les navigations vers le principal peuvent être configurées selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="8a021-151">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="8a021-152">Par conséquent, l’utilisation `RequiredAttribute` de sur la navigation vers le dépendant (l’entité qui contient la clé étrangère) créerait à la place la clé étrangère sur le type d’entité de définition.</span><span class="sxs-lookup"><span data-stu-id="8a021-152">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="8a021-153">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-153">**New behavior**</span></span>

<span data-ttu-id="8a021-154">Avec la prise en charge supplémentaire des dépendants requis, il est désormais possible de marquer toute navigation de référence comme étant obligatoire, ce qui signifie que dans le cas indiqué au-dessus de la clé étrangère sera défini de l’autre côté de la relation et que les propriétés ne seront pas marquées comme obligatoires.</span><span class="sxs-lookup"><span data-stu-id="8a021-154">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="8a021-155">`IsRequired`L’appel de avant la spécification de la terminaison dépendante est maintenant ambigu :</span><span class="sxs-lookup"><span data-stu-id="8a021-155">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="8a021-156">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-156">**Why**</span></span>

<span data-ttu-id="8a021-157">Le nouveau comportement est nécessaire pour activer la prise en charge des dépendants requis ([voir #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="8a021-157">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="8a021-158">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-158">**Mitigations**</span></span>

<span data-ttu-id="8a021-159">Supprimez `RequiredAttribute` de la navigation vers le dépendant et placez-le à la place sur la navigation vers le principal ou configurez la relation dans `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="8a021-159">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="8a021-160">Cosmos : la clé de partition est maintenant ajoutée à la clé primaire</span><span class="sxs-lookup"><span data-stu-id="8a021-160">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="8a021-161">#15289 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-161">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="8a021-162">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-162">**Old behavior**</span></span>

<span data-ttu-id="8a021-163">La propriété de clé de partition a été uniquement ajoutée à l’autre clé qui comprend `id` .</span><span class="sxs-lookup"><span data-stu-id="8a021-163">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="8a021-164">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-164">**New behavior**</span></span>

<span data-ttu-id="8a021-165">La propriété de clé de partition est maintenant également ajoutée à la clé primaire par Convention.</span><span class="sxs-lookup"><span data-stu-id="8a021-165">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="8a021-166">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-166">**Why**</span></span>

<span data-ttu-id="8a021-167">Cette modification rend le modèle mieux aligné avec Azure Cosmos DB sémantique et améliore les performances de `Find` et de certaines requêtes.</span><span class="sxs-lookup"><span data-stu-id="8a021-167">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="8a021-168">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-168">**Mitigations**</span></span>

<span data-ttu-id="8a021-169">Pour empêcher que la propriété de clé de partition soit ajoutée à la clé primaire, configurez-la dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="8a021-169">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="8a021-170">Cosmos : `id` propriété renommée en `__id`</span><span class="sxs-lookup"><span data-stu-id="8a021-170">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="8a021-171">#17751 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-171">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="8a021-172">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-172">**Old behavior**</span></span>

<span data-ttu-id="8a021-173">La propriété Shadow mappée à la `id` propriété JSON a également été nommée `id` .</span><span class="sxs-lookup"><span data-stu-id="8a021-173">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="8a021-174">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-174">**New behavior**</span></span>

<span data-ttu-id="8a021-175">La propriété Shadow créée par la Convention est maintenant nommée `__id` .</span><span class="sxs-lookup"><span data-stu-id="8a021-175">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="8a021-176">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-176">**Why**</span></span>

<span data-ttu-id="8a021-177">Cette modification rend moins probable la `id` propriété en conflit avec une propriété existante sur le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="8a021-177">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="8a021-178">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-178">**Mitigations**</span></span>

<span data-ttu-id="8a021-179">Pour revenir au comportement 3. x, configurez la `id` propriété dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="8a021-179">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="8a021-180">Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres</span><span class="sxs-lookup"><span data-stu-id="8a021-180">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="8a021-181">#17306 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-181">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="8a021-182">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-182">**Old behavior**</span></span>

<span data-ttu-id="8a021-183">Les propriétés de type Byte [] étaient stockées sous la forme d’un tableau de nombres.</span><span class="sxs-lookup"><span data-stu-id="8a021-183">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="8a021-184">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-184">**New behavior**</span></span>

<span data-ttu-id="8a021-185">Les propriétés de type Byte [] sont désormais stockées sous la forme d’une chaîne base64.</span><span class="sxs-lookup"><span data-stu-id="8a021-185">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="8a021-186">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-186">**Why**</span></span>

<span data-ttu-id="8a021-187">Cette représentation de Byte [] s’aligne mieux avec les attentes et est le comportement par défaut des principales bibliothèques de sérialisation JSON.</span><span class="sxs-lookup"><span data-stu-id="8a021-187">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="8a021-188">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-188">**Mitigations**</span></span>

<span data-ttu-id="8a021-189">Les données existantes stockées en tant que tableaux de nombres seront toujours interrogées correctement, mais il n’existe actuellement aucune méthode prise en charge pour modifier le comportement d’insertion.</span><span class="sxs-lookup"><span data-stu-id="8a021-189">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="8a021-190">Si cette limitation bloque votre scénario, commentez [-le](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="8a021-190">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="8a021-191">Cosmos : GetPropertyName et SetPropertyName ont été renommés</span><span class="sxs-lookup"><span data-stu-id="8a021-191">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="8a021-192">#17874 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-192">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="8a021-193">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-193">**Old behavior**</span></span>

<span data-ttu-id="8a021-194">Auparavant, les méthodes d’extension étaient appelées `GetPropertyName` et `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="8a021-194">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="8a021-195">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-195">**New behavior**</span></span>

<span data-ttu-id="8a021-196">L’ancienne API a été supprimée et de nouvelles méthodes ont été ajoutées : `GetJsonPropertyName` , `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="8a021-196">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="8a021-197">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-197">**Why**</span></span>

<span data-ttu-id="8a021-198">Cette modification supprime l’ambiguïté autour de la configuration de ces méthodes.</span><span class="sxs-lookup"><span data-stu-id="8a021-198">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="8a021-199">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-199">**Mitigations**</span></span>

<span data-ttu-id="8a021-200">Utilisez la nouvelle API.</span><span class="sxs-lookup"><span data-stu-id="8a021-200">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="8a021-201">Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé</span><span class="sxs-lookup"><span data-stu-id="8a021-201">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="8a021-202">#15289 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-202">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="8a021-203">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-203">**Old behavior**</span></span>

<span data-ttu-id="8a021-204">Les générateurs de valeur ont été appelés uniquement lorsque l’état de l’entité a été remplacé par ajouté.</span><span class="sxs-lookup"><span data-stu-id="8a021-204">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="8a021-205">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-205">**New behavior**</span></span>

<span data-ttu-id="8a021-206">Les générateurs de valeurs sont désormais appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé et que la propriété contient les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="8a021-206">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="8a021-207">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-207">**Why**</span></span>

<span data-ttu-id="8a021-208">Cette modification était nécessaire pour améliorer l’expérience des propriétés qui ne sont pas rendues persistantes dans le magasin de données et dont la valeur doit être générée systématiquement sur le client.</span><span class="sxs-lookup"><span data-stu-id="8a021-208">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="8a021-209">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-209">**Mitigations**</span></span>

<span data-ttu-id="8a021-210">Pour empêcher l’appel du générateur de valeurs, assignez une valeur non définie par défaut à la propriété avant la modification de l’État.</span><span class="sxs-lookup"><span data-stu-id="8a021-210">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="8a021-211">IMigrationsModelDiffer utilise désormais IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="8a021-211">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="8a021-212">#20305 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-212">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="8a021-213">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-213">**Old behavior**</span></span>

<span data-ttu-id="8a021-214">`IMigrationsModelDiffer` L’API a été définie à l’aide de `IModel` .</span><span class="sxs-lookup"><span data-stu-id="8a021-214">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="8a021-215">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-215">**New behavior**</span></span>

<span data-ttu-id="8a021-216">`IMigrationsModelDiffer` L’API utilise désormais `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="8a021-216">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="8a021-217">Toutefois, l’instantané de modèle contient toujours uniquement `IModel` , car ce code fait partie de l’application et Entity Framework ne peut pas le modifier sans apporter de modification plus importante.</span><span class="sxs-lookup"><span data-stu-id="8a021-217">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="8a021-218">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-218">**Why**</span></span>

<span data-ttu-id="8a021-219">`IRelationalModel` représentation qui vient d’être ajoutée au schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="8a021-219">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="8a021-220">Son utilisation pour trouver les différences est plus rapide et plus précise.</span><span class="sxs-lookup"><span data-stu-id="8a021-220">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="8a021-221">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-221">**Mitigations**</span></span>

<span data-ttu-id="8a021-222">Utilisez le code suivant pour comparer le modèle à `snapshot` l’aide du modèle à partir de `context` :</span><span class="sxs-lookup"><span data-stu-id="8a021-222">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="8a021-223">Nous envisageons d’améliorer cette expérience dans 6,0 ([voir #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="8a021-223">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="8a021-224">Les discriminateurs sont en lecture seule</span><span class="sxs-lookup"><span data-stu-id="8a021-224">Discriminators are read-only</span></span>

[<span data-ttu-id="8a021-225">#21154 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-225">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="8a021-226">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-226">**Old behavior**</span></span>

<span data-ttu-id="8a021-227">Il était possible de modifier la valeur du discriminateur avant d’appeler `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="8a021-227">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="8a021-228">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-228">**New behavior**</span></span>

<span data-ttu-id="8a021-229">Une exception sera levée dans le cas ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="8a021-229">An exception will be throws in the above case.</span></span>

<span data-ttu-id="8a021-230">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-230">**Why**</span></span>

<span data-ttu-id="8a021-231">EF ne s’attend pas à ce que le type d’entité change pendant qu’il est toujours suivi. la modification de la valeur de discriminateur laisse le contexte dans un état incohérent, ce qui peut entraîner un comportement inattendu.</span><span class="sxs-lookup"><span data-stu-id="8a021-231">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="8a021-232">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-232">**Mitigations**</span></span>

<span data-ttu-id="8a021-233">Si la modification de la valeur de discriminateur est nécessaire et que le contexte est supprimé immédiatement après l’appel `SaveChanges` de, le discriminateur peut devenir mutable :</span><span class="sxs-lookup"><span data-stu-id="8a021-233">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="8a021-234">La définition de la requête est remplacée par des méthodes spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="8a021-234">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="8a021-235">#18903 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-235">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="8a021-236">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-236">**Old behavior**</span></span>

<span data-ttu-id="8a021-237">Les types d’entité ont été mappés à la définition de requêtes au niveau du noyau.</span><span class="sxs-lookup"><span data-stu-id="8a021-237">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="8a021-238">Chaque fois que le type d’entité a été utilisé dans la racine de la requête du type d’entité a été remplacé par la requête de définition pour n’importe quel fournisseur.</span><span class="sxs-lookup"><span data-stu-id="8a021-238">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="8a021-239">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-239">**New behavior**</span></span>

<span data-ttu-id="8a021-240">Les API pour la définition de la requête sont déconseillées.</span><span class="sxs-lookup"><span data-stu-id="8a021-240">APIs for defining query are deprecated.</span></span> <span data-ttu-id="8a021-241">De nouvelles API spécifiques au fournisseur ont été introduites.</span><span class="sxs-lookup"><span data-stu-id="8a021-241">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="8a021-242">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-242">**Why**</span></span>

<span data-ttu-id="8a021-243">Alors que la définition de requêtes a été implémentée en tant que requête de remplacement chaque fois que la racine de la requête est utilisée dans la requête, il y a quelques problèmes :</span><span class="sxs-lookup"><span data-stu-id="8a021-243">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="8a021-244">Si la définition de la requête consiste à projeter un type d’entité à l’aide `new { ... }` de dans `Select` la méthode, vous devez ensuite identifier qu’en tant qu’entité a requis un travail supplémentaire et qu’elle est incohérente avec la façon dont EF Core traite les types nominaux dans la requête.</span><span class="sxs-lookup"><span data-stu-id="8a021-244">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="8a021-245">Pour les fournisseurs relationnels `FromSql` , il est toujours nécessaire de passer la chaîne SQL sous forme d’expression LINQ.</span><span class="sxs-lookup"><span data-stu-id="8a021-245">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="8a021-246">La définition initiale des requêtes a été introduite en tant que vues côté client à utiliser avec In-Memory fournisseur pour les entités keymoins (similaires aux vues de base de données dans les bases de données relationnelles).</span><span class="sxs-lookup"><span data-stu-id="8a021-246">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="8a021-247">Une telle définition facilite le test de l’application sur la base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="8a021-247">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="8a021-248">Par la suite, ils sont devenus largement applicables, ce qui était utile, mais incohérents et difficiles à comprendre.</span><span class="sxs-lookup"><span data-stu-id="8a021-248">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="8a021-249">Nous avons donc décidé de simplifier le concept.</span><span class="sxs-lookup"><span data-stu-id="8a021-249">So we decided to simplify the concept.</span></span> <span data-ttu-id="8a021-250">Nous avons fait en sorte que la requête de définition basée sur LINQ soit exclusive pour In-Memory fournisseur et les traite différemment.</span><span class="sxs-lookup"><span data-stu-id="8a021-250">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="8a021-251">Pour plus d’informations, [consultez ce problème](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="8a021-251">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="8a021-252">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-252">**Mitigations**</span></span>

<span data-ttu-id="8a021-253">Pour les fournisseurs relationnels, utilisez `ToSqlQuery` la méthode dans `OnModelCreating` et transmettez une chaîne SQL à utiliser pour le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="8a021-253">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="8a021-254">Pour le fournisseur In-Memory, utilisez la `ToInMemoryQuery` méthode dans `OnModelCreating` et transmettez une requête LINQ à utiliser pour le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="8a021-254">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="8a021-255">EF spécifique au fournisseur. Les méthodes Functions lèvent pour le fournisseur InMemory</span><span class="sxs-lookup"><span data-stu-id="8a021-255">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="8a021-256">#20294 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-256">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="8a021-257">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-257">**Old behavior**</span></span>

<span data-ttu-id="8a021-258">EF spécifique au fournisseur. Les méthodes Functions contenaient une implémentation pour l’exécution du client, ce qui leur permettait d’être exécutées sur le fournisseur d’InMemory.</span><span class="sxs-lookup"><span data-stu-id="8a021-258">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="8a021-259">Par exemple, `EF.Functions.DateDiffDay` est une méthode propre à SQL Server, qui fonctionnait sur le fournisseur d’InMemory.</span><span class="sxs-lookup"><span data-stu-id="8a021-259">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="8a021-260">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-260">**New behavior**</span></span>

<span data-ttu-id="8a021-261">Les méthodes spécifiques au fournisseur ont été mises à jour pour lever une exception dans leur corps de méthode afin de bloquer leur évaluation côté client.</span><span class="sxs-lookup"><span data-stu-id="8a021-261">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="8a021-262">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-262">**Why**</span></span>

<span data-ttu-id="8a021-263">Les méthodes spécifiques au fournisseur sont mappées à une fonction de base de données.</span><span class="sxs-lookup"><span data-stu-id="8a021-263">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="8a021-264">Le calcul effectué par la fonction de base de données mappée ne peut pas toujours être répliqué côté client dans LINQ.</span><span class="sxs-lookup"><span data-stu-id="8a021-264">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="8a021-265">Cela peut provoquer la différence entre le résultat du serveur et l’exécution de la même méthode sur le client.</span><span class="sxs-lookup"><span data-stu-id="8a021-265">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="8a021-266">Étant donné que ces méthodes sont utilisées dans LINQ pour traduire des fonctions de base de données spécifiques, elles n’ont pas besoin d’être évaluées côté client.</span><span class="sxs-lookup"><span data-stu-id="8a021-266">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="8a021-267">Comme le fournisseur d’InMemory est une *base de données*différente, ces méthodes ne sont pas disponibles pour ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="8a021-267">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="8a021-268">Si vous tentez de les exécuter pour le fournisseur d’InMemory ou tout autre fournisseur qui ne traduit pas ces méthodes, lève une exception.</span><span class="sxs-lookup"><span data-stu-id="8a021-268">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="8a021-269">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-269">**Mitigations**</span></span>

<span data-ttu-id="8a021-270">Étant donné qu’il n’existe aucun moyen de reproduire le comportement des fonctions de base de données avec précision, vous devez tester les requêtes qui les contiennent sur le même type de base de données qu’en production.</span><span class="sxs-lookup"><span data-stu-id="8a021-270">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="8a021-271">IndexBuilder. HasName est désormais obsolète</span><span class="sxs-lookup"><span data-stu-id="8a021-271">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="8a021-272">#21089 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-272">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

<span data-ttu-id="8a021-273">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-273">**Old behavior**</span></span>

<span data-ttu-id="8a021-274">Auparavant, un seul index pouvait être défini sur un ensemble de propriétés donné.</span><span class="sxs-lookup"><span data-stu-id="8a021-274">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="8a021-275">Le nom de la base de données d’un index a été configuré à l’aide de IndexBuilder. HasName.</span><span class="sxs-lookup"><span data-stu-id="8a021-275">The database name of an index was configured using IndexBuilder.HasName.</span></span>

<span data-ttu-id="8a021-276">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-276">**New behavior**</span></span>

<span data-ttu-id="8a021-277">Plusieurs index sont désormais autorisés sur le même jeu ou les mêmes propriétés.</span><span class="sxs-lookup"><span data-stu-id="8a021-277">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="8a021-278">Ces index sont à présent distingués par un nom dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="8a021-278">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="8a021-279">Par Convention, le nom du modèle est utilisé comme nom de la base de données ; Toutefois, il peut également être configuré indépendamment à l’aide de HasDatabaseName.</span><span class="sxs-lookup"><span data-stu-id="8a021-279">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

<span data-ttu-id="8a021-280">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-280">**Why**</span></span>

<span data-ttu-id="8a021-281">À l’avenir, nous souhaitons activer à la fois les index croissants et les index décroissant avec différents classements sur le même ensemble de propriétés.</span><span class="sxs-lookup"><span data-stu-id="8a021-281">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="8a021-282">Cette modification nous déplace une autre étape dans cette direction.</span><span class="sxs-lookup"><span data-stu-id="8a021-282">This change moves us another step in that direction.</span></span>

<span data-ttu-id="8a021-283">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-283">**Mitigations**</span></span>

<span data-ttu-id="8a021-284">Tout code qui appelle précédemment IndexBuilder. HasName doit être mis à jour pour appeler HasDatabaseName à la place.</span><span class="sxs-lookup"><span data-stu-id="8a021-284">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="8a021-285">Si votre projet comprend des migrations générées avant EF Core version 2.0.0, vous pouvez ignorer en toute sécurité l’avertissement dans ces fichiers et le supprimer en ajoutant `#pragma warning disable 612, 618` .</span><span class="sxs-lookup"><span data-stu-id="8a021-285">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="8a021-286">Un pluarlizer est maintenant inclus pour la génération de modèles automatique de modèles rétroconçus</span><span class="sxs-lookup"><span data-stu-id="8a021-286">A pluarlizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="8a021-287">#11160 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="8a021-287">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

<span data-ttu-id="8a021-288">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-288">**Old behavior**</span></span>

<span data-ttu-id="8a021-289">Auparavant, vous deviez installer un package pluraliseur distinct pour plurieliser les noms de navigation des DbSet et des collections et les noms de tables singulières lorsque scaffoding un DbContext et des types d’entité en reconstituant l’ingénierie inverse d’un schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="8a021-289">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffoding a DbContext and entity types by reverse engineering a database schema.</span></span>

<span data-ttu-id="8a021-290">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="8a021-290">**New behavior**</span></span>

<span data-ttu-id="8a021-291">EF Core comprend maintenant un pluraliseur qui utilise la bibliothèque [Humanizer](https://github.com/Humanizr/Humanizer) .</span><span class="sxs-lookup"><span data-stu-id="8a021-291">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="8a021-292">Il s’agit de la même bibliothèque que celle utilisée par Visual Studio pour recommander des noms de variables.</span><span class="sxs-lookup"><span data-stu-id="8a021-292">This is the same library Visual Studio uses to recommend variable names.</span></span>

<span data-ttu-id="8a021-293">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="8a021-293">**Why**</span></span>

<span data-ttu-id="8a021-294">L’utilisation de plusieurs formes de mots pour les propriétés de collection et les formes singulières pour les types et les propriétés de référence est idiomatique dans .NET.</span><span class="sxs-lookup"><span data-stu-id="8a021-294">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

<span data-ttu-id="8a021-295">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="8a021-295">**Mitigations**</span></span>

<span data-ttu-id="8a021-296">Pour désactiver pluraliseur, utilisez l' `--no-pluralize` option sur `dotnet ef dbcontext scaffold` ou le `-NoPluralize` commutateur activé `Scaffold-DbContext` .</span><span class="sxs-lookup"><span data-stu-id="8a021-296">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>
