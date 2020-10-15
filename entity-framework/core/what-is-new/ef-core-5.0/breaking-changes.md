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
# <a name="breaking-changes-in-ef-core-50"></a>Modifications avec rupture dans EF Core 5,0

Les modifications d’API et de comportement suivantes peuvent bloquer les mises à jour des applications existantes en EF Core 5.0.0.

## <a name="summary"></a>Résumé

| **Modification critique**                                                                                                                   | **Impact** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [Obligatoire sur la navigation du principal au dépendant a une sémantique différente](#required-dependent)                                 | Medium     |
| [La définition de la requête est remplacée par des méthodes spécifiques au fournisseur](#defining-query)                                                          | Medium     |
| [Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite](#geometric-sqlite)                                                   | Faible        |
| [Cosmos : la clé de partition est maintenant ajoutée à la clé primaire](#cosmos-partition-key)                                                        | Faible        |
| [Cosmos : `id` propriété renommée en `__id`](#cosmos-id)                                                                                 | Faible        |
| [Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres](#cosmos-byte)                                             | Faible        |
| [Cosmos : GetPropertyName et SetPropertyName ont été renommés](#cosmos-metadata)                                                          | Faible        |
| [Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé](#non-added-generation) | Faible        |
| [IMigrationsModelDiffer utilise désormais IRelationalModel](#relational-model)                                                                 | Faible        |
| [Les discriminateurs sont en lecture seule](#read-only-discriminators)                                                                             | Faible        |
| [EF spécifique au fournisseur. Les méthodes Functions lèvent pour le fournisseur InMemory](#no-client-methods)                                              | Faible        |
| [IndexBuilder. HasName est désormais obsolète](#index-obsolete)                                                                               | Faible        |
| [Un pluarlizer est maintenant inclus pour la génération de modèles automatique de modèles rétroconçus](#pluralizer)                                                 | Faible        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite

[#14257 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Ancien comportement**

HasGeometricDimension a été utilisé pour activer des dimensions supplémentaires (Z et M) sur les colonnes Geometry. Toutefois, il n’a jamais été affecté par la création de bases de données. Il n’était pas nécessaire de le spécifier pour interroger des valeurs avec des dimensions supplémentaires. Elle ne fonctionnait pas non plus correctement lors de l’insertion ou de la mise à jour de valeurs avec des dimensions supplémentaires ([voir #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Nouveau comportement**

Pour permettre l’insertion et la mise à jour de valeurs géométriques avec des dimensions supplémentaires (Z et M), la dimension doit être spécifiée en tant que partie du nom du type de colonne. Cette API correspond plus étroitement au comportement sous-jacent de la fonction AddGeometryColumn de SpatiaLite.

**Pourquoi**

L’utilisation de HasGeometricDimension après avoir spécifié la dimension dans le type de colonne est inutile et redondante, donc nous avons supprimé HasGeometricDimension entièrement.

**Corrections**

Utilisez `HasColumnType` pour spécifier la dimension :

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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>Obligatoire sur la navigation du principal au dépendant a une sémantique différente

[#17286 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

**Ancien comportement**

Seules les navigations vers le principal peuvent être configurées selon les besoins. Par conséquent, l’utilisation `RequiredAttribute` de sur la navigation vers le dépendant (l’entité qui contient la clé étrangère) créerait à la place la clé étrangère sur le type d’entité de définition.

**Nouveau comportement**

Avec la prise en charge supplémentaire des dépendants requis, il est désormais possible de marquer toute navigation de référence comme étant obligatoire, ce qui signifie que dans le cas indiqué au-dessus de la clé étrangère sera défini de l’autre côté de la relation et que les propriétés ne seront pas marquées comme obligatoires.

`IsRequired`L’appel de avant la spécification de la terminaison dépendante est maintenant ambigu :

```csharp
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

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos : la clé de partition est maintenant ajoutée à la clé primaire

[#15289 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**Ancien comportement**

La propriété de clé de partition a été uniquement ajoutée à l’autre clé qui comprend `id` .

**Nouveau comportement**

La propriété de clé de partition est maintenant également ajoutée à la clé primaire par Convention.

**Pourquoi**

Cette modification rend le modèle mieux aligné avec Azure Cosmos DB sémantique et améliore les performances de `Find` et de certaines requêtes.

**Corrections**

Pour empêcher que la propriété de clé de partition soit ajoutée à la clé primaire, configurez-la dans `OnModelCreating` .

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos : `id` propriété renommée en `__id`

[#17751 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

**Ancien comportement**

La propriété Shadow mappée à la `id` propriété JSON a également été nommée `id` .

**Nouveau comportement**

La propriété Shadow créée par la Convention est maintenant nommée `__id` .

**Pourquoi**

Cette modification rend moins probable la `id` propriété en conflit avec une propriété existante sur le type d’entité.

**Corrections**

Pour revenir au comportement 3. x, configurez la `id` propriété dans `OnModelCreating` .

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres

[#17306 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

**Ancien comportement**

Les propriétés de type Byte [] étaient stockées sous la forme d’un tableau de nombres.

**Nouveau comportement**

Les propriétés de type Byte [] sont désormais stockées sous la forme d’une chaîne base64.

**Pourquoi**

Cette représentation de Byte [] s’aligne mieux avec les attentes et est le comportement par défaut des principales bibliothèques de sérialisation JSON.

**Corrections**

Les données existantes stockées en tant que tableaux de nombres seront toujours interrogées correctement, mais il n’existe actuellement aucune méthode prise en charge pour modifier le comportement d’insertion. Si cette limitation bloque votre scénario, commentez [-le](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos : GetPropertyName et SetPropertyName ont été renommés

[#17874 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

**Ancien comportement**

Auparavant, les méthodes d’extension étaient appelées `GetPropertyName` et `SetPropertyName`

**Nouveau comportement**

L’ancienne API a été supprimée et de nouvelles méthodes ont été ajoutées : `GetJsonPropertyName` , `SetJsonPropertyName`

**Pourquoi**

Cette modification supprime l’ambiguïté autour de la configuration de ces méthodes.

**Corrections**

Utilisez la nouvelle API.

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

Pour empêcher l’appel du générateur de valeurs, assignez une valeur non définie par défaut à la propriété avant la modification de l’État.

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

Si la modification de la valeur de discriminateur est nécessaire et que le contexte est supprimé immédiatement après l’appel `SaveChanges` de, le discriminateur peut devenir mutable :

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>La définition de la requête est remplacée par des méthodes spécifiques au fournisseur

[#18903 du problème de suivi](https://github.com/dotnet/efcore/issues/18903)

**Ancien comportement**

Les types d’entité ont été mappés à la définition de requêtes au niveau du noyau. Chaque fois que le type d’entité a été utilisé dans la racine de la requête du type d’entité a été remplacé par la requête de définition pour n’importe quel fournisseur.

**Nouveau comportement**

Les API pour la définition de la requête sont déconseillées. De nouvelles API spécifiques au fournisseur ont été introduites.

**Pourquoi**

Alors que la définition de requêtes a été implémentée en tant que requête de remplacement chaque fois que la racine de la requête est utilisée dans la requête, il y a quelques problèmes :

- Si la définition de la requête consiste à projeter un type d’entité à l’aide `new { ... }` de dans `Select` la méthode, vous devez ensuite identifier qu’en tant qu’entité a requis un travail supplémentaire et qu’elle est incohérente avec la façon dont EF Core traite les types nominaux dans la requête.
- Pour les fournisseurs relationnels `FromSql` , il est toujours nécessaire de passer la chaîne SQL sous forme d’expression LINQ.

La définition initiale des requêtes a été introduite en tant que vues côté client à utiliser avec In-Memory fournisseur pour les entités keymoins (similaires aux vues de base de données dans les bases de données relationnelles). Une telle définition facilite le test de l’application sur la base de données en mémoire. Par la suite, ils sont devenus largement applicables, ce qui était utile, mais incohérents et difficiles à comprendre. Nous avons donc décidé de simplifier le concept. Nous avons fait en sorte que la requête de définition basée sur LINQ soit exclusive pour In-Memory fournisseur et les traite différemment. Pour plus d’informations, [consultez ce problème](https://github.com/dotnet/efcore/issues/20023).

**Corrections**

Pour les fournisseurs relationnels, utilisez `ToSqlQuery` la méthode dans `OnModelCreating` et transmettez une chaîne SQL à utiliser pour le type d’entité.
Pour le fournisseur In-Memory, utilisez la `ToInMemoryQuery` méthode dans `OnModelCreating` et transmettez une requête LINQ à utiliser pour le type d’entité.

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>EF spécifique au fournisseur. Les méthodes Functions lèvent pour le fournisseur InMemory

[#20294 du problème de suivi](https://github.com/dotnet/efcore/issues/20294)

**Ancien comportement**

EF spécifique au fournisseur. Les méthodes Functions contenaient une implémentation pour l’exécution du client, ce qui leur permettait d’être exécutées sur le fournisseur d’InMemory. Par exemple, `EF.Functions.DateDiffDay` est une méthode propre à SQL Server, qui fonctionnait sur le fournisseur d’InMemory.

**Nouveau comportement**

Les méthodes spécifiques au fournisseur ont été mises à jour pour lever une exception dans leur corps de méthode afin de bloquer leur évaluation côté client.

**Pourquoi**

Les méthodes spécifiques au fournisseur sont mappées à une fonction de base de données. Le calcul effectué par la fonction de base de données mappée ne peut pas toujours être répliqué côté client dans LINQ. Cela peut provoquer la différence entre le résultat du serveur et l’exécution de la même méthode sur le client. Étant donné que ces méthodes sont utilisées dans LINQ pour traduire des fonctions de base de données spécifiques, elles n’ont pas besoin d’être évaluées côté client. Comme le fournisseur d’InMemory est une *base de données*différente, ces méthodes ne sont pas disponibles pour ce fournisseur. Si vous tentez de les exécuter pour le fournisseur d’InMemory ou tout autre fournisseur qui ne traduit pas ces méthodes, lève une exception.

**Corrections**

Étant donné qu’il n’existe aucun moyen de reproduire le comportement des fonctions de base de données avec précision, vous devez tester les requêtes qui les contiennent sur le même type de base de données qu’en production.

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a>IndexBuilder. HasName est désormais obsolète

[#21089 du problème de suivi](https://github.com/dotnet/efcore/issues/21089)

**Ancien comportement**

Auparavant, un seul index pouvait être défini sur un ensemble de propriétés donné. Le nom de la base de données d’un index a été configuré à l’aide de IndexBuilder. HasName.

**Nouveau comportement**

Plusieurs index sont désormais autorisés sur le même jeu ou les mêmes propriétés. Ces index sont à présent distingués par un nom dans le modèle. Par Convention, le nom du modèle est utilisé comme nom de la base de données ; Toutefois, il peut également être configuré indépendamment à l’aide de HasDatabaseName.

**Pourquoi**

À l’avenir, nous souhaitons activer à la fois les index croissants et les index décroissant avec différents classements sur le même ensemble de propriétés. Cette modification nous déplace une autre étape dans cette direction.

**Corrections**

Tout code qui appelle précédemment IndexBuilder. HasName doit être mis à jour pour appeler HasDatabaseName à la place.

Si votre projet comprend des migrations générées avant EF Core version 2.0.0, vous pouvez ignorer en toute sécurité l’avertissement dans ces fichiers et le supprimer en ajoutant `#pragma warning disable 612, 618` .

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a>Un pluarlizer est maintenant inclus pour la génération de modèles automatique de modèles rétroconçus

[#11160 du problème de suivi](https://github.com/dotnet/efcore/issues/11160)

**Ancien comportement**

Auparavant, vous deviez installer un package pluraliseur distinct pour plurieliser les noms de navigation des DbSet et des collections et les noms de tables singulières lorsque scaffoding un DbContext et des types d’entité en reconstituant l’ingénierie inverse d’un schéma de base de données.

**Nouveau comportement**

EF Core comprend maintenant un pluraliseur qui utilise la bibliothèque [Humanizer](https://github.com/Humanizr/Humanizer) . Il s’agit de la même bibliothèque que celle utilisée par Visual Studio pour recommander des noms de variables.

**Pourquoi**

L’utilisation de plusieurs formes de mots pour les propriétés de collection et les formes singulières pour les types et les propriétés de référence est idiomatique dans .NET.

**Corrections**

Pour désactiver pluraliseur, utilisez l' `--no-pluralize` option sur `dotnet ef dbcontext scaffold` ou le `-NoPluralize` commutateur activé `Scaffold-DbContext` .
