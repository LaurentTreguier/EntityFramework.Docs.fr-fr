---
title: Interrogation des données - EF Core
description: Vue d’ensemble des informations sur les requêtes dans Entity Framework Core.
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: 733b44e2f571bd32689b9d8e5d7507bd90e7848d
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023729"
---
# <a name="querying-data"></a><span data-ttu-id="688bb-103">Interrogation des données</span><span class="sxs-lookup"><span data-stu-id="688bb-103">Querying Data</span></span>

<span data-ttu-id="688bb-104">Entity Framework Core utilise la requête Language-Integrated (LINQ) pour interroger les données de la base de données.</span><span class="sxs-lookup"><span data-stu-id="688bb-104">Entity Framework Core uses Language-Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="688bb-105">LINQ vous permet d’utiliser C# (ou le langage .NET de votre choix) pour écrire des requêtes fortement typées.</span><span class="sxs-lookup"><span data-stu-id="688bb-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="688bb-106">Il utilise vos classes de contexte et d’entité dérivées pour référencer les objet de base de données.</span><span class="sxs-lookup"><span data-stu-id="688bb-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="688bb-107">EF Core passe une représentation de la requête LINQ au fournisseur de bases de données.</span><span class="sxs-lookup"><span data-stu-id="688bb-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="688bb-108">Les fournisseurs de bases de données la traduisent à leur tour en langage de requête spécifique aux bases de données (par exemple, SQL pour une base de données relationnelle).</span><span class="sxs-lookup"><span data-stu-id="688bb-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span> <span data-ttu-id="688bb-109">Les requêtes sont toujours exécutées sur la base de données même si les entités retournées dans le résultat existent déjà dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="688bb-109">Queries are always executed against the database even if the entities returned in the result already exist in the context.</span></span>

> [!TIP]
> <span data-ttu-id="688bb-110">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/Overview) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="688bb-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/Overview) on GitHub.</span></span>

<span data-ttu-id="688bb-111">Les extraits de code montrent quelques exemples illustrant comment accomplir des tâches courantes avec Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="688bb-111">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="688bb-112">Chargement de toutes les données</span><span class="sxs-lookup"><span data-stu-id="688bb-112">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="688bb-113">Chargement d’une seule entité</span><span class="sxs-lookup"><span data-stu-id="688bb-113">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="688bb-114">Filtrage</span><span class="sxs-lookup"><span data-stu-id="688bb-114">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="688bb-115">Pour aller plus loin</span><span class="sxs-lookup"><span data-stu-id="688bb-115">Further readings</span></span>

- <span data-ttu-id="688bb-116">Découvrir les [expressions de requête LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="688bb-116">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="688bb-117">Pour plus d’informations sur le traitement des requêtes dans EF Core, consultez [Fonctionnement des requêtes](xref:core/querying/how-query-works).</span><span class="sxs-lookup"><span data-stu-id="688bb-117">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
