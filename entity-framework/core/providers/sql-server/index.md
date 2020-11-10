---
title: Fournisseur de base de données Microsoft SQL Server - EF Core
description: La documentation du fournisseur de base de données qui permet d’utiliser Entity Framework Core avec Microsoft SQL Server
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: 4118cd5737ece1ad084bb85d409523d217065353
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430320"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="d91e6-103">Fournisseur de base de données EF Core Microsoft SQL Server</span><span class="sxs-lookup"><span data-stu-id="d91e6-103">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="d91e6-104">Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec Microsoft SQL Server (notamment Azure SQL Database).</span><span class="sxs-lookup"><span data-stu-id="d91e6-104">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including Azure SQL Database).</span></span> <span data-ttu-id="d91e6-105">Il est géré dans le cadre du [projet Entity Framework Core](https://github.com/dotnet/efcore).</span><span class="sxs-lookup"><span data-stu-id="d91e6-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="d91e6-106">Installer</span><span class="sxs-lookup"><span data-stu-id="d91e6-106">Install</span></span>

<span data-ttu-id="d91e6-107">Installez le [package NuGet Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="d91e6-107">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="d91e6-108">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="d91e6-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="d91e6-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d91e6-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="d91e6-110">Depuis la version 3.0.0, le fournisseur référence Microsoft.Data.SqlClient (les versions précédentes dépendaient de System.Data.SqlClient).</span><span class="sxs-lookup"><span data-stu-id="d91e6-110">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="d91e6-111">Si votre projet dépend directement de SqlClient, assurez-vous qu’il fait référence au package Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="d91e6-111">If your project takes a direct dependency on SqlClient, make sure it references the Microsoft.Data.SqlClient package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="d91e6-112">Moteurs de base de données pris en charge</span><span class="sxs-lookup"><span data-stu-id="d91e6-112">Supported Database Engines</span></span>

* <span data-ttu-id="d91e6-113">Microsoft SQL Server (versions 2012 et suivantes)</span><span class="sxs-lookup"><span data-stu-id="d91e6-113">Microsoft SQL Server (2012 onwards)</span></span>
