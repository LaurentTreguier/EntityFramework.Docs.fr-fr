---
title: Création de DbContext au moment du design-EF Core
description: Stratégies de création d’un DbContext au moment du design avec Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dbcontext-creation
ms.openlocfilehash: 144ed26dcf605dc29d53519ad2ea9cea58fb4e44
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431587"
---
# <a name="design-time-dbcontext-creation"></a>Création de DbContext au moment de la conception

Certaines des commandes des outils de EF Core (par exemple, les commandes de [migration][1] ) requièrent la création d’une instance dérivée `DbContext` au moment de la conception afin de collecter des détails sur les types d’entité de l’application et la façon dont ils sont mappés à un schéma de base de données. Dans la plupart des cas, il est souhaitable que le `DbContext` créé soit configuré de manière similaire à la façon dont il est [configuré au moment][2]de l’exécution.

Les outils essaient de créer l’un des moyens `DbContext` suivants :

## <a name="from-application-services"></a>À partir des services d’application

Si votre projet de démarrage utilise l' [hôte Web ASP.net Core][3] ou l' [hôte générique .net Core][4], les outils essaient d’obtenir l’objet DbContext à partir du fournisseur de services de l’application.

Les outils essaient d’abord d’obtenir le fournisseur de services en `Program.CreateHostBuilder()` appelant, en appelant `Build()` , puis en accédant à la `Services` propriété.

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs#ApplicationService)]

> [!NOTE]
> Lorsque vous créez une application de ASP.NET Core, ce hook est inclus par défaut.

`DbContext`Lui-même et toutes les dépendances de son constructeur doivent être inscrits en tant que services dans le fournisseur de services de l’application. Pour ce faire, il est facile d’avoir [un constructeur sur `DbContext` qui accepte une instance de `DbContextOptions<TContext>` comme argument][5] et utilise la [ `AddDbContext<TContext>` méthode][6].

## <a name="using-a-constructor-with-no-parameters"></a>Utilisation d’un constructeur sans paramètres

Si la DbContext ne peut pas être obtenue à partir du fournisseur de services d’application, les outils recherchent le `DbContext` type dérivé dans le projet. Ensuite, ils essaient de créer une instance à l’aide d’un constructeur sans paramètres. Il peut s’agir du constructeur par défaut si le `DbContext` est configuré à l’aide de la [`OnConfiguring`][7] méthode.

## <a name="from-a-design-time-factory"></a>À partir d’une fabrique au moment de la conception

Vous pouvez également indiquer aux outils comment créer votre DbContext en implémentant l' `IDesignTimeDbContextFactory<TContext>` interface : si une classe qui implémente cette interface est trouvée dans le même projet que le dérivé `DbContext` ou dans le projet de démarrage de l’application, les outils ignorent les autres méthodes de création de DbContext et utilisent la fabrique au moment de la conception.

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs#BloggingContextFactory)]

> [!NOTE]
> Avant EFCore 5,0, le `args` paramètre était inutilisé (consultez [ce problème][8]).
> Ce problème est résolu dans EFCore 5,0 et tous les arguments supplémentaires au moment du design sont passés à l’application par le biais de ce paramètre.

Une fabrique au moment du design peut être particulièrement utile si vous avez besoin de configurer DbContext différemment pour le moment de la conception plutôt qu’au moment de l’exécution, si le `DbContext` constructeur prend des paramètres supplémentaires qui ne sont pas inscrits dans di, si vous n’utilisez pas de di du tout ou si, pour une raison quelconque, vous préférez ne pas avoir de `CreateHostBuilder` méthode dans la classe de votre application ASP.net Core `Main` .

## <a name="args"></a>Args

IDesignTimeDbContextFactory. CreateDbContext et Program. CreateHostBuilder acceptent les arguments de ligne de commande.

À partir de EF Core 5,0, vous pouvez spécifier ces arguments à partir des outils :

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update -- --environment Production
```

Le `--` jeton indique `dotnet ef` à traiter tout ce qui suit comme argument et ne tente pas de les analyser en tant qu’options. Les arguments supplémentaires non utilisés par `dotnet ef` sont transmis à l’application.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database -Args '--environment Production'
```

***

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/dbcontext-configuration/index
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/dbcontext-configuration/index#constructor-argument
  [6]: xref:core/dbcontext-configuration/index#using-dbcontext-with-dependency-injection
  [7]: xref:core/dbcontext-configuration/index#onconfiguring
  [8]: https://github.com/dotnet/efcore/issues/8332
