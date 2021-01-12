---
title: 'Enregistrement de base : EF Core'
description: Informations de base sur l’ajout, la mise à jour et la suppression de données avec Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/basic
ms.openlocfilehash: 8c98f95dee85641f11af716290333b47f340bff8
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129107"
---
# <a name="basic-save"></a><span data-ttu-id="b5a2a-103">Enregistrement de base</span><span class="sxs-lookup"><span data-stu-id="b5a2a-103">Basic Save</span></span>

<span data-ttu-id="b5a2a-104">Découvrez comment ajouter, modifier et supprimer des données à l’aide de vos classes de contexte et d’entité.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-104">Learn how to add, modify, and remove data using your context and entity classes.</span></span>

> [!TIP]
> <span data-ttu-id="b5a2a-105">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-105">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) on GitHub.</span></span>

## <a name="adding-data"></a><span data-ttu-id="b5a2a-106">Ajout de données</span><span class="sxs-lookup"><span data-stu-id="b5a2a-106">Adding Data</span></span>

<span data-ttu-id="b5a2a-107">Utilisez la méthode *DbSet.Add* pour ajouter de nouvelles instances de vos classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-107">Use the *DbSet.Add* method to add new instances of your entity classes.</span></span> <span data-ttu-id="b5a2a-108">Les données seront insérées dans la base de données lorsque vous appelez *SaveChanges*.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-108">The data will be inserted in the database when you call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]
> <span data-ttu-id="b5a2a-109">Les méthodes Add, Attach et Update fonctionnent toutes sur le graphique complet des entités passées, comme décrit dans la section [Données associées](xref:core/saving/related-data).</span><span class="sxs-lookup"><span data-stu-id="b5a2a-109">The Add, Attach, and Update methods all work on the full graph of entities passed to them, as described in the [Related Data](xref:core/saving/related-data) section.</span></span> <span data-ttu-id="b5a2a-110">Vous pouvez aussi utiliser la propriété EntityEntry.State pour définir l’état d’une seule entité.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-110">Alternately, the EntityEntry.State property can be used to set the state of just a single entity.</span></span> <span data-ttu-id="b5a2a-111">Par exemple : `context.Entry(blog).State = EntityState.Modified`.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-111">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="updating-data"></a><span data-ttu-id="b5a2a-112">Mise à jour des données</span><span class="sxs-lookup"><span data-stu-id="b5a2a-112">Updating Data</span></span>

<span data-ttu-id="b5a2a-113">EF détecte automatiquement les modifications apportées à une entité existante qui est suivie par le contexte.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-113">EF will automatically detect changes made to an existing entity that is tracked by the context.</span></span> <span data-ttu-id="b5a2a-114">Cela inclut les entités que vous chargez/demandez à partir de la base de données et des entités qui ont été précédemment ajoutées et enregistrées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-114">This includes entities that you load/query from the database, and entities that were previously added and saved to the database.</span></span>

<span data-ttu-id="b5a2a-115">Modifiez simplement les valeurs affectées aux propriétés, puis appelez *SaveChanges*.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-115">Simply modify the values assigned to properties and then call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a><span data-ttu-id="b5a2a-116">Suppression de données</span><span class="sxs-lookup"><span data-stu-id="b5a2a-116">Deleting Data</span></span>

<span data-ttu-id="b5a2a-117">Utilisez la méthode *DbSet.Remove* pour supprimer les instances de vos classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-117">Use the *DbSet.Remove* method to delete instances of your entity classes.</span></span>

<span data-ttu-id="b5a2a-118">Si l’entité existe déjà dans la base de données, elle sera supprimée pendant *SaveChanges*.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-118">If the entity already exists in the database, it will be deleted during *SaveChanges*.</span></span> <span data-ttu-id="b5a2a-119">Si l’entité n’a pas encore été enregistrée dans la base de données (autrement dit, si elle est suivie comme ajoutée), elle sera supprimée du contexte et ne pourra plus être insérée lorsque *SaveChanges* est appelé.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-119">If the entity has not yet been saved to the database (that is, it is tracked as added) then it will be removed from the context and will no longer be inserted when *SaveChanges* is called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a><span data-ttu-id="b5a2a-120">Plusieurs opérations dans un seul SaveChanges</span><span class="sxs-lookup"><span data-stu-id="b5a2a-120">Multiple Operations in a single SaveChanges</span></span>

<span data-ttu-id="b5a2a-121">Vous pouvez combiner plusieurs opérations d’ajout/mise à jour/suppression en un seul appel à *SaveChanges*.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-121">You can combine multiple Add/Update/Remove operations into a single call to *SaveChanges*.</span></span>

> [!NOTE]
> <span data-ttu-id="b5a2a-122">Pour la plupart des fournisseurs de base de données, *SaveChanges* est transactionnel.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-122">For most database providers, *SaveChanges* is transactional.</span></span> <span data-ttu-id="b5a2a-123">Cela signifie que toutes les opérations réussissent ou échouent complètement et que les opérations ne sont jamais partiellement appliquées.</span><span class="sxs-lookup"><span data-stu-id="b5a2a-123">This means  all the operations will either succeed or fail and the operations will never be left partially applied.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
