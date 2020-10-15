---
title: Fournisseur de base de données InMemory - EF Core
description: Informations sur le fournisseur de base de données Entity Framework Core InMemory.
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 1857ebbfa0eded1572220825a5b0d75961bcf3dd
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064009"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="c4525-103">Fournisseur de base de données InMemory EF Core</span><span class="sxs-lookup"><span data-stu-id="c4525-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="c4525-104">Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="c4525-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="c4525-105">La base de données en mémoire peut être utile pour le test, bien que le fournisseur SQLite en mode in-Memory puisse être un test de remplacement plus approprié pour les bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="c4525-105">The in-memory database can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="c4525-106">La base de données en mémoire est conçue à des fins de test uniquement.</span><span class="sxs-lookup"><span data-stu-id="c4525-106">The in-memory database is designed for testing only.</span></span> <span data-ttu-id="c4525-107">Il est géré dans le cadre du [projet Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="c4525-107">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="c4525-108">Installer</span><span class="sxs-lookup"><span data-stu-id="c4525-108">Install</span></span>

<span data-ttu-id="c4525-109">Installez le [package NuGet Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span><span class="sxs-lookup"><span data-stu-id="c4525-109">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c4525-110">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="c4525-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="c4525-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4525-111">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="c4525-112">Découvrir</span><span class="sxs-lookup"><span data-stu-id="c4525-112">Get Started</span></span>

<span data-ttu-id="c4525-113">Les ressources suivantes vous permettent de commencer à utiliser ce fournisseur.</span><span class="sxs-lookup"><span data-stu-id="c4525-113">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="c4525-114">Test avec InMemory</span><span class="sxs-lookup"><span data-stu-id="c4525-114">Testing with InMemory</span></span>](xref:core/miscellaneous/testing/in-memory)
* [<span data-ttu-id="c4525-115">Exemple de tests d’application UnicornStore</span><span class="sxs-lookup"><span data-stu-id="c4525-115">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="c4525-116">Moteurs de base de données pris en charge</span><span class="sxs-lookup"><span data-stu-id="c4525-116">Supported Database Engines</span></span>

<span data-ttu-id="c4525-117">Base de données de mémoire in-process, conçue à des fins de test uniquement.</span><span class="sxs-lookup"><span data-stu-id="c4525-117">In-process memory database, designed for testing purposes only.</span></span>
