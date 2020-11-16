---
title: Exemple de test de EF Core-EF Core
description: Exemple illustrant comment tester des applications qui utilisent Entity Framework Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/testing-sample
no-loc:
- 'Item'
- 'Tag'
- 'Items'
- 'Tags'
- 'items'
- 'tags'
ms.openlocfilehash: 9666bbde8ae9608dcebbea3ad37c51883960a942
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431606"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="3763c-103">Exemple de test EF Core</span><span class="sxs-lookup"><span data-stu-id="3763c-103">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="3763c-104">Vous trouverez le code de ce document sur GitHub en tant qu' [exemple exécutable](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span><span class="sxs-lookup"><span data-stu-id="3763c-104">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="3763c-105">Notez que certains de ces tests **sont censés échouer**.</span><span class="sxs-lookup"><span data-stu-id="3763c-105">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="3763c-106">Les raisons sont expliquées ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3763c-106">The reasons for this are explained below.</span></span>

<span data-ttu-id="3763c-107">Ce document décrit un exemple de test de code qui utilise EF Core.</span><span class="sxs-lookup"><span data-stu-id="3763c-107">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="3763c-108">L'application</span><span class="sxs-lookup"><span data-stu-id="3763c-108">The application</span></span>

<span data-ttu-id="3763c-109">L' [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contient deux projets :</span><span class="sxs-lookup"><span data-stu-id="3763c-109">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>

- <span data-ttu-id="3763c-110">ItemsWebApi : [API Web très simple sauvegardée par ASP.net Core](/aspnet/core/tutorials/first-web-api) avec un seul contrôleur</span><span class="sxs-lookup"><span data-stu-id="3763c-110">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api) with a single controller</span></span>
- <span data-ttu-id="3763c-111">Tests : projet de test [xUnit](https://xunit.net/) pour tester le contrôleur</span><span class="sxs-lookup"><span data-stu-id="3763c-111">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="3763c-112">Le modèle et les règles d’entreprise</span><span class="sxs-lookup"><span data-stu-id="3763c-112">The model and business rules</span></span>

<span data-ttu-id="3763c-113">Le modèle qui stocke cette API a deux types d’entité : Items et Tags .</span><span class="sxs-lookup"><span data-stu-id="3763c-113">The model backing this API has two entity types: Items and Tags.</span></span>

- <span data-ttu-id="3763c-114">Items avoir un nom qui respecte la casse et une collection de Tags .</span><span class="sxs-lookup"><span data-stu-id="3763c-114">Items have a case-sensitive name and a collection of Tags.</span></span>
- <span data-ttu-id="3763c-115">Chacun Tag a une étiquette et un nombre qui représente le nombre de fois où il a été appliqué à l' Item .</span><span class="sxs-lookup"><span data-stu-id="3763c-115">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
- <span data-ttu-id="3763c-116">Chaque Item doit être Tag associé à une étiquette donnée.</span><span class="sxs-lookup"><span data-stu-id="3763c-116">Each Item should only have one Tag with a given label.</span></span>
  - <span data-ttu-id="3763c-117">Si un élément est balisé plusieurs fois avec la même étiquette, le décompte de la balise existante avec cette étiquette est incrémenté au lieu d’une nouvelle balise créée.</span><span class="sxs-lookup"><span data-stu-id="3763c-117">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span>
- <span data-ttu-id="3763c-118">La suppression d’un Item doit supprimer tous les associés Tags .</span><span class="sxs-lookup"><span data-stu-id="3763c-118">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-no-locitem-entity-type"></a><span data-ttu-id="3763c-119">Le Item type d’entité</span><span class="sxs-lookup"><span data-stu-id="3763c-119">The Item entity type</span></span>

<span data-ttu-id="3763c-120">Le `Item` type d’entité :</span><span class="sxs-lookup"><span data-stu-id="3763c-120">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="3763c-121">Et sa configuration dans `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="3763c-121">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="3763c-122">Notez que le type d’entité limite la manière dont il peut être utilisé pour refléter le modèle de domaine et les règles d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="3763c-122">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="3763c-123">En particulier :</span><span class="sxs-lookup"><span data-stu-id="3763c-123">In particular:</span></span>

- <span data-ttu-id="3763c-124">La clé primaire est mappée directement au `_id` champ et n’est pas exposée publiquement</span><span class="sxs-lookup"><span data-stu-id="3763c-124">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="3763c-125">EF détecte et utilise le constructeur privé qui accepte la valeur et le nom de la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="3763c-125">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="3763c-126">La `Name` propriété est en lecture seule et définie uniquement dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="3763c-126">The `Name` property is read-only and set only in the constructor.</span></span>
- <span data-ttu-id="3763c-127">Tags sont exposés en tant que `IReadOnlyList<Tag>` pour empêcher une modification arbitraire.</span><span class="sxs-lookup"><span data-stu-id="3763c-127">Tags are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="3763c-128">EF associe la `Tags` propriété au `_tags` champ de stockage en faisant correspondre leurs noms.</span><span class="sxs-lookup"><span data-stu-id="3763c-128">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span>
  - <span data-ttu-id="3763c-129">La `AddTag` méthode prend une étiquette de balise et implémente la règle d’entreprise décrite ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="3763c-129">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="3763c-130">Autrement dit, une balise est ajoutée uniquement pour les nouvelles étiquettes.</span><span class="sxs-lookup"><span data-stu-id="3763c-130">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="3763c-131">Sinon, le nombre sur une étiquette existante est incrémenté.</span><span class="sxs-lookup"><span data-stu-id="3763c-131">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="3763c-132">La `Tags` propriété de navigation est configurée pour une relation plusieurs-à-un</span><span class="sxs-lookup"><span data-stu-id="3763c-132">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="3763c-133">Il n’est pas nécessaire d’utiliser une propriété de navigation de Tag à Item , donc elle n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="3763c-133">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="3763c-134">En outre, Tag ne définit pas une propriété de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="3763c-134">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="3763c-135">Dans ce cas, EF crée et gère une propriété en état Shadow.</span><span class="sxs-lookup"><span data-stu-id="3763c-135">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-no-loctag-entity-type"></a><span data-ttu-id="3763c-136">Le Tag type d’entité</span><span class="sxs-lookup"><span data-stu-id="3763c-136">The Tag entity type</span></span>

<span data-ttu-id="3763c-137">Le `Tag` type d’entité :</span><span class="sxs-lookup"><span data-stu-id="3763c-137">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="3763c-138">Et sa configuration dans `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="3763c-138">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="3763c-139">De même Item que, Tag masque sa clé primaire et définit la `Label` propriété en lecture seule.</span><span class="sxs-lookup"><span data-stu-id="3763c-139">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-no-locitemscontroller"></a><span data-ttu-id="3763c-140">Le Items contrôleur</span><span class="sxs-lookup"><span data-stu-id="3763c-140">The ItemsController</span></span>

<span data-ttu-id="3763c-141">Le contrôleur d’API Web est plutôt basique.</span><span class="sxs-lookup"><span data-stu-id="3763c-141">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="3763c-142">Elle obtient un `DbContext` à partir du conteneur d’injection de dépendances via l’injection de constructeur :</span><span class="sxs-lookup"><span data-stu-id="3763c-142">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="3763c-143">Il possède des méthodes pour obtenir tout Items ou un Item avec un nom donné :</span><span class="sxs-lookup"><span data-stu-id="3763c-143">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="3763c-144">Il a une méthode pour ajouter un nouveau Item :</span><span class="sxs-lookup"><span data-stu-id="3763c-144">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="3763c-145">Méthode pour baliser un Item avec une étiquette :</span><span class="sxs-lookup"><span data-stu-id="3763c-145">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="3763c-146">Et une méthode pour supprimer un Item et tous les associés Tags :</span><span class="sxs-lookup"><span data-stu-id="3763c-146">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="3763c-147">La plupart des validations et des erreurs ont été supprimées pour réduire l’encombrement.</span><span class="sxs-lookup"><span data-stu-id="3763c-147">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="3763c-148">Les tests</span><span class="sxs-lookup"><span data-stu-id="3763c-148">The Tests</span></span>

<span data-ttu-id="3763c-149">Les tests sont organisés pour s’exécuter avec plusieurs configurations de fournisseur de base de données :</span><span class="sxs-lookup"><span data-stu-id="3763c-149">The tests are organized to run with multiple database provider configurations:</span></span>

- <span data-ttu-id="3763c-150">Fournisseur SQL Server, qui est le fournisseur utilisé par l’application</span><span class="sxs-lookup"><span data-stu-id="3763c-150">The SQL Server provider, which is the provider used by the application</span></span>
- <span data-ttu-id="3763c-151">Fournisseur SQLite</span><span class="sxs-lookup"><span data-stu-id="3763c-151">The SQLite provider</span></span>
- <span data-ttu-id="3763c-152">Fournisseur SQLite utilisant des bases de données SQLite en mémoire</span><span class="sxs-lookup"><span data-stu-id="3763c-152">The SQLite provider using in-memory SQLite databases</span></span>
- <span data-ttu-id="3763c-153">Fournisseur de base de données EF en mémoire</span><span class="sxs-lookup"><span data-stu-id="3763c-153">The EF in-memory database provider</span></span>

<span data-ttu-id="3763c-154">Pour ce faire, vous devez placer tous les tests dans une classe de base, puis hériter de ce pour effectuer un test avec chaque fournisseur.</span><span class="sxs-lookup"><span data-stu-id="3763c-154">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="3763c-155">Si vous n’utilisez pas la base de données locale, vous devez modifier la chaîne de connexion SQL Server.</span><span class="sxs-lookup"><span data-stu-id="3763c-155">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>
> <span data-ttu-id="3763c-156">Pour obtenir des conseils sur l’utilisation de SQLite pour le test en mémoire, consultez [test avec SQLite](xref:core/testing/sqlite) .</span><span class="sxs-lookup"><span data-stu-id="3763c-156">See [Testing with SQLite](xref:core/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span>

<span data-ttu-id="3763c-157">Les deux tests suivants sont censés échouer :</span><span class="sxs-lookup"><span data-stu-id="3763c-157">The following two tests are expected to fail:</span></span>

- <span data-ttu-id="3763c-158">`Can_remove_item_and_all_associated_tags` en cas d’exécution avec le fournisseur de base de données EF en mémoire</span><span class="sxs-lookup"><span data-stu-id="3763c-158">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
- <span data-ttu-id="3763c-159">`Can_add_item_differing_only_by_case` en cas d’exécution avec le fournisseur SQL Server</span><span class="sxs-lookup"><span data-stu-id="3763c-159">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="3763c-160">Ce sujet est abordé plus en détail ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3763c-160">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="3763c-161">Configuration et amorçage de la base de données</span><span class="sxs-lookup"><span data-stu-id="3763c-161">Setting up and seeding the database</span></span>

<span data-ttu-id="3763c-162">XUnit, comme la plupart des frameworks de test, créera une nouvelle instance de classe de test pour chaque série de tests.</span><span class="sxs-lookup"><span data-stu-id="3763c-162">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="3763c-163">En outre, XUnit n’exécutera pas de tests dans une classe de test donnée en parallèle.</span><span class="sxs-lookup"><span data-stu-id="3763c-163">Also, XUnit will not run tests within a given test class in parallel.</span></span>
<span data-ttu-id="3763c-164">Cela signifie que nous pouvons configurer et configurer la base de données dans le constructeur de test et qu’elle sera dans un état connu pour chaque test.</span><span class="sxs-lookup"><span data-stu-id="3763c-164">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="3763c-165">Cet exemple recrée la base de données pour chaque test.</span><span class="sxs-lookup"><span data-stu-id="3763c-165">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="3763c-166">Cela fonctionne bien pour les tests de base de données SQLite et EF en mémoire, mais peut impliquer une surcharge importante avec d’autres systèmes de base de données, y compris SQL Server.</span><span class="sxs-lookup"><span data-stu-id="3763c-166">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="3763c-167">Les approches pour réduire cette surcharge sont traitées dans [partage des bases de données entre les tests](xref:core/testing/sharing-databases).</span><span class="sxs-lookup"><span data-stu-id="3763c-167">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/testing/sharing-databases).</span></span>

<span data-ttu-id="3763c-168">Quand chaque test est exécuté :</span><span class="sxs-lookup"><span data-stu-id="3763c-168">When each test is run:</span></span>

- <span data-ttu-id="3763c-169">Les DbContextOptions sont configurés pour le fournisseur en cours d’utilisation et passés au constructeur de classe de base</span><span class="sxs-lookup"><span data-stu-id="3763c-169">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  - <span data-ttu-id="3763c-170">Ces options sont stockées dans une propriété et utilisées tout au long des tests pour créer des instances de DbContext</span><span class="sxs-lookup"><span data-stu-id="3763c-170">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
- <span data-ttu-id="3763c-171">Une méthode Seed est appelée pour créer et amorcer la base de données</span><span class="sxs-lookup"><span data-stu-id="3763c-171">A Seed method is called to create and seed the database</span></span>
  - <span data-ttu-id="3763c-172">La méthode Seed garantit le nettoyage de la base de données en la supprimant puis en la recréant.</span><span class="sxs-lookup"><span data-stu-id="3763c-172">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  - <span data-ttu-id="3763c-173">Certaines entités de test connues sont créées et enregistrées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="3763c-173">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="3763c-174">Chaque classe de test concrète hérite ensuite de cette.</span><span class="sxs-lookup"><span data-stu-id="3763c-174">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="3763c-175">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="3763c-175">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="3763c-176">Structure de test</span><span class="sxs-lookup"><span data-stu-id="3763c-176">Test structure</span></span>

<span data-ttu-id="3763c-177">Bien que l’application utilise l’injection de dépendances, les tests ne le font pas.</span><span class="sxs-lookup"><span data-stu-id="3763c-177">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="3763c-178">Il serait intéressant d’utiliser l’injection de dépendances ici, mais le code supplémentaire dont elle a besoin n’a que peu de valeur.</span><span class="sxs-lookup"><span data-stu-id="3763c-178">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="3763c-179">Au lieu de cela, une DbContext est créée à l’aide de `new` , puis directement transmise en tant que dépendance au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="3763c-179">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="3763c-180">Chaque test exécute ensuite la méthode testée sur le contrôleur et Assert les résultats comme prévu.</span><span class="sxs-lookup"><span data-stu-id="3763c-180">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="3763c-181">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="3763c-181">For example:</span></span>

[!code-csharp[CanGetItems](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="3763c-182">Notez que différentes instances de DbContext sont utilisées pour amorcer la base de données et exécuter les tests.</span><span class="sxs-lookup"><span data-stu-id="3763c-182">Notice that different DbContext instances are used to seed the database and run the tests.</span></span>
<span data-ttu-id="3763c-183">Cela permet de s’assurer que le test n’utilise pas (ou ne passe pas) les entités suivies par le contexte lors de l’amorçage.</span><span class="sxs-lookup"><span data-stu-id="3763c-183">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="3763c-184">Il correspond également mieux à ce qui se passe dans les services et applications Web.</span><span class="sxs-lookup"><span data-stu-id="3763c-184">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="3763c-185">Les tests qui transforment la base de données créent une deuxième instance DbContext dans le test pour des raisons similaires.</span><span class="sxs-lookup"><span data-stu-id="3763c-185">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="3763c-186">Autrement dit, créer un nouveau contexte propre, puis le lire dans la base de données pour vous assurer que les modifications ont été enregistrées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="3763c-186">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span>
<span data-ttu-id="3763c-187">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="3763c-187">For example:</span></span>

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="3763c-188">Deux tests, un peu plus complexe, couvrent la logique métier autour de l’ajout de tags .</span><span class="sxs-lookup"><span data-stu-id="3763c-188">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="3763c-189">Problèmes liés à l’utilisation de différents fournisseurs de bases de données</span><span class="sxs-lookup"><span data-stu-id="3763c-189">Issues using different database providers</span></span>

<span data-ttu-id="3763c-190">Le test avec un système de base de données différent de celui utilisé dans l’application de production peut entraîner des problèmes.</span><span class="sxs-lookup"><span data-stu-id="3763c-190">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="3763c-191">Celles-ci sont couvertes au niveau conceptuel du [test de code qui utilise EF Core](xref:core/testing/index).</span><span class="sxs-lookup"><span data-stu-id="3763c-191">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/testing/index).</span></span>
<span data-ttu-id="3763c-192">Les sections ci-dessous couvrent deux exemples de tels problèmes démontrés par les tests de cet exemple.</span><span class="sxs-lookup"><span data-stu-id="3763c-192">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="3763c-193">Le test réussit lorsque l’application est interrompue</span><span class="sxs-lookup"><span data-stu-id="3763c-193">Test passes when the application is broken</span></span>

<span data-ttu-id="3763c-194">L’une des exigences de notre application est que « Items a un nom qui respecte la casse et une collection de Tags ».</span><span class="sxs-lookup"><span data-stu-id="3763c-194">One of the requirements for our application is that "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="3763c-195">Cela est assez simple à tester :</span><span class="sxs-lookup"><span data-stu-id="3763c-195">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="3763c-196">L’exécution de ce test sur la base de données EF en mémoire indique que tout fonctionne bien.</span><span class="sxs-lookup"><span data-stu-id="3763c-196">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="3763c-197">Tout est toujours parfait quand vous utilisez SQLite.</span><span class="sxs-lookup"><span data-stu-id="3763c-197">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="3763c-198">Mais le test échoue lorsqu’il est exécuté sur SQL Server !</span><span class="sxs-lookup"><span data-stu-id="3763c-198">But the test fails when run against SQL Server!</span></span>

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="3763c-199">Cela est dû au fait que la base de données EF en mémoire et la base de données SQLite sont sensibles à la casse par défaut.</span><span class="sxs-lookup"><span data-stu-id="3763c-199">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="3763c-200">SQL Server, en revanche, ne respecte pas la casse !</span><span class="sxs-lookup"><span data-stu-id="3763c-200">SQL Server, on the other hand, is case-insensitive!</span></span>

<span data-ttu-id="3763c-201">EF Core, par conception, ne modifie pas ces comportements, car le fait de forcer une modification du respect de la casse peut avoir un impact considérable sur les performances.</span><span class="sxs-lookup"><span data-stu-id="3763c-201">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="3763c-202">Une fois que nous savons qu’il s’agit d’un problème, nous pouvons corriger l’application et compenser les tests.</span><span class="sxs-lookup"><span data-stu-id="3763c-202">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="3763c-203">Toutefois, le point ici est que ce bogue peut être manqué en cas de test uniquement avec la base de données en mémoire EF ou les fournisseurs SQLite.</span><span class="sxs-lookup"><span data-stu-id="3763c-203">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="3763c-204">Le test échoue lorsque l’application est correcte</span><span class="sxs-lookup"><span data-stu-id="3763c-204">Test fails when the application is correct</span></span>

<span data-ttu-id="3763c-205">Une autre des exigences de notre application est que « la suppression d’un Item doit supprimer tous les éléments associés Tags ».</span><span class="sxs-lookup"><span data-stu-id="3763c-205">Another of the requirements for our application is that "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="3763c-206">Là encore, facile à tester :</span><span class="sxs-lookup"><span data-stu-id="3763c-206">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="3763c-207">Ce test réussit sur SQL Server et SQLite, mais il échoue avec la base de données EF en mémoire !</span><span class="sxs-lookup"><span data-stu-id="3763c-207">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="3763c-208">Dans ce cas, l’application fonctionne correctement, car SQL Server prend en charge les [suppressions en cascade](xref:core/saving/cascade-delete).</span><span class="sxs-lookup"><span data-stu-id="3763c-208">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span>
<span data-ttu-id="3763c-209">SQLite prend également en charge les suppressions en cascade, comme pour la plupart des bases de données relationnelles, ce qui permet de tester ce fonctionnement sur SQLite.</span><span class="sxs-lookup"><span data-stu-id="3763c-209">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="3763c-210">En revanche, la base de données EF en mémoire [ne prend pas en charge les suppressions en cascade](https://github.com/dotnet/efcore/issues/3924).</span><span class="sxs-lookup"><span data-stu-id="3763c-210">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="3763c-211">Cela signifie que cette partie de l’application ne peut pas être testée avec le fournisseur de base de données EF en mémoire.</span><span class="sxs-lookup"><span data-stu-id="3763c-211">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
