---
title: 'Transactions : EF Core'
description: Gestion des transactions pour l’atomicité lors de l’enregistrement de données avec Entity Framework Core
author: roji
ms.date: 9/26/2020
uid: core/saving/transactions
ms.openlocfilehash: 4e124747dc6dd9b57b6f60621c27d878d3f9be00
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106165909"
---
# <a name="using-transactions"></a><span data-ttu-id="86191-103">Utilisation de transactions</span><span class="sxs-lookup"><span data-stu-id="86191-103">Using Transactions</span></span>

<span data-ttu-id="86191-104">Les transactions permettent à plusieurs opérations de base de données d’être traitées de manière atomique.</span><span class="sxs-lookup"><span data-stu-id="86191-104">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="86191-105">Si la transaction est validée, toutes les opérations sont appliquées avec succès à la base de données.</span><span class="sxs-lookup"><span data-stu-id="86191-105">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="86191-106">Si la transaction est restaurée, aucune des opérations n’est appliquée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="86191-106">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]
> <span data-ttu-id="86191-107">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/Transactions/) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="86191-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="86191-108">Comportement de transaction par défaut</span><span class="sxs-lookup"><span data-stu-id="86191-108">Default transaction behavior</span></span>

<span data-ttu-id="86191-109">Par défaut, si le fournisseur de base de données prend en charge les transactions, toutes les modifications dans un seul appel à `SaveChanges` sont appliquées à une transaction.</span><span class="sxs-lookup"><span data-stu-id="86191-109">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges` are applied in a transaction.</span></span> <span data-ttu-id="86191-110">Si certaines des modifications échouent, la transaction est annulée et aucune des modifications n’est appliquée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="86191-110">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="86191-111">Cela signifie que `SaveChanges` réussit complètement ou laisse la base de données non modifiée si une erreur se produit.</span><span class="sxs-lookup"><span data-stu-id="86191-111">This means that `SaveChanges` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="86191-112">Pour la plupart des applications, ce comportement par défaut est suffisant.</span><span class="sxs-lookup"><span data-stu-id="86191-112">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="86191-113">Vous devez uniquement contrôler manuellement les transactions si les exigences de votre application le jugent nécessaire.</span><span class="sxs-lookup"><span data-stu-id="86191-113">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="86191-114">Contrôle des transactions</span><span class="sxs-lookup"><span data-stu-id="86191-114">Controlling transactions</span></span>

<span data-ttu-id="86191-115">Vous pouvez utiliser l’API `DbContext.Database` pour commencer, valider et annuler les transactions.</span><span class="sxs-lookup"><span data-stu-id="86191-115">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="86191-116">L’exemple suivant montre deux `SaveChanges` opérations et une requête LINQ en cours d’exécution dans une transaction unique :</span><span class="sxs-lookup"><span data-stu-id="86191-116">The following example shows two `SaveChanges` operations and a LINQ query being executed in a single transaction:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

<span data-ttu-id="86191-117">Bien que tous les fournisseurs de base de données relationnelle prennent en charge les transactions, les autres types de fournisseurs peuvent lever ou ne pas être opérationnels lorsque les API de transaction sont appelées.</span><span class="sxs-lookup"><span data-stu-id="86191-117">While all relational database providers support transactions, other providers types may throw or no-op when transaction APIs are called.</span></span>

## <a name="savepoints"></a><span data-ttu-id="86191-118">Points</span><span class="sxs-lookup"><span data-stu-id="86191-118">Savepoints</span></span>

> [!NOTE]
> <span data-ttu-id="86191-119">Cette fonctionnalité a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="86191-119">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="86191-120">Lorsque `SaveChanges` est appelé et qu’une transaction est déjà en cours sur le contexte, EF crée automatiquement un *point* de sauvegarde avant d’enregistrer les données.</span><span class="sxs-lookup"><span data-stu-id="86191-120">When `SaveChanges` is invoked and a transaction is already in progress on the context, EF automatically creates a *savepoint* before saving any data.</span></span> <span data-ttu-id="86191-121">Les points d’enregistrement sont des points dans une transaction de base de données qui peuvent être restaurés ultérieurement vers, si une erreur se produit ou pour toute autre raison.</span><span class="sxs-lookup"><span data-stu-id="86191-121">Savepoints are points within a database transaction which may later be rolled back to, if an error occurs or for any other reason.</span></span> <span data-ttu-id="86191-122">Si `SaveChanges` rencontre une erreur, il restaure automatiquement la transaction jusqu’au point de sauvegarde, ce qui laisse la transaction dans le même État que si elle n’avait jamais démarré.</span><span class="sxs-lookup"><span data-stu-id="86191-122">If `SaveChanges` encounters any error, it automatically rolls the transaction back to the savepoint, leaving the transaction in the same state as if it had never started.</span></span> <span data-ttu-id="86191-123">Cela vous permet de corriger éventuellement des problèmes et de réessayer d’enregistrer, en particulier lorsque des problèmes d' [accès concurrentiel optimiste](xref:core/saving/concurrency) se produisent.</span><span class="sxs-lookup"><span data-stu-id="86191-123">This allows you to possibly correct issues and retry saving, in particular when [optimistic concurrency](xref:core/saving/concurrency) issues occur.</span></span>

> [!WARNING]
> <span data-ttu-id="86191-124">Les points de sauvegarde sont incompatibles avec les jeux de résultats actifs multiples de SQL Server et ne sont pas utilisés.</span><span class="sxs-lookup"><span data-stu-id="86191-124">Savepoints are incompatible with SQL Server's Multiple Active Result Sets, and are not used.</span></span> <span data-ttu-id="86191-125">Si une erreur se produit pendant `SaveChanges` , la transaction peut être laissée dans un état inconnu.</span><span class="sxs-lookup"><span data-stu-id="86191-125">If an error occurs during `SaveChanges`, the transaction may be left in an unknown state.</span></span>

<span data-ttu-id="86191-126">Il est également possible de gérer manuellement les points de enregistrement, comme c’est le cas avec les transactions.</span><span class="sxs-lookup"><span data-stu-id="86191-126">It's also possible to manually manage savepoints, just as it is with transactions.</span></span> <span data-ttu-id="86191-127">L’exemple suivant crée un point de sauvegarde dans une transaction, puis le restaure en cas d’échec :</span><span class="sxs-lookup"><span data-stu-id="86191-127">The following example creates a savepoint within a transaction, and rolls back to it on failure:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a><span data-ttu-id="86191-128">Transaction de contexte croisé</span><span class="sxs-lookup"><span data-stu-id="86191-128">Cross-context transaction</span></span>

<span data-ttu-id="86191-129">Vous pouvez également partager une transaction sur plusieurs instances de contexte.</span><span class="sxs-lookup"><span data-stu-id="86191-129">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="86191-130">Cette fonctionnalité est disponible uniquement lorsque vous utilisez un fournisseur de base de données relationnelle, car elle requiert l’utilisation de `DbTransaction` et `DbConnection`, qui sont propres aux bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="86191-130">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="86191-131">Pour partager une transaction, les contextes doivent partager une `DbConnection` et une `DbTransaction`.</span><span class="sxs-lookup"><span data-stu-id="86191-131">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="86191-132">Autoriser la fourniture externe de la connexion</span><span class="sxs-lookup"><span data-stu-id="86191-132">Allow connection to be externally provided</span></span>

<span data-ttu-id="86191-133">Le partage d’une `DbConnection` nécessite la possibilité de passer une connexion dans un contexte lors de la construction.</span><span class="sxs-lookup"><span data-stu-id="86191-133">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="86191-134">Le moyen le plus simple pour autoriser la `DbConnection` à être fournie en externe, arrêtez d’utiliser la méthode `DbContext.OnConfiguring` pour configurer le contexte et créez les `DbContextOptions` en externe avant de les passer au constructeur de contexte.</span><span class="sxs-lookup"><span data-stu-id="86191-134">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]
> <span data-ttu-id="86191-135">`DbContextOptionsBuilder` est l’API que vous avez utilisée dans `DbContext.OnConfiguring` pour configurer le contexte. Vous allez maintenant l’utiliser en externe pour créer `DbContextOptions`.</span><span class="sxs-lookup"><span data-stu-id="86191-135">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="86191-136">Une alternative consiste à continuer à utiliser `DbContext.OnConfiguring`, mais accepte une `DbConnection` qui est enregistrée et ensuite utilisée dans `DbContext.OnConfiguring`.</span><span class="sxs-lookup"><span data-stu-id="86191-136">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

```csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a><span data-ttu-id="86191-137">Partage de connexions et transactions</span><span class="sxs-lookup"><span data-stu-id="86191-137">Share connection and transaction</span></span>

<span data-ttu-id="86191-138">Vous pouvez désormais créer plusieurs instances de contexte qui partagent la même connexion.</span><span class="sxs-lookup"><span data-stu-id="86191-138">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="86191-139">Utilisez ensuite l’API `DbContext.Database.UseTransaction(DbTransaction)` pour inscrire les deux contextes dans la même transaction.</span><span class="sxs-lookup"><span data-stu-id="86191-139">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="86191-140">Utilisation de DbTransactions externes (bases de données relationnelles uniquement)</span><span class="sxs-lookup"><span data-stu-id="86191-140">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="86191-141">Si vous utilisez plusieurs technologies d’accès aux données pour accéder à une base de données relationnelle, vous souhaiterez partager une transaction entre les opérations effectuées par ces différentes technologies.</span><span class="sxs-lookup"><span data-stu-id="86191-141">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="86191-142">L’exemple suivant montre comment effectuer une opération ADO.NET SqlClient et une opération Entity Framework Core dans la même transaction.</span><span class="sxs-lookup"><span data-stu-id="86191-142">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a><span data-ttu-id="86191-143">Utilisation de System.Transactions</span><span class="sxs-lookup"><span data-stu-id="86191-143">Using System.Transactions</span></span>

<span data-ttu-id="86191-144">Il est possible d’utiliser les transactions ambiantes si vous avez besoin de coordonner sur une plus grande portée.</span><span class="sxs-lookup"><span data-stu-id="86191-144">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

<span data-ttu-id="86191-145">Il est également possible de s’inscrire dans une transaction explicite.</span><span class="sxs-lookup"><span data-stu-id="86191-145">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="86191-146">Limitations de System.Transactions</span><span class="sxs-lookup"><span data-stu-id="86191-146">Limitations of System.Transactions</span></span>

1. <span data-ttu-id="86191-147">EF Core s’appuie sur les fournisseurs de base de données pour implémenter la prise en charge de System.Transactions.</span><span class="sxs-lookup"><span data-stu-id="86191-147">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="86191-148">Si un fournisseur n’implémente pas la prise en charge de System.Transactions, il est possible que les appels à ces API soient complètement ignorés.</span><span class="sxs-lookup"><span data-stu-id="86191-148">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="86191-149">SqlClient le prend en charge.</span><span class="sxs-lookup"><span data-stu-id="86191-149">SqlClient supports it.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="86191-150">Il est recommandé de vérifier que l’API se comporte correctement avec votre fournisseur avant de l’utiliser pour la gestion des transactions.</span><span class="sxs-lookup"><span data-stu-id="86191-150">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="86191-151">Nous vous invitons à contacter le chargé de maintenance du fournisseur de base de données si ce n’est pas le cas.</span><span class="sxs-lookup"><span data-stu-id="86191-151">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="86191-152">L’implémentation .NET Core de System. transactions n’inclut pas actuellement la prise en charge des transactions distribuées. par conséquent, vous ne pouvez pas utiliser `TransactionScope` ou `CommittableTransaction` pour coordonner des transactions sur plusieurs gestionnaires de ressources.</span><span class="sxs-lookup"><span data-stu-id="86191-152">The .NET Core implementation of System.Transactions does not currently include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span> <span data-ttu-id="86191-153">Le support est suivi par [ce problème](https://github.com/dotnet/runtime/issues/715).</span><span class="sxs-lookup"><span data-stu-id="86191-153">Support is tracked by [this issue](https://github.com/dotnet/runtime/issues/715).</span></span>
