---
title: Modifications avec rupture dans EF Core 5,0-EF Core
description: Liste complète des modifications avec rupture introduites dans Entity Framework Core 5,0
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 63fd1d1a01b7a72fd34bb9a0130191131306426c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070794"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="cd5c6-103">Modifications avec rupture dans EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="cd5c6-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="cd5c6-104">Les modifications d’API et de comportement suivantes peuvent bloquer les mises à jour des applications existantes en EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="cd5c6-105">Résumé</span><span class="sxs-lookup"><span data-stu-id="cd5c6-105">Summary</span></span>

| <span data-ttu-id="cd5c6-106">**Modification critique**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="cd5c6-107">**Impact**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="cd5c6-108">Obligatoire sur la navigation du principal au dépendant a une sémantique différente</span><span class="sxs-lookup"><span data-stu-id="cd5c6-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="cd5c6-109">Moyenne</span><span class="sxs-lookup"><span data-stu-id="cd5c6-109">Medium</span></span>     |
| [<span data-ttu-id="cd5c6-110">La définition de la requête est remplacée par des méthodes spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="cd5c6-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="cd5c6-111">Moyenne</span><span class="sxs-lookup"><span data-stu-id="cd5c6-111">Medium</span></span>     |
| [<span data-ttu-id="cd5c6-112">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="cd5c6-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="cd5c6-113">Faible</span><span class="sxs-lookup"><span data-stu-id="cd5c6-113">Low</span></span>        |
| [<span data-ttu-id="cd5c6-114">Cosmos : la clé de partition est maintenant ajoutée à la clé primaire</span><span class="sxs-lookup"><span data-stu-id="cd5c6-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="cd5c6-115">Faible</span><span class="sxs-lookup"><span data-stu-id="cd5c6-115">Low</span></span>        |
| [<span data-ttu-id="cd5c6-116">Cosmos : `id` propriété renommée en `__id`</span><span class="sxs-lookup"><span data-stu-id="cd5c6-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="cd5c6-117">Faible</span><span class="sxs-lookup"><span data-stu-id="cd5c6-117">Low</span></span>        |
| <span data-ttu-id="cd5c6-118">[Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="cd5c6-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="cd5c6-119">Faible</span><span class="sxs-lookup"><span data-stu-id="cd5c6-119">Low</span></span>        |
| [<span data-ttu-id="cd5c6-120">Cosmos : GetPropertyName et SetPropertyName ont été renommés</span><span class="sxs-lookup"><span data-stu-id="cd5c6-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="cd5c6-121">Faible</span><span class="sxs-lookup"><span data-stu-id="cd5c6-121">Low</span></span>        |
| [<span data-ttu-id="cd5c6-122">Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé</span><span class="sxs-lookup"><span data-stu-id="cd5c6-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="cd5c6-123">Faible</span><span class="sxs-lookup"><span data-stu-id="cd5c6-123">Low</span></span>        |
| [<span data-ttu-id="cd5c6-124">IMigrationsModelDiffer utilise désormais IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="cd5c6-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="cd5c6-125">Faible</span><span class="sxs-lookup"><span data-stu-id="cd5c6-125">Low</span></span>        |
| [<span data-ttu-id="cd5c6-126">Les discriminateurs sont en lecture seule</span><span class="sxs-lookup"><span data-stu-id="cd5c6-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="cd5c6-127">Faible</span><span class="sxs-lookup"><span data-stu-id="cd5c6-127">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="cd5c6-128">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="cd5c6-128">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="cd5c6-129">#14257 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="cd5c6-129">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="cd5c6-130">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-130">**Old behavior**</span></span>

<span data-ttu-id="cd5c6-131">HasGeometricDimension a été utilisé pour activer des dimensions supplémentaires (Z et M) sur les colonnes Geometry.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-131">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="cd5c6-132">Toutefois, il n’a jamais été affecté par la création de bases de données.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-132">However, it only ever affected database creation.</span></span> <span data-ttu-id="cd5c6-133">Il n’était pas nécessaire de le spécifier pour interroger des valeurs avec des dimensions supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-133">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="cd5c6-134">Elle ne fonctionnait pas non plus correctement lors de l’insertion ou de la mise à jour de valeurs avec des dimensions supplémentaires ([voir #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="cd5c6-134">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="cd5c6-135">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-135">**New behavior**</span></span>

<span data-ttu-id="cd5c6-136">Pour permettre l’insertion et la mise à jour de valeurs géométriques avec des dimensions supplémentaires (Z et M), la dimension doit être spécifiée en tant que partie du nom du type de colonne.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-136">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="cd5c6-137">Cette API correspond plus étroitement au comportement sous-jacent de la fonction AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-137">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="cd5c6-138">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-138">**Why**</span></span>

<span data-ttu-id="cd5c6-139">L’utilisation de HasGeometricDimension après avoir spécifié la dimension dans le type de colonne est inutile et redondante, donc nous avons supprimé HasGeometricDimension entièrement.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-139">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="cd5c6-140">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-140">**Mitigations**</span></span>

<span data-ttu-id="cd5c6-141">Utilisez `HasColumnType` pour spécifier la dimension :</span><span class="sxs-lookup"><span data-stu-id="cd5c6-141">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="cd5c6-142">Obligatoire sur la navigation du principal au dépendant a une sémantique différente</span><span class="sxs-lookup"><span data-stu-id="cd5c6-142">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="cd5c6-143">#17286 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="cd5c6-143">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="cd5c6-144">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-144">**Old behavior**</span></span>

<span data-ttu-id="cd5c6-145">Seules les navigations vers le principal peuvent être configurées selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-145">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="cd5c6-146">Par conséquent, l’utilisation `RequiredAttribute` de sur la navigation vers le dépendant (l’entité qui contient la clé étrangère) créerait à la place la clé étrangère sur le type d’entité de définition.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-146">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="cd5c6-147">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-147">**New behavior**</span></span>

<span data-ttu-id="cd5c6-148">Avec la prise en charge supplémentaire des dépendants requis, il est désormais possible de marquer toute navigation de référence comme étant obligatoire, ce qui signifie que dans le cas indiqué au-dessus de la clé étrangère sera défini de l’autre côté de la relation et que les propriétés ne seront pas marquées comme obligatoires.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-148">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="cd5c6-149">`IsRequired`L’appel de avant la spécification de la terminaison dépendante est maintenant ambigu :</span><span class="sxs-lookup"><span data-stu-id="cd5c6-149">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="cd5c6-150">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-150">**Why**</span></span>

<span data-ttu-id="cd5c6-151">Le nouveau comportement est nécessaire pour activer la prise en charge des dépendants requis ([voir #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="cd5c6-151">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="cd5c6-152">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-152">**Mitigations**</span></span>

<span data-ttu-id="cd5c6-153">Supprimez `RequiredAttribute` de la navigation vers le dépendant et placez-le à la place sur la navigation vers le principal ou configurez la relation dans `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="cd5c6-153">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="cd5c6-154">Cosmos : la clé de partition est maintenant ajoutée à la clé primaire</span><span class="sxs-lookup"><span data-stu-id="cd5c6-154">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="cd5c6-155">#15289 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="cd5c6-155">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="cd5c6-156">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-156">**Old behavior**</span></span>

<span data-ttu-id="cd5c6-157">La propriété de clé de partition a été uniquement ajoutée à l’autre clé qui comprend `id` .</span><span class="sxs-lookup"><span data-stu-id="cd5c6-157">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="cd5c6-158">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-158">**New behavior**</span></span>

<span data-ttu-id="cd5c6-159">La propriété de clé de partition est maintenant également ajoutée à la clé primaire par Convention.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-159">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="cd5c6-160">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-160">**Why**</span></span>

<span data-ttu-id="cd5c6-161">Cette modification rend le modèle mieux aligné avec Azure Cosmos DB sémantique et améliore les performances de `Find` et de certaines requêtes.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-161">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="cd5c6-162">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-162">**Mitigations**</span></span>

<span data-ttu-id="cd5c6-163">Pour empêcher que la propriété de clé de partition soit ajoutée à la clé primaire, configurez-la dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="cd5c6-163">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="cd5c6-164">Cosmos : `id` propriété renommée en `__id`</span><span class="sxs-lookup"><span data-stu-id="cd5c6-164">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="cd5c6-165">#17751 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="cd5c6-165">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="cd5c6-166">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-166">**Old behavior**</span></span>

<span data-ttu-id="cd5c6-167">La propriété Shadow mappée à la `id` propriété JSON a également été nommée `id` .</span><span class="sxs-lookup"><span data-stu-id="cd5c6-167">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="cd5c6-168">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-168">**New behavior**</span></span>

<span data-ttu-id="cd5c6-169">La propriété Shadow créée par la Convention est maintenant nommée `__id` .</span><span class="sxs-lookup"><span data-stu-id="cd5c6-169">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="cd5c6-170">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-170">**Why**</span></span>

<span data-ttu-id="cd5c6-171">Cette modification rend moins probable la `id` propriété en conflit avec une propriété existante sur le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-171">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="cd5c6-172">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-172">**Mitigations**</span></span>

<span data-ttu-id="cd5c6-173">Pour revenir au comportement 3. x, configurez la `id` propriété dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="cd5c6-173">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="cd5c6-174">Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres</span><span class="sxs-lookup"><span data-stu-id="cd5c6-174">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="cd5c6-175">#17306 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="cd5c6-175">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="cd5c6-176">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-176">**Old behavior**</span></span>

<span data-ttu-id="cd5c6-177">Les propriétés de type Byte [] étaient stockées sous la forme d’un tableau de nombres.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-177">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="cd5c6-178">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-178">**New behavior**</span></span>

<span data-ttu-id="cd5c6-179">Les propriétés de type Byte [] sont désormais stockées sous la forme d’une chaîne base64.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-179">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="cd5c6-180">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-180">**Why**</span></span>

<span data-ttu-id="cd5c6-181">Cette représentation de Byte [] s’aligne mieux avec les attentes et est le comportement par défaut des principales bibliothèques de sérialisation JSON.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-181">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="cd5c6-182">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-182">**Mitigations**</span></span>

<span data-ttu-id="cd5c6-183">Les données existantes stockées en tant que tableaux de nombres seront toujours interrogées correctement, mais il n’existe actuellement aucune méthode prise en charge pour modifier le comportement d’insertion.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-183">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="cd5c6-184">Si cette limitation bloque votre scénario, commentez [-le](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span><span class="sxs-lookup"><span data-stu-id="cd5c6-184">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="cd5c6-185">Cosmos : GetPropertyName et SetPropertyName ont été renommés</span><span class="sxs-lookup"><span data-stu-id="cd5c6-185">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="cd5c6-186">#17874 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="cd5c6-186">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="cd5c6-187">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-187">**Old behavior**</span></span>

<span data-ttu-id="cd5c6-188">Auparavant, les méthodes d’extension étaient appelées `GetPropertyName` et `SetPropertyName`</span><span class="sxs-lookup"><span data-stu-id="cd5c6-188">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="cd5c6-189">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-189">**New behavior**</span></span>

<span data-ttu-id="cd5c6-190">L’ancienne API a été obsolète et de nouvelles méthodes ont été ajoutées : `GetJsonPropertyName` , `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="cd5c6-190">The old API was obsoleted and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="cd5c6-191">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-191">**Why**</span></span>

<span data-ttu-id="cd5c6-192">Cette modification supprime l’ambiguïté autour de la configuration de ces méthodes.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-192">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="cd5c6-193">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-193">**Mitigations**</span></span>

<span data-ttu-id="cd5c6-194">Utilisez la nouvelle API ou suspendez temporairement les avertissements obsolètes.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-194">Use the new API or temporarily suspend the obsolete warnings.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="cd5c6-195">Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé</span><span class="sxs-lookup"><span data-stu-id="cd5c6-195">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="cd5c6-196">#15289 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="cd5c6-196">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="cd5c6-197">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-197">**Old behavior**</span></span>

<span data-ttu-id="cd5c6-198">Les générateurs de valeur ont été appelés uniquement lorsque l’état de l’entité a été remplacé par ajouté.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-198">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="cd5c6-199">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-199">**New behavior**</span></span>

<span data-ttu-id="cd5c6-200">Les générateurs de valeurs sont désormais appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé et que la propriété contient les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-200">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="cd5c6-201">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-201">**Why**</span></span>

<span data-ttu-id="cd5c6-202">Cette modification était nécessaire pour améliorer l’expérience des propriétés qui ne sont pas rendues persistantes dans le magasin de données et dont la valeur doit être générée systématiquement sur le client.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-202">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="cd5c6-203">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-203">**Mitigations**</span></span>

<span data-ttu-id="cd5c6-204">Pour empêcher l’appel du générateur de valeurs, assignez une valeur non définie par défaut à la propriété avant la modification de l’État.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-204">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="cd5c6-205">IMigrationsModelDiffer utilise désormais IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="cd5c6-205">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="cd5c6-206">#20305 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="cd5c6-206">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="cd5c6-207">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-207">**Old behavior**</span></span>

<span data-ttu-id="cd5c6-208">`IMigrationsModelDiffer` L’API a été définie à l’aide de `IModel` .</span><span class="sxs-lookup"><span data-stu-id="cd5c6-208">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="cd5c6-209">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-209">**New behavior**</span></span>

<span data-ttu-id="cd5c6-210">`IMigrationsModelDiffer` L’API utilise désormais `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="cd5c6-210">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="cd5c6-211">Toutefois, l’instantané de modèle contient toujours uniquement `IModel` , car ce code fait partie de l’application et Entity Framework ne peut pas le modifier sans apporter de modification plus importante.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-211">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="cd5c6-212">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-212">**Why**</span></span>

<span data-ttu-id="cd5c6-213">`IRelationalModel` représentation qui vient d’être ajoutée au schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-213">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="cd5c6-214">Son utilisation pour trouver les différences est plus rapide et plus précise.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-214">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="cd5c6-215">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-215">**Mitigations**</span></span>

<span data-ttu-id="cd5c6-216">Utilisez le code suivant pour comparer le modèle à `snapshot` l’aide du modèle à partir de `context` :</span><span class="sxs-lookup"><span data-stu-id="cd5c6-216">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="cd5c6-217">Nous envisageons d’améliorer cette expérience dans 6,0 ([voir #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="cd5c6-217">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="cd5c6-218">Les discriminateurs sont en lecture seule</span><span class="sxs-lookup"><span data-stu-id="cd5c6-218">Discriminators are read-only</span></span>

[<span data-ttu-id="cd5c6-219">#21154 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="cd5c6-219">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="cd5c6-220">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-220">**Old behavior**</span></span>

<span data-ttu-id="cd5c6-221">Il était possible de modifier la valeur du discriminateur avant d’appeler `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="cd5c6-221">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="cd5c6-222">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-222">**New behavior**</span></span>

<span data-ttu-id="cd5c6-223">Une exception sera levée dans le cas ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-223">An exception will be throws in the above case.</span></span>

<span data-ttu-id="cd5c6-224">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-224">**Why**</span></span>

<span data-ttu-id="cd5c6-225">EF ne s’attend pas à ce que le type d’entité change pendant qu’il est toujours suivi. la modification de la valeur de discriminateur laisse le contexte dans un état incohérent, ce qui peut entraîner un comportement inattendu.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-225">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="cd5c6-226">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-226">**Mitigations**</span></span>

<span data-ttu-id="cd5c6-227">Si la modification de la valeur de discriminateur est nécessaire et que le contexte est supprimé immédiatement après l’appel `SaveChanges` de, le discriminateur peut devenir mutable :</span><span class="sxs-lookup"><span data-stu-id="cd5c6-227">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="cd5c6-228">La définition de la requête est remplacée par des méthodes spécifiques au fournisseur</span><span class="sxs-lookup"><span data-stu-id="cd5c6-228">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="cd5c6-229">#18903 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="cd5c6-229">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="cd5c6-230">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-230">**Old behavior**</span></span>

<span data-ttu-id="cd5c6-231">Les types d’entité ont été mappés à la définition de requêtes au niveau du noyau.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-231">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="cd5c6-232">Chaque fois que le type d’entité a été utilisé dans la racine de la requête du type d’entité a été remplacé par la requête de définition pour n’importe quel fournisseur.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-232">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="cd5c6-233">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-233">**New behavior**</span></span>

<span data-ttu-id="cd5c6-234">Les API pour la définition de la requête sont déconseillées.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-234">APIs for defining query are deprecated.</span></span> <span data-ttu-id="cd5c6-235">De nouvelles API spécifiques au fournisseur ont été introduites.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-235">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="cd5c6-236">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-236">**Why**</span></span>

<span data-ttu-id="cd5c6-237">Alors que la définition de requêtes a été implémentée en tant que requête de remplacement chaque fois que la racine de la requête est utilisée dans la requête, il y a quelques problèmes :</span><span class="sxs-lookup"><span data-stu-id="cd5c6-237">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="cd5c6-238">Si la définition de la requête consiste à projeter un type d’entité à l’aide `new { ... }` de dans `Select` la méthode, vous devez ensuite identifier qu’en tant qu’entité a requis un travail supplémentaire et qu’elle est incohérente avec la façon dont EF Core traite les types nominaux dans la requête.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-238">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="cd5c6-239">Pour les fournisseurs relationnels `FromSql` , il est toujours nécessaire de passer la chaîne SQL sous forme d’expression LINQ.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-239">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="cd5c6-240">La définition initiale des requêtes a été introduite en tant que vues côté client à utiliser avec le fournisseur en mémoire pour les entités keymoins (similaires aux vues de base de données dans les bases de données relationnelles).</span><span class="sxs-lookup"><span data-stu-id="cd5c6-240">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="cd5c6-241">Une telle définition facilite le test de l’application sur la base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-241">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="cd5c6-242">Par la suite, ils sont devenus largement applicables, ce qui était utile, mais incohérents et difficiles à comprendre.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-242">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="cd5c6-243">Nous avons donc décidé de simplifier le concept.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-243">So we decided to simplify the concept.</span></span> <span data-ttu-id="cd5c6-244">Nous avons fait en sorte que la requête de définition basée sur LINQ soit exclusive au fournisseur en mémoire et ne les traite pas de manière différente.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-244">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="cd5c6-245">Pour plus d’informations, [consultez ce problème](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="cd5c6-245">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="cd5c6-246">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="cd5c6-246">**Mitigations**</span></span>

<span data-ttu-id="cd5c6-247">Pour les fournisseurs relationnels, utilisez `ToSqlQuery` la méthode dans `OnModelCreating` et transmettez une chaîne SQL à utiliser pour le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-247">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="cd5c6-248">Pour le fournisseur en mémoire, utilisez la `ToInMemoryQuery` méthode dans `OnModelCreating` et transmettez une requête LINQ à utiliser pour le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="cd5c6-248">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>
