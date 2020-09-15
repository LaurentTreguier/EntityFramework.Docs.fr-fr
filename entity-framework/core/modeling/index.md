---
title: Création et configuration d’un modèle – EF Core
description: Vue d’ensemble de la création et de la configuration d’un modèle avec Entity Framework Core.
author: rowanmiller
ms.date: 11/05/2019
uid: core/modeling/index
ms.openlocfilehash: 1380ed0c1af0fdd3ae24de311bea455cb57a7dcd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071418"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="dc10d-103">Création et configuration d’un modèle</span><span class="sxs-lookup"><span data-stu-id="dc10d-103">Creating and configuring a model</span></span>

<span data-ttu-id="dc10d-104">Entity Framework utilise un ensemble de conventions pour créer un modèle basé sur la forme de vos classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="dc10d-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="dc10d-105">Vous pouvez spécifier une configuration supplémentaire pour compléter et/ou remplacer ce qui a été découvert par convention.</span><span class="sxs-lookup"><span data-stu-id="dc10d-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="dc10d-106">Cet article traite de la configuration qui peut être appliquée à un modèle ciblant n’importe quel magasin de données et qui peut être appliquée pendant le ciblage d’une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="dc10d-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="dc10d-107">Les fournisseurs peuvent également activer la configuration qui est spécifique à un magasin de données particulier.</span><span class="sxs-lookup"><span data-stu-id="dc10d-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="dc10d-108">Pour plus d’informations sur la configuration spécifique du fournisseur, consultez la section  [Fournisseurs de base de données](xref:core/providers/index) .</span><span class="sxs-lookup"><span data-stu-id="dc10d-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]  
> <span data-ttu-id="dc10d-109">Vous pouvez voir l’ [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples)  de cet article sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="dc10d-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="dc10d-110">Utiliser l’API Fluent pour configurer un modèle</span><span class="sxs-lookup"><span data-stu-id="dc10d-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="dc10d-111">Vous pouvez substituer la méthode  `OnModelCreating`  dans votre contexte dérivé et utiliser  `ModelBuilder API` pour configurer votre modèle.</span><span class="sxs-lookup"><span data-stu-id="dc10d-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="dc10d-112">Il s’agit de la méthode de configuration la plus puissante, qui permet de spécifier une configuration sans modifier les classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="dc10d-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="dc10d-113">Dotée du niveau de priorité le plus élevé, la configuration de l’API Fluent remplace les conventions et les annotations de données.</span><span class="sxs-lookup"><span data-stu-id="dc10d-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="dc10d-114">Utiliser des annotations de données pour configurer un modèle</span><span class="sxs-lookup"><span data-stu-id="dc10d-114">Use data annotations to configure a model</span></span>

<span data-ttu-id="dc10d-115">Vous pouvez également appliquer des attributs (également appelés annotations de données) à vos classes et propriétés.</span><span class="sxs-lookup"><span data-stu-id="dc10d-115">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="dc10d-116">Les annotations de données remplacent les conventions, mais elles sont remplacées par la configuration de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="dc10d-116">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
