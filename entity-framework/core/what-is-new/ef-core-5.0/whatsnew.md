---
title: Nouveautés de EF Core 5,0
description: Vue d’ensemble des nouvelles fonctionnalités de EF Core 5,0
author: ajcvickers
ms.date: 03/30/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: c902988920e3b1a6039808fe0658fc19dee2728a
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103072"
---
# <a name="whats-new-in-ef-core-50"></a>Nouveautés de EF Core 5,0

EF Core 5,0 est actuellement en cours de développement.
Cette page contient une vue d’ensemble des changements intéressants introduits dans chaque version préliminaire.

Cette page ne duplique pas le [plan pour EF Core 5,0](plan.md).
Le plan décrit les thèmes globaux pour EF Core 5,0, y compris tout ce que nous envisageons d’inclure avant d’expédier la version finale.

Nous ajouterons des liens à partir d’ici à la documentation officielle au fur et à mesure de sa publication.

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>Include filtré

La méthode Include prend désormais en charge le filtrage des entités incluses.
Par exemple :

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Cette requête renverra les blogs avec chaque publication associée, mais uniquement lorsque le titre de publication contient « fromage ».

Skip et Take peuvent également être utilisés pour réduire le nombre d’entités incluses.
Par exemple :
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
Cette requête renverra les blogs contenant au maximum cinq publications, incluses sur chaque blog.

Pour plus d’informations, consultez la [documentation include](xref:core/querying/related-data#filtered-include) .

### <a name="new-modelbuilder-api-for-navigation-properties"></a>Nouvelle API ModelBuilder pour les propriétés de navigation

Les propriétés de navigation sont principalement configurées lors de la [définition de relations](xref:core/modeling/relationships).
Toutefois, la nouvelle `Navigation` méthode peut être utilisée dans les cas où les propriétés de navigation nécessitent une configuration supplémentaire.
Par exemple, pour définir un champ de stockage pour la navigation lorsque le champ est introuvable par Convention :

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

Notez que l' `Navigation` API ne remplace pas la configuration de la relation.
Au lieu de cela, il permet une configuration supplémentaire des propriétés de navigation dans les relations déjà découvertes ou définies.

La documentation est suivie d’un problème [#2302](https://github.com/dotnet/EntityFramework.Docs/issues/2302).

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>Nouveaux paramètres de ligne de commande pour les espaces de noms et les chaînes de connexion 

Les migrations et la génération de modèles automatique permettent désormais de spécifier des espaces de noms sur la ligne de commande.
Par exemple, pour rétroconcevoir une base de données en plaçant le contexte et les classes de modèle dans différents espaces de noms : 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

En outre, une chaîne de connexion peut désormais être passée `database-update` à la commande :

```
dotnet ef database update --connection "connection string"
```

Des paramètres équivalents ont également été ajoutés aux commandes PowerShell utilisées dans la console du gestionnaire de package VS.

La documentation est suivie d’un problème [#2303](https://github.com/dotnet/EntityFramework.Docs/issues/2303).

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors a retourné

Pour des raisons de performances, EF n’effectue pas de vérifications null supplémentaires lors de la lecture des valeurs de la base de données.
Cela peut entraîner des exceptions qui sont difficiles à la cause première lorsqu’une valeur Null inattendue est rencontrée.

L' `EnableDetailedErrors` utilisation de permet d’ajouter une vérification de valeur null supplémentaire aux requêtes de sorte que, pour une faible surcharge de performances, ces erreurs sont plus faciles à suivre à une cause racine.  

Par exemple :
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

La documentation est suivie d’un problème [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).

### <a name="cosmos-partition-keys"></a>Clés de partition Cosmos

La clé de partition à utiliser pour une requête donnée peut désormais être spécifiée dans la requête.
Par exemple :

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

La documentation est suivie d’un problème [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).

### <a name="support-for-the-sql-server-datalength-function"></a>Prise en charge de la fonction DATALENGTH SQL Server

Vous pouvez y accéder à l’aide `EF.Functions.DataLength` de la nouvelle méthode.
Par exemple :
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a>Préversion 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>Utiliser un attribut C# pour spécifier un champ de stockage de propriété

Un attribut C# peut maintenant être utilisé pour spécifier le champ de stockage d’une propriété.
Cet attribut permet à EF Core d’écrire et de lire toujours dans le champ de stockage, ce qui se produit normalement, même si le champ de stockage ne peut pas être trouvé automatiquement.
Par exemple :

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

La documentation est suivie d’un problème [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).

### <a name="complete-discriminator-mapping"></a>Terminer le mappage de discriminateur

EF Core utilise une colonne de discriminateur pour le [mappage TPH d’une hiérarchie d’héritage](/ef/core/modeling/inheritance).
Certaines améliorations des performances sont possibles tant que EF Core connaît toutes les valeurs possibles pour le discriminateur.
EF Core 5,0 implémente désormais ces améliorations.

Par exemple, les versions précédentes de EF Core génèrent toujours ce SQL pour une requête qui retourne tous les types dans une hiérarchie :

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

EF Core 5,0 génère désormais les éléments suivants lorsqu’un mappage de discriminateur complet est configuré :

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

Il s’agit du comportement par défaut à partir de Preview 3.

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Améliorations des performances dans Microsoft. Data. sqlite

Nous avons apporté deux améliorations des performances pour SQLIte :

* La récupération de données binaires et de chaîne avec GetBytes, GetChars et GetTextReader est désormais plus efficace grâce à l’utilisation de SqliteBlob et de flux.
* L’initialisation de SqliteConnection est désormais différée.

Ces améliorations se trouvent dans le fournisseur Microsoft. Data. sqlite de ADO.NET et améliorent donc également les performances en dehors de EF Core.

## <a name="preview-1"></a>Preview 1

### <a name="simple-logging"></a>Journalisation simple

Cette fonctionnalité ajoute des fonctionnalités similaires `Database.Log` à dans EF6.
Autrement dit, il offre un moyen simple d’obtenir des journaux à partir de EF Core sans avoir besoin de configurer tout type d’infrastructure de journalisation externe.

La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

Une documentation supplémentaire est suivie par le [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)du problème.

### <a name="simple-way-to-get-generated-sql"></a>Moyen simple d’utiliser le SQL généré

EF Core 5,0 introduit la `ToQueryString` méthode d’extension, qui retourne le SQL que EF Core générera lors de l’exécution d’une requête LINQ.

La documentation préliminaire est incluse dans l' [État EF Weekly pour le 9 janvier 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).

Une documentation supplémentaire est suivie par le [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)du problème.

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>Utiliser un attribut C# pour indiquer qu’une entité n’a pas de clé

Un type d’entité peut désormais être configuré comme n’ayant aucune clé à `KeylessAttribute`l’aide de la nouvelle.
Par exemple :

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

La documentation est suivie d’un problème [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>La connexion ou la chaîne de connexion peut être modifiée lors de l’DbContext initialisé

Il est désormais plus facile de créer une instance DbContext sans connexion ou chaîne de connexion.
En outre, la connexion ou la chaîne de connexion peut désormais être mutée sur l’instance de contexte.
Cette fonctionnalité permet à la même instance de contexte de se connecter de manière dynamique à différentes bases de données.

La documentation est suivie d’un problème [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).

### <a name="change-tracking-proxies"></a>Proxys de suivi des modifications

EF Core pouvez désormais générer des proxies d’exécution qui implémentent automatiquement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) et [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).
Celles-ci rerapportent ensuite les modifications de valeur sur les propriétés d’entité directement à EF Core, évitant ainsi la nécessité d’analyser les modifications.
Toutefois, les proxies sont fournis avec leur propre ensemble de limitations, donc ils ne le sont pas pour tout le monde.

La documentation est suivie d’un problème [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).

### <a name="enhanced-debug-views"></a>Vues de débogage améliorées

Les vues de débogage sont un moyen simple de consulter les éléments internes d’EF Core lors du débogage des problèmes.
Une vue de débogage a été implémentée pour le modèle il y a quelque temps.
Pour EF Core 5,0, nous avons facilité la lecture et l’ajout d’une nouvelle vue de débogage pour les entités suivies dans le gestionnaire d’État.

La documentation préliminaire est incluse dans l' [État EF Weekly pour le 12 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).

Une documentation supplémentaire est suivie par le [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)du problème.

### <a name="improved-handling-of-database-null-semantics"></a>Amélioration de la gestion de la sémantique null de la base de données

En général, les bases de données relationnelles traitent la valeur NULL comme une valeur inconnue et, par conséquent, n’est pas égale à une autre valeur NULL.
Alors que C# traite NULL comme une valeur définie, qui compare la valeur à toute autre valeur null.
EF Core par défaut traduit les requêtes afin qu’elles utilisent la sémantique null C#.
EF Core 5,0 améliore considérablement l’efficacité de ces traductions.

La documentation est suivie d’un problème [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).

### <a name="indexer-properties"></a>Propriétés de l’indexeur

EF Core 5,0 prend en charge le mappage des propriétés de l’indexeur C#.
Ces propriétés permettent aux entités d’agir en tant que conteneurs de propriétés où les colonnes sont mappées à des propriétés nommées dans le conteneur.

La documentation est suivie d’un problème [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).

### <a name="generation-of-check-constraints-for-enum-mappings"></a>Génération de contraintes de validation pour les mappages d’énumération

EF Core migrations 5,0 peut désormais générer des contraintes de validation pour les mappages de propriété d’énumération.
Par exemple :

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

La documentation est suivie d’un problème [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).

### <a name="isrelational"></a>IsRelational

Une nouvelle `IsRelational` méthode a été ajoutée en plus des, `IsSqlServer` `IsSqlite`, et `IsInMemory`existants.
Cette méthode peut être utilisée pour tester si DbContext utilise un fournisseur de base de données relationnelle.
Par exemple :

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

La documentation est suivie d’un problème [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).

### <a name="cosmos-optimistic-concurrency-with-etags"></a>Accès concurrentiel optimiste Cosmos avec ETags

Le fournisseur de base de données Azure Cosmos DB prend désormais en charge l’accès concurrentiel optimiste à l’aide d’ETags.
Utilisez le générateur de modèles de OnModelCreating pour configurer un ETag :

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

SaveChanges lèvera ensuite un `DbUpdateConcurrencyException` en cas de conflit d’accès concurrentiel, qui [peut être géré](https://docs.microsoft.com/ef/core/saving/concurrency) pour implémenter les nouvelles tentatives, etc.

La documentation est suivie d’un problème [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).

### <a name="query-translations-for-more-datetime-constructs"></a>Traductions de requêtes pour d’autres constructions DateTime

Les requêtes contenant la nouvelle construction DateTime sont maintenant traduites.

En outre, les fonctions de SQL Server suivantes sont maintenant mappées :

* DateDiffWeek
* DateFromParts

Par exemple :

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translations-for-more-byte-array-constructs"></a>Traductions de requêtes pour d’autres constructions de tableau d’octets

Les requêtes utilisant les propriétés Contains, Length, SequenceEqual, etc. sur Byte [] sont désormais traduites en SQL.

La documentation préliminaire est incluse dans l' [État EF hebdomadaire du 5 décembre 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

Une documentation supplémentaire est suivie par le [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)du problème.

### <a name="query-translation-for-reverse"></a>Traduction de requête pour l’inverse

Les requêtes `Reverse` utilisant sont désormais traduites.
Par exemple :

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-bitwise-operators"></a>Traduction des requêtes pour les opérateurs au niveau du bit

Les requêtes utilisant des opérateurs de bits sont maintenant traduites dans d’autres cas, par exemple :

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-strings-on-cosmos"></a>Traduction des requêtes pour les chaînes sur Cosmos

Les requêtes qui utilisent les méthodes de chaîne Contains, StartsWith et EndsWith sont désormais traduites lors de l’utilisation du fournisseur Azure Cosmos DB.

La documentation est suivie d’un problème [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).
