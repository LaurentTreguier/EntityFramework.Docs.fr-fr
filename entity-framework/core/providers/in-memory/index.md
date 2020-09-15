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
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="6e55e-103">Fournisseur de base de données InMemory EF Core</span><span class="sxs-lookup"><span data-stu-id="6e55e-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="6e55e-104">Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="6e55e-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="6e55e-105">Ceci peut s’avérer utile pour les tests, bien que le fournisseur SQLite en mode en mémoire constitue peut-être un remplacement de test plus approprié pour les bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="6e55e-105">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="6e55e-106">Il est géré dans le cadre du [projet Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="6e55e-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="6e55e-107">Installer</span><span class="sxs-lookup"><span data-stu-id="6e55e-107">Install</span></span>

<span data-ttu-id="6e55e-108">Installez le [package NuGet Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span><span class="sxs-lookup"><span data-stu-id="6e55e-108">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="6e55e-109">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="6e55e-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="6e55e-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e55e-110">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="6e55e-111">Découvrir</span><span class="sxs-lookup"><span data-stu-id="6e55e-111">Get Started</span></span>

<span data-ttu-id="6e55e-112">Les ressources suivantes vous permettent de commencer à utiliser ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="6e55e-112">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="6e55e-113">Test avec InMemory</span><span class="sxs-lookup"><span data-stu-id="6e55e-113">Testing with InMemory</span></span>](xref:core/miscellaneous/testing/in-memory)
* [<span data-ttu-id="6e55e-114">Exemple de tests d’application UnicornStore</span><span class="sxs-lookup"><span data-stu-id="6e55e-114">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="6e55e-115">Moteurs de base de données pris en charge</span><span class="sxs-lookup"><span data-stu-id="6e55e-115">Supported Database Engines</span></span>

<span data-ttu-id="6e55e-116">Base de données en mémoire in-process (conçue uniquement à des fins de test)</span><span class="sxs-lookup"><span data-stu-id="6e55e-116">In-process memory database (designed for testing purposes only)</span></span>
