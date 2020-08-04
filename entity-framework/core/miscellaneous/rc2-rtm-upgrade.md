---
title: Mise à niveau de EF Core 1,0 RC2 vers RTM-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: f496806ea6330c60cf43068882b7de839e18e383
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526769"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="a1aeb-102">Mise à niveau de EF Core 1,0 RC2 vers la version RTM</span><span class="sxs-lookup"><span data-stu-id="a1aeb-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="a1aeb-103">Cet article fournit des conseils pour déplacer une application générée avec les packages RC2 vers 1.0.0 RTM.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="a1aeb-104">Versions de package</span><span class="sxs-lookup"><span data-stu-id="a1aeb-104">Package Versions</span></span>

<span data-ttu-id="a1aeb-105">Les noms des packages de niveau supérieur que vous installez généralement dans une application n’ont pas changé entre RC2 et RTM.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="a1aeb-106">**Vous devez mettre à niveau les packages installés vers les versions RTM :**</span><span class="sxs-lookup"><span data-stu-id="a1aeb-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="a1aeb-107">Les packages d’exécution (par exemple, `Microsoft.EntityFrameworkCore.SqlServer` ) ont été remplacés par `1.0.0-rc2-final` `1.0.0` .</span><span class="sxs-lookup"><span data-stu-id="a1aeb-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="a1aeb-108">Le `Microsoft.EntityFrameworkCore.Tools` package a été remplacé par `1.0.0-preview1-final` `1.0.0-preview2-final` .</span><span class="sxs-lookup"><span data-stu-id="a1aeb-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="a1aeb-109">Notez que les outils sont toujours en version préliminaire.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="a1aeb-110">Des migrations existantes peuvent nécessiter l’ajout de maxLength</span><span class="sxs-lookup"><span data-stu-id="a1aeb-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="a1aeb-111">Dans RC2, la définition de colonne d’une migration a été recherchée, `table.Column<string>(nullable: true)` et la longueur de la colonne a été recherchée dans certaines métadonnées que nous stockons dans le code de la migration.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="a1aeb-112">Dans RTM, la longueur est désormais incluse dans le code de génération de modèles automatique `table.Column<string>(maxLength: 450, nullable: true)` .</span><span class="sxs-lookup"><span data-stu-id="a1aeb-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="a1aeb-113">L’argument n’est pas spécifié pour les migrations existantes qui ont été créées avant l’utilisation de RTM `maxLength` .</span><span class="sxs-lookup"><span data-stu-id="a1aeb-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="a1aeb-114">Cela signifie que la longueur maximale prise en charge par la base de données sera utilisée ( `nvarchar(max)` sur SQL Server).</span><span class="sxs-lookup"><span data-stu-id="a1aeb-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="a1aeb-115">Cela peut s’avérer parfait pour certaines colonnes, mais les colonnes qui font partie d’une clé, d’une clé étrangère ou d’un index doivent être mises à jour pour inclure une longueur maximale.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="a1aeb-116">Par Convention, 450 est la longueur maximale utilisée pour les clés, les clés étrangères et les colonnes indexées.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="a1aeb-117">Si vous avez configuré explicitement une longueur dans le modèle, vous devez utiliser cette longueur à la place.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

### <a name="aspnet-identity"></a><span data-ttu-id="a1aeb-118">ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="a1aeb-118">ASP.NET Identity</span></span>

<span data-ttu-id="a1aeb-119">Cette modification a un impact sur les projets qui utilisent ASP.NET Identity et ont été créés à partir d’un modèle de projet antérieur à la version RTM.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="a1aeb-120">Le modèle de projet comprend une migration utilisée pour créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="a1aeb-121">Cette migration doit être modifiée pour spécifier une longueur maximale de `256` pour les colonnes suivantes.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

* <span data-ttu-id="a1aeb-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="a1aeb-122">**AspNetRoles**</span></span>
  * <span data-ttu-id="a1aeb-123">Name</span><span class="sxs-lookup"><span data-stu-id="a1aeb-123">Name</span></span>
  * <span data-ttu-id="a1aeb-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="a1aeb-124">NormalizedName</span></span>
* <span data-ttu-id="a1aeb-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="a1aeb-125">**AspNetUsers**</span></span>
  * <span data-ttu-id="a1aeb-126">Courrier</span><span class="sxs-lookup"><span data-stu-id="a1aeb-126">Email</span></span>
  * <span data-ttu-id="a1aeb-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="a1aeb-127">NormalizedEmail</span></span>
  * <span data-ttu-id="a1aeb-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="a1aeb-128">NormalizedUserName</span></span>
  * <span data-ttu-id="a1aeb-129">UserName</span><span class="sxs-lookup"><span data-stu-id="a1aeb-129">UserName</span></span>

<span data-ttu-id="a1aeb-130">Si vous n’effectuez pas cette modification, l’exception suivante se produit lorsque la migration initiale est appliquée à une base de données.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="a1aeb-131">.NET Core : supprimez « Imports » dans project.jssur</span><span class="sxs-lookup"><span data-stu-id="a1aeb-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="a1aeb-132">Si vous ciblez .NET Core avec RC2, vous deviez ajouter `imports` à project.jssous la forme d’une solution de contournement temporaire pour certaines des dépendances de EF Core qui ne prennent pas en charge .NET standard.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="a1aeb-133">Vous pouvez maintenant les supprimer.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-133">These can now be removed.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> <span data-ttu-id="a1aeb-134">Depuis la version 1,0 RTM, le [Kit SDK .net Core](https://www.microsoft.com/net/download/core) ne prend plus en charge `project.json` ni le développement d’applications .net core à l’aide de Visual Studio 2015.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="a1aeb-135">Nous vous recommandons de [migrer de project.json vers csproj](/dotnet/articles/core/migration/).</span><span class="sxs-lookup"><span data-stu-id="a1aeb-135">We recommend you [migrate from project.json to csproj](/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="a1aeb-136">Si vous utilisez Visual Studio, nous vous recommandons de mettre à niveau vers [Visual studio 2017](https://www.visualstudio.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="a1aeb-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="a1aeb-137">UWP : ajouter des redirections de liaison</span><span class="sxs-lookup"><span data-stu-id="a1aeb-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="a1aeb-138">Si vous tentez d’exécuter des commandes EF sur des projets plateforme Windows universelle (UWP), l’erreur suivante se produit :</span><span class="sxs-lookup"><span data-stu-id="a1aeb-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

<span data-ttu-id="a1aeb-139">Vous devez ajouter manuellement les redirections de liaison au projet UWP.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="a1aeb-140">Créez un fichier nommé `App.config` dans le dossier racine du projet et ajoutez des redirections aux versions d’assembly appropriées.</span><span class="sxs-lookup"><span data-stu-id="a1aeb-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

```xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```
