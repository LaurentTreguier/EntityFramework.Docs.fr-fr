---
title: ''
description: ''
author: ''
ms.date: ''
uid: ''
no-loc:
- Item
- Tag
- Items
- Tags
ms.openlocfilehash: ae073fc0b3a99fb9de07a3e0a42c638fe0838a5a
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672813"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="34e77-101">Exemple de test EF Core</span><span class="sxs-lookup"><span data-stu-id="34e77-101">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="34e77-102">Vous trouverez le code de ce document sur GitHub en tant qu' [exemple exécutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span><span class="sxs-lookup"><span data-stu-id="34e77-102">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="34e77-103">Notez que certains de ces tests **sont censés échouer**.</span><span class="sxs-lookup"><span data-stu-id="34e77-103">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="34e77-104">Les raisons sont expliquées ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="34e77-104">The reasons for this are explained below.</span></span> 

<span data-ttu-id="34e77-105">Ce document décrit un exemple de test de code qui utilise EF Core.</span><span class="sxs-lookup"><span data-stu-id="34e77-105">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="34e77-106">L'application</span><span class="sxs-lookup"><span data-stu-id="34e77-106">The application</span></span>

<span data-ttu-id="34e77-107">L' [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contient deux projets :</span><span class="sxs-lookup"><span data-stu-id="34e77-107">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>
- <span data-ttu-id="34e77-108">ItemsWebApi : [API Web très simple sauvegardée par ASP.net Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) avec un seul contrôleur</span><span class="sxs-lookup"><span data-stu-id="34e77-108">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) with a single controller</span></span>
- <span data-ttu-id="34e77-109">Tests : projet de test [xUnit](https://xunit.net/) pour tester le contrôleur</span><span class="sxs-lookup"><span data-stu-id="34e77-109">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="34e77-110">Le modèle et les règles d’entreprise</span><span class="sxs-lookup"><span data-stu-id="34e77-110">The model and business rules</span></span>

<span data-ttu-id="34e77-111">Le modèle qui stocke cette API a deux types d’entité : Items et Tags .</span><span class="sxs-lookup"><span data-stu-id="34e77-111">The model backing this API has two entity types: Items and Tags.</span></span>

* Items<span data-ttu-id="34e77-112">avoir un nom qui respecte la casse et une collection de Tags .</span><span class="sxs-lookup"><span data-stu-id="34e77-112"> have a case-sensitive name and a collection of Tags.</span></span>
* <span data-ttu-id="34e77-113">Chacun Tag a une étiquette et un nombre qui représente le nombre de fois où il a été appliqué à l' Item .</span><span class="sxs-lookup"><span data-stu-id="34e77-113">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
* <span data-ttu-id="34e77-114">Chaque Item doit être Tag associé à une étiquette donnée.</span><span class="sxs-lookup"><span data-stu-id="34e77-114">Each Item should only have one Tag with a given label.</span></span>
  * <span data-ttu-id="34e77-115">Si un élément est balisé plusieurs fois avec la même étiquette, le décompte de la balise existante avec cette étiquette est incrémenté au lieu d’une nouvelle balise créée.</span><span class="sxs-lookup"><span data-stu-id="34e77-115">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span> 
* <span data-ttu-id="34e77-116">La suppression d’un Item doit supprimer tous les associés Tags .</span><span class="sxs-lookup"><span data-stu-id="34e77-116">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-item-entity-type"></a><span data-ttu-id="34e77-117">Le Item type d’entité</span><span class="sxs-lookup"><span data-stu-id="34e77-117">The Item entity type</span></span>

<span data-ttu-id="34e77-118">Le `Item` type d’entité :</span><span class="sxs-lookup"><span data-stu-id="34e77-118">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="34e77-119">Et sa configuration dans `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="34e77-119">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="34e77-120">Notez que le type d’entité limite la manière dont il peut être utilisé pour refléter le modèle de domaine et les règles d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="34e77-120">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="34e77-121">En particulier :</span><span class="sxs-lookup"><span data-stu-id="34e77-121">In particular:</span></span>
- <span data-ttu-id="34e77-122">La clé primaire est mappée directement au `_id` champ et n’est pas exposée publiquement</span><span class="sxs-lookup"><span data-stu-id="34e77-122">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="34e77-123">EF détecte et utilise le constructeur privé qui accepte la valeur et le nom de la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="34e77-123">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="34e77-124">La `Name` propriété est en lecture seule et définie uniquement dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="34e77-124">The `Name` property is read-only and set only in the constructor.</span></span> 
- Tags<span data-ttu-id="34e77-125">sont exposés en tant que `IReadOnlyList<Tag>` pour empêcher une modification arbitraire.</span><span class="sxs-lookup"><span data-stu-id="34e77-125"> are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="34e77-126">EF associe la `Tags` propriété au `_tags` champ de stockage en faisant correspondre leurs noms.</span><span class="sxs-lookup"><span data-stu-id="34e77-126">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span> 
  - <span data-ttu-id="34e77-127">La `AddTag` méthode prend une étiquette de balise et implémente la règle d’entreprise décrite ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="34e77-127">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="34e77-128">Autrement dit, une balise est ajoutée uniquement pour les nouvelles étiquettes.</span><span class="sxs-lookup"><span data-stu-id="34e77-128">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="34e77-129">Sinon, le nombre sur une étiquette existante est incrémenté.</span><span class="sxs-lookup"><span data-stu-id="34e77-129">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="34e77-130">La `Tags` propriété de navigation est configurée pour une relation plusieurs-à-un</span><span class="sxs-lookup"><span data-stu-id="34e77-130">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="34e77-131">Il n’est pas nécessaire d’utiliser une propriété de navigation de Tag à Item , donc elle n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="34e77-131">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="34e77-132">En outre, Tag ne définit pas une propriété de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="34e77-132">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="34e77-133">Dans ce cas, EF crée et gère une propriété en état Shadow.</span><span class="sxs-lookup"><span data-stu-id="34e77-133">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-tag-entity-type"></a><span data-ttu-id="34e77-134">Le Tag type d’entité</span><span class="sxs-lookup"><span data-stu-id="34e77-134">The Tag entity type</span></span>

<span data-ttu-id="34e77-135">Le `Tag` type d’entité :</span><span class="sxs-lookup"><span data-stu-id="34e77-135">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="34e77-136">Et sa configuration dans `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="34e77-136">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="34e77-137">De même Item que, Tag masque sa clé primaire et définit la `Label` propriété en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="34e77-137">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-itemscontroller"></a><span data-ttu-id="34e77-138">ItemsController</span><span class="sxs-lookup"><span data-stu-id="34e77-138">The ItemsController</span></span>

<span data-ttu-id="34e77-139">Le contrôleur d’API Web est plutôt basique.</span><span class="sxs-lookup"><span data-stu-id="34e77-139">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="34e77-140">Elle obtient un `DbContext` à partir du conteneur d’injection de dépendances via l’injection de constructeur :</span><span class="sxs-lookup"><span data-stu-id="34e77-140">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="34e77-141">Il possède des méthodes pour obtenir tout Items ou un Item avec un nom donné :</span><span class="sxs-lookup"><span data-stu-id="34e77-141">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="34e77-142">Il a une méthode pour ajouter un nouveau Item :</span><span class="sxs-lookup"><span data-stu-id="34e77-142">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="34e77-143">Méthode pour baliser un Item avec une étiquette :</span><span class="sxs-lookup"><span data-stu-id="34e77-143">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="34e77-144">Et une méthode pour supprimer un Item et tous les associés Tags :</span><span class="sxs-lookup"><span data-stu-id="34e77-144">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="34e77-145">La plupart des validations et des erreurs ont été supprimées pour réduire l’encombrement.</span><span class="sxs-lookup"><span data-stu-id="34e77-145">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="34e77-146">Les tests</span><span class="sxs-lookup"><span data-stu-id="34e77-146">The Tests</span></span>

<span data-ttu-id="34e77-147">Les tests sont organisés pour s’exécuter avec plusieurs configurations de fournisseur de base de données :</span><span class="sxs-lookup"><span data-stu-id="34e77-147">The tests are organized to run with multiple database provider configurations:</span></span>
* <span data-ttu-id="34e77-148">Fournisseur SQL Server, qui est le fournisseur utilisé par l’application</span><span class="sxs-lookup"><span data-stu-id="34e77-148">The SQL Server provider, which is the provider used by the application</span></span>
* <span data-ttu-id="34e77-149">Fournisseur SQLite</span><span class="sxs-lookup"><span data-stu-id="34e77-149">The SQLite provider</span></span>
* <span data-ttu-id="34e77-150">Fournisseur SQLite utilisant des bases de données SQLite en mémoire</span><span class="sxs-lookup"><span data-stu-id="34e77-150">The SQLite provider using in-memory SQLite databases</span></span>
* <span data-ttu-id="34e77-151">Fournisseur de base de données EF en mémoire</span><span class="sxs-lookup"><span data-stu-id="34e77-151">The EF in-memory database provider</span></span>

<span data-ttu-id="34e77-152">Pour ce faire, vous devez placer tous les tests dans une classe de base, puis hériter de ce pour effectuer un test avec chaque fournisseur.</span><span class="sxs-lookup"><span data-stu-id="34e77-152">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="34e77-153">Si vous n’utilisez pas la base de données locale, vous devez modifier la chaîne de connexion SQL Server.</span><span class="sxs-lookup"><span data-stu-id="34e77-153">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>

> [!TIP]
> <span data-ttu-id="34e77-154">Pour obtenir des conseils sur l’utilisation de SQLite pour le test en mémoire, consultez [test avec SQLite](xref:core/miscellaneous/testing/sqlite) .</span><span class="sxs-lookup"><span data-stu-id="34e77-154">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span> 

<span data-ttu-id="34e77-155">Les deux tests suivants sont censés échouer :</span><span class="sxs-lookup"><span data-stu-id="34e77-155">The following two tests are expected to fail:</span></span>
* <span data-ttu-id="34e77-156">`Can_remove_item_and_all_associated_tags`en cas d’exécution avec le fournisseur de base de données EF en mémoire</span><span class="sxs-lookup"><span data-stu-id="34e77-156">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
* <span data-ttu-id="34e77-157">`Can_add_item_differing_only_by_case`en cas d’exécution avec le fournisseur SQL Server</span><span class="sxs-lookup"><span data-stu-id="34e77-157">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="34e77-158">Ce sujet est abordé plus en détail ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="34e77-158">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="34e77-159">Configuration et amorçage de la base de données</span><span class="sxs-lookup"><span data-stu-id="34e77-159">Setting up and seeding the database</span></span>

<span data-ttu-id="34e77-160">XUnit, comme la plupart des frameworks de test, créera une nouvelle instance de classe de test pour chaque série de tests.</span><span class="sxs-lookup"><span data-stu-id="34e77-160">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="34e77-161">En outre, XUnit n’exécutera pas de tests dans une classe de test donnée en parallèle.</span><span class="sxs-lookup"><span data-stu-id="34e77-161">Also, XUnit will not run tests within a given test class in parallel.</span></span> <span data-ttu-id="34e77-162">Cela signifie que nous pouvons configurer et configurer la base de données dans le constructeur de test et qu’elle sera dans un état connu pour chaque test.</span><span class="sxs-lookup"><span data-stu-id="34e77-162">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="34e77-163">Cet exemple recrée la base de données pour chaque test.</span><span class="sxs-lookup"><span data-stu-id="34e77-163">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="34e77-164">Cela fonctionne bien pour les tests de base de données SQLite et EF en mémoire, mais peut impliquer une surcharge importante avec d’autres systèmes de base de données, y compris SQL Server.</span><span class="sxs-lookup"><span data-stu-id="34e77-164">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="34e77-165">Les approches pour réduire cette surcharge sont traitées dans [partage des bases de données entre les tests](xref:core/miscellaneous/testing/sharing-databases).</span><span class="sxs-lookup"><span data-stu-id="34e77-165">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/miscellaneous/testing/sharing-databases).</span></span>

<span data-ttu-id="34e77-166">Quand chaque test est exécuté :</span><span class="sxs-lookup"><span data-stu-id="34e77-166">When each test is run:</span></span>
* <span data-ttu-id="34e77-167">Les DbContextOptions sont configurés pour le fournisseur en cours d’utilisation et passés au constructeur de classe de base</span><span class="sxs-lookup"><span data-stu-id="34e77-167">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  * <span data-ttu-id="34e77-168">Ces options sont stockées dans une propriété et utilisées tout au long des tests pour créer des instances de DbContext</span><span class="sxs-lookup"><span data-stu-id="34e77-168">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
* <span data-ttu-id="34e77-169">Une méthode Seed est appelée pour créer et amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="34e77-169">A Seed method is called to create and seed the database</span></span>
  * <span data-ttu-id="34e77-170">La méthode Seed garantit le nettoyage de la base de données en la supprimant puis en la recréant.</span><span class="sxs-lookup"><span data-stu-id="34e77-170">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  * <span data-ttu-id="34e77-171">Certaines entités de test connues sont créées et enregistrées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="34e77-171">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="34e77-172">Chaque classe de test concrète hérite ensuite de cette.</span><span class="sxs-lookup"><span data-stu-id="34e77-172">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="34e77-173">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="34e77-173">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="34e77-174">Structure de test</span><span class="sxs-lookup"><span data-stu-id="34e77-174">Test structure</span></span>

<span data-ttu-id="34e77-175">Bien que l’application utilise l’injection de dépendances, les tests ne le font pas.</span><span class="sxs-lookup"><span data-stu-id="34e77-175">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="34e77-176">Il serait intéressant d’utiliser l’injection de dépendances ici, mais le code supplémentaire dont elle a besoin n’a que peu de valeur.</span><span class="sxs-lookup"><span data-stu-id="34e77-176">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="34e77-177">Au lieu de cela, une DbContext est créée à l’aide de `new` , puis directement transmise en tant que dépendance au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="34e77-177">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="34e77-178">Chaque test exécute ensuite la méthode testée sur le contrôleur et Assert les résultats comme prévu.</span><span class="sxs-lookup"><span data-stu-id="34e77-178">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="34e77-179">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="34e77-179">For example:</span></span>

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="34e77-180">Notez que différentes instances de DbContext sont utilisées pour amorcer la base de données et exécuter les tests.</span><span class="sxs-lookup"><span data-stu-id="34e77-180">Notice that different DbContext instances are used to seed the database and run the tests.</span></span> <span data-ttu-id="34e77-181">Cela permet de s’assurer que le test n’utilise pas (ou ne passe pas) les entités suivies par le contexte lors de l’amorçage.</span><span class="sxs-lookup"><span data-stu-id="34e77-181">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="34e77-182">Il correspond également mieux à ce qui se passe dans les services et applications Web.</span><span class="sxs-lookup"><span data-stu-id="34e77-182">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="34e77-183">Les tests qui transforment la base de données créent une deuxième instance DbContext dans le test pour des raisons similaires.</span><span class="sxs-lookup"><span data-stu-id="34e77-183">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="34e77-184">Autrement dit, créer un nouveau contexte propre, puis le lire dans la base de données pour vous assurer que les modifications ont été enregistrées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="34e77-184">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span> <span data-ttu-id="34e77-185">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="34e77-185">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="34e77-186">Deux tests légèrement plus impliqués couvrent la logique métier autour de l’ajout de balises.</span><span class="sxs-lookup"><span data-stu-id="34e77-186">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="34e77-187">Problèmes liés à l’utilisation de différents fournisseurs de bases de données</span><span class="sxs-lookup"><span data-stu-id="34e77-187">Issues using different database providers</span></span>

<span data-ttu-id="34e77-188">Le test avec un système de base de données différent de celui utilisé dans l’application de production peut entraîner des problèmes.</span><span class="sxs-lookup"><span data-stu-id="34e77-188">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="34e77-189">Celles-ci sont couvertes au niveau conceptuel du [test de code qui utilise EF Core](xref:core/miscellaneous/testing/index).</span><span class="sxs-lookup"><span data-stu-id="34e77-189">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  
<span data-ttu-id="34e77-190">Les sections ci-dessous couvrent deux exemples de tels problèmes démontrés par les tests de cet exemple.</span><span class="sxs-lookup"><span data-stu-id="34e77-190">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="34e77-191">Le test réussit lorsque l’application est interrompue</span><span class="sxs-lookup"><span data-stu-id="34e77-191">Test passes when the application is broken</span></span>

<span data-ttu-id="34e77-192">L’une des exigences de notre application est que « Items a un nom qui respecte la casse et une collection de Tags ».</span><span class="sxs-lookup"><span data-stu-id="34e77-192">One of the requirements for our application is that "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="34e77-193">Cela est assez simple à tester :</span><span class="sxs-lookup"><span data-stu-id="34e77-193">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="34e77-194">L’exécution de ce test sur la base de données EF en mémoire indique que tout fonctionne bien.</span><span class="sxs-lookup"><span data-stu-id="34e77-194">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="34e77-195">Tout est toujours parfait quand vous utilisez SQLite.</span><span class="sxs-lookup"><span data-stu-id="34e77-195">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="34e77-196">Mais le test échoue lorsqu’il est exécuté sur SQL Server !</span><span class="sxs-lookup"><span data-stu-id="34e77-196">But the test fails when run against SQL Server!</span></span>

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="34e77-197">Cela est dû au fait que la base de données EF en mémoire et la base de données SQLite sont sensibles à la casse par défaut.</span><span class="sxs-lookup"><span data-stu-id="34e77-197">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="34e77-198">SQL Server, en revanche, ne respecte pas la casse !</span><span class="sxs-lookup"><span data-stu-id="34e77-198">SQL Server, on the other hand, is case-insensitive!</span></span> 

<span data-ttu-id="34e77-199">EF Core, par conception, ne modifie pas ces comportements, car le fait de forcer une modification du respect de la casse peut avoir un impact considérable sur les performances.</span><span class="sxs-lookup"><span data-stu-id="34e77-199">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="34e77-200">Une fois que nous savons qu’il s’agit d’un problème, nous pouvons corriger l’application et compenser les tests.</span><span class="sxs-lookup"><span data-stu-id="34e77-200">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="34e77-201">Toutefois, le point ici est que ce bogue peut être manqué en cas de test uniquement avec la base de données en mémoire EF ou les fournisseurs SQLite.</span><span class="sxs-lookup"><span data-stu-id="34e77-201">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="34e77-202">Le test échoue lorsque l’application est correcte</span><span class="sxs-lookup"><span data-stu-id="34e77-202">Test fails when the application is correct</span></span> 

<span data-ttu-id="34e77-203">Une autre des exigences de notre application est que « la suppression d’un Item doit supprimer tous les éléments associés Tags ».</span><span class="sxs-lookup"><span data-stu-id="34e77-203">Another of the requirements for our application is that "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="34e77-204">Là encore, facile à tester :</span><span class="sxs-lookup"><span data-stu-id="34e77-204">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="34e77-205">Ce test réussit sur SQL Server et SQLite, mais il échoue avec la base de données EF en mémoire !</span><span class="sxs-lookup"><span data-stu-id="34e77-205">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="34e77-206">Dans ce cas, l’application fonctionne correctement, car SQL Server prend en charge les [suppressions en cascade](xref:core/saving/cascade-delete).</span><span class="sxs-lookup"><span data-stu-id="34e77-206">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span> <span data-ttu-id="34e77-207">SQLite prend également en charge les suppressions en cascade, comme pour la plupart des bases de données relationnelles, ce qui permet de tester ce fonctionnement sur SQLite.</span><span class="sxs-lookup"><span data-stu-id="34e77-207">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="34e77-208">En revanche, la base de données EF en mémoire [ne prend pas en charge les suppressions en cascade](https://github.com/dotnet/efcore/issues/3924).</span><span class="sxs-lookup"><span data-stu-id="34e77-208">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="34e77-209">Cela signifie que cette partie de l’application ne peut pas être testée avec le fournisseur de base de données EF en mémoire.</span><span class="sxs-lookup"><span data-stu-id="34e77-209">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
