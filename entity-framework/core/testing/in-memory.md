---
title: Test avec la base de données In-Memory EF-EF Core
description: Utilisation de la base de données en mémoire EF pour tester une application Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: db91570dc9d5a4b95d513df509867e9bca406356
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431509"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="5ee83-103">Test avec la base de données In-Memory EF</span><span class="sxs-lookup"><span data-stu-id="5ee83-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="5ee83-104">La base de données en mémoire EF se comporte souvent différemment des bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="5ee83-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="5ee83-105">Utilisez uniquement la base de données en mémoire EF après avoir pleinement compris les problèmes et les compromis impliqués, comme indiqué dans [test de code qui utilise EF Core](xref:core/testing/index).</span><span class="sxs-lookup"><span data-stu-id="5ee83-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/testing/index).</span></span>  

> [!TIP]
> <span data-ttu-id="5ee83-106">SQLite est un fournisseur relationnel et peut également utiliser des bases de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="5ee83-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="5ee83-107">Envisagez de l’utiliser pour le test afin de mieux correspondre aux comportements de base de données relationnelle courants.</span><span class="sxs-lookup"><span data-stu-id="5ee83-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="5ee83-108">Ce sujet est abordé dans [utilisation de SQLite pour tester une application EF Core](xref:core/testing/sqlite).</span><span class="sxs-lookup"><span data-stu-id="5ee83-108">This is covered in [Using SQLite to test an EF Core application](xref:core/testing/sqlite).</span></span>

<span data-ttu-id="5ee83-109">Les informations de cette page se trouvent désormais dans d’autres emplacements :</span><span class="sxs-lookup"><span data-stu-id="5ee83-109">The information on this page now lives in other locations:</span></span>

* <span data-ttu-id="5ee83-110">Consultez [test du code qui utilise EF Core](xref:core/testing/index) pour obtenir des informations générales sur les tests avec la base de données EF en mémoire.</span><span class="sxs-lookup"><span data-stu-id="5ee83-110">See [Testing code that uses EF Core](xref:core/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="5ee83-111">Consultez l' [exemple illustrant comment tester des applications qui utilisent EF Core](xref:core/testing/testing-sample) pour obtenir un exemple utilisant la base de données EF en mémoire.</span><span class="sxs-lookup"><span data-stu-id="5ee83-111">See [Sample showing how to test applications that use EF Core](xref:core/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="5ee83-112">Pour obtenir des informations générales sur la base de données EF en mémoire, consultez [le fournisseur de base de données en mémoire EF](xref:core/providers/in-memory/index) .</span><span class="sxs-lookup"><span data-stu-id="5ee83-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>