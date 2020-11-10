---
title: Fournisseur de base de données InMemory - EF Core
description: Informations sur le fournisseur de base de données Entity Framework Core InMemory.
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 1af75088ae892e3b428caf6bdb31dd2b750a05fe
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430168"
---
# <a name="ef-core-in-memory-database-provider"></a>Fournisseur de base de données InMemory EF Core

Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec une base de données en mémoire. La base de données en mémoire peut être utile pour le test, bien que le fournisseur SQLite en mode in-Memory puisse être un test de remplacement plus approprié pour les bases de données relationnelles. La base de données en mémoire est conçue à des fins de test uniquement. Il est géré dans le cadre du [projet Entity Framework Core](https://github.com/dotnet/efcore).

## <a name="install"></a>Installer

Installez le [package NuGet Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a>Découvrir

Les ressources suivantes vous permettent de commencer à utiliser ce fournisseur.

* [Test avec InMemory](xref:core/testing/in-memory)
* [Exemple de tests d’application UnicornStore](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a>Moteurs de base de données pris en charge

Base de données de mémoire in-process, conçue à des fins de test uniquement.
