---
title: Modifications avec rupture dans EF Core 5,0-EF Core
description: Liste complète des modifications avec rupture introduites dans Entity Framework Core 5,0
author: bricelam
ms.date: 09/08/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: bc6db48edcd7406b31ec2b4369cabf5d55fb4578
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618682"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="346a4-103">Modifications avec rupture dans EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="346a4-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="346a4-104">Les modifications d’API et de comportement suivantes peuvent bloquer les mises à jour des applications existantes en EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="346a4-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="346a4-105">Résumé</span><span class="sxs-lookup"><span data-stu-id="346a4-105">Summary</span></span>

| <span data-ttu-id="346a4-106">**Modification critique**</span><span class="sxs-lookup"><span data-stu-id="346a4-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="346a4-107">**Impact**</span><span class="sxs-lookup"><span data-stu-id="346a4-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="346a4-108">Obligatoire sur la navigation du principal au dépendant a une sémantique différente</span><span class="sxs-lookup"><span data-stu-id="346a4-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="346a4-109">Moyenne</span><span class="sxs-lookup"><span data-stu-id="346a4-109">Medium</span></span>     |
| [<span data-ttu-id="346a4-110">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="346a4-110">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="346a4-111">Faible</span><span class="sxs-lookup"><span data-stu-id="346a4-111">Low</span></span>        |
| [<span data-ttu-id="346a4-112">Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé</span><span class="sxs-lookup"><span data-stu-id="346a4-112">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>](#non-added-generation)  | <span data-ttu-id="346a4-113">Faible</span><span class="sxs-lookup"><span data-stu-id="346a4-113">Low</span></span>        |
| [<span data-ttu-id="346a4-114">IMigrationsModelDiffer utilise désormais IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="346a4-114">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="346a4-115">Faible</span><span class="sxs-lookup"><span data-stu-id="346a4-115">Low</span></span>        |
| [<span data-ttu-id="346a4-116">Les discriminateurs sont en lecture seule</span><span class="sxs-lookup"><span data-stu-id="346a4-116">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="346a4-117">Faible</span><span class="sxs-lookup"><span data-stu-id="346a4-117">Low</span></span>        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="346a4-118">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="346a4-118">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="346a4-119">#14257 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="346a4-119">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="346a4-120">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="346a4-120">**Old behavior**</span></span>

<span data-ttu-id="346a4-121">HasGeometricDimension a été utilisé pour activer des dimensions supplémentaires (Z et M) sur les colonnes Geometry.</span><span class="sxs-lookup"><span data-stu-id="346a4-121">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="346a4-122">Toutefois, il n’a jamais été affecté par la création de bases de données.</span><span class="sxs-lookup"><span data-stu-id="346a4-122">However, it only ever affected database creation.</span></span> <span data-ttu-id="346a4-123">Il n’était pas nécessaire de le spécifier pour interroger des valeurs avec des dimensions supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="346a4-123">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="346a4-124">Elle ne fonctionnait pas non plus correctement lors de l’insertion ou de la mise à jour de valeurs avec des dimensions supplémentaires ([voir #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="346a4-124">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="346a4-125">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="346a4-125">**New behavior**</span></span>

<span data-ttu-id="346a4-126">Pour permettre l’insertion et la mise à jour de valeurs géométriques avec des dimensions supplémentaires (Z et M), la dimension doit être spécifiée en tant que partie du nom du type de colonne.</span><span class="sxs-lookup"><span data-stu-id="346a4-126">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="346a4-127">Cela correspond plus précisément au comportement sous-jacent de la fonction AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="346a4-127">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="346a4-128">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="346a4-128">**Why**</span></span>

<span data-ttu-id="346a4-129">L’utilisation de HasGeometricDimension après avoir spécifié la dimension dans le type de colonne est inutile et redondante, donc nous avons supprimé HasGeometricDimension entièrement.</span><span class="sxs-lookup"><span data-stu-id="346a4-129">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="346a4-130">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="346a4-130">**Mitigations**</span></span>

<span data-ttu-id="346a4-131">Utilisez `HasColumnType` pour spécifier la dimension :</span><span class="sxs-lookup"><span data-stu-id="346a4-131">Use `HasColumnType` to specify the dimension:</span></span>

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
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="346a4-132">Obligatoire sur la navigation du principal au dépendant a une sémantique différente</span><span class="sxs-lookup"><span data-stu-id="346a4-132">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="346a4-133">#17286 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="346a4-133">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="346a4-134">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="346a4-134">**Old behavior**</span></span>

<span data-ttu-id="346a4-135">Seules les navigations vers le principal peuvent être configurées selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="346a4-135">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="346a4-136">Par conséquent, l’utilisation `RequiredAttribute` de sur la navigation vers le dépendant (l’entité qui contient la clé étrangère) créerait à la place la clé étrangère sur le type d’entité de définition.</span><span class="sxs-lookup"><span data-stu-id="346a4-136">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="346a4-137">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="346a4-137">**New behavior**</span></span>

<span data-ttu-id="346a4-138">Avec la prise en charge supplémentaire pour les dépendants requis, il est désormais possible de marquer toute navigation de référence comme obligatoire, ce qui signifie que dans le cas indiqué au-dessus de la clé étrangère sera défini de l’autre côté de la relation et que les propriétés ne seront pas marquées comme obligatoires.</span><span class="sxs-lookup"><span data-stu-id="346a4-138">With the added support for required dependents it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="346a4-139">`IsRequired`L’appel de avant la spécification de la terminaison dépendante est maintenant ambigu :</span><span class="sxs-lookup"><span data-stu-id="346a4-139">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="346a4-140">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="346a4-140">**Why**</span></span>

<span data-ttu-id="346a4-141">Le nouveau comportement est nécessaire pour activer la prise en charge des dépendants requis ([voir #12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="346a4-141">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="346a4-142">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="346a4-142">**Mitigations**</span></span>

<span data-ttu-id="346a4-143">Supprimez `RequiredAttribute` de la navigation vers le dépendant et placez-le à la place sur la navigation vers le principal ou configurez la relation dans `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="346a4-143">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="346a4-144">Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé</span><span class="sxs-lookup"><span data-stu-id="346a4-144">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>

[<span data-ttu-id="346a4-145">#15289 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="346a4-145">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="346a4-146">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="346a4-146">**Old behavior**</span></span>

<span data-ttu-id="346a4-147">Les générateurs de valeur ont été appelés uniquement lorsque l’état de l’entité a été remplacé par ajouté.</span><span class="sxs-lookup"><span data-stu-id="346a4-147">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="346a4-148">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="346a4-148">**New behavior**</span></span>

<span data-ttu-id="346a4-149">Les générateurs de valeurs sont désormais appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé et que la propriété contient les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="346a4-149">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="346a4-150">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="346a4-150">**Why**</span></span>

<span data-ttu-id="346a4-151">Cette modification était nécessaire pour améliorer l’expérience des propriétés qui ne sont pas rendues persistantes dans le magasin de données et dont la valeur doit être générée systématiquement sur le client.</span><span class="sxs-lookup"><span data-stu-id="346a4-151">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="346a4-152">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="346a4-152">**Mitigations**</span></span>

<span data-ttu-id="346a4-153">Pour empêcher le générateur de valeurs d’être appelé, assignez une valeur non définie par défaut à la propriété avant la modification de l’État.</span><span class="sxs-lookup"><span data-stu-id="346a4-153">To prevent the value generator from being called assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="346a4-154">IMigrationsModelDiffer utilise désormais IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="346a4-154">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="346a4-155">#20305 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="346a4-155">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="346a4-156">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="346a4-156">**Old behavior**</span></span>

<span data-ttu-id="346a4-157">`IMigrationsModelDiffer` L’API a été définie à l’aide de `IModel` .</span><span class="sxs-lookup"><span data-stu-id="346a4-157">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="346a4-158">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="346a4-158">**New behavior**</span></span>

<span data-ttu-id="346a4-159">`IMigrationsModelDiffer` L’API utilise désormais `IRelationalModel` .</span><span class="sxs-lookup"><span data-stu-id="346a4-159">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="346a4-160">Toutefois, l’instantané de modèle contient toujours uniquement `IModel` , car ce code fait partie de l’application et Entity Framework ne peut pas le modifier sans apporter de modification plus importante.</span><span class="sxs-lookup"><span data-stu-id="346a4-160">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="346a4-161">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="346a4-161">**Why**</span></span>

<span data-ttu-id="346a4-162">`IRelationalModel` représentation qui vient d’être ajoutée au schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="346a4-162">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="346a4-163">Son utilisation pour trouver les différences est plus rapide et plus précise.</span><span class="sxs-lookup"><span data-stu-id="346a4-163">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="346a4-164">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="346a4-164">**Mitigations**</span></span>

<span data-ttu-id="346a4-165">Utilisez le code suivant pour comparer le modèle à `snapshot` l’aide du modèle à partir de `context` :</span><span class="sxs-lookup"><span data-stu-id="346a4-165">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="346a4-166">Nous envisageons d’améliorer cette expérience dans 6,0 ([voir #22031](https://github.com/dotnet/efcore/issues/22031))</span><span class="sxs-lookup"><span data-stu-id="346a4-166">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a><span data-ttu-id="346a4-167">Les discriminateurs sont en lecture seule</span><span class="sxs-lookup"><span data-stu-id="346a4-167">Discriminators are read-only</span></span>

[<span data-ttu-id="346a4-168">#21154 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="346a4-168">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="346a4-169">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="346a4-169">**Old behavior**</span></span>

<span data-ttu-id="346a4-170">Il était possible de modifier la valeur du discriminateur avant d’appeler `SaveChanges`</span><span class="sxs-lookup"><span data-stu-id="346a4-170">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="346a4-171">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="346a4-171">**New behavior**</span></span>

<span data-ttu-id="346a4-172">Une exception sera levée dans le cas ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="346a4-172">An exception will be throws in the above case.</span></span>

<span data-ttu-id="346a4-173">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="346a4-173">**Why**</span></span>

<span data-ttu-id="346a4-174">EF ne s’attend pas à ce que le type d’entité change pendant qu’il est toujours suivi. la modification de la valeur de discriminateur laisse le contexte dans un état incohérent, ce qui peut entraîner un comportement inattendu.</span><span class="sxs-lookup"><span data-stu-id="346a4-174">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state which might result in unexpected behavior.</span></span>

<span data-ttu-id="346a4-175">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="346a4-175">**Mitigations**</span></span>

<span data-ttu-id="346a4-176">Si la modification de la valeur de discriminateur est nécessaire et si le contexte est supprimé immédiatement après `SaveChanges` l’appel du discriminateur, vous pouvez le rendre mutable :</span><span class="sxs-lookup"><span data-stu-id="346a4-176">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges` the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
