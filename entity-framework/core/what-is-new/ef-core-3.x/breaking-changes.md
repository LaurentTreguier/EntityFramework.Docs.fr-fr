---
title: Dernières modifications apportées à EF Core 3. x-EF Core
description: Liste complète des modifications avec rupture introduites dans Entity Framework Core 3. x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: bfcfb7257091d1b6889f7c0af00ddab10e0e12e3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429310"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a>Modifications avec rupture incluses dans EF Core 3. x

Les modifications d’API et de comportement suivantes peuvent bloquer les applications existantes lors de leur mise à niveau vers 3. x.
Les changements qui, selon nous, auront une incidence uniquement sur les fournisseurs de base de données sont documentés dans [Changements ayant un impact sur les fournisseurs](xref:core/providers/provider-log).

## <a name="summary"></a>Résumé

| **Modification critique**                                                                                               | **Impact** |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [Les requêtes LINQ ne sont plus évaluées sur le client](#linq-queries-are-no-longer-evaluated-on-the-client)         | Importante       |
| [L’outil en ligne de commande EF Core, dotnet ef, ne fait plus partie du SDK .NET Core](#dotnet-ef) | Importante      |
| [DetectChanges honore les valeurs de clés générées par le magasin](#dc) | Importante      |
| [FromSql, ExecuteSql et ExecuteSqlAsync ont été renommés](#fromsql) | Importante      |
| [Les types de requêtes sont regroupés avec les types d’entités](#qt) | Importante      |
| [Entity Framework Core ne fait plus partie du framework partagé ASP.NET Core](#no-longer) | Moyenne      |
| [Les suppressions en cascade se produisent désormais immédiatement par défaut](#cascade) | Moyenne      |
| [Le chargement hâtif des entités associées se produit désormais dans une seule requête](#eager-loading-single-query) | Moyenne      |
| [La sémantique de DeleteBehavior.Restrict est désormais plus propre](#deletebehavior) | Moyenne      |
| [L’API de configuration pour les relations de type détenu a changé](#config) | Moyenne      |
| [Chaque propriété utilise la génération indépendante de clé de type entier en mémoire](#each) | Moyenne      |
| [Les requêtes sans suivi ne procèdent plus à la résolution de l’identité](#notrackingresolution) | Moyenne      |
| [Changements apportés à l’API de métadonnées](#metadata-api-changes) | Moyenne      |
| [Modifications de l’API de métadonnées spécifiques au fournisseur](#provider) | Moyenne      |
| [UseRowNumberForPaging a été supprimé](#urn) | Moyenne      |
| [La méthode FromSql quand elle est utilisée avec une procédure stockée ne peut pas être composée](#fromsqlsproc) | Moyenne      |
| [Les méthodes FromSql peuvent uniquement être spécifiées sur les racines de requête](#fromsql) | Faible      |
| [Les valeurs de clés temporaires ne sont plus définies sur les instances d’entités](#tkv) | Faible      |
| [Les entités dépendantes qui partagent la table avec le principal sont maintenant facultatives](#de) | Faible      |
| [Toutes les entités qui partagent une table avec une colonne de jeton de concurrence doivent la mapper à une propriété](#aes) | Faible      |
| [Les entités détenues ne peuvent pas être interrogées sans le propriétaire à l’aide d’une requête de suivi](#owned-query) | Faible      |
| [Les propriétés héritées de types non mappés sont maintenant mappées à une seule colonne pour tous les types dérivés](#ip) | Faible      |
| [La convention de propriété de clé étrangère ne correspond plus au nom de la propriété principale](#fkp) | Faible      |
| [La connexion de base de données est maintenant fermée si elle n’est plus utilisée avant la fin de TransactionScope](#dbc) | Faible      |
| [Les champs de stockage sont utilisés par défaut](#backing-fields-are-used-by-default) | Faible      |
| [Erreur si plusieurs champs de stockage compatibles sont détectés](#throw-if-multiple-compatible-backing-fields-are-found) | Faible      |
| [Les noms de propriété de type « champ uniquement » doivent correspondre au nom du champ](#field-only-property-names-should-match-the-field-name) | Faible      |
| [AddDbContext/AddDbContextPool n’appelle plus AddLogging et AddMemoryCache](#adddbc) | Faible      |
| [AddEntityFramework * ajoute IMemoryCache avec une limite de taille](#addentityframework-adds-imemorycache-with-a-size-limit) | Faible      |
| [DbContext.Entry effectue maintenant un DetectChanges local](#dbe) | Faible      |
| [Les clés de tableaux d’octets et de chaînes ne sont pas générés par client par défaut](#string-and-byte-array-keys-are-not-client-generated-by-default) | Faible      |
| [ILoggerFactory est désormais un service délimité](#ilf) | Faible      |
| [Les proxys à chargement différé ne partent plus du principe que les propriétés de navigation sont entièrement chargées](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | Faible      |
| [La création excessive de fournisseurs de services internes est maintenant une erreur par défaut](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | Faible      |
| [Nouveau comportement de HasOne/HasMany appelé avec une chaîne unique](#nbh) | Faible      |
| [Le type de retour Task pour plusieurs méthodes asynchrones a été remplacé par ValueTask](#rtnt) | Faible      |
| [L’annotation Relational:TypeMapping est désormais simplement TypeMapping](#rtt) | Faible      |
| [ToTable sur un type dérivé lève une exception](#totable-on-a-derived-type-throws-an-exception) | Faible      |
| [EF Core n’envoie plus de pragma pour l’application de clé étrangère SQLite](#pragma) | Faible      |
| [Microsoft.EntityFrameworkCore.Sqlite dépend désormais de SQLitePCLRaw.bundle_e_sqlite3](#sqlite3) | Faible      |
| [Les valeurs GUID sont maintenant stockées au format TEXT sur SQLite](#guid) | Faible      |
| [Les valeurs char sont maintenant stockées au format TEXT sur SQLite](#char) | Faible      |
| [Les ID de migration sont maintenant générés à l’aide du calendrier de la culture invariante](#migid) | Faible      |
| [Les infos/métadonnées d’extension ont été supprimées de IDbContextOptionsExtension](#xinfo) | Faible      |
| [LogQueryPossibleExceptionWithAggregateOperator a été renommé](#lqpe) | Faible      |
| [Clarifier les noms de contrainte de clé étrangère dans l’API](#clarify) | Faible      |
| [IRelationalDatabaseCreator.HasTables/HasTablesAsync ont été rendues publiques](#irdc2) | Faible      |
| [Microsoft.EntityFrameworkCore.Design est désormais un package DevelopmentDependency](#dip) | Faible      |
| [SQLitePCL.raw mis à jour vers la version 2.0.0](#SQLitePCL) | Faible      |
| [NetTopologySuite mis à jour vers la version 2.0.0](#NetTopologySuite) | Faible      |
| [Microsoft. Data. SqlClient est utilisé à la place de System. Data. SqlClient](#SqlClient) | Faible      |
| [Plusieurs relations autoréférencées ambiguës doivent être configurées](#mersa) | Faible      |
| [DbFunction. Schema étant null ou une chaîne vide, il est configuré pour être dans le schéma par défaut du modèle](#udf-empty-string) | Faible      |
| [~~EF Core 3,0 cibles .NET Standard 2,1 au lieu de .NET Standard 2,0~~ Rétablie](#netstandard21) | |
| [~~L’exécution de requêtes est enregistrée au niveau du débogage ~~Rétabli](#qe) | |

## <a name="high-impact-changes"></a>Modifications à fort impact

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>Les requêtes LINQ ne sont plus évaluées sur le client

#14935 du problème de [suivi](https://github.com/dotnet/efcore/issues/14935) 
 [Consultez également le #12795 du problème](https://github.com/dotnet/efcore/issues/12795)

#### <a name="old-behavior"></a>Ancien comportement

Avant la version 3.0, quand EF Core ne pouvait pas convertir une expression faisant partie d’une requête en SQL ou en paramètre, elle évaluait automatiquement l’expression sur le client.
Par défaut, l’évaluation sur le client d’expressions potentiellement coûteuses déclenchait uniquement un avertissement.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de la version 3.0, EF Core autorise uniquement l’évaluation sur le client des expressions qui figurent dans la projection de niveau supérieur (le dernier appel `Select()` dans la requête).
Quand des expressions d’une autre partie de la requête ne peuvent pas être converties en SQL ou en paramètre, une exception est levée.

#### <a name="why"></a>Pourquoi

L’évaluation automatique des requêtes sur le client permet à de nombreuses requêtes d’être exécutées même si certaines de leurs parties importantes ne peuvent pas être traduites.
Ce comportement peut entraîner un comportement inattendu et potentiellement dangereux qui peut devenir évident uniquement en production.
Par exemple, une condition dans un appel `Where()` qui ne peut pas être traduite peut provoquer le transfert de toutes les lignes de la table hors du serveur de base de données, et l’application du filtre sur le client.
Cette situation peut facilement passer inaperçue si la table contient uniquement quelques lignes lors du développement, mais poser davantage de problèmes quand l’application passe en production, où la table peut contenir plusieurs millions de lignes.
Les avertissements relatifs à l’évaluation sur le client étaient également trop faciles à ignorer pendant le développement.

De plus, l’évaluation automatique sur le client peut entraîner des problèmes selon lesquels l’amélioration de la traduction des requêtes pour des expressions spécifiques provoque un changement cassant involontaire entre les versions.

#### <a name="mitigations"></a>Corrections

Si une requête ne peut pas être entièrement traduite, réécrivez-la sous une forme qui peut être traduite ou utilisez `AsEnumerable()`, `ToList()` ou autre méthode similaire pour réimporter explicitement les données sur le client, où elles peuvent ensuite être traitées à l’aide de LINQ-to-Objects.

<a name="no-longer"></a>

## <a name="medium-impact-changes"></a>Modifications à moyenne impact

### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core ne fait plus partie du framework partagé ASP.NET Core

[Annonces de suivi du problème n° 325](https://github.com/aspnet/Announcements/issues/325)

#### <a name="old-behavior"></a>Ancien comportement

Avant ASP.NET Core 3.0, quand vous ajoutiez une référence de package à `Microsoft.AspNetCore.App` ou `Microsoft.AspNetCore.All`, elle incluait EF Core et certains des fournisseurs de données EF Core tels que le fournisseur SQL Server.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de la version 3.0, le framework partagé ASP.NET Core n’inclut ni EF Core, ni aucun fournisseur de données EF Core.

#### <a name="why"></a>Pourquoi

Avant ce changement, l’obtention d’EF Core nécessitait différentes étapes selon que l’application ciblait ASP.NET Core et SQL Server ou non.
De plus, la mise à niveau d’ASP.NET Core forçait la mise à niveau d’EF Core et du fournisseur SQL Server, ce qui n’est pas toujours souhaitable.

Avec ce changement, l’expérience d’obtention d’EF Core est la même pour tous les fournisseurs, implémentations .NET prises en charge et types d’applications.
Les développeurs peuvent désormais contrôler exactement quand EF Core et les fournisseurs de données EF Core sont mis à niveau.

#### <a name="mitigations"></a>Corrections

Pour utiliser EF Core dans une application ASP.NET Core 3.0 ou toute autre application prise en charge, ajoutez explicitement une référence de package au fournisseur de base de données EF Core que votre application utilisera.

<a name="dotnet-ef"></a>

### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a>L’outil en ligne de commande EF Core, dotnet ef, ne fait plus partie du SDK .NET Core

[Suivi du problème n° 14016](https://github.com/dotnet/efcore/issues/14016)

#### <a name="old-behavior"></a>Ancien comportement

Avant la version 3.0, l’outil `dotnet ef` était inclus dans le SDK .NET Core et prêt à l’emploi depuis la ligne de commande d’un projet sans nécessiter d’étapes supplémentaires.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de la version 3.0, le SDK .NET n’inclut pas l’outil `dotnet ef`, donc vous pouvez explicitement l’installer pour pouvoir l’utiliser comme outil local ou global.

#### <a name="why"></a>Pourquoi

Ce changement nous permet de distribuer et mettre à jour `dotnet ef` sous forme d’outil CLI .NET normal sur NuGet, ce qui est cohérent avec le fait qu’EF Core 3.0 est également toujours distribué sous forme de package NuGet.

#### <a name="mitigations"></a>Corrections

Pour pouvoir gérer des migrations ou générer un `DbContext`, installez `dotnet-ef` comme outil général :

```dotnetcli
dotnet tool install --global dotnet-ef
```

Vous pouvez également obtenir un outil local quand vous restaurez les dépendances d’un projet qui le déclare en tant que dépendance d’outil à l’aide d’un [fichier manifeste d’outil](/dotnet/core/tools/global-tools#install-a-local-tool).

## <a name="low-impact-changes"></a>Modifications à faible impact

<a name="fromsql"></a>

### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a>FromSql, ExecuteSql et ExecuteSqlAsync ont été renommés

[Suivi du problème n<c0>o </c0>10996](https://github.com/dotnet/efcore/issues/10996)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, ces noms de méthode étaient surchargés pour être utilisés avec une chaîne normale ou une chaîne devant être interpolée dans SQL et dans des paramètres.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, utilisez `FromSqlRaw`, `ExecuteSqlRaw` et `ExecuteSqlRawAsync` pour créer une requête paramétrable, où les paramètres sont passés séparément à partir de la chaîne de requête.
Par exemple :

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

Utilisez `FromSqlInterpolated`, `ExecuteSqlInterpolated` et `ExecuteSqlInterpolatedAsync` pour créer une requête paramétrable, où les paramètres sont passés dans le cadre d’une chaîne de requête interpolée.
Par exemple :

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

Notez que les deux requêtes ci-dessus produisent le même SQL paramétrable avec les mêmes paramètres SQL.

#### <a name="why"></a>Pourquoi

Les surcharges de méthode comme celle-ci rendent très facile un appel accidentel à la méthode de chaîne brute quand l’intention est d’appeler la méthode de chaîne interpolée, et inversement.
De ce fait, les requêtes ne sont plus paramétrables alors qu’elles devraient l’être.

#### <a name="mitigations"></a>Corrections

Utilisez plutôt les nouveaux noms de méthode.

<a name="fromsqlsproc"></a>

### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a>La méthode FromSql quand elle est utilisée avec une procédure stockée ne peut pas être composée

[#15392 du problème de suivi](https://github.com/dotnet/efcore/issues/15392)

#### <a name="old-behavior"></a>Ancien comportement

Avant le EF Core 3,0, la méthode FromSql a tenté de détecter si le SQL passé peut être composé. Il a fait l’évaluation du client lorsque le SQL n’était pas composable comme une procédure stockée. La requête suivante a fonctionné en exécutant la procédure stockée sur le serveur et en procédant à l’opération FirstOrDefault côté client.

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

#### <a name="new-behavior"></a>Nouveau comportement

À compter de EF Core 3,0, EF Core n’essaiera pas d’analyser le SQL. Par conséquent, si vous effectuez une composition après FromSqlRaw/FromSqlInterpolated, EF Core compose le SQL en provoquant une sous-requête. Par conséquent, si vous utilisez une procédure stockée avec la composition, vous obtiendrez une exception pour la syntaxe SQL non valide.

#### <a name="why"></a>Pourquoi

EF Core 3,0 ne prend pas en charge l’évaluation automatique du client, car il s’agit d’une erreur, comme expliqué [ici](#linq-queries-are-no-longer-evaluated-on-the-client).

#### <a name="mitigations"></a>Corrections

Si vous utilisez une procédure stockée dans FromSqlRaw/FromSqlInterpolated, vous savez qu’elle ne peut pas être composée. vous pouvez donc ajouter __AsEnumerable/AsAsyncEnumerable__ juste après l’appel de la méthode FromSql pour éviter toute composition côté serveur.

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a>Les méthodes FromSql peuvent uniquement être spécifiées sur les racines de requête

Suivi de problème no 15704

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, la méthode `FromSql` pouvant être spécifiée n’importe où dans la requête.

#### <a name="new-behavior"></a>Nouveau comportement

À partir d’EF Core 3.0, les nouvelles méthodes `FromSqlRaw` et `FromSqlInterpolated` (qui remplacent `FromSql`) peuvent être spécifiées uniquement sur les racines de requête, par exemple, directement sur le `DbSet<>`. Une erreur de compilation survient si vous tentez de les spécifier à un autre emplacement.

#### <a name="why"></a>Pourquoi

La spécification de `FromSql` n’importe où autre que sur un `DbSet` n’avait aucune signification ni valeur ajoutée et pouvait entraîner une ambiguïté dans certains scénarios.

#### <a name="mitigations"></a>Corrections

Les appels `FromSql` doivent être déplacés pour être directement sur le `DbSet` auquel ils s’appliquent.

<a name="notrackingresolution"></a>

### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a>Les requêtes sans suivi ne procèdent plus à la résolution de l’identité

Suivi de problème no 13518

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, la même instance d’entité était utilisée pour chaque occurrence d’une entité avec un type et un ID donnés. Cela correspond au comportement des requêtes de suivi. Par exemple, cette requête :

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```

retournait la même instance `Category` pour chaque `Product` associé à la catégorie donnée.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de EF Core 3.0, différentes instances d’entité sont créées lorsqu’une entité avec un type et un ID donnés est rencontrée à différents emplacements dans le graphe retourné. Par exemple, la requête ci-dessus retourne à présent une nouvelle instance `Category` pour chaque `Product` même si deux produits sont associés à la même catégorie.

#### <a name="why"></a>Pourquoi

La résolution de l’identité (autrement dit, le fait de déterminer qu’une entité a les mêmes type et ID qu’une entité précédemment rencontrée) améliore les performances et la surcharge de la mémoire. Cela agit généralement à l’opposé de la raison pour laquelle aucune requête de suivi n’est utilisée en premier lieu. En outre, même si la résolution de l’identité peut parfois être utile, elle n’est pas nécessaire si les entités doivent être sérialisées et envoyées à un client, ce qui est courant pour les requêtes sans suivi.

#### <a name="mitigations"></a>Corrections

Utilisez une requête de suivi si la résolution de l’identité est requise.

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>Les valeurs de clés temporaires ne sont plus définies sur les instances d’entités

[Suivi du problème n<c0>o </c0>12378](https://github.com/dotnet/efcore/issues/12378)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, des valeurs temporaires étaient affectées à toutes les propriétés de clé qui auraient plus tard une valeur réelle générée par la base de données.
Généralement, ces valeurs temporaires étaient de grands nombres négatifs.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de la version 3.0, EF Core stocke la valeur de clé temporaire dans le cadre des informations de suivi de l’entité, et laisse la propriété de clé proprement dite inchangée.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin d’empêcher que les valeurs de clés temporaires ne deviennent à tort permanentes quand une entité qui a été suivie par une instance `DbContext` est déplacée vers une autre instance `DbContext`.

#### <a name="mitigations"></a>Corrections

Les applications qui affectent des valeurs de clés primaires sur des clés étrangères afin de former des associations entre des entités peuvent dépendre de l’ancien comportement si les clés primaires sont générées par le magasin et appartiennent à des entités à l’état `Added`.
Vous pouvez éviter cela en :

* N’utilisant pas de clés générées par le magasin.
* Définissant des propriétés de navigation afin d’établir des relations au lieu de définir des valeurs de clés étrangères.
* Obtenant les valeurs de clés temporaires réelles à partir des informations de suivi de l’entité.
Par exemple, `context.Entry(blog).Property(e => e.Id).CurrentValue` retournera la valeur temporaire même si la propriété `blog.Id` elle-même n’a pas été définie.

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a>DetectChanges honore les valeurs de clés générées par le magasin

[Suivi du problème n<c0>o </c0>14616](https://github.com/dotnet/efcore/issues/14616)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, une entité non suivie détectée par `DetectChanges` était suivie à l’état `Added` et insérée en tant que nouvelle ligne quand `SaveChanges` était appelée.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, si une entité utilise des valeurs de clés générées et qu’une valeur de clé est définie, alors l’entité est suivie à l’état `Modified`.
Cela signifie qu’une ligne pour l’entité est supposée exister et qu’elle sera mise à jour lors de l’appel à `SaveChanges`.
Si la valeur de clé n’est pas définie, ou si le type d’entité n’utilise pas de clés générées, alors la nouvelle entité sera quand même suivie comme `Added`, comme dans les versions précédentes.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin de simplifier l’utilisation des graphes d’entités déconnectées lors de l’utilisation de clés générées par le magasin.

#### <a name="mitigations"></a>Corrections

Ce changement peut casser une application si un type d’entité est configuré pour utiliser des clés générés, mais que les valeurs de clés sont définies explicitement pour les nouvelles instances.
La solution consiste à configurer explicitement les propriétés de clés de façon à ne pas utiliser des valeurs générées.
Par exemple, avec l’API Fluent :

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

Ou avec des annotations de données :

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

<a name="cascade"></a>

### <a name="cascade-deletions-now-happen-immediately-by-default"></a>Les suppressions en cascade se produisent désormais immédiatement par défaut

[Suivi du problème n<c0>o </c0>10114](https://github.com/dotnet/efcore/issues/10114)

#### <a name="old-behavior"></a>Ancien comportement

Avant la version 3.0, les actions en cascade appliquées par EF Core (suppression d’entités dépendantes quand un principal requis est supprimé ou quand la relation à un principal requis est interrompue) ne se produisaient qu’une fois que SaveChanges était appelée.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de la version 3.0, EF Core applique les actions en cascade dès que la condition de déclenchement est détectée.
Par exemple, si vous appelez `context.Remove()` pour supprimer une entité principale, toutes les dépendances requises suivies associées seront également définies immédiatement sur `Deleted`.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin d’améliorer l’expérience de liaison de données et les scénarios d’audit où il est important de comprendre quelles entités seront supprimées _avant l’appel à_ `SaveChanges`.

#### <a name="mitigations"></a>Corrections

Vous pouvez restaurer le comportement précédent par le biais des paramètres sur `context.ChangeTracker`.
Par exemple :

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

<a name="eager-loading-single-query"></a>

### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a>Le chargement hâtif des entités associées se produit désormais dans une seule requête

[#18022 du problème de suivi](https://github.com/dotnet/efcore/issues/18022)

#### <a name="old-behavior"></a>Ancien comportement

Avant le 3,0, le chargement des navigations de collections `Include` à l’aide d’opérateurs entraînait la génération de plusieurs requêtes sur une base de données relationnelle, une pour chaque type d’entité associée.

#### <a name="new-behavior"></a>Nouveau comportement

À partir de 3,0, EF Core génère une requête unique avec des JOINTUREs sur des bases de données relationnelles.

#### <a name="why"></a>Pourquoi

L’émission de plusieurs requêtes pour implémenter une seule requête LINQ a entraîné de nombreux problèmes, notamment des performances négatives lorsque plusieurs allers-retours de base de données étaient nécessaires et des problèmes de cohérence des données à mesure que chaque requête pouvait observer un état différent de la base de données.

#### <a name="mitigations"></a>Corrections

Techniquement, ce n’est pas une modification avec rupture, mais cela peut avoir un impact considérable sur les performances de l’application lorsqu’une seule requête contient un grand nombre d' `Include` opérateurs dans les navigations de collection. Pour plus d’informations et pour réécrire des requêtes de manière plus efficace, [consultez ce commentaire](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) .

**

<a name="deletebehavior"></a>

### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a>La sémantique de DeleteBehavior.Restrict est désormais plus propre

[Suivi de problème no 12661](https://github.com/dotnet/efcore/issues/12661)

#### <a name="old-behavior"></a>Ancien comportement

Avant la version 3.0, `DeleteBehavior.Restrict` créait les clés étrangères dans la base de données avec la sémantique `Restrict`, et il modifiait les corrections internes d’une manière non évidente.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de la version 3.0, `DeleteBehavior.Restrict` garantit la création des clés étrangères avec la sémantique `Restrict` (autrement dit, sans levée de cascades lors d’une violation de contrainte), sans impact sur les corrections internes EF.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté pour améliorer l’expérience et permettre une utilisation intuitive de `DeleteBehavior`, sans effets secondaires inattendus.

#### <a name="mitigations"></a>Corrections

Vous pouvez restaurer le comportement précédent par le biais de `DeleteBehavior.ClientNoAction`.

<a name="qt"></a>

### <a name="query-types-are-consolidated-with-entity-types"></a>Les types de requêtes sont regroupés avec les types d’entités

[Suivi du problème n<c0>o </c0>14194](https://github.com/dotnet/efcore/issues/14194)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, les [types de requêtes](xref:core/modeling/keyless-entity-types) étaient un moyen d’interroger des données qui ne définissait pas de clé primaire de façon structurée.
Autrement dit, on utilisait un type de requête pour mapper des types d’entités sans clés (le plus souvent à partir d’une vue, mais aussi éventuellement à partir d’une table), alors qu’on utilisait un type d’entité normal quand une clé était disponible (le plus souvent à partir d’une table, mais aussi éventuellement à partir d’une vue).

#### <a name="new-behavior"></a>Nouveau comportement

Un type de requête devient maintenant simplement un type d’entité sans clé primaire.
Les types d’entités sans clé ont les mêmes fonctionnalités que les types de requêtes dans les versions précédentes.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin de réduire la confusion concernant l’objectif des types de requêtes.
Plus précisément, il s’agit de types d’entités sans clé et sont intrinsèquement en lecture seule pour cette raison, mais vous ne devez pas les utiliser au seul motif qu’un type d’entité doit être en lecture seule.
De même, ils sont souvent mappés à des vues, mais c’est uniquement car les vues définissent rarement des clés.

#### <a name="mitigations"></a>Corrections

Les parties suivantes de l’API sont désormais obsolètes :

* **`ModelBuilder.Query<>()`** -À la place, `ModelBuilder.Entity<>().HasNoKey()` doit être appelé pour marquer un type d’entité comme n’ayant aucune clé.
Cela ne serait toujours pas configuré par convention, afin d’éviter une configuration incorrecte quand une clé primaire est attendue mais ne correspond pas à la convention.
* **`DbQuery<>`** - `DbSet<>` Doit être utilisé à la place.
* **`DbContext.Query<>()`** - `DbContext.Set<>()` Doit être utilisé à la place.
* **`IQueryTypeConfiguration<TQuery>`** - `IEntityTypeConfiguration<TEntity>` Doit être utilisé à la place.

> [!NOTE]
> En raison d' [un problème dans 3. x](https://github.com/dotnet/efcore/issues/19537) lors de l’interrogation de keymoins les entités dont toutes les propriétés ont la valeur est `null` `null` retournée à la place d’une entité, si ce problème est applicable à votre scénario, ajoutez également une logique pour gérer les `null` résultats.

<a name="config"></a>

### <a name="configuration-api-for-owned-type-relationships-has-changed"></a>L’API de configuration pour les relations de type détenu a changé

#12444 du problème de [suivi](https://github.com/dotnet/efcore/issues/12444) 
 #9148 du problème de [suivi](https://github.com/dotnet/efcore/issues/9148) 
 [#14153 du problème de suivi](https://github.com/dotnet/efcore/issues/14153)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, la configuration de la relation détenue était effectuée directement après l’appel à `OwnsOne` ou `OwnsMany`.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, il existe une API Fluent afin de configurer une propriété de navigation pour le propriétaire à l’aide de `WithOwner()`.
Par exemple :

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

La configuration liée à la relation entre le propriétaire et le détenu doit maintenant être chaînée après `WithOwner()` tout comme les autres relations.
En revanche, la configuration du type détenu lui-même serait toujours chaînée après `OwnsOne()/OwnsMany()`.
Par exemple :

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");

        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

De plus, l’appel à `Entity()`, `HasOne()` ou `Set()` avec un type détenu comme cible lève désormais une exception.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin de créer une distinction plus claire entre la configuration du type détenu lui-même et la _relation au type détenu_.
Cela lève ainsi toute ambiguïté et toute confusion autour des méthodes telles que `HasForeignKey`.

#### <a name="mitigations"></a>Corrections

Changez la configuration des relations de type détenu de façon à utiliser la nouvelle surface d’API, comme indiqué dans l’exemple ci-dessus.

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>Les entités dépendantes qui partagent la table avec le principal sont maintenant facultatives

[Suivi du problème n<c0>o </c0>9005](https://github.com/dotnet/efcore/issues/9005)

#### <a name="old-behavior"></a>Ancien comportement

Considérez le modèle suivant :

```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

Avant EF Core 3.0, si `OrderDetails` est détenu par `Order` ou explicitement mappé à la même table, une instance `OrderDetails` est toujours nécessaire pour ajouter un nouveau `Order`.

#### <a name="new-behavior"></a>Nouveau comportement

À partir de la version 3.0, EF Core permet d’ajouter un `Order` sans `OrderDetails` et mappe toutes les propriétés de `OrderDetails` à l’exception de la clé primaire aux colonnes de type nullable.
Pendant l’interrogation, EF Core définit `OrderDetails` sur `null` si une de ses propriétés obligatoires n’a pas de valeur, ou s’il n’a pas de propriété obligatoire en plus de la clé primaire et que toutes les propriétés sont `null`.

#### <a name="mitigations"></a>Corrections

Si votre modèle a une table qui partage des dépendances avec toutes les colonnes facultatives, mais que la navigation qui pointe vers lui n’est pas censée être `null`, l’application doit être modifiée pour gérer les situations où la navigation est `null`. Si ce n’est pas possible, une propriété obligatoire doit être ajoutée au type d’entité ou au moins une propriété doit avoir une valeur non-`null`.

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a>Toutes les entités qui partagent une table avec une colonne de jeton de concurrence doivent la mapper à une propriété

[Suivi du problème n<c0>o </c0>14154](https://github.com/dotnet/efcore/issues/14154)

#### <a name="old-behavior"></a>Ancien comportement

Considérez le modèle suivant :

```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```

Avant EF Core 3.0, si `OrderDetails` est détenu par `Order` ou explicitement mappé à la même table, la mise à jour de `OrderDetails` seulement ne met pas à jour la valeur de `Version` sur le client et la prochaine mise à jour échoue.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de la version 3.0, EF Core propage la nouvelle valeur `Version` sur `Order` s’il est propriétaire de `OrderDetails`. Sinon, une exception est levée pendant la validation de modèle.

#### <a name="why"></a>Pourquoi

Ce changement a été effectué pour éviter la péremption de la valeur du jeton de concurrence quand une seule des entités mappées à la même table est mise à jour.

#### <a name="mitigations"></a>Corrections

Toutes les entités partageant la table doivent inclure une propriété mappée à la colonne de jeton de concurrence. Vous pouvez en créer une dans un état fantôme :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a>Les entités détenues ne peuvent pas être interrogées sans le propriétaire à l’aide d’une requête de suivi

[#18876 du problème de suivi](https://github.com/dotnet/efcore/issues/18876)

#### <a name="old-behavior"></a>Ancien comportement

Avant le EF Core 3,0, les entités appartenant peuvent être interrogées comme n’importe quelle autre navigation.

```csharp
context.People.Select(p => p.Address);
```

#### <a name="new-behavior"></a>Nouveau comportement

À partir de 3,0, EF Core lève une exception si une requête de suivi projette une entité détenue sans le propriétaire.

#### <a name="why"></a>Pourquoi

Les entités détenues ne peuvent pas être manipulées sans le propriétaire, donc dans la grande majorité des cas, l’interrogation de cette façon est une erreur.

#### <a name="mitigations"></a>Corrections

Si l’entité possédée doit être suivie pour être modifiée de quelque façon que ce soit ultérieurement, le propriétaire doit être inclus dans la requête.

Sinon, ajoutez un `AsNoTracking()` appel :

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a>Les propriétés héritées de types non mappés sont maintenant mappées à une seule colonne pour tous les types dérivés

[Suivi du problème n<c0>o </c0>13998](https://github.com/dotnet/efcore/issues/13998)

#### <a name="old-behavior"></a>Ancien comportement

Considérez le modèle suivant :

```csharp
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

Avant EF Core 3.0, la propriété `ShippingAddress` est mappée à des colonnes distinctes pour `BulkOrder` et `Order` par défaut.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de la version 3.0, EF Core crée uniquement une colonne pour `ShippingAddress`.

#### <a name="why"></a>Pourquoi

L’ancien comportement n’était pas attendu.

#### <a name="mitigations"></a>Corrections

La propriété peut toujours être mappée explicitement à une colonne distincte sur les types dérivés :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>La convention de propriété de clé étrangère ne correspond plus au nom de la propriété principale

[Suivi du problème n<c0>o </c0>13274](https://github.com/dotnet/efcore/issues/13274)

#### <a name="old-behavior"></a>Ancien comportement

Considérez le modèle suivant :

```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

Avant EF Core 3.0, la propriété `CustomerId` était utilisée pour la clé étrangère par convention.
Toutefois, si `Order` est un type détenu, cela fait également de `CustomerId` la clé primaire, ce qui ne correspond généralement pas aux attentes.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de la version 3.0, EF Core ne tente pas d’utiliser des propriétés pour les clés étrangères par convention si elles ont le même nom que la propriété principale.
Les modèles de nom du type de principal concaténé au nom de la propriété de principal, et de nom de navigation concaténé au nom de propriété de principal sont toujours mis en correspondance.
Par exemple :

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin d’éviter de définir de manière erronée une propriété de clé primaire sur le type détenu.

#### <a name="mitigations"></a>Corrections

Si la propriété était censée être la clé étrangère, et par conséquent une partie de la clé primaire, configurez-la explicitement comme telle.

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a>La connexion de base de données est maintenant fermée si elle n’est plus utilisée avant la fin de TransactionScope

[Suivi du problème n<c0>o </c0>14218](https://github.com/dotnet/efcore/issues/14218)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, si le contexte ouvre la connexion à l’intérieur d’un `TransactionScope`, la connexion reste ouverte quand le `TransactionScope` actuel est actif.

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point

        var categories = context.ProductCategories().ToList();
    }
}
```

#### <a name="new-behavior"></a>Nouveau comportement

À compter de la version 3.0, EF Core ferme la connexion dès qu’il ne l’utilise plus.

#### <a name="why"></a>Pourquoi

Ce changement permet d’utiliser plusieurs contextes dans le même `TransactionScope`. Le nouveau comportement correspond également à EF6.

#### <a name="mitigations"></a>Corrections

Si la connexion doit rester ouverte, un appel explicite à `OpenConnection()` garantit qu’EF Core ne la ferme pas prématurément :

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>Chaque propriété utilise la génération indépendante de clé de type entier en mémoire

[Suivi du problème n<c0>o </c0>6872](https://github.com/dotnet/efcore/issues/6872)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, un seul générateur de valeurs partagées était utilisé pour toutes les propriétés de clé de type entier en mémoire.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, chaque propriété de clé de type entier obtient son propre générateur de valeur lors de l’utilisation de la base de données en mémoire.
De plus, si la base de données est supprimée, la génération de clé est réinitialisée pour toutes les tables.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin d’aligner plus étroitement la génération de clé en mémoire à la génération de clé de base de données réelle, et afin d’améliorer la capacité à isoler les tests les uns des autres lors de l’utilisation de la base de données en mémoire.

#### <a name="mitigations"></a>Corrections

Ceci peut casser une application qui repose sur la définition de valeurs de clé en mémoire spécifiques.
Au lieu de cela, ne vous appuyez pas sur des valeurs de clés spécifiques, ou procédez à une mise à jour pour vous aligner au nouveau comportement.

### <a name="backing-fields-are-used-by-default"></a>Les champs de stockage sont utilisés par défaut

[Suivi du problème n<c0>o </c0>12430](https://github.com/dotnet/efcore/issues/12430)

#### <a name="old-behavior"></a>Ancien comportement

Avant la version 3.0, même si le champ de stockage d’une propriété était connu, par défaut EF Core lisait et écrivait toujours la valeur de propriété à l’aide des méthodes get et set de la propriété.
L’exception à cette règle concernait l’exécution des requêtes, où le champ de stockage était défini directement s’il était connu.

#### <a name="new-behavior"></a>Nouveau comportement

Depuis EF Core 3.0, si le champ de stockage d’une propriété est connu, alors EF Core lit et écrit toujours cette propriété à l’aide du champ de stockage.
Cela peut casser une application si celle-ci repose sur un comportement supplémentaire codé dans les méthodes get ou set.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin d’empêcher EF Core de déclencher par erreur une logique métier par défaut quand vous effectuez des opérations de base de données impliquant les entités.

#### <a name="mitigations"></a>Corrections

Vous pouvez restaurer le comportement antérieur à la version 3.0 en configurant le mode d’accès à la propriété sur `ModelBuilder`.
Par exemple :

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>Erreur si plusieurs champs de stockage compatibles sont détectés

[Suivi du problème n<c0>o </c0>12523](https://github.com/dotnet/efcore/issues/12523)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, si plusieurs champs respectaient les règles de recherche du champ de stockage d’une propriété, l’un d’entre eux était choisi selon un ordre de priorité.
Cela pouvait entraîner l’utilisation d’un champ incorrect en cas d’ambiguïté.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, si plusieurs champs sont mis en correspondance avec la même propriété, une exception est levée.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin d’éviter d’utiliser en mode silencieux un champ plutôt qu’un autre quand un seul peut être correct.

#### <a name="mitigations"></a>Corrections

Pour les propriétés comportant des champs de stockage ambigus, le champ à utiliser doit être spécifié explicitement.
Par exemple, à l’aide de l’API Fluent :

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a>Les noms de propriété de type « champ uniquement » doivent correspondre au nom du champ

#### <a name="old-behavior"></a>Ancien comportement

Avant le EF Core 3,0, une propriété pourrait être spécifiée par une valeur de chaîne et si aucune propriété portant ce nom n’a été trouvée sur le type .NET, EF Core essaiera de la faire correspondre à un champ à l’aide de règles de Convention.

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, une propriété de type « champ uniquement » doit correspondre exactement au nom du champ.

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

#### <a name="why"></a>Pourquoi

Ce changement a été apporté pour éviter d’utiliser un même champ pour deux propriétés portant un nom similaire. De plus, les règles de correspondance des propriétés de type « champ uniquement » sont désormais les mêmes que pour les propriétés mappées aux propriétés CLR.

#### <a name="mitigations"></a>Corrections

Les propriétés de type « champ uniquement » doivent porter le même nom que le champ auquel elles sont mappées.
Dans une version ultérieure de EF Core après 3,0, nous prévoyons de réactiver explicitement la configuration d’un nom de champ différent du nom de la propriété (voir le problème [#15307](https://github.com/dotnet/efcore/issues/15307)) :

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a>AddDbContext/AddDbContextPool n’appelle plus AddLogging et AddMemoryCache

[Suivi du problème n<c0>o </c0>14756](https://github.com/dotnet/efcore/issues/14756)

#### <a name="old-behavior"></a>Ancien comportement

Avant le EF Core 3,0, l’appel de `AddDbContext` ou `AddDbContextPool` enregistrerait également des services de journalisation et de mise en cache de mémoire avec des appels à [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) et [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, `AddDbContext` et `AddDbContextPool` n’inscriront plus ces services auprès de l’injection de dépendances.

#### <a name="why"></a>Pourquoi

Il n’est pas nécessaire pour EF Core 3.0 que ces services se trouvent dans le conteneur d’injection de dépendances de l’application. Toutefois, `ILoggerFactory` est utilisé par EF Core même s’il est inscrit dans ce conteneur.

#### <a name="mitigations"></a>Corrections

Si votre application a besoin de ces services, inscrivez-les explicitement auprès du conteneur d’injection de dépendances avec [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) ou [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a>AddEntityFramework * ajoute IMemoryCache avec une limite de taille

[#12905 du problème de suivi](https://github.com/dotnet/efcore/issues/12905)

#### <a name="old-behavior"></a>Ancien comportement

Avant le EF Core 3,0, `AddEntityFramework*` les méthodes d’appel inscrivent également les services de mise en cache en mémoire avec l’injection de transaction sans limite de taille.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de EF Core 3,0, `AddEntityFramework*` inscrira un service IMemoryCache avec une limite de taille. Si d’autres services ajoutés par la suite dépendent de IMemoryCache, ils peuvent rapidement atteindre la limite par défaut provoquant des exceptions ou une dégradation des performances.

#### <a name="why"></a>Pourquoi

L’utilisation de IMemoryCache sans limite peut entraîner une utilisation incontrôlée de la mémoire s’il existe un bogue dans la logique de mise en cache des requêtes ou si les requêtes sont générées dynamiquement. Le fait d’avoir une limite par défaut atténue une attaque potentielle par déni de compte.

#### <a name="mitigations"></a>Corrections

Dans la plupart des cas, l’appel de `AddEntityFramework*` n’est pas nécessaire si `AddDbContext` ou `AddDbContextPool` est également appelé. Par conséquent, la meilleure atténuation consiste à supprimer l' `AddEntityFramework*` appel.

Si votre application a besoin de ces services, inscrivez une implémentation IMemoryCache explicitement avec le conteneur DI au préalable à l’aide de [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>DbContext.Entry effectue maintenant un DetectChanges local

[Suivi du problème n<c0>o </c0>13552](https://github.com/dotnet/efcore/issues/13552)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, le fait d’appeler `DbContext.Entry` provoquait la détection des changements pour toutes les entités suivies.
Cela garantissait que l’état exposé dans `EntityEntry` était à jour.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, l’appel à `DbContext.Entry` tente uniquement de détecter les changements apportés dans l’entité donnée et dans toute entité principale suivie qui lui est associée.
Cela signifie que les changements apportés ailleurs peuvent ne pas avoir été détectés par l’appel à cette méthode, ce qui pourrait avoir des conséquences sur l’état de l’application.

Notez que si `ChangeTracker.AutoDetectChangesEnabled` a la valeur `false`, même cette détection de changement locale sera désactivée.

Les autres méthodes qui provoquent une détection des changements (par exemple `ChangeTracker.Entries` et `SaveChanges`) entraînent toujours un `DetectChanges` complet de toutes les entités suivies.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin d’améliorer les performances par défaut de `context.Entry`.

#### <a name="mitigations"></a>Corrections

Appelez `ChangeTracker.DetectChanges()` explicitement avant d’appeler `Entry` pour garantir le comportement antérieur à la version 3.0.

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>Les clés de tableaux d’octets et de chaînes ne sont pas générés par client par défaut

[Suivi du problème n<c0>o </c0>14617](https://github.com/dotnet/efcore/issues/14617)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, les propriétés de clés `string` et `byte[]` pouvaient être utilisées sans définir explicitement de valeur non null.
Dans ce cas, la valeur de la clé était générée sur le client en tant que GUID, sérialisé en octets pour `byte[]`.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, une exception est levée pour signaler qu’aucune valeur de clé n’a été définie.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté car les valeurs `string`/`byte[]` générées par le client ne sont généralement pas utiles, et le comportement par défaut rendait les valeurs de clés générés de manière courante difficiles à expliquer.

#### <a name="mitigations"></a>Corrections

Vos pouvez obtenir le comportement antérieur à la version 3.0 en spécifiant explicitement que les propriétés de clés doivent utiliser des valeurs générées si aucune autre valeur non nulle n’est définie.
Par exemple, avec l’API Fluent :

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

Ou avec des annotations de données :

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a>ILoggerFactory est désormais un service délimité

[Suivi du problème n<c0>o </c0>14698](https://github.com/dotnet/efcore/issues/14698)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, `ILoggerFactory` était inscrit en tant que service singleton.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, `ILoggerFactory` est inscrit en tant que service délimité.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin d’autoriser l’association d’un journaliseur avec une instance `DbContext`, ce qui active d’autres fonctionnalités et élimine certains cas de comportement pathologique tels que l’explosion des fournisseurs de services internes.

#### <a name="mitigations"></a>Corrections

Ce changement ne devrait pas avoir d’impact sur le code de l’application, sauf en cas d’inscription et d’utilisation de services personnalisés sur le fournisseur de service interne EF Core,
Ce n’est pas courant.
Dans ces cas-là, la plupart des composants continueront à fonctionner, mais tout service singleton qui dépendait de `ILoggerFactory` devra être modifié pour obtenir le `ILoggerFactory` d’une manière différente.

Si vous faites face à ce genre de situation, veuillez soumettre un problème par le biais du [suivi des problèmes GitHub EF Core](https://github.com/dotnet/efcore/issues) pour nous dire comment vous utilisez `ILoggerFactory`, afin que nous puissions mieux comprendre comment ne rien casser à l’avenir.

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>Les proxys à chargement différé ne partent plus du principe que les propriétés de navigation sont entièrement chargées

[Suivi du problème n<c0>o </c0>12780](https://github.com/dotnet/efcore/issues/12780)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, une fois qu’un `DbContext` était supprimé, il n’existait aucun moyen de savoir si une propriété de navigation donnée sur une entité obtenue à partir de ce contexte était entièrement chargée.
Les serveurs proxy partaient du principe qu’une navigation de référence était chargée si elle avait une valeur non null, et qu’une navigation de collection était chargée si elle n’était pas vide.
Dans ces cas-là, toute tentative de chargement différé constituait une no-op.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, les proxys effectuent le suivi du chargement d’une propriété de navigation.
Cela signifie qu’une tentative d’accès à une propriété de navigation qui est chargée une fois que le contexte a été supprimé sera toujours une no-op, même quand la navigation chargée est vide ou null.
À l’inverse, une tentative d’accès à une propriété de navigation qui n’est pas chargée lèvera une exception si le contexte est supprimé, même si la propriété de navigation est une collection non vide.
Si cette situation se présente, cela signifie que le code d’application tente d’utiliser le chargement différé à un moment non valide, et que l’application doit être modifiée afin de ne pas tenter cette opération.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin de rendre le comportement cohérent et correct lors de la tentative de chargement différé sur une instance `DbContext` supprimée.

#### <a name="mitigations"></a>Corrections

Mettez à jour le code d’application pour qu’il ne tente pas d’effectuer un chargement différé avec un contexte supprimé, ou configurez cette opération pour qu’il s’agisse d’une no-op comme décrit dans le message d’exception.

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>La création excessive de fournisseurs de services internes est maintenant une erreur par défaut

[Suivi du problème n<c0>o </c0>10236](https://github.com/dotnet/efcore/issues/10236)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, un avertissement était enregistré dans le journal quand une application créait une quantité pathologique de fournisseurs de services internes.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, cet avertissement est désormais considéré comme une erreur, et une exception est levée.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin d’améliorer le code d’application avec une exposition plus explicite de ce cas pathologique.

#### <a name="mitigations"></a>Corrections

En présence de cette erreur, le mieux consiste à tenter de comprendre la cause racine, et de cesser de créer autant de fournisseurs de services internes.
Toutefois, vous pouvez reconvertir cette erreur en avertissement (ou l’ignorer) par le biais de la configuration sur le `DbContextOptionsBuilder`.
Par exemple :

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a>Nouveau comportement de HasOne/HasMany appelé avec une chaîne unique

[Suivi du problème n<c0>o </c0>9171](https://github.com/dotnet/efcore/issues/9171)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, la façon dont était interprété le code qui appelait `HasOne` ou `HasMany` avec une seule chaîne prêtait à confusion.
Par exemple :

```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

Le code semble relier `Samurai` à un autre type d’entité avec la propriété de navigation `Entrance`, qui peut être privée.

En réalité, ce code tente de créer une relation avec un certain type d’entité nommé `Entrance` sans propriété de navigation.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, le code ci-dessus effectue maintenant ce qu’il semblait devoir faire avant.

#### <a name="why"></a>Pourquoi

L’ancien comportement était très déroutant, en particulier pour qui lisait le code de configuration à la recherche d’erreurs.

#### <a name="mitigations"></a>Corrections

Seules les applications qui configurent explicitement des relations en utilisant des chaînes comme noms de type sans spécifier explicitement la propriété de navigation sont concernées,
ce qui n’est pas courant.
Pour revenir au comportement précédent, transmettez explicitement `null` comme nom de propriété de navigation.
Par exemple :

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a>Le type de retour Task pour plusieurs méthodes asynchrones a été remplacé par ValueTask

[Suivi du problème n<c0>o </c0>15184](https://github.com/dotnet/efcore/issues/15184)

#### <a name="old-behavior"></a>Ancien comportement

Les méthodes asynchrones suivantes retournaient précédemment un `Task<T>` :

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()` (et classes dérivées)

#### <a name="new-behavior"></a>Nouveau comportement

Les méthodes mentionnées précédemment retournent maintenant un `ValueTask<T>` sur le même `T` qu’avant.

#### <a name="why"></a>Pourquoi

Ce changement réduit le nombre d’allocations de tas induits par l’appel de ces méthodes, ce qui améliore les performances générales.

#### <a name="mitigations"></a>Corrections

Les applications qui sont simplement en attente des API ci-dessus doivent uniquement être recompilées, vous n’avez pas besoin de changer la source.
Une utilisation plus complexe (par exemple, le passage du `Task` retourné à `Task.WhenAny()`) nécessite généralement que le `ValueTask<T>` retourné soit converti en `Task<T>` en appelant `AsTask()` sur lui.
Notez que cette opération annule la réduction d’allocation apportée par ce changement.

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>L’annotation Relational:TypeMapping est désormais simplement TypeMapping

[Suivi du problème n<c0>o </c0>9913](https://github.com/dotnet/efcore/issues/9913)

#### <a name="old-behavior"></a>Ancien comportement

Le nom d’annotation pour les annotations de mappage de types était « annotations ».

#### <a name="new-behavior"></a>Nouveau comportement

Le nom d’annotation pour les annotations de mappage de types est désormais « TypeMapping ».

#### <a name="why"></a>Pourquoi

Les mappages de types ne sont désormais plus utilisés uniquement pour les fournisseurs de bases de données relationnelles.

#### <a name="mitigations"></a>Corrections

Ce changement casse uniquement les applications qui accèdent au mappage de types directement en tant qu’annotation, ce qui n’est pas courant.
La meilleure solution consiste à utiliser la surface d’API pour accéder aux mappages de types, plutôt que d’utiliser directement l’annotation.

### <a name="totable-on-a-derived-type-throws-an-exception"></a>ToTable sur un type dérivé lève une exception

[Suivi du problème n<c0>o </c0>11811](https://github.com/dotnet/efcore/issues/11811)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, l’appel à `ToTable()` sur un type dérivé était ignoré, car seule la stratégie de mappage d’héritage était TPH où cela n’est pas valide.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, et en préparation à l’ajout de la prise en charge de TPT et TPC dans une version ultérieure, l’appel à `ToTable()` sur un type dérivé lève maintenant une exception afin d’éviter tout changement inattendu du mappage à l’avenir.

#### <a name="why"></a>Pourquoi

Actuellement le mappage d’un type dérivé à une autre table n’est pas une opération valide.
Ce changement permettra d’éviter toute cassure ultérieure quand cette opération deviendra valide.

#### <a name="mitigations"></a>Corrections

Supprimez toutes les tentatives de mappage de types dérivés à d’autres tables.

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a>ForSqlServerHasIndex est remplacé par HasIndex

[Suivi du problème n<c0>o </c0>12366](https://github.com/dotnet/efcore/issues/12366)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` offrait un moyen de configurer des colonnes utilisées avec `INCLUDE`.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, l’utilisation de `Include` sur un index est prise en charge au niveau relationnel.
Utiliser `HasIndex().ForSqlServerInclude()`.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin de consolider l’API pour les index avec `Include` dans un seul emplacement pour tous les fournisseurs de bases de données.

#### <a name="mitigations"></a>Corrections

Utilisez la nouvelle API, comme indiqué ci-dessus.

### <a name="metadata-api-changes"></a>Changements apportés à l’API de métadonnées

Suivi du problème no 214

#### <a name="new-behavior"></a>Nouveau comportement

Les propriétés suivantes ont été converties en méthodes d’extension :

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

#### <a name="why"></a>Pourquoi

Ce changement simplifie l’implémentation des interfaces mentionnées précédemment.

#### <a name="mitigations"></a>Corrections

Utilisez les nouvelles méthodes d’extension.

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a>Modifications de l’API de métadonnées spécifiques au fournisseur

Suivi du problème no 214

#### <a name="new-behavior"></a>Nouveau comportement

Les méthodes d’extension spécifiques au fournisseur seront aplaties :

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

#### <a name="why"></a>Pourquoi

Ce changement simplifie l’implémentation des méthodes d’extension mentionnées précédemment.

#### <a name="mitigations"></a>Corrections

Utilisez les nouvelles méthodes d’extension.

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>EF Core n’envoie plus de pragma pour l’application de clé étrangère SQLite

[Suivi du problème n<c0>o </c0>12151](https://github.com/dotnet/efcore/issues/12151)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, EF Core envoyait `PRAGMA foreign_keys = 1` quand une connexion à SQLite était ouverte.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, EF Core n’envoie plus de `PRAGMA foreign_keys = 1` quand une connexion à SQLite est ouverte.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté car EF Core utilise `SQLitePCLRaw.bundle_e_sqlite3` par défaut, ce qui signifie que l’application de clé étrangère est activée par défaut et n’a pas besoin d’être activée explicitement chaque fois qu’une connexion est ouverte.

#### <a name="mitigations"></a>Corrections

Les clés étrangères sont activées par défaut dans SQLitePCLRaw.bundle_e_sqlite3, qui est utilisé par défaut pour EF Core.
Pour les autres cas, vous pouvez activer les clés étrangères en spécifiant `Foreign Keys=True` dans votre chaîne de connexion.

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a>Microsoft.EntityFrameworkCore.Sqlite dépend désormais de SQLitePCLRaw.bundle_e_sqlite3

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, EF Core utilisait `SQLitePCLRaw.bundle_green`.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, EF Core utilise `SQLitePCLRaw.bundle_e_sqlite3`.

#### <a name="why"></a>Pourquoi

Ce changement a été apporté afin que la version de SQLite utilisée sur iOS soit cohérente avec d’autres plateformes.

#### <a name="mitigations"></a>Corrections

Pour utiliser la version de SQLite native sur iOS, configurez `Microsoft.Data.Sqlite` de façon à utiliser un autre bundle `SQLitePCLRaw`.

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a>Les valeurs GUID sont maintenant stockées au format TEXT sur SQLite

[Suivi du problème #15078](https://github.com/dotnet/efcore/issues/15078)

#### <a name="old-behavior"></a>Ancien comportement

Avant, les valeurs GUID étaient stockées comme valeurs BLOB sur SQLite.

#### <a name="new-behavior"></a>Nouveau comportement

Maintenant, les valeurs Guid sont stockées au format TEXT.

#### <a name="why"></a>Pourquoi

Le format binaire des valeurs GUID n’est pas normalisé. Le stockage des valeurs au format TEXT rend la base de données plus compatible avec d’autres technologies.

#### <a name="mitigations"></a>Corrections

Vous pouvez migrer des bases de données existantes vers le nouveau format en exécutant SQL comme suit.

```sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

Dans EF Core, vous pouvez également continuer à utiliser le comportement précédent en configurant un convertisseur de valeur sur ces propriétés.

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

Microsoft.Data.Sqlite peut toujours lire les valeurs GUID dans les colonnes BLOB et TEXT. Toutefois, en raison du changement du format par défaut pour les paramètres et les constantes, vous devrez probablement apporter des modifications dans la plupart des scénarios utilisant des valeurs GUID.

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a>Les valeurs char sont maintenant stockées au format TEXT sur SQLite

[Suivi du problème n<c0>o </c0>15020](https://github.com/dotnet/efcore/issues/15020)

#### <a name="old-behavior"></a>Ancien comportement

Avant, les valeurs char étaient stockées comme valeurs INTEGER sur SQLite. Par exemple, une valeur char de *A* était stockée comme valeur entière 65.

#### <a name="new-behavior"></a>Nouveau comportement

Maintenant, les valeurs char sont stockées au format TEXT.

#### <a name="why"></a>Pourquoi

Le stockage des valeurs au format TEXT est plus naturel et rend la base de données plus compatible avec d’autres technologies.

#### <a name="mitigations"></a>Corrections

Vous pouvez migrer des bases de données existantes vers le nouveau format en exécutant SQL comme suit.

```sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

Dans EF Core, vous pouvez également continuer à utiliser le comportement précédent en configurant un convertisseur de valeur sur ces propriétés.

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

Microsoft.Data.Sqlite est aussi toujours capable de lire les valeurs de caractère à partir de colonnes INTEGER et TEXT, donc certains scénarios peuvent ne nécessiter aucune action.

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a>Les ID de migration sont maintenant générés à l’aide du calendrier de la culture invariante

[Suivi du problème n<c0>o </c0>12978](https://github.com/dotnet/efcore/issues/12978)

#### <a name="old-behavior"></a>Ancien comportement

Les ID de migration ont été générés par inadvertance à l’aide du calendrier de la culture actuelle.

#### <a name="new-behavior"></a>Nouveau comportement

Maintenant, les ID de migration sont toujours générés à l’aide du calendrier de la culture invariante (grégorien).

#### <a name="why"></a>Pourquoi

L’ordre des migrations est important lors de la mise à jour de la base de données ou de la résolution des conflits de fusion. L’utilisation du calendrier invariant permet d’éviter les problèmes de classement qui peuvent se produire si les membres de l’équipe ont des calendriers système différents.

#### <a name="mitigations"></a>Corrections

Ce changement affecte tous ceux qui utilisent un calendrier non grégorien où l’année est en avance sur le calendrier grégorien (comme le calendrier bouddhiste thaïlandais). Les ID de migration existants devront être mis à jour afin que les nouvelles migrations soient classées après les migrations existantes.

Vous trouverez les ID de migration dans l’attribut Migration des fichiers de concepteur des migrations.

```diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

Le tableau de l’historique Migrations doit également être mis à jour.

```sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a>UseRowNumberForPaging a été supprimé

Suivi de problème no 16400

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, `UseRowNumberForPaging` pouvait être utilisé pour générer SQL pour la pagination qui est compatible avec SQL Server 2008.

#### <a name="new-behavior"></a>Nouveau comportement

À compter de EF Core 3.0, EF génère uniquement SQL pour la pagination qui est uniquement compatible avec les versions de SQL Server ultérieures.

#### <a name="why"></a>Pourquoi

Nous effectuons cette modification, car [SQL Server 2008 n’est plus un produit pris en charge](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) et la mise à jour de cette fonctionnalité pour fonctionner avec les modifications de requête effectuées dans EF Core 3.0 est un travail significatif.

#### <a name="mitigations"></a>Corrections

Nous vous recommandons d’effectuer la mise à jour vers une version plus récente de SQL Server ou d’utiliser un niveau de compatibilité plus élevé, afin que le SQL généré soit pris en charge. Cela dit, si vous ne pouvez pas faire cela, veuillez [commenter le problème de suivi](https://github.com/dotnet/efcore/issues/16400) en incluant des détails. Nous pourrions revisiter cette décision en fonction des commentaires.

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a>Les info/métadonnées d’extension ont été supprimées de IDbContextOptionsExtension

Suivi du problème no 16119

#### <a name="old-behavior"></a>Ancien comportement

`IDbContextOptionsExtension` contenait des méthodes permettant de fournir des métadonnées relatives à l’extension.

#### <a name="new-behavior"></a>Nouveau comportement

Ces méthodes ont été déplacées vers une nouvelle classe de base abstraite `DbContextOptionsExtensionInfo`, qui est retournée à partir d’une nouvelle propriété `IDbContextOptionsExtension.Info`.

#### <a name="why"></a>Pourquoi

Sur les versions de 2.0 à 3.0, nous devions ajouter ou modifier ces méthodes plusieurs fois.
Les sortir dans une nouvelle classe de base abstraite simplifie l’apport de ces types de changements sans résiliation des extensions existantes.

#### <a name="mitigations"></a>Corrections

Mettre à jour des extensions pour suivre le nouveau modèle.
Des exemples se trouvent dans la plupart des implémentations de `IDbContextOptionsExtension` pour différents types d’extensions dans le code source EF Core.

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a>LogQueryPossibleExceptionWithAggregateOperator a été renommé

[Suivi du problème #10985](https://github.com/dotnet/efcore/issues/10985)

#### <a name="change"></a>Modifier

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` a été renommé en `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.

#### <a name="why"></a>Pourquoi

Aligne le nom de cet événement d’avertissement avec tous les autres événements d’avertissement.

#### <a name="mitigations"></a>Corrections

Utilisez le nouveau nom. (Notez que le numéro d’ID événement n’a pas changé.)

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a>Clarifier les noms de contrainte de clé étrangère dans l’API

[Suivi du problème #10730](https://github.com/dotnet/efcore/issues/10730)

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, les noms de contrainte de clé étrangère étaient désignés simplement par le terme « nom ». Par exemple :

```csharp
var constraintName = myForeignKey.Name;
```

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, les noms de contrainte de clé étrangère sont désignés par « noms de contrainte ». Par exemple :

```csharp
var constraintName = myForeignKey.ConstraintName;
```

#### <a name="why"></a>Pourquoi

Ce changement rend le nommage plus cohérent dans ce domaine. Il clarifie également le fait qu’il s’agit du nom de la contrainte de clé étrangère et pas du nom de la colonne ou de la propriété sur laquelle la clé étrangère est définie.

#### <a name="mitigations"></a>Corrections

Utilisez le nouveau nom.

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a>IRelationalDatabaseCreator.HasTables/HasTablesAsync ont été rendues publiques

Suivi du problème no 15997

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, ces méthodes étaient protégées.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, ces méthodes sont publiques.

#### <a name="why"></a>Pourquoi

Ces méthodes sont utilisées par EF pour déterminer si une base de données est créée mais vide. Cela peut également être utile depuis l’extérieur d’EF lorsque vous déterminez s’il faut appliquer des migrations ou pas.

#### <a name="mitigations"></a>Corrections

Modifiez l’accessibilité de n’importe quel remplacements.

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a>Microsoft.EntityFrameworkCore.Design est désormais un package DevelopmentDependency

Suivi du problème no 11506

#### <a name="old-behavior"></a>Ancien comportement

Avant EF Core 3.0, Microsoft.EntityFrameworkCore.Design était un package NuGet normal dont l’assembly pouvait être référencée par des projets qui en dépendaient.

#### <a name="new-behavior"></a>Nouveau comportement

À compter d’EF Core 3.0, il s’agit d’un package DevelopmentDependency. Cela signifie que la dépendance ne sera pas transitive dans d’autres projets, et que vous ne pouvez plus, par défaut, référencer son assembly.

#### <a name="why"></a>Pourquoi

Ce package est uniquement destiné à être utilisé au moment du design. Les applications déployées ne doivent pas y faire référence. Le fait de faire de ce package un DevelopmentDependency renforce cette recommandation.

#### <a name="mitigations"></a>Corrections

Si vous avez besoin de référencer ce package pour remplacer le comportement de EF Core au moment de la conception, vous pouvez mettre à jour les métadonnées de l’élément PackageReference dans votre projet.

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

Si le package est référencé de manière transitive via Microsoft.EntityFrameworkCore.Tools, vous devez ajouter un PackageReference explicite au package pour modifier ses métadonnées. Une telle référence explicite doit être ajoutée à n’importe quel projet où les types du package sont nécessaires.

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a>SQLitePCL.raw mis à jour vers la version 2.0.0

Suivi du problème no 14824

#### <a name="old-behavior"></a>Ancien comportement

Microsoft.EntityFrameworkCore.Sqlite dépendait précédemment de la version 1.1.12 de SQLitePCL.raw.

#### <a name="new-behavior"></a>Nouveau comportement

Nous avons mis à jour notre package pour dépendre de la version 2.0.0.

#### <a name="why"></a>Pourquoi

La version 2.0.0 de SQLitePCL.raw cible .NET Standard 2.0. Elle ciblait précédemment .NET Standard 1.1 qui nécessitait une fermeture volumineuse de packages transitifs pour fonctionner.

#### <a name="mitigations"></a>Corrections

SQLitePCL.raw version 2.0.0 inclut des changements cassants. Pour plus d’informations, consultez les [notes de publication](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) .

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a>NetTopologySuite mis à jour vers la version 2.0.0

Suivi de problème no 14825

#### <a name="old-behavior"></a>Ancien comportement

Les packages spatiaux dépendaient précédemment de la version 1.15.1 de NetTopologySuite.

#### <a name="new-behavior"></a>Nouveau comportement

Nous avons mis à jour notre package pour dépendre de la version 2.0.0.

#### <a name="why"></a>Pourquoi

La version 2.0.0 de NetTopologySuite vise à résoudre plusieurs problèmes de facilité d’utilisation rencontrés par les utilisateurs EF Core.

#### <a name="mitigations"></a>Corrections

NetTopologySuite version 2.0.0 inclut des changements cassants. Pour plus d’informations, consultez les [notes de publication](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) .

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a>Microsoft. Data. SqlClient est utilisé à la place de System. Data. SqlClient

[#15636 du problème de suivi](https://github.com/dotnet/efcore/issues/15636)

#### <a name="old-behavior"></a>Ancien comportement

Microsoft. EntityFrameworkCore. SqlServer était auparavant tributaire de System. Data. SqlClient.

#### <a name="new-behavior"></a>Nouveau comportement

Nous avons mis à jour notre package pour dépendre de Microsoft. Data. SqlClient.

#### <a name="why"></a>Pourquoi

Microsoft. Data. SqlClient est le pilote d’accès aux données phare pour la SQL Server à l’avenir, et System. Data. SqlClient ne représente plus le point de vue du développement.
Certaines fonctionnalités importantes, telles que Always Encrypted, sont uniquement disponibles sur Microsoft. Data. SqlClient.

#### <a name="mitigations"></a>Corrections

Si votre code prend une dépendance directe sur System. Data. SqlClient, vous devez le modifier pour qu’il fasse référence à Microsoft. Data. SqlClient à la place. étant donné que les deux packages maintiennent un très haut niveau de compatibilité d’API, il ne doit y avoir qu’une simple modification de package et d’espace de noms.

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a>Plusieurs relations autoréférencées ambiguës doivent être configurées

Suivi de problème no 13573

#### <a name="old-behavior"></a>Ancien comportement

Un type d’entité avec plusieurs propriétés de navigation unidirectionnelle autoréférencées et les clés étrangères correspondantes a été incorrectement configuré en tant que relation unique. Par exemple :

```csharp
public class User
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

#### <a name="new-behavior"></a>Nouveau comportement

Ce scénario est maintenant détecté dans la génération de modèle et une exception est levée, indiquant que le modèle est ambigu.

#### <a name="why"></a>Pourquoi

Le modèle résultant est ambigu et probablement erroné dans ce cas.

#### <a name="mitigations"></a>Corrections

Utilisez la configuration complète de la relation. Par exemple :

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();

 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>

### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a>DbFunction. Schema étant null ou une chaîne vide, il est configuré pour être dans le schéma par défaut du modèle

[#12757 du problème de suivi](https://github.com/dotnet/efcore/issues/12757)

#### <a name="old-behavior"></a>Ancien comportement

Un DbFunction configuré avec un schéma sous forme de chaîne vide a été traité comme une fonction intégrée sans schéma. Par exemple, le code suivant mappera la `DatePart` fonction CLR à la `DATEPART` fonction intégrée sur SqlServer.

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

#### <a name="new-behavior"></a>Nouveau comportement

Tous les mappages de DbFunction sont considérés comme mappés à des fonctions définies par l’utilisateur. Par conséquent, la valeur de chaîne vide placerait la fonction dans le schéma par défaut pour le modèle. Ce peut être le schéma configuré explicitement via l’API Fluent `modelBuilder.HasDefaultSchema()` ou `dbo` sinon.

#### <a name="why"></a>Pourquoi

Le schéma précédemment vide était un moyen de traiter que la fonction est intégrée, mais cette logique s’applique uniquement à SqlServer, où les fonctions intégrées n’appartiennent à aucun schéma.

#### <a name="mitigations"></a>Corrections

Configurez la traduction de DbFunction manuellement pour la mapper à une fonction intégrée.

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```

<a name="netstandard21"></a>

### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20-reverted"></a>~~EF Core 3,0 cibles .NET Standard 2,1 au lieu de .NET Standard 2,0~~ Rétablie

Suivi de problème no 15498

EF Core 3,0 cible .NET Standard 2,1, qui est une modification avec rupture qui exclut les applications .NET Framework. EF Core 3,1 a rétabli l’opération et cible à nouveau .NET Standard 2,0.

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a>~~L’exécution de requêtes est enregistrée au niveau du débogage ~~Rétabli

[Suivi du problème n<c0>o </c0>14523](https://github.com/dotnet/efcore/issues/14523)

Nous avons rétabli ce changement car la nouvelle configuration dans EF Core 3.0 permet la spécification du niveau d’enregistrement d’un événement par l’application. Par exemple, pour basculer l’enregistrement de SQL vers `Debug`, configurez explicitement le niveau dans `OnConfiguring` ou `AddDbContext` :

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```
