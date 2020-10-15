---
title: Vue d’ensemble de la journalisation et de l’interception-EF Core
description: Vue d’ensemble de la journalisation, des événements, des intercepteurs et des diagnostics pour EF Core
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066616"
---
# <a name="overview-of-logging-and-interception"></a>Vue d’ensemble de la journalisation et de l’interception

Entity Framework Core (EF Core) contient plusieurs mécanismes permettant de générer des journaux, de répondre aux événements et d’obtenir des Diagnostics. Chacun d’eux étant adapté à différentes situations, il est important de sélectionner le mécanisme le mieux adapté à la tâche, même lorsque plusieurs mécanismes peuvent fonctionner. Par exemple, un intercepteur de base de données peut être utilisé pour journaliser SQL, mais il est mieux géré par l’un des mécanismes spécifiques à la journalisation. Cette page présente une vue d’ensemble de chacun de ces mécanismes et décrit les cas d’utilisation de chacun d’entre eux.

## <a name="simple-logging"></a>Journalisation simple

> [!NOTE]
> Cette fonctionnalité a été ajoutée dans EF Core 5,0.

EF Core les journaux sont accessibles à partir de n’importe quel type d’application via l’utilisation de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> lors [de la configuration d’une instance DbContext](xref:core/miscellaneous/configuring-dbcontext). Cette configuration s’effectue généralement dans une substitution de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Par exemple :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Ce concept est semblable à <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> dans EF6.

Pour plus d’informations, consultez [journalisation simple](xref:core/miscellaneous/events/simple-logging) .
