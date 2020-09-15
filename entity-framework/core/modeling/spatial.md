---
title: Données spatiales-EF Core
description: Utilisation de données spatiales dans un modèle de Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
uid: core/modeling/spatial
ms.openlocfilehash: 8c08835f2d6211e6be5852b58b35f003f823bded
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071431"
---
# <a name="spatial-data"></a><span data-ttu-id="82c20-103">Données spatiales</span><span class="sxs-lookup"><span data-stu-id="82c20-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="82c20-104">Cette fonctionnalité a été ajoutée dans EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="82c20-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="82c20-105">Les données spatiales représentent l’emplacement physique et la forme des objets.</span><span class="sxs-lookup"><span data-stu-id="82c20-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="82c20-106">De nombreuses bases de données prennent en charge ce type de données afin qu’elles puissent être indexées et interrogées avec d’autres données.</span><span class="sxs-lookup"><span data-stu-id="82c20-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="82c20-107">Les scénarios courants incluent l’interrogation d’objets situés à une distance donnée à partir d’un emplacement ou la sélection de l’objet dont la bordure contient un emplacement donné.</span><span class="sxs-lookup"><span data-stu-id="82c20-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="82c20-108">EF Core prend en charge le mappage aux types de données spatiales à l’aide de la bibliothèque spatiale [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="82c20-108">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="82c20-109">Installation de</span><span class="sxs-lookup"><span data-stu-id="82c20-109">Installing</span></span>

<span data-ttu-id="82c20-110">Pour pouvoir utiliser les données spatiales avec EF Core, vous devez installer le package NuGet de prise en charge approprié.</span><span class="sxs-lookup"><span data-stu-id="82c20-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="82c20-111">Le package que vous devez installer dépend du fournisseur que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="82c20-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="82c20-112">Fournisseur EF Core</span><span class="sxs-lookup"><span data-stu-id="82c20-112">EF Core Provider</span></span>                        | <span data-ttu-id="82c20-113">Package NuGet spatial</span><span class="sxs-lookup"><span data-stu-id="82c20-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="82c20-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="82c20-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="82c20-115">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="82c20-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="82c20-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="82c20-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="82c20-117">Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="82c20-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="82c20-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="82c20-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="82c20-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="82c20-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="82c20-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="82c20-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="82c20-121">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="82c20-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="82c20-122">Rétroconception</span><span class="sxs-lookup"><span data-stu-id="82c20-122">Reverse engineering</span></span>

<span data-ttu-id="82c20-123">Les packages NuGet spatiaux activent également les modèles d' [ingénierie à rebours](xref:core/managing-schemas/scaffolding) avec des propriétés spatiales, mais vous devez installer le package ***avant*** d’exécuter `Scaffold-DbContext` ou `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="82c20-123">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="82c20-124">Si vous ne le souhaitez pas, vous recevrez des avertissements sur les mappages de type pour les colonnes et les colonnes seront ignorées.</span><span class="sxs-lookup"><span data-stu-id="82c20-124">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="82c20-125">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="82c20-125">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="82c20-126">NetTopologySuite est une bibliothèque spatiale pour .NET.</span><span class="sxs-lookup"><span data-stu-id="82c20-126">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="82c20-127">EF Core permet le mappage aux types de données spatiales dans la base de données à l’aide des types NTS dans votre modèle.</span><span class="sxs-lookup"><span data-stu-id="82c20-127">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="82c20-128">Pour activer le mappage aux types spatiaux via NTS, appelez la méthode UseNetTopologySuite sur le générateur d’options DbContext du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="82c20-128">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="82c20-129">Par exemple, avec SQL Server vous l’appellerez comme ceci.</span><span class="sxs-lookup"><span data-stu-id="82c20-129">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="82c20-130">Il existe plusieurs types de données spatiales.</span><span class="sxs-lookup"><span data-stu-id="82c20-130">There are several spatial data types.</span></span> <span data-ttu-id="82c20-131">Le type que vous utilisez dépend des types de formes que vous souhaitez autoriser.</span><span class="sxs-lookup"><span data-stu-id="82c20-131">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="82c20-132">Voici la hiérarchie des types NTS que vous pouvez utiliser pour les propriétés de votre modèle.</span><span class="sxs-lookup"><span data-stu-id="82c20-132">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="82c20-133">Ils sont situés dans l' `NetTopologySuite.Geometries` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="82c20-133">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="82c20-134">Géométrie</span><span class="sxs-lookup"><span data-stu-id="82c20-134">Geometry</span></span>
  * <span data-ttu-id="82c20-135">Point</span><span class="sxs-lookup"><span data-stu-id="82c20-135">Point</span></span>
  * <span data-ttu-id="82c20-136">LineString</span><span class="sxs-lookup"><span data-stu-id="82c20-136">LineString</span></span>
  * <span data-ttu-id="82c20-137">Polygone</span><span class="sxs-lookup"><span data-stu-id="82c20-137">Polygon</span></span>
  * <span data-ttu-id="82c20-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="82c20-138">GeometryCollection</span></span>
    * <span data-ttu-id="82c20-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="82c20-139">MultiPoint</span></span>
    * <span data-ttu-id="82c20-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="82c20-140">MultiLineString</span></span>
    * <span data-ttu-id="82c20-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="82c20-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="82c20-142">CircularString, CompoundCurve et CurePolygon ne sont pas pris en charge par NTS.</span><span class="sxs-lookup"><span data-stu-id="82c20-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="82c20-143">L’utilisation du type Geometry de base permet à n’importe quel type de forme d’être spécifié par la propriété.</span><span class="sxs-lookup"><span data-stu-id="82c20-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="82c20-144">Les classes d’entité suivantes peuvent être utilisées pour mapper à des tables dans l' [exemple de base de données grand World](https://go.microsoft.com/fwlink/?LinkID=800630)Importers.</span><span class="sxs-lookup"><span data-stu-id="82c20-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="82c20-145">Création de valeurs</span><span class="sxs-lookup"><span data-stu-id="82c20-145">Creating values</span></span>

<span data-ttu-id="82c20-146">Vous pouvez utiliser des constructeurs pour créer des objets Geometry ; Toutefois, NTS recommande d’utiliser à la place une fabrique de géométrie.</span><span class="sxs-lookup"><span data-stu-id="82c20-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="82c20-147">Cela vous permet de spécifier un SRID par défaut (le système de référence spatiale utilisé par les coordonnées) et vous donne le contrôle sur des éléments plus avancés tels que le modèle de précision (utilisé lors des calculs) et la séquence de coordonnées (détermine les ordonnées--dimensions et mesures, qui sont disponibles).</span><span class="sxs-lookup"><span data-stu-id="82c20-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="82c20-148">4326 fait référence à WGS 84, une norme utilisée dans le GPS et d’autres systèmes géographiques.</span><span class="sxs-lookup"><span data-stu-id="82c20-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="82c20-149">Longitude et Latitude</span><span class="sxs-lookup"><span data-stu-id="82c20-149">Longitude and Latitude</span></span>

<span data-ttu-id="82c20-150">Les coordonnées en NTS sont exprimées en valeurs X et Y.</span><span class="sxs-lookup"><span data-stu-id="82c20-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="82c20-151">Pour représenter la longitude et la latitude, utilisez X pour la longitude et Y pour la latitude.</span><span class="sxs-lookup"><span data-stu-id="82c20-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="82c20-152">Notez que cette valeur est **retournée** à partir du `latitude, longitude` format dans lequel vous voyez généralement ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="82c20-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="82c20-153">SRID ignoré pendant les opérations du client</span><span class="sxs-lookup"><span data-stu-id="82c20-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="82c20-154">NTS ignore les valeurs SRID lors des opérations.</span><span class="sxs-lookup"><span data-stu-id="82c20-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="82c20-155">Il part du principe qu’il s’agit d’un système de coordonnées planaire.</span><span class="sxs-lookup"><span data-stu-id="82c20-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="82c20-156">Cela signifie que si vous spécifiez des coordonnées en termes de longitude et de latitude, certaines valeurs évaluées par le client, telles que la distance, la longueur et la zone, seront en degrés, et non en mètres.</span><span class="sxs-lookup"><span data-stu-id="82c20-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="82c20-157">Pour les valeurs plus significatives, vous devez d’abord projeter les coordonnées dans un autre système de coordonnées à l’aide d’une bibliothèque comme [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) avant de calculer ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="82c20-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="82c20-158">Si une opération est évaluée par le serveur par EF Core via SQL, l’unité du résultat est déterminée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="82c20-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="82c20-159">Voici un exemple d’utilisation de ProjNet4GeoAPI pour calculer la distance entre deux villes.</span><span class="sxs-lookup"><span data-stu-id="82c20-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="82c20-160">Interrogation des données</span><span class="sxs-lookup"><span data-stu-id="82c20-160">Querying Data</span></span>

<span data-ttu-id="82c20-161">Dans LINQ, les méthodes et propriétés NTS disponibles en tant que fonctions de base de données sont traduites en SQL.</span><span class="sxs-lookup"><span data-stu-id="82c20-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="82c20-162">Par exemple, les méthodes distance et Contains sont traduites dans les requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="82c20-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="82c20-163">Le tableau à la fin de cet article indique quels membres sont pris en charge par différents fournisseurs de EF Core.</span><span class="sxs-lookup"><span data-stu-id="82c20-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="82c20-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="82c20-164">SQL Server</span></span>

<span data-ttu-id="82c20-165">Si vous utilisez SQL Server, vous devez connaître certaines choses supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="82c20-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="82c20-166">Geography ou Geometry</span><span class="sxs-lookup"><span data-stu-id="82c20-166">Geography or geometry</span></span>

<span data-ttu-id="82c20-167">Par défaut, les propriétés spatiales sont mappées aux `geography` colonnes dans SQL Server.</span><span class="sxs-lookup"><span data-stu-id="82c20-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="82c20-168">Pour `geometry` ce faire, [configurez le type de colonne](xref:core/modeling/entity-properties#column-data-types) dans votre modèle.</span><span class="sxs-lookup"><span data-stu-id="82c20-168">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="82c20-169">Anneaux de polygone Geography</span><span class="sxs-lookup"><span data-stu-id="82c20-169">Geography polygon rings</span></span>

<span data-ttu-id="82c20-170">Lors de l’utilisation du `geography` type de colonne, SQL Server impose des exigences supplémentaires sur l’anneau extérieur (ou l’interpréteur de commandes) et les anneaux intérieurs (ou trous).</span><span class="sxs-lookup"><span data-stu-id="82c20-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="82c20-171">L’anneau extérieur doit être orienté vers le sens inverse des aiguilles d’une montre et des anneaux intérieurs.</span><span class="sxs-lookup"><span data-stu-id="82c20-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="82c20-172">NTS valide cette valeur avant d’envoyer des valeurs à la base de données.</span><span class="sxs-lookup"><span data-stu-id="82c20-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="82c20-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="82c20-173">FullGlobe</span></span>

<span data-ttu-id="82c20-174">SQL Server a un type Geometry non standard pour représenter le globe complet lors de l’utilisation du `geography` type de colonne.</span><span class="sxs-lookup"><span data-stu-id="82c20-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="82c20-175">Il offre également un moyen de représenter les polygones en fonction du monde entier (sans anneau extérieur).</span><span class="sxs-lookup"><span data-stu-id="82c20-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="82c20-176">Aucun de ces deux n’est pris en charge par NTS.</span><span class="sxs-lookup"><span data-stu-id="82c20-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="82c20-177">Les FullGlobe et les polygones basés sur ce dernier ne sont pas pris en charge par NTS.</span><span class="sxs-lookup"><span data-stu-id="82c20-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="82c20-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="82c20-178">SQLite</span></span>

<span data-ttu-id="82c20-179">Voici quelques informations supplémentaires pour celles qui utilisent SQLite.</span><span class="sxs-lookup"><span data-stu-id="82c20-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="82c20-180">Installation de SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="82c20-180">Installing SpatiaLite</span></span>

<span data-ttu-id="82c20-181">Sur Windows, la bibliothèque de mod_spatialite native est distribuée en tant que dépendance de package NuGet.</span><span class="sxs-lookup"><span data-stu-id="82c20-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="82c20-182">D’autres plateformes doivent l’installer séparément.</span><span class="sxs-lookup"><span data-stu-id="82c20-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="82c20-183">Cette opération s’effectue généralement à l’aide d’un gestionnaire de package logiciel.</span><span class="sxs-lookup"><span data-stu-id="82c20-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="82c20-184">Par exemple, vous pouvez utiliser la fonction APT sur Ubuntu et homebrew sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="82c20-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="82c20-185">Malheureusement, les versions plus récentes de PROJ (une dépendance de SpatiaLite) ne sont pas compatibles avec le [Bundle SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)par défaut d’EF.</span><span class="sxs-lookup"><span data-stu-id="82c20-185">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="82c20-186">Vous pouvez contourner ce contournement en créant un [fournisseur SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) personnalisé qui utilise la bibliothèque SQLite système, ou vous pouvez installer une build personnalisée de SpatiaLite la désactivation de la prise en charge de proj.</span><span class="sxs-lookup"><span data-stu-id="82c20-186">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="82c20-187">Configuration de SRID</span><span class="sxs-lookup"><span data-stu-id="82c20-187">Configuring SRID</span></span>

<span data-ttu-id="82c20-188">Dans SpatiaLite, les colonnes doivent spécifier un SRID par colonne.</span><span class="sxs-lookup"><span data-stu-id="82c20-188">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="82c20-189">La valeur par défaut de SRID est `0` .</span><span class="sxs-lookup"><span data-stu-id="82c20-189">The default SRID is `0`.</span></span> <span data-ttu-id="82c20-190">Spécifiez un autre SRID à l’aide de la méthode ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="82c20-190">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="82c20-191">Dimension</span><span class="sxs-lookup"><span data-stu-id="82c20-191">Dimension</span></span>

<span data-ttu-id="82c20-192">Comme pour SRID, la dimension d’une colonne (ou ordonnée) est également spécifiée dans le cadre de la colonne.</span><span class="sxs-lookup"><span data-stu-id="82c20-192">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="82c20-193">Les ordonnées par défaut sont X et Y. Activez des ordonnées supplémentaires (Z et M) à l’aide de la méthode ForSqliteHasDimension.</span><span class="sxs-lookup"><span data-stu-id="82c20-193">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="82c20-194">Opérations traduites</span><span class="sxs-lookup"><span data-stu-id="82c20-194">Translated Operations</span></span>

<span data-ttu-id="82c20-195">Ce tableau montre les membres NTS qui sont convertis en SQL par chaque fournisseur de EF Core.</span><span class="sxs-lookup"><span data-stu-id="82c20-195">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="82c20-196">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="82c20-196">NetTopologySuite</span></span> | <span data-ttu-id="82c20-197">SQL Server (géométrie)</span><span class="sxs-lookup"><span data-stu-id="82c20-197">SQL Server (geometry)</span></span> | <span data-ttu-id="82c20-198">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="82c20-198">SQL Server (geography)</span></span> | <span data-ttu-id="82c20-199">SQLite</span><span class="sxs-lookup"><span data-stu-id="82c20-199">SQLite</span></span> | <span data-ttu-id="82c20-200">Npgsql</span><span class="sxs-lookup"><span data-stu-id="82c20-200">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="82c20-201">Geometry. Area</span><span class="sxs-lookup"><span data-stu-id="82c20-201">Geometry.Area</span></span> | <span data-ttu-id="82c20-202">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-202">✔</span></span> | <span data-ttu-id="82c20-203">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-203">✔</span></span> | <span data-ttu-id="82c20-204">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-204">✔</span></span> | <span data-ttu-id="82c20-205">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-205">✔</span></span>
<span data-ttu-id="82c20-206">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="82c20-206">Geometry.AsBinary()</span></span> | <span data-ttu-id="82c20-207">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-207">✔</span></span> | <span data-ttu-id="82c20-208">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-208">✔</span></span> | <span data-ttu-id="82c20-209">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-209">✔</span></span> | <span data-ttu-id="82c20-210">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-210">✔</span></span>
<span data-ttu-id="82c20-211">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="82c20-211">Geometry.AsText()</span></span> | <span data-ttu-id="82c20-212">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-212">✔</span></span> | <span data-ttu-id="82c20-213">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-213">✔</span></span> | <span data-ttu-id="82c20-214">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-214">✔</span></span> | <span data-ttu-id="82c20-215">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-215">✔</span></span>
<span data-ttu-id="82c20-216">Geometry. Boundary</span><span class="sxs-lookup"><span data-stu-id="82c20-216">Geometry.Boundary</span></span> | <span data-ttu-id="82c20-217">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-217">✔</span></span> | | <span data-ttu-id="82c20-218">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-218">✔</span></span> | <span data-ttu-id="82c20-219">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-219">✔</span></span>
<span data-ttu-id="82c20-220">Geometry. buffer (double)</span><span class="sxs-lookup"><span data-stu-id="82c20-220">Geometry.Buffer(double)</span></span> | <span data-ttu-id="82c20-221">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-221">✔</span></span> | <span data-ttu-id="82c20-222">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-222">✔</span></span> | <span data-ttu-id="82c20-223">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-223">✔</span></span> | <span data-ttu-id="82c20-224">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-224">✔</span></span>
<span data-ttu-id="82c20-225">Geometry. buffer (double, int)</span><span class="sxs-lookup"><span data-stu-id="82c20-225">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="82c20-226">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-226">✔</span></span> | <span data-ttu-id="82c20-227">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-227">✔</span></span>
<span data-ttu-id="82c20-228">Geometry. Centre de gravité</span><span class="sxs-lookup"><span data-stu-id="82c20-228">Geometry.Centroid</span></span> | <span data-ttu-id="82c20-229">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-229">✔</span></span> | | <span data-ttu-id="82c20-230">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-230">✔</span></span> | <span data-ttu-id="82c20-231">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-231">✔</span></span>
<span data-ttu-id="82c20-232">Geometry. Contains (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-232">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="82c20-233">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-233">✔</span></span> | <span data-ttu-id="82c20-234">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-234">✔</span></span> | <span data-ttu-id="82c20-235">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-235">✔</span></span> | <span data-ttu-id="82c20-236">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-236">✔</span></span>
<span data-ttu-id="82c20-237">Geometry. ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="82c20-237">Geometry.ConvexHull()</span></span> | <span data-ttu-id="82c20-238">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-238">✔</span></span> | <span data-ttu-id="82c20-239">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-239">✔</span></span> | <span data-ttu-id="82c20-240">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-240">✔</span></span> | <span data-ttu-id="82c20-241">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-241">✔</span></span>
<span data-ttu-id="82c20-242">Geometry. CoveredBy (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-242">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="82c20-243">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-243">✔</span></span> | <span data-ttu-id="82c20-244">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-244">✔</span></span>
<span data-ttu-id="82c20-245">Geometry. couvertures (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-245">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="82c20-246">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-246">✔</span></span> | <span data-ttu-id="82c20-247">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-247">✔</span></span>
<span data-ttu-id="82c20-248">Geometry. crosses (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-248">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="82c20-249">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-249">✔</span></span> | | <span data-ttu-id="82c20-250">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-250">✔</span></span> | <span data-ttu-id="82c20-251">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-251">✔</span></span>
<span data-ttu-id="82c20-252">Geometry. difference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-252">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="82c20-253">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-253">✔</span></span> | <span data-ttu-id="82c20-254">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-254">✔</span></span> | <span data-ttu-id="82c20-255">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-255">✔</span></span> | <span data-ttu-id="82c20-256">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-256">✔</span></span>
<span data-ttu-id="82c20-257">Geometry. dimension</span><span class="sxs-lookup"><span data-stu-id="82c20-257">Geometry.Dimension</span></span> | <span data-ttu-id="82c20-258">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-258">✔</span></span> | <span data-ttu-id="82c20-259">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-259">✔</span></span> | <span data-ttu-id="82c20-260">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-260">✔</span></span> | <span data-ttu-id="82c20-261">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-261">✔</span></span>
<span data-ttu-id="82c20-262">Geometry. disjointe (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-262">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="82c20-263">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-263">✔</span></span> | <span data-ttu-id="82c20-264">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-264">✔</span></span> | <span data-ttu-id="82c20-265">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-265">✔</span></span> | <span data-ttu-id="82c20-266">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-266">✔</span></span>
<span data-ttu-id="82c20-267">Geometry. distance (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-267">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="82c20-268">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-268">✔</span></span> | <span data-ttu-id="82c20-269">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-269">✔</span></span> | <span data-ttu-id="82c20-270">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-270">✔</span></span> | <span data-ttu-id="82c20-271">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-271">✔</span></span>
<span data-ttu-id="82c20-272">Geometry. Envelope</span><span class="sxs-lookup"><span data-stu-id="82c20-272">Geometry.Envelope</span></span> | <span data-ttu-id="82c20-273">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-273">✔</span></span> | | <span data-ttu-id="82c20-274">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-274">✔</span></span> | <span data-ttu-id="82c20-275">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-275">✔</span></span>
<span data-ttu-id="82c20-276">Geometry. EqualsExact (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-276">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="82c20-277">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-277">✔</span></span>
<span data-ttu-id="82c20-278">Geometry. EqualsTopologically (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-278">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="82c20-279">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-279">✔</span></span> | <span data-ttu-id="82c20-280">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-280">✔</span></span> | <span data-ttu-id="82c20-281">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-281">✔</span></span> | <span data-ttu-id="82c20-282">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-282">✔</span></span>
<span data-ttu-id="82c20-283">Geometry. GeometryType</span><span class="sxs-lookup"><span data-stu-id="82c20-283">Geometry.GeometryType</span></span> | <span data-ttu-id="82c20-284">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-284">✔</span></span> | <span data-ttu-id="82c20-285">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-285">✔</span></span> | <span data-ttu-id="82c20-286">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-286">✔</span></span> | <span data-ttu-id="82c20-287">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-287">✔</span></span>
<span data-ttu-id="82c20-288">Geometry. GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="82c20-288">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="82c20-289">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-289">✔</span></span> | | <span data-ttu-id="82c20-290">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-290">✔</span></span> | <span data-ttu-id="82c20-291">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-291">✔</span></span>
<span data-ttu-id="82c20-292">Geometry. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="82c20-292">Geometry.InteriorPoint</span></span> | <span data-ttu-id="82c20-293">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-293">✔</span></span> | | <span data-ttu-id="82c20-294">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-294">✔</span></span> | <span data-ttu-id="82c20-295">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-295">✔</span></span>
<span data-ttu-id="82c20-296">Geometry. intersection (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-296">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="82c20-297">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-297">✔</span></span> | <span data-ttu-id="82c20-298">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-298">✔</span></span> | <span data-ttu-id="82c20-299">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-299">✔</span></span> | <span data-ttu-id="82c20-300">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-300">✔</span></span>
<span data-ttu-id="82c20-301">Geometry. intersections (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-301">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="82c20-302">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-302">✔</span></span> | <span data-ttu-id="82c20-303">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-303">✔</span></span> | <span data-ttu-id="82c20-304">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-304">✔</span></span> | <span data-ttu-id="82c20-305">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-305">✔</span></span>
<span data-ttu-id="82c20-306">Geometry. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="82c20-306">Geometry.IsEmpty</span></span> | <span data-ttu-id="82c20-307">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-307">✔</span></span> | <span data-ttu-id="82c20-308">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-308">✔</span></span> | <span data-ttu-id="82c20-309">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-309">✔</span></span> | <span data-ttu-id="82c20-310">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-310">✔</span></span>
<span data-ttu-id="82c20-311">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="82c20-311">Geometry.IsSimple</span></span> | <span data-ttu-id="82c20-312">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-312">✔</span></span> | | <span data-ttu-id="82c20-313">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-313">✔</span></span> | <span data-ttu-id="82c20-314">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-314">✔</span></span>
<span data-ttu-id="82c20-315">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="82c20-315">Geometry.IsValid</span></span> | <span data-ttu-id="82c20-316">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-316">✔</span></span> | <span data-ttu-id="82c20-317">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-317">✔</span></span> | <span data-ttu-id="82c20-318">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-318">✔</span></span> | <span data-ttu-id="82c20-319">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-319">✔</span></span>
<span data-ttu-id="82c20-320">Geometry. IsWithinDistance (Geometry, double)</span><span class="sxs-lookup"><span data-stu-id="82c20-320">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="82c20-321">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-321">✔</span></span> | | <span data-ttu-id="82c20-322">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-322">✔</span></span> | <span data-ttu-id="82c20-323">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-323">✔</span></span>
<span data-ttu-id="82c20-324">Geometry. Length</span><span class="sxs-lookup"><span data-stu-id="82c20-324">Geometry.Length</span></span> | <span data-ttu-id="82c20-325">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-325">✔</span></span> | <span data-ttu-id="82c20-326">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-326">✔</span></span> | <span data-ttu-id="82c20-327">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-327">✔</span></span> | <span data-ttu-id="82c20-328">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-328">✔</span></span>
<span data-ttu-id="82c20-329">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="82c20-329">Geometry.NumGeometries</span></span> | <span data-ttu-id="82c20-330">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-330">✔</span></span> | <span data-ttu-id="82c20-331">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-331">✔</span></span> | <span data-ttu-id="82c20-332">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-332">✔</span></span> | <span data-ttu-id="82c20-333">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-333">✔</span></span>
<span data-ttu-id="82c20-334">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="82c20-334">Geometry.NumPoints</span></span> | <span data-ttu-id="82c20-335">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-335">✔</span></span> | <span data-ttu-id="82c20-336">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-336">✔</span></span> | <span data-ttu-id="82c20-337">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-337">✔</span></span> | <span data-ttu-id="82c20-338">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-338">✔</span></span>
<span data-ttu-id="82c20-339">Geometry. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="82c20-339">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="82c20-340">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-340">✔</span></span> | <span data-ttu-id="82c20-341">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-341">✔</span></span> | <span data-ttu-id="82c20-342">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-342">✔</span></span> | <span data-ttu-id="82c20-343">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-343">✔</span></span>
<span data-ttu-id="82c20-344">Geometry. chevauchements (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-344">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="82c20-345">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-345">✔</span></span> | <span data-ttu-id="82c20-346">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-346">✔</span></span> | <span data-ttu-id="82c20-347">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-347">✔</span></span> | <span data-ttu-id="82c20-348">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-348">✔</span></span>
<span data-ttu-id="82c20-349">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="82c20-349">Geometry.PointOnSurface</span></span> | <span data-ttu-id="82c20-350">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-350">✔</span></span> | | <span data-ttu-id="82c20-351">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-351">✔</span></span> | <span data-ttu-id="82c20-352">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-352">✔</span></span>
<span data-ttu-id="82c20-353">Geometry. relate (Geometry, String)</span><span class="sxs-lookup"><span data-stu-id="82c20-353">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="82c20-354">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-354">✔</span></span> | | <span data-ttu-id="82c20-355">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-355">✔</span></span> | <span data-ttu-id="82c20-356">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-356">✔</span></span>
<span data-ttu-id="82c20-357">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="82c20-357">Geometry.Reverse()</span></span> | | | <span data-ttu-id="82c20-358">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-358">✔</span></span> | <span data-ttu-id="82c20-359">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-359">✔</span></span>
<span data-ttu-id="82c20-360">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="82c20-360">Geometry.SRID</span></span> | <span data-ttu-id="82c20-361">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-361">✔</span></span> | <span data-ttu-id="82c20-362">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-362">✔</span></span> | <span data-ttu-id="82c20-363">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-363">✔</span></span> | <span data-ttu-id="82c20-364">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-364">✔</span></span>
<span data-ttu-id="82c20-365">Geometry. SymmetricDifference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-365">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="82c20-366">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-366">✔</span></span> | <span data-ttu-id="82c20-367">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-367">✔</span></span> | <span data-ttu-id="82c20-368">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-368">✔</span></span> | <span data-ttu-id="82c20-369">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-369">✔</span></span>
<span data-ttu-id="82c20-370">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="82c20-370">Geometry.ToBinary()</span></span> | <span data-ttu-id="82c20-371">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-371">✔</span></span> | <span data-ttu-id="82c20-372">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-372">✔</span></span> | <span data-ttu-id="82c20-373">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-373">✔</span></span> | <span data-ttu-id="82c20-374">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-374">✔</span></span>
<span data-ttu-id="82c20-375">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="82c20-375">Geometry.ToText()</span></span> | <span data-ttu-id="82c20-376">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-376">✔</span></span> | <span data-ttu-id="82c20-377">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-377">✔</span></span> | <span data-ttu-id="82c20-378">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-378">✔</span></span> | <span data-ttu-id="82c20-379">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-379">✔</span></span>
<span data-ttu-id="82c20-380">Geometry. touche (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-380">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="82c20-381">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-381">✔</span></span> | | <span data-ttu-id="82c20-382">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-382">✔</span></span> | <span data-ttu-id="82c20-383">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-383">✔</span></span>
<span data-ttu-id="82c20-384">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="82c20-384">Geometry.Union()</span></span> | | | <span data-ttu-id="82c20-385">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-385">✔</span></span> | <span data-ttu-id="82c20-386">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-386">✔</span></span>
<span data-ttu-id="82c20-387">Geometry. Union (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-387">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="82c20-388">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-388">✔</span></span> | <span data-ttu-id="82c20-389">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-389">✔</span></span> | <span data-ttu-id="82c20-390">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-390">✔</span></span> | <span data-ttu-id="82c20-391">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-391">✔</span></span>
<span data-ttu-id="82c20-392">Geometry. within (Geometry)</span><span class="sxs-lookup"><span data-stu-id="82c20-392">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="82c20-393">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-393">✔</span></span> | <span data-ttu-id="82c20-394">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-394">✔</span></span> | <span data-ttu-id="82c20-395">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-395">✔</span></span> | <span data-ttu-id="82c20-396">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-396">✔</span></span>
<span data-ttu-id="82c20-397">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="82c20-397">GeometryCollection.Count</span></span> | <span data-ttu-id="82c20-398">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-398">✔</span></span> | <span data-ttu-id="82c20-399">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-399">✔</span></span> | <span data-ttu-id="82c20-400">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-400">✔</span></span> | <span data-ttu-id="82c20-401">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-401">✔</span></span>
<span data-ttu-id="82c20-402">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="82c20-402">GeometryCollection[int]</span></span> | <span data-ttu-id="82c20-403">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-403">✔</span></span> | <span data-ttu-id="82c20-404">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-404">✔</span></span> | <span data-ttu-id="82c20-405">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-405">✔</span></span> | <span data-ttu-id="82c20-406">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-406">✔</span></span>
<span data-ttu-id="82c20-407">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="82c20-407">LineString.Count</span></span> | <span data-ttu-id="82c20-408">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-408">✔</span></span> | <span data-ttu-id="82c20-409">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-409">✔</span></span> | <span data-ttu-id="82c20-410">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-410">✔</span></span> | <span data-ttu-id="82c20-411">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-411">✔</span></span>
<span data-ttu-id="82c20-412">LineString. point de terminaison</span><span class="sxs-lookup"><span data-stu-id="82c20-412">LineString.EndPoint</span></span> | <span data-ttu-id="82c20-413">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-413">✔</span></span> | <span data-ttu-id="82c20-414">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-414">✔</span></span> | <span data-ttu-id="82c20-415">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-415">✔</span></span> | <span data-ttu-id="82c20-416">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-416">✔</span></span>
<span data-ttu-id="82c20-417">LineString. GetPointN (int)</span><span class="sxs-lookup"><span data-stu-id="82c20-417">LineString.GetPointN(int)</span></span> | <span data-ttu-id="82c20-418">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-418">✔</span></span> | <span data-ttu-id="82c20-419">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-419">✔</span></span> | <span data-ttu-id="82c20-420">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-420">✔</span></span> | <span data-ttu-id="82c20-421">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-421">✔</span></span>
<span data-ttu-id="82c20-422">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="82c20-422">LineString.IsClosed</span></span> | <span data-ttu-id="82c20-423">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-423">✔</span></span> | <span data-ttu-id="82c20-424">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-424">✔</span></span> | <span data-ttu-id="82c20-425">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-425">✔</span></span> | <span data-ttu-id="82c20-426">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-426">✔</span></span>
<span data-ttu-id="82c20-427">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="82c20-427">LineString.IsRing</span></span> | <span data-ttu-id="82c20-428">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-428">✔</span></span> | | <span data-ttu-id="82c20-429">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-429">✔</span></span> | <span data-ttu-id="82c20-430">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-430">✔</span></span>
<span data-ttu-id="82c20-431">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="82c20-431">LineString.StartPoint</span></span> | <span data-ttu-id="82c20-432">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-432">✔</span></span> | <span data-ttu-id="82c20-433">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-433">✔</span></span> | <span data-ttu-id="82c20-434">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-434">✔</span></span> | <span data-ttu-id="82c20-435">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-435">✔</span></span>
<span data-ttu-id="82c20-436">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="82c20-436">MultiLineString.IsClosed</span></span> | <span data-ttu-id="82c20-437">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-437">✔</span></span> | <span data-ttu-id="82c20-438">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-438">✔</span></span> | <span data-ttu-id="82c20-439">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-439">✔</span></span> | <span data-ttu-id="82c20-440">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-440">✔</span></span>
<span data-ttu-id="82c20-441">Point. M</span><span class="sxs-lookup"><span data-stu-id="82c20-441">Point.M</span></span> | <span data-ttu-id="82c20-442">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-442">✔</span></span> | <span data-ttu-id="82c20-443">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-443">✔</span></span> | <span data-ttu-id="82c20-444">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-444">✔</span></span> | <span data-ttu-id="82c20-445">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-445">✔</span></span>
<span data-ttu-id="82c20-446">Point. X</span><span class="sxs-lookup"><span data-stu-id="82c20-446">Point.X</span></span> | <span data-ttu-id="82c20-447">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-447">✔</span></span> | <span data-ttu-id="82c20-448">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-448">✔</span></span> | <span data-ttu-id="82c20-449">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-449">✔</span></span> | <span data-ttu-id="82c20-450">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-450">✔</span></span>
<span data-ttu-id="82c20-451">Point. Y</span><span class="sxs-lookup"><span data-stu-id="82c20-451">Point.Y</span></span> | <span data-ttu-id="82c20-452">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-452">✔</span></span> | <span data-ttu-id="82c20-453">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-453">✔</span></span> | <span data-ttu-id="82c20-454">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-454">✔</span></span> | <span data-ttu-id="82c20-455">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-455">✔</span></span>
<span data-ttu-id="82c20-456">Point. Z</span><span class="sxs-lookup"><span data-stu-id="82c20-456">Point.Z</span></span> | <span data-ttu-id="82c20-457">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-457">✔</span></span> | <span data-ttu-id="82c20-458">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-458">✔</span></span> | <span data-ttu-id="82c20-459">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-459">✔</span></span> | <span data-ttu-id="82c20-460">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-460">✔</span></span>
<span data-ttu-id="82c20-461">Polygon. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="82c20-461">Polygon.ExteriorRing</span></span> | <span data-ttu-id="82c20-462">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-462">✔</span></span> | <span data-ttu-id="82c20-463">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-463">✔</span></span> | <span data-ttu-id="82c20-464">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-464">✔</span></span> | <span data-ttu-id="82c20-465">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-465">✔</span></span>
<span data-ttu-id="82c20-466">Polygon. GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="82c20-466">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="82c20-467">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-467">✔</span></span> | <span data-ttu-id="82c20-468">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-468">✔</span></span> | <span data-ttu-id="82c20-469">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-469">✔</span></span> | <span data-ttu-id="82c20-470">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-470">✔</span></span>
<span data-ttu-id="82c20-471">Polygon. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="82c20-471">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="82c20-472">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-472">✔</span></span> | <span data-ttu-id="82c20-473">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-473">✔</span></span> | <span data-ttu-id="82c20-474">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-474">✔</span></span> | <span data-ttu-id="82c20-475">✔</span><span class="sxs-lookup"><span data-stu-id="82c20-475">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82c20-476">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="82c20-476">Additional resources</span></span>

* [<span data-ttu-id="82c20-477">Données spatiales dans SQL Server</span><span class="sxs-lookup"><span data-stu-id="82c20-477">Spatial Data in SQL Server</span></span>](/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="82c20-478">Page d’accueil SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="82c20-478">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="82c20-479">Documentation spatiale npgsql</span><span class="sxs-lookup"><span data-stu-id="82c20-479">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="82c20-480">Documentation PostGIS</span><span class="sxs-lookup"><span data-stu-id="82c20-480">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
