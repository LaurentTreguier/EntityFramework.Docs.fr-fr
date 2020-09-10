---
title: Création de DbContext au moment du design-EF Core
description: Stratégies de création d’un DbContext au moment du design avec Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: ef2eba93827e04a9731ba960c40e9a50168ca8ff
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619447"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="53d4a-103">Création de DbContext au moment de la conception</span><span class="sxs-lookup"><span data-stu-id="53d4a-103">Design-time DbContext Creation</span></span>

<span data-ttu-id="53d4a-104">Certaines des commandes des outils de EF Core (par exemple, les commandes de [migration][1] ) requièrent la création d’une instance dérivée `DbContext` au moment de la conception afin de collecter des détails sur les types d’entité de l’application et la façon dont ils sont mappés à un schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="53d4a-104">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="53d4a-105">Dans la plupart des cas, il est souhaitable que le `DbContext` créé soit configuré de manière similaire à la façon dont il est [configuré au moment][2]de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="53d4a-105">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="53d4a-106">Les outils essaient de créer l’un des moyens `DbContext` suivants :</span><span class="sxs-lookup"><span data-stu-id="53d4a-106">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="53d4a-107">À partir des services d’application</span><span class="sxs-lookup"><span data-stu-id="53d4a-107">From application services</span></span>

<span data-ttu-id="53d4a-108">Si votre projet de démarrage utilise l' [hôte Web ASP.net Core][3] ou l' [hôte générique .net Core][4], les outils essaient d’obtenir l’objet DbContext à partir du fournisseur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="53d4a-108">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="53d4a-109">Les outils essaient d’abord d’obtenir le fournisseur de services en `Program.CreateHostBuilder()` appelant, en appelant `Build()` , puis en accédant à la `Services` propriété.</span><span class="sxs-lookup"><span data-stu-id="53d4a-109">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> <span data-ttu-id="53d4a-110">Lorsque vous créez une application de ASP.NET Core, ce hook est inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="53d4a-110">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="53d4a-111">`DbContext`Lui-même et toutes les dépendances de son constructeur doivent être inscrits en tant que services dans le fournisseur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="53d4a-111">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="53d4a-112">Pour ce faire, il est facile d’avoir [un constructeur sur `DbContext` qui accepte une instance de `DbContextOptions<TContext>` comme argument][5] et utilise la [ `AddDbContext<TContext>` méthode][6].</span><span class="sxs-lookup"><span data-stu-id="53d4a-112">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="53d4a-113">Utilisation d’un constructeur sans paramètres</span><span class="sxs-lookup"><span data-stu-id="53d4a-113">Using a constructor with no parameters</span></span>

<span data-ttu-id="53d4a-114">Si la DbContext ne peut pas être obtenue à partir du fournisseur de services d’application, les outils recherchent le `DbContext` type dérivé dans le projet.</span><span class="sxs-lookup"><span data-stu-id="53d4a-114">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="53d4a-115">Ensuite, ils essaient de créer une instance à l’aide d’un constructeur sans paramètres.</span><span class="sxs-lookup"><span data-stu-id="53d4a-115">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="53d4a-116">Il peut s’agir du constructeur par défaut si le `DbContext` est configuré à l’aide de la [`OnConfiguring`][7] méthode.</span><span class="sxs-lookup"><span data-stu-id="53d4a-116">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="53d4a-117">À partir d’une fabrique au moment de la conception</span><span class="sxs-lookup"><span data-stu-id="53d4a-117">From a design-time factory</span></span>

<span data-ttu-id="53d4a-118">Vous pouvez également indiquer aux outils comment créer votre DbContext en implémentant l' `IDesignTimeDbContextFactory<TContext>` interface : si une classe qui implémente cette interface est trouvée dans le même projet que le dérivé `DbContext` ou dans le projet de démarrage de l’application, les outils ignorent les autres méthodes de création de DbContext et utilisent la fabrique au moment de la conception.</span><span class="sxs-lookup"><span data-stu-id="53d4a-118">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> <span data-ttu-id="53d4a-119">Avant EFCore 5,0, le `args` paramètre était inutilisé (consultez [ce problème][8]).</span><span class="sxs-lookup"><span data-stu-id="53d4a-119">Prior to EFCore 5.0 the `args` parameter was unused (see [this issue][8]).</span></span>
> <span data-ttu-id="53d4a-120">Ce problème est résolu dans EFCore 5,0 et tous les arguments supplémentaires au moment du design sont passés à l’application par le biais de ce paramètre.</span><span class="sxs-lookup"><span data-stu-id="53d4a-120">This is fixed in EFCore 5.0 and any additional design-time arguments are passed into the application through that parameter.</span></span>

<span data-ttu-id="53d4a-121">Une fabrique au moment du design peut être particulièrement utile si vous avez besoin de configurer DbContext différemment pour le moment de la conception plutôt qu’au moment de l’exécution, si le `DbContext` constructeur prend des paramètres supplémentaires qui ne sont pas inscrits dans di, si vous n’utilisez pas de di du tout ou si, pour une raison quelconque, vous préférez ne pas avoir de `BuildWebHost` méthode dans la classe de votre application ASP.net Core `Main` .</span><span class="sxs-lookup"><span data-stu-id="53d4a-121">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
