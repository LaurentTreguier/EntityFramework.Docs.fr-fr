---
title: Données spatiales-EF Core
description: Utilisation de données spatiales dans un modèle de Entity Framework Core
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: 2c0cd7a8acf7e4b58eadf8805afa1fe4a1d6e949
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063931"
---
# <a name="spatial-data"></a><span data-ttu-id="6938c-103">Données spatiales</span><span class="sxs-lookup"><span data-stu-id="6938c-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="6938c-104">Cette fonctionnalité a été ajoutée dans EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="6938c-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="6938c-105">Les données spatiales représentent l’emplacement physique et la forme des objets.</span><span class="sxs-lookup"><span data-stu-id="6938c-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="6938c-106">De nombreuses bases de données prennent en charge ce type de données afin qu’elles puissent être indexées et interrogées avec d’autres données.</span><span class="sxs-lookup"><span data-stu-id="6938c-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="6938c-107">Les scénarios courants incluent l’interrogation d’objets situés à une distance donnée à partir d’un emplacement ou la sélection de l’objet dont la bordure contient un emplacement donné.</span><span class="sxs-lookup"><span data-stu-id="6938c-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="6938c-108">EF Core prend en charge le mappage aux types de données spatiales à l’aide de la bibliothèque spatiale NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="6938c-108">EF Core supports mapping to spatial data types using the NetTopologySuite spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="6938c-109">Installation de</span><span class="sxs-lookup"><span data-stu-id="6938c-109">Installing</span></span>

<span data-ttu-id="6938c-110">Pour pouvoir utiliser les données spatiales avec EF Core, vous devez installer le package NuGet de prise en charge approprié.</span><span class="sxs-lookup"><span data-stu-id="6938c-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="6938c-111">Le package que vous devez installer dépend du fournisseur que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="6938c-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="6938c-112">Fournisseur EF Core</span><span class="sxs-lookup"><span data-stu-id="6938c-112">EF Core Provider</span></span>                        | <span data-ttu-id="6938c-113">Package NuGet spatial</span><span class="sxs-lookup"><span data-stu-id="6938c-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="6938c-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="6938c-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="6938c-115">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="6938c-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="6938c-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="6938c-117">Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="6938c-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="6938c-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="6938c-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="6938c-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="6938c-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="6938c-121">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
<span data-ttu-id="6938c-122">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="6938c-122">Pomelo.EntityFrameworkCore.MySql</span></span>        | [<span data-ttu-id="6938c-123">Pomelo. EntityFrameworkCore. MySql. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-123">Pomelo.EntityFrameworkCore.MySql.NetTopologySuite</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
<span data-ttu-id="6938c-124">Devart.Data.MySql.EFCore</span><span class="sxs-lookup"><span data-stu-id="6938c-124">Devart.Data.MySql.EFCore</span></span>                | [<span data-ttu-id="6938c-125">Devart. Data. MySql. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-125">Devart.Data.MySql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
<span data-ttu-id="6938c-126">Devart.Data.PostgreSql.EFCore</span><span class="sxs-lookup"><span data-stu-id="6938c-126">Devart.Data.PostgreSql.EFCore</span></span>           | [<span data-ttu-id="6938c-127">Devart. Data. PostgreSql. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-127">Devart.Data.PostgreSql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
<span data-ttu-id="6938c-128">Devart.Data.SQLite.EFCore</span><span class="sxs-lookup"><span data-stu-id="6938c-128">Devart.Data.SQLite.EFCore</span></span>               | [<span data-ttu-id="6938c-129">Devart. Data. SQLite. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-129">Devart.Data.SQLite.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
<span data-ttu-id="6938c-130">Teradata.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="6938c-130">Teradata.EntityFrameworkCore</span></span>            | [<span data-ttu-id="6938c-131">Teradata. EntityFrameworkCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-131">Teradata.EntityFrameworkCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a><span data-ttu-id="6938c-132">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-132">NetTopologySuite</span></span>

<span data-ttu-id="6938c-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) est une bibliothèque spatiale pour .net.</span><span class="sxs-lookup"><span data-stu-id="6938c-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) is a spatial library for .NET.</span></span> <span data-ttu-id="6938c-134">EF Core permet le mappage aux types de données spatiales dans la base de données à l’aide des types NTS dans votre modèle.</span><span class="sxs-lookup"><span data-stu-id="6938c-134">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="6938c-135">Pour activer le mappage aux types spatiaux via NTS, appelez la méthode UseNetTopologySuite sur le générateur d’options DbContext du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="6938c-135">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="6938c-136">Par exemple, avec SQL Server vous l’appellerez comme ceci.</span><span class="sxs-lookup"><span data-stu-id="6938c-136">For example, with SQL Server you'd call it like this.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

<span data-ttu-id="6938c-137">Il existe plusieurs types de données spatiales.</span><span class="sxs-lookup"><span data-stu-id="6938c-137">There are several spatial data types.</span></span> <span data-ttu-id="6938c-138">Le type que vous utilisez dépend des types de formes que vous souhaitez autoriser.</span><span class="sxs-lookup"><span data-stu-id="6938c-138">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="6938c-139">Voici la hiérarchie des types NTS que vous pouvez utiliser pour les propriétés de votre modèle.</span><span class="sxs-lookup"><span data-stu-id="6938c-139">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="6938c-140">Ils sont situés dans l' `NetTopologySuite.Geometries` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="6938c-140">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="6938c-141">Géométrie</span><span class="sxs-lookup"><span data-stu-id="6938c-141">Geometry</span></span>
  * <span data-ttu-id="6938c-142">Point</span><span class="sxs-lookup"><span data-stu-id="6938c-142">Point</span></span>
  * <span data-ttu-id="6938c-143">LineString</span><span class="sxs-lookup"><span data-stu-id="6938c-143">LineString</span></span>
  * <span data-ttu-id="6938c-144">Polygone</span><span class="sxs-lookup"><span data-stu-id="6938c-144">Polygon</span></span>
  * <span data-ttu-id="6938c-145">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="6938c-145">GeometryCollection</span></span>
    * <span data-ttu-id="6938c-146">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="6938c-146">MultiPoint</span></span>
    * <span data-ttu-id="6938c-147">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="6938c-147">MultiLineString</span></span>
    * <span data-ttu-id="6938c-148">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="6938c-148">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="6938c-149">CircularString, CompoundCurve et CurePolygon ne sont pas pris en charge par NTS.</span><span class="sxs-lookup"><span data-stu-id="6938c-149">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="6938c-150">L’utilisation du type Geometry de base permet à n’importe quel type de forme d’être spécifié par la propriété.</span><span class="sxs-lookup"><span data-stu-id="6938c-150">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

## <a name="longitude-and-latitude"></a><span data-ttu-id="6938c-151">Longitude et Latitude</span><span class="sxs-lookup"><span data-stu-id="6938c-151">Longitude and Latitude</span></span>

<span data-ttu-id="6938c-152">Les coordonnées en NTS sont exprimées en valeurs X et Y.</span><span class="sxs-lookup"><span data-stu-id="6938c-152">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="6938c-153">Pour représenter la longitude et la latitude, utilisez X pour la longitude et Y pour la latitude.</span><span class="sxs-lookup"><span data-stu-id="6938c-153">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="6938c-154">Notez que cette valeur est **retournée** à partir du `latitude, longitude` format dans lequel vous voyez généralement ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="6938c-154">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

## <a name="querying-data"></a><span data-ttu-id="6938c-155">Interrogation des données</span><span class="sxs-lookup"><span data-stu-id="6938c-155">Querying Data</span></span>

<span data-ttu-id="6938c-156">Les classes d’entité suivantes peuvent être utilisées pour mapper à des tables dans l' [exemple de base de données grand World](https://go.microsoft.com/fwlink/?LinkID=800630)Importers.</span><span class="sxs-lookup"><span data-stu-id="6938c-156">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

<span data-ttu-id="6938c-157">Dans LINQ, les méthodes et propriétés NTS disponibles en tant que fonctions de base de données sont traduites en SQL.</span><span class="sxs-lookup"><span data-stu-id="6938c-157">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="6938c-158">Par exemple, les méthodes distance et Contains sont traduites dans les requêtes suivantes.</span><span class="sxs-lookup"><span data-stu-id="6938c-158">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="6938c-159">Consultez la documentation de votre fournisseur pour connaître les méthodes prises en charge.</span><span class="sxs-lookup"><span data-stu-id="6938c-159">See your provider's documentation for which methods are supported.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a><span data-ttu-id="6938c-160">Rétroconception</span><span class="sxs-lookup"><span data-stu-id="6938c-160">Reverse engineering</span></span>

<span data-ttu-id="6938c-161">Les packages NuGet spatiaux activent également les modèles d' [ingénierie à rebours](xref:core/managing-schemas/scaffolding) avec des propriétés spatiales, mais vous devez installer le package ***avant*** d’exécuter `Scaffold-DbContext` ou `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="6938c-161">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="6938c-162">Si vous ne le souhaitez pas, vous recevrez des avertissements sur les mappages de type pour les colonnes et les colonnes seront ignorées.</span><span class="sxs-lookup"><span data-stu-id="6938c-162">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="6938c-163">SRID ignoré pendant les opérations du client</span><span class="sxs-lookup"><span data-stu-id="6938c-163">SRID Ignored during client operations</span></span>

<span data-ttu-id="6938c-164">NTS ignore les valeurs SRID lors des opérations.</span><span class="sxs-lookup"><span data-stu-id="6938c-164">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="6938c-165">Il part du principe qu’il s’agit d’un système de coordonnées planaire.</span><span class="sxs-lookup"><span data-stu-id="6938c-165">It assumes a planar coordinate system.</span></span> <span data-ttu-id="6938c-166">Cela signifie que si vous spécifiez des coordonnées en termes de longitude et de latitude, certaines valeurs évaluées par le client, telles que la distance, la longueur et la zone, seront en degrés, et non en mètres.</span><span class="sxs-lookup"><span data-stu-id="6938c-166">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="6938c-167">Pour les valeurs plus significatives, vous devez d’abord projeter les coordonnées dans un autre système de coordonnées à l’aide d’une bibliothèque comme [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) avant de calculer ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="6938c-167">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="6938c-168">Si une opération est évaluée par le serveur par EF Core via SQL, l’unité du résultat est déterminée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="6938c-168">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="6938c-169">Voici un exemple d’utilisation de ProjNet4GeoAPI pour calculer la distance entre deux villes.</span><span class="sxs-lookup"><span data-stu-id="6938c-169">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> <span data-ttu-id="6938c-170">4326 fait référence à WGS 84, une norme utilisée dans le GPS et d’autres systèmes géographiques.</span><span class="sxs-lookup"><span data-stu-id="6938c-170">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6938c-171">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6938c-171">Additional resources</span></span>

<span data-ttu-id="6938c-172">Veillez à lire la documentation de votre fournisseur pour plus d’informations sur l’utilisation des données spatiales.</span><span class="sxs-lookup"><span data-stu-id="6938c-172">Be sure to read your provider's documentation for additional information on working with spatial data.</span></span>

* [<span data-ttu-id="6938c-173">Données spatiales dans le fournisseur SQL Server</span><span class="sxs-lookup"><span data-stu-id="6938c-173">Spatial Data in the SQL Server Provider</span></span>](xref:core/providers/sql-server/spatial)
* [<span data-ttu-id="6938c-174">Données spatiales dans le fournisseur SQLite</span><span class="sxs-lookup"><span data-stu-id="6938c-174">Spatial Data in the SQLite Provider</span></span>](xref:core/providers/sqlite/spatial)
* [<span data-ttu-id="6938c-175">Données spatiales dans le fournisseur npgsql</span><span class="sxs-lookup"><span data-stu-id="6938c-175">Spatial Data in the Npgsql Provider</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="6938c-176">Documentation NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="6938c-176">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)
