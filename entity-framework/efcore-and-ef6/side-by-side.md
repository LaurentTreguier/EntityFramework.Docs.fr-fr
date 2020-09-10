---
title: EF6 et Core EF - Utilisation conjointe dans la même application
description: Aide sur l’utilisation de Entity Framework Core et Entity Framework 6 dans la même application
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a06e3c35-110c-4294-a1e2-32d2c31c90a7
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: 9bd3d837a333eb23be4cb3095b7f387ad303376d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619551"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a>Utilisation conjointe d’EF Core et d’EF6 dans la même application

Il est possible d’utiliser EF Core et EF6 dans la même application ou bibliothèque en installant les deux packages NuGet.

Certains types portent les mêmes noms dans EF Core et EF6 et se distinguent uniquement par l’espace de noms, ce qui peut compliquer l’utilisation conjointe d’EF Core et d’EF6 dans le même fichier de code. L’ambiguïté peut être facilement supprimée en utilisant des directives d’alias d’espace de noms. Par exemple :

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

Si vous déplacez une application existante qui possède plusieurs modèles EF, vous pouvez, si vous le souhaitez, déplacer spécifiquement certains d'entre eux dans EF Core et continuer à utiliser EF6 pour les autres.
