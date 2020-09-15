---
title: Testabilité et Entity Framework 4,0-EF6
description: Testabilité et Entity Framework 4,0
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 439e2af6b0c851a2ab70d6d9ed1004deb83b1a0e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070508"
---
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="1f5a9-103">Testabilité et Entity Framework 4,0</span><span class="sxs-lookup"><span data-stu-id="1f5a9-103">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="1f5a9-104">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="1f5a9-104">Scott Allen</span></span>

<span data-ttu-id="1f5a9-105">Date de publication : mai 2010</span><span class="sxs-lookup"><span data-stu-id="1f5a9-105">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="1f5a9-106">Introduction</span><span class="sxs-lookup"><span data-stu-id="1f5a9-106">Introduction</span></span>

<span data-ttu-id="1f5a9-107">Ce livre blanc décrit et montre comment écrire du code testable avec ADO.NET Entity Framework 4,0 et Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-107">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="1f5a9-108">Ce document ne tente pas de se concentrer sur une méthodologie de test spécifique, telle que la conception pilotée par test (TDD) ou la conception pilotée par comportement (BDD).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-108">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="1f5a9-109">Au lieu de cela, cet article se concentrera sur la façon d’écrire du code qui utilise le ADO.NET Entity Framework tout en restant facile à isoler et à tester de manière automatisée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-109">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="1f5a9-110">Nous allons examiner les modèles de conception courants qui facilitent les tests dans les scénarios d’accès aux données et comment appliquer ces modèles quand vous utilisez l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-110">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="1f5a9-111">Nous examinerons également les fonctionnalités spécifiques de l’infrastructure pour voir comment ces fonctionnalités peuvent fonctionner dans du code testable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-111">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="1f5a9-112">Qu’est-ce que le code testable ?</span><span class="sxs-lookup"><span data-stu-id="1f5a9-112">What Is Testable Code?</span></span>

<span data-ttu-id="1f5a9-113">La possibilité de vérifier un logiciel à l’aide de tests unitaires automatisés offre de nombreux avantages souhaitables.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-113">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="1f5a9-114">Tout le monde sait que les bons tests vont réduire le nombre de défauts logiciels dans une application et augmenter la qualité de l’application, mais que les tests unitaires sont en place bien plus que de trouver des bogues.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-114">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="1f5a9-115">Une bonne suite de tests unitaires permet à une équipe de développement de gagner du temps et de garder le contrôle du logiciel qu’elle crée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-115">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="1f5a9-116">Une équipe peut apporter des modifications au code existant, Refactoriser, reconcevoir et restructurer des logiciels pour répondre à de nouvelles exigences, et ajouter de nouveaux composants dans une application tout en sachant que la suite de tests peut vérifier le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-116">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="1f5a9-117">Les tests unitaires font partie d’un cycle de commentaires rapide pour faciliter les modifications et préserver la maintenabilité des logiciels à mesure que la complexité augmente.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-117">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="1f5a9-118">Toutefois, les tests unitaires sont fournis avec un prix.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-118">Unit testing comes with a price, however.</span></span> <span data-ttu-id="1f5a9-119">Une équipe doit consacrer le temps nécessaire à la création et à la maintenance des tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-119">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="1f5a9-120">L’effort requis pour créer ces tests est directement lié à la **testabilité** du logiciel sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-120">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="1f5a9-121">Le logiciel est-il facile à tester ?</span><span class="sxs-lookup"><span data-stu-id="1f5a9-121">How easy is the software to test?</span></span> <span data-ttu-id="1f5a9-122">Une équipe qui développe des logiciels avec la testabilité à l’esprit créera des tests efficaces plus rapidement que l’équipe travaillant avec des logiciels non testables.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-122">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="1f5a9-123">Microsoft a conçu le ADO.NET Entity Framework 4,0 (EF4) avec la testabilité à l’esprit.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-123">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="1f5a9-124">Cela ne signifie pas que les développeurs écrivent des tests unitaires sur le code du Framework lui-même.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-124">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="1f5a9-125">Au lieu de cela, les objectifs de testabilité pour EF4 facilitent la création de code testable qui s’appuie sur l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-125">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="1f5a9-126">Avant d’examiner des exemples spécifiques, il est utile de comprendre les qualités du code testable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-126">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="1f5a9-127">Les qualités du code testable</span><span class="sxs-lookup"><span data-stu-id="1f5a9-127">The Qualities of Testable Code</span></span>

<span data-ttu-id="1f5a9-128">Le code facile à tester présentera toujours au moins deux traits.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-128">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="1f5a9-129">Tout d’abord, le code testable est facile à **observer**.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-129">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="1f5a9-130">Étant donné un ensemble d’entrées, il doit être facile d’observer la sortie du code.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-130">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="1f5a9-131">Par exemple, le test de la méthode suivante est simple, car la méthode retourne directement le résultat d’un calcul.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-131">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="1f5a9-132">Le test d’une méthode est difficile si la méthode écrit la valeur calculée dans un socket réseau, une table de base de données ou un fichier comme le code suivant.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-132">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="1f5a9-133">Le test doit effectuer un travail supplémentaire pour récupérer la valeur.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-133">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="1f5a9-134">Deuxièmement, il est facile d' **isoler**du code testable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-134">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="1f5a9-135">Utilisons le pseudo-code suivant comme mauvais exemple de code testable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-135">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

``` csharp
    public int ComputePolicyValue(InsurancePolicy policy) {
        using (var connection = new SqlConnection("dbConnection"))
        using (var command = new SqlCommand(query, connection)) {

            // business calculations omitted ...               

            if (totalValue > notificationThreshold) {
                var message = new MailMessage();
                message.Subject = "Warning!";
                var client = new SmtpClient();
                client.Send(message);
            }
        }
        return totalValue;
    }
```

<span data-ttu-id="1f5a9-136">La méthode est facile à observer : nous pouvons transmettre une stratégie d’assurance et vérifier que la valeur de retour correspond à un résultat attendu.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-136">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="1f5a9-137">Toutefois, pour tester la méthode, vous devez disposer d’une base de données installée avec le schéma correct, et configurer le serveur SMTP en cas de tentative d’envoi d’un e-mail par la méthode.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-137">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="1f5a9-138">Le test unitaire souhaite uniquement vérifier la logique de calcul à l’intérieur de la méthode, mais le test peut échouer parce que le serveur de messagerie est hors connexion ou que le serveur de base de données a été déplacé.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-138">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="1f5a9-139">Ces deux échecs ne sont pas liés au comportement que le test veut vérifier.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-139">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="1f5a9-140">Le comportement est difficile à isoler.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-140">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="1f5a9-141">Les développeurs de logiciels qui cherchent à écrire du code testable s’efforcent souvent de conserver une séparation des préoccupations dans le code qu’ils écrivent.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-141">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="1f5a9-142">La méthode ci-dessus doit se concentrer sur les calculs d’entreprise et déléguer les détails d’implémentation de la base de données et du courrier électronique à d’autres composants.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-142">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="1f5a9-143">Robert C. Martin appelle ce principe de responsabilité unique.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-143">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="1f5a9-144">Un objet doit encapsuler une seule responsabilité étroite, comme le calcul de la valeur d’une stratégie.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-144">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="1f5a9-145">Toutes les autres opérations de base de données et de notification doivent être de la responsabilité d’un autre objet.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-145">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="1f5a9-146">Le code écrit de cette manière est plus facile à isoler, car il est axé sur une seule tâche.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-146">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="1f5a9-147">Dans .NET, nous disposons des abstractions dont nous avons besoin pour suivre le principe de responsabilité unique et parvenir à l’isolation.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-147">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="1f5a9-148">Nous pouvons utiliser des définitions d’interface et forcer le code à utiliser l’abstraction d’interface au lieu d’un type concret.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-148">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="1f5a9-149">Plus loin dans ce document, nous verrons comment une méthode telle que l’exemple mal présenté ci-dessus peut fonctionner avec des interfaces qui *semblent* communiquer avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-149">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="1f5a9-150">Toutefois, au moment du test, nous pouvons remplacer une implémentation factice qui ne communique pas avec la base de données, mais qui contient à la place des données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-150">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="1f5a9-151">Cette implémentation factice isole le code des problèmes non liés dans le code d’accès aux données ou la configuration de la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-151">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="1f5a9-152">L’isolation présente des avantages supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-152">There are additional benefits to isolation.</span></span> <span data-ttu-id="1f5a9-153">L’exécution du calcul de l’activité dans la dernière méthode ne doit prendre que quelques millisecondes, mais le test lui-même peut s’exécuter pendant plusieurs secondes à mesure que le code saute sur le réseau et communique avec différents serveurs.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-153">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="1f5a9-154">Les tests unitaires doivent s’exécuter rapidement pour faciliter les petites modifications.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-154">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="1f5a9-155">Les tests unitaires doivent également être reproductibles et ne pas échouer car un composant qui n’est pas lié au test a un problème.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-155">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="1f5a9-156">L’écriture de code facile à observer et à isoler signifie que les développeurs auront un temps plus facile pour écrire des tests pour le code, passer moins de temps à attendre l’exécution des tests et, plus important encore, passer moins de temps à suivre les bogues qui n’existent pas.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-156">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="1f5a9-157">Nous espérons que vous pouvez apprécier les avantages des tests et comprendre les qualités qu’il présente.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-157">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="1f5a9-158">Nous sommes sur le point de traiter l’écriture de code qui fonctionne avec EF4 pour enregistrer des données dans une base de données tout en restant observables et faciles à isoler, mais tout d’abord, nous allons affiner l’étude des conceptions pouvant être testées pour l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-158">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="1f5a9-159">Modèles de conception pour la persistance des données</span><span class="sxs-lookup"><span data-stu-id="1f5a9-159">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="1f5a9-160">Les deux exemples incorrects présentés précédemment avaient trop de responsabilités.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-160">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="1f5a9-161">Le premier exemple inapproprié devait effectuer un calcul *et* écrire dans un fichier.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-161">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="1f5a9-162">Le deuxième exemple incorrect devait lire les données d’une base de données *et* effectuer un calcul d’entreprise *et* envoyer des e-mails.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-162">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="1f5a9-163">En concevant des méthodes plus petites qui distinguent les préoccupations et délèguent la responsabilité à d’autres composants, vous ferez de superbes progrès pour écrire du code testable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-163">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="1f5a9-164">L’objectif est de créer des fonctionnalités en composant des actions à partir d’abstractions petites et concentrées.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-164">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="1f5a9-165">Lorsqu’il s’agit de la persistance des données, les petites abstractions concentrées que nous recherchons sont tellement courantes qu’elles ont été documentées en tant que modèles de conception.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-165">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="1f5a9-166">Les modèles de l’architecture d’applications d’entreprise de Martin Fowler étaient le premier travail à décrire ces modèles à l’impression.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-166">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="1f5a9-167">Nous fournissons une brève description de ces modèles dans les sections suivantes avant de montrer comment ces ADO.NET Entity Framework implémentent et fonctionnent avec ces modèles.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-167">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="1f5a9-168">The Repository Pattern</span><span class="sxs-lookup"><span data-stu-id="1f5a9-168">The Repository Pattern</span></span>

<span data-ttu-id="1f5a9-169">Fowler indique un référentiel qui suit les couches de mappage de données et de domaine à l’aide d’une interface de type collection pour accéder aux objets de domaine.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-169">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="1f5a9-170">L’objectif du modèle de référentiel est d’isoler le code du détails d’accès aux données, et comme nous l’avons vu, l’isolation précédente est un critère requis pour la testabilité.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-170">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="1f5a9-171">La clé de l’isolation est la façon dont le référentiel expose les objets à l’aide d’une interface de type collection.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-171">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="1f5a9-172">La logique que vous écrivez pour utiliser le référentiel n’a aucune idée de la façon dont le référentiel matérialisera les objets que vous demandez.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-172">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="1f5a9-173">Le référentiel peut communiquer avec une base de données, ou il peut simplement retourner des objets à partir d’une collection en mémoire.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-173">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="1f5a9-174">Tout votre code doit savoir que le référentiel semble gérer la collection et que vous pouvez récupérer, ajouter et supprimer des objets de la collection.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-174">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="1f5a9-175">Dans les applications .NET existantes, un référentiel concret hérite souvent d’une interface générique comme suit :</span><span class="sxs-lookup"><span data-stu-id="1f5a9-175">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="1f5a9-176">Nous allons apporter quelques modifications à la définition de l’interface lorsque nous fournissons une implémentation pour EF4, mais le concept de base reste le même.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-176">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="1f5a9-177">Le code peut utiliser un référentiel concret qui implémente cette interface pour récupérer une entité par sa valeur de clé primaire, pour récupérer une collection d’entités en fonction de l’évaluation d’un prédicat, ou simplement récupérer toutes les entités disponibles.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-177">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="1f5a9-178">Le code peut également ajouter et supprimer des entités par le biais de l’interface de référentiel.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-178">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="1f5a9-179">Étant donné un IRepository d’objets Employee, le code peut effectuer les opérations suivantes.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-179">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="1f5a9-180">Étant donné que le code utilise une interface (IRepository of Employee), nous pouvons fournir le code avec différentes implémentations de l’interface.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-180">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="1f5a9-181">Une implémentation peut être une implémentation sauvegardée par EF4 et la persistance d’objets dans une base de données Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-181">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="1f5a9-182">Une implémentation différente (celle que nous utilisons pendant le test) peut être sauvegardée par une liste en mémoire d’objets Employee.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-182">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="1f5a9-183">L’interface permet d’obtenir un isolement dans le code.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-183">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="1f5a9-184">Notez que l' &lt; interface IRepository t &gt; n’expose pas d’opération d’enregistrement.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-184">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="1f5a9-185">Comment mettre à jour les objets existants ?</span><span class="sxs-lookup"><span data-stu-id="1f5a9-185">How do we update existing objects?</span></span> <span data-ttu-id="1f5a9-186">Vous pouvez vous trouver parmi les définitions de IRepository qui incluent l’opération d’enregistrement, et les implémentations de ces dépôts devront conserver immédiatement un objet dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-186">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="1f5a9-187">Toutefois, dans de nombreuses applications, nous ne souhaitons pas conserver les objets individuellement.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-187">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="1f5a9-188">Au lieu de cela, nous voulons donner vie aux objets, peut-être à partir de différents référentiels, modifier ces objets dans le cadre d’une activité d’entreprise, puis conserver tous les objets dans le cadre d’une opération atomique unique.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-188">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="1f5a9-189">Heureusement, il existe un modèle pour autoriser ce type de comportement.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-189">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="1f5a9-190">Modèle d’unité de travail</span><span class="sxs-lookup"><span data-stu-id="1f5a9-190">The Unit of Work Pattern</span></span>

<span data-ttu-id="1f5a9-191">Fowler dit qu’une unité de travail conservera une liste d’objets affectés par une transaction commerciale et coordonne l’écriture des modifications et la résolution des problèmes d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-191">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="1f5a9-192">Il incombe à l’unité de travail de suivre les modifications apportées aux objets que nous avons apportées à la vie à partir d’un référentiel et de conserver les modifications que nous avons apportées aux objets quand nous indiquons à l’unité de travail de valider les modifications.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-192">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="1f5a9-193">Il incombe également à l’unité de travail de prendre les nouveaux objets que nous avons ajoutés à tous les référentiels, d’insérer les objets dans une base de données et de gérer également la suppression.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-193">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="1f5a9-194">Si vous avez déjà effectué des tâches avec des jeux de données ADO.NET, vous êtes déjà familiarisé avec le modèle d’unité de travail.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-194">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="1f5a9-195">Les jeux de données ADO.NET pouvaient effectuer le suivi de nos mises à jour, suppressions et insertions d’objets DataRow et pouvaient (à l’aide d’un TableAdapter) réconcilier toutes les modifications apportées à une base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-195">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="1f5a9-196">Toutefois, les objets DataSet modélisent un sous-ensemble déconnecté de la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-196">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="1f5a9-197">Le modèle unité de travail présente le même comportement, mais fonctionne avec les objets métier et les objets de domaine qui sont isolés du code d’accès aux données et ne connaissent pas la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-197">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="1f5a9-198">Une abstraction pour modéliser l’unité de travail dans du code .NET peut se présenter comme suit :</span><span class="sxs-lookup"><span data-stu-id="1f5a9-198">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="1f5a9-199">En exposant les références de référentiel à partir de l’unité de travail, nous pouvons garantir qu’un seul objet d’unité de travail a la possibilité de suivre toutes les entités matérialisées lors d’une transaction commerciale.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-199">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="1f5a9-200">L’implémentation de la méthode Commit pour une unité de travail réelle est l’endroit où tout le Magic se produit pour rapprocher les modifications en mémoire avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-200">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="1f5a9-201">À partir d’une référence IUnitOfWork, le code peut apporter des modifications aux objets métier récupérés à partir d’un ou de plusieurs référentiels et enregistrer toutes les modifications à l’aide de l’opération de validation atomique.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-201">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="1f5a9-202">Le modèle de chargement différé</span><span class="sxs-lookup"><span data-stu-id="1f5a9-202">The Lazy Load Pattern</span></span>

<span data-ttu-id="1f5a9-203">Fowler utilise la charge différée de nom pour décrire « un objet qui ne contient pas toutes les données dont vous avez besoin, mais qui sait comment l’accéder ».</span><span class="sxs-lookup"><span data-stu-id="1f5a9-203">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="1f5a9-204">Le chargement différé transparent est une fonctionnalité importante pour l’écriture de code d’entreprise testable et l’utilisation d’une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-204">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="1f5a9-205">À titre d’exemple, considérez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-205">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="1f5a9-206">Comment la collection des entrées de procédure est-elle remplie ?</span><span class="sxs-lookup"><span data-stu-id="1f5a9-206">How is the TimeCards collection populated?</span></span> <span data-ttu-id="1f5a9-207">Il existe deux réponses possibles.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-207">There are two possible answers.</span></span> <span data-ttu-id="1f5a9-208">L’une des réponses est que le dépôt de l’employé, lorsqu’il est invité à extraire un employé, émet une requête pour récupérer à la fois l’employé et les informations de carte de l’employé associées.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-208">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="1f5a9-209">Dans les bases de données relationnelles, cela nécessite généralement une requête avec une clause JOIN et peut entraîner la récupération d’informations supplémentaires par rapport aux besoins d’une application.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-209">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="1f5a9-210">Que se passe-t-il si l’application n’a jamais besoin de toucher la propriété de la fonction de la</span><span class="sxs-lookup"><span data-stu-id="1f5a9-210">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="1f5a9-211">Une deuxième réponse consiste à charger la propriété de la fonction de la requête « à la demande ».</span><span class="sxs-lookup"><span data-stu-id="1f5a9-211">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="1f5a9-212">Ce chargement différé est implicite et transparent pour la logique métier, car le code n’appelle pas d’API spéciales pour récupérer les informations de carte de temps.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-212">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="1f5a9-213">Le code suppose que les informations de carte de temps sont présentes si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-213">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="1f5a9-214">Une magie est impliquée dans le chargement différé qui implique généralement l’interception de l’exécution d’appels de méthode.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-214">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="1f5a9-215">Le code d’interception est chargé de communiquer avec la base de données et de récupérer les informations de la carte de temps tout en laissant la logique métier libre pour être logique métier.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-215">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="1f5a9-216">Cette magie de chargement différé permet au code d’entreprise de s’isoler des opérations d’extraction de données et de se traduit par un code plus testable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-216">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="1f5a9-217">L’inconvénient d’une charge différée est que lorsqu’une application *a* besoin des informations de carte de temps, le code exécutera une requête supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-217">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="1f5a9-218">Ce n’est pas une préoccupation pour de nombreuses applications, mais pour les applications sensibles aux performances, en boucle sur un certain nombre d’objets Employee et en exécutant une requête pour récupérer des cartes de temps lors de chaque itération de la boucle (un problème souvent appelé problème de requête N + 1), le chargement différé est un glissement.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-218">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="1f5a9-219">Dans ces scénarios, une application peut souhaiter charger de manière dynamique les informations de carte de temps de la manière la plus efficace possible.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-219">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="1f5a9-220">Heureusement, nous allons voir comment EF4 prend en charge à la fois les charges tardives implicites et les charges hâtif efficaces à mesure que nous passons à la section suivante et implémentons ces modèles.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-220">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="1f5a9-221">Implémentation de modèles avec l’Entity Framework</span><span class="sxs-lookup"><span data-stu-id="1f5a9-221">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="1f5a9-222">La bonne nouvelle, c’est que tous les modèles de conception que nous avons décrits dans la dernière section sont faciles à implémenter avec EF4.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-222">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="1f5a9-223">Pour démontrer que nous allons utiliser une simple application MVC ASP.NET pour modifier et afficher les employés et leurs informations de carte de temps associées.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-223">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="1f5a9-224">Nous allons commencer par utiliser les objets « Plain Old CLR Objects » (POCO) suivants.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-224">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

``` csharp
    public class Employee {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime HireDate { get; set; }
        public ICollection<TimeCard> TimeCards { get; set; }
    }

    public class TimeCard {
        public int Id { get; set; }
        public int Hours { get; set; }
        public DateTime EffectiveDate { get; set; }
    }
```

<span data-ttu-id="1f5a9-225">Ces définitions de classe seront légèrement modifiées à mesure que nous explorerons les différentes approches et fonctionnalités de EF4, mais l’objectif est de conserver ces classes comme étant le plus possible (PI).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-225">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="1f5a9-226">Un objet PI ne sait pas *Comment*, ou même *si*, l’État qu’il contient réside dans une base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-226">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="1f5a9-227">PI et POCO vont de pair avec des logiciels testables.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-227">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="1f5a9-228">Les objets utilisant une approche POCO sont moins limités, plus flexibles et plus faciles à tester, car ils peuvent fonctionner sans une base de données présente.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-228">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="1f5a9-229">Une fois les POCO en place, nous pouvons créer un Entity Data Model (EDM) dans Visual Studio (voir la figure 1).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-229">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="1f5a9-230">Nous n’utiliserons pas le modèle EDM pour générer du code pour nos entités.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-230">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="1f5a9-231">Au lieu de cela, nous souhaitons utiliser les entités qui nous intéressent à la main.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-231">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="1f5a9-232">Nous n’utiliserons le modèle EDM que pour générer le schéma de base de données et fournir les métadonnées dont EF4 a besoin pour mapper les objets dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-232">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![test_01 EF](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="1f5a9-234">**Figure 1**</span><span class="sxs-lookup"><span data-stu-id="1f5a9-234">**Figure 1**</span></span>

<span data-ttu-id="1f5a9-235">Remarque : Si vous souhaitez développer le modèle EDM en premier, il est possible de générer du code POCO propre à partir du modèle EDM.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-235">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="1f5a9-236">Vous pouvez le faire avec une extension Visual Studio 2010 fournie par l’équipe de programmabilité des données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-236">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="1f5a9-237">Pour télécharger l’extension, lancez le gestionnaire d’extensions à partir du menu outils de Visual Studio et recherchez « POCO » dans la galerie en ligne de modèles (voir figure 2).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-237">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="1f5a9-238">Plusieurs modèles POCO sont disponibles pour EF.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-238">There are several POCO templates available for EF.</span></span> <span data-ttu-id="1f5a9-239">Pour plus d’informations sur l’utilisation du modèle, consultez la rubrique « [procédure pas à pas : modèle POCO pour le Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)».</span><span class="sxs-lookup"><span data-stu-id="1f5a9-239">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![test_02 EF](~/ef6/media/eftest-02.png)

<span data-ttu-id="1f5a9-241">**Figure 2**</span><span class="sxs-lookup"><span data-stu-id="1f5a9-241">**Figure 2**</span></span>

<span data-ttu-id="1f5a9-242">À partir de ce point de départ POCO, nous explorerons deux approches différentes du code testable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-242">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="1f5a9-243">La première approche que j’appelle l’approche EF, c’est qu’elle tire parti des abstractions de l’API Entity Framework pour implémenter des unités de travail et des dépôts.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-243">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="1f5a9-244">Dans la deuxième approche, nous allons créer nos propres abstractions de référentiel personnalisées, puis voir les avantages et les inconvénients de chaque approche.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-244">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="1f5a9-245">Nous allons commencer par explorer l’approche EF.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-245">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="1f5a9-246">Une implémentation d’EF centrée</span><span class="sxs-lookup"><span data-stu-id="1f5a9-246">An EF Centric Implementation</span></span>

<span data-ttu-id="1f5a9-247">Examinez l’action de contrôleur suivante à partir d’un projet MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-247">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="1f5a9-248">L’action extrait un objet Employee et retourne un résultat pour afficher une vue détaillée de l’employé.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-248">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="1f5a9-249">Le code est-il testable ?</span><span class="sxs-lookup"><span data-stu-id="1f5a9-249">Is the code testable?</span></span> <span data-ttu-id="1f5a9-250">Il faut au moins deux tests pour vérifier le comportement de l’action.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-250">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="1f5a9-251">Tout d’abord, nous souhaitons vérifier que l’action retourne la bonne vue, un test facile.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-251">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="1f5a9-252">Nous aimerions également écrire un test pour vérifier que l’action récupère le bon employé et nous aimerions le faire sans exécuter de code pour interroger la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-252">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="1f5a9-253">N’oubliez pas que nous souhaitons isoler le code testé.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-253">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="1f5a9-254">L’isolation garantit que le test n’échoue pas en raison d’un bogue dans le code d’accès aux données ou la configuration de la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-254">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="1f5a9-255">Si le test échoue, nous savons que nous avons un bogue dans la logique du contrôleur, et non dans un composant système de niveau inférieur.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-255">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="1f5a9-256">Pour atteindre l’isolation, nous avons besoin de certaines abstractions comme les interfaces présentées précédemment pour les dépôts et les unités de travail.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-256">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="1f5a9-257">N’oubliez pas que le modèle de référentiel est conçu pour faire l’objet d’un médiateur entre les objets de domaine et la couche de mappage de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-257">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="1f5a9-258">Dans ce scénario, EF4 *est* la couche de mappage des données et fournit déjà une abstraction de type dépôt nommée IObjectSet &lt; T &gt; (à partir de l’espace de noms System. Data. Objects).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-258">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="1f5a9-259">La définition de l’interface se présente comme suit.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-259">The interface definition looks like the following.</span></span>

``` csharp
    public interface IObjectSet<TEntity> :
                     IQueryable<TEntity>,
                     IEnumerable<TEntity>,
                     IQueryable,
                     IEnumerable
                     where TEntity : class
    {
        void AddObject(TEntity entity);
        void Attach(TEntity entity);
        void DeleteObject(TEntity entity);
        void Detach(TEntity entity);
    }
```

<span data-ttu-id="1f5a9-260">IObjectSet &lt; T &gt; répond à la configuration requise pour un référentiel, car il ressemble à une collection d’objets (via IEnumerable &lt; T &gt; ) et fournit des méthodes pour ajouter et supprimer des objets de la collection simulée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-260">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="1f5a9-261">Les méthodes d’attachement et de détachement exposent des fonctionnalités supplémentaires de l’API EF4.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-261">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="1f5a9-262">Pour utiliser IObjectSet &lt; T &gt; comme interface pour les dépôts, nous avons besoin d’une abstraction d’unité de travail pour lier les référentiels ensemble.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-262">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="1f5a9-263">Une implémentation concrète de cette interface va communiquer avec SQL Server et est facile à créer à l’aide de la classe ObjectContext de EF4.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-263">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="1f5a9-264">La classe ObjectContext est l’unité de travail réelle dans l’API EF4.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-264">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;
            _context = new ObjectContext(connectionString);
        }

        public IObjectSet<Employee> Employees {
            get { return _context.CreateObjectSet<Employee>(); }
        }

        public IObjectSet<TimeCard> TimeCards {
            get { return _context.CreateObjectSet<TimeCard>(); }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

<span data-ttu-id="1f5a9-265">L’intégration d’un IObjectSet &lt; T &gt; à Life est aussi simple que l’appel de la méthode méthode CreateObjectSet de l’objet ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-265">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="1f5a9-266">En coulisses, l’infrastructure utilisera les métadonnées que nous avons fournies dans le modèle EDM pour produire un ObjectSet &lt; T concret &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-266">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="1f5a9-267">Nous allons utiliser le retour de l' &lt; interface IObjectSet T &gt; , car cela permet de préserver la testabilité dans le code client.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-267">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="1f5a9-268">Cette implémentation concrète est utile en production, mais nous devons nous concentrer sur la façon dont nous allons utiliser notre abstraction IUnitOfWork pour faciliter les tests.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-268">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="1f5a9-269">Les doubles de test</span><span class="sxs-lookup"><span data-stu-id="1f5a9-269">The Test Doubles</span></span>

<span data-ttu-id="1f5a9-270">Pour isoler l’action du contrôleur, nous avons besoin de la possibilité de basculer entre l’unité de travail réelle (avec un ObjectContext) et une unité de travail de test double ou « factice » (en effectuant des opérations en mémoire).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-270">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="1f5a9-271">L’approche courante pour effectuer ce type de commutation consiste à ne pas laisser le contrôleur MVC instancier une unité de travail, mais à passer à la place l’unité de travail dans le contrôleur en tant que paramètre de constructeur.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-271">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="1f5a9-272">Le code ci-dessus est un exemple d’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-272">The above code is an example of dependency injection.</span></span> <span data-ttu-id="1f5a9-273">Nous n’autorisons pas le contrôleur à créer sa dépendance (l’unité de travail), mais injectent la dépendance dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-273">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="1f5a9-274">Dans un projet MVC, il est courant d’utiliser une fabrique de contrôleurs personnalisée en association avec un conteneur d’inversion de contrôle (IoC) pour automatiser l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-274">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="1f5a9-275">Ces rubriques n’entrent pas dans le cadre de cet article, mais vous pouvez en savoir plus en suivant les références à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-275">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="1f5a9-276">Une implémentation fictive d’une unité de travail que nous pouvons utiliser pour le test peut ressembler à ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-276">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

``` csharp
    public class InMemoryUnitOfWork : IUnitOfWork {
        public InMemoryUnitOfWork() {
            Committed = false;
        }
        public IObjectSet<Employee> Employees {
            get;
            set;
        }

        public IObjectSet<TimeCard> TimeCards {
            get;
            set;
        }

        public bool Committed { get; set; }
        public void Commit() {
            Committed = true;
        }
    }
```

<span data-ttu-id="1f5a9-277">Notez que l’unité de travail factice expose une propriété validée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-277">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="1f5a9-278">Il est parfois utile d’ajouter des fonctionnalités à une classe factice qui facilite les tests.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-278">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="1f5a9-279">Dans ce cas, il est facile d’observer si le code valide une unité de travail en vérifiant la propriété validée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-279">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="1f5a9-280">Nous aurons également besoin d’un faux IObjectSet &lt; T &gt; pour contenir les objets Employee et de la table de pointage en mémoire.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-280">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="1f5a9-281">Nous pouvons fournir une implémentation unique à l’aide de génériques.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-281">We can provide a single implementation using generics.</span></span>

``` csharp
    public class InMemoryObjectSet<T> : IObjectSet<T> where T : class
        public InMemoryObjectSet()
            : this(Enumerable.Empty<T>()) {
        }
        public InMemoryObjectSet(IEnumerable<T> entities) {
            _set = new HashSet<T>();
            foreach (var entity in entities) {
                _set.Add(entity);
            }
            _queryableSet = _set.AsQueryable();
        }
        public void AddObject(T entity) {
            _set.Add(entity);
        }
        public void Attach(T entity) {
            _set.Add(entity);
        }
        public void DeleteObject(T entity) {
            _set.Remove(entity);
        }
        public void Detach(T entity) {
            _set.Remove(entity);
        }
        public Type ElementType {
            get { return _queryableSet.ElementType; }
        }
        public Expression Expression {
            get { return _queryableSet.Expression; }
        }
        public IQueryProvider Provider {
            get { return _queryableSet.Provider; }
        }
        public IEnumerator<T> GetEnumerator() {
            return _set.GetEnumerator();
        }
        IEnumerator IEnumerable.GetEnumerator() {
            return GetEnumerator();
        }

        readonly HashSet<T> _set;
        readonly IQueryable<T> _queryableSet;
    }
```

<span data-ttu-id="1f5a9-282">Ce double de test délègue la majeure partie de son travail à un objet HashSet T sous-jacent &lt; &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-282">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="1f5a9-283">Notez que IObjectSet &lt; T &gt; requiert une contrainte générique appliquant t en tant que classe (un type référence) et nous oblige également à implémenter l’option IQueryable &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-283">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="1f5a9-284">Il est facile de faire apparaître une collection en mémoire sous la forme d’un IQueryable &lt; T &gt; à l’aide de l’opérateur standard LINQ AsQueryable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-284">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="1f5a9-285">Les tests</span><span class="sxs-lookup"><span data-stu-id="1f5a9-285">The Tests</span></span>

<span data-ttu-id="1f5a9-286">Les tests unitaires traditionnels utilisent une classe de test unique pour contenir tous les tests pour toutes les actions dans un seul contrôleur MVC.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-286">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="1f5a9-287">Nous pouvons écrire ces tests, ou n’importe quel type de test unitaire, à l’aide des substituts en mémoire que nous avons générés.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-287">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="1f5a9-288">Toutefois, pour cet article, nous allons éviter l’approche de la classe de test monolithique et regrouper nos tests pour vous concentrer sur une fonctionnalité spécifique.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-288">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="1f5a9-289">Par exemple, « Create New Employee » peut être la fonctionnalité que nous voulons tester, donc nous utiliserons une seule classe de test pour vérifier l’action de contrôleur unique responsable de la création d’un nouvel employé.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-289">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="1f5a9-290">Il existe un code d’installation commun dont nous avons besoin pour toutes ces classes de test affinées.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-290">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="1f5a9-291">Par exemple, nous devons toujours créer nos référentiels en mémoire et l’unité de travail factice.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-291">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="1f5a9-292">Nous avons également besoin d’une instance du contrôleur Employee avec l’unité de travail factice injectée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-292">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="1f5a9-293">Nous allons partager ce code d’installation commun entre les classes de test à l’aide d’une classe de base.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-293">We’ll share this common setup code across test classes by using a base class.</span></span>

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .ToList();
            _repository = new InMemoryObjectSet<Employee>(_employeeData);
            _unitOfWork = new InMemoryUnitOfWork();
            _unitOfWork.Employees = _repository;
            _controller = new EmployeeController(_unitOfWork);
        }

        protected IList<Employee> _employeeData;
        protected EmployeeController _controller;
        protected InMemoryObjectSet<Employee> _repository;
        protected InMemoryUnitOfWork _unitOfWork;
    }
```

<span data-ttu-id="1f5a9-294">L’objet « maman » que nous utilisons dans la classe de base est un modèle commun pour la création de données de test.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-294">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="1f5a9-295">Un objet maman contient des méthodes de fabrique pour instancier des entités de test à utiliser sur plusieurs contextes de test.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-295">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

``` csharp
    public static class EmployeeObjectMother {
        public static IEnumerable<Employee> CreateEmployees() {
            yield return new Employee() {
                Id = 1, Name = "Scott", HireDate=new DateTime(2002, 1, 1)
            };
            yield return new Employee() {
                Id = 2, Name = "Poonam", HireDate=new DateTime(2001, 1, 1)
            };
            yield return new Employee() {
                Id = 3, Name = "Simon", HireDate=new DateTime(2008, 1, 1)
            };
        }
        // ... more fake data for different scenarios
    }
```

<span data-ttu-id="1f5a9-296">Nous pouvons utiliser EmployeeControllerTestBase comme classe de base pour un certain nombre de contextes de test (voir la figure 3).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-296">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="1f5a9-297">Chaque contexte de test teste une action de contrôleur spécifique.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-297">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="1f5a9-298">Par exemple, un seul contexte de test se concentrera sur le test de l’action de création utilisée lors d’une requête HTTP. (pour afficher la vue de la création d’un employé) et un autre contexte se concentrera sur l’action de création utilisée dans une requête HTTP Après (pour prendre les informations envoyées par l’utilisateur pour créer un employé).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-298">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="1f5a9-299">Chaque classe dérivée est uniquement responsable de l’installation nécessaire dans son contexte spécifique, et pour fournir les assertions nécessaires pour vérifier les résultats pour son contexte de test spécifique.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-299">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![test_03 EF](~/ef6/media/eftest-03.png)

<span data-ttu-id="1f5a9-301">**Figure 3**</span><span class="sxs-lookup"><span data-stu-id="1f5a9-301">**Figure 3**</span></span>

<span data-ttu-id="1f5a9-302">La Convention d’affectation de noms et le style de test présentés ici ne sont pas requis pour le code testable, il s’agit d’une seule approche.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-302">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="1f5a9-303">La figure 4 montre les tests en cours d’exécution dans le plug-in Test Runner de jet cerveau pour Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-303">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![test_04 EF](~/ef6/media/eftest-04.png)

<span data-ttu-id="1f5a9-305">**Figure 4**</span><span class="sxs-lookup"><span data-stu-id="1f5a9-305">**Figure 4**</span></span>

<span data-ttu-id="1f5a9-306">Avec une classe de base pour gérer le code d’installation partagé, les tests unitaires pour chaque action du contrôleur sont petits et faciles à écrire.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-306">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="1f5a9-307">Les tests s’exécuteront rapidement (puisque nous effectuons des opérations en mémoire) et ne devraient pas échouer en raison d’une infrastructure ou de problèmes environnementaux non liés (car nous avons isolé l’unité testée).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-307">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerCreateActionPostTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldAddNewEmployeeToRepository() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_repository.Contains(_newEmployee));
        }
        [TestMethod]
        public void ShouldCommitUnitOfWork() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_unitOfWork.Committed);
        }
        // ... more tests

        Employee _newEmployee = new Employee() {
            Name = "NEW EMPLOYEE",
            HireDate = new System.DateTime(2010, 1, 1)
        };
    }
```

<span data-ttu-id="1f5a9-308">Dans ces tests, la classe de base effectue la plupart des tâches d’installation.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-308">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="1f5a9-309">Souvenez-vous que le constructeur de classe de base crée le référentiel en mémoire, une unité de travail factice et une instance de la classe EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-309">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="1f5a9-310">La classe de test dérive de cette classe de base et se concentre sur les spécificités du test de la méthode Create.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-310">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="1f5a9-311">Dans ce cas, les spécificités s’appliquent aux étapes « arrange, Act et Assert » que vous verrez dans toutes les procédures de test unitaire :</span><span class="sxs-lookup"><span data-stu-id="1f5a9-311">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="1f5a9-312">Créez un objet nouvel employé pour simuler des données entrantes.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-312">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="1f5a9-313">Appelez l’action Create du EmployeeController et transmettez nouvel employé.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-313">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="1f5a9-314">Vérifiez que l’action créer produit les résultats attendus (l’employé apparaît dans le référentiel).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-314">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="1f5a9-315">Ce que nous avons créé nous permet de tester n’importe quelle action EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-315">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="1f5a9-316">Par exemple, lorsque nous écrivons des tests pour l’action d’index du contrôleur Employee Controller, nous pouvons hériter de la classe de base test pour établir la même configuration de base pour nos tests.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-316">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="1f5a9-317">Là encore, la classe de base crée le référentiel en mémoire, l’unité de travail factice et une instance de EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-317">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="1f5a9-318">Les tests de l’action d’index doivent uniquement se concentrer sur l’appel de l’action d’index et le test des qualités du modèle retourné par l’action.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-318">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count);
        }
        [TestMethod]
        public void ShouldOrderModelByHiredateAscending() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                         as IEnumerable<Employee>;
            Assert.IsTrue(model.SequenceEqual(
                           _employeeData.OrderBy(e => e.HireDate)));
        }
        // ...
    }
```

<span data-ttu-id="1f5a9-319">Les tests que nous créons avec les substituts en mémoire sont orientés vers le test de l' *État* du logiciel.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-319">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="1f5a9-320">Par exemple, lors du test de l’action de création, nous souhaitons inspecter l’état du dépôt après l’exécution de l’action de création : le référentiel conserve-t-il le nouvel employé ?</span><span class="sxs-lookup"><span data-stu-id="1f5a9-320">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="1f5a9-321">Nous examinerons ultérieurement les tests basés sur l’interaction.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-321">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="1f5a9-322">Le test basé sur l’interaction vous demande si le code testé a appelé les méthodes appropriées sur nos objets et a passé les paramètres corrects.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-322">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="1f5a9-323">Pour le moment, nous allons passer à la couverture d’un autre modèle de conception : le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-323">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="1f5a9-324">Chargement hâtif et chargement différé</span><span class="sxs-lookup"><span data-stu-id="1f5a9-324">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="1f5a9-325">À un moment donné dans l’application Web MVC ASP.NET, nous pouvons souhaiter afficher les informations d’un employé et inclure les cartes de point associées de l’employé.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-325">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="1f5a9-326">Par exemple, il peut y avoir un affichage de résumé de la carte de temps qui indique le nom de l’employé et le nombre total de cartes de temps dans le système.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-326">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="1f5a9-327">Il existe plusieurs approches pour implémenter cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-327">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="1f5a9-328">Projection</span><span class="sxs-lookup"><span data-stu-id="1f5a9-328">Projection</span></span>

<span data-ttu-id="1f5a9-329">Une approche simple pour créer le résumé consiste à construire un modèle dédié aux informations que nous souhaitons afficher dans la vue.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-329">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="1f5a9-330">Dans ce scénario, le modèle peut ressembler à ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-330">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="1f5a9-331">Notez que le EmployeeSummaryViewModel n’est pas une entité. en d’autres termes, il ne doit pas être conservé dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-331">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="1f5a9-332">Nous allons uniquement utiliser cette classe pour mélanger des données dans la vue d’une manière fortement typée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-332">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="1f5a9-333">Le modèle de vue est semblable à un objet de transfert de données (DTO), car il ne contient aucun comportement (aucune méthode), uniquement des propriétés.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-333">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="1f5a9-334">Les propriétés contiendront les données que vous devez déplacer.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-334">The properties will hold the data we need to move.</span></span> <span data-ttu-id="1f5a9-335">Il est facile d’instancier ce modèle de vue à l’aide de l’opérateur de projection standard de LINQ (l’opérateur SELECT).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-335">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

``` csharp
    public ViewResult Summary(int id) {
        var model = _unitOfWork.Employees
                               .Where(e => e.Id == id)
                               .Select(e => new EmployeeSummaryViewModel
                                  {
                                    Name = e.Name,
                                    TotalTimeCards = e.TimeCards.Count()
                                  })
                               .Single();
        return View(model);
    }
```

<span data-ttu-id="1f5a9-336">Il existe deux fonctionnalités notables pour le code ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-336">There are two notable features to the above code.</span></span> <span data-ttu-id="1f5a9-337">Tout d’abord, le code est facile à tester, car il est toujours facile à observer et à isoler.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-337">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="1f5a9-338">L’opérateur SELECT fonctionne tout aussi bien sur nos substituts en mémoire que sur l’unité de travail réelle.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-338">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerSummaryActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithCorrectEmployeeSummary() {
            var id = 1;
            var result = _controller.Summary(id);
            var model = result.ViewData.Model as EmployeeSummaryViewModel;
            Assert.IsTrue(model.TotalTimeCards == 3);
        }
        // ...
    }
```

<span data-ttu-id="1f5a9-339">La deuxième fonctionnalité notable est la manière dont le code permet à EF4 de générer une requête unique et efficace pour assembler les informations relatives aux employés et aux cartes de temps.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-339">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="1f5a9-340">Nous avons chargé les informations sur les employés et les informations de carte de temps dans le même objet sans utiliser d’API spéciales.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-340">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="1f5a9-341">Le code a simplement exprimé les informations dont il a besoin à l’aide d’opérateurs LINQ standard qui fonctionnent avec les sources de données en mémoire, ainsi que les sources de données distantes.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-341">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="1f5a9-342">EF4 a pu traduire les arborescences d’expressions générées par la requête LINQ et le \# compilateur C en une requête T-SQL unique et efficace.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-342">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

``` SQL
    SELECT
    [Limit1].[Id] AS [Id],
    [Limit1].[Name] AS [Name],
    [Limit1].[C1] AS [C1]
    FROM (SELECT TOP (2)
      [Project1].[Id] AS [Id],
      [Project1].[Name] AS [Name],
      [Project1].[C1] AS [C1]
      FROM (SELECT
        [Extent1].[Id] AS [Id],
        [Extent1].[Name] AS [Name],
        (SELECT COUNT(1) AS [A1]
         FROM [dbo].[TimeCards] AS [Extent2]
         WHERE [Extent1].[Id] =
               [Extent2].[EmployeeTimeCard_TimeCard_Id]) AS [C1]
              FROM [dbo].[Employees] AS [Extent1]
               WHERE [Extent1].[Id] = @p__linq__0
         )  AS [Project1]
    )  AS [Limit1]
```

<span data-ttu-id="1f5a9-343">Il y a d’autres fois que nous ne souhaitons pas travailler avec un modèle de vue ou un objet DTO, mais avec des entités réelles.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-343">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="1f5a9-344">Lorsque nous savons que nous avons besoin d’un employé *et* des cartes de l’employé, nous pouvons charger les données associées de manière discrète et efficace.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-344">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="1f5a9-345">Chargement hâtif explicite</span><span class="sxs-lookup"><span data-stu-id="1f5a9-345">Explicit Eager Loading</span></span>

<span data-ttu-id="1f5a9-346">Lorsque nous souhaitons charger des informations d’entité connexes, nous avons besoin d’un mécanisme pour la logique métier (ou dans ce scénario, la logique d’action du contrôleur) pour exprimer sa volonté dans le référentiel.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-346">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="1f5a9-347">La classe EF4 ObjectQuery &lt; T &gt; définit une méthode Include pour spécifier les objets connexes à récupérer au cours d’une requête.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-347">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="1f5a9-348">N’oubliez pas que EF4 ObjectContext expose des entités via la classe concrète ObjectSet &lt; t &gt; qui hérite de ObjectQuery &lt; T &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-348">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="1f5a9-349">Si nous utilisions &lt; &gt; des références ObjectSet T dans notre action de contrôleur, nous pourrions écrire le code suivant pour spécifier un chargement hâtif d’informations de carte de temps pour chaque employé.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-349">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="1f5a9-350">Toutefois, étant donné que nous essayons de garder notre code testable, nous n’exposez pas ObjectSet &lt; T &gt; depuis l’extérieur de la classe de l’unité de travail réelle.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-350">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="1f5a9-351">Au lieu de cela, nous nous appuyons sur l' &lt; interface IObjectSet t &gt; qui est plus facile à falsifier, mais IObjectSet &lt; t &gt; ne définit pas de méthode Include.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-351">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="1f5a9-352">La beauté de LINQ est que nous pouvons créer notre propre opérateur include.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-352">The beauty of LINQ is that we can create our own Include operator.</span></span>

``` csharp
    public static class QueryableExtensions {
        public static IQueryable<T> Include<T>
                (this IQueryable<T> sequence, string path) {
            var objectQuery = sequence as ObjectQuery<T>;
            if(objectQuery != null)
            {
                return objectQuery.Include(path);
            }
            return sequence;
        }
    }
```

<span data-ttu-id="1f5a9-353">Notez que cet opérateur include est défini en tant que méthode d’extension pour IQueryable &lt; t &gt; au lieu de IObjectSet &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-353">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="1f5a9-354">Cela nous donne la possibilité d’utiliser la méthode avec un plus grand nombre de types possibles, notamment IQueryable &lt; t &gt; , IObjectSet &lt; t &gt; , ObjectQuery &lt; t &gt; et ObjectSet &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-354">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="1f5a9-355">Dans le cas où la séquence sous-jacente n’est pas un EF4 ObjectQuery &lt; T authentique &gt; , il n’y a pas de dommages causés et l’opérateur include est une absence d’opération.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-355">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="1f5a9-356">Si la séquence sous-jacente *est* un ObjectQuery &lt; t &gt; (ou dérivée de OBJECTQUERY &lt; t &gt; ), EF4 verra notre exigence de données supplémentaires et formulera la requête SQL appropriée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-356">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="1f5a9-357">Avec ce nouvel opérateur en place, nous pouvons demander explicitement un chargement hâtif d’informations de carte de temps dans le référentiel.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-357">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="1f5a9-358">Lorsqu’il est exécuté sur un ObjectContext réel, le code génère la requête unique suivante.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-358">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="1f5a9-359">La requête rassemble suffisamment d’informations de la base de données en un seul voyage pour matérialiser les objets des employés et remplir entièrement leur propriété de la table de temps.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-359">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

``` SQL
    SELECT
    [Project1].[Id] AS [Id],
    [Project1].[Name] AS [Name],
    [Project1].[HireDate] AS [HireDate],
    [Project1].[C1] AS [C1],
    [Project1].[Id1] AS [Id1],
    [Project1].[Hours] AS [Hours],
    [Project1].[EffectiveDate] AS [EffectiveDate],
    [Project1].[EmployeeTimeCard_TimeCard_Id] AS [EmployeeTimeCard_TimeCard_Id]
    FROM ( SELECT
         [Extent1].[Id] AS [Id],
         [Extent1].[Name] AS [Name],
         [Extent1].[HireDate] AS [HireDate],
         [Extent2].[Id] AS [Id1],
         [Extent2].[Hours] AS [Hours],
         [Extent2].[EffectiveDate] AS [EffectiveDate],
         [Extent2].[EmployeeTimeCard_TimeCard_Id] AS
                    [EmployeeTimeCard_TimeCard_Id],
         CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int)
         ELSE 1 END AS [C1]
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

<span data-ttu-id="1f5a9-360">La bonne nouvelle, c’est que le code à l’intérieur de la méthode d’action reste entièrement testable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-360">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="1f5a9-361">Nous n’avons pas besoin de fournir des fonctionnalités supplémentaires pour notre substituts pour prendre en charge l’opérateur include.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-361">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="1f5a9-362">La mauvaise nouvelle, c’est que nous devions utiliser l’opérateur include à l’intérieur du code, nous souhaitions conserver l’ignore de la persistance.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-362">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="1f5a9-363">Il s’agit d’un excellent exemple du type de compromis que vous devrez évaluer lors de la création de code testable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-363">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="1f5a9-364">Dans certains cas, vous devez laisser des problèmes de persistance en dehors de l’abstraction du référentiel pour atteindre les objectifs de performances.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-364">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="1f5a9-365">L’alternative au chargement hâtif est le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-365">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="1f5a9-366">Le chargement différé signifie que nous n’avons *pas* besoin de notre code d’entreprise pour annoncer explicitement la nécessité de données associées.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-366">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="1f5a9-367">Au lieu de cela, nous utilisons nos entités dans l’application et si des données supplémentaires sont nécessaires Entity Framework chargera les données à la demande.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-367">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="1f5a9-368">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="1f5a9-368">Lazy Loading</span></span>

<span data-ttu-id="1f5a9-369">Il est facile d’imaginer un scénario dans lequel nous ne savons pas quelles données une partie de la logique métier aura besoin.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-369">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="1f5a9-370">Nous savons peut-être que la logique a besoin d’un objet Employee, mais nous pouvons créer des branches dans différents chemins d’exécution où certains de ces chemins nécessitent des informations de carte de l’employé et d’autres non.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-370">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="1f5a9-371">Les scénarios de ce type sont parfaits pour le chargement différé implicite, car les données s’affichent par magie en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-371">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="1f5a9-372">Le chargement différé, également connu sous le nom de chargement différé, place certaines exigences sur nos objets d’entité.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-372">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="1f5a9-373">Les POCO avec l’ignorance de la persistance réelle ne représenteront aucune exigence de la couche de persistance, mais l’ignorance de la persistance est pratiquement impossible à atteindre.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-373">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="1f5a9-374">Au lieu de cela, nous mesurons l’ignorance de la persistance en degrés relatifs.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-374">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="1f5a9-375">Cela serait malheureux si nous avions besoin d’hériter d’une classe de base orientée persistance ou d’utiliser une collection spécialisée pour atteindre un chargement différé dans les POCO.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-375">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="1f5a9-376">Heureusement, EF4 a une solution moins intrusive.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-376">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="1f5a9-377">Pratiquement indétectables</span><span class="sxs-lookup"><span data-stu-id="1f5a9-377">Virtually Undetectable</span></span>

<span data-ttu-id="1f5a9-378">Lors de l’utilisation d’objets POCO, EF4 peut générer dynamiquement des proxies d’exécution pour les entités.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-378">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="1f5a9-379">Ces proxies encapsulent de façon invisible les POCO matérialisés et fournissent des services supplémentaires en interceptant chaque propriété obtenir et définir l’opération pour effectuer un travail supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-379">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="1f5a9-380">L’un de ces services est la fonctionnalité de chargement différé que nous recherchons.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-380">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="1f5a9-381">Un autre service est un mécanisme de suivi des modifications efficace qui peut enregistrer lorsque le programme modifie les valeurs de propriété d’une entité.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-381">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="1f5a9-382">La liste des modifications est utilisée par ObjectContext pendant la méthode SaveChanges pour rendre persistantes toutes les entités modifiées à l’aide de commandes UPDATE.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-382">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="1f5a9-383">Toutefois, pour que ces proxies fonctionnent, ils ont besoin d’un moyen de se raccorder à des opérations d’extraction et de définition de propriétés sur une entité, et les proxys atteignent cet objectif en remplaçant les membres virtuels.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-383">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="1f5a9-384">Par conséquent, si nous souhaitons avoir un chargement différé implicite et un suivi efficace des modifications, nous devons revenir à nos définitions de classe POCO et marquer les propriétés comme étant virtuelles.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-384">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="1f5a9-385">Nous pouvons toujours indiquer que l’entité Employee est principalement ignorant la persistance.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-385">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="1f5a9-386">La seule exigence consiste à utiliser des membres virtuels et cela n’a aucun impact sur la testabilité du code.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-386">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="1f5a9-387">Nous n’avons pas besoin de dériver d’une classe de base spéciale, ni même d’utiliser une collection spéciale dédiée au chargement différé.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-387">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="1f5a9-388">Comme le montre le code, toute classe qui implémente ICollection &lt; T &gt; est disponible pour conserver les entités associées.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-388">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="1f5a9-389">Il y a également une modification mineure que nous devons faire au sein de notre unité de travail.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-389">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="1f5a9-390">Le chargement différé est *désactivé* par défaut quand vous travaillez directement avec un objet ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-390">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="1f5a9-391">Il existe une propriété que nous pouvons définir sur la propriété ContextOptions pour activer le chargement différé, et nous pouvons définir cette propriété à l’intérieur de notre véritable unité de travail si vous souhaitez activer le chargement différé partout.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-391">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
         public SqlUnitOfWork() {
             // ...
             _context = new ObjectContext(connectionString);
             _context.ContextOptions.LazyLoadingEnabled = true;
         }    
         // ...
     }
```

<span data-ttu-id="1f5a9-392">Si le chargement différé implicite est activé, le code de l’application peut utiliser un employé et les cartes de temps associées de l’employé, tout en restant tranquillement ne connaissant pas le travail nécessaire à EF pour charger les données supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-392">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="1f5a9-393">Le chargement différé rend le code de l’application plus facile à écrire et, avec la magie du proxy, le code reste entièrement testable.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-393">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="1f5a9-394">Les substituts en mémoire de l’unité de travail peuvent simplement précharger des entités factices avec des données associées lorsque cela est nécessaire pendant un test.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-394">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="1f5a9-395">À ce stade, nous nous pencherons sur la création de référentiels à l’aide de IObjectSet &lt; T &gt; et nous examinerons des abstractions pour masquer tous les signes de l’infrastructure de persistance.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-395">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="1f5a9-396">Référentiels personnalisés</span><span class="sxs-lookup"><span data-stu-id="1f5a9-396">Custom Repositories</span></span>

<span data-ttu-id="1f5a9-397">Lorsque nous avons présenté le modèle de conception d’unité de travail dans cet article, nous avons fourni un exemple de code pour ce à quoi peut ressembler l’unité de travail.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-397">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="1f5a9-398">Nous allons représenter cette idée originale à l’aide du scénario Employee et Employee Time Card que nous travaillons.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-398">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="1f5a9-399">La principale différence entre cette unité de travail et l’unité de travail que nous avons créée dans la dernière section est la manière dont cette unité de travail n’utilise pas d’abstractions de l’infrastructure EF4 (il n’y a pas de IObjectSet &lt; T &gt; ).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-399">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="1f5a9-400">IObjectSet &lt; T &gt; fonctionne bien comme une interface de référentiel, mais l’API qu’il expose peut ne pas être parfaitement adaptée aux besoins de l’application.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-400">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="1f5a9-401">Dans cette approche à venir, nous allons représenter des dépôts à l’aide d’une &lt; abstraction IRepository T personnalisée &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-401">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="1f5a9-402">De nombreux développeurs qui suivent la conception pilotée par test, la conception pilotée par le comportement et les méthodologies pilotées par domaine préfèrent l' &lt; approche IRepository T &gt; pour plusieurs raisons.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-402">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="1f5a9-403">Tout d’abord, &lt; l' &gt; interface IRepository T représente une couche « anti-corruption ».</span><span class="sxs-lookup"><span data-stu-id="1f5a9-403">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="1f5a9-404">Comme décrit par Eric Evans dans son livre de conception piloté par domaine, une couche de lutte contre la corruption permet d’éloigner votre code de domaine des API d’infrastructure, comme une API de persistance.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-404">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="1f5a9-405">Deuxièmement, les développeurs peuvent créer des méthodes dans le référentiel qui répondent aux besoins exacts d’une application (telle qu’elle a été détectée pendant l’écriture de tests).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-405">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="1f5a9-406">Par exemple, il se peut que nous ayons souvent besoin de localiser une seule entité à l’aide d’une valeur d’ID. nous pouvons donc ajouter une méthode FindById à l’interface du référentiel.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-406">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="1f5a9-407">Notre &lt; définition IRepository T &gt; se présente comme suit.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-407">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

``` csharp
    public interface IRepository<T>
                    where T : class, IEntity {
        IQueryable<T> FindAll();
        IQueryable<T> FindWhere(Expression<Func\<T, bool>> predicate);
        T FindById(int id);       
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="1f5a9-408">Notez que nous allons revenir à l’utilisation d’une &lt; interface T IQueryable &gt; pour exposer des collections d’entités.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-408">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="1f5a9-409">IQueryable &lt; T &gt; permet aux arborescences d’expression LINQ de circuler dans le fournisseur EF4 et de fournir au fournisseur une vue holistique de la requête.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-409">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="1f5a9-410">Une seconde option consisterait à retourner IEnumerable &lt; T &gt; , ce qui signifie que le fournisseur LINQ EF4 verra uniquement les expressions générées dans le référentiel.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-410">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="1f5a9-411">Les regroupements, ordonnancements et projection effectués en dehors du référentiel ne sont pas composés de la commande SQL envoyée à la base de données, ce qui peut nuire aux performances.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-411">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="1f5a9-412">En revanche, un référentiel renvoyant uniquement &lt; &gt; des résultats IEnumerable T ne vous étonnera jamais avec une nouvelle commande SQL.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-412">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="1f5a9-413">Les deux approches fonctionnent et les deux approches restent testables.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-413">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="1f5a9-414">Il est facile de fournir une implémentation unique de l' &lt; interface IRepository T &gt; à l’aide de génériques et de l’API ObjectContext EF4.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-414">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

``` csharp
    public class SqlRepository<T> : IRepository<T>
                                    where T : class, IEntity {
        public SqlRepository(ObjectContext context) {
            _objectSet = context.CreateObjectSet<T>();
        }
        public IQueryable<T> FindAll() {
            return _objectSet;
        }
        public IQueryable<T> FindWhere(
                               Expression<Func\<T, bool>> predicate) {
            return _objectSet.Where(predicate);
        }
        public T FindById(int id) {
            return _objectSet.Single(o => o.Id == id);
        }
        public void Add(T newEntity) {
            _objectSet.AddObject(newEntity);
        }
        public void Remove(T entity) {
            _objectSet.DeleteObject(entity);
        }
        protected ObjectSet<T> _objectSet;
    }
```

<span data-ttu-id="1f5a9-415">L' &lt; approche IRepository T &gt; nous donne un contrôle supplémentaire sur nos requêtes, car un client doit appeler une méthode pour accéder à une entité.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-415">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="1f5a9-416">À l’intérieur de la méthode, nous pourrions fournir des contrôles supplémentaires et des opérateurs LINQ pour appliquer des contraintes d’application.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-416">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="1f5a9-417">Notez que l’interface a deux contraintes sur le paramètre de type générique.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-417">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="1f5a9-418">La première contrainte est la classe cons Tainted requise par ObjectSet &lt; T &gt; , et la deuxième contrainte force nos entités à implémenter IEntity – une abstraction créée pour l’application.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-418">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="1f5a9-419">L’interface IEntity force les entités à avoir une propriété ID lisible, et nous pouvons ensuite utiliser cette propriété dans la méthode FindById.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-419">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="1f5a9-420">IEntity est défini avec le code suivant.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-420">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="1f5a9-421">IEntity peut être considéré comme une faible violation de l’ignorance de la persistance, car nos entités sont requises pour implémenter cette interface.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-421">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="1f5a9-422">Rappelez-vous que l’ignorance de la persistance concerne les compromis, et que de nombreuses fonctionnalités FindById compenseront la contrainte imposée par l’interface.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-422">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="1f5a9-423">L’interface n’a aucun impact sur la testabilité.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-423">The interface has no impact on testability.</span></span>

<span data-ttu-id="1f5a9-424">L’instanciation d’un IRepository en temps réel &lt; &gt; nécessite un EF4 ObjectContext, de sorte qu’une implémentation de l’unité de travail concrète doit gérer l’instanciation.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-424">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;

            _context = new ObjectContext(connectionString);
            _context.ContextOptions.LazyLoadingEnabled = true;
        }

        public IRepository<Employee> Employees {
            get {
                if (_employees == null) {
                    _employees = new SqlRepository<Employee>(_context);
                }
                return _employees;
            }
        }

        public IRepository<TimeCard> TimeCards {
            get {
                if (_timeCards == null) {
                    _timeCards = new SqlRepository<TimeCard>(_context);
                }
                return _timeCards;
            }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        SqlRepository<Employee> _employees = null;
        SqlRepository<TimeCard> _timeCards = null;
        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

### <a name="using-the-custom-repository"></a><span data-ttu-id="1f5a9-425">Utilisation du référentiel personnalisé</span><span class="sxs-lookup"><span data-stu-id="1f5a9-425">Using the Custom Repository</span></span>

<span data-ttu-id="1f5a9-426">L’utilisation de notre référentiel personnalisé n’est pas très différente de celle d’un référentiel basé sur IObjectSet &lt; T &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-426">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="1f5a9-427">Au lieu d’appliquer des opérateurs LINQ directement à une propriété, nous devons d’abord appeler l’une des méthodes du référentiel pour récupérer une référence de &lt; T IQueryable &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-427">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="1f5a9-428">Notez que l’opérateur include personnalisé que nous avons implémenté précédemment fonctionnera sans modification.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-428">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="1f5a9-429">La méthode FindById du référentiel supprime la logique dupliquée des actions tentant de récupérer une entité unique.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-429">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="1f5a9-430">Il n’existe aucune différence significative dans la testabilité des deux approches que nous avons examinées.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-430">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="1f5a9-431">Nous pourrions fournir des implémentations factices de IRepository &lt; T &gt; en générant des classes concrètes avec HashSet &lt; Employee &gt; , tout comme nous l’avons fait dans la dernière section.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-431">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="1f5a9-432">Toutefois, certains développeurs préfèrent utiliser des objets factices et des infrastructures d’objets factices au lieu de générer des substituts.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-432">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="1f5a9-433">Nous allons examiner l’utilisation de simulacres pour tester notre implémentation et discuter des différences entre les simulacres et les simulations dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-433">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="1f5a9-434">Test avec des simulacres</span><span class="sxs-lookup"><span data-stu-id="1f5a9-434">Testing with Mocks</span></span>

<span data-ttu-id="1f5a9-435">Il existe différentes approches pour créer ce que Martin Fowler appelle un « test double ».</span><span class="sxs-lookup"><span data-stu-id="1f5a9-435">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="1f5a9-436">Un double de test (comme un film stunt double) est un objet que vous créez pour « mettre en attente » pour les objets de production réels pendant les tests.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-436">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="1f5a9-437">Les dépôts en mémoire que nous avons créés sont des doubles de test pour les dépôts qui communiquent avec SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-437">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="1f5a9-438">Nous avons vu comment utiliser ces doubles de test lors des tests unitaires pour isoler le code et faire en sorte que les tests s’exécutent rapidement.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-438">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="1f5a9-439">Les doubles de test que nous avons créés ont des implémentations de travail réelles.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-439">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="1f5a9-440">En arrière-plan, chacun d’entre eux stocke une collection concrète d’objets, et ceux-ci ajoutent et suppriment des objets de cette collection au fur et à mesure que nous manipulons le référentiel pendant un test.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-440">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="1f5a9-441">Certains développeurs aiment créer leurs doubles de test de cette façon, avec un code réel et des implémentations de travail.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-441">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="1f5a9-442">Ces doubles de test sont ce que nous appelons les *substituts*.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-442">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="1f5a9-443">Ils ont des implémentations opérationnelles, mais ils ne sont pas assez réels pour une utilisation en production.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-443">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="1f5a9-444">Le référentiel factice n’écrit pas réellement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-444">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="1f5a9-445">Le serveur SMTP factice n’envoie pas en fait un message électronique sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-445">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="1f5a9-446">Simulacres et substituts</span><span class="sxs-lookup"><span data-stu-id="1f5a9-446">Mocks versus Fakes</span></span>

<span data-ttu-id="1f5a9-447">Il existe un autre type de test double connu sous le nom de *fictif*.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-447">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="1f5a9-448">Alors que les substituts ont des implémentations opérationnelles, les simulacres ne sont pas implémentés.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-448">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="1f5a9-449">Avec l’aide d’une infrastructure d’objets factices, nous construisons ces objets factices au moment de l’exécution et les utilisons en tant que double de test.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-449">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="1f5a9-450">Dans cette section, nous allons utiliser l’infrastructure de simulation open source MOQ.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-450">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="1f5a9-451">Voici un exemple simple d’utilisation de MOQ pour créer dynamiquement un double de test pour un dépôt d’employés.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-451">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="1f5a9-452">Nous demandons à MOQ pour une &lt; implémentation IRepository Employee &gt; et il en crée un de manière dynamique.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-452">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="1f5a9-453">Nous pouvons accéder à l’objet qui implémente IRepository &lt; Employee &gt; en accédant à la propriété Object de l' &lt; objet factice T &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-453">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="1f5a9-454">Il s’agit de cet objet interne que nous pouvons transmettre à nos contrôleurs, et ils ne savent pas s’il s’agit d’un double de test ou d’un référentiel réel.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-454">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="1f5a9-455">Nous pouvons appeler des méthodes sur l’objet de la même façon que nous appellerons des méthodes sur un objet avec une implémentation réelle.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-455">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="1f5a9-456">Vous devez vous demander ce que fera le dépôt fictif quand nous invoquons la méthode Add.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-456">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="1f5a9-457">Étant donné qu’il n’y a pas d’implémentation derrière l’objet factice, Add n’a aucun effet.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-457">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="1f5a9-458">Il n’y a pas de collection concrète en arrière-plan, comme nous l’avons fait avec les substituts que nous avons écrits, l’employé est donc ignoré.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-458">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="1f5a9-459">Qu’en est-il de la valeur de retour de FindById ?</span><span class="sxs-lookup"><span data-stu-id="1f5a9-459">What about the return value of FindById?</span></span> <span data-ttu-id="1f5a9-460">Dans ce cas, l’objet factice fait la seule chose qu’il peut faire, qui retourne une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-460">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="1f5a9-461">Étant donné que nous retournons un type référence (un employé), la valeur de retour est une valeur null.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-461">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="1f5a9-462">Les simulacres peuvent ne pas avoir de bruit ; Toutefois, nous n’avons pas parlé de deux autres fonctionnalités fictives.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-462">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="1f5a9-463">Tout d’abord, l’infrastructure MOQ enregistre tous les appels effectués sur l’objet factice.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-463">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="1f5a9-464">Plus tard dans le code, nous pouvons demander à MOQ si quelqu’un a appelé la méthode Add ou si quelqu’un a appelé la méthode FindById.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-464">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="1f5a9-465">Nous verrons plus tard comment nous pouvons utiliser cette fonctionnalité d’enregistrement « boîte noire » dans les tests.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-465">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="1f5a9-466">La deuxième fonctionnalité intéressante est la façon dont nous pouvons utiliser MOQ pour programmer un objet fictif avec des *attentes*.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-466">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="1f5a9-467">Une attente indique à l’objet factice comment répondre à une interaction donnée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-467">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="1f5a9-468">Par exemple, nous pouvons programmer une attente dans notre simulacre et lui demander de retourner un objet employé lorsqu’un utilisateur appelle FindById.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-468">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="1f5a9-469">L’infrastructure MOQ utilise une API d’installation et des expressions lambda pour programmer ces attentes.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-469">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

``` csharp
    [TestMethod]
    public void MockSample() {
        Mock<IRepository<Employee>> mock =
            new Mock<IRepository<Employee>>();
        mock.Setup(m => m.FindById(5))
            .Returns(new Employee {Id = 5});
        IRepository<Employee> repository = mock.Object;
        var employee = repository.FindById(5);
        Assert.IsTrue(employee.Id == 5);
    }
```

<span data-ttu-id="1f5a9-470">Dans cet exemple, nous demandons à MOQ de créer dynamiquement un référentiel, puis de programmer le dépôt dans une attente.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-470">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="1f5a9-471">L’attente indique à l’objet factice de retourner un nouvel objet Employee avec une valeur d’ID de 5 lorsqu’un utilisateur appelle la méthode FindById en passant une valeur de 5.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-471">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="1f5a9-472">Ce test réussit, et nous n’avons pas besoin de créer une implémentation complète de factice IRepository &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-472">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="1f5a9-473">Nous allons revoir les tests que nous avons écrits précédemment et les réutiliser pour utiliser des simulacres au lieu de simulations.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-473">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="1f5a9-474">Comme précédemment, nous utilisons une classe de base pour configurer les éléments d’infrastructure courants dont nous avons besoin pour tous les tests du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-474">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .AsQueryable();
            _repository = new Mock<IRepository<Employee>>();
            _unitOfWork = new Mock<IUnitOfWork>();
            _unitOfWork.Setup(u => u.Employees)
                       .Returns(_repository.Object);
            _controller = new EmployeeController(_unitOfWork.Object);
        }

        protected IQueryable<Employee> _employeeData;
        protected Mock<IUnitOfWork> _unitOfWork;
        protected EmployeeController _controller;
        protected Mock<IRepository<Employee>> _repository;
    }
```

<span data-ttu-id="1f5a9-475">Le code d’installation reste quasiment le même.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-475">The setup code remains mostly the same.</span></span> <span data-ttu-id="1f5a9-476">Au lieu d’utiliser des substituts, nous allons utiliser MOQ pour construire des objets factices.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-476">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="1f5a9-477">La classe de base fait en sorte que l’unité factice de travail retourne un référentiel fictif lorsque le code appelle la propriété Employees.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-477">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="1f5a9-478">Le reste de l’installation factice aura lieu à l’intérieur des contextes de test dédiés à chaque scénario spécifique.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-478">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="1f5a9-479">Par exemple, le contexte de test de l’action d’index configurera le référentiel fictif pour retourner une liste d’employés quand l’action appelle la méthode FindAll du référentiel fictif.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-479">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        public EmployeeControllerIndexActionTests() {
            _repository.Setup(r => r.FindAll())
                        .Returns(_employeeData);
        }
        // .. tests
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count());
        }
        // .. and more tests
    }
```

<span data-ttu-id="1f5a9-480">À l’exception des attentes, nos tests sont similaires aux tests que nous avions auparavant.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-480">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="1f5a9-481">Toutefois, avec la capacité d’enregistrement d’un Framework fictif, nous pouvons aborder les tests à partir d’un angle différent.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-481">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="1f5a9-482">Nous allons examiner cette nouvelle perspective dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-482">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="1f5a9-483">État et tests d’interaction</span><span class="sxs-lookup"><span data-stu-id="1f5a9-483">State versus Interaction Testing</span></span>

<span data-ttu-id="1f5a9-484">Vous pouvez utiliser différentes techniques pour tester des logiciels avec des objets fictifs.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-484">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="1f5a9-485">Une approche consiste à utiliser des tests basés sur l’État, ce que nous avons fait dans ce document jusqu’à présent.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-485">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="1f5a9-486">Le test basé sur les États fait des assertions sur l’état du logiciel.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-486">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="1f5a9-487">Dans le dernier test, nous avons appelé une méthode d’action sur le contrôleur et effectué une assertion sur le modèle à générer.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-487">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="1f5a9-488">Voici d’autres exemples d’état de test :</span><span class="sxs-lookup"><span data-stu-id="1f5a9-488">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="1f5a9-489">Vérifiez que le référentiel contient le nouvel objet d’employé après l’exécution de Create.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-489">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="1f5a9-490">Vérifiez que le modèle contient une liste de tous les employés après l’exécution de l’index.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-490">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="1f5a9-491">Vérifiez que le dépôt ne contient pas d’employé donné après l’exécution de la suppression.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-491">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="1f5a9-492">Une autre approche que vous verrez avec les objets factices consiste à vérifier les *interactions*.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-492">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="1f5a9-493">Bien que le test basé sur l’État fasse des assertions sur l’état des objets, le test basé sur l’interaction fait des assertions sur la manière dont les objets interagissent.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-493">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="1f5a9-494">Exemple :</span><span class="sxs-lookup"><span data-stu-id="1f5a9-494">For example:</span></span>

-   <span data-ttu-id="1f5a9-495">Vérifiez que le contrôleur appelle la méthode Add du référentiel lorsque Create s’exécute.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-495">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="1f5a9-496">Vérifiez que le contrôleur appelle la méthode FindAll du référentiel lorsque l’index s’exécute.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-496">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="1f5a9-497">Vérifiez que le contrôleur appelle la méthode Commit de l’unité de travail pour enregistrer les modifications lorsque la modification est exécutée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-497">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="1f5a9-498">Le test d’interaction requiert souvent moins de données de test, car il n’est pas possible de les percer à l’intérieur des collections et de vérifier les nombres.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-498">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="1f5a9-499">Par exemple, si nous savons que l’action Details appelle la méthode FindById d’un référentiel avec la valeur correcte, l’action se comporte probablement correctement.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-499">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="1f5a9-500">Nous pouvons vérifier ce comportement sans configurer les données de test à retourner à partir de FindById.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-500">We can verify this behavior without setting up any test data to return from FindById.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerDetailsActionTests
               : EmployeeControllerTestBase {
         // ...
        [TestMethod]
        public void ShouldInvokeRepositoryToFindEmployee() {
            var result = _controller.Details(_detailsId);
            _repository.Verify(r => r.FindById(_detailsId));
        }
        int _detailsId = 1;
    }
```

<span data-ttu-id="1f5a9-501">La seule configuration requise dans le contexte de test ci-dessus est celle fournie par la classe de base.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-501">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="1f5a9-502">Lorsque nous invoquons l’action du contrôleur, MOQ enregistre les interactions avec le référentiel fictif.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-502">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="1f5a9-503">À l’aide de l’API Verify de MOQ, nous pouvons demander à MOQ si le contrôleur a appelé FindById avec la valeur d’ID appropriée.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-503">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="1f5a9-504">Si le contrôleur n’a pas appelé la méthode ou a appelé la méthode avec une valeur de paramètre inattendue, la méthode Verify lèvera une exception et le test échouera.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-504">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="1f5a9-505">Voici un autre exemple pour vérifier que l’action Create appelle commit sur l’unité de travail actuelle.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-505">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="1f5a9-506">L’un des risques avec les tests d’interaction est la tendance à définir des interactions.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-506">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="1f5a9-507">La capacité de l’objet factice à enregistrer et à vérifier chaque interaction avec l’objet factice ne signifie pas que le test doit essayer de vérifier chaque interaction.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-507">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="1f5a9-508">Certaines interactions sont des détails d’implémentation et vous devez uniquement vérifier les interactions *requises* pour répondre au test actuel.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-508">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="1f5a9-509">Le choix entre les simulacres ou les substituts dépend en grande partie du système que vous testez et de vos préférences personnelles (ou de votre équipe).</span><span class="sxs-lookup"><span data-stu-id="1f5a9-509">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="1f5a9-510">Les objets factices peuvent réduire considérablement la quantité de code dont vous avez besoin pour implémenter les doubles de test, mais il n’est pas tout à fait facile de programmer les attentes en matière de programmation et de vérification des interactions.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-510">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="1f5a9-511">Conclusions</span><span class="sxs-lookup"><span data-stu-id="1f5a9-511">Conclusions</span></span>

<span data-ttu-id="1f5a9-512">Dans ce document, nous avons présenté plusieurs approches pour créer du code testable tout en utilisant le Entity Framework ADO.NET pour la persistance des données.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-512">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="1f5a9-513">Nous pouvons tirer parti des abstractions intégrées telles que IObjectSet &lt; t &gt; , ou créer vos propres abstractions comme IRepository &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-513">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="1f5a9-514">Dans les deux cas, la prise en charge de POCO dans le ADO.NET Entity Framework 4,0 permet aux consommateurs de ces abstractions de rester persistants et d’être facilement testables.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-514">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="1f5a9-515">Des fonctionnalités EF4 supplémentaires telles que le chargement différé implicite permettent au code de service d’application et d’entreprise de fonctionner sans se soucier des détails d’une banque de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-515">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="1f5a9-516">Enfin, les abstractions que nous créons sont faciles à imiter ou factices dans les tests unitaires, et nous pouvons utiliser ces doubles de test pour exécuter des tests rapides, très isolés et fiables.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-516">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="1f5a9-517">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1f5a9-517">Additional Resources</span></span>

-   <span data-ttu-id="1f5a9-518">Robert C. Martin, « [le principe de responsabilité unique](https://www.objectmentor.com/resources/articles/srp.pdf)»</span><span class="sxs-lookup"><span data-stu-id="1f5a9-518">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="1f5a9-519">Martin Fowler, [catalogue des modèles](https://www.martinfowler.com/eaaCatalog/index.html) de l' *architecture des applications d’entreprise*</span><span class="sxs-lookup"><span data-stu-id="1f5a9-519">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="1f5a9-520">Griffin Caprio, " [injection de dépendances](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="1f5a9-520">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="1f5a9-521">Blog sur la programmabilité des données, « [procédure pas à pas : développement piloté par les tests avec le Entity Framework 4,0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)».</span><span class="sxs-lookup"><span data-stu-id="1f5a9-521">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="1f5a9-522">Blog sur la programmabilité des données, « [utilisation du référentiel et des modèles d’unité de travail avec Entity Framework 4,0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)»</span><span class="sxs-lookup"><span data-stu-id="1f5a9-522">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="1f5a9-523">Aaron Jensen, « [Présentation des spécifications d’ordinateur](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)»</span><span class="sxs-lookup"><span data-stu-id="1f5a9-523">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="1f5a9-524">Eric Lee, « [BDD avec MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)»</span><span class="sxs-lookup"><span data-stu-id="1f5a9-524">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="1f5a9-525">Eric Evans, « [conception pilotée par domaine](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)»</span><span class="sxs-lookup"><span data-stu-id="1f5a9-525">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="1f5a9-526">Martin Fowler, « les [simulacres ne sont pas des stubs](https://martinfowler.com/articles/mocksArentStubs.html)»</span><span class="sxs-lookup"><span data-stu-id="1f5a9-526">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="1f5a9-527">Martin Fowler, « [test double](https://martinfowler.com/bliki/TestDouble.html)»</span><span class="sxs-lookup"><span data-stu-id="1f5a9-527">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="1f5a9-528">MOQ</span><span class="sxs-lookup"><span data-stu-id="1f5a9-528">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="1f5a9-529">Biographie</span><span class="sxs-lookup"><span data-stu-id="1f5a9-529">Biography</span></span>

<span data-ttu-id="1f5a9-530">Scott Allen est membre du personnel technique de Pluralsight et du fondateur de OdeToCode.com.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-530">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="1f5a9-531">Dans 15 ans de développement logiciel commercial, Scott a travaillé sur des solutions pour tous les éléments, des appareils embarqués 8 bits aux applications Web ASP.NET hautement évolutives.</span><span class="sxs-lookup"><span data-stu-id="1f5a9-531">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="1f5a9-532">Vous pouvez contacter Scott sur son blog sur OdeToCode ou sur Twitter à l’adresse [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode) .</span><span class="sxs-lookup"><span data-stu-id="1f5a9-532">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
