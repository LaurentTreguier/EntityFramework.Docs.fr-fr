---
title: Fournisseur de base de données SQLite - EF Core
description: Informations sur le fournisseur de base de données Entity Framework Core SQLite.
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: 8620f0e37825368cb3d7965a05118ab1297fb9e8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616566"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="64a8b-103">Fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="64a8b-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="64a8b-104">Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec SQLite.</span><span class="sxs-lookup"><span data-stu-id="64a8b-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="64a8b-105">Il est géré dans le cadre du [projet Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="64a8b-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="64a8b-106">Installer</span><span class="sxs-lookup"><span data-stu-id="64a8b-106">Install</span></span>

<span data-ttu-id="64a8b-107">Installez le [package NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span><span class="sxs-lookup"><span data-stu-id="64a8b-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="64a8b-108">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="64a8b-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="64a8b-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64a8b-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="64a8b-110">Moteurs de base de données pris en charge</span><span class="sxs-lookup"><span data-stu-id="64a8b-110">Supported Database Engines</span></span>

* <span data-ttu-id="64a8b-111">SQLite (versions 3.7 et suivantes)</span><span class="sxs-lookup"><span data-stu-id="64a8b-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="64a8b-112">Limites</span><span class="sxs-lookup"><span data-stu-id="64a8b-112">Limitations</span></span>

<span data-ttu-id="64a8b-113">Consultez [Limitations de SQLite](xref:core/providers/sqlite/limitations) pour connaître certaines limitations importantes du fournisseur SQLite.</span><span class="sxs-lookup"><span data-stu-id="64a8b-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
