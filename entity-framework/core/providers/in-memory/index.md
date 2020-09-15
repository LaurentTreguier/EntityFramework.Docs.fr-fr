---
title: Fournisseur de base de données InMemory - EF Core
description: Informations sur le fournisseur de base de données Entity Framework Core InMemory.
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: 6af1d61a6ff76b82cc0096edbf095a6338d21109
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619010"
---
# <a name="ef-core-in-memory-database-provider"></a>Fournisseur de base de données InMemory EF Core

Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec une base de données en mémoire. Ceci peut s’avérer utile pour les tests, bien que le fournisseur SQLite en mode en mémoire constitue peut-être un remplacement de test plus approprié pour les bases de données relationnelles. Il est géré dans le cadre du [projet Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).

## <a name="install"></a>Installer

Installez le [package NuGet Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a>Découvrir

Les ressources suivantes vous permettent de commencer à utiliser ce fournisseur.

* [Test avec InMemory](xref:core/miscellaneous/testing/in-memory)
* [Exemple de tests d’application UnicornStore](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a>Moteurs de base de données pris en charge

Base de données en mémoire in-process (conçue uniquement à des fins de test)
