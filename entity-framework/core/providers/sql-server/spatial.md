---
title: Fournisseur de base de données Microsoft SQL Server-données spatiales-EF Core
description: Utilisation de données spatiales avec le fournisseur de base de données Entity Framework Core Microsoft SQL Server
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sql-server/spatial
ms.openlocfilehash: 1356d2d86a497f01c4eacca777a8a260f33c0e9b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066630"
---
# <a name="spatial-data-in-the-sql-server-ef-core-provider"></a>Données spatiales dans le fournisseur de EF Core SQL Server

Cette page contient des informations supplémentaires sur l’utilisation de données spatiales avec le fournisseur de base de données Microsoft SQL Server. Pour obtenir des informations générales sur l’utilisation des données spatiales dans EF Core, consultez la documentation principale sur les [données spatiales](xref:core/modeling/spatial) .

## <a name="geography-or-geometry"></a>Geography ou Geometry

Par défaut, les propriétés spatiales sont mappées aux `geography` colonnes dans SQL Server. Pour `geometry` ce faire, [configurez le type de colonne](xref:core/modeling/entity-properties#column-data-types) dans votre modèle.

## <a name="geography-polygon-rings"></a>Anneaux de polygone Geography

Lors de l’utilisation du `geography` type de colonne, SQL Server impose des exigences supplémentaires sur l’anneau extérieur (ou l’interpréteur de commandes) et les anneaux intérieurs (ou trous). L’anneau extérieur doit être orienté vers le sens inverse des aiguilles d’une montre et des anneaux intérieurs. [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) valide cette valeur avant d’envoyer des valeurs à la base de données.

## <a name="fullglobe"></a>FullGlobe

SQL Server a un type Geometry non standard pour représenter le globe complet lors de l’utilisation du `geography` type de colonne. Il offre également un moyen de représenter les polygones en fonction du monde entier (sans anneau extérieur). Aucun de ces deux n’est pris en charge par NTS.

> [!WARNING]
> Les FullGlobe et les polygones basés sur ce dernier ne sont pas pris en charge par NTS.

## <a name="curves"></a>Courbes

Comme mentionné dans la documentation principale relative aux [données spatiales](xref:core/modeling/spatial) , NTS ne peut actuellement pas représenter des courbes. Cela signifie que vous devrez transformer les valeurs CircularString, CompoundCurve et CurePolygon à l’aide de la méthode [STCurveToLine](/sql/t-sql/spatial-geography/stcurvetoline-geography-data-type) avant de les utiliser dans EF Core.

> [!WARNING]
> CircularString, CompoundCurve et CurePolygon ne sont pas pris en charge par NTS.

## <a name="spatial-function-mappings"></a>Mappages de fonctions spatiales

Ce tableau montre les membres NTS qui sont traduits en fonctions SQL. Notez que les traductions varient selon que la colonne est de type Geography ou Geometry.

.NET                                      | SQL (geography)                                              | SQL (géométrie)
----------------------------------------- | ------------------------------------------------------------ | --------------
geometr. Partie                             | @geometry.STArea()                                           | @geometry.STArea()
geometr. AsBinary()                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
geometr. AsText()                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
geometr. Bordure                         |                                                              | @geometry.STBoundary()
geometr. Mémoire tampon (distance)                 | @geometry.STBuffer(@distance)                                | @geometry.STBuffer(@distance)
geometr. Centroïde                         |                                                              | @geometry.STCentroid()
geometr. Contient (g)                      | @geometry.STContains(@g)                                     | @geometry.STContains(@g)
geometr. ConvexHull()                     | @geometry.STConvexHull()                                     | @geometry.STConvexHull()
geometr. Croisements (g)                       |                                                              | @geometry.STCrosses(@g)
geometr. Différence (autre)                | @geometry.STDifference(@other)                               | @geometry.STDifference(@other)
geometr. Codes                        | @geometry.STDimension()                                      | @geometry.STDimension()
geometr. Disjoint (g)                      | @geometry.STDisjoint(@g)                                     | @geometry.STDisjoint(@g)
geometr. Distance (g)                      | @geometry.STDistance(@g)                                     | @geometry.STDistance(@g)
geometr. Envelope                         |                                                              | @geometry.STEnvelope()
geometr. EqualsTopologically (g)           | @geometry.STEquals(@g)                                       | @geometry.STEquals(@g)
geometr. GeometryType                     | @geometry.STGeometryType()                                   | @geometry.STGeometryType()
geometr. GetGeometryN (n)                  | @geometry.STGeometryN( @n + 1)                                | @geometry.STGeometryN( @n + 1)
geometr. InteriorPoint                    |                                                              | @geometry.STPointOnSurface()
geometr. Intersection (autre)              | @geometry.STIntersection(@other)                             | @geometry.STIntersection(@other)
geometr. Croisements (g)                    | @geometry.STIntersects(@g)                                   | @geometry.STIntersects(@g)
geometr. IsEmpty                          | @geometry.STIsEmpty()                                        | @geometry.STIsEmpty()
geometr. IsSimple                         |                                                              | @geometry.STIsSimple()
geometr. IsValid                          | @geometry.STIsValid()                                        | @geometry.STIsValid()
geometr. IsWithinDistance (Geom, distance) | @geometry.STDistance( @geom ) <= @distance                     | @geometry.STDistance( @geom ) <= @distance
geometr. Base                           | @geometry.STLength()                                         | @geometry.STLength()
geometr. NumGeometries                    | @geometry.STNumGeometries()                                  | @geometry.STNumGeometries()
geometr. NumPoints                        | @geometry.STNumPoints()                                      | @geometry.STNumPoints()
geometr. OgcGeometryType                  | CASE @geometry.STGeometryType () quand N’Point’Then 1... EFFET | CASE @geometry.STGeometryType () quand N’Point’Then 1... EFFET
geometr. Chevauchements (g)                      | @geometry.STOverlaps(@g)                                     | @geometry.STOverlaps(@g)
geometr. PointOnSurface                   |                                                              | @geometry.STPointOnSurface()
geometr. Associer (g, intersectionPattern)   |                                                              | @geometry.STRelate(@g, @intersectionPattern)
geometr. SRID                             | @geometry.STSrid                                             | @geometry.STSrid
geometr. SymmetricDifference (autre)       | @geometry.STSymDifference(@other)                            | @geometry.STSymDifference(@other)
geometr. ToBinary()                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
geometr. ToText()                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
geometr. Touche (g)                       |                                                              | @geometry.STTouches(@g)
geometr. Union (autre)                     | @geometry.STUnion(@other)                                    | @geometry.STUnion(@other)
geometr. Dans (g)                        | @geometry.STWithin(@g)                                       | @geometry.STWithin(@g)
geometryCollection [i]                     | @geometryCollection.STGeometryN( @i + 1)                      | @geometryCollection.STGeometryN( @i + 1)
geometryCollection. Count                  | @geometryCollection.STNumGeometries()                        | @geometryCollection.STNumGeometries()
lineString. Count                          | @lineString.STNumPoints()                                    | @lineString.STNumPoints()
lineString. point de terminaison                       | @lineString.STEndPoint()                                     | @lineString.STEndPoint()
lineString. GetPointN (n)                   | @lineString.STPointN( @n + 1)                                 | @lineString.STPointN( @n + 1)
lineString. IsClosed                       | @lineString.STIsClosed()                                     | @lineString.STIsClosed()
lineString. IsRing                         |                                                              | @lineString.IsRing()
lineString. StartPoint                     | @lineString.STStartPoint()                                   | @lineString.STStartPoint()
multiLineString. IsClosed                  | @multiLineString.STIsClosed()                                | @multiLineString.STIsClosed()
point. Lecteur                                   | @point.M                                                     | @point.M
point. X                                   | @point.Long                                                  | @point.STX
point. Y                                   | @point.Lat                                                   | @point.STY
point. Lettre                                   | @point.Z                                                     | @point.Z
Polygone. ExteriorRing                      | @polygon.RingN1,0                                            | @polygon.STExteriorRing()
Polygone. GetInteriorRingN (n)               | @polygon.RingN( @n + 2)                                       | @polygon.STInteriorRingN( @n + 1)
Polygone. NumInteriorRings                  | @polygon.NumRings()-1                                      | @polygon.STNumInteriorRing()

## <a name="additional-resources"></a>Ressources supplémentaires

* [Données spatiales dans SQL Server](/sql/relational-databases/spatial/spatial-data-sql-server)
* [Documentation NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/)
