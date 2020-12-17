---
title: Fournisseur de base de données SQLite-données spatiales-EF Core
description: Utilisation de données spatiales avec le fournisseur de base de données Entity Framework Core SQLite
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sqlite/spatial
ms.openlocfilehash: 3296955dc046b91b53a1dcb09c51b340bc853b4a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066623"
---
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a><span data-ttu-id="ace32-103">Données spatiales dans le fournisseur de EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="ace32-103">Spatial Data in the SQLite EF Core Provider</span></span>

<span data-ttu-id="ace32-104">Cette page contient des informations supplémentaires sur l’utilisation de données spatiales avec le fournisseur de base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="ace32-104">This page includes additional information about using spatial data with the SQLite database provider.</span></span> <span data-ttu-id="ace32-105">Pour obtenir des informations générales sur l’utilisation des données spatiales dans EF Core, consultez la documentation principale sur les [données spatiales](xref:core/modeling/spatial) .</span><span class="sxs-lookup"><span data-stu-id="ace32-105">For general information about using spatial data in EF Core, see the main [Spatial Data](xref:core/modeling/spatial) documentation.</span></span>

## <a name="installing-spatialite"></a><span data-ttu-id="ace32-106">Installation de SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="ace32-106">Installing SpatiaLite</span></span>

<span data-ttu-id="ace32-107">Sur Windows, la bibliothèque de mod_spatialite native est distribuée en tant que dépendance de package NuGet.</span><span class="sxs-lookup"><span data-stu-id="ace32-107">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="ace32-108">D’autres plateformes doivent l’installer séparément.</span><span class="sxs-lookup"><span data-stu-id="ace32-108">Other platforms need to install it separately.</span></span> <span data-ttu-id="ace32-109">Cette opération s’effectue généralement à l’aide d’un gestionnaire de package logiciel.</span><span class="sxs-lookup"><span data-stu-id="ace32-109">This is typically done using a software package manager.</span></span> <span data-ttu-id="ace32-110">Par exemple, vous pouvez utiliser APT sur Debian et Ubuntu. et homebrew sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="ace32-110">For example, you can use APT on Debian and Ubuntu; and Homebrew on MacOS.</span></span>

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="ace32-111">Malheureusement, les versions plus récentes de PROJ (une dépendance de SpatiaLite) ne sont pas compatibles avec le [Bundle SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)par défaut d’EF.</span><span class="sxs-lookup"><span data-stu-id="ace32-111">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="ace32-112">Vous pouvez contourner ce cas à l’aide de la bibliothèque SQLite système.</span><span class="sxs-lookup"><span data-stu-id="ace32-112">You can work around this by using the system SQLite library instead.</span></span>

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

<span data-ttu-id="ace32-113">Sur **MacOS**, vous devez également définir une variable d’environnement avant d’exécuter votre application afin qu’elle utilise la version de homebrew de sqlite.</span><span class="sxs-lookup"><span data-stu-id="ace32-113">On **macOS**, you'll also need set an environment variable before running your app so it uses Homebrew's version of SQLite.</span></span> <span data-ttu-id="ace32-114">Dans Visual Studio pour Mac, vous pouvez définir cette **option sous project > Project Options > exécuter les configurations de > > par défaut**</span><span class="sxs-lookup"><span data-stu-id="ace32-114">In Visual Studio for Mac, you can set this under **Project > Project Options > Run > Configurations > Default**</span></span>

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a><span data-ttu-id="ace32-115">Configuration de SRID</span><span class="sxs-lookup"><span data-stu-id="ace32-115">Configuring SRID</span></span>

<span data-ttu-id="ace32-116">Dans SpatiaLite, les colonnes doivent spécifier un SRID par colonne.</span><span class="sxs-lookup"><span data-stu-id="ace32-116">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="ace32-117">La valeur par défaut de SRID est `0` .</span><span class="sxs-lookup"><span data-stu-id="ace32-117">The default SRID is `0`.</span></span> <span data-ttu-id="ace32-118">Spécifiez un autre SRID à l’aide de la méthode ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="ace32-118">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> <span data-ttu-id="ace32-119">4326 fait référence à WGS 84, une norme utilisée dans le GPS et d’autres systèmes géographiques.</span><span class="sxs-lookup"><span data-stu-id="ace32-119">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="dimension"></a><span data-ttu-id="ace32-120">Dimension</span><span class="sxs-lookup"><span data-stu-id="ace32-120">Dimension</span></span>

<span data-ttu-id="ace32-121">La dimension par défaut (ou les ordonnées) d’une colonne est X et Y. Pour activer des ordonnées supplémentaires comme Z ou M, configurez le type de colonne.</span><span class="sxs-lookup"><span data-stu-id="ace32-121">The default dimension (or ordinates) of a column is X and Y. To enable additional ordinates like Z or M, configure the column type.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a><span data-ttu-id="ace32-122">Mappages de fonctions spatiales</span><span class="sxs-lookup"><span data-stu-id="ace32-122">Spatial function mappings</span></span>

<span data-ttu-id="ace32-123">Ce tableau montre les membres [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) traduits dans les fonctions SQL.</span><span class="sxs-lookup"><span data-stu-id="ace32-123">This table shows which [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) members are translated into which SQL functions.</span></span>

<span data-ttu-id="ace32-124">.NET</span><span class="sxs-lookup"><span data-stu-id="ace32-124">.NET</span></span>                                        | <span data-ttu-id="ace32-125">SQL</span><span class="sxs-lookup"><span data-stu-id="ace32-125">SQL</span></span>
------------------------------------------- | ---
<span data-ttu-id="ace32-126">geometr. Partie</span><span class="sxs-lookup"><span data-stu-id="ace32-126">geometry.Area</span></span>                               | <span data-ttu-id="ace32-127">Area ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-127">Area(@geometry)</span></span>
<span data-ttu-id="ace32-128">geometr. AsBinary()</span><span class="sxs-lookup"><span data-stu-id="ace32-128">geometry.AsBinary()</span></span>                         | <span data-ttu-id="ace32-129">AsBinary ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-129">AsBinary(@geometry)</span></span>
<span data-ttu-id="ace32-130">geometr. AsText()</span><span class="sxs-lookup"><span data-stu-id="ace32-130">geometry.AsText()</span></span>                           | <span data-ttu-id="ace32-131">AsText ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-131">AsText(@geometry)</span></span>
<span data-ttu-id="ace32-132">geometr. Bordure</span><span class="sxs-lookup"><span data-stu-id="ace32-132">geometry.Boundary</span></span>                           | <span data-ttu-id="ace32-133">Limite ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-133">Boundary(@geometry)</span></span>
<span data-ttu-id="ace32-134">geometr. Mémoire tampon (distance)</span><span class="sxs-lookup"><span data-stu-id="ace32-134">geometry.Buffer(distance)</span></span>                   | <span data-ttu-id="ace32-135">Mémoire tampon ( @geometry , @distance )</span><span class="sxs-lookup"><span data-stu-id="ace32-135">Buffer(@geometry, @distance)</span></span>
<span data-ttu-id="ace32-136">geometr. Buffer (distance, quadrantSegments)</span><span class="sxs-lookup"><span data-stu-id="ace32-136">geometry.Buffer(distance, quadrantSegments)</span></span> | <span data-ttu-id="ace32-137">Mémoire tampon ( @geometry , @distance , @quadrantSegments )</span><span class="sxs-lookup"><span data-stu-id="ace32-137">Buffer(@geometry, @distance, @quadrantSegments)</span></span>
<span data-ttu-id="ace32-138">geometr. Centroïde</span><span class="sxs-lookup"><span data-stu-id="ace32-138">geometry.Centroid</span></span>                           | <span data-ttu-id="ace32-139">Centre de gravité ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-139">Centroid(@geometry)</span></span>
<span data-ttu-id="ace32-140">geometr. Contient (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-140">geometry.Contains(g)</span></span>                        | <span data-ttu-id="ace32-141">Contient ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-141">Contains(@geometry, @g)</span></span>
<span data-ttu-id="ace32-142">geometr. ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="ace32-142">geometry.ConvexHull()</span></span>                       | <span data-ttu-id="ace32-143">ConvexHull ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-143">ConvexHull(@geometry)</span></span>
<span data-ttu-id="ace32-144">geometr. CoveredBy (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-144">geometry.CoveredBy(g)</span></span>                       | <span data-ttu-id="ace32-145">CoveredBy ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-145">CoveredBy(@geometry, @g)</span></span>
<span data-ttu-id="ace32-146">geometr. Couvertures (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-146">geometry.Covers(g)</span></span>                          | <span data-ttu-id="ace32-147">Couvertures ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-147">Covers(@geometry, @g)</span></span>
<span data-ttu-id="ace32-148">geometr. Croisements (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-148">geometry.Crosses(g)</span></span>                         | <span data-ttu-id="ace32-149">Croisements ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-149">Crosses(@geometry, @g)</span></span>
<span data-ttu-id="ace32-150">geometr. Différence (autre)</span><span class="sxs-lookup"><span data-stu-id="ace32-150">geometry.Difference(other)</span></span>                  | <span data-ttu-id="ace32-151">Différence ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="ace32-151">Difference(@geometry, @other)</span></span>
<span data-ttu-id="ace32-152">geometr. Codes</span><span class="sxs-lookup"><span data-stu-id="ace32-152">geometry.Dimension</span></span>                          | <span data-ttu-id="ace32-153">Dimension ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-153">Dimension(@geometry)</span></span>
<span data-ttu-id="ace32-154">geometr. Disjoint (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-154">geometry.Disjoint(g)</span></span>                        | <span data-ttu-id="ace32-155">Disjoint ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-155">Disjoint(@geometry, @g)</span></span>
<span data-ttu-id="ace32-156">geometr. Distance (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-156">geometry.Distance(g)</span></span>                        | <span data-ttu-id="ace32-157">Distance ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-157">Distance(@geometry, @g)</span></span>
<span data-ttu-id="ace32-158">geometr. Envelope</span><span class="sxs-lookup"><span data-stu-id="ace32-158">geometry.Envelope</span></span>                           | <span data-ttu-id="ace32-159">Enveloppe ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-159">Envelope(@geometry)</span></span>
<span data-ttu-id="ace32-160">geometr. EqualsTopologically (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-160">geometry.EqualsTopologically(g)</span></span>             | <span data-ttu-id="ace32-161">Est égal à ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-161">Equals(@geometry, @g)</span></span>
<span data-ttu-id="ace32-162">geometr. GeometryType</span><span class="sxs-lookup"><span data-stu-id="ace32-162">geometry.GeometryType</span></span>                       | <span data-ttu-id="ace32-163">GeometryType ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-163">GeometryType(@geometry)</span></span>
<span data-ttu-id="ace32-164">geometr. GetGeometryN (n)</span><span class="sxs-lookup"><span data-stu-id="ace32-164">geometry.GetGeometryN(n)</span></span>                    | <span data-ttu-id="ace32-165">Geometry ( @geometry , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="ace32-165">GeometryN(@geometry, @n + 1)</span></span>
<span data-ttu-id="ace32-166">geometr. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="ace32-166">geometry.InteriorPoint</span></span>                      | <span data-ttu-id="ace32-167">PointOnSurface ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-167">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="ace32-168">geometr. Intersection (autre)</span><span class="sxs-lookup"><span data-stu-id="ace32-168">geometry.Intersection(other)</span></span>                | <span data-ttu-id="ace32-169">Intersection ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="ace32-169">Intersection(@geometry, @other)</span></span>
<span data-ttu-id="ace32-170">geometr. Croisements (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-170">geometry.Intersects(g)</span></span>                      | <span data-ttu-id="ace32-171">Croisements ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-171">Intersects(@geometry, @g)</span></span>
<span data-ttu-id="ace32-172">geometr. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="ace32-172">geometry.IsEmpty</span></span>                            | <span data-ttu-id="ace32-173">IsEmpty ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-173">IsEmpty(@geometry)</span></span>
<span data-ttu-id="ace32-174">geometr. IsSimple</span><span class="sxs-lookup"><span data-stu-id="ace32-174">geometry.IsSimple</span></span>                           | <span data-ttu-id="ace32-175">IsSimple ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-175">IsSimple(@geometry)</span></span>
<span data-ttu-id="ace32-176">geometr. IsValid</span><span class="sxs-lookup"><span data-stu-id="ace32-176">geometry.IsValid</span></span>                            | <span data-ttu-id="ace32-177">IsValid ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-177">IsValid(@geometry)</span></span>
<span data-ttu-id="ace32-178">geometr. IsWithinDistance (Geom, distance)</span><span class="sxs-lookup"><span data-stu-id="ace32-178">geometry.IsWithinDistance(geom, distance)</span></span>   | <span data-ttu-id="ace32-179">Distance ( @geometry , @geom ) <= @distance</span><span class="sxs-lookup"><span data-stu-id="ace32-179">Distance(@geometry, @geom) <= @distance</span></span>
<span data-ttu-id="ace32-180">geometr. Base</span><span class="sxs-lookup"><span data-stu-id="ace32-180">geometry.Length</span></span>                             | <span data-ttu-id="ace32-181">GLength ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-181">GLength(@geometry)</span></span>
<span data-ttu-id="ace32-182">geometr. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="ace32-182">geometry.NumGeometries</span></span>                      | <span data-ttu-id="ace32-183">NumGeometries ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-183">NumGeometries(@geometry)</span></span>
<span data-ttu-id="ace32-184">geometr. NumPoints</span><span class="sxs-lookup"><span data-stu-id="ace32-184">geometry.NumPoints</span></span>                          | <span data-ttu-id="ace32-185">NumPoints ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-185">NumPoints(@geometry)</span></span>
<span data-ttu-id="ace32-186">geometr. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="ace32-186">geometry.OgcGeometryType</span></span>                    | <span data-ttu-id="ace32-187">CASE GeometryType ( @geometry ) lorsque « point », puis 1... EFFET</span><span class="sxs-lookup"><span data-stu-id="ace32-187">CASE GeometryType(@geometry) WHEN 'POINT' THEN 1 ... END</span></span>
<span data-ttu-id="ace32-188">geometr. Chevauchements (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-188">geometry.Overlaps(g)</span></span>                        | <span data-ttu-id="ace32-189">Chevauchements ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-189">Overlaps(@geometry, @g)</span></span>
<span data-ttu-id="ace32-190">geometr. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="ace32-190">geometry.PointOnSurface</span></span>                     | <span data-ttu-id="ace32-191">PointOnSurface ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-191">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="ace32-192">geometr. Associer (g, intersectionPattern)</span><span class="sxs-lookup"><span data-stu-id="ace32-192">geometry.Relate(g, intersectionPattern)</span></span>     | <span data-ttu-id="ace32-193">Associer ( @geometry , @g , @intersectionPattern )</span><span class="sxs-lookup"><span data-stu-id="ace32-193">Relate(@geometry, @g, @intersectionPattern)</span></span>
<span data-ttu-id="ace32-194">geometr. Inverse ()</span><span class="sxs-lookup"><span data-stu-id="ace32-194">geometry.Reverse()</span></span>                          | <span data-ttu-id="ace32-195">ST_Reverse ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-195">ST_Reverse(@geometry)</span></span>
<span data-ttu-id="ace32-196">geometr. SRID</span><span class="sxs-lookup"><span data-stu-id="ace32-196">geometry.SRID</span></span>                               | <span data-ttu-id="ace32-197">SRID ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-197">SRID(@geometry)</span></span>
<span data-ttu-id="ace32-198">geometr. SymmetricDifference (autre)</span><span class="sxs-lookup"><span data-stu-id="ace32-198">geometry.SymmetricDifference(other)</span></span>         | <span data-ttu-id="ace32-199">SymDifference ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="ace32-199">SymDifference(@geometry, @other)</span></span>
<span data-ttu-id="ace32-200">geometr. ToBinary()</span><span class="sxs-lookup"><span data-stu-id="ace32-200">geometry.ToBinary()</span></span>                         | <span data-ttu-id="ace32-201">AsBinary ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-201">AsBinary(@geometry)</span></span>
<span data-ttu-id="ace32-202">geometr. ToText()</span><span class="sxs-lookup"><span data-stu-id="ace32-202">geometry.ToText()</span></span>                           | <span data-ttu-id="ace32-203">AsText ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-203">AsText(@geometry)</span></span>
<span data-ttu-id="ace32-204">geometr. Touche (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-204">geometry.Touches(g)</span></span>                         | <span data-ttu-id="ace32-205">Touche ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-205">Touches(@geometry, @g)</span></span>
<span data-ttu-id="ace32-206">geometr. Union ()</span><span class="sxs-lookup"><span data-stu-id="ace32-206">geometry.Union()</span></span>                            | <span data-ttu-id="ace32-207">UnaryUnion ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="ace32-207">UnaryUnion(@geometry)</span></span>
<span data-ttu-id="ace32-208">geometr. Union (autre)</span><span class="sxs-lookup"><span data-stu-id="ace32-208">geometry.Union(other)</span></span>                       | <span data-ttu-id="ace32-209">GUnion ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="ace32-209">GUnion(@geometry, @other)</span></span>
<span data-ttu-id="ace32-210">geometr. Dans (g)</span><span class="sxs-lookup"><span data-stu-id="ace32-210">geometry.Within(g)</span></span>                          | <span data-ttu-id="ace32-211">Dans ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="ace32-211">Within(@geometry, @g)</span></span>
<span data-ttu-id="ace32-212">geometryCollection [i]</span><span class="sxs-lookup"><span data-stu-id="ace32-212">geometryCollection[i]</span></span>                       | <span data-ttu-id="ace32-213">Geometry ( @geometryCollection , @i + 1)</span><span class="sxs-lookup"><span data-stu-id="ace32-213">GeometryN(@geometryCollection, @i + 1)</span></span>
<span data-ttu-id="ace32-214">geometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="ace32-214">geometryCollection.Count</span></span>                    | <span data-ttu-id="ace32-215">NumGeometries ( @geometryCollection )</span><span class="sxs-lookup"><span data-stu-id="ace32-215">NumGeometries(@geometryCollection)</span></span>
<span data-ttu-id="ace32-216">lineString. Count</span><span class="sxs-lookup"><span data-stu-id="ace32-216">lineString.Count</span></span>                            | <span data-ttu-id="ace32-217">NumPoints ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="ace32-217">NumPoints(@lineString)</span></span>
<span data-ttu-id="ace32-218">lineString. point de terminaison</span><span class="sxs-lookup"><span data-stu-id="ace32-218">lineString.EndPoint</span></span>                         | <span data-ttu-id="ace32-219">Point de terminaison ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="ace32-219">EndPoint(@lineString)</span></span>
<span data-ttu-id="ace32-220">lineString. GetPointN (n)</span><span class="sxs-lookup"><span data-stu-id="ace32-220">lineString.GetPointN(n)</span></span>                     | <span data-ttu-id="ace32-221">PointN ( @lineString , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="ace32-221">PointN(@lineString, @n + 1)</span></span>
<span data-ttu-id="ace32-222">lineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="ace32-222">lineString.IsClosed</span></span>                         | <span data-ttu-id="ace32-223">IsClosed ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="ace32-223">IsClosed(@lineString)</span></span>
<span data-ttu-id="ace32-224">lineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="ace32-224">lineString.IsRing</span></span>                           | <span data-ttu-id="ace32-225">IsRing ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="ace32-225">IsRing(@lineString)</span></span>
<span data-ttu-id="ace32-226">lineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="ace32-226">lineString.StartPoint</span></span>                       | <span data-ttu-id="ace32-227">StartPoint ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="ace32-227">StartPoint(@lineString)</span></span>
<span data-ttu-id="ace32-228">multiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="ace32-228">multiLineString.IsClosed</span></span>                    | <span data-ttu-id="ace32-229">IsClosed ( @multiLineString )</span><span class="sxs-lookup"><span data-stu-id="ace32-229">IsClosed(@multiLineString)</span></span>
<span data-ttu-id="ace32-230">point. Lecteur</span><span class="sxs-lookup"><span data-stu-id="ace32-230">point.M</span></span>                                     | <span data-ttu-id="ace32-231">M ( @point )</span><span class="sxs-lookup"><span data-stu-id="ace32-231">M(@point)</span></span>
<span data-ttu-id="ace32-232">point. X</span><span class="sxs-lookup"><span data-stu-id="ace32-232">point.X</span></span>                                     | <span data-ttu-id="ace32-233">X ( @point )</span><span class="sxs-lookup"><span data-stu-id="ace32-233">X(@point)</span></span>
<span data-ttu-id="ace32-234">point. Y</span><span class="sxs-lookup"><span data-stu-id="ace32-234">point.Y</span></span>                                     | <span data-ttu-id="ace32-235">Y ( @point )</span><span class="sxs-lookup"><span data-stu-id="ace32-235">Y(@point)</span></span>
<span data-ttu-id="ace32-236">point. Lettre</span><span class="sxs-lookup"><span data-stu-id="ace32-236">point.Z</span></span>                                     | <span data-ttu-id="ace32-237">Z ( @point )</span><span class="sxs-lookup"><span data-stu-id="ace32-237">Z(@point)</span></span>
<span data-ttu-id="ace32-238">Polygone. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="ace32-238">polygon.ExteriorRing</span></span>                        | <span data-ttu-id="ace32-239">ExteriorRing ( @polygon )</span><span class="sxs-lookup"><span data-stu-id="ace32-239">ExteriorRing(@polygon)</span></span>
<span data-ttu-id="ace32-240">Polygone. GetInteriorRingN (n)</span><span class="sxs-lookup"><span data-stu-id="ace32-240">polygon.GetInteriorRingN(n)</span></span>                 | <span data-ttu-id="ace32-241">InteriorRingN ( @polygon , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="ace32-241">InteriorRingN(@polygon, @n + 1)</span></span>
<span data-ttu-id="ace32-242">Polygone. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="ace32-242">polygon.NumInteriorRings</span></span>                    | <span data-ttu-id="ace32-243">NumInteriorRing ( @polygon )</span><span class="sxs-lookup"><span data-stu-id="ace32-243">NumInteriorRing(@polygon)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ace32-244">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ace32-244">Additional resources</span></span>

* [<span data-ttu-id="ace32-245">Page d’accueil SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="ace32-245">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="ace32-246">Documentation NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="ace32-246">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)