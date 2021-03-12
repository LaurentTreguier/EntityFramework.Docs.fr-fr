---
title: Modifications avec rupture dans EF Core 5,0-EF Core
description: Liste complète des modifications avec rupture introduites dans Entity Framework Core 5,0
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e52b77e8c19ab3005aee50e1cf4e170d3ee1f502
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023443"
---
# <a name="breaking-changes-in-ef-core-50"></a>Modifications avec rupture dans EF Core 5,0

Les modifications d’API et de comportement suivantes peuvent bloquer les mises à jour des applications existantes en EF Core 5.0.0.

## <a name="summary"></a>Résumé

| **Modification critique**                                                                                                                   | **Impact** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [EF Core 5,0 ne prend pas en charge .NET Framework](#netstandard21)                                                                         | Moyenne     |
| [Nouvel IProperty. GetColumnName () est désormais obsolète](#getcolumnname-obsolete)                                                                  | Moyenne     |
| [La précision et l’échelle sont requises pour les décimales](#decimals)                                                                            | Moyenne     |
| [Obligatoire sur la navigation du principal au dépendant a une sémantique différente](#required-dependent)                                 | Moyenne     |
| [La définition de la requête est remplacée par des méthodes spécifiques au fournisseur](#defining-query)                                                          | Moyenne     |
| [Les navigations de référence non NULL ne sont pas remplacées par les requêtes](#nonnullreferences)                                                   | Moyenne     |
| [ToView () est traité différemment par les migrations](#toview)                                                                              | Moyenne     |
| [ToTable (null) marque le type d’entité comme non mappé à une table](#totable)                                                              | Moyenne     |
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
| [Un pluraliseur est maintenant inclus pour la génération de modèles automatique de modèles rétroconçus](#pluralizer)                                                 | Faible        |
| [INavigationBase remplace INavigation dans certaines API pour prendre en charge les navigations ignorées](#inavigationbase)                                     | Faible        |
| [Certaines requêtes avec une collection corrélée qui utilisent également `Distinct` ou `GroupBy` ne sont plus prises en charge](#collection-distinct-groupby) | Faible        |
| [L’utilisation d’une collection de type interrogeable dans la projection n’est pas prise en charge](#queryable-projection)                                          | Faible        |

## <a name="medium-impact-changes"></a>Modifications à moyenne impact

<a name="netstandard21"></a>

### <a name="ef-core-50-does-not-support-net-framework"></a>EF Core 5,0 ne prend pas en charge .NET Framework

Suivi de problème no 15498

#### <a name="old-behavior"></a>Ancien comportement

EF Core 3,1 cible .NET Standard 2,0, qui est pris en charge par .NET Framework.

#### <a name="new-behavior"></a>Nouveau comportement

EF Core 5,0 cible .NET Standard 2,1, ce qui n’est pas pris en charge par .NET Framework. Cela signifie que EF Core 5,0 ne peut pas être utilisé avec les applications .NET Framework.

#### <a name="why"></a>Pourquoi

Cela fait partie du déplacement plus vaste entre les équipes .NET, visant à l’unification à un Framework cible .NET unique. Pour plus d’informations, consultez [l’avenir de .NET standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).

#### <a name="mitigations"></a>Corrections

.NET Framework applications peuvent continuer à utiliser EF Core 3,1, qui est une [version de support à long terme (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Les applications peuvent également être mises à jour pour utiliser .NET Core 3,1 ou .NET 5, qui prennent toutes deux en charge .NET Standard 2,1.

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a>Nouvel IProperty. GetColumnName () est désormais obsolète

[#2266 du problème de suivi](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a>Ancien comportement

`GetColumnName()` retourne le nom de la colonne à laquelle une propriété est mappée.

#### <a name="new-behavior"></a>Nouveau comportement

`GetColumnName()` retourne toujours le nom d’une colonne à laquelle une propriété est mappée, mais ce comportement est désormais ambigu, car EF Core 5 prend en charge TPT et le mappage simultané à une vue ou une fonction dans laquelle ces mappages peuvent utiliser des noms de colonne différents pour la même propriété.

#### <a name="why"></a>Pourquoi

Nous avons marqué cette méthode comme obsolète pour guider les utilisateurs vers une surcharge plus précise <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> .

#### <a name="mitigations"></a>Corrections

Utilisez le code suivant pour obtenir le nom de colonne d’une table spécifique :

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="decimals"></a>

### <a name="precision-and-scale-are-required-for-decimals"></a>La précision et l’échelle sont requises pour les décimales

[#19293 du problème de suivi](https://github.com/dotnet/efcore/issues/19293)

#### <a name="old-behavior"></a>Ancien comportement

EF Core n’a normalement pas défini la précision et l’échelle des <xref:Microsoft.Data.SqlClient.SqlParameter> objets. Cela signifie que la précision et l’échelle complètes ont été envoyées à SQL Server, auquel cas SQL Server arrondit en fonction de la précision et de l’échelle de la colonne de base de données.

#### <a name="new-behavior"></a>Nouveau comportement

EF Core définit désormais la précision et l’échelle des paramètres à l’aide des valeurs configurées pour les propriétés dans le modèle EF Core. Cela signifie que l’arrondi s’effectue désormais dans SqlClient. En conséquence, si la précision et l’échelle configurées ne correspondent pas à la précision et à l’échelle de la base de données, l’arrondi visible peut changer.

#### <a name="why"></a>Pourquoi

Les nouvelles fonctionnalités de SQL Server, y compris les Always Encrypted, requièrent que les facettes de paramètres soient entièrement spécifiées. En outre, SqlClient a apporté une modification à Round au lieu de tronquer les valeurs décimales, ce qui correspond au comportement de SQL Server. Cela permet à EF Core de définir ces facettes sans modifier le comportement des décimales correctement configurées.

#### <a name="mitigations"></a>Corrections

Mappez vos propriétés décimales à l’aide d’un nom de type qui comprend la précision et l’échelle. Par exemple :

```csharp
public class Blog
{
    public int Id { get; set; }

    [Column(TypeName = "decimal(16, 5")]
    public decimal Score { get; set; }
}
```

Ou utilisez `HasPrecision` dans les API de génération de modèle. Par exemple :

```csharp
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(e => e.Score).HasPrecision(16, 5);
    }
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>Obligatoire sur la navigation du principal au dépendant a une sémantique différente

[#17286 du problème de suivi](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a>Ancien comportement

Seules les navigations vers le principal peuvent être configurées selon les besoins. Par conséquent, l’utilisation `RequiredAttribute` de sur la navigation vers le dépendant (l’entité qui contient la clé étrangère) créerait à la place la clé étrangère sur le type d’entité de définition.

#### <a name="new-behavior"></a>Nouveau comportement

Avec la prise en charge supplémentaire des dépendants requis, il est désormais possible de marquer toute navigation de référence comme étant obligatoire, ce qui signifie que dans le cas indiqué au-dessus de la clé étrangère sera défini de l’autre côté de la relation et que les propriétés ne seront pas marquées comme obligatoires.

`IsRequired`L’appel de avant la spécification de la terminaison dépendante est maintenant ambigu :

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a>Pourquoi

Le nouveau comportement est nécessaire pour activer la prise en charge des dépendants requis ([voir #12100](https://github.com/dotnet/efcore/issues/12100)).

#### <a name="mitigations"></a>Corrections

Supprimez `RequiredAttribute` de la navigation vers le dépendant et placez-le à la place sur la navigation vers le principal ou configurez la relation dans `OnModelCreating` :

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>La définition de la requête est remplacée par des méthodes spécifiques au fournisseur

[#18903 du problème de suivi](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a>Ancien comportement

Les types d’entité ont été mappés à la définition de requêtes au niveau du noyau. Chaque fois que le type d’entité a été utilisé dans la racine de la requête du type d’entité a été remplacé par la requête de définition pour n’importe quel fournisseur.

#### <a name="new-behavior"></a>Nouveau comportement

Les API pour la définition de la requête sont déconseillées. De nouvelles API spécifiques au fournisseur ont été introduites.

#### <a name="why"></a>Pourquoi

Alors que la définition de requêtes a été implémentée en tant que requête de remplacement chaque fois que la racine de la requête est utilisée dans la requête, il y a quelques problèmes :

- Si la définition de la requête consiste à projeter un type d’entité à l’aide `new { ... }` de dans `Select` la méthode, vous devez ensuite identifier qu’en tant qu’entité a requis un travail supplémentaire et qu’elle est incohérente avec la façon dont EF Core traite les types nominaux dans la requête.
- Pour les fournisseurs relationnels `FromSql` , il est toujours nécessaire de passer la chaîne SQL sous forme d’expression LINQ.

La définition initiale des requêtes a été introduite en tant que vues côté client à utiliser avec In-Memory fournisseur pour les entités keymoins (similaires aux vues de base de données dans les bases de données relationnelles). Une telle définition facilite le test de l’application sur la base de données en mémoire. Par la suite, ils sont devenus largement applicables, ce qui était utile, mais incohérents et difficiles à comprendre. Nous avons donc décidé de simplifier le concept. Nous avons fait en sorte que la requête de définition basée sur LINQ soit exclusive pour In-Memory fournisseur et les traite différemment. Pour plus d’informations, [consultez ce problème](https://github.com/dotnet/efcore/issues/20023).

#### <a name="mitigations"></a>Corrections

Pour les fournisseurs relationnels, utilisez `ToSqlQuery` la méthode dans `OnModelCreating` et transmettez une chaîne SQL à utiliser pour le type d’entité.
Pour le fournisseur In-Memory, utilisez la `ToInMemoryQuery` méthode dans `OnModelCreating` et transmettez une requête LINQ à utiliser pour le type d’entité.

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a>Les navigations de référence non NULL ne sont pas remplacées par les requêtes

[#2693 du problème de suivi](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a>Ancien comportement

Dans EF Core 3,1, les navigations de référence initialisées de façon dynamique aux valeurs non null seraient parfois remplacées par des instances d’entité de la base de données, que les valeurs de clés correspondent ou non. Toutefois, dans d’autres cas, EF Core 3,1 ferait l’inverse et laissent la valeur non null existante.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de EF Core 5,0, les navigations de référence non NULL ne sont jamais remplacées par les instances retournées par une requête.

Notez que l’initialisation hâtif d’une _collection_ de navigation vers une collection vide est toujours prise en charge.

#### <a name="why"></a>Pourquoi

L’initialisation d’une propriété de navigation de référence en une instance d’entité « Empty » produit un État ambigu. Par exemple :

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

Normalement, une requête pour les blogs et les auteurs crée d’abord des `Blog` instances, puis définit les `Author` instances appropriées en fonction des données retournées par la base de données. Toutefois, dans ce cas, chaque `Blog.Author` propriété est déjà initialisée sur un vide `Author` . Sauf EF Core n’a aucun moyen de savoir que cette instance est « vide ». Par conséquent, le remplacement de cette instance peut éventuellement lever une exception en silence `Author` . Par conséquent, EF Core 5,0 ne remplace pas de manière cohérente une navigation déjà initialisée.

Ce nouveau comportement s’aligne également sur le comportement de EF6 dans la plupart des cas, bien que lors de l’investigation, nous ayons également trouvé certains cas d’incohérence dans EF6.

#### <a name="mitigations"></a>Corrections

Si ce problème se produit, le correctif est l’arrêt de l’initialisation des propriétés de navigation de référence.

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a>ToView () est traité différemment par les migrations

[#2725 du problème de suivi](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a>Ancien comportement

`ToView(string)`Le fait d’appeler les migrations ignore le type d’entité en plus de le mapper à une vue.

#### <a name="new-behavior"></a>Nouveau comportement

`ToView(string)`Marque maintenant que le type d’entité n’est pas mappé à une table, en plus de le mapper à une vue. Cela entraîne la première migration après une mise à niveau vers EF Core 5 pour essayer de supprimer la table par défaut pour ce type d’entité, car elle n’est plus ignorée.

#### <a name="why"></a>Pourquoi

EF Core permet désormais de mapper un type d’entité à une table et à une vue simultanément `ToView` . par conséquent, il n’est plus valide qu’il soit ignoré par les migrations.

#### <a name="mitigations"></a>Corrections

Utilisez le code suivant pour marquer la table mappée comme exclue des migrations :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a>ToTable (null) marque le type d’entité comme non mappé à une table

[#21172 du problème de suivi](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a>Ancien comportement

`ToTable(null)` rétablit la valeur par défaut du nom de la table.

#### <a name="new-behavior"></a>Nouveau comportement

`ToTable(null)` marque à présent que le type d’entité n’est mappé à aucune table.

#### <a name="why"></a>Pourquoi

EF Core permet désormais de mapper un type d’entité à la fois à une table et à une vue simultanément, `ToTable(null)` est utilisé pour indiquer qu’il n’est mappé à aucune table.

#### <a name="mitigations"></a>Corrections

Utilisez le code suivant pour rétablir le nom de table par défaut s’il n’est pas mappé à une vue ou un DbFunction :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

## <a name="low-impact-changes"></a>Modifications à faible impact

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite

[#14257 du problème de suivi](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a>Ancien comportement

HasGeometricDimension a été utilisé pour activer des dimensions supplémentaires (Z et M) sur les colonnes Geometry. Toutefois, il n’a jamais été affecté par la création de bases de données. Il n’était pas nécessaire de le spécifier pour interroger des valeurs avec des dimensions supplémentaires. Elle ne fonctionnait pas non plus correctement lors de l’insertion ou de la mise à jour de valeurs avec des dimensions supplémentaires ([voir #14257](https://github.com/dotnet/efcore/issues/14257)).

#### <a name="new-behavior"></a>Nouveau comportement

Pour permettre l’insertion et la mise à jour de valeurs géométriques avec des dimensions supplémentaires (Z et M), la dimension doit être spécifiée en tant que partie du nom du type de colonne. Cette API correspond plus étroitement au comportement sous-jacent de la fonction AddGeometryColumn de SpatiaLite.

#### <a name="why"></a>Pourquoi

L’utilisation de HasGeometricDimension après avoir spécifié la dimension dans le type de colonne est inutile et redondante, donc nous avons supprimé HasGeometricDimension entièrement.

#### <a name="mitigations"></a>Corrections

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

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos : la clé de partition est maintenant ajoutée à la clé primaire

[#15289 du problème de suivi](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>Ancien comportement

La propriété de clé de partition a été uniquement ajoutée à l’autre clé qui comprend `id` .

#### <a name="new-behavior"></a>Nouveau comportement

La propriété de clé de partition est maintenant également ajoutée à la clé primaire par Convention.

#### <a name="why"></a>Pourquoi

Cette modification rend le modèle mieux aligné avec Azure Cosmos DB sémantique et améliore les performances de `Find` et de certaines requêtes.

#### <a name="mitigations"></a>Corrections

Pour empêcher que la propriété de clé de partition soit ajoutée à la clé primaire, configurez-la dans `OnModelCreating` .

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos : `id` propriété renommée en `__id`

[#17751 du problème de suivi](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a>Ancien comportement

La propriété Shadow mappée à la `id` propriété JSON a également été nommée `id` .

#### <a name="new-behavior"></a>Nouveau comportement

La propriété Shadow créée par la Convention est maintenant nommée `__id` .

#### <a name="why"></a>Pourquoi

Cette modification rend moins probable la `id` propriété en conflit avec une propriété existante sur le type d’entité.

#### <a name="mitigations"></a>Corrections

Pour revenir au comportement 3. x, configurez la `id` propriété dans `OnModelCreating` .

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos : Byte [] est maintenant stocké sous la forme d’une chaîne base64 au lieu d’un tableau de nombres

[#17306 du problème de suivi](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a>Ancien comportement

Les propriétés de type Byte [] étaient stockées sous la forme d’un tableau de nombres.

#### <a name="new-behavior"></a>Nouveau comportement

Les propriétés de type Byte [] sont désormais stockées sous la forme d’une chaîne base64.

#### <a name="why"></a>Pourquoi

Cette représentation de Byte [] s’aligne mieux avec les attentes et est le comportement par défaut des principales bibliothèques de sérialisation JSON.

#### <a name="mitigations"></a>Corrections

Les données existantes stockées en tant que tableaux de nombres seront toujours interrogées correctement, mais il n’existe actuellement aucune méthode prise en charge pour modifier le comportement d’insertion. Si cette limitation bloque votre scénario, commentez [-le](https://github.com/dotnet/efcore/issues/17306)

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos : GetPropertyName et SetPropertyName ont été renommés

[#17874 du problème de suivi](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a>Ancien comportement

Auparavant, les méthodes d’extension étaient appelées `GetPropertyName` et `SetPropertyName`

#### <a name="new-behavior"></a>Nouveau comportement

L’ancienne API a été supprimée et de nouvelles méthodes ont été ajoutées : `GetJsonPropertyName` , `SetJsonPropertyName`

#### <a name="why"></a>Pourquoi

Cette modification supprime l’ambiguïté autour de la configuration de ces méthodes.

#### <a name="mitigations"></a>Corrections

Utilisez la nouvelle API.

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>Les générateurs de valeur sont appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé

[#15289 du problème de suivi](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>Ancien comportement

Les générateurs de valeur ont été appelés uniquement lorsque l’état de l’entité a été remplacé par ajouté.

#### <a name="new-behavior"></a>Nouveau comportement

Les générateurs de valeurs sont désormais appelés lorsque l’état de l’entité passe de détaché à inchangé, mis à jour ou supprimé et que la propriété contient les valeurs par défaut.

#### <a name="why"></a>Pourquoi

Cette modification était nécessaire pour améliorer l’expérience des propriétés qui ne sont pas rendues persistantes dans le magasin de données et dont la valeur doit être générée systématiquement sur le client.

#### <a name="mitigations"></a>Corrections

Pour empêcher l’appel du générateur de valeurs, assignez une valeur non définie par défaut à la propriété avant la modification de l’État.

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer utilise désormais IRelationalModel

[#20305 du problème de suivi](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a>Ancien comportement

`IMigrationsModelDiffer` L’API a été définie à l’aide de `IModel` .

#### <a name="new-behavior"></a>Nouveau comportement

`IMigrationsModelDiffer` L’API utilise désormais `IRelationalModel` . Toutefois, l’instantané de modèle contient toujours uniquement `IModel` , car ce code fait partie de l’application et Entity Framework ne peut pas le modifier sans apporter de modification plus importante.

#### <a name="why"></a>Pourquoi

`IRelationalModel` représentation qui vient d’être ajoutée au schéma de la base de données. Son utilisation pour trouver les différences est plus rapide et plus précise.

#### <a name="mitigations"></a>Corrections

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

[#21154 du problème de suivi](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a>Ancien comportement

Il était possible de modifier la valeur du discriminateur avant d’appeler `SaveChanges`

#### <a name="new-behavior"></a>Nouveau comportement

Une exception sera levée dans le cas ci-dessus.

#### <a name="why"></a>Pourquoi

EF ne s’attend pas à ce que le type d’entité change pendant qu’il est toujours suivi. la modification de la valeur de discriminateur laisse le contexte dans un état incohérent, ce qui peut entraîner un comportement inattendu.

#### <a name="mitigations"></a>Corrections

Si la modification de la valeur de discriminateur est nécessaire et que le contexte est supprimé immédiatement après l’appel `SaveChanges` de, le discriminateur peut devenir mutable :

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>EF spécifique au fournisseur. Les méthodes Functions lèvent pour le fournisseur InMemory

[#20294 du problème de suivi](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a>Ancien comportement

EF spécifique au fournisseur. Les méthodes Functions contenaient une implémentation pour l’exécution du client, ce qui leur permettait d’être exécutées sur le fournisseur d’InMemory. Par exemple, `EF.Functions.DateDiffDay` est une méthode propre à SQL Server, qui fonctionnait sur le fournisseur d’InMemory.

#### <a name="new-behavior"></a>Nouveau comportement

Les méthodes spécifiques au fournisseur ont été mises à jour pour lever une exception dans leur corps de méthode afin de bloquer leur évaluation côté client.

#### <a name="why"></a>Pourquoi

Les méthodes spécifiques au fournisseur sont mappées à une fonction de base de données. Le calcul effectué par la fonction de base de données mappée ne peut pas toujours être répliqué côté client dans LINQ. Cela peut provoquer la différence entre le résultat du serveur et l’exécution de la même méthode sur le client. Étant donné que ces méthodes sont utilisées dans LINQ pour traduire des fonctions de base de données spécifiques, elles n’ont pas besoin d’être évaluées côté client. Comme le fournisseur d’InMemory est une *base de données* différente, ces méthodes ne sont pas disponibles pour ce fournisseur. Si vous tentez de les exécuter pour le fournisseur d’InMemory ou tout autre fournisseur qui ne traduit pas ces méthodes, lève une exception.

#### <a name="mitigations"></a>Corrections

Étant donné qu’il n’existe aucun moyen de reproduire le comportement des fonctions de base de données avec précision, vous devez tester les requêtes qui les contiennent sur le même type de base de données qu’en production.

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a>IndexBuilder. HasName est désormais obsolète

[#21089 du problème de suivi](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a>Ancien comportement

Auparavant, un seul index pouvait être défini sur un ensemble de propriétés donné. Le nom de la base de données d’un index a été configuré à l’aide de IndexBuilder. HasName.

#### <a name="new-behavior"></a>Nouveau comportement

Plusieurs index sont désormais autorisés sur le même jeu ou les mêmes propriétés. Ces index sont à présent distingués par un nom dans le modèle. Par Convention, le nom du modèle est utilisé comme nom de la base de données ; Toutefois, il peut également être configuré indépendamment à l’aide de HasDatabaseName.

#### <a name="why"></a>Pourquoi

À l’avenir, nous souhaitons activer à la fois les index croissants et les index décroissant avec différents classements sur le même ensemble de propriétés. Cette modification nous déplace une autre étape dans cette direction.

#### <a name="mitigations"></a>Corrections

Tout code qui appelle précédemment IndexBuilder. HasName doit être mis à jour pour appeler HasDatabaseName à la place.

Si votre projet comprend des migrations générées avant EF Core version 2.0.0, vous pouvez ignorer en toute sécurité l’avertissement dans ces fichiers et le supprimer en ajoutant `#pragma warning disable 612, 618` .

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a>Un pluraliseur est maintenant inclus pour la génération de modèles automatique de modèles rétroconçus

[#11160 du problème de suivi](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a>Ancien comportement

Auparavant, vous deviez installer un package pluraliseur distinct afin de plurieler les noms de navigation des DbSet et des collections et de distinguer les noms de tables lors de la génération de modèles automatique de DbContext et de types d’entités en rétroconception d’un schéma de base de données.

#### <a name="new-behavior"></a>Nouveau comportement

EF Core comprend maintenant un pluraliseur qui utilise la bibliothèque [Humanizer](https://github.com/Humanizr/Humanizer) . Il s’agit de la même bibliothèque que celle utilisée par Visual Studio pour recommander des noms de variables.

#### <a name="why"></a>Pourquoi

L’utilisation de plusieurs formes de mots pour les propriétés de collection et les formes singulières pour les types et les propriétés de référence est idiomatique dans .NET.

#### <a name="mitigations"></a>Corrections

Pour désactiver pluraliseur, utilisez l' `--no-pluralize` option sur `dotnet ef dbcontext scaffold` ou le `-NoPluralize` commutateur activé `Scaffold-DbContext` .

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a>INavigationBase remplace INavigation dans certaines API pour prendre en charge les navigations ignorées

[#2568 du problème de suivi](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a>Ancien comportement

EF Core antérieures à 5,0 ne prenait en charge qu’une seule forme de propriété de navigation, représentée par l' `INavigation` interface.

#### <a name="new-behavior"></a>Nouveau comportement

EF Core 5,0 introduit des relations plusieurs-à-plusieurs qui utilisent « ignorer les navigations ». Celles-ci sont représentées par l' `ISkipNavigation` interface, et la plupart des fonctionnalités de `INavigation` ont été poussées vers une interface de base commune : `INavigationBase` .

#### <a name="why"></a>Pourquoi

La plupart des fonctionnalités entre les navigations normale et ignorée sont identiques. Toutefois, les navigations ignorées ont une relation différente avec les clés étrangères par rapport aux navigations normales, puisque les clés étrangères impliquées ne sont pas directement sur l’une ou l’autre des extrémités de la relation, mais plutôt dans l’entité de jointure.

#### <a name="mitigations"></a>Corrections

Dans de nombreux cas, les applications peuvent basculer vers à l’aide de la nouvelle interface de base sans aucune autre modification. Toutefois, dans les cas où la navigation est utilisée pour accéder aux propriétés de clé étrangère, le code d’application doit être restreint aux navigations normales, ou mis à jour pour effectuer la tâche appropriée à la fois pour les navigations normales et les navigations ignorées.

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a>Certaines requêtes avec une collection corrélée qui utilisent également `Distinct` ou `GroupBy` ne sont plus prises en charge

[#15873 du problème de suivi](https://github.com/dotnet/efcore/issues/15873)

**Ancien comportement**

Auparavant, les requêtes impliquant des collections corrélées suivies de `GroupBy` , ainsi que certaines requêtes utilisant `Distinct` nous autorisant l’exécution.

Exemple GroupBy :

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

`Distinct` exemple : `Distinct` requêtes spécifiques où la projection de la collection interne ne contient pas la clé primaire :

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

Ces requêtes peuvent retourner des résultats incorrects si la collection interne contenait des doublons, mais fonctionnait correctement si tous les éléments de la collection interne étaient uniques.

**Nouveau comportement**

Ces requêtes ne sont plus prises en charge. Une exception est levée et indique que nous n’avons pas assez d’informations pour générer correctement les résultats.

**Pourquoi**

Pour les scénarios de collecte corrélée, nous avons besoin de connaître la clé primaire de l’entité pour affecter des entités de regroupement au parent approprié. Lorsque la collection interne n’utilise pas `GroupBy` ou `Distinct` , la clé primaire manquante peut simplement être ajoutée à la projection. Toutefois, dans le cas de `GroupBy` , `Distinct` elle ne peut pas être effectuée car elle modifie le résultat d’une `GroupBy` `Distinct` opération or.

**Corrections**

Réécrivez la requête de façon à ne pas utiliser les `GroupBy` `Distinct` opérations ou sur la collection interne, et effectuez ces opérations sur le client à la place.

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

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a>L’utilisation d’une collection de type interrogeable dans la projection n’est pas prise en charge

[#16314 du problème de suivi](https://github.com/dotnet/efcore/issues/16314)

**Ancien comportement**

Auparavant, il était possible d’utiliser la collection d’un type pouvant être interrogé à l’intérieur de la projection dans certains cas, par exemple en tant qu’argument d’un `List<T>` constructeur :

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

**Nouveau comportement**

Ces requêtes ne sont plus prises en charge. Une exception est levée, indiquant que nous ne pouvons pas créer un objet de type interrogeable et suggérer la manière dont cela peut être résolu.

**Pourquoi**

Nous ne pouvons pas matérialiser un objet d’un type pouvant faire l’objet d’une requête. il est donc créé automatiquement à l’aide du `List<T>` type. Cela entraînerait souvent une exception en raison d’une incompatibilité de type, ce qui n’était pas très clair et pourrait être surprenant pour certains utilisateurs. Nous avons décidé de reconnaître le modèle et de lever une exception plus significative.

**Corrections**

Ajouter `ToList()` un appel après l’objet interrogeable dans la projection :

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
