---
title: Regroupement DbContext
description: Regroupement DbContext dans Entity Framework Core
author: rick-anderson
ms.author: riande
ms.date: 9/19/2020
uid: core/miscellaneous/context-pooling
ms.openlocfilehash: fd5f53ff97a73895f0c4239439730dd8cb3ecc29
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2020
ms.locfileid: "91215599"
---
# <a name="dbcontext-pooling"></a>Regroupement DbContext

`AddDbContextPool` active le regroupement d' `DbContext` instances. Le regroupement de contexte peut augmenter le débit dans des scénarios à grande échelle tels que les serveurs Web en réutilisant des instances de contexte, plutôt que de créer des instances pour chaque demande.

Le modèle typique d’une application ASP.NET Core utilisant EF Core implique l’inscription d’un type personnalisé dans <xref:Microsoft.EntityFrameworkCore.DbContext> le conteneur d' [injection de dépendances](/aspnet/core/fundamentals/dependency-injection) et l’obtention d’instances de ce type par le biais de paramètres de constructeur dans les contrôleurs ou les Razor pages. À l’aide de l’injection de constructeur, une nouvelle instance de contexte est créée pour chaque requête.

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> active un pool d’instances de contexte réutilisables. Pour utiliser le regroupement de contexte, utilisez la `AddDbContextPool` méthode au lieu de lors de l' `AddDbContext` inscription du service :

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Lorsque `AddDbContextPool` est utilisé, au moment où une instance de contexte est demandée, EF vérifie d’abord si une instance est disponible dans le pool. Une fois finalisé le traitement de la demande, tout état sur l’instance est réinitialisé et l’instance elle-même est retournée au pool.

Ce concept est similaire à celui de l’utilisation du regroupement de connexions dans les fournisseurs ADO.NET et présente l’avantage d’enregistrer un certain coût d’initialisation de l’instance de contexte.

Le `poolSize` paramètre de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> définit le nombre maximal d’instances conservées par le pool. Une fois que `poolSize` est dépassé, les nouvelles instances de contexte ne sont pas mises en cache et EF revient au comportement de non-regroupement de la création d’instances à la demande.

## <a name="limitations"></a>Limites

Les applications doivent être profilées et testées pour montrer que l’initialisation du contexte est un coût significatif.

`AddDbContextPool` présente quelques limitations sur ce qui peut être fait dans la `OnConfiguring` méthode du contexte.

> [!WARNING]  
> Évitez d’utiliser le regroupement de contexte dans les applications qui maintiennent l’État. Par exemple, les champs privés dans le contexte qui ne doivent pas être partagés entre les requêtes. EF Core réinitialise uniquement l’État qu’il reconnaît avant d’ajouter une instance de contexte au pool.

Le regroupement de contexte fonctionne en réutilisant la même instance de contexte entre les requêtes. Cela signifie qu’elle est enregistrée en tant que [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) en termes de l’instance elle-même afin de pouvoir être persistante.

Le regroupement de contexte est destiné aux scénarios dans lesquels la configuration de contexte, qui comprend les services résolus, est fixée entre les demandes. Dans les cas où des services [délimités](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) sont requis ou si la configuration doit être modifiée, n’utilisez pas le regroupement. Le gain de performances par rapport au regroupement est généralement négligeable, sauf dans les scénarios hautement optimisés.
