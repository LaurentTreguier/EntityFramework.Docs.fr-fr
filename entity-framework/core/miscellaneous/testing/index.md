---
title: Test de code qui utilise EF Core - EF Core
description: Différentes approches possibles pour tester les applications qui utilisent Entity Framework Core.
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/index
ms.openlocfilehash: c55290f4af81a49bf7ab131ebe93af209f96b430
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617702"
---
# <a name="testing-code-that-uses-ef-core"></a><span data-ttu-id="1c6af-103">Test de code qui utilise EF Core</span><span class="sxs-lookup"><span data-stu-id="1c6af-103">Testing code that uses EF Core</span></span>

<span data-ttu-id="1c6af-104">Pour tester le code qui accède à une base de données, vous devez adopter l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="1c6af-104">Testing code that accesses a database requires either:</span></span>

* <span data-ttu-id="1c6af-105">Exécuter des requêtes et des mises à jour sur le même système de base de données que celui utilisé en production.</span><span class="sxs-lookup"><span data-stu-id="1c6af-105">Running queries and updates against the same database system used in production.</span></span>
* <span data-ttu-id="1c6af-106">Exécuter des requêtes et des mises à jour sur un autre système de base de données plus facile à gérer.</span><span class="sxs-lookup"><span data-stu-id="1c6af-106">Running queries and updates against some other easier to manage database system.</span></span>
* <span data-ttu-id="1c6af-107">Utiliser des doubles de test ou un autre mécanisme pour éviter d’utiliser une base de données.</span><span class="sxs-lookup"><span data-stu-id="1c6af-107">Using test doubles or some other mechanism to avoid using a database at all.</span></span>

<span data-ttu-id="1c6af-108">Ce document décrit les compromis liés à chacun de ces choix et montre comment utiliser EF Core avec chaque approche.</span><span class="sxs-lookup"><span data-stu-id="1c6af-108">This document outlines the trade-offs involved in each of these choices and shows how EF Core can be used with each approach.</span></span>  

> [!TIP]
> <span data-ttu-id="1c6af-109">Pour obtenir du code illustrant les concepts introduits ici, consultez l’[exemple de test EF Core](xref:core/miscellaneous/testing/testing-sample).</span><span class="sxs-lookup"><span data-stu-id="1c6af-109">See [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) for code demonstrating the concepts introduced here.</span></span>

## <a name="all-database-providers-are-not-equal"></a><span data-ttu-id="1c6af-110">Tous les fournisseurs de base de données ne sont pas égaux</span><span class="sxs-lookup"><span data-stu-id="1c6af-110">All database providers are not equal</span></span>

<span data-ttu-id="1c6af-111">Il est très important de comprendre qu’EF Core n’est pas conçu pour effectuer l’abstraction de tous les aspects du système de base de données sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="1c6af-111">It is very important to understand that EF Core is not designed to abstract every aspect of the underlying database system.</span></span>
<span data-ttu-id="1c6af-112">Au lieu de cela, EF Core est un ensemble commun de modèles et de concepts qui peuvent être utilisés avec n’importe quel système de base de données.</span><span class="sxs-lookup"><span data-stu-id="1c6af-112">Instead, EF Core is a common set of patterns and concepts that can be used with any database system.</span></span>
<span data-ttu-id="1c6af-113">Les fournisseurs de base de données EF Core superposent ensuite le comportement et les fonctionnalités propres à la base de données sur ce framework commun.</span><span class="sxs-lookup"><span data-stu-id="1c6af-113">EF Core database providers then layer database-specific behavior and functionality over this common framework.</span></span>
<span data-ttu-id="1c6af-114">Cela permet à chaque système de base de données de faire ce qu’il fait le mieux, tout en maintenant une compatibilité, le cas échéant, avec d’autres systèmes de base de données.</span><span class="sxs-lookup"><span data-stu-id="1c6af-114">This allows each database system to do what it does best while still maintaining commonality, where appropriate, with other database systems.</span></span>

<span data-ttu-id="1c6af-115">Fondamentalement, cela signifie que le fait de changer de fournisseur de base de données changera le comportement d’EF Core, et que l’application ne fonctionnera pas correctement à moins de tenir compte explicitement de toute différence de comportement.</span><span class="sxs-lookup"><span data-stu-id="1c6af-115">Fundamentally, this means that switching out the database provider will change EF Core behavior and the application can't be expected to function correctly unless it explicitly accounts for any differences in behavior.</span></span>
<span data-ttu-id="1c6af-116">Cela dit, dans de nombreux cas cela fonctionnera car il existe un degré élevé de normalisation entre les bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="1c6af-116">That being said, in many cases doing this will work because there is a high degree of commonality amongst relational databases.</span></span>
<span data-ttu-id="1c6af-117">C’est à la fois bien et embêtant.</span><span class="sxs-lookup"><span data-stu-id="1c6af-117">This is good and bad.</span></span>
<span data-ttu-id="1c6af-118">Bien, car le passage d’un système de base de données à un autre peut être relativement simple.</span><span class="sxs-lookup"><span data-stu-id="1c6af-118">Good because moving between database systems can be relatively easy.</span></span>
<span data-ttu-id="1c6af-119">Embêtant, car cela risque de procurer un faux sentiment de sécurité si l’application n’est pas entièrement testée sur le nouveau système de base de données.</span><span class="sxs-lookup"><span data-stu-id="1c6af-119">Bad because it can give a false sense of security if the application is not fully tested against the new database system.</span></span>  

## <a name="approach-1-production-database-system"></a><span data-ttu-id="1c6af-120">Approche 1 : système de base de données de production</span><span class="sxs-lookup"><span data-stu-id="1c6af-120">Approach 1: Production database system</span></span>

<span data-ttu-id="1c6af-121">Comme décrit dans la section précédente, le seul moyen d’être sûr de tester ce qui s’exécute en production consiste à utiliser le même système de base de données.</span><span class="sxs-lookup"><span data-stu-id="1c6af-121">As described in the previous section, the only way to be sure you are testing what runs in production is to use the same database system.</span></span>
<span data-ttu-id="1c6af-122">Par exemple, si l’application déployée utilise SQL Azure, les tests doivent également être exécutés par rapport à SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="1c6af-122">For example, if the deployed application uses SQL Azure, then testing should also be done against SQL Azure.</span></span>

<span data-ttu-id="1c6af-123">Toutefois, demander à chaque développeur d’exécuter des tests par rapport à SQL Azure tout en travaillant activement sur le code serait lent et coûteux.</span><span class="sxs-lookup"><span data-stu-id="1c6af-123">However, having every developer run tests against SQL Azure while actively working on the code would be both slow and expensive.</span></span>
<span data-ttu-id="1c6af-124">Cela illustre le principal compromis lié à ces approches : quand convient-il de dévier du système de base de données de production afin d’améliorer l’efficacité des tests ?</span><span class="sxs-lookup"><span data-stu-id="1c6af-124">This illustrates the main trade-off involved throughout these approaches: when is it appropriate to deviate from the production database system so as to improve test efficiency?</span></span>

<span data-ttu-id="1c6af-125">Heureusement, dans ce cas la réponse est très facile : utilisez des serveurs SQL locaux pour les tests des développeurs.</span><span class="sxs-lookup"><span data-stu-id="1c6af-125">Luckily, in this case the answer is quite easy: use local or on-premises SQL Server for developer testing.</span></span>
<span data-ttu-id="1c6af-126">SQL Azure et SQL Server sont extrêmement similaires ; exécuter des tests par rapport à SQL Server constitue donc généralement un compromis raisonnable.</span><span class="sxs-lookup"><span data-stu-id="1c6af-126">SQL Azure and SQL Server are extremely similar, so testing against SQL Server is usually a reasonable trade-off.</span></span>
<span data-ttu-id="1c6af-127">Cela dit, il est toujours prudent d’exécuter des tests par rapport à SQL Azure lui-même avant de passer en production.</span><span class="sxs-lookup"><span data-stu-id="1c6af-127">That being said, it is still wise to run tests against SQL Azure itself before going into production.</span></span>

### <a name="localdb"></a><span data-ttu-id="1c6af-128">LocalDB</span><span class="sxs-lookup"><span data-stu-id="1c6af-128">LocalDB</span></span>

<span data-ttu-id="1c6af-129">Tous les principaux systèmes de base de données ont une forme de « Developer Edition » pour les tests locaux.</span><span class="sxs-lookup"><span data-stu-id="1c6af-129">All the major database systems have some form of "Developer Edition" for local testing.</span></span>
<span data-ttu-id="1c6af-130">SQL Server offre une fonctionnalité nommée [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="1c6af-130">SQL Server also has a feature called [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb).</span></span>
<span data-ttu-id="1c6af-131">Le principal avantage de LocalDB est qu’elle fait tourner l’instance de base de données à la demande.</span><span class="sxs-lookup"><span data-stu-id="1c6af-131">The primary advantage of LocalDB is that it spins up the database instance on demand.</span></span>
<span data-ttu-id="1c6af-132">Cela évite d’avoir un service de base de données en cours d’exécution sur votre ordinateur même quand vous n’exécutez pas de tests.</span><span class="sxs-lookup"><span data-stu-id="1c6af-132">This avoids having a database service running on your machine even when you're not running tests.</span></span>

<span data-ttu-id="1c6af-133">LocalDB n’est pas sans problème :</span><span class="sxs-lookup"><span data-stu-id="1c6af-133">LocalDB is not without its issues:</span></span>

* <span data-ttu-id="1c6af-134">Elle ne prend pas en charge tout ce que [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15&preserve-view=true) prend en charge</span><span class="sxs-lookup"><span data-stu-id="1c6af-134">It doesn't support everything that [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15&preserve-view=true) does.</span></span>
* <span data-ttu-id="1c6af-135">Elle n’est pas disponible sur Linux</span><span class="sxs-lookup"><span data-stu-id="1c6af-135">It isn't available on Linux.</span></span>
* <span data-ttu-id="1c6af-136">Elle peut entraîner un décalage lors de la première série de tests quand le service est lancé</span><span class="sxs-lookup"><span data-stu-id="1c6af-136">It can cause lag on first test run as the service is spun up.</span></span>

<span data-ttu-id="1c6af-137">Personnellement, je n’ai jamais pensé que c’était un problème d’avoir un service de base de données en cours d’exécution sur mon ordinateur de développement, et je recommande généralement d’utiliser Developer Edition à la place.</span><span class="sxs-lookup"><span data-stu-id="1c6af-137">Personally, I've never found it a problem having a database service running on my dev machine and I would generally recommend using Developer Edition instead.</span></span>
<span data-ttu-id="1c6af-138">Toutefois, LocalDB peut convenir à certaines personnes, en particulier sur les ordinateurs de développement moins puissants.</span><span class="sxs-lookup"><span data-stu-id="1c6af-138">However, LocalDB may be appropriate for some people, especially on less powerful dev machines.</span></span>

<span data-ttu-id="1c6af-139">[L’exécution de SQL Server](/sql/linux/quickstart-install-connect-docker) (ou de tout autre système de base de données) dans un conteneur Docker (ou similaire) est une autre façon d’éviter d’exécuter le système de base de données directement sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="1c6af-139">[Running SQL Server](/sql/linux/quickstart-install-connect-docker) (or any other database system) in a Docker container (or similar) is another way to avoid running the database system directly on your development machine.</span></span>  

## <a name="approach-2-sqlite"></a><span data-ttu-id="1c6af-140">Approche 2 : SQLite</span><span class="sxs-lookup"><span data-stu-id="1c6af-140">Approach 2: SQLite</span></span>

<span data-ttu-id="1c6af-141">EF Core teste le fournisseur SQL Server principalement en l’exécutant sur une instance de SQL Server locale.</span><span class="sxs-lookup"><span data-stu-id="1c6af-141">EF Core tests the SQL Server provider primarily by running it against a local SQL Server instance.</span></span>
<span data-ttu-id="1c6af-142">Ces tests exécutent des dizaines de milliers de requêtes en quelques minutes sur une machine rapide.</span><span class="sxs-lookup"><span data-stu-id="1c6af-142">These tests run tens of thousands of queries in a couple of minutes on a fast machine.</span></span>
<span data-ttu-id="1c6af-143">Cela montre que l’utilisation du véritable système de base de données peut être une solution performante.</span><span class="sxs-lookup"><span data-stu-id="1c6af-143">This illustrates that using the real database system can be a performant solution.</span></span>
<span data-ttu-id="1c6af-144">C’est une idée fausse de penser qu’utiliser une base de données plus légère est la seule façon d’exécuter des tests rapidement.</span><span class="sxs-lookup"><span data-stu-id="1c6af-144">It is a myth that using some lighter-weight database is the only way to run tests quickly.</span></span>

<span data-ttu-id="1c6af-145">Mais que se passe-t-il si, pour une raison quelconque, vous ne pouvez pas exécuter des tests à proximité de votre système de base de données de production ?</span><span class="sxs-lookup"><span data-stu-id="1c6af-145">That being said, what if for whatever reason you can't run tests against something close to your production database system?</span></span>
<span data-ttu-id="1c6af-146">Dans ce cas, le meilleur choix consiste à utiliser quelque chose ayant des fonctionnalités similaires,</span><span class="sxs-lookup"><span data-stu-id="1c6af-146">The next best choice is to use something with similar functionality.</span></span>
<span data-ttu-id="1c6af-147">généralement une autre base de données relationnelle, [SQLite](https://sqlite.org/index.html) étant ici le choix évident.</span><span class="sxs-lookup"><span data-stu-id="1c6af-147">This usually means another relational database, for which [SQLite](https://sqlite.org/index.html) is the obvious choice.</span></span>

<span data-ttu-id="1c6af-148">SQLite est un bon choix pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="1c6af-148">SQLite is a good choice because:</span></span>

* <span data-ttu-id="1c6af-149">Il s’exécute In-process avec votre application, et a donc une faible surcharge.</span><span class="sxs-lookup"><span data-stu-id="1c6af-149">It runs in-process with your application and so has low overhead.</span></span>
* <span data-ttu-id="1c6af-150">Il utilise des fichiers simples et créés automatiquement pour les bases de données, et ne nécessite donc pas de gestion de base de données.</span><span class="sxs-lookup"><span data-stu-id="1c6af-150">It uses simple, automatically created files for databases, and so doesn't require database management.</span></span>
* <span data-ttu-id="1c6af-151">Il a un mode « en mémoire » qui évite même la création de fichiers.</span><span class="sxs-lookup"><span data-stu-id="1c6af-151">It has an in-memory mode that avoids even the file creation.</span></span>

<span data-ttu-id="1c6af-152">Toutefois, n’oubliez pas que :</span><span class="sxs-lookup"><span data-stu-id="1c6af-152">However, remember that:</span></span>

* <span data-ttu-id="1c6af-153">SQLite ne prend pas en charge toutes les opérations prises en charge par votre système de base de données de production.</span><span class="sxs-lookup"><span data-stu-id="1c6af-153">SQLite inevitability doesn't support everything that your production database system does.</span></span>
* <span data-ttu-id="1c6af-154">SQLite se comportera différemment de votre système de base de données de production pour certaines requêtes.</span><span class="sxs-lookup"><span data-stu-id="1c6af-154">SQLite will behave differently than your production database system for some queries.</span></span>

<span data-ttu-id="1c6af-155">Par conséquent, si vous utilisez SQLite pour certains tests, veillez également à tester votre système de base de données réel.</span><span class="sxs-lookup"><span data-stu-id="1c6af-155">So if you do use SQLite for some testing, make sure to also test against your real database system.</span></span>

<span data-ttu-id="1c6af-156">Pour obtenir des instructions propres à EF Core, consultez [Test avec SQLite](xref:core/miscellaneous/testing/sqlite).</span><span class="sxs-lookup"><span data-stu-id="1c6af-156">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for EF Core specific guidance.</span></span>

## <a name="approach-3-the-ef-core-in-memory-database"></a><span data-ttu-id="1c6af-157">Approche 3 : la base de données en mémoire EF Core</span><span class="sxs-lookup"><span data-stu-id="1c6af-157">Approach 3: The EF Core in-memory database</span></span>

<span data-ttu-id="1c6af-158">EF Core est fourni avec une base de données en mémoire que nous utilisons pour les tests internes d’EF Core lui-même.</span><span class="sxs-lookup"><span data-stu-id="1c6af-158">EF Core comes with an in-memory database that we use for internal testing of EF Core itself.</span></span>
<span data-ttu-id="1c6af-159">Cette base de données n’est en général **pas appropriée pour les tests des applications qui utilisent EF Core**.</span><span class="sxs-lookup"><span data-stu-id="1c6af-159">This database is in general **not suitable for testing applications that use EF Core**.</span></span> <span data-ttu-id="1c6af-160">Plus précisément :</span><span class="sxs-lookup"><span data-stu-id="1c6af-160">Specifically:</span></span>

* <span data-ttu-id="1c6af-161">Il ne s’agit pas d’une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="1c6af-161">It is not a relational database.</span></span>
* <span data-ttu-id="1c6af-162">Elle ne prend pas en charge les transactions.</span><span class="sxs-lookup"><span data-stu-id="1c6af-162">It doesn't support transactions.</span></span>
* <span data-ttu-id="1c6af-163">Elle ne peut pas exécuter de requêtes SQL brutes.</span><span class="sxs-lookup"><span data-stu-id="1c6af-163">It cannot run raw SQL queries.</span></span>
* <span data-ttu-id="1c6af-164">Elle n’est pas optimisée pour les performances.</span><span class="sxs-lookup"><span data-stu-id="1c6af-164">It is not optimized for performance.</span></span>

<span data-ttu-id="1c6af-165">Aucun de ces éléments n’est très important lors du test des composants internes d’EF Core, car nous les utilisons spécifiquement là où la base de données n’a pas d’importance pour le test.</span><span class="sxs-lookup"><span data-stu-id="1c6af-165">None of this is very important when testing EF Core internals because we use it specifically where the database is irrelevant to the test.</span></span>
<span data-ttu-id="1c6af-166">En revanche, ces éléments ont tendance à être très importants lors du test d’une application qui utilise EF Core.</span><span class="sxs-lookup"><span data-stu-id="1c6af-166">On the other hand, these things tend to be very important when testing an application that uses EF Core.</span></span>

## <a name="unit-testing"></a><span data-ttu-id="1c6af-167">Test unitaire</span><span class="sxs-lookup"><span data-stu-id="1c6af-167">Unit testing</span></span>

<span data-ttu-id="1c6af-168">Pensez à tester une partie de la logique métier qui peut nécessiter l’utilisation de certaines données d’une base de données, mais qui ne teste pas fondamentalement les interactions de la base de données.</span><span class="sxs-lookup"><span data-stu-id="1c6af-168">Consider testing a piece of business logic that might need to use some data from a database, but is not inherently testing the database interactions.</span></span>
<span data-ttu-id="1c6af-169">L’une des options consiste à utiliser un [double de test](https://en.wikipedia.org/wiki/Test_double), comme un test fictif.</span><span class="sxs-lookup"><span data-stu-id="1c6af-169">One option is to use a [test double](https://en.wikipedia.org/wiki/Test_double) such as a mock or fake.</span></span>

<span data-ttu-id="1c6af-170">Nous utilisons des doubles de test pour les tests internes d’EF Core.</span><span class="sxs-lookup"><span data-stu-id="1c6af-170">We use test doubles for internal testing of EF Core.</span></span>
<span data-ttu-id="1c6af-171">Toutefois, nous n’essayons jamais de simuler DbContext ou IQueryable.</span><span class="sxs-lookup"><span data-stu-id="1c6af-171">However, we never try to mock DbContext or IQueryable.</span></span>
<span data-ttu-id="1c6af-172">Ceci est difficile, fastidieux et fragile.</span><span class="sxs-lookup"><span data-stu-id="1c6af-172">Doing so is difficult, cumbersome, and fragile.</span></span>
<span data-ttu-id="1c6af-173">**Ne le faites pas.**</span><span class="sxs-lookup"><span data-stu-id="1c6af-173">**Don't do it.**</span></span>

<span data-ttu-id="1c6af-174">Au lieu de cela, nous utilisons la base de données en mémoire EF quand nous exécutons des tests unitaires sur quelque chose qui utilise DbContext.</span><span class="sxs-lookup"><span data-stu-id="1c6af-174">Instead we use the EF in-memory database when unit testing something that uses DbContext.</span></span>
<span data-ttu-id="1c6af-175">Dans ce cas, l’utilisation de la base de données en mémoire EF est appropriée, car le test ne dépend pas du comportement de la base de données.</span><span class="sxs-lookup"><span data-stu-id="1c6af-175">In this case using the EF in-memory database is appropriate because the test is not dependent on database behavior.</span></span>
<span data-ttu-id="1c6af-176">Mais ne procédez pas ainsi pour tester des mises à jour ou des requêtes de base de données réelles.</span><span class="sxs-lookup"><span data-stu-id="1c6af-176">Just don't do this to test actual database queries or updates.</span></span>

<span data-ttu-id="1c6af-177">L’[exemple de test EF Core](xref:core/miscellaneous/testing/testing-sample) illustre des tests avec la base de données en mémoire EF, ainsi que SQL Server et SQLite.</span><span class="sxs-lookup"><span data-stu-id="1c6af-177">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) demonstrates tests using the EF in-memory database, as well as SQL Server and SQLite.</span></span>
