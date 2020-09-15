---
title: Résilience des connexions et logique de nouvelle tentative-EF6
description: Résilience des connexions et logique de nouvelle tentative dans Entity Framework 6
author: AndriySvyryd
ms.date: 11/20/2019
uid: ef6/fundamentals/connection-resiliency/retry-logic
ms.openlocfilehash: 3e71e973be5a23c86f873e9adf1bf00f4b6def58
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073186"
---
# <a name="connection-resiliency-and-retry-logic"></a><span data-ttu-id="04f4c-103">Résilience des connexions et logique de nouvelle tentative</span><span class="sxs-lookup"><span data-stu-id="04f4c-103">Connection resiliency and retry logic</span></span>
> [!NOTE]
> <span data-ttu-id="04f4c-104">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="04f4c-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="04f4c-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="04f4c-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="04f4c-106">Les applications se connectant à un serveur de base de données ont toujours été vulnérables aux interruptions de connexion en raison de défaillances du serveur principal et de l’instabilité du réseau.</span><span class="sxs-lookup"><span data-stu-id="04f4c-106">Applications connecting to a database server have always been vulnerable to connection breaks due to back-end failures and network instability.</span></span> <span data-ttu-id="04f4c-107">Toutefois, dans un environnement LAN qui fonctionne sur des serveurs de base de données dédiés, ces erreurs sont assez rares, car une logique supplémentaire pour gérer ces échecs n’est souvent pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="04f4c-107">However, in a LAN based environment working against dedicated database servers these errors are rare enough that extra logic to handle those failures is not often required.</span></span> <span data-ttu-id="04f4c-108">Avec la montée en charge des serveurs de base de données Cloud tels que Windows Azure SQL Database et des connexions sur des réseaux moins fiables, il est désormais plus courant de rencontrer des interruptions de connexion.</span><span class="sxs-lookup"><span data-stu-id="04f4c-108">With the rise of cloud based database servers such as Windows Azure SQL Database and connections over less reliable networks it is now more common for connection breaks to occur.</span></span> <span data-ttu-id="04f4c-109">Cela peut être dû à des techniques défensives utilisées par les bases de données Cloud pour garantir l’équité du service, telles que la limitation de la connexion, ou l’instabilité du réseau provoquant des délais d’attente intermittents et d’autres erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="04f4c-109">This could be due to defensive techniques that cloud databases use to ensure fairness of service, such as connection throttling, or to instability in the network causing intermittent timeouts and other transient errors.</span></span>  

<span data-ttu-id="04f4c-110">La résilience de connexion fait référence à la possibilité pour EF de retenter automatiquement les commandes qui échouent en raison de ces interruptions de connexion.</span><span class="sxs-lookup"><span data-stu-id="04f4c-110">Connection Resiliency refers to the ability for EF to automatically retry any commands that fail due to these connection breaks.</span></span>  

## <a name="execution-strategies"></a><span data-ttu-id="04f4c-111">Stratégies d’exécution</span><span class="sxs-lookup"><span data-stu-id="04f4c-111">Execution Strategies</span></span>  

<span data-ttu-id="04f4c-112">La nouvelle tentative de connexion est prise en charge par une implémentation de l’interface IDbExecutionStrategy.</span><span class="sxs-lookup"><span data-stu-id="04f4c-112">Connection retry is taken care of by an implementation of the IDbExecutionStrategy interface.</span></span> <span data-ttu-id="04f4c-113">Les implémentations de IDbExecutionStrategy sont chargées d’accepter une opération et, si une exception se produit, de déterminer si une nouvelle tentative est appropriée et de retenter si elle est.</span><span class="sxs-lookup"><span data-stu-id="04f4c-113">Implementations of the IDbExecutionStrategy will be responsible for accepting an operation and, if an exception occurs, determining if a retry is appropriate and retrying if it is.</span></span> <span data-ttu-id="04f4c-114">Il existe quatre stratégies d’exécution fournies avec EF :</span><span class="sxs-lookup"><span data-stu-id="04f4c-114">There are four execution strategies that ship with EF:</span></span>  

1. <span data-ttu-id="04f4c-115">**DefaultExecutionStrategy**: cette stratégie d’exécution ne réessaye aucune opération, il s’agit de la valeur par défaut pour les bases de données autres que SQL Server.</span><span class="sxs-lookup"><span data-stu-id="04f4c-115">**DefaultExecutionStrategy**: this execution strategy does not retry any operations, it is the default for databases other than sql server.</span></span>  
2. <span data-ttu-id="04f4c-116">**Defaultsqlexecutionstrategy,**: il s’agit d’une stratégie d’exécution interne qui est utilisée par défaut.</span><span class="sxs-lookup"><span data-stu-id="04f4c-116">**DefaultSqlExecutionStrategy**: this is an internal execution strategy that is used by default.</span></span> <span data-ttu-id="04f4c-117">Cette stratégie ne réessaye pas du tout, mais elle encapsule toutes les exceptions qui peuvent être temporaires pour informer les utilisateurs qu’ils peuvent souhaiter activer la résilience des connexions.</span><span class="sxs-lookup"><span data-stu-id="04f4c-117">This strategy does not retry at all, however, it will wrap any exceptions that could be transient to inform users that they might want to enable connection resiliency.</span></span>  
3. <span data-ttu-id="04f4c-118">**DbExecutionStrategy**: cette classe est appropriée comme classe de base pour d’autres stratégies d’exécution, y compris celles personnalisées.</span><span class="sxs-lookup"><span data-stu-id="04f4c-118">**DbExecutionStrategy**: this class is suitable as a base class for other execution strategies, including your own custom ones.</span></span> <span data-ttu-id="04f4c-119">Il implémente une stratégie de nouvelle tentative exponentielle, où la nouvelle tentative initiale a lieu sans délai et le délai augmente de façon exponentielle jusqu’à ce que le nombre maximal de tentatives soit atteint.</span><span class="sxs-lookup"><span data-stu-id="04f4c-119">It implements an exponential retry policy, where the initial retry happens with zero delay and the delay increases exponentially until the maximum retry count is hit.</span></span> <span data-ttu-id="04f4c-120">Cette classe possède une méthode ShouldRetryOn abstraite qui peut être implémentée dans des stratégies d’exécution dérivées pour contrôler les exceptions qui doivent être retentées.</span><span class="sxs-lookup"><span data-stu-id="04f4c-120">This class has an abstract ShouldRetryOn method that can be implemented in derived execution strategies to control which exceptions should be retried.</span></span>  
4. <span data-ttu-id="04f4c-121">**SqlAzureExecutionStrategy**: cette stratégie d’exécution hérite de DbExecutionStrategy et réessaie sur les exceptions connues comme pouvant être transitoires lorsque vous utilisez Azure SQL Database.</span><span class="sxs-lookup"><span data-stu-id="04f4c-121">**SqlAzureExecutionStrategy**: this execution strategy inherits from DbExecutionStrategy and will retry on exceptions that are known to be possibly transient when working with Azure SQL Database.</span></span>

> [!NOTE]
> <span data-ttu-id="04f4c-122">Les stratégies d’exécution 2 et 4 sont incluses dans le fournisseur SQL Server fourni avec EF, qui se trouve dans l’assembly EntityFramework. SqlServer et qui sont conçus pour fonctionner avec SQL Server.</span><span class="sxs-lookup"><span data-stu-id="04f4c-122">Execution strategies 2 and 4 are included in the Sql Server provider that ships with EF, which is in the EntityFramework.SqlServer assembly and are designed to work with SQL Server.</span></span>  

## <a name="enabling-an-execution-strategy"></a><span data-ttu-id="04f4c-123">Activation d’une stratégie d’exécution</span><span class="sxs-lookup"><span data-stu-id="04f4c-123">Enabling an Execution Strategy</span></span>  

<span data-ttu-id="04f4c-124">Le moyen le plus simple pour indiquer à EF d’utiliser une stratégie d’exécution consiste à utiliser la méthode SetExecutionStrategy de la classe [DbConfiguration](xref:ef6/fundamentals/configuring/code-based) :</span><span class="sxs-lookup"><span data-stu-id="04f4c-124">The easiest way to tell EF to use an execution strategy is with the SetExecutionStrategy method of the [DbConfiguration](xref:ef6/fundamentals/configuring/code-based) class:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

<span data-ttu-id="04f4c-125">Ce code indique à EF d’utiliser le SqlAzureExecutionStrategy lors de la connexion à SQL Server.</span><span class="sxs-lookup"><span data-stu-id="04f4c-125">This code tells EF to use the SqlAzureExecutionStrategy when connecting to SQL Server.</span></span>  

## <a name="configuring-the-execution-strategy"></a><span data-ttu-id="04f4c-126">Configuration de la stratégie d’exécution</span><span class="sxs-lookup"><span data-stu-id="04f4c-126">Configuring the Execution Strategy</span></span>  

<span data-ttu-id="04f4c-127">Le constructeur de SqlAzureExecutionStrategy peut accepter deux paramètres, MaxRetryCount et MaxDelay.</span><span class="sxs-lookup"><span data-stu-id="04f4c-127">The constructor of SqlAzureExecutionStrategy can accept two parameters, MaxRetryCount and MaxDelay.</span></span> <span data-ttu-id="04f4c-128">Le nombre de MaxRetry est le nombre maximal de tentatives de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="04f4c-128">MaxRetry count is the maximum number of times that the strategy will retry.</span></span> <span data-ttu-id="04f4c-129">Le MaxDelay est un intervalle de temps qui représente le délai maximal entre les nouvelles tentatives que la stratégie d’exécution doit utiliser.</span><span class="sxs-lookup"><span data-stu-id="04f4c-129">The MaxDelay is a TimeSpan representing the maximum delay between retries that the execution strategy will use.</span></span>  

<span data-ttu-id="04f4c-130">Pour définir le nombre maximal de nouvelles tentatives sur 1 et le délai maximal à 30 secondes, vous devez exécuter la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="04f4c-130">To set the maximum number of retries to 1 and the maximum delay to 30 seconds you would execute the following:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy(
            "System.Data.SqlClient",
            () => new SqlAzureExecutionStrategy(1, TimeSpan.FromSeconds(30)));
    }
}
```  

<span data-ttu-id="04f4c-131">Le SqlAzureExecutionStrategy réessaie instantanément la première fois qu’un échec temporaire se produit, mais il retardera entre chaque nouvelle tentative jusqu’à ce que le nombre maximal de nouvelles tentatives soit dépassé ou que le délai total atteigne le délai maximal.</span><span class="sxs-lookup"><span data-stu-id="04f4c-131">The SqlAzureExecutionStrategy will retry instantly the first time a transient failure occurs, but will delay longer between each retry until either the max retry limit is exceeded or the total time hits the max delay.</span></span>  

<span data-ttu-id="04f4c-132">Les stratégies d’exécution renouvellent uniquement un nombre limité d’exceptions qui sont généralement temporaires, vous devrez toujours gérer d’autres erreurs et intercepter l’exception RetryLimitExceeded pour le cas où une erreur n’est pas temporaire ou prend trop de temps pour se résoudre elle-même.</span><span class="sxs-lookup"><span data-stu-id="04f4c-132">The execution strategies will only retry a limited number of exceptions that are usually transient, you will still need to handle other errors as well as catching the RetryLimitExceeded exception for the case where an error is not transient or takes too long to resolve itself.</span></span>  

<span data-ttu-id="04f4c-133">Il existe des limitations connues en cas d’utilisation d’une stratégie de nouvelle tentative d’exécution :</span><span class="sxs-lookup"><span data-stu-id="04f4c-133">There are some known of limitations when using a retrying execution strategy:</span></span>  

## <a name="streaming-queries-are-not-supported"></a><span data-ttu-id="04f4c-134">Les requêtes de streaming ne sont pas prises en charge</span><span class="sxs-lookup"><span data-stu-id="04f4c-134">Streaming queries are not supported</span></span>  

<span data-ttu-id="04f4c-135">Par défaut, EF6 et versions ultérieures effectuent la mise en mémoire tampon des résultats de la requête au lieu de les diffuser.</span><span class="sxs-lookup"><span data-stu-id="04f4c-135">By default, EF6 and later version will buffer query results rather than streaming them.</span></span> <span data-ttu-id="04f4c-136">Si vous souhaitez que les résultats soient diffusés en continu, vous pouvez utiliser la méthode AsStreaming pour modifier une requête LINQ to Entities en streaming.</span><span class="sxs-lookup"><span data-stu-id="04f4c-136">If you want to have results streamed you can use the AsStreaming method to change a LINQ to Entities query to streaming.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

<span data-ttu-id="04f4c-137">La diffusion en continu n’est pas prise en charge lorsqu’une stratégie d’exécution de nouvelle tentative est inscrite.</span><span class="sxs-lookup"><span data-stu-id="04f4c-137">Streaming is not supported when a retrying execution strategy is registered.</span></span> <span data-ttu-id="04f4c-138">Cette limitation existe, car la connexion peut supprimer de façon partielle les résultats retournés.</span><span class="sxs-lookup"><span data-stu-id="04f4c-138">This limitation exists because the connection could drop part way through the results being returned.</span></span> <span data-ttu-id="04f4c-139">Dans ce cas, EF doit réexécuter l’intégralité de la requête, mais n’a pas de moyen fiable de savoir quels résultats ont déjà été retournés (les données ont peut-être été modifiées depuis l’envoi de la requête initiale, les résultats peuvent être renvoyés dans un ordre différent, les résultats peuvent ne pas avoir un identificateur unique, etc.).</span><span class="sxs-lookup"><span data-stu-id="04f4c-139">When this occurs, EF needs to re-run the entire query but has no reliable way of knowing which results have already been returned (data may have changed since the initial query was sent, results may come back in a different order, results may not have a unique identifier, etc.).</span></span>  

## <a name="user-initiated-transactions-are-not-supported"></a><span data-ttu-id="04f4c-140">Les transactions initiées par l’utilisateur ne sont pas prises en charge</span><span class="sxs-lookup"><span data-stu-id="04f4c-140">User initiated transactions are not supported</span></span>  

<span data-ttu-id="04f4c-141">Lorsque vous avez configuré une stratégie d’exécution qui se traduit par de nouvelles tentatives, il existe des restrictions concernant l’utilisation des transactions.</span><span class="sxs-lookup"><span data-stu-id="04f4c-141">When you have configured an execution strategy that results in retries, there are some limitations around the use of transactions.</span></span>  

<span data-ttu-id="04f4c-142">Par défaut, EF effectue toutes les mises à jour de la base de données dans une transaction.</span><span class="sxs-lookup"><span data-stu-id="04f4c-142">By default, EF will perform any database updates within a transaction.</span></span> <span data-ttu-id="04f4c-143">Vous n’avez rien à faire pour activer cela, EF le fait toujours automatiquement.</span><span class="sxs-lookup"><span data-stu-id="04f4c-143">You don’t need to do anything to enable this, EF always does this automatically.</span></span>  

<span data-ttu-id="04f4c-144">Par exemple, dans le code suivant, SaveChanges est exécuté automatiquement dans une transaction.</span><span class="sxs-lookup"><span data-stu-id="04f4c-144">For example, in the following code SaveChanges is automatically performed within a transaction.</span></span> <span data-ttu-id="04f4c-145">Si SaveChanges devait échouer après l’insertion de l’un des nouveaux sites, la transaction est restaurée et aucune modification n’est appliquée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="04f4c-145">If SaveChanges were to fail after inserting one of the new Site’s then the transaction would be rolled back and no changes applied to the database.</span></span> <span data-ttu-id="04f4c-146">Le contexte est également laissé dans un État qui permet d’appeler à nouveau SaveChanges pour réessayer d’appliquer les modifications.</span><span class="sxs-lookup"><span data-stu-id="04f4c-146">The context is also left in a state that allows SaveChanges to be called again to retry applying the changes.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

<span data-ttu-id="04f4c-147">Lorsque vous n’utilisez pas une stratégie de nouvelle tentative d’exécution, vous pouvez encapsuler plusieurs opérations dans une transaction unique.</span><span class="sxs-lookup"><span data-stu-id="04f4c-147">When not using a retrying execution strategy you can wrap multiple operations in a single transaction.</span></span> <span data-ttu-id="04f4c-148">Par exemple, le code suivant encapsule deux appels de SaveChanges dans une transaction unique.</span><span class="sxs-lookup"><span data-stu-id="04f4c-148">For example, the following code wraps two SaveChanges calls in a single transaction.</span></span> <span data-ttu-id="04f4c-149">Si une partie de l’une ou l’autre des opérations échoue, aucune des modifications n’est appliquée.</span><span class="sxs-lookup"><span data-stu-id="04f4c-149">If any part of either operation fails then none of the changes are applied.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Site { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }
}
```  

<span data-ttu-id="04f4c-150">Cela n’est pas pris en charge lors de l’utilisation d’une stratégie de nouvelle tentative d’exécution, car EF n’est pas conscient des opérations précédentes et de la manière de les réessayer.</span><span class="sxs-lookup"><span data-stu-id="04f4c-150">This is not supported when using a retrying execution strategy because EF isn’t aware of any previous operations and how to retry them.</span></span> <span data-ttu-id="04f4c-151">Par exemple, si le deuxième SaveChanges a échoué, EF n’a plus les informations requises pour réessayer le premier appel SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="04f4c-151">For example, if the second SaveChanges failed then EF no longer has the required information to retry the first SaveChanges call.</span></span>  

### <a name="solution-manually-call-execution-strategy"></a><span data-ttu-id="04f4c-152">Solution : appeler manuellement la stratégie d’exécution</span><span class="sxs-lookup"><span data-stu-id="04f4c-152">Solution: Manually Call Execution Strategy</span></span>  

<span data-ttu-id="04f4c-153">La solution consiste à utiliser manuellement la stratégie d’exécution et à lui attribuer l’ensemble de la logique à exécuter, afin qu’elle puisse réessayer tout en cas d’échec de l’une des opérations.</span><span class="sxs-lookup"><span data-stu-id="04f4c-153">The solution is to manually use the execution strategy and give it the entire set of logic to be run, so that it can retry everything if one of the operations fails.</span></span> <span data-ttu-id="04f4c-154">Lorsqu’une stratégie d’exécution dérivée de DbExecutionStrategy est en cours d’exécution, elle interrompt la stratégie d’exécution implicite utilisée dans SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="04f4c-154">When an execution strategy derived from DbExecutionStrategy is running it will suspend the implicit execution strategy used in SaveChanges.</span></span>  

<span data-ttu-id="04f4c-155">Notez que tous les contextes doivent être construits dans le bloc de code pour être retentés.</span><span class="sxs-lookup"><span data-stu-id="04f4c-155">Note that any contexts should be constructed within the code block to be retried.</span></span> <span data-ttu-id="04f4c-156">Cela garantit que nous démarrons avec un état propre pour chaque nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="04f4c-156">This ensures that we are starting with a clean state for each retry.</span></span>  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

executionStrategy.Execute(
    () =>
    {
        using (var db = new BloggingContext())
        {
            using (var trn = db.Database.BeginTransaction())
            {
                db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                db.SaveChanges();

                db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
                db.SaveChanges();

                trn.Commit();
            }
        }
    });
```  
