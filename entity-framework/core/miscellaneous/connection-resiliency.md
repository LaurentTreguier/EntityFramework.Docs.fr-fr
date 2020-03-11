---
title: Résilience de connexion-EF Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 07646e6ead845c38537945a03367ac7f50784236
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416640"
---
# <a name="connection-resiliency"></a><span data-ttu-id="ab4cd-102">Résilience des connexions</span><span class="sxs-lookup"><span data-stu-id="ab4cd-102">Connection Resiliency</span></span>

<span data-ttu-id="ab4cd-103">La résilience des connexions réessaie automatiquement les commandes de base de données ayant échoué.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-103">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="ab4cd-104">La fonctionnalité peut être utilisée avec n’importe quelle base de données en fournissant une « stratégie d’exécution », qui encapsule la logique nécessaire pour détecter les échecs et les commandes de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-104">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="ab4cd-105">Les fournisseurs de EF Core peuvent fournir des stratégies d’exécution adaptées à leurs conditions d’échec de base de données spécifiques et aux stratégies de nouvelle tentative optimales.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-105">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="ab4cd-106">Par exemple, le fournisseur SQL Server inclut une stratégie d’exécution spécifiquement adaptée à SQL Server (y compris SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="ab4cd-106">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="ab4cd-107">Il reconnaît les types d’exception qui peuvent être retentés et qui ont des valeurs par défaut sensibles pour les nouvelles tentatives, le délai entre les nouvelles tentatives, etc.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-107">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="ab4cd-108">Une stratégie d’exécution est spécifiée lors de la configuration des options de votre contexte.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-108">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="ab4cd-109">En général, il s’agit de la méthode `OnConfiguring` de votre contexte dérivé :</span><span class="sxs-lookup"><span data-stu-id="ab4cd-109">This is typically in the `OnConfiguring` method of your derived context:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

<span data-ttu-id="ab4cd-110">ou `Startup.cs` pour une application ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="ab4cd-110">or in `Startup.cs` for an ASP.NET Core application:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a><span data-ttu-id="ab4cd-111">Stratégie d’exécution personnalisée</span><span class="sxs-lookup"><span data-stu-id="ab4cd-111">Custom execution strategy</span></span>

<span data-ttu-id="ab4cd-112">Il existe un mécanisme permettant d’inscrire votre propre stratégie d’exécution personnalisée si vous souhaitez modifier les valeurs par défaut.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-112">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="ab4cd-113">Stratégies et transactions d’exécution</span><span class="sxs-lookup"><span data-stu-id="ab4cd-113">Execution strategies and transactions</span></span>

<span data-ttu-id="ab4cd-114">Une stratégie d’exécution qui effectue automatiquement de nouvelles tentatives en cas de défaillance doit pouvoir lire chaque opération dans un bloc de nouvelle tentative qui échoue.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-114">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="ab4cd-115">Lorsque les nouvelles tentatives sont activées, chaque opération que vous effectuez via EF Core devient sa propre opération reproductibles.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-115">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="ab4cd-116">Autrement dit, chaque requête et chaque appel à `SaveChanges()` sont retentés en tant qu’unité en cas de défaillance temporaire.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-116">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="ab4cd-117">Toutefois, si votre code lance une transaction à l’aide de `BeginTransaction()` vous définissez votre propre groupe d’opérations qui doivent être traitées comme une unité, et tout ce qui se trouve à l’intérieur de la transaction doit être lu en cas de défaillance.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-117">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="ab4cd-118">Si vous tentez d’effectuer cette opération lors de l’utilisation d’une stratégie d’exécution, vous recevrez une exception semblable à la suivante :</span><span class="sxs-lookup"><span data-stu-id="ab4cd-118">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="ab4cd-119">InvalidOperationException : la stratégie d’exécution configurée’SqlServerRetryingExecutionStrategy’ne prend pas en charge les transactions initiées par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-119">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="ab4cd-120">Utilisez la stratégie d’exécution retournée par « DbContext.Database.CreateExecutionStrategy() » pour exécuter toutes les opérations de la transaction en tant qu’ensemble pouvant être retenté.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-120">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="ab4cd-121">La solution consiste à appeler manuellement la stratégie d’exécution avec un délégué représentant tout ce qui doit être exécuté.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-121">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="ab4cd-122">En cas d’échec passager, la stratégie d’exécution appelle de nouveau le délégué.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-122">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

<span data-ttu-id="ab4cd-123">Cette approche peut également être utilisée avec les transactions ambiantes.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-123">This approach can also be used with ambient transactions.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="ab4cd-124">Échec de validation de transaction et problème idempotence</span><span class="sxs-lookup"><span data-stu-id="ab4cd-124">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="ab4cd-125">En général, en cas d’échec de la connexion, la transaction actuelle est annulée.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-125">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="ab4cd-126">Toutefois, si la connexion est abandonnée pendant la validation de la transaction, l’état résultant de la transaction est inconnu.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-126">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> 

<span data-ttu-id="ab4cd-127">Par défaut, la stratégie d’exécution retentera l’opération comme si la transaction était restaurée, mais si ce n’est pas le cas, cela entraînera une exception si le nouvel état de la base de données est incompatible ou risque d' **endommager les données** si l’opération ne repose pas sur un état particulier, par exemple lors de l’insertion d’une nouvelle ligne avec des valeurs de clé générées</span><span class="sxs-lookup"><span data-stu-id="ab4cd-127">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="ab4cd-128">Il existe plusieurs façons de traiter ce problème.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-128">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="ab4cd-129">Option 1-do (presque) Nothing</span><span class="sxs-lookup"><span data-stu-id="ab4cd-129">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="ab4cd-130">La probabilité d’un échec de connexion pendant la validation de la transaction est faible, ce qui peut être acceptable pour votre application d’échouer si cette condition se produit réellement.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-130">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="ab4cd-131">Toutefois, vous devez éviter d’utiliser des clés générées par le magasin afin de garantir qu’une exception est levée au lieu d’ajouter une ligne dupliquée.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-131">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="ab4cd-132">Envisagez d’utiliser une valeur de GUID générée par le client ou un générateur de valeur côté client.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-132">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="ab4cd-133">Option 2-reconstruire l’état de l’application</span><span class="sxs-lookup"><span data-stu-id="ab4cd-133">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="ab4cd-134">Ignore le `DbContext`actif.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-134">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="ab4cd-135">Créez un nouveau `DbContext` et restaurez l’état de votre application à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-135">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="ab4cd-136">Informe l’utilisateur que la dernière opération n’a peut-être pas été effectuée avec succès.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-136">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="ab4cd-137">Option 3-Ajouter une vérification de l’État</span><span class="sxs-lookup"><span data-stu-id="ab4cd-137">Option 3 - Add state verification</span></span>

<span data-ttu-id="ab4cd-138">Pour la plupart des opérations qui modifient l’état de la base de données, il est possible d’ajouter du code qui vérifie si elle a réussi.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-138">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="ab4cd-139">EF fournit une méthode d’extension pour faciliter ce `IExecutionStrategy.ExecuteInTransaction`.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-139">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="ab4cd-140">Cette méthode commence et valide une transaction et accepte également une fonction dans le paramètre `verifySucceeded` qui est appelée lorsqu’une erreur temporaire se produit pendant la validation de la transaction.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-140">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="ab4cd-141">Ici `SaveChanges` est appelée avec `acceptAllChangesOnSuccess` défini sur `false` pour éviter de modifier l’état de l’entité `Blog` à `Unchanged` si la `SaveChanges` est réussie.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-141">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="ab4cd-142">Cela permet à de retenter la même opération si la validation échoue et que la transaction est restaurée.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-142">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="ab4cd-143">Option 4 : suivre manuellement la transaction</span><span class="sxs-lookup"><span data-stu-id="ab4cd-143">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="ab4cd-144">Si vous devez utiliser des clés générées par le magasin ou si vous avez besoin d’une méthode générique pour gérer les échecs de validation qui ne dépendent pas de l’opération effectuée, il se peut qu’un ID qui est vérifié lorsque la validation échoue.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-144">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="ab4cd-145">Ajoutez une table à la base de données utilisée pour suivre l’état des transactions.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-145">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="ab4cd-146">Insérez une ligne dans la table au début de chaque transaction.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-146">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="ab4cd-147">Si la connexion échoue pendant la validation, vérifiez la présence de la ligne correspondante dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-147">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="ab4cd-148">Si la validation réussit, supprimez la ligne correspondante pour éviter la croissance de la table.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-148">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="ab4cd-149">Assurez-vous que le contexte utilisé pour la vérification a une stratégie d’exécution définie, car la connexion risque d’échouer à nouveau lors de la vérification en cas d’échec lors de la validation de la transaction.</span><span class="sxs-lookup"><span data-stu-id="ab4cd-149">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
