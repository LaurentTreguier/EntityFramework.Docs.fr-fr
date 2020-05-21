---
title: Création de DbContext au moment du design-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 0b0271dcabea63a2529c091cc14cb9059d56ac8d
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672961"
---
# <a name="design-time-dbcontext-creation"></a>Création de DbContext au moment de la conception

Certaines des commandes des outils de EF Core (par exemple, les commandes de [migration][1] ) requièrent la création d’une instance dérivée `DbContext` au moment de la conception afin de collecter des détails sur les types d’entité de l’application et la façon dont ils sont mappés à un schéma de base de données. Dans la plupart des cas, il est souhaitable que le `DbContext` créé soit configuré de manière similaire à la façon dont il est [configuré au moment][2]de l’exécution.

Les outils essaient de créer l’un des moyens `DbContext` suivants :

## <a name="from-application-services"></a>À partir des services d’application

Si votre projet de démarrage utilise l' [hôte Web ASP.net Core][3] ou l' [hôte générique .net Core][4], les outils essaient d’obtenir l’objet DbContext à partir du fournisseur de services de l’application.

Les outils essaient d’abord d’obtenir le fournisseur de services en `Program.CreateHostBuilder()` appelant, en appelant `Build()` , puis en accédant à la `Services` propriété.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> Lorsque vous créez une application de ASP.NET Core, ce hook est inclus par défaut.

`DbContext`Lui-même et toutes les dépendances de son constructeur doivent être inscrits en tant que services dans le fournisseur de services de l’application. Pour ce faire, il est facile d’avoir [un constructeur sur `DbContext` qui accepte une instance de `DbContextOptions<TContext>` comme argument][5] et utilise la [ `AddDbContext<TContext>` méthode][6].

## <a name="using-a-constructor-with-no-parameters"></a>Utilisation d’un constructeur sans paramètres

Si la DbContext ne peut pas être obtenue à partir du fournisseur de services d’application, les outils recherchent le `DbContext` type dérivé dans le projet. Ensuite, ils essaient de créer une instance à l’aide d’un constructeur sans paramètres. Il peut s’agir du constructeur par défaut si le `DbContext` est configuré à l’aide de la [`OnConfiguring`][7] méthode.

## <a name="from-a-design-time-factory"></a>À partir d’une fabrique au moment de la conception

Vous pouvez également indiquer aux outils comment créer votre DbContext en implémentant l' `IDesignTimeDbContextFactory<TContext>` interface : si une classe qui implémente cette interface est trouvée dans le même projet que le dérivé `DbContext` ou dans le projet de démarrage de l’application, les outils ignorent les autres méthodes de création de DbContext et utilisent la fabrique au moment de la conception.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> Avant EFCore 5,0, le `args` paramètre était inutilisé (consultez [ce problème][8]).
> Ce problème est résolu dans EFCore 5,0 et tous les arguments supplémentaires au moment du design sont passés à l’application par le biais de ce paramètre.

Une fabrique au moment du design peut être particulièrement utile si vous avez besoin de configurer DbContext différemment pour le moment de la conception plutôt qu’au moment de l’exécution, si le `DbContext` constructeur prend des paramètres supplémentaires qui ne sont pas inscrits dans di, si vous n’utilisez pas de di du tout ou si, pour une raison quelconque, vous préférez ne pas avoir de `BuildWebHost` méthode dans la classe de votre application ASP.net Core `Main` .

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
