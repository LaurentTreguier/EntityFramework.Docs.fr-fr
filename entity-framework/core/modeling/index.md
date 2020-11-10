---
title: Création et configuration d’un modèle – EF Core
description: Vue d’ensemble de la création et de la configuration d’un modèle avec Entity Framework Core.
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: b18db0059efd335abe2fc44bbc78e0106717e058
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429609"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="8d71c-103">Création et configuration d’un modèle</span><span class="sxs-lookup"><span data-stu-id="8d71c-103">Creating and configuring a model</span></span>

<span data-ttu-id="8d71c-104">Entity Framework utilise un ensemble de conventions pour créer un modèle basé sur la forme de vos classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="8d71c-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="8d71c-105">Vous pouvez spécifier une configuration supplémentaire pour compléter et/ou remplacer ce qui a été découvert par convention.</span><span class="sxs-lookup"><span data-stu-id="8d71c-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="8d71c-106">Cet article traite de la configuration qui peut être appliquée à un modèle ciblant n’importe quel magasin de données et qui peut être appliquée pendant le ciblage d’une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="8d71c-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="8d71c-107">Les fournisseurs peuvent également activer la configuration qui est spécifique à un magasin de données particulier.</span><span class="sxs-lookup"><span data-stu-id="8d71c-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="8d71c-108">Pour plus d’informations sur la configuration spécifique du fournisseur, consultez la section [Fournisseurs de base de données](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="8d71c-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]  
> <span data-ttu-id="8d71c-109">Vous pouvez consulter l' [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) de cet article sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="8d71c-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="8d71c-110">Utiliser l’API Fluent pour configurer un modèle</span><span class="sxs-lookup"><span data-stu-id="8d71c-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="8d71c-111">Vous pouvez substituer la méthode `OnModelCreating` dans le contexte dérivé et utiliser `ModelBuilder API` pour configurer votre modèle.</span><span class="sxs-lookup"><span data-stu-id="8d71c-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="8d71c-112">Il s’agit de la méthode de configuration la plus puissante, qui permet de spécifier une configuration sans modifier les classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="8d71c-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="8d71c-113">Dotée du niveau de priorité le plus élevé, la configuration de l’API Fluent remplace les conventions et les annotations de données.</span><span class="sxs-lookup"><span data-stu-id="8d71c-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a><span data-ttu-id="8d71c-114">Configuration du regroupement</span><span class="sxs-lookup"><span data-stu-id="8d71c-114">Grouping configuration</span></span>

<span data-ttu-id="8d71c-115">Pour réduire la taille de la <xref:System.Data.Entity.DbContext.OnModelCreating%2A> méthode, toute la configuration d’un type d’entité peut être extraite dans une classe distincte qui implémente <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> .</span><span class="sxs-lookup"><span data-stu-id="8d71c-115">To reduce the size of the <xref:System.Data.Entity.DbContext.OnModelCreating%2A> method all configuration for an entity type can be extracted to a separate class implementing <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=IEntityTypeConfiguration)]

<span data-ttu-id="8d71c-116">Appelez ensuite simplement la `Configure` méthode à partir de `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="8d71c-116">Then just invoke the `Configure` method from `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyIEntityTypeConfiguration)]

<span data-ttu-id="8d71c-117">Il est possible d’appliquer l’ensemble de la configuration spécifiée dans les types qui implémentent `IEntityTypeConfiguration` dans un assembly donné.</span><span class="sxs-lookup"><span data-stu-id="8d71c-117">It is possible to apply all configuration specified in types implementing `IEntityTypeConfiguration` in a given assembly.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyConfigurationsFromAssembly)]

> [!NOTE]
> <span data-ttu-id="8d71c-118">L’ordre dans lequel les configurations seront appliquées n’est pas défini. par conséquent, cette méthode ne doit être utilisée que lorsque l’ordre n’a pas d’importance.</span><span class="sxs-lookup"><span data-stu-id="8d71c-118">The order in which the configurations will be applied is undefined, therefore this method should only be used when the order doesn't matter.</span></span>

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="8d71c-119">Utiliser des annotations de données pour configurer un modèle</span><span class="sxs-lookup"><span data-stu-id="8d71c-119">Use data annotations to configure a model</span></span>

<span data-ttu-id="8d71c-120">Vous pouvez également appliquer des attributs (également appelés annotations de données) à vos classes et propriétés.</span><span class="sxs-lookup"><span data-stu-id="8d71c-120">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="8d71c-121">Les annotations de données remplacent les conventions, mais elles sont remplacées par la configuration de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="8d71c-121">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
