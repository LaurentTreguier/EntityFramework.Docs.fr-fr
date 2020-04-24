---
title: Nouvelles fonctionnalités d’Entity Framework Core 3.0 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: 39684cdcc17e3baa4b77cf29d54b626294771332
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103137"
---
# <a name="new-features-in-entity-framework-core-30"></a>Nouvelles fonctionnalités d’Entity Framework Core 3.0

La liste suivante présente les nouvelles fonctionnalités majeures d’EF Core 3.0.

En tant que version majeure, EF Core 3.0 contient également plusieurs [changements cassants](xref:core/what-is-new/ef-core-3.0/breaking-changes), qui sont des améliorations d’API pouvant avoir un impact négatif sur les applications existantes.  

## <a name="linq-overhaul"></a>Révision de LINQ

LINQ permet d’écrire des requêtes de base de données dans le langage .NET de prédilection, en tirant parti de riches informations de type pour proposer IntelliSense et le contrôle de type au moment de la compilation.
Toutefois, LINQ vous permet également d’écrire un nombre illimité de requêtes complexes contenant des expressions arbitraires (appels de méthode ou opérations).
La gestion de toutes ces combinaisons est le principal défi des fournisseurs LINQ.

Dans EF Core 3,0, nous avons remanié notre fournisseur LINQ pour permettre la traduction d’un plus grand nombre de modèles de requête en SQL, en générant des requêtes efficaces dans des cas plus nombreux et en empêchant les requêtes inefficaces de passer inaperçues. Le nouveau fournisseur LINQ est la fondation qui nous permettra d’offrir de nouvelles capacités de requête et des améliorations de performances dans les versions futures, sans arrêter les applications et les fournisseurs de données existants.

### <a name="restricted-client-evaluation"></a>Évaluation restreinte du client

Le changement de conception le plus important porte sur la façon dont nous manipulons les expressions LINQ qui ne peuvent pas être converties en paramètres ou traduites en SQL.

Dans les versions précédentes, EF Core identifiait les parties d’une requête pouvant être traduites en SQL et exécutait le reste de la requête sur le client.
Ce type d’exécution côté client est souhaitable dans certains cas, mais dans de nombreux autres cas, il peut entraîner des requêtes inefficaces.

Par exemple, si EF Core 2.2 ne pouvait pas traduire un prédicat en appel `Where()`, il exécutait une instruction SQL sans filtre, transférait toutes les lignes depuis la base de données, puis les filtrait dans la mémoire :

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

Cela peut être acceptable si la base de données contient un petit nombre de lignes, mais peut entraîner des problèmes de performances significatifs, voire une défaillance de l’application si la base de données contient un grand nombre de lignes.

Dans EF Core 3.0, nous avons restreint l’évaluation du client à la seule projection au niveau supérieur (essentiellement le dernier appel à `Select()`).
Lorsqu’EF Core 3.0 détecte des expressions qui ne peuvent pas être traduites ailleurs dans la requête, une exception runtime est levée.

Pour évaluer une condition de prédicat sur le client comme dans l’exemple précédent, les développeurs ont désormais besoin de basculer explicitement l’évaluation de la requête LINQ to Objects :

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n))
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

Consultez la [documentation des changements cassants](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) pour en savoir plus sur la façon dont cela peut affecter des applications existantes.

### <a name="single-sql-statement-per-linq-query"></a>Une seule instruction SQL par requête LINQ

Un autre aspect de la conception qui a beaucoup changé dans la version 3.0 est que nous créons maintenant toujours une seule instruction SQL par requête LINQ. Dans les versions précédentes, nous avions l’habitude de créer plusieurs instructions SQL dans certains cas, à savoir des appels `Include()` traduits sur les propriétés de navigation de collection et des requêtes traduites qui suivaient certains modèles avec des sous-requêtes. Même si c’était pratique dans certains cas, et que, pour `Include()` cela aidait même à éviter d’envoyer des données redondantes sur le réseau, l’implémentation était complexe et des comportements extrêmement inefficaces en résultaient (requêtes N+1). Dans certaines situations, les données retournées sur plusieurs requêtes étaient potentiellement incohérentes.

Similairement à l’évaluation du client, si EF Core 3.0 ne peut pas traduire une requête LINQ en instruction SQL unique, une exception runtime est levée. Cependant, nous avons rendu EF Core capable de traduire beaucoup des modèles courants utilisés pour générer plusieurs requêtes relatives à une requête unique avec des jointures.

## <a name="cosmos-db-support"></a>Prise en charge de Cosmos DB

Le fournisseur Cosmos DB pour EF Core permet aux développeurs qui maîtrisent le modèle de programmation d’EF de cibler facilement Azure Cosmos DB comme base de données d'application. L’objectif est de rendre certains des avantages de Cosmos DB (par exemple la distribution mondiale, la disponibilité « Always On », la scalabilité élastique et la faible latence) encore plus accessibles aux développeurs .NET. Le fournisseur propose la plupart des fonctionnalités d’EF Core, comme le suivi automatique des modifications, les conversions de valeurs et LINQ, sur l’API SQL dans Cosmos DB.

Consultez la [documentation fournisseur Cosmos DB](xref:core/providers/cosmos/index) pour plus de détails.

## <a name="c-80-support"></a>Prise en charge de C# 8.0

EF Core 3.0 bénéficie de quelques-unes des [nouvelles fonctionnalités de C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8) :

### <a name="asynchronous-streams"></a>Flux asynchrones

Les résultats des requêtes asynchrones sont maintenant exposés à l’aide de la nouvelle interface `IAsyncEnumerable<T>` standard et peuvent être consommés avec `await foreach`.

``` csharp
var orders =
    from o in context.Orders
    where o.Status == OrderStatus.Pending
    select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
    Process(o);
}
```

Consultez la rubrique [Flux asynchrones dans la documentation C#](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) pour plus de détails.

### <a name="nullable-reference-types"></a>Types références Nullables

Lorsque cette nouvelle fonctionnalité est activée dans votre code, EF Core examine la possibilité de valeur Null des propriétés de type référence et l’applique aux colonnes et aux relations correspondantes dans la base de données : les propriétés de types de références sans possibilité de valeur Null sont traitées comme si elles avaient l’attribut d’annotation de données `[Required]`.

Par exemple, dans la classe suivante, les propriétés marquées comme étant de type `string?` sont configurées comme facultatives, tandis que `string` est configuré comme obligatoire :

``` csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string? MiddleName { get; set; }
}
```

Consultez [Utilisation de types références avec possibilité de valeur Null](xref:core/miscellaneous/nullable-reference-types) dans la documentation EF Core pour plus de détails.

## <a name="interception-of-database-operations"></a>Interception d’opérations de base de données

La nouvelle API d’interception dans EF Core 3.0 permet de fournir une logique personnalisée à appeler automatiquement à chaque fois que des opérations de base de données de bas niveau sont effectuées dans le cadre du fonctionnement normal d’EF Core. Par exemple, lors de l’ouverture de connexions, de la validation de transactions ou de l’exécution de commandes.

Similairement aux fonctionnalités d’interception qui existaient dans EF 6, les intercepteurs vous permettent d’intercepter des opérations avant ou après leur exécution. Lorsque vous les interceptez avant leur exécution, vous êtes autorisé à contourner l’exécution et à fournir d’autres résultats provenant de la logique d’interception.

Par exemple, pour manipuler un texte de commande, vous pouvez créer un `DbCommandInterceptor` :

``` csharp
public class HintCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        // Manipulate the command text, etc. here...
        command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
        return result;
    }
}
```

Et l’enregistrer avec votre `DbContext` :

``` csharp
services.AddDbContext(b => b
    .UseSqlServer(connectionString)
    .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a>Ingénierie à rebours des vues de base de données

Les types de requêtes qui représentent des données pouvant être lues à partir de la base de données, mais ne pouvant pas être mises à jour, ont été renommés [types d’entités sans clé](xref:core/modeling/keyless-entity-types).
Comme ils sont très bien adaptés au mappage d’affichages de bases de données dans la plupart des scénarios, EF Core crée désormais automatiquement des types d’entités sans clé lors de l’ingénierie à rebours d’affichages de base de données.

Par exemple, avec l’[outil dotnet ef command-line](xref:core/miscellaneous/cli/dotnet), vous pouvez saisir :

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

L’outil génère désormais automatiquement des types de vues et de tables sans clés :

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Names>(entity =>
    {
        entity.HasNoKey();
        entity.ToView("Names");
    });

    modelBuilder.Entity<Things>(entity =>
    {
        entity.HasNoKey();
    });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>Les entités dépendantes qui partagent la table avec le principal sont maintenant facultatives

À partir d’EF Core 3.0, si `OrderDetails` est détenu par `Order` ou explicitement mappé à la même table, il est possible d’ajouter un `Order` sans `OrderDetails` et toutes les propriétés `OrderDetails` à l’exception de la clé primaire sont mappées à des colonnes de type nullable.

Pendant l’interrogation, EF Core définit `OrderDetails` sur `null` si une de ses propriétés obligatoires n’a pas de valeur, ou s’il n’a pas de propriété obligatoire en plus de la clé primaire et que toutes les propriétés sont `null`.

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a>EF 6.3 sur .NET Core

Ce n’est pas véritablement une fonctionnalité d’EF Core 3.0, mais nous pensons qu’elle est importante pour beaucoup de vos clients actuels.

Nous sommes conscients du fait que de nombreuses applications utilisent d’anciennes versions d’EF et qu’une migration vers EF Core dans l’unique objectif de tirer parti de .NET Core représente un effort considérable.
C’est pourquoi nous avons décidé de déplacer la version la plus récente d’EF 6 de façon à ce qu’elle s’exécute sur .NET Core 3.0.

Pour plus d'informations, consultez les [nouveautés de la base de données dans EF 6](xref:ef6/what-is-new/index).

## <a name="postponed-features"></a>Fonctionnalités différées

Certaines fonctionnalités prévues à l’origine pour EF Core 3.0 ont été différées à des versions futures :

- Capacité d’ignorer des parties d’un modèle lors de migrations, référence de suivi [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).
- Entités des conteneurs de propriétés, suivies comme deux problèmes distincts : [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) pour les entités de type partagé et [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) pour le support du mappage de propriété.
