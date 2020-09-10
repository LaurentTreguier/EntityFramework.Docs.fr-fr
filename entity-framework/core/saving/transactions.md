---
title: 'Transactions : EF Core'
description: Gestion des transactions pour l’atomicité lors de l’enregistrement de données avec Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
uid: core/saving/transactions
ms.openlocfilehash: 2674fdc59cb08e5faf2302270629c446abc2670c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618930"
---
# <a name="using-transactions"></a><span data-ttu-id="f9949-103">Utilisation de transactions</span><span class="sxs-lookup"><span data-stu-id="f9949-103">Using Transactions</span></span>

<span data-ttu-id="f9949-104">Les transactions permettent à plusieurs opérations de base de données d’être traitées de manière atomique.</span><span class="sxs-lookup"><span data-stu-id="f9949-104">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="f9949-105">Si la transaction est validée, toutes les opérations sont appliquées avec succès à la base de données.</span><span class="sxs-lookup"><span data-stu-id="f9949-105">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="f9949-106">Si la transaction est restaurée, aucune des opérations n’est appliquée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="f9949-106">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="f9949-107">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="f9949-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="f9949-108">Comportement de transaction par défaut</span><span class="sxs-lookup"><span data-stu-id="f9949-108">Default transaction behavior</span></span>

<span data-ttu-id="f9949-109">Par défaut, si le fournisseur de base de données prend en charge les transactions, toutes les modifications dans un seul appel à `SaveChanges()` sont appliquées à une transaction.</span><span class="sxs-lookup"><span data-stu-id="f9949-109">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges()` are applied in a transaction.</span></span> <span data-ttu-id="f9949-110">Si certaines des modifications échouent, la transaction est annulée et aucune des modifications n’est appliquée à la base de données.</span><span class="sxs-lookup"><span data-stu-id="f9949-110">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="f9949-111">Cela signifie que `SaveChanges()` réussit complètement ou laisse la base de données non modifiée si une erreur se produit.</span><span class="sxs-lookup"><span data-stu-id="f9949-111">This means that `SaveChanges()` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="f9949-112">Pour la plupart des applications, ce comportement par défaut est suffisant.</span><span class="sxs-lookup"><span data-stu-id="f9949-112">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="f9949-113">Vous devez uniquement contrôler manuellement les transactions si les exigences de votre application le jugent nécessaire.</span><span class="sxs-lookup"><span data-stu-id="f9949-113">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="f9949-114">Contrôle des transactions</span><span class="sxs-lookup"><span data-stu-id="f9949-114">Controlling transactions</span></span>

<span data-ttu-id="f9949-115">Vous pouvez utiliser l’API `DbContext.Database` pour commencer, valider et annuler les transactions.</span><span class="sxs-lookup"><span data-stu-id="f9949-115">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="f9949-116">L’exemple suivant montre deux opérations `SaveChanges()` et une requête LINQ en cours d’exécution dans une transaction unique.</span><span class="sxs-lookup"><span data-stu-id="f9949-116">The following example shows two `SaveChanges()` operations and a LINQ query being executed in a single transaction.</span></span>

<span data-ttu-id="f9949-117">Tous les fournisseurs de base de données ne prennent pas en charge les transactions.</span><span class="sxs-lookup"><span data-stu-id="f9949-117">Not all database providers support transactions.</span></span> <span data-ttu-id="f9949-118">Certains fournisseurs peuvent lever une exception ou ne pas exécuter d’opération lorsque des API de transaction sont appelées.</span><span class="sxs-lookup"><span data-stu-id="f9949-118">Some providers may throw or no-op when transaction APIs are called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a><span data-ttu-id="f9949-119">Transaction à contexte croisé (bases de données relationnelles uniquement)</span><span class="sxs-lookup"><span data-stu-id="f9949-119">Cross-context transaction (relational databases only)</span></span>

<span data-ttu-id="f9949-120">Vous pouvez également partager une transaction sur plusieurs instances de contexte.</span><span class="sxs-lookup"><span data-stu-id="f9949-120">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="f9949-121">Cette fonctionnalité est disponible uniquement lorsque vous utilisez un fournisseur de base de données relationnelle, car elle requiert l’utilisation de `DbTransaction` et `DbConnection`, qui sont propres aux bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="f9949-121">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="f9949-122">Pour partager une transaction, les contextes doivent partager une `DbConnection` et une `DbTransaction`.</span><span class="sxs-lookup"><span data-stu-id="f9949-122">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="f9949-123">Autoriser la fourniture externe de la connexion</span><span class="sxs-lookup"><span data-stu-id="f9949-123">Allow connection to be externally provided</span></span>

<span data-ttu-id="f9949-124">Le partage d’une `DbConnection` nécessite la possibilité de passer une connexion dans un contexte lors de la construction.</span><span class="sxs-lookup"><span data-stu-id="f9949-124">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="f9949-125">Le moyen le plus simple pour autoriser la `DbConnection` à être fournie en externe, arrêtez d’utiliser la méthode `DbContext.OnConfiguring` pour configurer le contexte et créez les `DbContextOptions` en externe avant de les passer au constructeur de contexte.</span><span class="sxs-lookup"><span data-stu-id="f9949-125">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]  
> <span data-ttu-id="f9949-126">`DbContextOptionsBuilder` est l’API que vous avez utilisée dans `DbContext.OnConfiguring` pour configurer le contexte. Vous allez maintenant l’utiliser en externe pour créer `DbContextOptions`.</span><span class="sxs-lookup"><span data-stu-id="f9949-126">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="f9949-127">Une alternative consiste à continuer à utiliser `DbContext.OnConfiguring`, mais accepte une `DbConnection` qui est enregistrée et ensuite utilisée dans `DbContext.OnConfiguring`.</span><span class="sxs-lookup"><span data-stu-id="f9949-127">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

``` csharp
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

### <a name="share-connection-and-transaction"></a><span data-ttu-id="f9949-128">Partage de connexions et transactions</span><span class="sxs-lookup"><span data-stu-id="f9949-128">Share connection and transaction</span></span>

<span data-ttu-id="f9949-129">Vous pouvez désormais créer plusieurs instances de contexte qui partagent la même connexion.</span><span class="sxs-lookup"><span data-stu-id="f9949-129">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="f9949-130">Utilisez ensuite l’API `DbContext.Database.UseTransaction(DbTransaction)` pour inscrire les deux contextes dans la même transaction.</span><span class="sxs-lookup"><span data-stu-id="f9949-130">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="f9949-131">Utilisation de DbTransactions externes (bases de données relationnelles uniquement)</span><span class="sxs-lookup"><span data-stu-id="f9949-131">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="f9949-132">Si vous utilisez plusieurs technologies d’accès aux données pour accéder à une base de données relationnelle, vous souhaiterez partager une transaction entre les opérations effectuées par ces différentes technologies.</span><span class="sxs-lookup"><span data-stu-id="f9949-132">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="f9949-133">L’exemple suivant montre comment effectuer une opération ADO.NET SqlClient et une opération Entity Framework Core dans la même transaction.</span><span class="sxs-lookup"><span data-stu-id="f9949-133">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a><span data-ttu-id="f9949-134">Utilisation de System.Transactions</span><span class="sxs-lookup"><span data-stu-id="f9949-134">Using System.Transactions</span></span>

> [!NOTE]  
> <span data-ttu-id="f9949-135">Cette fonctionnalité est une nouveauté d’EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="f9949-135">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="f9949-136">Il est possible d’utiliser les transactions ambiantes si vous avez besoin de coordonner sur une plus grande portée.</span><span class="sxs-lookup"><span data-stu-id="f9949-136">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

<span data-ttu-id="f9949-137">Il est également possible de s’inscrire dans une transaction explicite.</span><span class="sxs-lookup"><span data-stu-id="f9949-137">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="f9949-138">Limitations de System.Transactions</span><span class="sxs-lookup"><span data-stu-id="f9949-138">Limitations of System.Transactions</span></span>  

1. <span data-ttu-id="f9949-139">EF Core s’appuie sur les fournisseurs de base de données pour implémenter la prise en charge de System.Transactions.</span><span class="sxs-lookup"><span data-stu-id="f9949-139">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="f9949-140">Bien que la prise en charge est assez courante parmi les fournisseurs ADO.NET de .NET Framework, l’API n’a été que récemment ajoutée à .NET Core et n’est par conséquent pas aussi répandue.</span><span class="sxs-lookup"><span data-stu-id="f9949-140">Although support is quite common among ADO.NET providers for .NET Framework, the API has only been recently added to .NET Core and hence support is not as widespread.</span></span> <span data-ttu-id="f9949-141">Si un fournisseur n’implémente pas la prise en charge de System.Transactions, il est possible que les appels à ces API soient complètement ignorés.</span><span class="sxs-lookup"><span data-stu-id="f9949-141">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="f9949-142">SqlClient pour .NET Core le prend en charge à compter de la version 2.1 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="f9949-142">SqlClient for .NET Core does support it from 2.1 onwards.</span></span> <span data-ttu-id="f9949-143">SqlClient pour .NET Core 2.0 lève une exception en cas de tentative d’utilisation de la fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="f9949-143">SqlClient for .NET Core 2.0 will throw an exception if you attempt to use the feature.</span></span>

   > [!IMPORTANT]  
   > <span data-ttu-id="f9949-144">Il est recommandé de vérifier que l’API se comporte correctement avec votre fournisseur avant de l’utiliser pour la gestion des transactions.</span><span class="sxs-lookup"><span data-stu-id="f9949-144">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="f9949-145">Nous vous invitons à contacter le chargé de maintenance du fournisseur de base de données si ce n’est pas le cas.</span><span class="sxs-lookup"><span data-stu-id="f9949-145">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="f9949-146">À compter de la version 2.1, l’implémentation de System.Transactions dans .NET Core n’inclut pas de prise en charge des transactions distribuées. Par conséquent, vous ne pouvez pas utiliser `TransactionScope` ou `CommittableTransaction` pour coordonner des transactions sur plusieurs gestionnaires de ressources.</span><span class="sxs-lookup"><span data-stu-id="f9949-146">As of version 2.1, the System.Transactions implementation in .NET Core does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>
