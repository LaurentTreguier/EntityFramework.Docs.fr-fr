---
title: Modifications avec rupture dans EF Core 5,0-EF Core
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666194"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="acb63-102">Modifications avec rupture dans EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="acb63-102">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="acb63-103">Les modifications d’API et de comportement suivantes peuvent bloquer les mises à jour des applications existantes en EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="acb63-103">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="acb63-104">Résumé</span><span class="sxs-lookup"><span data-stu-id="acb63-104">Summary</span></span>

| <span data-ttu-id="acb63-105">**Modification critique**</span><span class="sxs-lookup"><span data-stu-id="acb63-105">**Breaking change**</span></span>                                                                                                               | <span data-ttu-id="acb63-106">**Impact**</span><span class="sxs-lookup"><span data-stu-id="acb63-106">**Impact**</span></span> |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="acb63-107">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="acb63-107">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | <span data-ttu-id="acb63-108">Faible</span><span class="sxs-lookup"><span data-stu-id="acb63-108">Low</span></span>        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="acb63-109">Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite</span><span class="sxs-lookup"><span data-stu-id="acb63-109">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="acb63-110">#14257 du problème de suivi</span><span class="sxs-lookup"><span data-stu-id="acb63-110">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="acb63-111">**Ancien comportement**</span><span class="sxs-lookup"><span data-stu-id="acb63-111">**Old behavior**</span></span>

<span data-ttu-id="acb63-112">HasGeometricDimension a été utilisé pour activer des dimensions supplémentaires (Z et M) sur les colonnes Geometry.</span><span class="sxs-lookup"><span data-stu-id="acb63-112">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="acb63-113">Toutefois, il n’a jamais été affecté par la création de bases de données.</span><span class="sxs-lookup"><span data-stu-id="acb63-113">However, it only ever affected database creation.</span></span> <span data-ttu-id="acb63-114">Il n’était pas nécessaire de le spécifier pour interroger des valeurs avec des dimensions supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="acb63-114">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="acb63-115">Elle ne fonctionnait pas non plus correctement lors de l’insertion ou de la mise à jour de valeurs avec des dimensions supplémentaires ([voir #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="acb63-115">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="acb63-116">**Nouveau comportement**</span><span class="sxs-lookup"><span data-stu-id="acb63-116">**New behavior**</span></span>

<span data-ttu-id="acb63-117">Pour permettre l’insertion et la mise à jour de valeurs géométriques avec des dimensions supplémentaires (Z et M), la dimension doit être spécifiée en tant que partie du nom du type de colonne.</span><span class="sxs-lookup"><span data-stu-id="acb63-117">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="acb63-118">Cela correspond plus précisément au comportement sous-jacent de la fonction AddGeometryColumn de SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="acb63-118">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="acb63-119">**Pourquoi**</span><span class="sxs-lookup"><span data-stu-id="acb63-119">**Why**</span></span>

<span data-ttu-id="acb63-120">L’utilisation de HasGeometricDimension après avoir spécifié la dimension dans le type de colonne est inutile et redondante, donc nous avons supprimé HasGeometricDimension entièrement.</span><span class="sxs-lookup"><span data-stu-id="acb63-120">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="acb63-121">**Corrections**</span><span class="sxs-lookup"><span data-stu-id="acb63-121">**Mitigations**</span></span>

<span data-ttu-id="acb63-122">Utilisez `HasColumnType` pour spécifier la dimension :</span><span class="sxs-lookup"><span data-stu-id="acb63-122">Use `HasColumnType` to specify the dimension:</span></span>

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```
