---
title: Nouveautés d’EF Core 2.2 - EF Core
description: Modifications et améliorations dans Entity Framework Core 2,2
author: divega
ms.date: 11/14/2018
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: 68e3cbd5c7345330a47f1457c9b096fee5dd49e9
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072327"
---
# <a name="new-features-in-ef-core-22"></a>Nouvelles fonctionnalités d’EF Core 2.2

## <a name="spatial-data-support"></a>Prise en charge des données spatiales

Les données spatiales peuvent être utilisées pour représenter l’emplacement physique et la forme d’objets.
Plusieurs bases de données peuvent nativement stocker, indexer et interroger des données spatiales.
Les scénarios courants incluent l’interrogation d’objets dans un rayon donné et le test pour vérifier si un polygone contient un emplacement donné.
EF Core 2.2 prend désormais en charge l’utilisation de données spatiales provenant de diverses bases de données à l’aide de types issus de la bibliothèque [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS).

La prise en charge des données spatiales est implémenté sous forme d’une série de packages d’extension spécifiques à un fournisseur.
Chacun de ces packages contribue aux mappages des types NTS et des méthodes ainsi que des types spatiaux et fonctions correspondants dans la base de données.
Ces extensions de fournisseur sont désormais disponibles pour [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/), et [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (à partir du [projet Npgsql](https://www.npgsql.org/)).
Les types spatiaux peuvent être utilisés directement avec le [fournisseur en mémoire EF Core](xref:core/providers/in-memory/index), sans extensions supplémentaires.

Une fois l’extension du fournisseur installée, vous pouvez ajouter à vos entités des propriétés de types pris en charge. Exemple :

``` csharp
using NetTopologySuite.Geometries;

namespace MyApp
{
  public class Friend
  {
    [Key]
    public string Name { get; set; }
  
    [Required]
    public Point Location { get; set; }
  }
}
```

Vous pouvez alors conserver les entités avec des données spatiales :

``` csharp
using (var context = new MyDbContext())
{
    context.Add(
        new Friend
        {
            Name = "Bill",
            Location = new Point(-122.34877, 47.6233355) {SRID = 4326 }
        });
    context.SaveChanges();
}
```

Et vous pouvez exécuter des requêtes de base de données basées sur des données spatiales et des opérations :

``` csharp
  var nearestFriends =
      (from f in context.Friends
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Pour plus d’informations sur cette fonctionnalité, consultez la [documentation sur les types spatiaux](xref:core/modeling/spatial).

## <a name="collections-of-owned-entities"></a>Collections d’entités détenues

EF Core 2.0 a ajouté la possibilité de modéliser la propriété dans les associations un-à-un.
EF Core 2.2 étend la capacité d’exprimer la propriété aux associations un-à-plusieurs.
La propriété permet de limiter la façon dont les entités sont utilisées.

Par exemple, les entités détenues :

- Peuvent apparaître uniquement sur les propriétés de navigation d’autres types d’entités.
- Sont automatiquement chargées et ne peut être uniquement suivies par un objet DbContext en même temps que leur propriétaire.

Dans les bases de données relationnelles, les collections détenues sont mappées à des tables distinctes du propriétaire, comme des associations un-à-plusieurs standard.
Mais dans les bases de données orientées document, nous prévoyons d’imbriquer les entités détenues (dans des collections ou références détenues) dans le même document que le propriétaire.

Vous pouvez utiliser cette fonctionnalité en appelant la nouvelle API OwnsMany() :

``` csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

Pour plus d’informations, consultez la [documentation mise à jour sur les entités détenues](xref:core/modeling/owned-entities#collections-of-owned-types).

## <a name="query-tags"></a>Balises de requête

Cette fonctionnalité simplifie la corrélation des requêtes LINQ dans le code avec des requêtes SQL générées et capturées dans des journaux.

Pour tirer parti des balises de requête, vous annotez une requête LINQ à l’aide de la nouvelle méthode TagWith().
En utilisant la requête spatiale d’un exemple précédent :

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith(@"This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Cette requête LINQ générera la sortie SQL suivante :

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

Pour plus d’informations, consultez la [documentation sur les balises de requête](xref:core/querying/tags).
