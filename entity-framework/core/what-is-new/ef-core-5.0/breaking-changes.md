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
# <a name="breaking-changes-in-ef-core-50"></a>Modifications avec rupture dans EF Core 5,0

Les modifications d’API et de comportement suivantes peuvent bloquer les mises à jour des applications existantes en EF Core 5.0.0.

## <a name="summary"></a>Résumé

| **Modification critique**                                                                                                               | **Impact** |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | Faible        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Suppression de la méthode HasGeometricDimension de l’extension NTS SQLite

[#14257 du problème de suivi](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Ancien comportement**

HasGeometricDimension a été utilisé pour activer des dimensions supplémentaires (Z et M) sur les colonnes Geometry. Toutefois, il n’a jamais été affecté par la création de bases de données. Il n’était pas nécessaire de le spécifier pour interroger des valeurs avec des dimensions supplémentaires. Elle ne fonctionnait pas non plus correctement lors de l’insertion ou de la mise à jour de valeurs avec des dimensions supplémentaires ([voir #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Nouveau comportement**

Pour permettre l’insertion et la mise à jour de valeurs géométriques avec des dimensions supplémentaires (Z et M), la dimension doit être spécifiée en tant que partie du nom du type de colonne. Cela correspond plus précisément au comportement sous-jacent de la fonction AddGeometryColumn de SpatiaLite.

**Pourquoi**

L’utilisation de HasGeometricDimension après avoir spécifié la dimension dans le type de colonne est inutile et redondante, donc nous avons supprimé HasGeometricDimension entièrement.

**Corrections**

Utilisez `HasColumnType` pour spécifier la dimension :

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
