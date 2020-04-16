---
title: Testabilité et cadre d’entité 4.0 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 96b6b2791f12b7d60a233f7e6dc77e5a8579fb66
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434311"
---
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="49b03-102">Testabilité et cadre d’entité 4.0</span><span class="sxs-lookup"><span data-stu-id="49b03-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="49b03-103">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="49b03-103">Scott Allen</span></span>

<span data-ttu-id="49b03-104">Date de publication : mai 2010</span><span class="sxs-lookup"><span data-stu-id="49b03-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="49b03-105">Introduction</span><span class="sxs-lookup"><span data-stu-id="49b03-105">Introduction</span></span>

<span data-ttu-id="49b03-106">Ce livre blanc décrit et démontre comment écrire du code testable avec le cadre d’entité ADO.NET 4.0 et Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="49b03-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="49b03-107">Ce document n’essaie pas de se concentrer sur une méthodologie de test spécifique, comme la conception axée sur les tests (TDD) ou la conception axée sur le comportement (BDD).</span><span class="sxs-lookup"><span data-stu-id="49b03-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="49b03-108">Au lieu de cela, ce document se concentrera sur la façon d’écrire du code qui utilise le cadre de l’entité ADO.NET mais reste facile à isoler et à tester de façon automatisée.</span><span class="sxs-lookup"><span data-stu-id="49b03-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="49b03-109">Nous examinerons les modèles de conception communs qui facilitent les tests dans les scénarios d’accès aux données et nous verrons comment appliquer ces modèles lors de l’utilisation du cadre.</span><span class="sxs-lookup"><span data-stu-id="49b03-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="49b03-110">Nous examinerons également les caractéristiques spécifiques du cadre pour voir comment ces fonctionnalités peuvent fonctionner dans le code testable.</span><span class="sxs-lookup"><span data-stu-id="49b03-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="49b03-111">Qu’est-ce que le Code testable?</span><span class="sxs-lookup"><span data-stu-id="49b03-111">What Is Testable Code?</span></span>

<span data-ttu-id="49b03-112">La possibilité de vérifier un logiciel à l’aide de tests unitaires automatisés offre de nombreux avantages souhaitables.</span><span class="sxs-lookup"><span data-stu-id="49b03-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="49b03-113">Tout le monde sait que de bons tests permettra de réduire le nombre de défauts logiciels dans une application et d’augmenter la qualité de l’application - mais avoir des tests unitaires en place va bien au-delà de la simple recherche de bogues.</span><span class="sxs-lookup"><span data-stu-id="49b03-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="49b03-114">Une bonne suite de test unitaire permet à une équipe de développement de gagner du temps et de garder le contrôle du logiciel qu’elle crée.</span><span class="sxs-lookup"><span data-stu-id="49b03-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="49b03-115">Une équipe peut apporter des modifications au code existant, refactor, refonte et logiciel de restructuration pour répondre à de nouvelles exigences, et ajouter de nouveaux composants dans une application tout en sachant que la suite de test peut vérifier le comportement de l’application.</span><span class="sxs-lookup"><span data-stu-id="49b03-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="49b03-116">Les tests unitaires font partie d’un cycle de rétroaction rapide pour faciliter le changement et préserver la maintenance du logiciel à mesure que la complexité augmente.</span><span class="sxs-lookup"><span data-stu-id="49b03-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="49b03-117">Les tests unitaires ont toutefois un prix.</span><span class="sxs-lookup"><span data-stu-id="49b03-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="49b03-118">Une équipe doit investir le temps de créer et de maintenir des tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="49b03-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="49b03-119">La quantité d’efforts nécessaires pour créer ces tests est directement liée à la **testabilité** du logiciel sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="49b03-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="49b03-120">Dans quelle mesure le logiciel est-il facile à tester ?</span><span class="sxs-lookup"><span data-stu-id="49b03-120">How easy is the software to test?</span></span> <span data-ttu-id="49b03-121">Une équipe de conception de logiciels avec la testabilité à l’esprit créera des tests efficaces plus rapidement que l’équipe travaillant avec un logiciel non testable.</span><span class="sxs-lookup"><span data-stu-id="49b03-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="49b03-122">Microsoft a conçu le cadre d’entité ADO.NET 4.0 (EF4) avec la testabilité à l’esprit.</span><span class="sxs-lookup"><span data-stu-id="49b03-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="49b03-123">Cela ne signifie pas que les développeurs écrira des tests unitaires contre le code-cadre lui-même.</span><span class="sxs-lookup"><span data-stu-id="49b03-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="49b03-124">Au lieu de cela, les objectifs de testabilité pour EF4 facilitent la création d’un code testable qui s’appuie sur le cadre.</span><span class="sxs-lookup"><span data-stu-id="49b03-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="49b03-125">Avant d’examiner des exemples spécifiques, il vaut la peine de comprendre les qualités du code testable.</span><span class="sxs-lookup"><span data-stu-id="49b03-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="49b03-126">Les qualités du code testable</span><span class="sxs-lookup"><span data-stu-id="49b03-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="49b03-127">Le code facile à tester présente toujours au moins deux traits.</span><span class="sxs-lookup"><span data-stu-id="49b03-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="49b03-128">Tout d’abord, le code testable est facile à **observer**.</span><span class="sxs-lookup"><span data-stu-id="49b03-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="49b03-129">Compte tenu de certains ensembles d’entrées, il devrait être facile d’observer la sortie du code.</span><span class="sxs-lookup"><span data-stu-id="49b03-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="49b03-130">Par exemple, le test de la méthode suivante est facile car la méthode renvoie directement le résultat d’un calcul.</span><span class="sxs-lookup"><span data-stu-id="49b03-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="49b03-131">Tester une méthode est difficile si la méthode écrit la valeur calculée dans une prise réseau, une table de base de données ou un fichier comme le code suivant.</span><span class="sxs-lookup"><span data-stu-id="49b03-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="49b03-132">Le test doit effectuer des travaux supplémentaires pour récupérer la valeur.</span><span class="sxs-lookup"><span data-stu-id="49b03-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="49b03-133">Deuxièmement, le code testable est facile à **isoler**.</span><span class="sxs-lookup"><span data-stu-id="49b03-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="49b03-134">Utilisons le pseudo-code suivant comme un mauvais exemple de code testable.</span><span class="sxs-lookup"><span data-stu-id="49b03-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="49b03-135">La méthode est facile à observer - nous pouvons passer dans une police d’assurance et de vérifier la valeur de retour correspond à un résultat attendu.</span><span class="sxs-lookup"><span data-stu-id="49b03-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="49b03-136">Cependant, pour tester la méthode, nous aurons besoin d’avoir une base de données installée avec le schéma correct, et configurer le serveur SMTP au cas où la méthode tente d’envoyer un e-mail.</span><span class="sxs-lookup"><span data-stu-id="49b03-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="49b03-137">Le test unitaire ne veut vérifier que la logique de calcul à l’intérieur de la méthode, mais le test peut échouer parce que le serveur de messagerie est hors ligne, ou parce que le serveur de base de données déplacé.</span><span class="sxs-lookup"><span data-stu-id="49b03-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="49b03-138">Ces deux échecs ne sont pas liés au comportement que le test veut vérifier.</span><span class="sxs-lookup"><span data-stu-id="49b03-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="49b03-139">Le comportement est difficile à isoler.</span><span class="sxs-lookup"><span data-stu-id="49b03-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="49b03-140">Les développeurs de logiciels qui s’efforcent d’écrire du code testable s’efforcent souvent de maintenir une séparation des préoccupations dans le code qu’ils écrivent.</span><span class="sxs-lookup"><span data-stu-id="49b03-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="49b03-141">La méthode ci-dessus devrait se concentrer sur les calculs de l’entreprise et déléguer les détails de la base de données et de la mise en œuvre par courriel à d’autres composantes.</span><span class="sxs-lookup"><span data-stu-id="49b03-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="49b03-142">Robert C. Martin appelle cela le principe de la responsabilité unique.</span><span class="sxs-lookup"><span data-stu-id="49b03-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="49b03-143">Un objet doit encapsuler une responsabilité unique et étroite, comme calculer la valeur d’une police.</span><span class="sxs-lookup"><span data-stu-id="49b03-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="49b03-144">Tous les autres travaux de base de données et de notification doivent être la responsabilité d’un autre objet.</span><span class="sxs-lookup"><span data-stu-id="49b03-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="49b03-145">Code écrit de cette façon est plus facile à isoler parce qu’il est axé sur une seule tâche.</span><span class="sxs-lookup"><span data-stu-id="49b03-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="49b03-146">En .NET, nous avons les abstractions dont nous avons besoin pour suivre le principe de responsabilité unique et parvenir à l’isolement.</span><span class="sxs-lookup"><span data-stu-id="49b03-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="49b03-147">Nous pouvons utiliser les définitions d’interface et forcer le code à utiliser l’abstraction de l’interface au lieu d’un type concret.</span><span class="sxs-lookup"><span data-stu-id="49b03-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="49b03-148">Plus tard dans ce document, nous verrons comment une méthode comme le mauvais exemple présenté ci-dessus peut fonctionner avec des interfaces qui *ressemblent* à ils vont parler à la base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="49b03-149">Au moment du test, cependant, nous pouvons substituer une implémentation factice qui ne parle pas à la base de données, mais tient plutôt des données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="49b03-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="49b03-150">Cette implémentation factice isolera le code des problèmes non liés dans le code d’accès aux données ou la configuration de base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="49b03-151">L’isolement a d’autres avantages.</span><span class="sxs-lookup"><span data-stu-id="49b03-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="49b03-152">Le calcul de l’entreprise dans la dernière méthode ne devrait prendre que quelques millisecondes pour exécuter, mais le test lui-même pourrait s’exécuter pendant plusieurs secondes que le code saute autour du réseau et parle à divers serveurs.</span><span class="sxs-lookup"><span data-stu-id="49b03-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="49b03-153">Les tests unitaires doivent être effectués rapidement pour faciliter les petits changements.</span><span class="sxs-lookup"><span data-stu-id="49b03-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="49b03-154">Les tests unitaires doivent également être reproductibles et ne pas échouer parce qu’un composant sans rapport avec le test a un problème.</span><span class="sxs-lookup"><span data-stu-id="49b03-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="49b03-155">Écrire du code qui est facile à observer et à isoler signifie que les développeurs auront plus de facilité à écrire des tests pour le code, passer moins de temps à attendre que les tests s’exécutent, et surtout, passer moins de temps à traquer les bogues qui n’existent pas.</span><span class="sxs-lookup"><span data-stu-id="49b03-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="49b03-156">Espérons que vous pouvez apprécier les avantages de tester et de comprendre les qualités que le code testable montre.</span><span class="sxs-lookup"><span data-stu-id="49b03-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="49b03-157">Nous sommes sur le point d’aborder la façon d’écrire du code qui fonctionne avec EF4 pour enregistrer des données dans une base de données tout en restant observable et facile à isoler, mais d’abord nous allons réduire notre objectif pour discuter des conceptions testables pour l’accès aux données.</span><span class="sxs-lookup"><span data-stu-id="49b03-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="49b03-158">Modèles de conception pour la persistance des données</span><span class="sxs-lookup"><span data-stu-id="49b03-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="49b03-159">Les deux mauvais exemples présentés précédemment avaient trop de responsabilités.</span><span class="sxs-lookup"><span data-stu-id="49b03-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="49b03-160">Le premier mauvais exemple a dû effectuer un calcul *et* écrire à un fichier.</span><span class="sxs-lookup"><span data-stu-id="49b03-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="49b03-161">Le deuxième mauvais exemple a dû lire les données d’une base de données *et* effectuer un calcul d’entreprise *et* envoyer des courriels.</span><span class="sxs-lookup"><span data-stu-id="49b03-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="49b03-162">En concevant des méthodes plus petites qui séparent les préoccupations et déléguent la responsabilité à d’autres composants, vous ferez de grands progrès vers l’écriture de code testable.</span><span class="sxs-lookup"><span data-stu-id="49b03-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="49b03-163">L’objectif est de construire la fonctionnalité en composant des actions à partir d’abstractions petites et ciblées.</span><span class="sxs-lookup"><span data-stu-id="49b03-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="49b03-164">Quand il s’agit de la persistance des données les petites abstractions ciblées que nous recherchons sont si communs qu’ils ont été documentés comme modèles de conception.</span><span class="sxs-lookup"><span data-stu-id="49b03-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="49b03-165">Le livre de Martin Fowler Patterns of Enterprise Application Architecture a été la première œuvre à décrire ces modèles imprimés.</span><span class="sxs-lookup"><span data-stu-id="49b03-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="49b03-166">Nous fournirons une brève description de ces modèles dans les sections suivantes avant de montrer comment ces ADO.NET Cadre d’entité met en œuvre et fonctionne avec ces modèles.</span><span class="sxs-lookup"><span data-stu-id="49b03-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="49b03-167">The Repository Pattern</span><span class="sxs-lookup"><span data-stu-id="49b03-167">The Repository Pattern</span></span>

<span data-ttu-id="49b03-168">Selon M. Fowler, un référentiel « se médiate entre les couches de cartographie du domaine et de données à l’aide d’une interface de type collection pour accéder aux objets de domaine ».</span><span class="sxs-lookup"><span data-stu-id="49b03-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="49b03-169">Le but du modèle de dépôt est d’isoler le code des détails de l’accès aux données, et comme nous l’avons vu l’isolement plus tôt est un trait requis pour la testabilité.</span><span class="sxs-lookup"><span data-stu-id="49b03-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="49b03-170">La clé de l’isolement est la façon dont le référentiel expose les objets à l’aide d’une interface de collection.</span><span class="sxs-lookup"><span data-stu-id="49b03-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="49b03-171">La logique que vous écrivez pour utiliser le référentiel n’a aucune idée de la façon dont le référentiel matérialisera les objets que vous demandez.</span><span class="sxs-lookup"><span data-stu-id="49b03-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="49b03-172">Le référentiel peut parler à une base de données, ou il pourrait simplement retourner des objets d’une collection en mémoire.</span><span class="sxs-lookup"><span data-stu-id="49b03-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="49b03-173">Tout ce que votre code doit savoir, c’est que le référentiel semble maintenir la collection, et vous pouvez récupérer, ajouter et supprimer des objets de la collection.</span><span class="sxs-lookup"><span data-stu-id="49b03-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="49b03-174">Dans les applications .NET existantes, un référentiel en béton hérite souvent d’une interface générique comme suit :</span><span class="sxs-lookup"><span data-stu-id="49b03-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="49b03-175">Nous apporterons quelques modifications à la définition de l’interface lorsque nous fournirons une implémentation pour EF4, mais le concept de base reste le même.</span><span class="sxs-lookup"><span data-stu-id="49b03-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="49b03-176">Code peut utiliser un référentiel concret implémentant cette interface pour récupérer une entité par sa valeur clé principale, pour récupérer une collection d’entités basées sur l’évaluation d’un prédicat, ou tout simplement récupérer toutes les entités disponibles.</span><span class="sxs-lookup"><span data-stu-id="49b03-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="49b03-177">Le code peut également ajouter et supprimer des entités via l’interface de dépôt.</span><span class="sxs-lookup"><span data-stu-id="49b03-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="49b03-178">Compte tenu d’un IRepository d’objets employés, le code peut effectuer les opérations suivantes.</span><span class="sxs-lookup"><span data-stu-id="49b03-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="49b03-179">Étant donné que le code utilise une interface (IRepository of Employee), nous pouvons fournir au code différentes implémentations de l’interface.</span><span class="sxs-lookup"><span data-stu-id="49b03-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="49b03-180">Une mise en œuvre peut être une implémentation soutenue par EF4 et des objets persistants dans une base de données Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="49b03-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="49b03-181">Une mise en œuvre différente (que nous utilisons pendant les tests) peut être soutenue par une liste mémoire des objets des employés.</span><span class="sxs-lookup"><span data-stu-id="49b03-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="49b03-182">L’interface aidera à atteindre l’isolement dans le code.</span><span class="sxs-lookup"><span data-stu-id="49b03-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="49b03-183">Remarquez que l’interface IRepository&lt;T&gt; n’expose pas une opération Save.</span><span class="sxs-lookup"><span data-stu-id="49b03-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="49b03-184">Comment mettre à jour les objets existants ?</span><span class="sxs-lookup"><span data-stu-id="49b03-184">How do we update existing objects?</span></span> <span data-ttu-id="49b03-185">Vous pourriez rencontrer des définitions IRepository qui incluent l’opération Save, et les implémentations de ces dépôts devront immédiatement persister un objet dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="49b03-186">Cependant, dans de nombreuses applications, nous ne voulons pas persister des objets individuellement.</span><span class="sxs-lookup"><span data-stu-id="49b03-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="49b03-187">Au lieu de cela, nous voulons donner vie à des objets, peut-être à partir de différents dépôts, modifier ces objets dans le cadre d’une activité commerciale, puis persister tous les objets dans le cadre d’une seule opération atomique.</span><span class="sxs-lookup"><span data-stu-id="49b03-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="49b03-188">Heureusement, il existe un modèle pour permettre ce type de comportement.</span><span class="sxs-lookup"><span data-stu-id="49b03-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="49b03-189">L’unité de modèle de travail</span><span class="sxs-lookup"><span data-stu-id="49b03-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="49b03-190">M. Fowler affirme qu’une unité de travail « maintiendra une liste d’objets touchés par une transaction commerciale et coordonnera l’écriture à partir de modifications et la résolution des problèmes de concurrence ».</span><span class="sxs-lookup"><span data-stu-id="49b03-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="49b03-191">Il incombe à l’unité de travail de suivre les changements apportés aux objets que nous apportons à la vie à partir d’un référentiel et de persister tous les changements que nous avons apportés aux objets lorsque nous disons à l’unité de travail d’engager les changements.</span><span class="sxs-lookup"><span data-stu-id="49b03-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="49b03-192">Il est également de la responsabilité de l’unité de travail de prendre les nouveaux objets que nous avons ajoutés à tous les dépôts et d’insérer les objets dans une base de données, ainsi que la suppression des manges.</span><span class="sxs-lookup"><span data-stu-id="49b03-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="49b03-193">Si vous avez déjà travaillé avec ADO.NET DataSets, vous serez déjà familier avec l’unité de modèle de travail.</span><span class="sxs-lookup"><span data-stu-id="49b03-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="49b03-194">ADO.NET DataSets avait la capacité de suivre nos mises à jour, suppressions et insertion d’objets DataRow et pouvait (avec l’aide d’un TableAdapter) concilier toutes nos modifications à une base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="49b03-195">Toutefois, les objets DataSet modéliser un sous-ensemble déconnecté de la base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="49b03-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="49b03-196">L’unité de modèle de travail présente le même comportement, mais fonctionne avec des objets d’affaires et des objets de domaine qui sont isolés du code d’accès aux données et ignorants de la base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="49b03-197">Une abstraction pour modéliser l’unité de travail en code .NET peut ressembler à ce qui suit:</span><span class="sxs-lookup"><span data-stu-id="49b03-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="49b03-198">En exposant les références de dépôt de l’unité de travail, nous pouvons nous assurer qu’une seule unité d’objet de travail a la capacité de suivre toutes les entités matérialisées au cours d’une transaction commerciale.</span><span class="sxs-lookup"><span data-stu-id="49b03-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="49b03-199">La mise en œuvre de la méthode Commit pour une véritable unité de travail est l’endroit où toute la magie se trouve à concilier les changements de mémoire avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="49b03-200">Compte tenu d’une référence IUnitOfWork, le code peut apporter des modifications aux objets d’affaires récupérés à partir d’un ou plusieurs dépôts et enregistrer toutes les modifications à l’aide de l’opération Atomic Commit.</span><span class="sxs-lookup"><span data-stu-id="49b03-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="49b03-201">Le modèle de charge paresseux</span><span class="sxs-lookup"><span data-stu-id="49b03-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="49b03-202">Fowler utilise le nom de charge paresseuse pour décrire "un objet qui ne contient pas toutes les données dont vous avez besoin, mais sait comment l’obtenir".</span><span class="sxs-lookup"><span data-stu-id="49b03-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="49b03-203">Le chargement paresseux transparent est une caractéristique importante à avoir lors de la rédaction de code d’affaires testable et en travaillant avec une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="49b03-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="49b03-204">À titre d’exemple, considérez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="49b03-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="49b03-205">Comment la collection TimeCards est-elle peuplée?</span><span class="sxs-lookup"><span data-stu-id="49b03-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="49b03-206">Il y a deux réponses possibles.</span><span class="sxs-lookup"><span data-stu-id="49b03-206">There are two possible answers.</span></span> <span data-ttu-id="49b03-207">Une réponse est que le dépôt de l’employé, lorsqu’on lui demande d’aller chercher un employé, émet une requête pour récupérer à la fois l’employé ainsi que les renseignements connexes de la carte de temps de l’employé.</span><span class="sxs-lookup"><span data-stu-id="49b03-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="49b03-208">Dans les bases de données relationnelles, cela nécessite généralement une requête avec une clause JOIN et peut entraîner la récupération de plus d’informations qu’un besoin d’application.</span><span class="sxs-lookup"><span data-stu-id="49b03-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="49b03-209">Que se passe-t-il si l’application n’a jamais besoin de toucher la propriété TimeCards ?</span><span class="sxs-lookup"><span data-stu-id="49b03-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="49b03-210">Une deuxième réponse est de charger la propriété TimeCards "sur demande".</span><span class="sxs-lookup"><span data-stu-id="49b03-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="49b03-211">Ce chargement paresseux est implicite et transparent à la logique métier parce que le code n’invoque pas des API spéciales pour récupérer les informations de carte de temps.</span><span class="sxs-lookup"><span data-stu-id="49b03-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="49b03-212">Le code suppose que les informations de carte de temps sont présentes en cas de besoin.</span><span class="sxs-lookup"><span data-stu-id="49b03-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="49b03-213">Il ya une certaine magie impliquée dans le chargement paresseux qui implique généralement l’interception de la méthode d’invocations.</span><span class="sxs-lookup"><span data-stu-id="49b03-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="49b03-214">Le code d’interception est responsable de parler à la base de données et de récupérer des informations de carte de temps tout en laissant la logique d’entreprise libre d’être logique d’affaires.</span><span class="sxs-lookup"><span data-stu-id="49b03-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="49b03-215">Cette magie de charge paresseuse permet au code d’entreprise de s’isoler des opérations de récupération de données et se traduit par plus de code testable.</span><span class="sxs-lookup"><span data-stu-id="49b03-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="49b03-216">L’inconvénient d’une charge paresseuse est que lorsqu’une application *a* besoin des informations de carte de temps, le code exécutera une requête supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="49b03-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="49b03-217">Ce n’est pas une préoccupation pour de nombreuses applications, mais pour les applications sensibles aux performances ou des applications en boucle à travers un certain nombre d’objets des employés et l’exécution d’une requête pour récupérer les cartes de temps au cours de chaque itération de la boucle (un problème souvent appelé le problème de requête N '1), le chargement paresseux est une traînée.</span><span class="sxs-lookup"><span data-stu-id="49b03-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="49b03-218">Dans ces scénarios, une application peut vouloir charger avec impatience les informations de carte de temps de la manière la plus efficace possible.</span><span class="sxs-lookup"><span data-stu-id="49b03-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="49b03-219">Heureusement, nous verrons comment EF4 prend en charge à la fois les charges paresseuses implicites et les charges enthousiastes efficaces que nous nous déplaçons dans la section suivante et mettre en œuvre ces modèles.</span><span class="sxs-lookup"><span data-stu-id="49b03-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="49b03-220">Mise en œuvre des modèles avec le Cadre d’entité</span><span class="sxs-lookup"><span data-stu-id="49b03-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="49b03-221">Les bonnes nouvelles sont que tous les modèles de conception que nous avons décrit dans la dernière section sont simples à mettre en œuvre avec EF4.</span><span class="sxs-lookup"><span data-stu-id="49b03-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="49b03-222">Pour démontrer que nous allons utiliser une application MVC simple ASP.NET pour modifier et afficher les employés et les informations de leur carte de temps.</span><span class="sxs-lookup"><span data-stu-id="49b03-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="49b03-223">Nous allons commencer par utiliser les « objets CLR anciens » suivants (POCOs).</span><span class="sxs-lookup"><span data-stu-id="49b03-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="49b03-224">Ces définitions de classe changeront légèrement au fur et à mesure que nous explorerons différentes approches et caractéristiques d’EF4, mais l’intention est de garder ces classes aussi ignorantes de persistance (PI) que possible.</span><span class="sxs-lookup"><span data-stu-id="49b03-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="49b03-225">Un objet PI ne sait pas *comment,* ou même *si,* l’état qu’il détient vit à l’intérieur d’une base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="49b03-226">LES PI et poCOs vont de pair avec les logiciels testables.</span><span class="sxs-lookup"><span data-stu-id="49b03-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="49b03-227">Les objets utilisant une approche POCO sont moins contraints, plus flexibles et plus faciles à tester parce qu’ils peuvent fonctionner sans une base de données présente.</span><span class="sxs-lookup"><span data-stu-id="49b03-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="49b03-228">Avec les POCO en place, nous pouvons créer un modèle de données d’entité (EDM) dans Visual Studio (voir figure 1).</span><span class="sxs-lookup"><span data-stu-id="49b03-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="49b03-229">Nous n’utiliserons pas l’EDM pour générer du code pour nos entités.</span><span class="sxs-lookup"><span data-stu-id="49b03-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="49b03-230">Au lieu de cela, nous voulons utiliser les entités que nous fabriquons avec amour à la main.</span><span class="sxs-lookup"><span data-stu-id="49b03-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="49b03-231">Nous n’utiliserons l’EDM que pour générer notre schéma de base de données et fournir les métadonnées EF4 pour cartographier les objets dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="49b03-233">**La figure 1**</span><span class="sxs-lookup"><span data-stu-id="49b03-233">**Figure 1**</span></span>

<span data-ttu-id="49b03-234">Remarque : si vous souhaitez d’abord développer le modèle EDM, il est possible de générer du code POCO propre à partir de l’EDM.</span><span class="sxs-lookup"><span data-stu-id="49b03-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="49b03-235">Vous pouvez le faire avec une extension Visual Studio 2010 fournie par l’équipe De programmabilité des données.</span><span class="sxs-lookup"><span data-stu-id="49b03-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="49b03-236">Pour télécharger l’extension, lancez le gestionnaire d’extension à partir du menu Tools dans Visual Studio et recherchez la galerie en ligne de modèles pour "POCO" (Voir la figure 2).</span><span class="sxs-lookup"><span data-stu-id="49b03-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="49b03-237">Il existe plusieurs modèles POCO disponibles pour EF.</span><span class="sxs-lookup"><span data-stu-id="49b03-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="49b03-238">Pour plus d’informations sur l’utilisation du modèle, voir « [Procédure pas à pas : modèle DE l’OCO pour le cadre d’entité](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)».</span><span class="sxs-lookup"><span data-stu-id="49b03-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![ef test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="49b03-240">**Figure 2**</span><span class="sxs-lookup"><span data-stu-id="49b03-240">**Figure 2**</span></span>

<span data-ttu-id="49b03-241">À partir de ce point de départ du BCO, nous explorerons deux approches différentes du code testable.</span><span class="sxs-lookup"><span data-stu-id="49b03-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="49b03-242">La première approche que j’appelle l’approche EF parce qu’elle tire parti des abstractions de l’API cadre d’entité pour mettre en œuvre des unités de travail et de dépôts.</span><span class="sxs-lookup"><span data-stu-id="49b03-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="49b03-243">Dans la deuxième approche, nous allons créer nos propres abstractions de dépôt personnalisé et ensuite voir les avantages et les inconvénients de chaque approche.</span><span class="sxs-lookup"><span data-stu-id="49b03-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="49b03-244">Nous allons commencer par explorer l’approche EF.</span><span class="sxs-lookup"><span data-stu-id="49b03-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="49b03-245">Une mise en œuvre EF Centric</span><span class="sxs-lookup"><span data-stu-id="49b03-245">An EF Centric Implementation</span></span>

<span data-ttu-id="49b03-246">Considérez l’action suivante du contrôleur d’un projet de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="49b03-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="49b03-247">L’action récupère un objet de l’employé et renvoie un résultat pour afficher une vue détaillée de l’employé.</span><span class="sxs-lookup"><span data-stu-id="49b03-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="49b03-248">Le code est-il testable ?</span><span class="sxs-lookup"><span data-stu-id="49b03-248">Is the code testable?</span></span> <span data-ttu-id="49b03-249">Il y a au moins deux tests dont nous aurions besoin pour vérifier le comportement de l’action.</span><span class="sxs-lookup"><span data-stu-id="49b03-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="49b03-250">Tout d’abord, nous aimerions vérifier que l’action renvoie la vue correcte - un test facile.</span><span class="sxs-lookup"><span data-stu-id="49b03-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="49b03-251">Nous aimerions également passer un test pour vérifier que l’action récupère l’employé correct, et nous aimerions le faire sans exécuter de code pour interroger la base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="49b03-252">Rappelez-vous que nous voulons isoler le code à l’essai.</span><span class="sxs-lookup"><span data-stu-id="49b03-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="49b03-253">L’isolement garantira que le test ne manque pas en raison d’un bogue dans le code d’accès aux données ou la configuration de base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="49b03-254">Si le test échoue, nous saurons que nous avons un bogue dans la logique du contrôleur, et non dans un composant système de niveau inférieur.</span><span class="sxs-lookup"><span data-stu-id="49b03-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="49b03-255">Pour parvenir à l’isolement, nous aurons besoin de quelques abstractions comme les interfaces que nous avons présentées plus tôt pour les dépôts et les unités de travail.</span><span class="sxs-lookup"><span data-stu-id="49b03-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="49b03-256">N’oubliez pas que le modèle de référentiel est conçu pour être médiateur entre les objets de domaine et la couche de cartographie des données.</span><span class="sxs-lookup"><span data-stu-id="49b03-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="49b03-257">Dans ce scénario EF4 *est* la couche de cartographie des données, et&lt;fournit&gt; déjà une abstraction de type référentiel nommé IObjectSet T (à partir de l’espace de nom System.Data.Objects).</span><span class="sxs-lookup"><span data-stu-id="49b03-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="49b03-258">La définition de l’interface ressemble à ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="49b03-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="49b03-259">IObjectSet&lt;&gt; T répond aux exigences d’un référentiel car il ressemble à&lt;&gt;une collection d’objets (via IEnumerable T ) et fournit des méthodes pour ajouter et supprimer des objets de la collection simulée.</span><span class="sxs-lookup"><span data-stu-id="49b03-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="49b03-260">Les méthodes Attach et Detach exposent les capacités supplémentaires de l’API EF4.</span><span class="sxs-lookup"><span data-stu-id="49b03-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="49b03-261">Pour utiliser IObjectSet&lt;T&gt; comme interface pour les dépôts, nous avons besoin d’une unité d’abstraction de travail pour lier les dépôts ensemble.</span><span class="sxs-lookup"><span data-stu-id="49b03-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="49b03-262">Une implémentation concrète de cette interface parlera à SQL Server et est facile à créer en utilisant la classe ObjectContext à partir d’EF4.</span><span class="sxs-lookup"><span data-stu-id="49b03-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="49b03-263">La classe ObjectContext est la véritable unité de travail de l’API EF4.</span><span class="sxs-lookup"><span data-stu-id="49b03-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="49b03-264">Donner vie&lt;&gt; à un T IObjectSet est aussi facile que d’invoquer la méthode CreateObjectSet de l’objet ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="49b03-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="49b03-265">Dans les coulisses, le cadre utilisera les métadonnées que nous&lt;&gt;avons fournies dans l’EDM pour produire un objet en bétonSet T .</span><span class="sxs-lookup"><span data-stu-id="49b03-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="49b03-266">Nous nous en tiendrons au&lt;&gt; retour de l’interface IObjectSet T car elle aidera à préserver la testabilité du code client.</span><span class="sxs-lookup"><span data-stu-id="49b03-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="49b03-267">Cette mise en œuvre concrète est utile dans la production, mais nous devons nous concentrer sur la façon dont nous allons utiliser notre abstraction IUnitOfWork pour faciliter les tests.</span><span class="sxs-lookup"><span data-stu-id="49b03-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="49b03-268">Le test double</span><span class="sxs-lookup"><span data-stu-id="49b03-268">The Test Doubles</span></span>

<span data-ttu-id="49b03-269">Pour isoler l’action du contrôleur, nous aurons besoin de la possibilité de basculer entre l’unité réelle de travail (soutenue par un objectContexte) et une unité de travail double ou « faux » de test (opérations de mise en mémoire).</span><span class="sxs-lookup"><span data-stu-id="49b03-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="49b03-270">L’approche commune pour effectuer ce type de commutation est de ne pas laisser le contrôleur MVC instantané une unité de travail, mais plutôt passer l’unité de travail dans le contrôleur comme un paramètre constructeur.</span><span class="sxs-lookup"><span data-stu-id="49b03-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="49b03-271">Le code ci-dessus est un exemple d’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="49b03-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="49b03-272">Nous ne permettons pas au contrôleur de créer sa dépendance (l’unité de travail) mais d’injecter la dépendance dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="49b03-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="49b03-273">Dans un projet MVC, il est courant d’utiliser une usine de contrôleurs personnalisés en combinaison avec un conteneur d’inversion de contrôle (IoC) pour automatiser l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="49b03-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="49b03-274">Ces sujets sont au-delà de la portée de cet article, mais vous pouvez en savoir plus en suivant les références à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="49b03-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="49b03-275">Une fausse unité de mise en œuvre du travail que nous pouvons utiliser pour les tests pourrait ressembler à ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="49b03-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="49b03-276">Notez que la fausse unité de travail expose une propriété commise.</span><span class="sxs-lookup"><span data-stu-id="49b03-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="49b03-277">Il est parfois utile d’ajouter des fonctionnalités à une classe de faux qui facilitent les tests.</span><span class="sxs-lookup"><span data-stu-id="49b03-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="49b03-278">Dans ce cas, il est facile d’observer si le code commet une unité de travail en vérifiant la propriété engagée.</span><span class="sxs-lookup"><span data-stu-id="49b03-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="49b03-279">Nous aurons également besoin d’un&lt;&gt; faux IObjectSet T pour contenir en mémoire les objets Employee et TimeCard.</span><span class="sxs-lookup"><span data-stu-id="49b03-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="49b03-280">Nous pouvons fournir une mise en œuvre unique à l’aide de génériques.</span><span class="sxs-lookup"><span data-stu-id="49b03-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="49b03-281">Ce test double la majeure partie de son&lt;&gt; travail à un objet HashSet T sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="49b03-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="49b03-282">&lt;Notez que IObjectSet T&gt; nécessite une contrainte générique appliquant T comme une classe (un&lt;&gt;type de référence), et nous oblige également à mettre en œuvre IQueryable T .</span><span class="sxs-lookup"><span data-stu-id="49b03-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="49b03-283">Il est facile de faire apparaître une collection en&lt;&gt; mémoire comme un T IQueryable en utilisant l’opérateur LINQ standard AsQueryable.</span><span class="sxs-lookup"><span data-stu-id="49b03-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="49b03-284">Les tests</span><span class="sxs-lookup"><span data-stu-id="49b03-284">The Tests</span></span>

<span data-ttu-id="49b03-285">Les tests unitaires traditionnels utiliseront une seule classe de test pour tenir tous les tests pour toutes les actions dans un seul contrôleur MVC.</span><span class="sxs-lookup"><span data-stu-id="49b03-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="49b03-286">Nous pouvons écrire ces tests, ou n’importe quel type de test unitaire, en utilisant les faux de mémoire que nous avons construits.</span><span class="sxs-lookup"><span data-stu-id="49b03-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="49b03-287">Cependant, pour cet article, nous éviterons l’approche monolithique de classe de test et au lieu de regrouper nos tests pour se concentrer sur un morceau spécifique de fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="49b03-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="49b03-288">Par exemple, « créer un nouvel employé » peut être la fonctionnalité que nous voulons tester, de sorte que nous allons utiliser une seule classe de test pour vérifier l’action de contrôleur unique responsable de la création d’un nouvel employé.</span><span class="sxs-lookup"><span data-stu-id="49b03-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="49b03-289">Il y a un code d’installation commun dont nous avons besoin pour toutes ces classes de test à grain fin.</span><span class="sxs-lookup"><span data-stu-id="49b03-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="49b03-290">Par exemple, nous avons toujours besoin de créer nos dépôts en mémoire et de fausses unités de travail.</span><span class="sxs-lookup"><span data-stu-id="49b03-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="49b03-291">Nous avons également besoin d’un cas du contrôleur des employés avec la fausse unité de travail injectée.</span><span class="sxs-lookup"><span data-stu-id="49b03-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="49b03-292">Nous partagerons ce code de configuration commun entre les classes de test en utilisant une classe de base.</span><span class="sxs-lookup"><span data-stu-id="49b03-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="49b03-293">La « mère objet » que nous utilisons dans la classe de base est un modèle commun pour créer des données de test.</span><span class="sxs-lookup"><span data-stu-id="49b03-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="49b03-294">Une mère objet contient des méthodes d’usine pour instantané des entités de test pour une utilisation à travers plusieurs appareils de test.</span><span class="sxs-lookup"><span data-stu-id="49b03-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="49b03-295">Nous pouvons utiliser la EmployeeControllerTestBase comme classe de base pour un certain nombre de matchs d’essai (voir la figure 3).</span><span class="sxs-lookup"><span data-stu-id="49b03-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="49b03-296">Chaque luminaire d’essai testera une action spécifique du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="49b03-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="49b03-297">Par exemple, un appareil de test se concentrera sur le test de l’action Créer utilisé lors d’une demande HTTP GET (pour afficher la vue pour créer un employé), et un appareil différent se concentrera sur l’action Créer utilisé dans une demande HTTP POST (pour prendre les informations soumises par l’utilisateur pour créer un employé).</span><span class="sxs-lookup"><span data-stu-id="49b03-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="49b03-298">Chaque classe dérivée n’est responsable que de la configuration nécessaire dans son contexte spécifique, et de fournir les affirmations nécessaires pour vérifier les résultats de son contexte de test spécifique.</span><span class="sxs-lookup"><span data-stu-id="49b03-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![ef test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="49b03-300">**Figure 3**</span><span class="sxs-lookup"><span data-stu-id="49b03-300">**Figure 3**</span></span>

<span data-ttu-id="49b03-301">La convention de nommage et le style de test présenté ici n’est pas nécessaire pour le code testable - c’est juste une approche.</span><span class="sxs-lookup"><span data-stu-id="49b03-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="49b03-302">La figure 4 montre les tests en cours d’exécution dans le plugin jet Brains Resharper coureur d’essai pour Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="49b03-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![ef test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="49b03-304">**Figure 4**</span><span class="sxs-lookup"><span data-stu-id="49b03-304">**Figure 4**</span></span>

<span data-ttu-id="49b03-305">Avec une classe de base pour gérer le code de configuration partagé, les tests unitaires pour chaque action de contrôleur sont petits et faciles à écrire.</span><span class="sxs-lookup"><span data-stu-id="49b03-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="49b03-306">Les tests s’exécuteront rapidement (puisque nous effectuons des opérations en mémoire), et ne devraient pas échouer en raison d’infrastructures ou de préoccupations environnementales indépendantes (parce que nous avons isolé l’unité à l’essai).</span><span class="sxs-lookup"><span data-stu-id="49b03-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="49b03-307">Dans ces tests, la classe de base fait la plupart du travail d’installation.</span><span class="sxs-lookup"><span data-stu-id="49b03-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="49b03-308">Rappelez-vous que le constructeur de la classe de base crée le référentiel en mémoire, une fausse unité de travail et un exemple de la classe EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="49b03-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="49b03-309">La classe de test dérive de cette classe de base et se concentre sur les spécificités de l’essai de la méthode Créer.</span><span class="sxs-lookup"><span data-stu-id="49b03-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="49b03-310">Dans ce cas, les détails se résument aux étapes « organiser, agir et affirmer » que vous verrez dans n’importe quelle procédure de test unitaire :</span><span class="sxs-lookup"><span data-stu-id="49b03-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="49b03-311">Créez un nouvel objet d’emploi pour simuler les données entrantes.</span><span class="sxs-lookup"><span data-stu-id="49b03-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="49b03-312">Invoquez l’action Créer du EmployeeController et passer dans le nouvel employé.</span><span class="sxs-lookup"><span data-stu-id="49b03-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="49b03-313">Vérifier l’action Créer produit les résultats attendus (l’employé apparaît dans le référentiel).</span><span class="sxs-lookup"><span data-stu-id="49b03-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="49b03-314">Ce que nous avons construit nous permet de tester l’une ou l’autre des actions employeeController.</span><span class="sxs-lookup"><span data-stu-id="49b03-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="49b03-315">Par exemple, lorsque nous écrivons des tests pour l’action Index du contrôleur des employés, nous pouvons hériter de la classe de base de test pour établir la même configuration de base pour nos tests.</span><span class="sxs-lookup"><span data-stu-id="49b03-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="49b03-316">Encore une fois, la classe de base créera le référentiel en mémoire, la fausse unité de travail, et un exemple de l’EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="49b03-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="49b03-317">Les tests d’action Index n’ont qu’à se concentrer sur l’invocation de l’action Index et de tester les qualités du modèle les retours d’action.</span><span class="sxs-lookup"><span data-stu-id="49b03-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="49b03-318">Les tests que nous créons avec des faux en mémoire sont orientés vers le test de *l’état* du logiciel.</span><span class="sxs-lookup"><span data-stu-id="49b03-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="49b03-319">Par exemple, lorsque nous testons l’action Créer, nous voulons inspecter l’état du référentiel après l’exécution de l’action de création , le référentiel tient-il le nouvel employé ?</span><span class="sxs-lookup"><span data-stu-id="49b03-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="49b03-320">Plus tard, nous examinerons les tests basés sur l’interaction.</span><span class="sxs-lookup"><span data-stu-id="49b03-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="49b03-321">Les tests basés sur l’interaction demanderont si le code testé a invoqué les méthodes appropriées sur nos objets et a passé les paramètres corrects.</span><span class="sxs-lookup"><span data-stu-id="49b03-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="49b03-322">Pour l’instant, nous allons passer sur la couverture un autre modèle de conception - la charge paresseuse.</span><span class="sxs-lookup"><span data-stu-id="49b03-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="49b03-323">Chargement et chargement paresseux impatients</span><span class="sxs-lookup"><span data-stu-id="49b03-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="49b03-324">À un moment donné dans la ASP.NET application Web MVC, nous pourrions souhaiter afficher les informations d’un employé et inclure les cartes de temps associées de l’employé.</span><span class="sxs-lookup"><span data-stu-id="49b03-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="49b03-325">Par exemple, nous pouvons avoir un affichage sommaire de carte de temps qui indique le nom de l’employé et le nombre total de cartes de temps dans le système.</span><span class="sxs-lookup"><span data-stu-id="49b03-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="49b03-326">Il existe plusieurs approches que nous pouvons adopter pour mettre en œuvre cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="49b03-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="49b03-327">Projection</span><span class="sxs-lookup"><span data-stu-id="49b03-327">Projection</span></span>

<span data-ttu-id="49b03-328">Une approche facile pour créer le résumé est de construire un modèle dédié à l’information que nous voulons afficher dans la vue.</span><span class="sxs-lookup"><span data-stu-id="49b03-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="49b03-329">Dans ce scénario, le modèle pourrait ressembler à ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="49b03-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="49b03-330">Notez que le EmployeeSummaryViewModel n’est pas une entité - en d’autres termes, ce n’est pas quelque chose que nous voulons persister dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="49b03-331">Nous n’utiliserons cette classe que pour mélanger les données dans la vue d’une manière fortement tapée.</span><span class="sxs-lookup"><span data-stu-id="49b03-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="49b03-332">Le modèle de vue est comme un objet de transfert de données (DTO) parce qu’il ne contient aucun comportement (pas de méthodes) - seulement les propriétés.</span><span class="sxs-lookup"><span data-stu-id="49b03-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="49b03-333">Les propriétés conserveront les données dont nous avons besoin pour nous déplacer.</span><span class="sxs-lookup"><span data-stu-id="49b03-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="49b03-334">Il est facile d’instantanéiser ce modèle de vue en utilisant l’opérateur de projection standard de LINQ, l’opérateur Select.</span><span class="sxs-lookup"><span data-stu-id="49b03-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="49b03-335">Il ya deux caractéristiques notables au code ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="49b03-335">There are two notable features to the above code.</span></span> <span data-ttu-id="49b03-336">Tout d’abord , le code est facile à tester parce qu’il est encore facile à observer et à isoler.</span><span class="sxs-lookup"><span data-stu-id="49b03-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="49b03-337">L’opérateur Select travaille tout aussi bien contre nos faux en mémoire qu’il le fait contre la véritable unité de travail.</span><span class="sxs-lookup"><span data-stu-id="49b03-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="49b03-338">La deuxième caractéristique notable est la façon dont le code permet à EF4 de générer une requête unique et efficace pour assembler les informations des employés et des cartes de temps ensemble.</span><span class="sxs-lookup"><span data-stu-id="49b03-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="49b03-339">Nous avons chargé les informations des employés et les informations de carte de temps dans le même objet sans utiliser d’API spéciales.</span><span class="sxs-lookup"><span data-stu-id="49b03-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="49b03-340">Le code s’est contenté d’exprimer l’information dont il a besoin en utilisant des opérateurs LINQ standard qui travaillent contre les sources de données en mémoire ainsi que les sources de données à distance.</span><span class="sxs-lookup"><span data-stu-id="49b03-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="49b03-341">EF4 a été en mesure de traduire les arbres\# d’expression générés par la requête LINQ et le compilateur C en une requête T-SQL unique et efficace.</span><span class="sxs-lookup"><span data-stu-id="49b03-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="49b03-342">Il ya d’autres moments où nous ne voulons pas travailler avec un modèle de vue ou objet DTO, mais avec des entités réelles.</span><span class="sxs-lookup"><span data-stu-id="49b03-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="49b03-343">Lorsque nous savons que nous avons besoin d’un employé *et* des cartes de temps de l’employé, nous pouvons charger avec empressement les données connexes d’une manière discrète et efficace.</span><span class="sxs-lookup"><span data-stu-id="49b03-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="49b03-344">Chargement enthousiaste explicite</span><span class="sxs-lookup"><span data-stu-id="49b03-344">Explicit Eager Loading</span></span>

<span data-ttu-id="49b03-345">Lorsque nous voulons charger avec empressement les informations d’entités connexes, nous avons besoin d’un mécanisme de logique d’entreprise (ou dans ce scénario, logique d’action de contrôleur) pour exprimer son désir au référentiel.</span><span class="sxs-lookup"><span data-stu-id="49b03-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="49b03-346">La classe EF4&lt;&gt; ObjectQuery T définit une méthode Inclure pour spécifier les objets connexes à récupérer lors d’une requête.</span><span class="sxs-lookup"><span data-stu-id="49b03-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="49b03-347">Rappelez-vous l’objet EF4Context expose les&lt;&gt; entités via la classe&lt;&gt;concrète ObjectSet T qui hérite de ObjectQuery T .</span><span class="sxs-lookup"><span data-stu-id="49b03-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="49b03-348">Si nous utilisions les références ObjectSet&lt;T&gt; dans notre action de contrôleur, nous pourrions écrire le code suivant pour spécifier une charge avide d’informations de carte de temps pour chaque employé.</span><span class="sxs-lookup"><span data-stu-id="49b03-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="49b03-349">Cependant, puisque nous essayons de garder notre code testable, nous n’exposons pas ObjectSet&lt;T&gt; de l’extérieur de la véritable unité de classe de travail.</span><span class="sxs-lookup"><span data-stu-id="49b03-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="49b03-350">Au lieu de cela, nous&lt;&gt; nous appuyons sur l’interface IObjectSet T qui est plus facile à simuler, mais IObjectSet&lt;T&gt; ne définit pas une méthode Inclure.</span><span class="sxs-lookup"><span data-stu-id="49b03-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="49b03-351">La beauté de LINQ, c’est que nous pouvons créer notre propre opérateur Inclure.</span><span class="sxs-lookup"><span data-stu-id="49b03-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="49b03-352">Notez que cet opérateur Inclure est défini&lt;comme&gt; une méthode d’extension pour IQueryable T au lieu d’IObjectSet&lt;T&gt;.</span><span class="sxs-lookup"><span data-stu-id="49b03-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="49b03-353">Cela nous donne la possibilité d’utiliser la méthode avec un&lt;plus&gt;large éventail&lt;de&gt;types possibles, y compris IQueryable T , IObjectSet T , ObjectQuery&lt;T&gt;, et ObjectSet&lt;T&gt;.</span><span class="sxs-lookup"><span data-stu-id="49b03-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="49b03-354">Dans le cas où la séquence sous-jacente n’est pas un véritable EF4 ObjectQuery&lt;T&gt;, alors il n’y a pas de mal fait et l’opérateur Inclure est un no-op.</span><span class="sxs-lookup"><span data-stu-id="49b03-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="49b03-355">Si la *is* séquence sous-jacente&lt;&gt; est un ObjectQuery&lt;&gt;T (ou dérivé de ObjectQuery T ), alors EF4 verra notre exigence de données supplémentaires et formulera la requête SQL appropriée.</span><span class="sxs-lookup"><span data-stu-id="49b03-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="49b03-356">Avec ce nouvel opérateur en place, nous pouvons explicitement demander une charge avide d’informations de carte de temps du référentiel.</span><span class="sxs-lookup"><span data-stu-id="49b03-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="49b03-357">Lorsqu’il est exécuté contre un vrai ObjectContext, le code produit la requête unique suivante.</span><span class="sxs-lookup"><span data-stu-id="49b03-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="49b03-358">La requête recueille suffisamment d’informations à partir de la base de données en un seul voyage pour matérialiser les objets de l’employé et remplir entièrement leur propriété TimeCards.</span><span class="sxs-lookup"><span data-stu-id="49b03-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="49b03-359">La bonne nouvelle est que le code à l’intérieur de la méthode d’action reste entièrement testable.</span><span class="sxs-lookup"><span data-stu-id="49b03-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="49b03-360">Nous n’avons pas besoin de fournir des fonctionnalités supplémentaires pour nos faux pour soutenir l’opérateur Inclure.</span><span class="sxs-lookup"><span data-stu-id="49b03-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="49b03-361">La mauvaise nouvelle, c’est que nous avons dû utiliser l’opérateur Inclure à l’intérieur du code que nous voulions garder la persistance ignorante.</span><span class="sxs-lookup"><span data-stu-id="49b03-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="49b03-362">Il s’agit d’un excellent exemple du type de compromis que vous devrez évaluer lors de la construction de code testable.</span><span class="sxs-lookup"><span data-stu-id="49b03-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="49b03-363">Il ya des moments où vous avez besoin de laisser les préoccupations de persistance fuite en dehors de l’abstraction du référentiel pour atteindre les objectifs de performance.</span><span class="sxs-lookup"><span data-stu-id="49b03-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="49b03-364">L’alternative au chargement avide est le chargement paresseux.</span><span class="sxs-lookup"><span data-stu-id="49b03-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="49b03-365">Le chargement paresseux signifie que nous n’avons *pas* besoin de notre code d’entreprise pour annoncer explicitement l’exigence pour les données associées.</span><span class="sxs-lookup"><span data-stu-id="49b03-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="49b03-366">Au lieu de cela, nous utilisons nos entités dans l’application et si des données supplémentaires sont nécessaires Entity Framework chargera les données sur demande.</span><span class="sxs-lookup"><span data-stu-id="49b03-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="49b03-367">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="49b03-367">Lazy Loading</span></span>

<span data-ttu-id="49b03-368">Il est facile d’imaginer un scénario où nous ne savons pas quelles données une logique d’entreprise aura besoin.</span><span class="sxs-lookup"><span data-stu-id="49b03-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="49b03-369">Nous savons peut-être que la logique a besoin d’un objet de l’employé, mais nous pouvons nous brancher sur différentes voies d’exécution où certaines de ces voies exigent des informations de carte de temps de l’employé, et d’autres pas.</span><span class="sxs-lookup"><span data-stu-id="49b03-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="49b03-370">Des scénarios comme celui-ci sont parfaits pour le chargement implicite paresseux parce que les données apparaissent comme par magie sur une base nécessaire.</span><span class="sxs-lookup"><span data-stu-id="49b03-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="49b03-371">Le chargement paresseux, également connu sous le nom de chargement différé, place certaines exigences sur nos objets d’entité.</span><span class="sxs-lookup"><span data-stu-id="49b03-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="49b03-372">Les POCO avec une véritable ignorance de persistance ne seraient pas confrontés à des exigences de la couche de persistance, mais l’ignorance de la vraie persistance est pratiquement impossible à réaliser.</span><span class="sxs-lookup"><span data-stu-id="49b03-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="49b03-373">Au lieu de cela, nous mesurons l’ignorance de persistance dans les degrés relatifs.</span><span class="sxs-lookup"><span data-stu-id="49b03-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="49b03-374">Il serait regrettable que nous ayons besoin d’hériter d’une classe de base axée sur la persistance ou d’utiliser une collection spécialisée pour obtenir un chargement paresseux dans les POCO.</span><span class="sxs-lookup"><span data-stu-id="49b03-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="49b03-375">Heureusement, EF4 a une solution moins intrusive.</span><span class="sxs-lookup"><span data-stu-id="49b03-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="49b03-376">Pratiquement indétectable</span><span class="sxs-lookup"><span data-stu-id="49b03-376">Virtually Undetectable</span></span>

<span data-ttu-id="49b03-377">Lors de l’utilisation d’objets POCO, EF4 peut générer dynamiquement des procurations en temps d’exécution pour les entités.</span><span class="sxs-lookup"><span data-stu-id="49b03-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="49b03-378">Ces procurations enveloppent invisiblement les OPO matérialisées et fournissent des services supplémentaires en interceptant chaque propriété obtenir et définir l’opération pour effectuer des travaux supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="49b03-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="49b03-379">Un tel service est la fonction de chargement paresseux que nous recherchons.</span><span class="sxs-lookup"><span data-stu-id="49b03-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="49b03-380">Un autre service est un mécanisme efficace de suivi des changements qui peut enregistrer lorsque le programme modifie la valeur des propriétés d’une entité.</span><span class="sxs-lookup"><span data-stu-id="49b03-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="49b03-381">La liste des modifications est utilisée par l’ObjectContext pendant la méthode SaveChanges pour persister toutes les entités modifiées à l’aide de commandes UPDATE.</span><span class="sxs-lookup"><span data-stu-id="49b03-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="49b03-382">Pour que ces procurations fonctionnent, cependant, ils ont besoin d’un moyen de s’accrocher à la propriété obtenir et définir des opérations sur une entité, et les procurations atteindre cet objectif en dominant les membres virtuels.</span><span class="sxs-lookup"><span data-stu-id="49b03-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="49b03-383">Ainsi, si nous voulons avoir un chargement implicite paresseux et un suivi efficace des changements, nous devons revenir à nos définitions de classe POCO et marquer les propriétés comme virtuelles.</span><span class="sxs-lookup"><span data-stu-id="49b03-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="49b03-384">Nous pouvons encore dire que l’entité employee est la plupart du temps la persistance ignorante.</span><span class="sxs-lookup"><span data-stu-id="49b03-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="49b03-385">La seule exigence est d’utiliser des membres virtuels et cela n’a pas d’impact sur la testabilité du code.</span><span class="sxs-lookup"><span data-stu-id="49b03-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="49b03-386">Nous n’avons pas besoin de dériver d’une classe de base spéciale, ou même utiliser une collection spéciale dédiée au chargement paresseux.</span><span class="sxs-lookup"><span data-stu-id="49b03-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="49b03-387">Comme le code le démontre, toute&lt;classe&gt; implémentant ICollection T est disponible pour détenir des entités connexes.</span><span class="sxs-lookup"><span data-stu-id="49b03-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="49b03-388">Il y a aussi un changement mineur que nous devons apporter à l’intérieur de notre unité de travail.</span><span class="sxs-lookup"><span data-stu-id="49b03-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="49b03-389">Le chargement paresseux est *éteint* par défaut lorsque vous travaillez directement avec un objet ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="49b03-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="49b03-390">Il y a une propriété que nous pouvons mettre sur la propriété ContextOptions pour permettre le chargement différé, et nous pouvons définir cette propriété à l’intérieur de notre unité de travail réelle si nous voulons permettre le chargement paresseux partout.</span><span class="sxs-lookup"><span data-stu-id="49b03-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="49b03-391">Avec le chargement paresseux implicite activé, le code d’application peut utiliser un employé et les cartes de temps associées de l’employé tout en restant parfaitement inconscient du travail requis pour EF pour charger les données supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="49b03-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="49b03-392">Le chargement paresseux rend le code d’application plus facile à écrire, et avec la magie proxy le code reste complètement testable.</span><span class="sxs-lookup"><span data-stu-id="49b03-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="49b03-393">Les faux en mémoire de l’unité de travail peuvent simplement précharger les fausses entités avec des données associées en cas de besoin lors d’un test.</span><span class="sxs-lookup"><span data-stu-id="49b03-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="49b03-394">À ce stade, nous allons tourner notre attention de&lt;la&gt; construction de dépôts en utilisant IObjectSet T et regarder les abstractions pour cacher tous les signes du cadre de persistance.</span><span class="sxs-lookup"><span data-stu-id="49b03-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="49b03-395">Dépôts personnalisés</span><span class="sxs-lookup"><span data-stu-id="49b03-395">Custom Repositories</span></span>

<span data-ttu-id="49b03-396">Lorsque nous avons présenté pour la première fois le modèle de conception de l’unité de travail dans cet article, nous avons fourni un certain code d’échantillon pour ce que l’unité de travail pourrait ressembler.</span><span class="sxs-lookup"><span data-stu-id="49b03-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="49b03-397">Présentons à nouveau cette idée originale en utilisant le scénario de carte de temps de l’employé et des employés avec lequel nous avons travaillé.</span><span class="sxs-lookup"><span data-stu-id="49b03-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="49b03-398">La principale différence entre cette unité de travail et l’unité de travail que nous avons créée dans la dernière section est&lt;de&gt;savoir comment cette unité de travail n’utilise aucune abstraction du cadre EF4 (il n’y a pas d’IObjectSet T ).</span><span class="sxs-lookup"><span data-stu-id="49b03-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="49b03-399">IObjectSet&lt;&gt; T fonctionne bien comme une interface de référentiel, mais l’API qu’il expose pourrait ne pas parfaitement correspondre aux besoins de notre application.</span><span class="sxs-lookup"><span data-stu-id="49b03-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="49b03-400">Dans cette approche à venir, nous représenterons des&lt;&gt; dépôts à l’aide d’une abstraction IRepository T personnalisée.</span><span class="sxs-lookup"><span data-stu-id="49b03-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="49b03-401">De nombreux développeurs qui suivent la conception axée sur les tests, la conception&lt;&gt; axée sur le comportement, et la conception de méthodologies axées sur le domaine préfèrent l’approche IRepository T pour plusieurs raisons.</span><span class="sxs-lookup"><span data-stu-id="49b03-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="49b03-402">Tout d’abord,&lt;l’interface IRepository T&gt; représente une couche « anti-corruption ».</span><span class="sxs-lookup"><span data-stu-id="49b03-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="49b03-403">Comme décrit par Eric Evans dans son domain Driven Design livre une couche anti-corruption maintient votre code de domaine loin des API infrastructure, comme une API persistance.</span><span class="sxs-lookup"><span data-stu-id="49b03-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="49b03-404">Deuxièmement, les développeurs peuvent intégrer des méthodes dans le référentiel qui répondent aux besoins exacts d’une application (comme découvert lors de la rédaction de tests).</span><span class="sxs-lookup"><span data-stu-id="49b03-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="49b03-405">Par exemple, nous pourrions souvent avoir besoin de localiser une seule entité à l’aide d’une valeur d’identification, de sorte que nous pouvons ajouter une méthode FindById à l’interface de dépôt.</span><span class="sxs-lookup"><span data-stu-id="49b03-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="49b03-406">Notre définition IRepository&lt;T&gt; ressemblera à ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="49b03-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="49b03-407">Notez que nous allons revenir à&lt;l’utilisation d’une interface IQueryable T&gt; pour exposer les collections d’entités.</span><span class="sxs-lookup"><span data-stu-id="49b03-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="49b03-408">IQueryable&lt;&gt; T permet aux arbres d’expression LINQ de s’écouler dans le fournisseur EF4 et de donner au fournisseur une vue holistique de la requête.</span><span class="sxs-lookup"><span data-stu-id="49b03-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="49b03-409">Une deuxième option serait de retourner&lt;IEnumerable T&gt;, ce qui signifie que le fournisseur EF4 LINQ ne verra que les expressions construites à l’intérieur du dépôt.</span><span class="sxs-lookup"><span data-stu-id="49b03-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="49b03-410">Tout regroupement, commande et projection effectué à l’extérieur du dépôt ne sera pas composé dans la commande SQL envoyée à la base de données, ce qui peut nuire aux performances.</span><span class="sxs-lookup"><span data-stu-id="49b03-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="49b03-411">D’autre part, un référentiel de retour&lt;&gt; que les résultats IEnumerable T ne vous surprendra jamais avec une nouvelle commande SQL.</span><span class="sxs-lookup"><span data-stu-id="49b03-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="49b03-412">Les deux approches fonctionneront, et les deux approches restent testables.</span><span class="sxs-lookup"><span data-stu-id="49b03-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="49b03-413">Il est simple de fournir une mise en&lt;&gt; œuvre unique de l’interface IRepository T à l’aide de génériques et de l’API EF4 ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="49b03-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="49b03-414">L’approche IRepository&lt;T&gt; nous donne un contrôle supplémentaire sur nos requêtes parce qu’un client doit invoquer une méthode pour se rendre à une entité.</span><span class="sxs-lookup"><span data-stu-id="49b03-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="49b03-415">À l’intérieur de la méthode, nous pourrions fournir des vérifications supplémentaires et des opérateurs de LINQ pour faire respecter les contraintes d’application.</span><span class="sxs-lookup"><span data-stu-id="49b03-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="49b03-416">Notez que l’interface a deux contraintes sur le paramètre de type générique.</span><span class="sxs-lookup"><span data-stu-id="49b03-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="49b03-417">La première contrainte est la classe contre&lt;souillée requise par ObjectSet T&gt;, et la deuxième contrainte oblige nos entités à mettre en œuvre IEntity - une abstraction créée pour l’application.</span><span class="sxs-lookup"><span data-stu-id="49b03-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="49b03-418">L’interface IEntity oblige les entités à avoir une propriété Id lisible, et nous pouvons ensuite utiliser cette propriété dans la méthode FindById.</span><span class="sxs-lookup"><span data-stu-id="49b03-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="49b03-419">IEntity est défini avec le code suivant.</span><span class="sxs-lookup"><span data-stu-id="49b03-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="49b03-420">IEntity pourrait être considéré comme une petite violation de l’ignorance de persistance puisque nos entités sont tenues de mettre en œuvre cette interface.</span><span class="sxs-lookup"><span data-stu-id="49b03-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="49b03-421">Rappelez-vous l’ignorance de persistance est sur les compromis, et pour beaucoup la fonctionnalité FindById l’emportera sur la contrainte imposée par l’interface.</span><span class="sxs-lookup"><span data-stu-id="49b03-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="49b03-422">L’interface n’a aucun impact sur la testabilité.</span><span class="sxs-lookup"><span data-stu-id="49b03-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="49b03-423">L’instantanéisation d’un&lt;T&gt; IRepository en direct nécessite un EF4 ObjectContext, de sorte qu’une unité concrète de mise en œuvre du travail devrait gérer l’instantanéisation.</span><span class="sxs-lookup"><span data-stu-id="49b03-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="49b03-424">Utilisation du référentiel personnalisé</span><span class="sxs-lookup"><span data-stu-id="49b03-424">Using the Custom Repository</span></span>

<span data-ttu-id="49b03-425">L’utilisation de notre référentiel personnalisé n’est pas significativement&lt;&gt;différente de l’utilisation du référentiel basé sur IObjectSet T .</span><span class="sxs-lookup"><span data-stu-id="49b03-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="49b03-426">Au lieu d’appliquer directement les exploitants de LINQ à une propriété, nous devrons d’abord invoquer l’une des méthodes du référentiel pour saisir une référence IQueryable&lt;T.&gt;</span><span class="sxs-lookup"><span data-stu-id="49b03-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="49b03-427">Remarquez que l’opérateur Personnalisé Inclure que nous avons mis en œuvre précédemment fonctionnera sans changement.</span><span class="sxs-lookup"><span data-stu-id="49b03-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="49b03-428">La méthode FindById du référentiel supprime la logique dupliquée des actions visant à récupérer une seule entité.</span><span class="sxs-lookup"><span data-stu-id="49b03-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="49b03-429">Il n’y a pas de différence significative dans la testabilité des deux approches que nous avons examinées.</span><span class="sxs-lookup"><span data-stu-id="49b03-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="49b03-430">Nous pourrions fournir de fausses implémentations d’IRepository&lt;T&gt; en construisant des classes en béton soutenues par HashSet&lt;Employee&gt; - tout comme ce que nous avons fait dans la dernière section.</span><span class="sxs-lookup"><span data-stu-id="49b03-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="49b03-431">Cependant, certains développeurs préfèrent utiliser des objets fictifs et des cadres d’objets simulés au lieu de construire des faux.</span><span class="sxs-lookup"><span data-stu-id="49b03-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="49b03-432">Nous allons envisager d’utiliser des maquettes pour tester notre implémentation et discuter des différences entre les moqueurs et les faux dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="49b03-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="49b03-433">Test avec Mocks</span><span class="sxs-lookup"><span data-stu-id="49b03-433">Testing with Mocks</span></span>

<span data-ttu-id="49b03-434">Il existe différentes approches pour construire ce que Martin Fowler appelle un « double test ».</span><span class="sxs-lookup"><span data-stu-id="49b03-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="49b03-435">Un double test (comme un double film cascadeur) est un objet que vous construisez pour "stand in" pour de vrais objets de production pendant les tests.</span><span class="sxs-lookup"><span data-stu-id="49b03-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="49b03-436">Les dépôts en mémoire que nous avons créés sont des doubles de test pour les référentiels qui parlent à SQL Server.</span><span class="sxs-lookup"><span data-stu-id="49b03-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="49b03-437">Nous avons vu comment utiliser ces tests-doubles pendant les tests unitaires pour isoler le code et faire fonctionner les tests rapidement.</span><span class="sxs-lookup"><span data-stu-id="49b03-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="49b03-438">Les doubles de test que nous avons construits ont de véritables implémentations de travail.</span><span class="sxs-lookup"><span data-stu-id="49b03-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="49b03-439">Dans les coulisses, chacun stocke une collection d’objets en béton, et ils ajouteront et supprimeront des objets de cette collection lorsque nous manipulons le référentiel lors d’un test.</span><span class="sxs-lookup"><span data-stu-id="49b03-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="49b03-440">Certains développeurs aiment construire leur test double de cette façon - avec un code réel et des implémentations de travail.</span><span class="sxs-lookup"><span data-stu-id="49b03-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="49b03-441">Ces doubles test sont ce que nous appelons *des faux*.</span><span class="sxs-lookup"><span data-stu-id="49b03-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="49b03-442">Ils ont des implémentations de travail, mais ils ne sont pas assez réels pour l’utilisation de la production.</span><span class="sxs-lookup"><span data-stu-id="49b03-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="49b03-443">Le faux référentiel n’écrit pas à la base de données.</span><span class="sxs-lookup"><span data-stu-id="49b03-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="49b03-444">Le faux serveur SMTP n’envoie pas de message électronique sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="49b03-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="49b03-445">Mocks contre Fakes</span><span class="sxs-lookup"><span data-stu-id="49b03-445">Mocks versus Fakes</span></span>

<span data-ttu-id="49b03-446">Il ya un autre type de double test connu sous le nom d’un *simulacre*.</span><span class="sxs-lookup"><span data-stu-id="49b03-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="49b03-447">Alors que les faux ont des implémentations de travail, les moqueries viennent sans implémentation.</span><span class="sxs-lookup"><span data-stu-id="49b03-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="49b03-448">À l’aide d’un cadre d’objets fictifs, nous construisons ces objets simulés au moment de l’exécution et les utilisons comme doubles d’essai.</span><span class="sxs-lookup"><span data-stu-id="49b03-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="49b03-449">Dans cette section, nous allons utiliser le cadre de moquerie open source Moq.</span><span class="sxs-lookup"><span data-stu-id="49b03-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="49b03-450">Voici un exemple simple d’utilisation de Moq pour créer dynamiquement un double test pour un référentiel d’employé.</span><span class="sxs-lookup"><span data-stu-id="49b03-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="49b03-451">Nous demandons à Moq une&lt;&gt; mise en œuvre de l’IRepository Employee et elle en construit une dynamique.</span><span class="sxs-lookup"><span data-stu-id="49b03-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="49b03-452">Nous pouvons accéder à l’objet&lt;de&gt; mise en œuvre&lt;IRepository Employee en accédant à la propriété Objet de l’objet Mock T.&gt;</span><span class="sxs-lookup"><span data-stu-id="49b03-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="49b03-453">C’est cet objet intérieur que nous pouvons transmettre à nos contrôleurs, et ils ne sauront pas s’il s’agit d’un double test ou du véritable référentiel.</span><span class="sxs-lookup"><span data-stu-id="49b03-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="49b03-454">Nous pouvons invoquer des méthodes sur l’objet tout comme nous invoquons des méthodes sur un objet avec une réelle implémentation.</span><span class="sxs-lookup"><span data-stu-id="49b03-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="49b03-455">Vous devez vous demander ce que le faux référentiel fera lorsque nous invoquons la méthode Add.</span><span class="sxs-lookup"><span data-stu-id="49b03-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="49b03-456">Comme il n’y a pas d’implémentation derrière l’objet simulé, Add ne fait rien.</span><span class="sxs-lookup"><span data-stu-id="49b03-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="49b03-457">Il n’y a pas de collection de béton dans les coulisses comme nous l’avions eu avec les faux que nous avons écrits, de sorte que l’employé est jeté.</span><span class="sxs-lookup"><span data-stu-id="49b03-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="49b03-458">Qu’en est-il de la valeur de retour de FindById?</span><span class="sxs-lookup"><span data-stu-id="49b03-458">What about the return value of FindById?</span></span> <span data-ttu-id="49b03-459">Dans ce cas, l’objet simulé fait la seule chose qu’il peut faire, qui est de retourner une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="49b03-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="49b03-460">Puisque nous revenons à un type de référence (un employé), la valeur de rendement est une valeur nulle.</span><span class="sxs-lookup"><span data-stu-id="49b03-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="49b03-461">Les faux peuvent sembler sans valeur; cependant, il ya deux autres caractéristiques de moqueries que nous n’avons pas parlé.</span><span class="sxs-lookup"><span data-stu-id="49b03-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="49b03-462">Tout d’abord, le cadre Moq enregistre tous les appels effectués sur l’objet simulé.</span><span class="sxs-lookup"><span data-stu-id="49b03-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="49b03-463">Plus tard dans le code, nous pouvons demander à Moq si quelqu’un a invoqué la méthode Add, ou si quelqu’un a invoqué la méthode FindById.</span><span class="sxs-lookup"><span data-stu-id="49b03-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="49b03-464">Nous verrons plus tard comment nous pouvons utiliser cette fonction d’enregistrement "boîte noire" dans les tests.</span><span class="sxs-lookup"><span data-stu-id="49b03-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="49b03-465">La deuxième grande fonctionnalité est de savoir comment nous pouvons utiliser Moq pour programmer un objet simulé avec *des attentes*.</span><span class="sxs-lookup"><span data-stu-id="49b03-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="49b03-466">Une attente indique à l’objet simulé comment répondre à une interaction donnée.</span><span class="sxs-lookup"><span data-stu-id="49b03-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="49b03-467">Par exemple, nous pouvons programmer une attente dans notre maquette et lui dire de retourner un objet employé quand quelqu’un invoque FindById.</span><span class="sxs-lookup"><span data-stu-id="49b03-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="49b03-468">Le cadre Moq utilise une API de configuration et des expressions lambda pour programmer ces attentes.</span><span class="sxs-lookup"><span data-stu-id="49b03-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="49b03-469">Dans cet échantillon, nous demandons à Moq de construire dynamiquement un référentiel, puis nous programmons le référentiel avec une attente.</span><span class="sxs-lookup"><span data-stu-id="49b03-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="49b03-470">L’attente indique à l’objet simulé de retourner un nouvel objet employé d’une valeur Id de 5 lorsque quelqu’un invoque la méthode FindById en passant une valeur de 5.</span><span class="sxs-lookup"><span data-stu-id="49b03-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="49b03-471">Ce test passe, et nous n’avons pas besoin de&lt;construire&gt;une implémentation complète pour faux IRepository T .</span><span class="sxs-lookup"><span data-stu-id="49b03-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="49b03-472">Revenons sur les tests que nous avons écrits plus tôt et retravainons-les pour utiliser des maquettes au lieu de faux.</span><span class="sxs-lookup"><span data-stu-id="49b03-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="49b03-473">Comme avant, nous allons utiliser une classe de base pour configurer les pièces communes de l’infrastructure dont nous avons besoin pour tous les tests du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="49b03-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="49b03-474">Le code de configuration reste la plupart du temps le même.</span><span class="sxs-lookup"><span data-stu-id="49b03-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="49b03-475">Au lieu d’utiliser des faux, nous allons utiliser Moq pour construire des objets simulés.</span><span class="sxs-lookup"><span data-stu-id="49b03-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="49b03-476">La classe de base s’arrange pour que l’unité de travail fictive renvoie un faux référentiel lorsque le code invoque la propriété des employés.</span><span class="sxs-lookup"><span data-stu-id="49b03-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="49b03-477">Le reste de la configuration simulée aura lieu à l’intérieur des montages d’essai dédiés à chaque scénario spécifique.</span><span class="sxs-lookup"><span data-stu-id="49b03-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="49b03-478">Par exemple, le montage de test pour l’action Index configurera le faux référentiel pour renvoyer une liste d’employés lorsque l’action invoque la méthode FindAll du faux référentiel.</span><span class="sxs-lookup"><span data-stu-id="49b03-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="49b03-479">À l’exception des attentes, nos tests ressemblent aux tests que nous avions auparavant.</span><span class="sxs-lookup"><span data-stu-id="49b03-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="49b03-480">Cependant, avec la capacité d’enregistrement d’un cadre fictif, nous pouvons aborder les tests sous un angle différent.</span><span class="sxs-lookup"><span data-stu-id="49b03-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="49b03-481">Nous examinerons cette nouvelle perspective dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="49b03-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="49b03-482">Test d’état contre interaction</span><span class="sxs-lookup"><span data-stu-id="49b03-482">State versus Interaction Testing</span></span>

<span data-ttu-id="49b03-483">Il existe différentes techniques que vous pouvez utiliser pour tester un logiciel avec des objets simulés.</span><span class="sxs-lookup"><span data-stu-id="49b03-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="49b03-484">Une approche consiste à utiliser les tests basés sur l’État, ce que nous avons fait dans ce document jusqu’à présent.</span><span class="sxs-lookup"><span data-stu-id="49b03-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="49b03-485">Les tests basés sur l’État font des affirmations sur l’état du logiciel.</span><span class="sxs-lookup"><span data-stu-id="49b03-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="49b03-486">Dans le dernier test, nous avons invoqué une méthode d’action sur le contrôleur et fait une affirmation sur le modèle qu’il devrait construire.</span><span class="sxs-lookup"><span data-stu-id="49b03-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="49b03-487">Voici quelques autres exemples d’état de test :</span><span class="sxs-lookup"><span data-stu-id="49b03-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="49b03-488">Vérifier le référentiel contient le nouvel objet de l’employé après l’exécution de Create.</span><span class="sxs-lookup"><span data-stu-id="49b03-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="49b03-489">Vérifier le modèle contient une liste de tous les employés après index exécute.</span><span class="sxs-lookup"><span data-stu-id="49b03-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="49b03-490">Vérifier le référentiel ne contient pas un employé donné après l’exécution de Suppression.</span><span class="sxs-lookup"><span data-stu-id="49b03-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="49b03-491">Une autre approche que vous verrez avec des objets simulés est de vérifier les *interactions*.</span><span class="sxs-lookup"><span data-stu-id="49b03-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="49b03-492">Alors que les tests basés sur l’état font des affirmations sur l’état des objets, les tests basés sur l’interaction font des affirmations sur la façon dont les objets interagissent.</span><span class="sxs-lookup"><span data-stu-id="49b03-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="49b03-493">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="49b03-493">For example:</span></span>

-   <span data-ttu-id="49b03-494">Vérifiez que le contrôleur invoque la méthode Add du référentiel lorsque La création s’exécute.</span><span class="sxs-lookup"><span data-stu-id="49b03-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="49b03-495">Vérifiez que le contrôleur invoque la méthode FindAll du référentiel lorsque Index s’exécute.</span><span class="sxs-lookup"><span data-stu-id="49b03-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="49b03-496">Vérifiez que le contrôleur invoque la méthode Commit de l’unité de travail pour enregistrer les modifications lorsque Edit s’exécute.</span><span class="sxs-lookup"><span data-stu-id="49b03-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="49b03-497">Les tests d’interaction nécessitent souvent moins de données de test, car nous ne pions pas à l’intérieur des collections et vérifions les comptes.</span><span class="sxs-lookup"><span data-stu-id="49b03-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="49b03-498">Par exemple, si nous savons que l’action Détails invoque la méthode FindById d’un référentiel avec la valeur correcte - alors l’action se comporte probablement correctement.</span><span class="sxs-lookup"><span data-stu-id="49b03-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="49b03-499">Nous pouvons vérifier ce comportement sans configurer les données de test à revenir de FindById.</span><span class="sxs-lookup"><span data-stu-id="49b03-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="49b03-500">La seule configuration requise dans le montage de test ci-dessus est la configuration fournie par la classe de base.</span><span class="sxs-lookup"><span data-stu-id="49b03-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="49b03-501">Lorsque nous invoquons l’action du contrôleur, Moq enregistre les interactions avec le faux référentiel.</span><span class="sxs-lookup"><span data-stu-id="49b03-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="49b03-502">À l’aide de l’API Verify de Moq, nous pouvons demander à Moq si le contrôleur a invoqué FindById avec la valeur d’identification appropriée.</span><span class="sxs-lookup"><span data-stu-id="49b03-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="49b03-503">Si le contrôleur n’a pas invoqué la méthode, ou invoqué la méthode avec une valeur de paramètre inattendue, la méthode Vérifier lancera une exception et le test échouera.</span><span class="sxs-lookup"><span data-stu-id="49b03-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="49b03-504">Voici un autre exemple pour vérifier l’action Créer invoque Commit sur l’unité de travail actuelle.</span><span class="sxs-lookup"><span data-stu-id="49b03-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="49b03-505">Un danger avec les tests d’interaction est la tendance à surcifier les interactions.</span><span class="sxs-lookup"><span data-stu-id="49b03-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="49b03-506">La capacité de l’objet simulé d’enregistrer et de vérifier chaque interaction avec l’objet simulé ne signifie pas que le test doit essayer de vérifier chaque interaction.</span><span class="sxs-lookup"><span data-stu-id="49b03-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="49b03-507">Certaines interactions sont des détails d’implémentation et vous ne devez vérifier que les interactions *requises* pour satisfaire le test actuel.</span><span class="sxs-lookup"><span data-stu-id="49b03-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="49b03-508">Le choix entre les maquettes ou les faux dépend en grande partie du système que vous testez et de vos préférences personnelles (ou d’équipe).</span><span class="sxs-lookup"><span data-stu-id="49b03-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="49b03-509">Les objets simulés peuvent réduire considérablement la quantité de code dont vous avez besoin pour implémenter des doubles tests, mais tout le monde n’est pas à l’aise d’attentes de programmation et de vérification des interactions.</span><span class="sxs-lookup"><span data-stu-id="49b03-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="49b03-510">Conclusions</span><span class="sxs-lookup"><span data-stu-id="49b03-510">Conclusions</span></span>

<span data-ttu-id="49b03-511">Dans cet article, nous avons démontré plusieurs approches pour créer du code testable tout en utilisant le cadre d’entité ADO.NET pour la persistance des données.</span><span class="sxs-lookup"><span data-stu-id="49b03-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="49b03-512">Nous pouvons tirer parti construit dans&lt;des&gt;abstractions comme IObjectSet T&lt;&gt;, ou créer nos propres abstractions comme IRepository T .</span><span class="sxs-lookup"><span data-stu-id="49b03-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="49b03-513">Dans les deux cas, le soutien du POCO dans le cadre d’entités ADO.NET 4.0 permet aux consommateurs de ces abstractions de rester ignorants persistants et hautement testables.</span><span class="sxs-lookup"><span data-stu-id="49b03-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="49b03-514">D’autres fonctionnalités EF4 comme le chargement paresseux implicite permettent au code de service d’entreprise et d’application de fonctionner sans se soucier des détails d’un magasin de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="49b03-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="49b03-515">Enfin, les abstractions que nous créons sont faciles à se moquer ou à simuler à l’intérieur des tests unitaires, et nous pouvons utiliser ces doubles de test pour réaliser des tests rapides, très isolés et fiables.</span><span class="sxs-lookup"><span data-stu-id="49b03-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="49b03-516">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="49b03-516">Additional Resources</span></span>

-   <span data-ttu-id="49b03-517">Robert C. Martin, « [Le principe de la responsabilité unique](https://www.objectmentor.com/resources/articles/srp.pdf)»</span><span class="sxs-lookup"><span data-stu-id="49b03-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="49b03-518">Martin Fowler, [Catalogue des modèles](https://www.martinfowler.com/eaaCatalog/index.html) de Patterns of Enterprise Application *Architecture*</span><span class="sxs-lookup"><span data-stu-id="49b03-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="49b03-519">Griffin Caprio, " [Injection de dépendance](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="49b03-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="49b03-520">Blog de programmabilité des données, « [Procédure pas à pas : Développement piloté par test avec le cadre d’entité 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)».</span><span class="sxs-lookup"><span data-stu-id="49b03-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="49b03-521">Blog de programmabilité des données, « [Utilisation du dépôt et des modèles d’unité de travail avec le cadre d’entité 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)»</span><span class="sxs-lookup"><span data-stu-id="49b03-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="49b03-522">Aaron Jensen, " [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"</span><span class="sxs-lookup"><span data-stu-id="49b03-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="49b03-523">Eric Lee, " [BDD avec MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"</span><span class="sxs-lookup"><span data-stu-id="49b03-523">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="49b03-524">Eric Evans, " [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="49b03-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="49b03-525">Martin Fowler, " [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="49b03-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="49b03-526">Martin Fowler, " [Test Double](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="49b03-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="49b03-527">Moq</span><span class="sxs-lookup"><span data-stu-id="49b03-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="49b03-528">Biographie</span><span class="sxs-lookup"><span data-stu-id="49b03-528">Biography</span></span>

<span data-ttu-id="49b03-529">Scott Allen est membre du personnel technique de Pluralsight et fondateur de OdeToCode.com.</span><span class="sxs-lookup"><span data-stu-id="49b03-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="49b03-530">En 15 ans de développement de logiciels commerciaux, Scott a travaillé sur des solutions pour tout, des appareils embarqués 8 bits aux applications Web ASP.NET hautement évolutives.</span><span class="sxs-lookup"><span data-stu-id="49b03-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="49b03-531">Vous pouvez joindre Scott sur son blog à [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)OdeToCode, ou sur Twitter à .</span><span class="sxs-lookup"><span data-stu-id="49b03-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
