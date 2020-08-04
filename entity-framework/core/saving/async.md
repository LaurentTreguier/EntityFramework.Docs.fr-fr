---
title: 'Enregistrement asynchrone : EF Core'
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
uid: core/saving/async
ms.openlocfilehash: aa1fbfdc44895eace04354063435f98370aed2c4
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526912"
---
# <a name="asynchronous-saving"></a><span data-ttu-id="4d5e7-102">Enregistrement asynchrone</span><span class="sxs-lookup"><span data-stu-id="4d5e7-102">Asynchronous Saving</span></span>

<span data-ttu-id="4d5e7-103">L’enregistrement asynchrone évite de bloquer un thread lorsque les modifications sont écrites dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="4d5e7-103">Asynchronous saving avoids blocking a thread while the changes are written to the database.</span></span> <span data-ttu-id="4d5e7-104">Cela peut être utile pour éviter le gel de l’interface utilisateur d’une application client lourde.</span><span class="sxs-lookup"><span data-stu-id="4d5e7-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="4d5e7-105">Les opérations asynchrones peuvent également augmenter le débit dans une application web, où le thread peut être libéré pour d’autres demandes de service lors de la fin de l’opération de base de données.</span><span class="sxs-lookup"><span data-stu-id="4d5e7-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="4d5e7-106">Pour plus d’informations, consultez [programmation asynchrone en C#](/dotnet/csharp/async).</span><span class="sxs-lookup"><span data-stu-id="4d5e7-106">For more information, see [Asynchronous Programming in C#](/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="4d5e7-107">EF Core ne prend pas en charge les opérations parallèles multiples en cours d’exécution sur la même instance de contexte.</span><span class="sxs-lookup"><span data-stu-id="4d5e7-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="4d5e7-108">Vous devez toujours attendre qu’opération se termine avant de commencer l’opération suivante.</span><span class="sxs-lookup"><span data-stu-id="4d5e7-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="4d5e7-109">Cela est généralement effectué à l’aide du mot-clé `await` sur chaque opération asynchrone.</span><span class="sxs-lookup"><span data-stu-id="4d5e7-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="4d5e7-110">Entity Framework Core fournit `DbContext.SaveChangesAsync()` comme alternative asynchrone à `DbContext.SaveChanges()`.</span><span class="sxs-lookup"><span data-stu-id="4d5e7-110">Entity Framework Core provides `DbContext.SaveChangesAsync()` as an asynchronous alternative to `DbContext.SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
