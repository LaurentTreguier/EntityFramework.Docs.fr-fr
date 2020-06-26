---
title: Données spatiales-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 2222df84be7bfde3f252766bef1cfab39b476efa
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370446"
---
# <a name="spatial-data"></a><span data-ttu-id="5489f-102">Données spatiales</span><span class="sxs-lookup"><span data-stu-id="5489f-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="5489f-103">Cette fonctionnalité a été ajoutée dans EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="5489f-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="5489f-104">Les données spatiales représentent l’emplacement physique et la forme des objets.</span><span class="sxs-lookup"><span data-stu-id="5489f-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="5489f-105">De nombreuses bases de données prennent en charge ce type de données afin qu’elles puissent être indexées et interrogées avec d’autres données.</span><span class="sxs-lookup"><span data-stu-id="5489f-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="5489f-106">Les scénarios courants incluent l’interrogation d’objets situés à une distance donnée à partir d’un emplacement ou la sélection de l’objet dont la bordure contient un emplacement donné.</span><span class="sxs-lookup"><span data-stu-id="5489f-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="5489f-107">EF Core prend en charge le mappage aux types de données spatiales à l’aide de la bibliothèque spatiale [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="5489f-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="5489f-108">En cours d'installation</span><span class="sxs-lookup"><span data-stu-id="5489f-108">Installing</span></span>

<span data-ttu-id="5489f-109">Pour pouvoir utiliser les données spatiales avec EF Core, vous devez installer le package NuGet de prise en charge approprié.</span><span class="sxs-lookup"><span data-stu-id="5489f-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="5489f-110">Le package que vous devez installer dépend du fournisseur que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="5489f-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="5489f-111">Fournisseur EF Core</span><span class="sxs-lookup"><span data-stu-id="5489f-111">EF Core Provider</span></span>                        | <span data-ttu-id="5489f-112">Package NuGet spatial</span><span class="sxs-lookup"><span data-stu-id="5489f-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="5489f-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="5489f-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="5489f-114">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="5489f-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="5489f-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="5489f-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="5489f-116">Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="5489f-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="5489f-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="5489f-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="5489f-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="5489f-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="5489f-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="5489f-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="5489f-120">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="5489f-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="5489f-121">Rétroconception</span><span class="sxs-lookup"><span data-stu-id="5489f-121">Reverse engineering</span></span>

<span data-ttu-id="5489f-122">Les packages NuGet spatiaux activent également les modèles d' [ingénierie à rebours](../managing-schemas/scaffolding.md) avec des propriétés spatiales, mais vous devez installer le package ***avant*** d’exécuter `Scaffold-DbContext` ou `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="5489f-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="5489f-123">Si vous ne le souhaitez pas, vous recevrez des avertissements sur les mappages de type pour les colonnes et les colonnes seront ignorées.</span><span class="sxs-lookup"><span data-stu-id="5489f-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="5489f-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="5489f-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="5489f-125">NetTopologySuite est une bibliothèque spatiale pour .NET.</span><span class="sxs-lookup"><span data-stu-id="5489f-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="5489f-126">EF Core permet le mappage aux types de données spatiales dans la base de données à l’aide des types NTS dans votre modèle.</span><span class="sxs-lookup"><span data-stu-id="5489f-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="5489f-127">Pour activer le mappage aux types spatiaux via NTS, appelez la méthode UseNetTopologySuite sur le générateur d’options DbContext du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="5489f-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="5489f-128">Par exemple, avec SQL Server vous l’appellerez comme ceci.</span><span class="sxs-lookup"><span data-stu-id="5489f-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="5489f-129">Il existe plusieurs types de données spatiales.</span><span class="sxs-lookup"><span data-stu-id="5489f-129">There are several spatial data types.</span></span> <span data-ttu-id="5489f-130">Le type que vous utilisez dépend des types de formes que vous souhaitez autoriser.</span><span class="sxs-lookup"><span data-stu-id="5489f-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="5489f-131">Voici la hiérarchie des types NTS que vous pouvez utiliser pour les propriétés de votre modèle.</span><span class="sxs-lookup"><span data-stu-id="5489f-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="5489f-132">Ils sont situés dans l' `NetTopologySuite.Geometries` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="5489f-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="5489f-133">Géométrie</span><span class="sxs-lookup"><span data-stu-id="5489f-133">Geometry</span></span>
  * <span data-ttu-id="5489f-134">Point</span><span class="sxs-lookup"><span data-stu-id="5489f-134">Point</span></span>
  * <span data-ttu-id="5489f-135">LineString</span><span class="sxs-lookup"><span data-stu-id="5489f-135">LineString</span></span>
  * <span data-ttu-id="5489f-136">Polygone</span><span class="sxs-lookup"><span data-stu-id="5489f-136">Polygon</span></span>
  * <span data-ttu-id="5489f-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="5489f-137">GeometryCollection</span></span>
    * <span data-ttu-id="5489f-138">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="5489f-138">MultiPoint</span></span>
    * <span data-ttu-id="5489f-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="5489f-139">MultiLineString</span></span>
    * <span data-ttu-id="5489f-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="5489f-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="5489f-141">CircularString, CompoundCurve et CurePolygon ne sont pas pris en charge par NTS.</span><span class="sxs-lookup"><span data-stu-id="5489f-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="5489f-142">L’utilisation du type Geometry de base permet à n’importe quel type de forme d’être spécifié par la propriété.</span><span class="sxs-lookup"><span data-stu-id="5489f-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="5489f-143">Les classes d’entité suivantes peuvent être utilisées pour mapper à des tables dans l' [exemple de base de données grand World](https://go.microsoft.com/fwlink/?LinkID=800630)Importers.</span><span class="sxs-lookup"><span data-stu-id="5489f-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="5489f-144">Création de valeurs</span><span class="sxs-lookup"><span data-stu-id="5489f-144">Creating values</span></span>

<span data-ttu-id="5489f-145">Vous pouvez utiliser des constructeurs pour créer des objets Geometry ; Toutefois, NTS recommande d’utiliser à la place une fabrique de géométrie.</span><span class="sxs-lookup"><span data-stu-id="5489f-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="5489f-146">Cela vous permet de spécifier un SRID par défaut (le système de référence spatiale utilisé par les coordonnées) et vous donne le contrôle sur des éléments plus avancés tels que le modèle de précision (utilisé lors des calculs) et la séquence de coordonnées (détermine les ordonnées--dimensions et mesures, qui sont disponibles).</span><span class="sxs-lookup"><span data-stu-id="5489f-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="5489f-147">4326 fait référence à WGS 84, une norme utilisée dans le GPS et d’autres systèmes géographiques.</span><span class="sxs-lookup"><span data-stu-id="5489f-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="5489f-148">Longitude et Latitude</span><span class="sxs-lookup"><span data-stu-id="5489f-148">Longitude and Latitude</span></span>

<span data-ttu-id="5489f-149">Les coordonnées en NTS sont exprimées en valeurs X et Y.</span><span class="sxs-lookup"><span data-stu-id="5489f-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="5489f-150">Pour représenter la longitude et la latitude, utilisez X pour la longitude et Y pour la latitude.</span><span class="sxs-lookup"><span data-stu-id="5489f-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="5489f-151">Notez que cette valeur est **retournée** à partir du `latitude, longitude` format dans lequel vous voyez généralement ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="5489f-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="5489f-152">SRID ignoré pendant les opérations du client</span><span class="sxs-lookup"><span data-stu-id="5489f-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="5489f-153">NTS ignore les valeurs SRID lors des opérations.</span><span class="sxs-lookup"><span data-stu-id="5489f-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="5489f-154">Il part du principe qu’il s’agit d’un système de coordonnées planaire.</span><span class="sxs-lookup"><span data-stu-id="5489f-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="5489f-155">Cela signifie que si vous spécifiez des coordonnées en termes de longitude et de latitude, certaines valeurs évaluées par le client, telles que la distance, la longueur et la zone, seront en degrés, et non en mètres.</span><span class="sxs-lookup"><span data-stu-id="5489f-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="5489f-156">Pour les valeurs plus significatives, vous devez d’abord projeter les coordonnées dans un autre système de coordonnées à l’aide d’une bibliothèque comme [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) avant de calculer ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="5489f-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="5489f-157">Si une opération est évaluée par le serveur par EF Core via SQL, l’unité du résultat est déterminée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="5489f-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="5489f-158">Voici un exemple d’utilisation de ProjNet4GeoAPI pour calculer la distance entre deux villes.</span><span class="sxs-lookup"><span data-stu-id="5489f-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="5489f-159">Interrogation des données</span><span class="sxs-lookup"><span data-stu-id="5489f-159">Querying Data</span></span>

<span data-ttu-id="5489f-160">Dans LINQ, les méthodes et propriétés NTS disponibles en tant que fonctions de base de données sont traduites en SQL.</span><span class="sxs-lookup"><span data-stu-id="5489f-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="5489f-161">Par exemple, les méthodes distance et Contains sont traduites dans les requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="5489f-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="5489f-162">Le tableau à la fin de cet article indique quels membres sont pris en charge par différents fournisseurs de EF Core.</span><span class="sxs-lookup"><span data-stu-id="5489f-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="5489f-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="5489f-163">SQL Server</span></span>

<span data-ttu-id="5489f-164">Si vous utilisez SQL Server, vous devez connaître certaines choses supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="5489f-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="5489f-165">Geography ou Geometry</span><span class="sxs-lookup"><span data-stu-id="5489f-165">Geography or geometry</span></span>

<span data-ttu-id="5489f-166">Par défaut, les propriétés spatiales sont mappées aux `geography` colonnes dans SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5489f-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="5489f-167">Pour `geometry` ce faire, [configurez le type de colonne](xref:core/modeling/entity-properties#column-data-types) dans votre modèle.</span><span class="sxs-lookup"><span data-stu-id="5489f-167">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="5489f-168">Anneaux de polygone Geography</span><span class="sxs-lookup"><span data-stu-id="5489f-168">Geography polygon rings</span></span>

<span data-ttu-id="5489f-169">Lors de l’utilisation du `geography` type de colonne, SQL Server impose des exigences supplémentaires sur l’anneau extérieur (ou l’interpréteur de commandes) et les anneaux intérieurs (ou trous).</span><span class="sxs-lookup"><span data-stu-id="5489f-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="5489f-170">L’anneau extérieur doit être orienté vers le sens inverse des aiguilles d’une montre et des anneaux intérieurs.</span><span class="sxs-lookup"><span data-stu-id="5489f-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="5489f-171">NTS valide cette valeur avant d’envoyer des valeurs à la base de données.</span><span class="sxs-lookup"><span data-stu-id="5489f-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="5489f-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="5489f-172">FullGlobe</span></span>

<span data-ttu-id="5489f-173">SQL Server a un type Geometry non standard pour représenter le globe complet lors de l’utilisation du `geography` type de colonne.</span><span class="sxs-lookup"><span data-stu-id="5489f-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="5489f-174">Il offre également un moyen de représenter les polygones en fonction du monde entier (sans anneau extérieur).</span><span class="sxs-lookup"><span data-stu-id="5489f-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="5489f-175">Aucun de ces deux n’est pris en charge par NTS.</span><span class="sxs-lookup"><span data-stu-id="5489f-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="5489f-176">Les FullGlobe et les polygones basés sur ce dernier ne sont pas pris en charge par NTS.</span><span class="sxs-lookup"><span data-stu-id="5489f-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="5489f-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="5489f-177">SQLite</span></span>

<span data-ttu-id="5489f-178">Voici quelques informations supplémentaires pour celles qui utilisent SQLite.</span><span class="sxs-lookup"><span data-stu-id="5489f-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="5489f-179">Installation de SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="5489f-179">Installing SpatiaLite</span></span>

<span data-ttu-id="5489f-180">Sur Windows, la bibliothèque de mod_spatialite native est distribuée en tant que dépendance de package NuGet.</span><span class="sxs-lookup"><span data-stu-id="5489f-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="5489f-181">D’autres plateformes doivent l’installer séparément.</span><span class="sxs-lookup"><span data-stu-id="5489f-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="5489f-182">Cette opération s’effectue généralement à l’aide d’un gestionnaire de package logiciel.</span><span class="sxs-lookup"><span data-stu-id="5489f-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="5489f-183">Par exemple, vous pouvez utiliser la fonction APT sur Ubuntu et homebrew sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="5489f-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="5489f-184">Malheureusement, les versions plus récentes de PROJ (une dépendance de SpatiaLite) ne sont pas compatibles avec le [Bundle SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)par défaut d’EF.</span><span class="sxs-lookup"><span data-stu-id="5489f-184">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="5489f-185">Vous pouvez contourner ce contournement en créant un [fournisseur SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) personnalisé qui utilise la bibliothèque SQLite système, ou vous pouvez installer une build personnalisée de SpatiaLite la désactivation de la prise en charge de proj.</span><span class="sxs-lookup"><span data-stu-id="5489f-185">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="5489f-186">Configuration de SRID</span><span class="sxs-lookup"><span data-stu-id="5489f-186">Configuring SRID</span></span>

<span data-ttu-id="5489f-187">Dans SpatiaLite, les colonnes doivent spécifier un SRID par colonne.</span><span class="sxs-lookup"><span data-stu-id="5489f-187">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="5489f-188">La valeur par défaut de SRID est `0` .</span><span class="sxs-lookup"><span data-stu-id="5489f-188">The default SRID is `0`.</span></span> <span data-ttu-id="5489f-189">Spécifiez un autre SRID à l’aide de la méthode ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="5489f-189">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="5489f-190">Dimension</span><span class="sxs-lookup"><span data-stu-id="5489f-190">Dimension</span></span>

<span data-ttu-id="5489f-191">Comme pour SRID, la dimension d’une colonne (ou ordonnée) est également spécifiée dans le cadre de la colonne.</span><span class="sxs-lookup"><span data-stu-id="5489f-191">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="5489f-192">Les ordonnées par défaut sont X et Y. Activez des ordonnées supplémentaires (Z et M) à l’aide de la méthode ForSqliteHasDimension.</span><span class="sxs-lookup"><span data-stu-id="5489f-192">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="5489f-193">Opérations traduites</span><span class="sxs-lookup"><span data-stu-id="5489f-193">Translated Operations</span></span>

<span data-ttu-id="5489f-194">Ce tableau montre les membres NTS qui sont convertis en SQL par chaque fournisseur de EF Core.</span><span class="sxs-lookup"><span data-stu-id="5489f-194">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="5489f-195">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="5489f-195">NetTopologySuite</span></span> | <span data-ttu-id="5489f-196">SQL Server (géométrie)</span><span class="sxs-lookup"><span data-stu-id="5489f-196">SQL Server (geometry)</span></span> | <span data-ttu-id="5489f-197">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="5489f-197">SQL Server (geography)</span></span> | <span data-ttu-id="5489f-198">SQLite</span><span class="sxs-lookup"><span data-stu-id="5489f-198">SQLite</span></span> | <span data-ttu-id="5489f-199">Npgsql</span><span class="sxs-lookup"><span data-stu-id="5489f-199">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="5489f-200">Geometry. Area</span><span class="sxs-lookup"><span data-stu-id="5489f-200">Geometry.Area</span></span> | <span data-ttu-id="5489f-201">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-201">✔</span></span> | <span data-ttu-id="5489f-202">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-202">✔</span></span> | <span data-ttu-id="5489f-203">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-203">✔</span></span> | <span data-ttu-id="5489f-204">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-204">✔</span></span>
<span data-ttu-id="5489f-205">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="5489f-205">Geometry.AsBinary()</span></span> | <span data-ttu-id="5489f-206">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-206">✔</span></span> | <span data-ttu-id="5489f-207">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-207">✔</span></span> | <span data-ttu-id="5489f-208">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-208">✔</span></span> | <span data-ttu-id="5489f-209">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-209">✔</span></span>
<span data-ttu-id="5489f-210">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="5489f-210">Geometry.AsText()</span></span> | <span data-ttu-id="5489f-211">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-211">✔</span></span> | <span data-ttu-id="5489f-212">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-212">✔</span></span> | <span data-ttu-id="5489f-213">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-213">✔</span></span> | <span data-ttu-id="5489f-214">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-214">✔</span></span>
<span data-ttu-id="5489f-215">Geometry. Boundary</span><span class="sxs-lookup"><span data-stu-id="5489f-215">Geometry.Boundary</span></span> | <span data-ttu-id="5489f-216">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-216">✔</span></span> | | <span data-ttu-id="5489f-217">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-217">✔</span></span> | <span data-ttu-id="5489f-218">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-218">✔</span></span>
<span data-ttu-id="5489f-219">Geometry. buffer (double)</span><span class="sxs-lookup"><span data-stu-id="5489f-219">Geometry.Buffer(double)</span></span> | <span data-ttu-id="5489f-220">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-220">✔</span></span> | <span data-ttu-id="5489f-221">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-221">✔</span></span> | <span data-ttu-id="5489f-222">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-222">✔</span></span> | <span data-ttu-id="5489f-223">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-223">✔</span></span>
<span data-ttu-id="5489f-224">Geometry. buffer (double, int)</span><span class="sxs-lookup"><span data-stu-id="5489f-224">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="5489f-225">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-225">✔</span></span> | <span data-ttu-id="5489f-226">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-226">✔</span></span>
<span data-ttu-id="5489f-227">Geometry. Centre de gravité</span><span class="sxs-lookup"><span data-stu-id="5489f-227">Geometry.Centroid</span></span> | <span data-ttu-id="5489f-228">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-228">✔</span></span> | | <span data-ttu-id="5489f-229">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-229">✔</span></span> | <span data-ttu-id="5489f-230">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-230">✔</span></span>
<span data-ttu-id="5489f-231">Geometry. Contains (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-231">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="5489f-232">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-232">✔</span></span> | <span data-ttu-id="5489f-233">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-233">✔</span></span> | <span data-ttu-id="5489f-234">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-234">✔</span></span> | <span data-ttu-id="5489f-235">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-235">✔</span></span>
<span data-ttu-id="5489f-236">Geometry. ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="5489f-236">Geometry.ConvexHull()</span></span> | <span data-ttu-id="5489f-237">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-237">✔</span></span> | <span data-ttu-id="5489f-238">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-238">✔</span></span> | <span data-ttu-id="5489f-239">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-239">✔</span></span> | <span data-ttu-id="5489f-240">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-240">✔</span></span>
<span data-ttu-id="5489f-241">Geometry. CoveredBy (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-241">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="5489f-242">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-242">✔</span></span> | <span data-ttu-id="5489f-243">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-243">✔</span></span>
<span data-ttu-id="5489f-244">Geometry. couvertures (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-244">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="5489f-245">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-245">✔</span></span> | <span data-ttu-id="5489f-246">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-246">✔</span></span>
<span data-ttu-id="5489f-247">Geometry. crosses (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-247">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="5489f-248">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-248">✔</span></span> | | <span data-ttu-id="5489f-249">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-249">✔</span></span> | <span data-ttu-id="5489f-250">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-250">✔</span></span>
<span data-ttu-id="5489f-251">Geometry. difference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-251">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="5489f-252">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-252">✔</span></span> | <span data-ttu-id="5489f-253">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-253">✔</span></span> | <span data-ttu-id="5489f-254">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-254">✔</span></span> | <span data-ttu-id="5489f-255">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-255">✔</span></span>
<span data-ttu-id="5489f-256">Geometry. dimension</span><span class="sxs-lookup"><span data-stu-id="5489f-256">Geometry.Dimension</span></span> | <span data-ttu-id="5489f-257">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-257">✔</span></span> | <span data-ttu-id="5489f-258">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-258">✔</span></span> | <span data-ttu-id="5489f-259">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-259">✔</span></span> | <span data-ttu-id="5489f-260">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-260">✔</span></span>
<span data-ttu-id="5489f-261">Geometry. disjointe (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-261">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="5489f-262">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-262">✔</span></span> | <span data-ttu-id="5489f-263">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-263">✔</span></span> | <span data-ttu-id="5489f-264">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-264">✔</span></span> | <span data-ttu-id="5489f-265">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-265">✔</span></span>
<span data-ttu-id="5489f-266">Geometry. distance (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-266">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="5489f-267">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-267">✔</span></span> | <span data-ttu-id="5489f-268">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-268">✔</span></span> | <span data-ttu-id="5489f-269">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-269">✔</span></span> | <span data-ttu-id="5489f-270">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-270">✔</span></span>
<span data-ttu-id="5489f-271">Geometry. Envelope</span><span class="sxs-lookup"><span data-stu-id="5489f-271">Geometry.Envelope</span></span> | <span data-ttu-id="5489f-272">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-272">✔</span></span> | | <span data-ttu-id="5489f-273">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-273">✔</span></span> | <span data-ttu-id="5489f-274">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-274">✔</span></span>
<span data-ttu-id="5489f-275">Geometry. EqualsExact (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-275">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="5489f-276">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-276">✔</span></span>
<span data-ttu-id="5489f-277">Geometry. EqualsTopologically (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-277">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="5489f-278">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-278">✔</span></span> | <span data-ttu-id="5489f-279">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-279">✔</span></span> | <span data-ttu-id="5489f-280">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-280">✔</span></span> | <span data-ttu-id="5489f-281">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-281">✔</span></span>
<span data-ttu-id="5489f-282">Geometry. GeometryType</span><span class="sxs-lookup"><span data-stu-id="5489f-282">Geometry.GeometryType</span></span> | <span data-ttu-id="5489f-283">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-283">✔</span></span> | <span data-ttu-id="5489f-284">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-284">✔</span></span> | <span data-ttu-id="5489f-285">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-285">✔</span></span> | <span data-ttu-id="5489f-286">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-286">✔</span></span>
<span data-ttu-id="5489f-287">Geometry. GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="5489f-287">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="5489f-288">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-288">✔</span></span> | | <span data-ttu-id="5489f-289">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-289">✔</span></span> | <span data-ttu-id="5489f-290">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-290">✔</span></span>
<span data-ttu-id="5489f-291">Geometry. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="5489f-291">Geometry.InteriorPoint</span></span> | <span data-ttu-id="5489f-292">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-292">✔</span></span> | | <span data-ttu-id="5489f-293">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-293">✔</span></span> | <span data-ttu-id="5489f-294">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-294">✔</span></span>
<span data-ttu-id="5489f-295">Geometry. intersection (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-295">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="5489f-296">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-296">✔</span></span> | <span data-ttu-id="5489f-297">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-297">✔</span></span> | <span data-ttu-id="5489f-298">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-298">✔</span></span> | <span data-ttu-id="5489f-299">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-299">✔</span></span>
<span data-ttu-id="5489f-300">Geometry. intersections (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-300">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="5489f-301">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-301">✔</span></span> | <span data-ttu-id="5489f-302">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-302">✔</span></span> | <span data-ttu-id="5489f-303">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-303">✔</span></span> | <span data-ttu-id="5489f-304">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-304">✔</span></span>
<span data-ttu-id="5489f-305">Geometry. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="5489f-305">Geometry.IsEmpty</span></span> | <span data-ttu-id="5489f-306">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-306">✔</span></span> | <span data-ttu-id="5489f-307">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-307">✔</span></span> | <span data-ttu-id="5489f-308">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-308">✔</span></span> | <span data-ttu-id="5489f-309">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-309">✔</span></span>
<span data-ttu-id="5489f-310">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="5489f-310">Geometry.IsSimple</span></span> | <span data-ttu-id="5489f-311">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-311">✔</span></span> | | <span data-ttu-id="5489f-312">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-312">✔</span></span> | <span data-ttu-id="5489f-313">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-313">✔</span></span>
<span data-ttu-id="5489f-314">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="5489f-314">Geometry.IsValid</span></span> | <span data-ttu-id="5489f-315">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-315">✔</span></span> | <span data-ttu-id="5489f-316">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-316">✔</span></span> | <span data-ttu-id="5489f-317">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-317">✔</span></span> | <span data-ttu-id="5489f-318">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-318">✔</span></span>
<span data-ttu-id="5489f-319">Geometry. IsWithinDistance (Geometry, double)</span><span class="sxs-lookup"><span data-stu-id="5489f-319">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="5489f-320">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-320">✔</span></span> | | <span data-ttu-id="5489f-321">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-321">✔</span></span> | <span data-ttu-id="5489f-322">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-322">✔</span></span>
<span data-ttu-id="5489f-323">Geometry. Length</span><span class="sxs-lookup"><span data-stu-id="5489f-323">Geometry.Length</span></span> | <span data-ttu-id="5489f-324">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-324">✔</span></span> | <span data-ttu-id="5489f-325">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-325">✔</span></span> | <span data-ttu-id="5489f-326">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-326">✔</span></span> | <span data-ttu-id="5489f-327">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-327">✔</span></span>
<span data-ttu-id="5489f-328">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="5489f-328">Geometry.NumGeometries</span></span> | <span data-ttu-id="5489f-329">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-329">✔</span></span> | <span data-ttu-id="5489f-330">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-330">✔</span></span> | <span data-ttu-id="5489f-331">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-331">✔</span></span> | <span data-ttu-id="5489f-332">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-332">✔</span></span>
<span data-ttu-id="5489f-333">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="5489f-333">Geometry.NumPoints</span></span> | <span data-ttu-id="5489f-334">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-334">✔</span></span> | <span data-ttu-id="5489f-335">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-335">✔</span></span> | <span data-ttu-id="5489f-336">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-336">✔</span></span> | <span data-ttu-id="5489f-337">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-337">✔</span></span>
<span data-ttu-id="5489f-338">Geometry. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="5489f-338">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="5489f-339">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-339">✔</span></span> | <span data-ttu-id="5489f-340">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-340">✔</span></span> | <span data-ttu-id="5489f-341">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-341">✔</span></span> | <span data-ttu-id="5489f-342">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-342">✔</span></span>
<span data-ttu-id="5489f-343">Geometry. chevauchements (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-343">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="5489f-344">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-344">✔</span></span> | <span data-ttu-id="5489f-345">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-345">✔</span></span> | <span data-ttu-id="5489f-346">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-346">✔</span></span> | <span data-ttu-id="5489f-347">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-347">✔</span></span>
<span data-ttu-id="5489f-348">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="5489f-348">Geometry.PointOnSurface</span></span> | <span data-ttu-id="5489f-349">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-349">✔</span></span> | | <span data-ttu-id="5489f-350">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-350">✔</span></span> | <span data-ttu-id="5489f-351">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-351">✔</span></span>
<span data-ttu-id="5489f-352">Geometry. relate (Geometry, String)</span><span class="sxs-lookup"><span data-stu-id="5489f-352">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="5489f-353">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-353">✔</span></span> | | <span data-ttu-id="5489f-354">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-354">✔</span></span> | <span data-ttu-id="5489f-355">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-355">✔</span></span>
<span data-ttu-id="5489f-356">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="5489f-356">Geometry.Reverse()</span></span> | | | <span data-ttu-id="5489f-357">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-357">✔</span></span> | <span data-ttu-id="5489f-358">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-358">✔</span></span>
<span data-ttu-id="5489f-359">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="5489f-359">Geometry.SRID</span></span> | <span data-ttu-id="5489f-360">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-360">✔</span></span> | <span data-ttu-id="5489f-361">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-361">✔</span></span> | <span data-ttu-id="5489f-362">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-362">✔</span></span> | <span data-ttu-id="5489f-363">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-363">✔</span></span>
<span data-ttu-id="5489f-364">Geometry. SymmetricDifference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-364">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="5489f-365">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-365">✔</span></span> | <span data-ttu-id="5489f-366">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-366">✔</span></span> | <span data-ttu-id="5489f-367">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-367">✔</span></span> | <span data-ttu-id="5489f-368">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-368">✔</span></span>
<span data-ttu-id="5489f-369">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="5489f-369">Geometry.ToBinary()</span></span> | <span data-ttu-id="5489f-370">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-370">✔</span></span> | <span data-ttu-id="5489f-371">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-371">✔</span></span> | <span data-ttu-id="5489f-372">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-372">✔</span></span> | <span data-ttu-id="5489f-373">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-373">✔</span></span>
<span data-ttu-id="5489f-374">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="5489f-374">Geometry.ToText()</span></span> | <span data-ttu-id="5489f-375">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-375">✔</span></span> | <span data-ttu-id="5489f-376">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-376">✔</span></span> | <span data-ttu-id="5489f-377">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-377">✔</span></span> | <span data-ttu-id="5489f-378">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-378">✔</span></span>
<span data-ttu-id="5489f-379">Geometry. touche (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-379">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="5489f-380">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-380">✔</span></span> | | <span data-ttu-id="5489f-381">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-381">✔</span></span> | <span data-ttu-id="5489f-382">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-382">✔</span></span>
<span data-ttu-id="5489f-383">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="5489f-383">Geometry.Union()</span></span> | | | <span data-ttu-id="5489f-384">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-384">✔</span></span> | <span data-ttu-id="5489f-385">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-385">✔</span></span>
<span data-ttu-id="5489f-386">Geometry. Union (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-386">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="5489f-387">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-387">✔</span></span> | <span data-ttu-id="5489f-388">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-388">✔</span></span> | <span data-ttu-id="5489f-389">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-389">✔</span></span> | <span data-ttu-id="5489f-390">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-390">✔</span></span>
<span data-ttu-id="5489f-391">Geometry. within (Geometry)</span><span class="sxs-lookup"><span data-stu-id="5489f-391">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="5489f-392">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-392">✔</span></span> | <span data-ttu-id="5489f-393">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-393">✔</span></span> | <span data-ttu-id="5489f-394">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-394">✔</span></span> | <span data-ttu-id="5489f-395">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-395">✔</span></span>
<span data-ttu-id="5489f-396">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="5489f-396">GeometryCollection.Count</span></span> | <span data-ttu-id="5489f-397">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-397">✔</span></span> | <span data-ttu-id="5489f-398">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-398">✔</span></span> | <span data-ttu-id="5489f-399">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-399">✔</span></span> | <span data-ttu-id="5489f-400">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-400">✔</span></span>
<span data-ttu-id="5489f-401">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="5489f-401">GeometryCollection[int]</span></span> | <span data-ttu-id="5489f-402">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-402">✔</span></span> | <span data-ttu-id="5489f-403">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-403">✔</span></span> | <span data-ttu-id="5489f-404">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-404">✔</span></span> | <span data-ttu-id="5489f-405">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-405">✔</span></span>
<span data-ttu-id="5489f-406">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="5489f-406">LineString.Count</span></span> | <span data-ttu-id="5489f-407">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-407">✔</span></span> | <span data-ttu-id="5489f-408">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-408">✔</span></span> | <span data-ttu-id="5489f-409">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-409">✔</span></span> | <span data-ttu-id="5489f-410">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-410">✔</span></span>
<span data-ttu-id="5489f-411">LineString. point de terminaison</span><span class="sxs-lookup"><span data-stu-id="5489f-411">LineString.EndPoint</span></span> | <span data-ttu-id="5489f-412">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-412">✔</span></span> | <span data-ttu-id="5489f-413">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-413">✔</span></span> | <span data-ttu-id="5489f-414">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-414">✔</span></span> | <span data-ttu-id="5489f-415">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-415">✔</span></span>
<span data-ttu-id="5489f-416">LineString. GetPointN (int)</span><span class="sxs-lookup"><span data-stu-id="5489f-416">LineString.GetPointN(int)</span></span> | <span data-ttu-id="5489f-417">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-417">✔</span></span> | <span data-ttu-id="5489f-418">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-418">✔</span></span> | <span data-ttu-id="5489f-419">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-419">✔</span></span> | <span data-ttu-id="5489f-420">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-420">✔</span></span>
<span data-ttu-id="5489f-421">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="5489f-421">LineString.IsClosed</span></span> | <span data-ttu-id="5489f-422">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-422">✔</span></span> | <span data-ttu-id="5489f-423">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-423">✔</span></span> | <span data-ttu-id="5489f-424">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-424">✔</span></span> | <span data-ttu-id="5489f-425">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-425">✔</span></span>
<span data-ttu-id="5489f-426">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="5489f-426">LineString.IsRing</span></span> | <span data-ttu-id="5489f-427">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-427">✔</span></span> | | <span data-ttu-id="5489f-428">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-428">✔</span></span> | <span data-ttu-id="5489f-429">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-429">✔</span></span>
<span data-ttu-id="5489f-430">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="5489f-430">LineString.StartPoint</span></span> | <span data-ttu-id="5489f-431">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-431">✔</span></span> | <span data-ttu-id="5489f-432">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-432">✔</span></span> | <span data-ttu-id="5489f-433">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-433">✔</span></span> | <span data-ttu-id="5489f-434">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-434">✔</span></span>
<span data-ttu-id="5489f-435">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="5489f-435">MultiLineString.IsClosed</span></span> | <span data-ttu-id="5489f-436">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-436">✔</span></span> | <span data-ttu-id="5489f-437">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-437">✔</span></span> | <span data-ttu-id="5489f-438">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-438">✔</span></span> | <span data-ttu-id="5489f-439">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-439">✔</span></span>
<span data-ttu-id="5489f-440">Point. M</span><span class="sxs-lookup"><span data-stu-id="5489f-440">Point.M</span></span> | <span data-ttu-id="5489f-441">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-441">✔</span></span> | <span data-ttu-id="5489f-442">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-442">✔</span></span> | <span data-ttu-id="5489f-443">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-443">✔</span></span> | <span data-ttu-id="5489f-444">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-444">✔</span></span>
<span data-ttu-id="5489f-445">Point. X</span><span class="sxs-lookup"><span data-stu-id="5489f-445">Point.X</span></span> | <span data-ttu-id="5489f-446">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-446">✔</span></span> | <span data-ttu-id="5489f-447">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-447">✔</span></span> | <span data-ttu-id="5489f-448">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-448">✔</span></span> | <span data-ttu-id="5489f-449">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-449">✔</span></span>
<span data-ttu-id="5489f-450">Point. Y</span><span class="sxs-lookup"><span data-stu-id="5489f-450">Point.Y</span></span> | <span data-ttu-id="5489f-451">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-451">✔</span></span> | <span data-ttu-id="5489f-452">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-452">✔</span></span> | <span data-ttu-id="5489f-453">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-453">✔</span></span> | <span data-ttu-id="5489f-454">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-454">✔</span></span>
<span data-ttu-id="5489f-455">Point. Z</span><span class="sxs-lookup"><span data-stu-id="5489f-455">Point.Z</span></span> | <span data-ttu-id="5489f-456">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-456">✔</span></span> | <span data-ttu-id="5489f-457">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-457">✔</span></span> | <span data-ttu-id="5489f-458">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-458">✔</span></span> | <span data-ttu-id="5489f-459">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-459">✔</span></span>
<span data-ttu-id="5489f-460">Polygon. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="5489f-460">Polygon.ExteriorRing</span></span> | <span data-ttu-id="5489f-461">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-461">✔</span></span> | <span data-ttu-id="5489f-462">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-462">✔</span></span> | <span data-ttu-id="5489f-463">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-463">✔</span></span> | <span data-ttu-id="5489f-464">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-464">✔</span></span>
<span data-ttu-id="5489f-465">Polygon. GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="5489f-465">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="5489f-466">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-466">✔</span></span> | <span data-ttu-id="5489f-467">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-467">✔</span></span> | <span data-ttu-id="5489f-468">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-468">✔</span></span> | <span data-ttu-id="5489f-469">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-469">✔</span></span>
<span data-ttu-id="5489f-470">Polygon. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="5489f-470">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="5489f-471">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-471">✔</span></span> | <span data-ttu-id="5489f-472">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-472">✔</span></span> | <span data-ttu-id="5489f-473">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-473">✔</span></span> | <span data-ttu-id="5489f-474">✔</span><span class="sxs-lookup"><span data-stu-id="5489f-474">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5489f-475">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5489f-475">Additional resources</span></span>

* [<span data-ttu-id="5489f-476">Données spatiales dans SQL Server</span><span class="sxs-lookup"><span data-stu-id="5489f-476">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="5489f-477">Page d’accueil SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="5489f-477">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="5489f-478">Documentation spatiale npgsql</span><span class="sxs-lookup"><span data-stu-id="5489f-478">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="5489f-479">Documentation PostGIS</span><span class="sxs-lookup"><span data-stu-id="5489f-479">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
