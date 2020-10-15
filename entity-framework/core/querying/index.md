---
title: Interrogation des données - EF Core
description: Vue d’ensemble des informations sur les requêtes dans Entity Framework Core.
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: 7cf53d59a577c5bc45f3ea4ea0cfbe257437b44e
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062561"
---
# <a name="querying-data"></a><span data-ttu-id="61415-103">Interrogation des données</span><span class="sxs-lookup"><span data-stu-id="61415-103">Querying Data</span></span>

<span data-ttu-id="61415-104">Entity Framework Core utilise LINQ (Language Integrated Query) pour interroger les données de la base de données.</span><span class="sxs-lookup"><span data-stu-id="61415-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="61415-105">LINQ vous permet d’utiliser C# (ou le langage .NET de votre choix) pour écrire des requêtes fortement typées.</span><span class="sxs-lookup"><span data-stu-id="61415-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="61415-106">Il utilise vos classes de contexte et d’entité dérivées pour référencer les objet de base de données.</span><span class="sxs-lookup"><span data-stu-id="61415-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="61415-107">EF Core passe une représentation de la requête LINQ au fournisseur de bases de données.</span><span class="sxs-lookup"><span data-stu-id="61415-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="61415-108">Les fournisseurs de bases de données la traduisent à leur tour en langage de requête spécifique aux bases de données (par exemple, SQL pour une base de données relationnelle).</span><span class="sxs-lookup"><span data-stu-id="61415-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span>

> [!TIP]
> <span data-ttu-id="61415-109">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="61415-109">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) on GitHub.</span></span>

<span data-ttu-id="61415-110">Les extraits de code montrent quelques exemples illustrant comment accomplir des tâches courantes avec Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="61415-110">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="61415-111">Chargement de toutes les données</span><span class="sxs-lookup"><span data-stu-id="61415-111">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="61415-112">Chargement d’une seule entité</span><span class="sxs-lookup"><span data-stu-id="61415-112">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="61415-113">Filtrage</span><span class="sxs-lookup"><span data-stu-id="61415-113">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="61415-114">Pour aller plus loin</span><span class="sxs-lookup"><span data-stu-id="61415-114">Further readings</span></span>

- <span data-ttu-id="61415-115">Découvrir les [expressions de requête LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="61415-115">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="61415-116">Pour plus d’informations sur le traitement des requêtes dans EF Core, consultez [Fonctionnement des requêtes](xref:core/querying/how-query-works).</span><span class="sxs-lookup"><span data-stu-id="61415-116">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
