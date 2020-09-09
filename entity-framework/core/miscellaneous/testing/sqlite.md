---
title: Test avec SQLite-EF Core
description: Utilisation de SQLite pour tester une application Entity Framework Core
author: ajcvickers
ms.date: 04/24/2020
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: b580ed58cb9466c8eac32f71951734f4bd565733
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617693"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a><span data-ttu-id="38c9f-103">Utilisation de SQLite pour tester une application EF Core</span><span class="sxs-lookup"><span data-stu-id="38c9f-103">Using SQLite to test an EF Core application</span></span>

> [!WARNING]
> <span data-ttu-id="38c9f-104">L’utilisation de SQLite peut être un moyen efficace de tester une application EF Core.</span><span class="sxs-lookup"><span data-stu-id="38c9f-104">Using SQLite can be an effective way to test an EF Core application.</span></span>
> <span data-ttu-id="38c9f-105">Toutefois, des problèmes peuvent survenir lorsque SQLite se comporte différemment des autres systèmes de base de données.</span><span class="sxs-lookup"><span data-stu-id="38c9f-105">However, problems can arise where SQLite behaves differently from other database systems.</span></span> <span data-ttu-id="38c9f-106">Consultez [test du code qui utilise EF Core](xref:core/miscellaneous/testing/index) pour en savoir plus sur les problèmes et les compromis.</span><span class="sxs-lookup"><span data-stu-id="38c9f-106">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for a discussion of the issues and trade-offs.</span></span>  

<span data-ttu-id="38c9f-107">Ce document génère des utilisations sur les concepts introduits dans l' [exemple illustrant comment tester des applications qui utilisent EF Core](xref:core/miscellaneous/testing/testing-sample).</span><span class="sxs-lookup"><span data-stu-id="38c9f-107">This document builds uses on the concepts introduced in [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample).</span></span>
<span data-ttu-id="38c9f-108">Les exemples de code présentés ici proviennent de cet exemple.</span><span class="sxs-lookup"><span data-stu-id="38c9f-108">The code examples shown here come from this sample.</span></span>

## <a name="using-sqlite-in-memory-databases"></a><span data-ttu-id="38c9f-109">Utilisation de bases de données en mémoire SQLite</span><span class="sxs-lookup"><span data-stu-id="38c9f-109">Using SQLite in-memory databases</span></span>

<span data-ttu-id="38c9f-110">Normalement, SQLite crée des bases de données en tant que fichiers simples et accède au fichier in-process avec votre application.</span><span class="sxs-lookup"><span data-stu-id="38c9f-110">Normally, SQLite creates databases as simple files and accesses the file in-process with your application.</span></span>
<span data-ttu-id="38c9f-111">C’est très rapide, en particulier lors de l’utilisation d’un [SSD](https://en.wikipedia.org/wiki/Solid-state_drive)rapide.</span><span class="sxs-lookup"><span data-stu-id="38c9f-111">This is very fast, especially when using a fast [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span></span> 

<span data-ttu-id="38c9f-112">SQLite peut également utiliser des bases de données créées uniquement en mémoire.</span><span class="sxs-lookup"><span data-stu-id="38c9f-112">SQLite can also use databases created purely in-memory.</span></span>
<span data-ttu-id="38c9f-113">Cela est facile à utiliser avec EF Core tant que vous comprenez la durée de vie de la base de données en mémoire :</span><span class="sxs-lookup"><span data-stu-id="38c9f-113">This is easy to use with EF Core as long as you understand the in-memory database lifetime:</span></span>
* <span data-ttu-id="38c9f-114">La base de données est créée lors de l’ouverture de la connexion à celle-ci.</span><span class="sxs-lookup"><span data-stu-id="38c9f-114">The database is created when the connection to it is opened</span></span>
* <span data-ttu-id="38c9f-115">La base de données est supprimée lors de la fermeture de la connexion à celle-ci</span><span class="sxs-lookup"><span data-stu-id="38c9f-115">The database is deleted when the connection to it is closed</span></span>

<span data-ttu-id="38c9f-116">EF Core utilisera une connexion déjà ouverte lorsque vous en aurez donné une, et ne tentera pas de la fermer.</span><span class="sxs-lookup"><span data-stu-id="38c9f-116">EF Core will use an already open connection when given one, and will never attempt to close it.</span></span>
<span data-ttu-id="38c9f-117">La clé d’utilisation d’EF Core avec une base de données SQLite en mémoire consiste donc à ouvrir la connexion avant de la transmettre à EF.</span><span class="sxs-lookup"><span data-stu-id="38c9f-117">So the key to using EF Core with an in-memory SQLite database is to open the connection before passing it to EF.</span></span>  

<span data-ttu-id="38c9f-118">L' [exemple](xref:core/miscellaneous/testing/testing-sample) atteint ce code avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="38c9f-118">The [sample](xref:core/miscellaneous/testing/testing-sample) achieves this with the following code:</span></span>

[!code-csharp[SqliteInMemory](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

<span data-ttu-id="38c9f-119">Remarques :</span><span class="sxs-lookup"><span data-stu-id="38c9f-119">Notice:</span></span>
* <span data-ttu-id="38c9f-120">La `CreateInMemoryDatabase` méthode crée une base de données en mémoire SQLite et ouvre la connexion à celle-ci.</span><span class="sxs-lookup"><span data-stu-id="38c9f-120">The `CreateInMemoryDatabase` method creates a SQLite in-memory database and opens the connection to it.</span></span>
* <span data-ttu-id="38c9f-121">Le créé `DbConnection` est extrait du `ContextOptions` et enregistré.</span><span class="sxs-lookup"><span data-stu-id="38c9f-121">The created `DbConnection` is extracted from the `ContextOptions` and saved.</span></span>
* <span data-ttu-id="38c9f-122">La connexion est supprimée lorsque le test est supprimé afin que les ressources ne soient pas divulguées.</span><span class="sxs-lookup"><span data-stu-id="38c9f-122">The connection is disposed when the test is disposed so that resources are not leaked.</span></span> 

> [!NOTE]
> <span data-ttu-id="38c9f-123">[Problème #16103](https://github.com/dotnet/efcore/issues/16103) effectue le suivi des méthodes pour faciliter cette gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="38c9f-123">[Issue #16103](https://github.com/dotnet/efcore/issues/16103) is tracking ways to make this connection management easier.</span></span> 
