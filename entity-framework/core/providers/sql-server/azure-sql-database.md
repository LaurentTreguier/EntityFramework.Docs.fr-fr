---
title: Fournisseur de base de données Microsoft SQL Server-options de Azure SQL Database-EF Core
description: Comment spécifier les niveaux de service et de performances pour Azure SQL Database avec le fournisseur de base de données SQL Server Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: 3863a5a224ba26df8cb319844bc2af4158d2497a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618952"
---
# <a name="specifying-azure-sql-database-options"></a><span data-ttu-id="3a41f-103">Spécification des options Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="3a41f-103">Specifying Azure SQL Database Options</span></span>

>[!NOTE]
> <span data-ttu-id="3a41f-104">Cette API est nouvelle dans EF Core 3,1.</span><span class="sxs-lookup"><span data-stu-id="3a41f-104">This API is new in EF Core 3.1.</span></span>

<span data-ttu-id="3a41f-105">Azure SQL Database propose [diverses options de tarification](https://azure.microsoft.com/pricing/details/sql-database/single/) qui sont généralement configurées via le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="3a41f-105">Azure SQL Database provides [a variety of pricing options](https://azure.microsoft.com/pricing/details/sql-database/single/) that are usually configured through the Azure Portal.</span></span> <span data-ttu-id="3a41f-106">Toutefois, si vous gérez le schéma à l’aide de [EF Core migrations](xref:core/managing-schemas/migrations/index) , vous pouvez spécifier les options souhaitées dans le modèle lui-même.</span><span class="sxs-lookup"><span data-stu-id="3a41f-106">However if you are managing the schema using [EF Core migrations](xref:core/managing-schemas/migrations/index) you can specify the desired options in the model itself.</span></span>

<span data-ttu-id="3a41f-107">Vous pouvez spécifier le niveau de service de la base de données (édition) à l’aide de [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier):</span><span class="sxs-lookup"><span data-stu-id="3a41f-107">You can specify the service tier of the database (EDITION) using [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier):</span></span>

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

<span data-ttu-id="3a41f-108">Vous pouvez spécifier la taille maximale de la base de données à l’aide de [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize):</span><span class="sxs-lookup"><span data-stu-id="3a41f-108">You can specify the maximum size of the database using [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize):</span></span>

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

<span data-ttu-id="3a41f-109">Vous pouvez spécifier le niveau de performance de la base de données (SERVICE_OBJECTIVE) à l’aide de [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel):</span><span class="sxs-lookup"><span data-stu-id="3a41f-109">You can specify the performance level of the database (SERVICE_OBJECTIVE) using [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel):</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

<span data-ttu-id="3a41f-110">Utilisez [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) pour configurer le pool élastique, car la valeur n’est pas un littéral de chaîne :</span><span class="sxs-lookup"><span data-stu-id="3a41f-110">Use [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) to configure the elastic pool, since the value is not a string literal:</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]

>[!TIP]
> <span data-ttu-id="3a41f-111">Vous pouvez trouver toutes les valeurs prises en charge dans la [documentation ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).</span><span class="sxs-lookup"><span data-stu-id="3a41f-111">You can find all the supported values in the [ALTER DATABASE documentation](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).</span></span>
