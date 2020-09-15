---
title: Fournisseur de base de données SQLite - EF Core
description: Informations sur le fournisseur de base de données Entity Framework Core SQLite.
author: rowanmiller
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: ba537acdf537fa475378c08b8c6290930b29239d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071236"
---
# <a name="sqlite-ef-core-database-provider"></a>Fournisseur de base de données EF Core SQLite

Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec SQLite. Il est géré dans le cadre du [projet Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).

## <a name="install"></a>Installer

Installez le [package NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a>Moteurs de base de données pris en charge

* SQLite (versions 3.7 et suivantes)

## <a name="limitations"></a>Limites

Consultez [Limitations de SQLite](xref:core/providers/sqlite/limitations) pour connaître certaines limitations importantes du fournisseur SQLite.
