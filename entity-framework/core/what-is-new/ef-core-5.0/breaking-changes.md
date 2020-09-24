---
title: Modifications avec rupture dans EF Core 5,0-EF Core
description: Liste complète des modifications avec rupture introduites dans Entity Framework Core 5,0
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 8e9df4e2ff81e20cf5a36855247c5aff89ea2394
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210365"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="41361-103">Modifications avec rupture dans EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="41361-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="41361-104">Les modifications d’API et de comportement suivantes peuvent bloquer les mises à jour des applications existantes en EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="41361-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="41361-105">Résumé</span><span class="sxs-lookup"><span data-stu-id="41361-105">Summary</span></span>

| <span data-ttu-id="41361-106">**Modification critique**</span><span class="sxs-lookup"><span data-stu-id="41361-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="41361-107">**Impact**</span><span class="sxs-lookup"><span data-stu-id="41361-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="41361-108">Obligatoire sur la navigation du principal au dépendant a une sémantique différente</span><span class="sxs-lookup"><span data-stu-id="41361-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="41361-109">Moyenne</span><span class="sxs-lookup"><span data-stu-id="41361-109">Medium</span></span>     |
| [<span data-ttu-id="41361-110">La définition de la requête est remplacée par des méthodes spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="41361-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="41361-111">Moyenne</span><span class="sxs-lookup"><span data-stu-id="41361-111">Medium</span></span>     |
| [<span data-ttu-id="41361-112">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="41361-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="41361-113">Faible</span><span class="sxs-lookup"><span data-stu-id="41361-113">Low</span></span>        |
| [<span data-ttu-id="41361-114">Cosmos : la clé de partition est maintenant ajoutée à la clé primaire</span><span class="sxs-lookup"><span data-stu-id="41361-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="41361-115">Faible</span><span class="sxs-lookup"><span data-stu-id="41361-115">Low</span></span>        |
| [<span data-ttu-id="41361-116">Cosmos : `id` propriété renommée en `__id`</span><span class="sxs-lookup"><span data-stu-id="41361-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="41361-117">Faible</span><span class="sxs-lookup"><span data-stu-id="41361-117">Low</span></span>        |
| <span data-ttu-id="41361-118">[Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="41361-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="41361-119">Faible</span><span class="sxs-lookup"><span data-stu-id="41361-119">Low</span></span>        |
| [<span data-ttu-id="41361-120">Cosmos : GetPropertyName et SetPropertyName ont été renommés</span><span class="sxs-lookup"><span data-stu-id="41361-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="41361-121">Faible</span><span class="sxs-lookup"><span data-stu-id="41361-121">Low</span></span>        |
| [<span data-ttu-id="41361-122">Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé</span><span class="sxs-lookup"><span data-stu-id="41361-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="41361-123">Faible</span><span class="sxs-lookup"><span data-stu-id="41361-123">Low</span></span>        |
| [<span data-ttu-id="41361-124">IMigrationsModelDiffer utilise désormais IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="41361-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="41361-125">Faible</span><span class="sxs-lookup"><span data-stu-id="41361-125">Low</span></span>        |
| [<span data-ttu-id="41361-126">Les discriminateurs sont en lecture seule</span><span class="sxs-lookup"><span data-stu-id="41361-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="41361-127">Faible</span><span class="sxs-lookup"><span data-stu-id="41361-127">Low</span></span>        |
| [<span data-ttu-id="41361-128">EF spécifique au fournisseur. Les méthodes Functions lèvent pour le fournisseur InMemory</span><span class="sxs-lookup"><span data-stu-id="41361-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="41361-129">Faible</span><span class="sxs-lookup"><span data-stu-id="41361-129">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="41361-130">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="41361-130">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="41361-131">#14257 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-131">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="41361-132">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-132">**Old behavior**</span></span>

<span data-ttu-id="41361-133">HasGeometricDimension a été utilisé pour activer des dimensions supplémentaires (Z et M) sur les colonnes Geometry.</span><span class="sxs-lookup"><span data-stu-id="41361-133">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="41361-134">Toutefois, il n’a jamais été affecté par la création de bases de données.</span><span class="sxs-lookup"><span data-stu-id="41361-134">However, it only ever affected database creation.</span></span> <span data-ttu-id="41361-135">Il n’était pas nécessaire de le spécifier pour interroger des valeurs avec des dimensions supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="41361-135">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="41361-136">Elle ne fonctionnait pas non plus correctement lors de l’insertion ou de la mise à jour de valeurs avec des dimensions supplémentaires ([voir #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="41361-136">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="41361-137">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-137">**New behavior**</span></span>

<span data-ttu-id="41361-138">Pour permettre l’insertion et la mise à jour de valeurs géométriques avec des dimensions supplémentaires (Z et M), la dimension doit être spécifiée en tant que partie du nom du type de colonne.</span><span class="sxs-lookup"><span data-stu-id="41361-138">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="41361-139">Cette API correspond plus étroitement au comportement sous-jacent de la fonction AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="41361-139">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="41361-140">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-140">**Why**</span></span>

<span data-ttu-id="41361-141">L’utilisation de HasGeometricDimension après avoir spécifié la dimension dans le type de colonne est inutile et redondante, donc nous avons supprimé HasGeometricDimension entièrement.</span><span class="sxs-lookup"><span data-stu-id="41361-141">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="41361-142">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-142">**Mitigations**</span></span>

<span data-ttu-id="41361-143">Utilisez `HasColumnType` pour spécifier la dimension :</span><span class="sxs-lookup"><span data-stu-id="41361-143">Use `HasColumnType` to specify the dimension:</span></span>

```cs
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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="41361-144">Obligatoire sur la navigation du principal au dépendant a une sémantique différente</span><span class="sxs-lookup"><span data-stu-id="41361-144">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="41361-145">#17286 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-145">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="41361-146">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-146">**Old behavior**</span></span>

<span data-ttu-id="41361-147">Seules les navigations vers le principal peuvent être configurées selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="41361-147">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="41361-148">Par conséquent, l’utilisation `RequiredAttribute` de sur la navigation vers le dépendant (l’entité qui contient la clé étrangère) créerait à la place la clé étrangère sur le type d’entité de définition.</span><span class="sxs-lookup"><span data-stu-id="41361-148">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="41361-149">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-149">**New behavior**</span></span>

<span data-ttu-id="41361-150">Avec la prise en charge supplémentaire des dépendants requis, il est désormais possible de marquer toute navigation de référence comme étant obligatoire, ce qui signifie que dans le cas indiqué au-dessus de la clé étrangère sera défini de l’autre côté de la relation et que les propriétés ne seront pas marquées comme obligatoires.</span><span class="sxs-lookup"><span data-stu-id="41361-150">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="41361-151">`IsRequired`L’appel de avant la spécification de la terminaison dépendante est maintenant ambigu :</span><span class="sxs-lookup"><span data-stu-id="41361-151">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="41361-152">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-152">**Why**</span></span>

<span data-ttu-id="41361-153">Le nouveau comportement est nécessaire pour activer la prise en charge des dépendants requis ([voir #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="41361-153">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="41361-154">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-154">**Mitigations**</span></span>

<span data-ttu-id="41361-155">Supprimez `RequiredAttribute` de la navigation vers le dépendant et placez-le à la place sur la navigation vers le principal ou configurez la relation dans `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="41361-155">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="41361-156">Cosmos : la clé de partition est maintenant ajoutée à la clé primaire</span><span class="sxs-lookup"><span data-stu-id="41361-156">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="41361-157">#15289 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-157">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="41361-158">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-158">**Old behavior**</span></span>

<span data-ttu-id="41361-159">La propriété de clé de partition a été uniquement ajoutée à l’autre clé qui comprend `id` .</span><span class="sxs-lookup"><span data-stu-id="41361-159">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="41361-160">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-160">**New behavior**</span></span>

<span data-ttu-id="41361-161">La propriété de clé de partition est maintenant également ajoutée à la clé primaire par Convention.</span><span class="sxs-lookup"><span data-stu-id="41361-161">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="41361-162">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-162">**Why**</span></span>

<span data-ttu-id="41361-163">Cette modification rend le modèle mieux aligné avec Azure Cosmos DB sémantique et améliore les performances de `Find` et de certaines requêtes.</span><span class="sxs-lookup"><span data-stu-id="41361-163">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="41361-164">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-164">**Mitigations**</span></span>

<span data-ttu-id="41361-165">Pour empêcher que la propriété de clé de partition soit ajoutée à la clé primaire, configurez-la dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="41361-165">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="41361-166">Cosmos : `id` propriété renommée en `__id`</span><span class="sxs-lookup"><span data-stu-id="41361-166">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="41361-167">#17751 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-167">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="41361-168">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-168">**Old behavior**</span></span>

<span data-ttu-id="41361-169">La propriété Shadow mappée à la `id` propriété JSON a également été nommée `id` .</span><span class="sxs-lookup"><span data-stu-id="41361-169">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="41361-170">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-170">**New behavior**</span></span>

<span data-ttu-id="41361-171">La propriété Shadow créée par la Convention est maintenant nommée `__id` .</span><span class="sxs-lookup"><span data-stu-id="41361-171">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="41361-172">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-172">**Why**</span></span>

<span data-ttu-id="41361-173">Cette modification rend moins probable la `id` propriété en conflit avec une propriété existante sur le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="41361-173">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="41361-174">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-174">**Mitigations**</span></span>

<span data-ttu-id="41361-175">Pour revenir au comportement 3. x, configurez la `id` propriété dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="41361-175">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="41361-176">Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres</span><span class="sxs-lookup"><span data-stu-id="41361-176">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="41361-177">#17306 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-177">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="41361-178">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-178">**Old behavior**</span></span>

<span data-ttu-id="41361-179">Les propriétés de type Byte [] étaient stockées sous la forme d’un tableau de nombres.</span><span class="sxs-lookup"><span data-stu-id="41361-179">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="41361-180">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-180">**New behavior**</span></span>

<span data-ttu-id="41361-181">Les propriétés de type Byte [] sont désormais stockées sous la forme d’une chaîne base64.</span><span class="sxs-lookup"><span data-stu-id="41361-181">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="41361-182">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-182">**Why**</span></span>

<span data-ttu-id="41361-183">Cette représentation de Byte [] s’aligne mieux avec les attentes et est le comportement par défaut des principales bibliothèques de sérialisation JSON.</span><span class="sxs-lookup"><span data-stu-id="41361-183">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="41361-184">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-184">**Mitigations**</span></span>

<span data-ttu-id="41361-185">Les données existantes stockées en tant que tableaux de nombres seront toujours interrogées correctement, mais il n’existe actuellement aucune méthode prise en charge pour modifier le comportement d’insertion.</span><span class="sxs-lookup"><span data-stu-id="41361-185">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="41361-186">Si cette limitation bloque votre scénario, commentez [-le](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="41361-186">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="41361-187">Cosmos : GetPropertyName et SetPropertyName ont été renommés</span><span class="sxs-lookup"><span data-stu-id="41361-187">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="41361-188">#17874 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-188">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="41361-189">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-189">**Old behavior**</span></span>

<span data-ttu-id="41361-190">Auparavant, les méthodes d’extension étaient appelées `GetPropertyName` et `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="41361-190">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="41361-191">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-191">**New behavior**</span></span>

<span data-ttu-id="41361-192">L’ancienne API a été supprimée et de nouvelles méthodes ont été ajoutées : `GetJsonPropertyName` , `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="41361-192">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="41361-193">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-193">**Why**</span></span>

<span data-ttu-id="41361-194">Cette modification supprime l’ambiguïté autour de la configuration de ces méthodes.</span><span class="sxs-lookup"><span data-stu-id="41361-194">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="41361-195">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-195">**Mitigations**</span></span>

<span data-ttu-id="41361-196">Utilisez la nouvelle API.</span><span class="sxs-lookup"><span data-stu-id="41361-196">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="41361-197">Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé</span><span class="sxs-lookup"><span data-stu-id="41361-197">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="41361-198">#15289 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-198">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="41361-199">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-199">**Old behavior**</span></span>

<span data-ttu-id="41361-200">Les générateurs de valeur ont été appelés uniquement lorsque l’état de l’entité a été remplacé par ajouté.</span><span class="sxs-lookup"><span data-stu-id="41361-200">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="41361-201">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-201">**New behavior**</span></span>

<span data-ttu-id="41361-202">Les générateurs de valeurs sont désormais appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé et que la propriété contient les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="41361-202">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="41361-203">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-203">**Why**</span></span>

<span data-ttu-id="41361-204">Cette modification était nécessaire pour améliorer l’expérience des propriétés qui ne sont pas rendues persistantes dans le magasin de données et dont la valeur doit être générée systématiquement sur le client.</span><span class="sxs-lookup"><span data-stu-id="41361-204">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="41361-205">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-205">**Mitigations**</span></span>

<span data-ttu-id="41361-206">Pour empêcher l’appel du générateur de valeurs, assignez une valeur non définie par défaut à la propriété avant la modification de l’État.</span><span class="sxs-lookup"><span data-stu-id="41361-206">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="41361-207">IMigrationsModelDiffer utilise désormais IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="41361-207">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="41361-208">#20305 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-208">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="41361-209">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-209">**Old behavior**</span></span>

<span data-ttu-id="41361-210">`IMigrationsModelDiffer` L’API a été définie à l’aide de `IModel` .</span><span class="sxs-lookup"><span data-stu-id="41361-210">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="41361-211">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-211">**New behavior**</span></span>

<span data-ttu-id="41361-212">`IMigrationsModelDiffer` L’API utilise désormais `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="41361-212">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="41361-213">Toutefois, l’instantané de modèle contient toujours uniquement `IModel` , car ce code fait partie de l’application et Entity Framework ne peut pas le modifier sans apporter de modification plus importante.</span><span class="sxs-lookup"><span data-stu-id="41361-213">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="41361-214">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-214">**Why**</span></span>

<span data-ttu-id="41361-215">`IRelationalModel` représentation qui vient d’être ajoutée au schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="41361-215">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="41361-216">Son utilisation pour trouver les différences est plus rapide et plus précise.</span><span class="sxs-lookup"><span data-stu-id="41361-216">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="41361-217">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-217">**Mitigations**</span></span>

<span data-ttu-id="41361-218">Utilisez le code suivant pour comparer le modèle à `snapshot` l’aide du modèle à partir de `context` :</span><span class="sxs-lookup"><span data-stu-id="41361-218">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```cs
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

<span data-ttu-id="41361-219">Nous envisageons d’améliorer cette expérience dans 6,0 ([voir #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="41361-219">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="41361-220">Les discriminateurs sont en lecture seule</span><span class="sxs-lookup"><span data-stu-id="41361-220">Discriminators are read-only</span></span>

[<span data-ttu-id="41361-221">#21154 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-221">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="41361-222">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-222">**Old behavior**</span></span>

<span data-ttu-id="41361-223">Il était possible de modifier la valeur du discriminateur avant d’appeler `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="41361-223">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="41361-224">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-224">**New behavior**</span></span>

<span data-ttu-id="41361-225">Une exception sera levée dans le cas ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="41361-225">An exception will be throws in the above case.</span></span>

<span data-ttu-id="41361-226">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-226">**Why**</span></span>

<span data-ttu-id="41361-227">EF ne s’attend pas à ce que le type d’entité change pendant qu’il est toujours suivi. la modification de la valeur de discriminateur laisse le contexte dans un état incohérent, ce qui peut entraîner un comportement inattendu.</span><span class="sxs-lookup"><span data-stu-id="41361-227">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="41361-228">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-228">**Mitigations**</span></span>

<span data-ttu-id="41361-229">Si la modification de la valeur de discriminateur est nécessaire et que le contexte est supprimé immédiatement après l’appel `SaveChanges` de, le discriminateur peut devenir mutable :</span><span class="sxs-lookup"><span data-stu-id="41361-229">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="41361-230">La définition de la requête est remplacée par des méthodes spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="41361-230">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="41361-231">#18903 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-231">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="41361-232">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-232">**Old behavior**</span></span>

<span data-ttu-id="41361-233">Les types d’entité ont été mappés à la définition de requêtes au niveau du noyau.</span><span class="sxs-lookup"><span data-stu-id="41361-233">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="41361-234">Chaque fois que le type d’entité a été utilisé dans la racine de la requête du type d’entité a été remplacé par la requête de définition pour n’importe quel fournisseur.</span><span class="sxs-lookup"><span data-stu-id="41361-234">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="41361-235">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-235">**New behavior**</span></span>

<span data-ttu-id="41361-236">Les API pour la définition de la requête sont déconseillées.</span><span class="sxs-lookup"><span data-stu-id="41361-236">APIs for defining query are deprecated.</span></span> <span data-ttu-id="41361-237">De nouvelles API spécifiques au fournisseur ont été introduites.</span><span class="sxs-lookup"><span data-stu-id="41361-237">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="41361-238">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-238">**Why**</span></span>

<span data-ttu-id="41361-239">Alors que la définition de requêtes a été implémentée en tant que requête de remplacement chaque fois que la racine de la requête est utilisée dans la requête, il y a quelques problèmes :</span><span class="sxs-lookup"><span data-stu-id="41361-239">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="41361-240">Si la définition de la requête consiste à projeter un type d’entité à l’aide `new { ... }` de dans `Select` la méthode, vous devez ensuite identifier qu’en tant qu’entité a requis un travail supplémentaire et qu’elle est incohérente avec la façon dont EF Core traite les types nominaux dans la requête.</span><span class="sxs-lookup"><span data-stu-id="41361-240">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="41361-241">Pour les fournisseurs relationnels `FromSql` , il est toujours nécessaire de passer la chaîne SQL sous forme d’expression LINQ.</span><span class="sxs-lookup"><span data-stu-id="41361-241">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="41361-242">La définition initiale des requêtes a été introduite en tant que vues côté client à utiliser avec le fournisseur en mémoire pour les entités keymoins (similaires aux vues de base de données dans les bases de données relationnelles).</span><span class="sxs-lookup"><span data-stu-id="41361-242">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="41361-243">Une telle définition facilite le test de l’application sur la base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="41361-243">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="41361-244">Par la suite, ils sont devenus largement applicables, ce qui était utile, mais incohérents et difficiles à comprendre.</span><span class="sxs-lookup"><span data-stu-id="41361-244">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="41361-245">Nous avons donc décidé de simplifier le concept.</span><span class="sxs-lookup"><span data-stu-id="41361-245">So we decided to simplify the concept.</span></span> <span data-ttu-id="41361-246">Nous avons fait en sorte que la requête de définition basée sur LINQ soit exclusive au fournisseur en mémoire et ne les traite pas de manière différente.</span><span class="sxs-lookup"><span data-stu-id="41361-246">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="41361-247">Pour plus d’informations, [consultez ce problème](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="41361-247">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="41361-248">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-248">**Mitigations**</span></span>

<span data-ttu-id="41361-249">Pour les fournisseurs relationnels, utilisez `ToSqlQuery` la méthode dans `OnModelCreating` et transmettez une chaîne SQL à utiliser pour le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="41361-249">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="41361-250">Pour le fournisseur en mémoire, utilisez la `ToInMemoryQuery` méthode dans `OnModelCreating` et transmettez une requête LINQ à utiliser pour le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="41361-250">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="41361-251">EF spécifique au fournisseur. Les méthodes Functions lèvent pour le fournisseur InMemory</span><span class="sxs-lookup"><span data-stu-id="41361-251">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="41361-252">#20294 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="41361-252">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="41361-253">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-253">**Old behavior**</span></span>

<span data-ttu-id="41361-254">EF spécifique au fournisseur. Les méthodes Functions contenaient une implémentation pour l’exécution du client, ce qui leur permettait d’être exécutées sur le fournisseur d’InMemory.</span><span class="sxs-lookup"><span data-stu-id="41361-254">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="41361-255">Par exemple, `EF.Functions.DateDiffDay` est une méthode propre à SQL Server, qui fonctionnait sur le fournisseur d’InMemory.</span><span class="sxs-lookup"><span data-stu-id="41361-255">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="41361-256">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="41361-256">**New behavior**</span></span>

<span data-ttu-id="41361-257">Les méthodes spécifiques au fournisseur ont été mises à jour pour lever une exception dans leur corps de méthode afin de bloquer leur évaluation côté client.</span><span class="sxs-lookup"><span data-stu-id="41361-257">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="41361-258">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="41361-258">**Why**</span></span>

<span data-ttu-id="41361-259">Les méthodes spécifiques au fournisseur sont mappées à une fonction de base de données.</span><span class="sxs-lookup"><span data-stu-id="41361-259">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="41361-260">Le calcul effectué par la fonction de base de données mappée ne peut pas toujours être répliqué côté client dans LINQ.</span><span class="sxs-lookup"><span data-stu-id="41361-260">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="41361-261">Cela peut provoquer la différence entre le résultat du serveur et l’exécution de la même méthode sur le client.</span><span class="sxs-lookup"><span data-stu-id="41361-261">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="41361-262">Étant donné que ces méthodes sont utilisées dans LINQ pour traduire des fonctions de base de données spécifiques, elles n’ont pas besoin d’être évaluées côté client.</span><span class="sxs-lookup"><span data-stu-id="41361-262">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="41361-263">Comme le fournisseur d’InMemory est une *base de données*différente, ces méthodes ne sont pas disponibles pour ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="41361-263">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="41361-264">Si vous tentez de les exécuter pour le fournisseur d’InMemory ou tout autre fournisseur qui ne traduit pas ces méthodes, lève une exception.</span><span class="sxs-lookup"><span data-stu-id="41361-264">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="41361-265">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="41361-265">**Mitigations**</span></span>

<span data-ttu-id="41361-266">Étant donné qu’il n’existe aucun moyen de reproduire le comportement des fonctions de base de données avec précision, vous devez tester les requêtes qui les contiennent sur le même type de base de données qu’en production.</span><span class="sxs-lookup"><span data-stu-id="41361-266">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>
