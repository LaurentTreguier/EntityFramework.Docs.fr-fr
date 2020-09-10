---
title: Créer et supprimer des API-EF Core
description: API pour la création et la suppression de bases de données avec Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 25e7352269531e881e83e44ea90108f12d4dcbea
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619228"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="b8271-103">Créer et supprimer des API</span><span class="sxs-lookup"><span data-stu-id="b8271-103">Create and Drop APIs</span></span>

<span data-ttu-id="b8271-104">Les méthodes EnsureCreated et EnsureDeleted fournissent une alternative légère aux [migrations](xref:core/managing-schemas/migrations/index) pour la gestion du schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="b8271-104">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](xref:core/managing-schemas/migrations/index) for managing the database schema.</span></span> <span data-ttu-id="b8271-105">Ces méthodes sont utiles dans les scénarios où les données sont temporaires et peuvent être supprimées lorsque le schéma est modifié.</span><span class="sxs-lookup"><span data-stu-id="b8271-105">These methods are useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="b8271-106">Par exemple pendant le prototypage, les tests ou les caches locaux.</span><span class="sxs-lookup"><span data-stu-id="b8271-106">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="b8271-107">Certains fournisseurs (surtout non relationnels) ne prennent pas en charge les migrations.</span><span class="sxs-lookup"><span data-stu-id="b8271-107">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="b8271-108">Pour ces fournisseurs, EnsureCreated est souvent le moyen le plus simple d’initialiser le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="b8271-108">For these providers, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="b8271-109">EnsureCreated et les migrations ne fonctionnent pas correctement ensemble.</span><span class="sxs-lookup"><span data-stu-id="b8271-109">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="b8271-110">Si vous utilisez des migrations, n’utilisez pas EnsureCreated pour initialiser le schéma.</span><span class="sxs-lookup"><span data-stu-id="b8271-110">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="b8271-111">La transition de EnsureCreated à des migrations n’est pas une expérience transparente.</span><span class="sxs-lookup"><span data-stu-id="b8271-111">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="b8271-112">La façon la plus simple de procéder consiste à supprimer la base de données et à la recréer à l’aide des migrations.</span><span class="sxs-lookup"><span data-stu-id="b8271-112">The simplest way to do it is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="b8271-113">Si vous prévoyez d’utiliser des migrations à l’avenir, il est préférable de commencer par les migrations au lieu d’utiliser EnsureCreated.</span><span class="sxs-lookup"><span data-stu-id="b8271-113">If you anticipate using migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="b8271-114">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="b8271-114">EnsureDeleted</span></span>

<span data-ttu-id="b8271-115">La méthode EnsureDeleted supprime la base de données, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="b8271-115">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="b8271-116">Si vous ne disposez pas des autorisations appropriées, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="b8271-116">If you don't have the appropriate permissions, an exception is thrown.</span></span>

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="b8271-117">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="b8271-117">EnsureCreated</span></span>

<span data-ttu-id="b8271-118">EnsureCreated crée la base de données si elle n’existe pas et initialise le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8271-118">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="b8271-119">Si des tables existent (y compris des tables pour une autre classe DbContext), le schéma ne sera pas initialisé.</span><span class="sxs-lookup"><span data-stu-id="b8271-119">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="b8271-120">Les versions asynchrones de ces méthodes sont également disponibles.</span><span class="sxs-lookup"><span data-stu-id="b8271-120">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="b8271-121">Script SQL</span><span class="sxs-lookup"><span data-stu-id="b8271-121">SQL Script</span></span>

<span data-ttu-id="b8271-122">Pour récupérer le SQL utilisé par EnsureCreated, vous pouvez utiliser la méthode GenerateCreateScript.</span><span class="sxs-lookup"><span data-stu-id="b8271-122">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="b8271-123">Plusieurs classes DbContext</span><span class="sxs-lookup"><span data-stu-id="b8271-123">Multiple DbContext classes</span></span>

<span data-ttu-id="b8271-124">EnsureCreated fonctionne uniquement si aucune table n’est présente dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="b8271-124">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="b8271-125">Si nécessaire, vous pouvez écrire votre propre vérification pour voir si le schéma doit être initialisé et utiliser le service IRelationalDatabaseCreator sous-jacent pour initialiser le schéma.</span><span class="sxs-lookup"><span data-stu-id="b8271-125">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
