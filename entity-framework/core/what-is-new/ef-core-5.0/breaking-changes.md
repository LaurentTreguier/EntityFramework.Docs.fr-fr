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
# <a name="breaking-changes-in-ef-core-50"></a>Modifications avec rupture dans EF Core 5,0

Les modifications d’API et de comportement suivantes peuvent bloquer les mises à jour des applications existantes en EF Core 5.0.0.

## <a name="summary"></a>Résumé

| **Modification critique**                                                                                                                   | **Impact** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [Obligatoire sur la navigation du principal au dépendant a une sémantique différente](#required-dependent)                                 | Moyenne     |
| [Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite](#geometric-sqlite)                                                   | Faible        |
| [Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé](#non-added-generation)  | Faible        |
| [IMigrationsModelDiffer utilise désormais IRelationalModel](#relational-model)                                                                 | Faible        |
| [Les discriminateurs sont en lecture seule](#read-only-discriminators)                                                                             | Faible        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite

[#14257 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Ancien comportement**

HasGeometricDimension a été utilisé pour activer des dimensions supplémentaires (Z et M) sur les colonnes Geometry. Toutefois, il n’a jamais été affecté par la création de bases de données. Il n’était pas nécessaire de le spécifier pour interroger des valeurs avec des dimensions supplémentaires. Elle ne fonctionnait pas non plus correctement lors de l’insertion ou de la mise à jour de valeurs avec des dimensions supplémentaires ([voir #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Nouveau comportement**

Pour permettre l’insertion et la mise à jour de valeurs géométriques avec des dimensions supplémentaires (Z et M), la dimension doit être spécifiée en tant que partie du nom du type de colonne. Cela correspond plus précisément au comportement sous-jacent de la fonction AddGeometryColumn de SpatiaLite.

**Pourquoi**

L’utilisation de HasGeometricDimension après avoir spécifié la dimension dans le type de colonne est inutile et redondante, donc nous avons supprimé HasGeometricDimension entièrement.

**Corrections**

Utilisez `HasColumnType` pour spécifier la dimension :

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
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>Obligatoire sur la navigation du principal au dépendant a une sémantique différente

[#17286 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

**Ancien comportement**

Seules les navigations vers le principal peuvent être configurées selon les besoins. Par conséquent, l’utilisation `RequiredAttribute` de sur la navigation vers le dépendant (l’entité qui contient la clé étrangère) créerait à la place la clé étrangère sur le type d’entité de définition.

**Nouveau comportement**

Avec la prise en charge supplémentaire pour les dépendants requis, il est désormais possible de marquer toute navigation de référence comme obligatoire, ce qui signifie que dans le cas indiqué au-dessus de la clé étrangère sera défini de l’autre côté de la relation et que les propriétés ne seront pas marquées comme obligatoires.

`IsRequired`L’appel de avant la spécification de la terminaison dépendante est maintenant ambigu :

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

**Pourquoi**

Le nouveau comportement est nécessaire pour activer la prise en charge des dépendants requis ([voir #12100](https://github.com/dotnet/efcore/issues/12100)).

**Corrections**

Supprimez `RequiredAttribute` de la navigation vers le dépendant et placez-le à la place sur la navigation vers le principal ou configurez la relation dans `OnModelCreating` :

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé

[#15289 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**Ancien comportement**

Les générateurs de valeur ont été appelés uniquement lorsque l’état de l’entité a été remplacé par ajouté.

**Nouveau comportement**

Les générateurs de valeurs sont désormais appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé et que la propriété contient les valeurs par défaut.

**Pourquoi**

Cette modification était nécessaire pour améliorer l’expérience des propriétés qui ne sont pas rendues persistantes dans le magasin de données et dont la valeur doit être générée systématiquement sur le client.

**Corrections**

Pour empêcher le générateur de valeurs d’être appelé, assignez une valeur non définie par défaut à la propriété avant la modification de l’État.

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer utilise désormais IRelationalModel

[#20305 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

**Ancien comportement**

`IMigrationsModelDiffer` L’API a été définie à l’aide de `IModel` .

**Nouveau comportement**

`IMigrationsModelDiffer` L’API utilise désormais `IRelationalModel` . Toutefois, l’instantané de modèle contient toujours uniquement `IModel` , car ce code fait partie de l’application et Entity Framework ne peut pas le modifier sans apporter de modification plus importante.

**Pourquoi**

`IRelationalModel` représentation qui vient d’être ajoutée au schéma de la base de données. Son utilisation pour trouver les différences est plus rapide et plus précise.

**Corrections**

Utilisez le code suivant pour comparer le modèle à `snapshot` l’aide du modèle à partir de `context` :

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

Nous envisageons d’améliorer cette expérience dans 6,0 ([voir #22031](https://github.com/dotnet/efcore/issues/22031))

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a>Les discriminateurs sont en lecture seule

[#21154 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

**Ancien comportement**

Il était possible de modifier la valeur du discriminateur avant d’appeler `SaveChanges`

**Nouveau comportement**

Une exception sera levée dans le cas ci-dessus.

**Pourquoi**

EF ne s’attend pas à ce que le type d’entité change pendant qu’il est toujours suivi. la modification de la valeur de discriminateur laisse le contexte dans un état incohérent, ce qui peut entraîner un comportement inattendu.

**Corrections**

Si la modification de la valeur de discriminateur est nécessaire et si le contexte est supprimé immédiatement après `SaveChanges` l’appel du discriminateur, vous pouvez le rendre mutable :

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
