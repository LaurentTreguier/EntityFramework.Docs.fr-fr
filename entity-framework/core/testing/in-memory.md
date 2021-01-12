---
title: Test avec la base de données In-Memory EF-EF Core
description: Utilisation de la base de données en mémoire EF pour tester une application Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: 78dcac3d0fd69110986c99a097a864104caa1951
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128808"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="ecfe6-103">Test avec la base de données In-Memory EF</span><span class="sxs-lookup"><span data-stu-id="ecfe6-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="ecfe6-104">La base de données en mémoire EF se comporte souvent différemment des bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="ecfe6-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="ecfe6-105">Utilisez uniquement la base de données en mémoire EF après avoir pleinement compris les problèmes et les compromis impliqués, comme indiqué dans [test de code qui utilise EF Core](xref:core/testing/index).</span><span class="sxs-lookup"><span data-stu-id="ecfe6-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/testing/index).</span></span>

> [!TIP]
> <span data-ttu-id="ecfe6-106">SQLite est un fournisseur relationnel et peut également utiliser des bases de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="ecfe6-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="ecfe6-107">Envisagez de l’utiliser pour le test afin de mieux correspondre aux comportements de base de données relationnelle courants.</span><span class="sxs-lookup"><span data-stu-id="ecfe6-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="ecfe6-108">Ce sujet est abordé dans [utilisation de SQLite pour tester une application EF Core](xref:core/testing/sqlite).</span><span class="sxs-lookup"><span data-stu-id="ecfe6-108">This is covered in [Using SQLite to test an EF Core application](xref:core/testing/sqlite).</span></span>

<span data-ttu-id="ecfe6-109">Les informations de cette page se trouvent désormais dans d’autres emplacements :</span><span class="sxs-lookup"><span data-stu-id="ecfe6-109">The information on this page now lives in other locations:</span></span>

* <span data-ttu-id="ecfe6-110">Consultez [test du code qui utilise EF Core](xref:core/testing/index) pour obtenir des informations générales sur les tests avec la base de données EF en mémoire.</span><span class="sxs-lookup"><span data-stu-id="ecfe6-110">See [Testing code that uses EF Core](xref:core/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="ecfe6-111">Consultez l' [exemple illustrant comment tester des applications qui utilisent EF Core](xref:core/testing/testing-sample) pour obtenir un exemple utilisant la base de données EF en mémoire.</span><span class="sxs-lookup"><span data-stu-id="ecfe6-111">See [Sample showing how to test applications that use EF Core](xref:core/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="ecfe6-112">Pour obtenir des informations générales sur la base de données EF en mémoire, consultez [le fournisseur de base de données en mémoire EF](xref:core/providers/in-memory/index) .</span><span class="sxs-lookup"><span data-stu-id="ecfe6-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
