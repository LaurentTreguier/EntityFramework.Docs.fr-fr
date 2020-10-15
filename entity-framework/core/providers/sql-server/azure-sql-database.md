---
title: Fournisseur de base de données Microsoft SQL Server-options de Azure SQL Database-EF Core
description: Comment spécifier les niveaux de service et de performances pour Azure SQL Database avec le fournisseur de base de données SQL Server Entity Framework Core
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: 9b2cc5e0790beb41295dcea6ae30c4ad15b537fa
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065366"
---
# <a name="specifying-azure-sql-database-options"></a>Spécification des options Azure SQL Database

>[!NOTE]
> Cette API est nouvelle dans EF Core 3,1.

Azure SQL Database propose [diverses options de tarification](https://azure.microsoft.com/pricing/details/sql-database/single/) qui sont généralement configurées via le portail Azure. Toutefois, si vous gérez le schéma à l’aide de [EF Core migrations](xref:core/managing-schemas/migrations/index) , vous pouvez spécifier les options souhaitées dans le modèle lui-même.

Vous pouvez spécifier le niveau de service de la base de données (édition) à l’aide de [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier):

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

Vous pouvez spécifier la taille maximale de la base de données à l’aide de [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize):

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

Vous pouvez spécifier le niveau de performance de la base de données (SERVICE_OBJECTIVE) à l’aide de [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel):

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

Utilisez [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) pour configurer le pool élastique, car la valeur n’est pas un littéral de chaîne :

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]

>[!TIP]
> Vous pouvez trouver toutes les valeurs prises en charge dans la [documentation ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).
