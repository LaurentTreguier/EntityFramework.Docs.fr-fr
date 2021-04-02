---
title: Fournisseur de base de données SQLite-données spatiales-EF Core
description: Utilisation de données spatiales avec le fournisseur de base de données Entity Framework Core SQLite
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sqlite/spatial
ms.openlocfilehash: aaf0dcd7dc4b670d56ac99567e2e129313427e40
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106165942"
---
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a>Données spatiales dans le fournisseur de EF Core SQLite

Cette page contient des informations supplémentaires sur l’utilisation de données spatiales avec le fournisseur de base de données SQLite. Pour obtenir des informations générales sur l’utilisation des données spatiales dans EF Core, consultez la documentation principale sur les [données spatiales](xref:core/modeling/spatial) .

## <a name="installing-spatialite"></a>Installation de SpatiaLite

Sur Windows, la bibliothèque de mod_spatialite native est distribuée en tant que dépendance de package NuGet. D’autres plateformes doivent l’installer séparément. Cette opération s’effectue généralement à l’aide d’un gestionnaire de package logiciel. Par exemple, vous pouvez utiliser APT sur Debian et Ubuntu. et homebrew sur MacOS.

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

Malheureusement, les versions plus récentes de PROJ (une dépendance de SpatiaLite) ne sont pas compatibles avec le [Bundle SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)par défaut d’EF. Vous pouvez contourner ce cas à l’aide de la bibliothèque SQLite système.

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

Sur **MacOS**, vous devez également définir une variable d’environnement avant d’exécuter votre application afin qu’elle utilise la version de homebrew de sqlite. Dans Visual Studio pour Mac, vous pouvez définir cette **option sous project > Project Options > exécuter les configurations de > > par défaut**

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a>Configuration de SRID

Dans SpatiaLite, les colonnes doivent spécifier un SRID par colonne. La valeur par défaut de SRID est `0` . Spécifiez un autre SRID à l’aide de la méthode HasSrid.

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasSrid(4326);
```

> [!NOTE]
> 4326 fait référence à WGS 84, une norme utilisée dans le GPS et d’autres systèmes géographiques.

## <a name="dimension"></a>Dimension

La dimension par défaut (ou les ordonnées) d’une colonne est X et Y. Pour activer des ordonnées supplémentaires comme Z ou M, configurez le type de colonne.

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a>Mappages de fonctions spatiales

Ce tableau montre les membres [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) traduits dans les fonctions SQL.

.NET                                        | SQL
------------------------------------------- | ---
geometr. Partie                               | Area ( @geometry )
geometr. AsBinary()                         | AsBinary ( @geometry )
geometr. AsText()                           | AsText ( @geometry )
geometr. Bordure                           | Limite ( @geometry )
geometr. Mémoire tampon (distance)                   | Mémoire tampon ( @geometry , @distance )
geometr. Buffer (distance, quadrantSegments) | Mémoire tampon ( @geometry , @distance , @quadrantSegments )
geometr. Centroïde                           | Centre de gravité ( @geometry )
geometr. Contient (g)                        | Contient ( @geometry , @g )
geometr. ConvexHull()                       | ConvexHull ( @geometry )
geometr. CoveredBy (g)                       | CoveredBy ( @geometry , @g )
geometr. Couvertures (g)                          | Couvertures ( @geometry , @g )
geometr. Croisements (g)                         | Croisements ( @geometry , @g )
geometr. Différence (autre)                  | Différence ( @geometry , @other )
geometr. Codes                          | Dimension ( @geometry )
geometr. Disjoint (g)                        | Disjoint ( @geometry , @g )
geometr. Distance (g)                        | Distance ( @geometry , @g )
geometr. Envelope                           | Enveloppe ( @geometry )
geometr. EqualsTopologically (g)             | Est égal à ( @geometry , @g )
geometr. GeometryType                       | GeometryType ( @geometry )
geometr. GetGeometryN (n)                    | Geometry ( @geometry , @n + 1)
geometr. InteriorPoint                      | PointOnSurface ( @geometry )
geometr. Intersection (autre)                | Intersection ( @geometry , @other )
geometr. Croisements (g)                      | Croisements ( @geometry , @g )
geometr. IsEmpty                            | IsEmpty ( @geometry )
geometr. IsSimple                           | IsSimple ( @geometry )
geometr. IsValid                            | IsValid ( @geometry )
geometr. IsWithinDistance (Geom, distance)   | Distance ( @geometry , @geom ) <= @distance
geometr. Base                             | GLength ( @geometry )
geometr. NumGeometries                      | NumGeometries ( @geometry )
geometr. NumPoints                          | NumPoints ( @geometry )
geometr. OgcGeometryType                    | CASE GeometryType ( @geometry ) lorsque « point », puis 1... EFFET
geometr. Chevauchements (g)                        | Chevauchements ( @geometry , @g )
geometr. PointOnSurface                     | PointOnSurface ( @geometry )
geometr. Associer (g, intersectionPattern)     | Associer ( @geometry , @g , @intersectionPattern )
geometr. Inverse ()                          | ST_Reverse ( @geometry )
geometr. SRID                               | SRID ( @geometry )
geometr. SymmetricDifference (autre)         | SymDifference ( @geometry , @other )
geometr. ToBinary()                         | AsBinary ( @geometry )
geometr. ToText()                           | AsText ( @geometry )
geometr. Touche (g)                         | Touche ( @geometry , @g )
geometr. Union ()                            | UnaryUnion ( @geometry )
geometr. Union (autre)                       | GUnion ( @geometry , @other )
geometr. Dans (g)                          | Dans ( @geometry , @g )
geometryCollection [i]                       | Geometry ( @geometryCollection , @i + 1)
geometryCollection. Count                    | NumGeometries ( @geometryCollection )
lineString. Count                            | NumPoints ( @lineString )
lineString. point de terminaison                         | Point de terminaison ( @lineString )
lineString. GetPointN (n)                     | PointN ( @lineString , @n + 1)
lineString. IsClosed                         | IsClosed ( @lineString )
lineString. IsRing                           | IsRing ( @lineString )
lineString. StartPoint                       | StartPoint ( @lineString )
multiLineString. IsClosed                    | IsClosed ( @multiLineString )
point. Lecteur                                     | M ( @point )
point. X                                     | X ( @point )
point. Y                                     | Y ( @point )
point. Lettre                                     | Z ( @point )
Polygone. ExteriorRing                        | ExteriorRing ( @polygon )
Polygone. GetInteriorRingN (n)                 | InteriorRingN ( @polygon , @n + 1)
Polygone. NumInteriorRings                    | NumInteriorRing ( @polygon )

## <a name="additional-resources"></a>Ressources supplémentaires

* [Page d’accueil SpatiaLite](https://www.gaia-gis.it/fossil/libspatialite)
* [Documentation NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/)
