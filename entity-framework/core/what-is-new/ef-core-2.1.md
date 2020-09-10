---
title: Nouveautés d’EF Core 2.1 - EF Core
description: Modifications et améliorations dans Entity Framework Core 2,1
author: divega
ms.date: 02/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: 5aa8aa5d3578b9f69e63adaf731a1642f748c5c2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618762"
---
# <a name="new-features-in-ef-core-21"></a>Nouvelles fonctionnalités d’EF Core 2.1

En plus de nombreux correctifs de bogues et de petites améliorations des fonctionnalités et des performances, EF Core 2.1 inclut de nouvelles fonctionnalités intéressantes :

## <a name="lazy-loading"></a>Chargement différé

EF Core contient désormais les blocs de construction nécessaires pour permettre à quiconque de créer des classes d’entité capables de charger leurs propriétés de navigation à la demande. Nous avons également introduit un nouveau package, Microsoft.EntityFrameworkCore.Proxies, qui tire parti de ces composants pour produire des classes proxy de chargement différé basées sur des classes d’entité ayant subi des modifications minimales (par exemple des classes avec des propriétés de navigation virtuelles).

Pour plus d’informations sur cette rubrique, lisez la [section sur le chargement différé](xref:core/querying/related-data#lazy-loading).

## <a name="parameters-in-entity-constructors"></a>Paramètres dans les constructeurs d’entité

L’un des blocs de construction nécessaires pour le chargement différé prend désormais en charge la création d’entités qui acceptent des paramètres dans leurs constructeurs. Vous pouvez utiliser des paramètres pour injecter des valeurs de propriété, des délégués de chargement différé et des services.

Pour plus d’informations sur cette rubrique, lisez la [section sur le constructeur d’entité avec des paramètres](xref:core/modeling/constructors).

## <a name="value-conversions"></a>Conversions de valeurs

Jusqu’à présent, EF Core pouvait uniquement mapper les propriétés de types pris en charge nativement par le fournisseur de base de données sous-jacent. Les valeurs étaient copiées entre les colonnes et les propriétés sans aucune transformation. À compter d’EF Core 2.1, les conversions de valeurs peuvent être appliquées pour transformer les valeurs obtenues à partir de colonnes avant leur application aux propriétés, et vice versa. Nous avons un certain nombre de conversions qui peuvent être appliquées par convention, le cas échéant, ainsi qu’une API de configuration explicite qui permet d’inscrire des conversions personnalisées entre des colonnes et des propriétés. Parmi les applications de cette fonctionnalité, citons les suivantes :

- Stockage des enums sous forme de chaînes
- Mappage d’entiers non signés avec SQL Server
- Chiffrement et déchiffrement automatiques des valeurs de propriété

Pour plus d’informations sur cette rubrique, lisez la [section sur les conversions de valeurs](xref:core/modeling/value-conversions).  

## <a name="linq-groupby-translation"></a>Traduction LINQ GroupBy

Avant la version 2.1, l’opérateur LINQ GroupBy dans EF Core était toujours évalué en mémoire. Il est désormais possible de le traduire en clause SQL GROUP BY dans les scénarios les plus courants.

Cet exemple illustre une requête avec GroupBy utilisée pour calculer différentes fonctions d’agrégation :

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => o.Amount)
        });
```

La traduction SQL correspondante ressemble à ceci :

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a>Amorçage des données

Avec la nouvelle version, il sera possible de fournir des données initiales pour remplir une base de données. Contrairement à ce qui se passe dans EF6, l’amorçage des données est associé à un type d’entité dans le cadre de la configuration du modèle. Les migrations EF Core peuvent automatiquement calculer les opérations d’insertion, de mise à jour ou de suppression à appliquer au moment de la mise à niveau de la base de données vers une nouvelle version du modèle.

Par exemple, utilisez ceci pour configurer les données d’amorçage d’un Post dans `OnModelCreating`:

``` csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

Pour plus d’informations sur cette rubrique, lisez la [section sur l’amorçage des données](xref:core/modeling/data-seeding).  

## <a name="query-types"></a>Types de requêtes

Un modèle EF Core peut désormais inclure des types de requêtes. Contrairement aux types d’entités, les types de requêtes ne sont pas associés à une définition de clé et ne peuvent pas être insérés, supprimés ou mis à jour (autrement dit, ils sont en lecture seule). Toutefois, ils peuvent être retournés directement par des requêtes. Voici quelques scénarios d’usages pour les types de requêtes :

- Mappage à des vues sans clés primaires
- Mappage à des tables sans clés primaires
- Mappage à des requêtes définies dans le modèle
- Utilisation comme type de retour pour les requêtes `FromSql()`

Pour plus d’informations sur cette rubrique, lisez la [section sur les types de requêtes](xref:core/modeling/keyless-entity-types).

## <a name="include-for-derived-types"></a>Include pour les types dérivés

Vous pouvez désormais spécifier des propriétés de navigation définies uniquement sur des types dérivés lors de l’écriture d’expressions pour la méthode `Include`. Pour la version fortement typée de `Include`, nous prenons en charge l’utilisation d’un cast explicite ou de l’opérateur `as`. Nous prenons également en charge le référencement des noms de propriété de navigation définis sur des types dérivés dans la version chaîne de `Include` :

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

Pour plus d’informations sur cette rubrique, lisez la [section sur Include avec des types dérivés](xref:core/querying/related-data#include-on-derived-types).

## <a name="systemtransactions-support"></a>Prise en charge de System.Transactions

Vous pouvez désormais utiliser les fonctionnalités System.Transactions, notamment TransactionScope. Celles-ci fonctionnent sur le .NET Framework et .NET Core avec des fournisseurs de base de données compatibles.

Pour plus d’informations sur cette rubrique, lisez la [section sur System.Transactions](xref:core/saving/transactions#using-systemtransactions).

## <a name="better-column-ordering-in-initial-migration"></a>Meilleur classement des colonnes dans la migration initiale

Après avoir passé en revue les commentaires des clients, nous avons mis à jour les migrations de manière à générer initialement des colonnes pour les tables dans le même ordre que les propriétés déclarées dans les classes. Notez qu’EF Core ne peut pas changer l’ordre si de nouveaux membres sont ajoutés après la création de la table initiale.

## <a name="optimization-of-correlated-subqueries"></a>Optimisation des sous-requêtes corrélées

Nous avons amélioré la traduction des requêtes pour éviter l’exécution de requêtes SQL « N + 1 » dans de nombreux scénarios courants dans lesquels l’utilisation d’une propriété de navigation dans la projection débouche sur la jointure des données de la requête racine avec les données d’une sous-requête corrélée. L’optimisation nécessite la mise en mémoire tampon des résultats à partir de la sous-requête. Nous vous demandons par ailleurs de modifier la requête pour procéder à l’adhésion du nouveau comportement.

Par exemple, la requête suivante est normalement traduite en une seule requête pour Customers, plus N requêtes séparées pour Orders (où « N » est le nombre de clients retournés) :

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

En incluant `ToList()` au bon endroit, vous indiquez que la mise en mémoire tampon est appropriée pour Orders, ce qui déclenche l’optimisation :

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

Notez que cette requête est traduite en deux requêtes SQL seulement : une pour Customers et la suivante pour Orders.

## <a name="owned-attribute"></a>Attribut [Owned]

Il est désormais possible de configurer des [types d’entités détenus](xref:core/modeling/owned-entities) en annotant simplement le type avec `[Owned]` et en veillant à ce que l’entité propriétaire soit ajoutée au modèle :

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a>Outil en ligne de commande dotnet-ef inclus dans le SDK .NET Core

Les commandes _ef-dotnet_ font désormais partie du SDK .NET Core. Ainsi, il n’est plus nécessaire d’utiliser DotNetCliToolReference dans le projet pour pouvoir utiliser des migrations ou structurer un DbContext à partir d’une base de données existante.

Pour plus d’informations sur la façon d’activer les outils en ligne de commande pour différentes versions du SDK .NET Core et EF Core, consultez la section sur [l’installation des outils](xref:core/miscellaneous/cli/dotnet#installing-the-tools).

## <a name="microsoftentityframeworkcoreabstractions-package"></a>Package Microsoft.EntityFrameworkCore.Abstractions

Le nouveau package contient des attributs et des interfaces que vous pouvez utiliser dans vos projets pour alléger les fonctionnalités EF Core sans dépendance envers Core EF dans sa globalité. Par exemple, l’attribut [Owned] et l’interface ILazyLoader se trouvent ici.

## <a name="state-change-events"></a>Événements de changement d’état

Les nouveaux événements `Tracked` et `StateChanged` sur `ChangeTracker` peuvent être utilisés pour écrire une logique qui réagit aux entités qui entrent dans DbContext ou modifiant leur état.

## <a name="raw-sql-parameter-analyzer"></a>Analyseur de paramètre SQL brut

Un nouvel analyseur de code est inclus avec EF Core. Il détecte les utilisations potentiellement dangereuses de nos API SQL brutes, comme `FromSql` ou `ExecuteSqlCommand`. Par exemple, dans la requête suivante, un avertissement s’affiche, car _minAge_ n’est pas paramétrable :

``` csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a>Compatibilité des fournisseurs de base de données

Nous vous recommandons d’utiliser EF Core 2.1 avec des fournisseurs qui ont été mis à jour ou au moins testés pour fonctionner avec EF Core 2.1.

> [!TIP]
> Si vous vous heurtez à des incompatibilités inattendues ou à d’autres problèmes liés aux nouvelles fonctionnalités, ou si vous avez des commentaires à propos de ces nouveautés, utilisez [notre système de suivi des problèmes](https://github.com/aspnet/EntityFrameworkCore/issues/new) pour nous en faire part.
