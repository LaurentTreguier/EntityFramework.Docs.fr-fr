---
title: Données spatiales-EF Core
description: Utilisation de données spatiales dans un modèle de Entity Framework Core
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: 721aa2628d17b89b79160f8f658f8ef0dd78d6a6
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543287"
---
# <a name="spatial-data"></a>Données spatiales

> [!NOTE]
> Cette fonctionnalité a été introduite dans EF Core 2,2.

Les données spatiales représentent l’emplacement physique et la forme des objets. De nombreuses bases de données prennent en charge ce type de données afin qu’elles puissent être indexées et interrogées avec d’autres données. Les scénarios courants incluent l’interrogation d’objets situés à une distance donnée à partir d’un emplacement ou la sélection de l’objet dont la bordure contient un emplacement donné. EF Core prend en charge le mappage aux types de données spatiales à l’aide de la bibliothèque spatiale NetTopologySuite.

## <a name="installing"></a>Installing

Pour pouvoir utiliser les données spatiales avec EF Core, vous devez installer le package NuGet de prise en charge approprié. Le package que vous devez installer dépend du fournisseur que vous utilisez.

Fournisseur EF Core                        | Package NuGet spatial
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
Pomelo.EntityFrameworkCore.MySql        | [Pomelo. EntityFrameworkCore. MySql. NetTopologySuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
Devart.Data.MySql.EFCore                | [Devart. Data. MySql. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
Devart.Data.PostgreSql.EFCore           | [Devart. Data. PostgreSql. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
Devart.Data.SQLite.EFCore               | [Devart. Data. SQLite. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
Teradata.EntityFrameworkCore            | [Teradata. EntityFrameworkCore. NetTopologySuite](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a>NetTopologySuite

[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) est une bibliothèque spatiale pour .net. EF Core permet le mappage aux types de données spatiales dans la base de données à l’aide des types NTS dans votre modèle.

Pour activer le mappage aux types spatiaux via NTS, appelez la méthode UseNetTopologySuite sur le générateur d’options DbContext du fournisseur. Par exemple, avec SQL Server vous l’appellerez comme ceci.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

Il existe plusieurs types de données spatiales. Le type que vous utilisez dépend des types de formes que vous souhaitez autoriser. Voici la hiérarchie des types NTS que vous pouvez utiliser pour les propriétés de votre modèle. Ils sont situés dans l' `NetTopologySuite.Geometries` espace de noms.

* Géométrie
  * Point
  * LineString
  * Polygone
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> CircularString, CompoundCurve et CurePolygon ne sont pas pris en charge par NTS.

L’utilisation du type Geometry de base permet à n’importe quel type de forme d’être spécifié par la propriété.

## <a name="longitude-and-latitude"></a>Longitude et Latitude

Les coordonnées en NTS sont exprimées en valeurs X et Y. Pour représenter la longitude et la latitude, utilisez X pour la longitude et Y pour la latitude. Notez que cette valeur est **retournée** à partir du `latitude, longitude` format dans lequel vous voyez généralement ces valeurs.

## <a name="querying-data"></a>Interrogation des données

Les classes d’entité suivantes peuvent être utilisées pour mapper à des tables dans l' [exemple de base de données grand World](https://go.microsoft.com/fwlink/?LinkID=800630)Importers.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

Dans LINQ, les méthodes et propriétés NTS disponibles en tant que fonctions de base de données sont traduites en SQL. Par exemple, les méthodes distance et Contains sont traduites dans les requêtes suivantes. Consultez la documentation de votre fournisseur pour connaître les méthodes prises en charge.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a>Rétroconception

Les packages NuGet spatiaux activent également les modèles d' [ingénierie à rebours](xref:core/managing-schemas/scaffolding) avec des propriétés spatiales, mais vous devez installer le package ***avant*** d’exécuter `Scaffold-DbContext` ou `dotnet ef dbcontext scaffold` . Si vous ne le souhaitez pas, vous recevrez des avertissements sur les mappages de type pour les colonnes et les colonnes seront ignorées.

## <a name="srid-ignored-during-client-operations"></a>SRID ignoré pendant les opérations du client

NTS ignore les valeurs SRID lors des opérations. Il part du principe qu’il s’agit d’un système de coordonnées planaire. Cela signifie que si vous spécifiez des coordonnées en termes de longitude et de latitude, certaines valeurs évaluées par le client, telles que la distance, la longueur et la zone, seront en degrés, et non en mètres. Pour les valeurs plus significatives, vous devez d’abord projeter les coordonnées dans un autre système de coordonnées à l’aide d’une bibliothèque comme [ProjNet (pour GeoAPI)](https://github.com/NetTopologySuite/ProjNet4GeoAPI).

> [!NOTE]
> Utilisez le [package NuGet ProjNet](https://www.nuget.org/packages/ProjNet/)plus récent, **pas** le package plus ancien appelé ProjNet4GeoAPI.

Si une opération est évaluée par le serveur par EF Core via SQL, l’unité du résultat est déterminée par la base de données.

Voici un exemple d’utilisation de ProjNet pour calculer la distance entre deux villes.

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> 4326 fait référence à WGS 84, une norme utilisée dans le GPS et d’autres systèmes géographiques.

## <a name="additional-resources"></a>Ressources supplémentaires

### <a name="database-specific-information"></a>Informations spécifiques à la base de données

Veillez à lire la documentation de votre fournisseur pour plus d’informations sur l’utilisation des données spatiales.

* [Données spatiales dans le fournisseur SQL Server](xref:core/providers/sql-server/spatial)
* [Données spatiales dans le fournisseur SQLite](xref:core/providers/sqlite/spatial)
* [Données spatiales dans le fournisseur npgsql](https://www.npgsql.org/efcore/mapping/nts.html)

### <a name="other-resources"></a>Autres ressources

* [Documentation NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/)
* [EF Core session réunions](https://www.youtube.com/watch?v=IHslY5rrxD0&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=15)de la Communauté, en se concentrant sur les données spatiales et les NetTopologySuite.
