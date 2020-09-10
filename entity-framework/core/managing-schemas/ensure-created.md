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
# <a name="create-and-drop-apis"></a>Créer et supprimer des API

Les méthodes EnsureCreated et EnsureDeleted fournissent une alternative légère aux [migrations](xref:core/managing-schemas/migrations/index) pour la gestion du schéma de base de données. Ces méthodes sont utiles dans les scénarios où les données sont temporaires et peuvent être supprimées lorsque le schéma est modifié. Par exemple pendant le prototypage, les tests ou les caches locaux.

Certains fournisseurs (surtout non relationnels) ne prennent pas en charge les migrations. Pour ces fournisseurs, EnsureCreated est souvent le moyen le plus simple d’initialiser le schéma de base de données.

> [!WARNING]
> EnsureCreated et les migrations ne fonctionnent pas correctement ensemble. Si vous utilisez des migrations, n’utilisez pas EnsureCreated pour initialiser le schéma.

La transition de EnsureCreated à des migrations n’est pas une expérience transparente. La façon la plus simple de procéder consiste à supprimer la base de données et à la recréer à l’aide des migrations. Si vous prévoyez d’utiliser des migrations à l’avenir, il est préférable de commencer par les migrations au lieu d’utiliser EnsureCreated.

## <a name="ensuredeleted"></a>EnsureDeleted

La méthode EnsureDeleted supprime la base de données, le cas échéant. Si vous ne disposez pas des autorisations appropriées, une exception est levée.

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a>EnsureCreated

EnsureCreated crée la base de données si elle n’existe pas et initialise le schéma de la base de données. Si des tables existent (y compris des tables pour une autre classe DbContext), le schéma ne sera pas initialisé.

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> Les versions asynchrones de ces méthodes sont également disponibles.

## <a name="sql-script"></a>Script SQL

Pour récupérer le SQL utilisé par EnsureCreated, vous pouvez utiliser la méthode GenerateCreateScript.

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a>Plusieurs classes DbContext

EnsureCreated fonctionne uniquement si aucune table n’est présente dans la base de données. Si nécessaire, vous pouvez écrire votre propre vérification pour voir si le schéma doit être initialisé et utiliser le service IRelationalDatabaseCreator sous-jacent pour initialiser le schéma.

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
