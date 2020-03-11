---
title: Nouveautés d’EF Core 2.1 - EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: ba3a26bcd76cd0b9615b13f32456e7280afe533a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417481"
---
# <a name="new-features-in-ef-core-21"></a><span data-ttu-id="35092-102">Nouvelles fonctionnalités d’EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="35092-102">New features in EF Core 2.1</span></span>

<span data-ttu-id="35092-103">En plus de nombreux correctifs de bogues et de petites améliorations des fonctionnalités et des performances, EF Core 2.1 inclut de nouvelles fonctionnalités intéressantes :</span><span class="sxs-lookup"><span data-stu-id="35092-103">Besides numerous bug fixes and small functional and performance enhancements, EF Core 2.1 includes some compelling new features:</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="35092-104">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="35092-104">Lazy loading</span></span>

<span data-ttu-id="35092-105">EF Core contient désormais les blocs de construction nécessaires pour permettre à quiconque de créer des classes d’entité capables de charger leurs propriétés de navigation à la demande.</span><span class="sxs-lookup"><span data-stu-id="35092-105">EF Core now contains the necessary building blocks for anyone to author entity classes that can load their navigation properties on demand.</span></span> <span data-ttu-id="35092-106">Nous avons également introduit un nouveau package, Microsoft.EntityFrameworkCore.Proxies, qui tire parti de ces composants pour produire des classes proxy de chargement différé basées sur des classes d’entité ayant subi des modifications minimales (par exemple des classes avec des propriétés de navigation virtuelles).</span><span class="sxs-lookup"><span data-stu-id="35092-106">We have also created a new package, Microsoft.EntityFrameworkCore.Proxies, that leverages those building blocks to produce lazy loading proxy classes based on minimally modified entity classes (for example, classes with virtual navigation properties).</span></span>

<span data-ttu-id="35092-107">Pour plus d’informations sur cette rubrique, lisez la [section sur le chargement différé](xref:core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="35092-107">Read the [section on lazy loading](xref:core/querying/related-data#lazy-loading) for more information about this topic.</span></span>

## <a name="parameters-in-entity-constructors"></a><span data-ttu-id="35092-108">Paramètres dans les constructeurs d’entité</span><span class="sxs-lookup"><span data-stu-id="35092-108">Parameters in entity constructors</span></span>

<span data-ttu-id="35092-109">L’un des blocs de construction nécessaires pour le chargement différé prend désormais en charge la création d’entités qui acceptent des paramètres dans leurs constructeurs.</span><span class="sxs-lookup"><span data-stu-id="35092-109">As one of the required building blocks for lazy loading, we enabled the creation of entities that take parameters in their constructors.</span></span> <span data-ttu-id="35092-110">Vous pouvez utiliser des paramètres pour injecter des valeurs de propriété, des délégués de chargement différé et des services.</span><span class="sxs-lookup"><span data-stu-id="35092-110">You can use parameters to inject property values, lazy loading delegates, and services.</span></span>

<span data-ttu-id="35092-111">Pour plus d’informations sur cette rubrique, lisez la [section sur le constructeur d’entité avec des paramètres](xref:core/modeling/constructors).</span><span class="sxs-lookup"><span data-stu-id="35092-111">Read the [section on entity constructor with parameters](xref:core/modeling/constructors) for more information about this topic.</span></span>

## <a name="value-conversions"></a><span data-ttu-id="35092-112">Conversions de valeurs</span><span class="sxs-lookup"><span data-stu-id="35092-112">Value conversions</span></span>

<span data-ttu-id="35092-113">Jusqu’à présent, EF Core pouvait uniquement mapper les propriétés de types pris en charge nativement par le fournisseur de base de données sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="35092-113">Until now, EF Core could only map properties of types natively supported by the underlying database provider.</span></span> <span data-ttu-id="35092-114">Les valeurs étaient copiées entre les colonnes et les propriétés sans aucune transformation.</span><span class="sxs-lookup"><span data-stu-id="35092-114">Values were copied back and forth between columns and properties without any transformation.</span></span> <span data-ttu-id="35092-115">À compter d’EF Core 2.1, les conversions de valeurs peuvent être appliquées pour transformer les valeurs obtenues à partir de colonnes avant leur application aux propriétés, et vice versa.</span><span class="sxs-lookup"><span data-stu-id="35092-115">Starting with EF Core 2.1, value conversions can be applied to transform the values obtained from columns before they are applied to properties, and vice versa.</span></span> <span data-ttu-id="35092-116">Nous avons un certain nombre de conversions qui peuvent être appliquées par convention, le cas échéant, ainsi qu’une API de configuration explicite qui permet d’inscrire des conversions personnalisées entre des colonnes et des propriétés.</span><span class="sxs-lookup"><span data-stu-id="35092-116">We have a number of conversions that can be applied by convention as necessary, as well as an explicit configuration API that allows registering custom conversions between columns and properties.</span></span> <span data-ttu-id="35092-117">Parmi les applications de cette fonctionnalité, citons les suivantes :</span><span class="sxs-lookup"><span data-stu-id="35092-117">Some of the application of this feature are:</span></span>

- <span data-ttu-id="35092-118">Stockage des enums sous forme de chaînes</span><span class="sxs-lookup"><span data-stu-id="35092-118">Storing enums as strings</span></span>
- <span data-ttu-id="35092-119">Mappage d’entiers non signés avec SQL Server</span><span class="sxs-lookup"><span data-stu-id="35092-119">Mapping unsigned integers with SQL Server</span></span>
- <span data-ttu-id="35092-120">Chiffrement et déchiffrement automatiques des valeurs de propriété</span><span class="sxs-lookup"><span data-stu-id="35092-120">Automatic encryption and decryption of property values</span></span>

<span data-ttu-id="35092-121">Pour plus d’informations sur cette rubrique, lisez la [section sur les conversions de valeurs](xref:core/modeling/value-conversions).</span><span class="sxs-lookup"><span data-stu-id="35092-121">Read the [section on value conversions](xref:core/modeling/value-conversions) for more information about this topic.</span></span>  

## <a name="linq-groupby-translation"></a><span data-ttu-id="35092-122">Traduction LINQ GroupBy</span><span class="sxs-lookup"><span data-stu-id="35092-122">LINQ GroupBy translation</span></span>

<span data-ttu-id="35092-123">Avant la version 2.1, l’opérateur LINQ GroupBy dans EF Core était toujours évalué en mémoire.</span><span class="sxs-lookup"><span data-stu-id="35092-123">Before version 2.1, in EF Core the GroupBy LINQ operator would always be evaluated in memory.</span></span> <span data-ttu-id="35092-124">Il est désormais possible de le traduire en clause SQL GROUP BY dans les scénarios les plus courants.</span><span class="sxs-lookup"><span data-stu-id="35092-124">We now support translating it to the SQL GROUP BY clause in most common cases.</span></span>

<span data-ttu-id="35092-125">Cet exemple illustre une requête avec GroupBy utilisée pour calculer différentes fonctions d’agrégation :</span><span class="sxs-lookup"><span data-stu-id="35092-125">This example shows a query with GroupBy used to compute various aggregate functions:</span></span>

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

<span data-ttu-id="35092-126">La traduction SQL correspondante ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="35092-126">The corresponding SQL translation looks like this:</span></span>

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a><span data-ttu-id="35092-127">Amorçage des données</span><span class="sxs-lookup"><span data-stu-id="35092-127">Data Seeding</span></span>

<span data-ttu-id="35092-128">Avec la nouvelle version, il sera possible de fournir des données initiales pour remplir une base de données.</span><span class="sxs-lookup"><span data-stu-id="35092-128">With the new release it will be possible to provide initial data to populate a database.</span></span> <span data-ttu-id="35092-129">Contrairement à ce qui se passe dans EF6, l’amorçage des données est associé à un type d’entité dans le cadre de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="35092-129">Unlike in EF6, seeding data is associated to an entity type as part of the model configuration.</span></span> <span data-ttu-id="35092-130">Les migrations EF Core peuvent automatiquement calculer les opérations d’insertion, de mise à jour ou de suppression à appliquer au moment de la mise à niveau de la base de données vers une nouvelle version du modèle.</span><span class="sxs-lookup"><span data-stu-id="35092-130">Then EF Core migrations can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="35092-131">Par exemple, utilisez ceci pour configurer les données d’amorçage d’un Post dans `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="35092-131">As an example, you can use this to configure seed data for a Post in `OnModelCreating`:</span></span>

``` csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

<span data-ttu-id="35092-132">Pour plus d’informations sur cette rubrique, lisez la [section sur l’amorçage des données](xref:core/modeling/data-seeding).</span><span class="sxs-lookup"><span data-stu-id="35092-132">Read the [section on data seeding](xref:core/modeling/data-seeding) for more information about this topic.</span></span>  

## <a name="query-types"></a><span data-ttu-id="35092-133">Types de requêtes</span><span class="sxs-lookup"><span data-stu-id="35092-133">Query types</span></span>

<span data-ttu-id="35092-134">Un modèle EF Core peut désormais inclure des types de requêtes.</span><span class="sxs-lookup"><span data-stu-id="35092-134">An EF Core model can now include query types.</span></span> <span data-ttu-id="35092-135">Contrairement aux types d’entités, les types de requêtes ne sont pas associés à une définition de clé et ne peuvent pas être insérés, supprimés ou mis à jour (autrement dit, ils sont en lecture seule). Toutefois, ils peuvent être retournés directement par des requêtes.</span><span class="sxs-lookup"><span data-stu-id="35092-135">Unlike entity types, query types do not have keys defined on them and cannot be inserted, deleted or updated (that is, they are read-only), but they can be returned directly by queries.</span></span> <span data-ttu-id="35092-136">Voici quelques scénarios d’usages pour les types de requêtes :</span><span class="sxs-lookup"><span data-stu-id="35092-136">Some of the usage scenarios for query types are:</span></span>

- <span data-ttu-id="35092-137">Mappage à des vues sans clés primaires</span><span class="sxs-lookup"><span data-stu-id="35092-137">Mapping to views without primary keys</span></span>
- <span data-ttu-id="35092-138">Mappage à des tables sans clés primaires</span><span class="sxs-lookup"><span data-stu-id="35092-138">Mapping to tables without primary keys</span></span>
- <span data-ttu-id="35092-139">Mappage à des requêtes définies dans le modèle</span><span class="sxs-lookup"><span data-stu-id="35092-139">Mapping to queries defined in the model</span></span>
- <span data-ttu-id="35092-140">Utilisation comme type de retour pour les requêtes `FromSql()`</span><span class="sxs-lookup"><span data-stu-id="35092-140">Serving as the return type for `FromSql()` queries</span></span>

<span data-ttu-id="35092-141">Pour plus d’informations sur cette rubrique, lisez la [section sur les types de requêtes](xref:core/modeling/keyless-entity-types).</span><span class="sxs-lookup"><span data-stu-id="35092-141">Read the [section on query types](xref:core/modeling/keyless-entity-types) for more information about this topic.</span></span>

## <a name="include-for-derived-types"></a><span data-ttu-id="35092-142">Include pour les types dérivés</span><span class="sxs-lookup"><span data-stu-id="35092-142">Include for derived types</span></span>

<span data-ttu-id="35092-143">Vous pouvez désormais spécifier des propriétés de navigation définies uniquement sur des types dérivés lors de l’écriture d’expressions pour la méthode `Include`.</span><span class="sxs-lookup"><span data-stu-id="35092-143">It will be now possible to specify navigation properties only defined on derived types when writing expressions for the `Include` method.</span></span> <span data-ttu-id="35092-144">Pour la version fortement typée de `Include`, nous prenons en charge l’utilisation d’un cast explicite ou de l’opérateur `as`.</span><span class="sxs-lookup"><span data-stu-id="35092-144">For the strongly typed version of `Include`, we support using either an explicit cast or the `as` operator.</span></span> <span data-ttu-id="35092-145">Nous prenons également en charge le référencement des noms de propriété de navigation définis sur des types dérivés dans la version chaîne de `Include` :</span><span class="sxs-lookup"><span data-stu-id="35092-145">We also now support referencing the names of navigation property defined on derived types in the string version of `Include`:</span></span>

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

<span data-ttu-id="35092-146">Pour plus d’informations sur cette rubrique, lisez la [section sur Include avec des types dérivés](xref:core/querying/related-data#include-on-derived-types).</span><span class="sxs-lookup"><span data-stu-id="35092-146">Read the [section on Include with derived types](xref:core/querying/related-data#include-on-derived-types) for more information about this topic.</span></span>

## <a name="systemtransactions-support"></a><span data-ttu-id="35092-147">Prise en charge de System.Transactions</span><span class="sxs-lookup"><span data-stu-id="35092-147">System.Transactions support</span></span>

<span data-ttu-id="35092-148">Vous pouvez désormais utiliser les fonctionnalités System.Transactions, notamment TransactionScope.</span><span class="sxs-lookup"><span data-stu-id="35092-148">We have added the ability to work with System.Transactions features such as TransactionScope.</span></span> <span data-ttu-id="35092-149">Celles-ci fonctionnent sur le .NET Framework et .NET Core avec des fournisseurs de base de données compatibles.</span><span class="sxs-lookup"><span data-stu-id="35092-149">This will work on both .NET Framework and .NET Core when using database providers that support it.</span></span>

<span data-ttu-id="35092-150">Pour plus d’informations sur cette rubrique, lisez la [section sur System.Transactions](xref:core/saving/transactions#using-systemtransactions).</span><span class="sxs-lookup"><span data-stu-id="35092-150">Read the [section on System.Transactions](xref:core/saving/transactions#using-systemtransactions) for more information about this topic.</span></span>

## <a name="better-column-ordering-in-initial-migration"></a><span data-ttu-id="35092-151">Meilleur classement des colonnes dans la migration initiale</span><span class="sxs-lookup"><span data-stu-id="35092-151">Better column ordering in initial migration</span></span>

<span data-ttu-id="35092-152">Après avoir passé en revue les commentaires des clients, nous avons mis à jour les migrations de manière à générer initialement des colonnes pour les tables dans le même ordre que les propriétés déclarées dans les classes.</span><span class="sxs-lookup"><span data-stu-id="35092-152">Based on customer feedback, we have updated migrations to initially generate columns for tables in the same order as properties are declared in classes.</span></span> <span data-ttu-id="35092-153">Notez qu’EF Core ne peut pas changer l’ordre si de nouveaux membres sont ajoutés après la création de la table initiale.</span><span class="sxs-lookup"><span data-stu-id="35092-153">Note that EF Core cannot change order when new members are added after the initial table creation.</span></span>

## <a name="optimization-of-correlated-subqueries"></a><span data-ttu-id="35092-154">Optimisation des sous-requêtes corrélées</span><span class="sxs-lookup"><span data-stu-id="35092-154">Optimization of correlated subqueries</span></span>

<span data-ttu-id="35092-155">Nous avons amélioré la traduction des requêtes pour éviter l’exécution de requêtes SQL « N + 1 » dans de nombreux scénarios courants dans lesquels l’utilisation d’une propriété de navigation dans la projection débouche sur la jointure des données de la requête racine avec les données d’une sous-requête corrélée.</span><span class="sxs-lookup"><span data-stu-id="35092-155">We have improved our query translation to avoid executing "N + 1" SQL queries in many common scenarios in which the usage of a navigation property in the projection leads to joining data from the root query with data from a correlated subquery.</span></span> <span data-ttu-id="35092-156">L’optimisation nécessite la mise en mémoire tampon des résultats à partir de la sous-requête. Nous vous demandons par ailleurs de modifier la requête pour procéder à l’adhésion du nouveau comportement.</span><span class="sxs-lookup"><span data-stu-id="35092-156">The optimization requires buffering the results from the subquery, and we require that you modify the query to opt-in the new behavior.</span></span>

<span data-ttu-id="35092-157">Par exemple, la requête suivante est normalement traduite en une seule requête pour Customers, plus N requêtes séparées pour Orders (où « N » est le nombre de clients retournés) :</span><span class="sxs-lookup"><span data-stu-id="35092-157">As an example, the following query normally gets translated into one query for Customers, plus N (where "N" is the number of customers returned) separate queries for Orders:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

<span data-ttu-id="35092-158">En incluant `ToList()` au bon endroit, vous indiquez que la mise en mémoire tampon est appropriée pour Orders, ce qui déclenche l’optimisation :</span><span class="sxs-lookup"><span data-stu-id="35092-158">By including `ToList()` in the right place, you indicate that buffering is appropriate for the Orders, which enable the optimization:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

<span data-ttu-id="35092-159">Notez que cette requête est traduite en deux requêtes SQL seulement : une pour Customers et la suivante pour Orders.</span><span class="sxs-lookup"><span data-stu-id="35092-159">Note that this query will be translated to only two SQL queries: One for Customers and the next one for Orders.</span></span>

## <a name="owned-attribute"></a><span data-ttu-id="35092-160">Attribut [Owned]</span><span class="sxs-lookup"><span data-stu-id="35092-160">[Owned] attribute</span></span>

<span data-ttu-id="35092-161">Il est désormais possible de configurer des [types d’entités détenus](xref:core/modeling/owned-entities) en annotant simplement le type avec `[Owned]` et en veillant à ce que l’entité propriétaire soit ajoutée au modèle :</span><span class="sxs-lookup"><span data-stu-id="35092-161">It is now possible to configure [owned entity types](xref:core/modeling/owned-entities) by simply annotating the type with `[Owned]` and then making sure the owner entity is added to the model:</span></span>

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

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a><span data-ttu-id="35092-162">Outil en ligne de commande dotnet-ef inclus dans le SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="35092-162">Command-line tool dotnet-ef included in .NET Core SDK</span></span>

<span data-ttu-id="35092-163">Les commandes _ef-dotnet_ font désormais partie du SDK .NET Core. Ainsi, il n’est plus nécessaire d’utiliser DotNetCliToolReference dans le projet pour pouvoir utiliser des migrations ou structurer un DbContext à partir d’une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="35092-163">The _dotnet-ef_ commands are now part of the .NET Core SDK, therefore it will no longer be necessary to use DotNetCliToolReference in the project to be able to use migrations or to scaffold a DbContext from an existing database.</span></span>

<span data-ttu-id="35092-164">Pour plus d’informations sur la façon d’activer les outils en ligne de commande pour différentes versions du SDK .NET Core et EF Core, consultez la section sur [l’installation des outils](xref:core/miscellaneous/cli/dotnet#installing-the-tools).</span><span class="sxs-lookup"><span data-stu-id="35092-164">See the section on [installing the tools](xref:core/miscellaneous/cli/dotnet#installing-the-tools) for more details on how to enable command line tools for different versions of the .NET Core SDK and EF Core.</span></span>

## <a name="microsoftentityframeworkcoreabstractions-package"></a><span data-ttu-id="35092-165">Package Microsoft.EntityFrameworkCore.Abstractions</span><span class="sxs-lookup"><span data-stu-id="35092-165">Microsoft.EntityFrameworkCore.Abstractions package</span></span>

<span data-ttu-id="35092-166">Le nouveau package contient des attributs et des interfaces que vous pouvez utiliser dans vos projets pour alléger les fonctionnalités EF Core sans dépendance envers Core EF dans sa globalité.</span><span class="sxs-lookup"><span data-stu-id="35092-166">The new package contains attributes and interfaces that you can use in your projects to light up EF Core features without taking a dependency on EF Core as a whole.</span></span> <span data-ttu-id="35092-167">Par exemple, l’attribut [Owned] et l’interface ILazyLoader se trouvent ici.</span><span class="sxs-lookup"><span data-stu-id="35092-167">For example, the [Owned] attribute and the ILazyLoader interface are located here.</span></span>

## <a name="state-change-events"></a><span data-ttu-id="35092-168">Événements de changement d’état</span><span class="sxs-lookup"><span data-stu-id="35092-168">State change events</span></span>

<span data-ttu-id="35092-169">Les nouveaux événements `Tracked` et `StateChanged` sur `ChangeTracker` peuvent être utilisés pour écrire une logique qui réagit aux entités qui entrent dans DbContext ou modifiant leur état.</span><span class="sxs-lookup"><span data-stu-id="35092-169">New `Tracked` And `StateChanged` events on `ChangeTracker` can be used to write logic that reacts to entities entering the DbContext or changing their state.</span></span>

## <a name="raw-sql-parameter-analyzer"></a><span data-ttu-id="35092-170">Analyseur de paramètre SQL brut</span><span class="sxs-lookup"><span data-stu-id="35092-170">Raw SQL parameter analyzer</span></span>

<span data-ttu-id="35092-171">Un nouvel analyseur de code est inclus avec EF Core. Il détecte les utilisations potentiellement dangereuses de nos API SQL brutes, comme `FromSql` ou `ExecuteSqlCommand`.</span><span class="sxs-lookup"><span data-stu-id="35092-171">A new code analyzer is included with EF Core that detects potentially unsafe usages of our raw-SQL APIs, like `FromSql` or `ExecuteSqlCommand`.</span></span> <span data-ttu-id="35092-172">Par exemple, dans la requête suivante, un avertissement s’affiche, car _minAge_ n’est pas paramétrable :</span><span class="sxs-lookup"><span data-stu-id="35092-172">For example, for the following query, you will see a warning because _minAge_ is not parameterized:</span></span>

``` csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a><span data-ttu-id="35092-173">Compatibilité des fournisseurs de base de données</span><span class="sxs-lookup"><span data-stu-id="35092-173">Database provider compatibility</span></span>

<span data-ttu-id="35092-174">Nous vous recommandons d’utiliser EF Core 2.1 avec des fournisseurs qui ont été mis à jour ou au moins testés pour fonctionner avec EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="35092-174">It is recommended that you use EF Core 2.1 with providers that have been updated or at least tested to work with EF Core 2.1.</span></span>

> [!TIP]
> <span data-ttu-id="35092-175">Si vous vous heurtez à des incompatibilités inattendues ou à d’autres problèmes liés aux nouvelles fonctionnalités, ou si vous avez des commentaires à propos de ces nouveautés, utilisez [notre système de suivi des problèmes](https://github.com/aspnet/EntityFrameworkCore/issues/new) pour nous en faire part.</span><span class="sxs-lookup"><span data-stu-id="35092-175">If you find any unexpected incompatibility or any issue in the new features, or if you have feedback on them, please report it using [our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues/new).</span></span>
