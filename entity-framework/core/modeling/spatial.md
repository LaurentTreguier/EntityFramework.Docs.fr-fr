---
title: Données spatiales-EF Core
description: Utilisation de données spatiales dans un modèle de Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 42386fb132f135d725a2068d91dc49c7f613e277
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616698"
---
# <a name="spatial-data"></a>Données spatiales

> [!NOTE]
> Cette fonctionnalité a été ajoutée dans EF Core 2,2.

Les données spatiales représentent l’emplacement physique et la forme des objets. De nombreuses bases de données prennent en charge ce type de données afin qu’elles puissent être indexées et interrogées avec d’autres données. Les scénarios courants incluent l’interrogation d’objets situés à une distance donnée à partir d’un emplacement ou la sélection de l’objet dont la bordure contient un emplacement donné. EF Core prend en charge le mappage aux types de données spatiales à l’aide de la bibliothèque spatiale [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .

## <a name="installing"></a>Installation de

Pour pouvoir utiliser les données spatiales avec EF Core, vous devez installer le package NuGet de prise en charge approprié. Le package que vous devez installer dépend du fournisseur que vous utilisez.

Fournisseur EF Core                        | Package NuGet spatial
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a>Rétroconception

Les packages NuGet spatiaux activent également les modèles d' [ingénierie à rebours](xref:core/managing-schemas/scaffolding) avec des propriétés spatiales, mais vous devez installer le package ***avant*** d’exécuter `Scaffold-DbContext` ou `dotnet ef dbcontext scaffold` . Si vous ne le souhaitez pas, vous recevrez des avertissements sur les mappages de type pour les colonnes et les colonnes seront ignorées.

## <a name="nettopologysuite-nts"></a>NetTopologySuite (NTS)

NetTopologySuite est une bibliothèque spatiale pour .NET. EF Core permet le mappage aux types de données spatiales dans la base de données à l’aide des types NTS dans votre modèle.

Pour activer le mappage aux types spatiaux via NTS, appelez la méthode UseNetTopologySuite sur le générateur d’options DbContext du fournisseur. Par exemple, avec SQL Server vous l’appellerez comme ceci.

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

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

Les classes d’entité suivantes peuvent être utilisées pour mapper à des tables dans l' [exemple de base de données grand World](https://go.microsoft.com/fwlink/?LinkID=800630)Importers.

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public Point Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public Geometry Border { get; set; }
}
```

### <a name="creating-values"></a>Création de valeurs

Vous pouvez utiliser des constructeurs pour créer des objets Geometry ; Toutefois, NTS recommande d’utiliser à la place une fabrique de géométrie. Cela vous permet de spécifier un SRID par défaut (le système de référence spatiale utilisé par les coordonnées) et vous donne le contrôle sur des éléments plus avancés tels que le modèle de précision (utilisé lors des calculs) et la séquence de coordonnées (détermine les ordonnées--dimensions et mesures, qui sont disponibles).

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> 4326 fait référence à WGS 84, une norme utilisée dans le GPS et d’autres systèmes géographiques.

### <a name="longitude-and-latitude"></a>Longitude et Latitude

Les coordonnées en NTS sont exprimées en valeurs X et Y. Pour représenter la longitude et la latitude, utilisez X pour la longitude et Y pour la latitude. Notez que cette valeur est **retournée** à partir du `latitude, longitude` format dans lequel vous voyez généralement ces valeurs.

### <a name="srid-ignored-during-client-operations"></a>SRID ignoré pendant les opérations du client

NTS ignore les valeurs SRID lors des opérations. Il part du principe qu’il s’agit d’un système de coordonnées planaire. Cela signifie que si vous spécifiez des coordonnées en termes de longitude et de latitude, certaines valeurs évaluées par le client, telles que la distance, la longueur et la zone, seront en degrés, et non en mètres. Pour les valeurs plus significatives, vous devez d’abord projeter les coordonnées dans un autre système de coordonnées à l’aide d’une bibliothèque comme [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) avant de calculer ces valeurs.

Si une opération est évaluée par le serveur par EF Core via SQL, l’unité du résultat est déterminée par la base de données.

Voici un exemple d’utilisation de ProjNet4GeoAPI pour calculer la distance entre deux villes.

``` csharp
static class GeometryExtensions
{
    static readonly CoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                [4326] = GeographicCoordinateSystem.WGS84.WKT,

                // This coordinate system covers the area of our data.
                // Different data requires a different coordinate system.
                [2855] =
                @"
                    PROJCS[""NAD83(HARN) / Washington North"",
                        GEOGCS[""NAD83(HARN)"",
                            DATUM[""NAD83_High_Accuracy_Regional_Network"",
                                SPHEROID[""GRS 1980"",6378137,298.257222101,
                                    AUTHORITY[""EPSG"",""7019""]],
                                AUTHORITY[""EPSG"",""6152""]],
                            PRIMEM[""Greenwich"",0,
                                AUTHORITY[""EPSG"",""8901""]],
                            UNIT[""degree"",0.01745329251994328,
                                AUTHORITY[""EPSG"",""9122""]],
                            AUTHORITY[""EPSG"",""4152""]],
                        PROJECTION[""Lambert_Conformal_Conic_2SP""],
                        PARAMETER[""standard_parallel_1"",48.73333333333333],
                        PARAMETER[""standard_parallel_2"",47.5],
                        PARAMETER[""latitude_of_origin"",47],
                        PARAMETER[""central_meridian"",-120.8333333333333],
                        PARAMETER[""false_easting"",500000],
                        PARAMETER[""false_northing"",0],
                        UNIT[""metre"",1,
                            AUTHORITY[""EPSG"",""9001""]],
                        AUTHORITY[""EPSG"",""2855""]]
                "
            });

    public static Geometry ProjectTo(this Geometry geometry, int srid)
    {
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        var result = geometry.Copy();
        result.Apply(new MathTransformFilter(transformation.MathTransform));

        return result;
    }

    class MathTransformFilter : ICoordinateSequenceFilter
    {
        readonly MathTransform _transform;

        public MathTransformFilter(MathTransform transform)
            => _transform = transform;

        public bool Done => false;
        public bool GeometryChanged => true;

        public void Filter(CoordinateSequence seq, int i)
        {
            var result = _transform.Transform(
                new[]
                {
                    seq.GetOrdinate(i, Ordinate.X),
                    seq.GetOrdinate(i, Ordinate.Y)
                });
            seq.SetOrdinate(i, Ordinate.X, result[0]);
            seq.SetOrdinate(i, Ordinate.Y, result[1]);
        }
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a>Interrogation des données

Dans LINQ, les méthodes et propriétés NTS disponibles en tant que fonctions de base de données sont traduites en SQL. Par exemple, les méthodes distance et Contains sont traduites dans les requêtes suivantes. Le tableau à la fin de cet article indique quels membres sont pris en charge par différents fournisseurs de EF Core.

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a>SQL Server

Si vous utilisez SQL Server, vous devez connaître certaines choses supplémentaires.

### <a name="geography-or-geometry"></a>Geography ou Geometry

Par défaut, les propriétés spatiales sont mappées aux `geography` colonnes dans SQL Server. Pour `geometry` ce faire, [configurez le type de colonne](xref:core/modeling/entity-properties#column-data-types) dans votre modèle.

### <a name="geography-polygon-rings"></a>Anneaux de polygone Geography

Lors de l’utilisation du `geography` type de colonne, SQL Server impose des exigences supplémentaires sur l’anneau extérieur (ou l’interpréteur de commandes) et les anneaux intérieurs (ou trous). L’anneau extérieur doit être orienté vers le sens inverse des aiguilles d’une montre et des anneaux intérieurs. NTS valide cette valeur avant d’envoyer des valeurs à la base de données.

### <a name="fullglobe"></a>FullGlobe

SQL Server a un type Geometry non standard pour représenter le globe complet lors de l’utilisation du `geography` type de colonne. Il offre également un moyen de représenter les polygones en fonction du monde entier (sans anneau extérieur). Aucun de ces deux n’est pris en charge par NTS.

> [!WARNING]
> Les FullGlobe et les polygones basés sur ce dernier ne sont pas pris en charge par NTS.

## <a name="sqlite"></a>SQLite

Voici quelques informations supplémentaires pour celles qui utilisent SQLite.

### <a name="installing-spatialite"></a>Installation de SpatiaLite

Sur Windows, la bibliothèque de mod_spatialite native est distribuée en tant que dépendance de package NuGet. D’autres plateformes doivent l’installer séparément. Cette opération s’effectue généralement à l’aide d’un gestionnaire de package logiciel. Par exemple, vous pouvez utiliser la fonction APT sur Ubuntu et homebrew sur MacOS.

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

Malheureusement, les versions plus récentes de PROJ (une dépendance de SpatiaLite) ne sont pas compatibles avec le [Bundle SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)par défaut d’EF. Vous pouvez contourner ce contournement en créant un [fournisseur SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) personnalisé qui utilise la bibliothèque SQLite système, ou vous pouvez installer une build personnalisée de SpatiaLite la désactivation de la prise en charge de proj.

``` sh
curl https://www.gaia-gis.it/gaia-sins/libspatialite-4.3.0a.tar.gz | tar -xz
cd libspatialite-4.3.0a

if [[ `uname -s` == Darwin* ]]; then
    # Mac OS requires some minor patching
    sed -i "" "s/shrext_cmds='\`test \\.\$module = .yes && echo .so \\|\\| echo \\.dylib\`'/shrext_cmds='.dylib'/g" configure
fi

./configure --disable-proj
make
make install
```

### <a name="configuring-srid"></a>Configuration de SRID

Dans SpatiaLite, les colonnes doivent spécifier un SRID par colonne. La valeur par défaut de SRID est `0` . Spécifiez un autre SRID à l’aide de la méthode ForSqliteHasSrid.

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a>Dimension

Comme pour SRID, la dimension d’une colonne (ou ordonnée) est également spécifiée dans le cadre de la colonne. Les ordonnées par défaut sont X et Y. Activez des ordonnées supplémentaires (Z et M) à l’aide de la méthode ForSqliteHasDimension.

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a>Opérations traduites

Ce tableau montre les membres NTS qui sont convertis en SQL par chaque fournisseur de EF Core.

NetTopologySuite | SQL Server (géométrie) | SQL Server (geography) | SQLite | Npgsql
--- |:---:|:---:|:---:|:---:
Geometry. Area | ✔ | ✔ | ✔ | ✔
Geometry. AsBinary () | ✔ | ✔ | ✔ | ✔
Geometry. AsText () | ✔ | ✔ | ✔ | ✔
Geometry. Boundary | ✔ | | ✔ | ✔
Geometry. buffer (double) | ✔ | ✔ | ✔ | ✔
Geometry. buffer (double, int) | | | ✔ | ✔
Geometry. Centre de gravité | ✔ | | ✔ | ✔
Geometry. Contains (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. ConvexHull () | ✔ | ✔ | ✔ | ✔
Geometry. CoveredBy (Geometry) | | | ✔ | ✔
Geometry. couvertures (Geometry) | | | ✔ | ✔
Geometry. crosses (Geometry) | ✔ | | ✔ | ✔
Geometry. difference (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. dimension | ✔ | ✔ | ✔ | ✔
Geometry. disjointe (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. distance (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. Envelope | ✔ | | ✔ | ✔
Geometry. EqualsExact (Geometry) | | | | ✔
Geometry. EqualsTopologically (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. GeometryType | ✔ | ✔ | ✔ | ✔
Geometry. GetGeometryN (int) | ✔ | | ✔ | ✔
Geometry. InteriorPoint | ✔ | | ✔ | ✔
Geometry. intersection (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. intersections (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. IsEmpty | ✔ | ✔ | ✔ | ✔
Geometry. IsSimple | ✔ | | ✔ | ✔
Geometry. IsValid | ✔ | ✔ | ✔ | ✔
Geometry. IsWithinDistance (Geometry, double) | ✔ | | ✔ | ✔
Geometry. Length | ✔ | ✔ | ✔ | ✔
Geometry. NumGeometries | ✔ | ✔ | ✔ | ✔
Geometry. NumPoints | ✔ | ✔ | ✔ | ✔
Geometry. OgcGeometryType | ✔ | ✔ | ✔ | ✔
Geometry. chevauchements (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. PointOnSurface | ✔ | | ✔ | ✔
Geometry. relate (Geometry, String) | ✔ | | ✔ | ✔
Geometry. Reverse () | | | ✔ | ✔
Geometry. SRID | ✔ | ✔ | ✔ | ✔
Geometry. SymmetricDifference (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. ToBinary () | ✔ | ✔ | ✔ | ✔
Geometry. ToText () | ✔ | ✔ | ✔ | ✔
Geometry. touche (Geometry) | ✔ | | ✔ | ✔
Geometry. Union () | | | ✔ | ✔
Geometry. Union (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. within (Geometry) | ✔ | ✔ | ✔ | ✔
GeometryCollection. Count | ✔ | ✔ | ✔ | ✔
GeometryCollection [int] | ✔ | ✔ | ✔ | ✔
LineString. Count | ✔ | ✔ | ✔ | ✔
LineString. point de terminaison | ✔ | ✔ | ✔ | ✔
LineString. GetPointN (int) | ✔ | ✔ | ✔ | ✔
LineString. IsClosed | ✔ | ✔ | ✔ | ✔
LineString. IsRing | ✔ | | ✔ | ✔
LineString. StartPoint | ✔ | ✔ | ✔ | ✔
MultiLineString. IsClosed | ✔ | ✔ | ✔ | ✔
Point. M | ✔ | ✔ | ✔ | ✔
Point. X | ✔ | ✔ | ✔ | ✔
Point. Y | ✔ | ✔ | ✔ | ✔
Point. Z | ✔ | ✔ | ✔ | ✔
Polygon. ExteriorRing | ✔ | ✔ | ✔ | ✔
Polygon. GetInteriorRingN (int) | ✔ | ✔ | ✔ | ✔
Polygon. NumInteriorRings | ✔ | ✔ | ✔ | ✔

## <a name="additional-resources"></a>Ressources supplémentaires

* [Données spatiales dans SQL Server](/sql/relational-databases/spatial/spatial-data-sql-server)
* [Page d’accueil SpatiaLite](https://www.gaia-gis.it/fossil/libspatialite)
* [Documentation spatiale npgsql](https://www.npgsql.org/efcore/mapping/nts.html)
* [Documentation PostGIS](https://postgis.net/documentation/)
