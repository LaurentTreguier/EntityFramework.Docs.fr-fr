---
title: Partage de bases de données entre des tests-EF Core
description: Exemple illustrant comment partager une base de données entre plusieurs tests
author: ajcvickers
ms.date: 04/25/2020
uid: core/miscellaneous/testing/sharing-databases
ms.openlocfilehash: 96216fb8afd6916402637de3ffab04b79d37e11c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564279"
---
# <a name="sharing-databases-between-tests"></a><span data-ttu-id="3cce7-103">Partage de bases de données entre des tests</span><span class="sxs-lookup"><span data-stu-id="3cce7-103">Sharing databases between tests</span></span>

<span data-ttu-id="3cce7-104">L' [exemple de test EF Core](xref:core/miscellaneous/testing/testing-sample) a montré comment tester des applications sur différents systèmes de base de données.</span><span class="sxs-lookup"><span data-stu-id="3cce7-104">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) showed how to test applications against different database systems.</span></span>
<span data-ttu-id="3cce7-105">Pour cet exemple, chaque test a créé une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="3cce7-105">For that sample, each test created a new database.</span></span>
<span data-ttu-id="3cce7-106">Il s’agit d’un bon modèle lors de l’utilisation de SQLite ou de la base de données en mémoire EF, mais cela peut impliquer une surcharge importante lors de l’utilisation d’autres systèmes de base de données.</span><span class="sxs-lookup"><span data-stu-id="3cce7-106">This is a good pattern when using SQLite or the EF in-memory database, but it can involve significant overhead when using other database systems.</span></span>

<span data-ttu-id="3cce7-107">Cet exemple s’appuie sur l’exemple précédent en déplaçant la création de la base de données dans un contexte de test.</span><span class="sxs-lookup"><span data-stu-id="3cce7-107">This sample builds on the previous sample by moving database creation into a test fixture.</span></span>
<span data-ttu-id="3cce7-108">Cela permet de créer une seule base de données SQL Server et de l’amorcer une seule fois pour tous les tests.</span><span class="sxs-lookup"><span data-stu-id="3cce7-108">This allows a single SQL Server database to be created and seeded only once for all tests.</span></span>

> [!TIP]
> <span data-ttu-id="3cce7-109">Veillez à utiliser l' [exemple de test EF Core](xref:core/miscellaneous/testing/testing-sample) avant de continuer.</span><span class="sxs-lookup"><span data-stu-id="3cce7-109">Make sure to work through the [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) before continuing here.</span></span>

<span data-ttu-id="3cce7-110">Il n’est pas difficile d’écrire plusieurs tests sur la même base de données.</span><span class="sxs-lookup"><span data-stu-id="3cce7-110">It's not difficult to write multiple tests against the same database.</span></span>
<span data-ttu-id="3cce7-111">L’astuce consiste à le faire de manière à ce que les tests ne s’exécutent pas les uns sur les autres au fur et à mesure de leur exécution.</span><span class="sxs-lookup"><span data-stu-id="3cce7-111">The trick is doing it in a way that the tests don't trip over each other as they run.</span></span>
<span data-ttu-id="3cce7-112">Pour cela, vous devez comprendre :</span><span class="sxs-lookup"><span data-stu-id="3cce7-112">This requires understanding:</span></span>
* <span data-ttu-id="3cce7-113">Comment partager en toute sécurité des objets entre des tests</span><span class="sxs-lookup"><span data-stu-id="3cce7-113">How to safely share objects between tests</span></span>
* <span data-ttu-id="3cce7-114">Lorsque l’infrastructure de test exécute des tests en parallèle</span><span class="sxs-lookup"><span data-stu-id="3cce7-114">When the test framework runs tests in parallel</span></span>
* <span data-ttu-id="3cce7-115">Comment conserver la base de données dans un état propre pour chaque test</span><span class="sxs-lookup"><span data-stu-id="3cce7-115">How to keep the database in a clean state for every test</span></span>  

## <a name="the-fixture"></a><span data-ttu-id="3cce7-116">Le contexte</span><span class="sxs-lookup"><span data-stu-id="3cce7-116">The fixture</span></span>

<span data-ttu-id="3cce7-117">Nous utiliserons un contexte de test pour partager des objets entre les tests.</span><span class="sxs-lookup"><span data-stu-id="3cce7-117">We will use a test fixture for sharing objects between tests.</span></span>
<span data-ttu-id="3cce7-118">La [documentation xUnit](https://xunit.net/docs/shared-context.html) stipule qu’un ablocage doit être utilisé quand vous souhaitez créer un contexte de test unique et le partager entre tous les tests de la classe et le nettoyer une fois tous les tests de la classe terminés.</span><span class="sxs-lookup"><span data-stu-id="3cce7-118">The [XUnit documentation](https://xunit.net/docs/shared-context.html) states that a fixture should be used "when you want to create a single test context and share it among all the tests in the class, and have it cleaned up after all the tests in the class have finished."</span></span>

> [!TIP]
> <span data-ttu-id="3cce7-119">Cet exemple utilise [xUnit](https://xunit.net/), mais des concepts similaires existent dans d’autres infrastructures de test, y compris [nunit](https://nunit.org/).</span><span class="sxs-lookup"><span data-stu-id="3cce7-119">This sample uses [XUnit](https://xunit.net/), but similar concepts exist in other testing frameworks, including [NUnit](https://nunit.org/).</span></span>

<span data-ttu-id="3cce7-120">Cela signifie que nous devons déplacer la création et l’amorçage de la base de données vers une classe de contexte.</span><span class="sxs-lookup"><span data-stu-id="3cce7-120">This means that we need to move database creation and seeding to a fixture class.</span></span>
<span data-ttu-id="3cce7-121">Voici à quoi elle ressemble :</span><span class="sxs-lookup"><span data-stu-id="3cce7-121">Here's what it looks like:</span></span>

[!code-csharp[SharedDatabaseFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

<span data-ttu-id="3cce7-122">Pour le moment, Notez comment le constructeur :</span><span class="sxs-lookup"><span data-stu-id="3cce7-122">For now, notice how the constructor:</span></span>
* <span data-ttu-id="3cce7-123">Crée une connexion de base de données unique pour la durée de vie de l’appareil</span><span class="sxs-lookup"><span data-stu-id="3cce7-123">Creates a single database connection for the lifetime of the fixture</span></span>
* <span data-ttu-id="3cce7-124">Crée et amorce cette base de données en `Seed` appelant la méthode.</span><span class="sxs-lookup"><span data-stu-id="3cce7-124">Creates and seeds that database by calling the `Seed` method</span></span> 

<span data-ttu-id="3cce7-125">Ignorer le verrouillage pour l’instant ; Nous y reviendrons plus tard.</span><span class="sxs-lookup"><span data-stu-id="3cce7-125">Ignore the locking for now; we will come back to it later.</span></span>

> [!TIP]
> <span data-ttu-id="3cce7-126">Le code de création et d’amorçage n’a pas besoin d’être asynchrone.</span><span class="sxs-lookup"><span data-stu-id="3cce7-126">The creation and seeding code does not need to be async.</span></span>
> <span data-ttu-id="3cce7-127">Le fait de le rendre asynchrone complique le code et n’améliore pas les performances ou le débit des tests.</span><span class="sxs-lookup"><span data-stu-id="3cce7-127">Making it async will complicate the code and will not improve performance or throughput of tests.</span></span>

<span data-ttu-id="3cce7-128">La base de données est créée en supprimant d’abord toute base de données existante, puis en créant une nouvelle base de données.</span><span class="sxs-lookup"><span data-stu-id="3cce7-128">The database is created by first deleting any existing database and then creating a new database.</span></span>
<span data-ttu-id="3cce7-129">Cela garantit que la base de données correspond au modèle EF actuel même si elle a été modifiée depuis la dernière série de tests.</span><span class="sxs-lookup"><span data-stu-id="3cce7-129">This ensures that the database matches the current EF model even if it has been changed since the last test run.</span></span>

> [!TIP]
> <span data-ttu-id="3cce7-130">Il peut être plus rapide de « nettoyer » la base de données existante à l’aide d’un exemple de [regénération](https://jimmybogard.com/tag/respawn/) , plutôt que de la recréer à chaque fois.</span><span class="sxs-lookup"><span data-stu-id="3cce7-130">It can be faster to "clean" the existing database using something like [respawn](https://jimmybogard.com/tag/respawn/) rather than re-create it each time.</span></span>
> <span data-ttu-id="3cce7-131">Toutefois, il est nécessaire de veiller à ce que le schéma de base de données soit à jour avec le modèle EF.</span><span class="sxs-lookup"><span data-stu-id="3cce7-131">However, care must be taken to ensure that the database schema is up-to-date with the EF model when doing this.</span></span>

<span data-ttu-id="3cce7-132">La connexion de base de données est supprimée lorsque le contexte est supprimé.</span><span class="sxs-lookup"><span data-stu-id="3cce7-132">The database connection is disposed when the fixture is disposed.</span></span>
<span data-ttu-id="3cce7-133">Vous pouvez également envisager de supprimer la base de données de test à ce stade.</span><span class="sxs-lookup"><span data-stu-id="3cce7-133">You may also consider deleting the test database at this point.</span></span>
<span data-ttu-id="3cce7-134">Toutefois, cette opération nécessite un verrouillage supplémentaire et un décompte de références si le contexte est partagé par plusieurs classes de test.</span><span class="sxs-lookup"><span data-stu-id="3cce7-134">However, this will require additional locking and reference counting if the fixture is being shared by multiple test classes.</span></span>
<span data-ttu-id="3cce7-135">En outre, il est souvent utile que la base de données de test soit toujours disponible pour le débogage des tests ayant échoué.</span><span class="sxs-lookup"><span data-stu-id="3cce7-135">Also, it is often useful to have the test database still available for debugging failed tests.</span></span>  

## <a name="using-the-fixture"></a><span data-ttu-id="3cce7-136">Utilisation du contexte</span><span class="sxs-lookup"><span data-stu-id="3cce7-136">Using the fixture</span></span>

<span data-ttu-id="3cce7-137">XUnit a un modèle commun pour associer un contexte de test à une classe de tests :</span><span class="sxs-lookup"><span data-stu-id="3cce7-137">XUnit has a common pattern for associating a test fixture with a class of tests:</span></span>

[!code-csharp[UsingTheFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

<span data-ttu-id="3cce7-138">XUnit va maintenant créer une instance de bride unique et la passer à chaque instance de la classe de test.</span><span class="sxs-lookup"><span data-stu-id="3cce7-138">XUnit will now create a single fixture instance and pass it to each instance of the test class.</span></span>
<span data-ttu-id="3cce7-139">(N’oubliez pas à partir du premier [exemple](xref:core/miscellaneous/testing/testing-sample) de test que xUnit crée une nouvelle instance de classe de test chaque fois qu’il exécute un test.) Cela signifie que la base de données sera créée et amorcée une fois, puis que chaque test utilisera cette base de données.</span><span class="sxs-lookup"><span data-stu-id="3cce7-139">(Remember from the first [testing sample](xref:core/miscellaneous/testing/testing-sample) that XUnit creates a new test class instance every time it runs a test.) This means that the database will be created and seeded once and then each test will use this database.</span></span>

<span data-ttu-id="3cce7-140">Notez que les tests au sein d’une même classe ne seront pas exécutés en parallèle.</span><span class="sxs-lookup"><span data-stu-id="3cce7-140">Note that tests within a single class will not be run in parallel.</span></span>
<span data-ttu-id="3cce7-141">Cela signifie qu’il est possible pour chaque test d’utiliser la même connexion de base de données `DbConnection` , même si l’objet n’est pas thread-safe.</span><span class="sxs-lookup"><span data-stu-id="3cce7-141">This means it is safe for each test to use the same database connection, even though the `DbConnection` object is not thread-safe.</span></span>

## <a name="maintaining-database-state"></a><span data-ttu-id="3cce7-142">Maintien de l’état de la base de données</span><span class="sxs-lookup"><span data-stu-id="3cce7-142">Maintaining database state</span></span>

<span data-ttu-id="3cce7-143">Les tests doivent souvent muter les données de test avec les insertions, les mises à jour et les suppressions.</span><span class="sxs-lookup"><span data-stu-id="3cce7-143">Tests often need to mutate the test data with inserts, updates, and deletes.</span></span>
<span data-ttu-id="3cce7-144">Toutefois, ces modifications ont un impact sur les autres tests qui attendent une base de données propre et amorcée.</span><span class="sxs-lookup"><span data-stu-id="3cce7-144">But these changes will then impact other tests which are expecting a clean, seeded database.</span></span>

<span data-ttu-id="3cce7-145">Cela peut être traité en exécutant des tests mutants à l’intérieur d’une transaction.</span><span class="sxs-lookup"><span data-stu-id="3cce7-145">This can be dealt with by running mutating tests inside a transaction.</span></span>
<span data-ttu-id="3cce7-146">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="3cce7-146">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

<span data-ttu-id="3cce7-147">Notez que la transaction est créée lors du démarrage du test et de sa suppression une fois l’opération terminée.</span><span class="sxs-lookup"><span data-stu-id="3cce7-147">Notice that the transaction is created as the test starts and disposed when it is finished.</span></span>
<span data-ttu-id="3cce7-148">Si vous supprimez la transaction, celle-ci est restaurée, de sorte qu’aucune des modifications ne sera visible par les autres tests.</span><span class="sxs-lookup"><span data-stu-id="3cce7-148">Disposing the transaction causes it to be rolled back, so none of the changes will be seen by other tests.</span></span>

<span data-ttu-id="3cce7-149">La méthode d’assistance pour créer un contexte (voir le code de la bride ci-dessus) accepte cette transaction et l’utilise pour l’utiliser.</span><span class="sxs-lookup"><span data-stu-id="3cce7-149">The helper method for creating a context (see the fixture code above) accepts this transaction and opts the DbContext into using it.</span></span> 

## <a name="sharing-the-fixture"></a><span data-ttu-id="3cce7-150">Partage du contexte</span><span class="sxs-lookup"><span data-stu-id="3cce7-150">Sharing the fixture</span></span>

<span data-ttu-id="3cce7-151">Vous avez peut-être remarqué le verrouillage du code autour de la création et de l’amorçage de la base de données.</span><span class="sxs-lookup"><span data-stu-id="3cce7-151">You may have noticed locking code around database creation and seeding.</span></span>
<span data-ttu-id="3cce7-152">Cela n’est pas nécessaire pour cet exemple, car une seule classe de tests utilise l’ablocage, de sorte qu’une seule instance de contexte est créée.</span><span class="sxs-lookup"><span data-stu-id="3cce7-152">This is not needed for this sample since only one class of tests use the fixture, so only a single fixture instance is created.</span></span>

<span data-ttu-id="3cce7-153">Toutefois, vous souhaiterez peut-être utiliser le même contexte avec plusieurs classes de tests.</span><span class="sxs-lookup"><span data-stu-id="3cce7-153">However, you may want to use the same fixture with multiple classes of tests.</span></span>
<span data-ttu-id="3cce7-154">XUnit créera une instance de bride pour chacune de ces classes.</span><span class="sxs-lookup"><span data-stu-id="3cce7-154">XUnit will create one fixture instance for each of these classes.</span></span>
<span data-ttu-id="3cce7-155">Elles peuvent être utilisées par différents threads exécutant des tests en parallèle.</span><span class="sxs-lookup"><span data-stu-id="3cce7-155">These may be used by different threads running tests in parallel.</span></span>
<span data-ttu-id="3cce7-156">Par conséquent, il est important de disposer d’un verrouillage approprié pour s’assurer qu’un seul thread effectue la création et l’amorçage de la base de données.</span><span class="sxs-lookup"><span data-stu-id="3cce7-156">Therefore, it is important to have appropriate locking to ensure only one thread does the database creation and seeding.</span></span>

> [!TIP]
> <span data-ttu-id="3cce7-157">Un simple `lock` est parfait ici.</span><span class="sxs-lookup"><span data-stu-id="3cce7-157">A simple `lock` is fine here.</span></span>
> <span data-ttu-id="3cce7-158">Il n’est pas nécessaire d’essayer quelque chose de plus complexe, comme les modèles sans verrou.</span><span class="sxs-lookup"><span data-stu-id="3cce7-158">There is no need to attempt anything more complex, such as any lock-free patterns.</span></span>
