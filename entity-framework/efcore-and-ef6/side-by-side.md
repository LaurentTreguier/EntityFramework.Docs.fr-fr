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
# <a name="using-ef-core-and-ef6-in-the-same-application"></a><span data-ttu-id="090f2-103">Utilisation conjointe d’EF Core et d’EF6 dans la même application</span><span class="sxs-lookup"><span data-stu-id="090f2-103">Using EF Core and EF6 in the Same Application</span></span>

<span data-ttu-id="090f2-104">Il est possible d’utiliser EF Core et EF6 dans la même application ou bibliothèque en installant les deux packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="090f2-104">It is possible to use EF Core and EF6 in the same application or library by installing both NuGet packages.</span></span>

<span data-ttu-id="090f2-105">Certains types portent les mêmes noms dans EF Core et EF6 et se distinguent uniquement par l’espace de noms, ce qui peut compliquer l’utilisation conjointe d’EF Core et d’EF6 dans le même fichier de code.</span><span class="sxs-lookup"><span data-stu-id="090f2-105">Some types have the same names in EF Core and EF6 and differ only by namespace, which may complicate using both EF Core and EF6 in the same code file.</span></span> <span data-ttu-id="090f2-106">L’ambiguïté peut être facilement supprimée en utilisant des directives d’alias d’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="090f2-106">The ambiguity can be easily removed using namespace alias directives.</span></span> <span data-ttu-id="090f2-107">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="090f2-107">For example:</span></span>

``` csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

<span data-ttu-id="090f2-108">Si vous déplacez une application existante qui possède plusieurs modèles EF, vous pouvez, si vous le souhaitez, déplacer spécifiquement certains d'entre eux dans EF Core et continuer à utiliser EF6 pour les autres.</span><span class="sxs-lookup"><span data-stu-id="090f2-108">If you are porting an existing application that has multiple EF models, you can choose to selectively port some of them to EF Core, and continue using EF6 for the others.</span></span>
