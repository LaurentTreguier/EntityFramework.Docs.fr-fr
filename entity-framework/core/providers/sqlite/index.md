---
title: Fournisseur de base de données SQLite - EF Core
description: Informations sur le fournisseur de base de données Entity Framework Core SQLite.
author: bricelam
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: 2d221168c093271d220d4d8fbc7779c1a5aab701
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063983"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="b72d2-103">Fournisseur de base de données EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="b72d2-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="b72d2-104">Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec SQLite.</span><span class="sxs-lookup"><span data-stu-id="b72d2-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="b72d2-105">Il est géré dans le cadre du [projet Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="b72d2-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="b72d2-106">Installer</span><span class="sxs-lookup"><span data-stu-id="b72d2-106">Install</span></span>

<span data-ttu-id="b72d2-107">Installez le [package NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span><span class="sxs-lookup"><span data-stu-id="b72d2-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="b72d2-108">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b72d2-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="b72d2-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b72d2-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="b72d2-110">Moteurs de base de données pris en charge</span><span class="sxs-lookup"><span data-stu-id="b72d2-110">Supported Database Engines</span></span>

* <span data-ttu-id="b72d2-111">SQLite (versions 3.7 et suivantes)</span><span class="sxs-lookup"><span data-stu-id="b72d2-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="b72d2-112">Limites</span><span class="sxs-lookup"><span data-stu-id="b72d2-112">Limitations</span></span>

<span data-ttu-id="b72d2-113">Consultez [Limitations de SQLite](xref:core/providers/sqlite/limitations) pour connaître certaines limitations importantes du fournisseur SQLite.</span><span class="sxs-lookup"><span data-stu-id="b72d2-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
