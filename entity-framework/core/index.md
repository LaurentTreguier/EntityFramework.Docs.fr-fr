---
title: Vue d’ensemble d’Entity Framework Core - EF Core
description: Vue d’ensemble générale d’Entity Framework Core.
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 4816f2d57590ddd2ad4807daa2cc1c5000cd5713
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619492"
---
# <a name="entity-framework-core"></a><span data-ttu-id="6a56b-103">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6a56b-103">Entity Framework Core</span></span>

<span data-ttu-id="6a56b-104">Entity Framework (EF) Core est une version légère, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) et multiplateforme de la très connue technologie d’accès aux données Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="6a56b-104">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="6a56b-105">EF Core peut servir de mappeur relationnel/objet (O/RM), permettant aux développeurs .NET de travailler avec une base de données à l’aide d’objets .NET, et éliminant la nécessité de la plupart du code d’accès aux données qu’ils doivent généralement écrire.</span><span class="sxs-lookup"><span data-stu-id="6a56b-105">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="6a56b-106">EF Core prend en charge de nombreux moteurs de base de données ; consultez [Fournisseurs de base de données](xref:core/providers/index) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="6a56b-106">EF Core supports many database engines, see [Database Providers](xref:core/providers/index) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="6a56b-107">Modèle</span><span class="sxs-lookup"><span data-stu-id="6a56b-107">The Model</span></span>

<span data-ttu-id="6a56b-108">Avec EF Core, l’accès aux données est effectué à l’aide d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="6a56b-108">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="6a56b-109">Un modèle est composé de classes d’entités et d’un objet de contexte représentant une session avec la base de données, ce qui permet d’interroger et d’enregistrer des données.</span><span class="sxs-lookup"><span data-stu-id="6a56b-109">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="6a56b-110">Pour en savoir plus, consultez [Création d’un modèle](xref:core/modeling/index).</span><span class="sxs-lookup"><span data-stu-id="6a56b-110">See [Creating a Model](xref:core/modeling/index) to learn more.</span></span>

<span data-ttu-id="6a56b-111">Vous pouvez générer un modèle à partir d’une base de données existante, coder manuellement un modèle en fonction de votre base de données ou utiliser [EF Migrations](xref:core/managing-schemas/migrations/index) pour créer une base de données à partir de votre modèle et la faire évoluer au même rythme que celui-ci.</span><span class="sxs-lookup"><span data-stu-id="6a56b-111">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](xref:core/managing-schemas/migrations/index) to create a database from your model, and then evolve it as your model changes over time.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="6a56b-112">Interrogation</span><span class="sxs-lookup"><span data-stu-id="6a56b-112">Querying</span></span>

<span data-ttu-id="6a56b-113">Les instances de vos classes d’entité sont récupérées de la base de données à l’aide de LINQ (Language Integrated Query).</span><span class="sxs-lookup"><span data-stu-id="6a56b-113">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="6a56b-114">Pour en savoir plus, consultez [Interrogation des données](xref:core/querying/index).</span><span class="sxs-lookup"><span data-stu-id="6a56b-114">See [Querying Data](xref:core/querying/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="6a56b-115">Enregistrement de données</span><span class="sxs-lookup"><span data-stu-id="6a56b-115">Saving Data</span></span>

<span data-ttu-id="6a56b-116">Les données sont créées, supprimées et modifiées dans la base de données à l’aide d’instances de vos classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="6a56b-116">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="6a56b-117">Pour en savoir plus, consultez [Enregistrement de données](xref:core/saving/index).</span><span class="sxs-lookup"><span data-stu-id="6a56b-117">See [Saving Data](xref:core/saving/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a><span data-ttu-id="6a56b-118">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="6a56b-118">Next steps</span></span>

<span data-ttu-id="6a56b-119">Pour des tutoriels d’introduction, consultez [Bien démarrer avec Entity Framework Core](xref:core/get-started/index).</span><span class="sxs-lookup"><span data-stu-id="6a56b-119">For introductory tutorials, see [Getting Started with Entity Framework Core](xref:core/get-started/index).</span></span>
