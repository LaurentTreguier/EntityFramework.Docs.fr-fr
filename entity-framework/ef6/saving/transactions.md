---
title: Utilisation des transactions-EF6
description: Utilisation des transactions dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/transactions
ms.openlocfilehash: 525b5cf605c1b61225ee2b9f1e0559a8e13f3052
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064430"
---
# <a name="working-with-transactions"></a><span data-ttu-id="4916a-103">Utilisation des transactions</span><span class="sxs-lookup"><span data-stu-id="4916a-103">Working with Transactions</span></span>
> [!NOTE]
> <span data-ttu-id="4916a-104">**EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="4916a-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="4916a-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="4916a-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="4916a-106">Ce document décrit l’utilisation des transactions dans EF6, y compris les améliorations que nous avons ajoutées depuis EF5 pour faciliter l’utilisation des transactions.</span><span class="sxs-lookup"><span data-stu-id="4916a-106">This document will describe using transactions in EF6 including the enhancements we have added since EF5 to make working with transactions easy.</span></span>  

## <a name="what-ef-does-by-default"></a><span data-ttu-id="4916a-107">Par défaut, EF</span><span class="sxs-lookup"><span data-stu-id="4916a-107">What EF does by default</span></span>  

<span data-ttu-id="4916a-108">Dans toutes les versions de Entity Framework, chaque fois que vous exécutez **SaveChanges ()** pour insérer, mettre à jour ou supprimer sur la base de données, l’infrastructure encapsule cette opération dans une transaction.</span><span class="sxs-lookup"><span data-stu-id="4916a-108">In all versions of Entity Framework, whenever you execute **SaveChanges()** to insert, update or delete on the database the framework will wrap that operation in a transaction.</span></span> <span data-ttu-id="4916a-109">Cette transaction ne dure que suffisamment longtemps pour exécuter l’opération, puis se termine.</span><span class="sxs-lookup"><span data-stu-id="4916a-109">This transaction lasts only long enough to execute the operation and then completes.</span></span> <span data-ttu-id="4916a-110">Lorsque vous exécutez une autre opération, une nouvelle transaction est démarrée.</span><span class="sxs-lookup"><span data-stu-id="4916a-110">When you execute another such operation a new transaction is started.</span></span>  

<span data-ttu-id="4916a-111">À compter de EF6 **Database.ExecuteSqlCommand ()** par défaut encapsule la commande dans une transaction, si celle-ci n’était pas déjà présente.</span><span class="sxs-lookup"><span data-stu-id="4916a-111">Starting with EF6 **Database.ExecuteSqlCommand()** by default will wrap the command in a transaction if one was not already present.</span></span> <span data-ttu-id="4916a-112">Il existe des surcharges de cette méthode qui vous permettent de substituer ce comportement si vous le souhaitez.</span><span class="sxs-lookup"><span data-stu-id="4916a-112">There are overloads of this method that allow you to override this behavior if you wish.</span></span> <span data-ttu-id="4916a-113">En outre, dans EF6, l’exécution de procédures stockées incluses dans le modèle via des API telles que **ObjectContext.ExecuteFunction ()** fait de même (sauf que le comportement par défaut ne peut pas être remplacé).</span><span class="sxs-lookup"><span data-stu-id="4916a-113">Also in EF6 execution of stored procedures included in the model through APIs such as **ObjectContext.ExecuteFunction()** does the same (except that the default behavior cannot at the moment be overridden).</span></span>  

<span data-ttu-id="4916a-114">Dans les deux cas, le niveau d’isolation de la transaction est le niveau d’isolation que le fournisseur de base de données considère comme son paramètre par défaut.</span><span class="sxs-lookup"><span data-stu-id="4916a-114">In either case, the isolation level of the transaction is whatever isolation level the database provider considers its default setting.</span></span> <span data-ttu-id="4916a-115">Par défaut, par exemple, sur SQL Server il s’agit de READ COMMITTED.</span><span class="sxs-lookup"><span data-stu-id="4916a-115">By default, for instance, on SQL Server this is READ COMMITTED.</span></span>  

<span data-ttu-id="4916a-116">Entity Framework n’encapsule pas les requêtes dans une transaction.</span><span class="sxs-lookup"><span data-stu-id="4916a-116">Entity Framework does not wrap queries in a transaction.</span></span>  

<span data-ttu-id="4916a-117">Cette fonctionnalité par défaut convient à un grand nombre d’utilisateurs et, si tel est le cas, il n’est pas nécessaire de faire quelque chose de différent dans EF6. il vous suffit d’écrire le code comme vous l’avez toujours fait.</span><span class="sxs-lookup"><span data-stu-id="4916a-117">This default functionality is suitable for a lot of users and if so there is no need to do anything different in EF6; just write the code as you always did.</span></span>  

<span data-ttu-id="4916a-118">Toutefois, certains utilisateurs ont besoin d’un meilleur contrôle sur leurs transactions. cela est abordé dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="4916a-118">However some users require greater control over their transactions – this is covered in the following sections.</span></span>  

## <a name="how-the-apis-work"></a><span data-ttu-id="4916a-119">Fonctionnement des API</span><span class="sxs-lookup"><span data-stu-id="4916a-119">How the APIs work</span></span>  

<span data-ttu-id="4916a-120">Avant EF6 Entity Framework insistait sur l’ouverture de la connexion de base de données elle-même (une exception a été levée si une connexion déjà ouverte était passée).</span><span class="sxs-lookup"><span data-stu-id="4916a-120">Prior to EF6 Entity Framework insisted on opening the database connection itself (it threw an exception if it was passed a connection that was already open).</span></span> <span data-ttu-id="4916a-121">Étant donné qu’une transaction ne peut être démarrée que sur une connexion ouverte, cela signifiait que le seul moyen pour un utilisateur d’encapsuler plusieurs opérations dans une seule transaction était d’utiliser [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) ou d’utiliser la propriété **ObjectContext. Connection** et de commencer à appeler **Open ()** et **BeginTransaction ()** directement sur l’objet **EntityConnection** renvoyé.</span><span class="sxs-lookup"><span data-stu-id="4916a-121">Since a transaction can only be started on an open connection, this meant that the only way a user could wrap several operations into one transaction was either to use a [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) or use the **ObjectContext.Connection** property and start calling **Open()** and **BeginTransaction()** directly on the returned **EntityConnection** object.</span></span> <span data-ttu-id="4916a-122">En outre, les appels d’API qui ont contacté la base de données échouent si vous avez démarré une transaction sur la connexion de base de données sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="4916a-122">In addition, API calls which contacted the database would fail if you had started a transaction on the underlying database connection on your own.</span></span>  

> [!NOTE]
> <span data-ttu-id="4916a-123">La limitation de l’acceptation des connexions fermées a été supprimée dans Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="4916a-123">The limitation of only accepting closed connections was removed in Entity Framework 6.</span></span> <span data-ttu-id="4916a-124">Pour plus d’informations, consultez [gestion des connexions](xref:ef6/fundamentals/connection-management).</span><span class="sxs-lookup"><span data-stu-id="4916a-124">For details, see [Connection Management](xref:ef6/fundamentals/connection-management).</span></span>  

<span data-ttu-id="4916a-125">À compter de EF6, le Framework fournit désormais :</span><span class="sxs-lookup"><span data-stu-id="4916a-125">Starting with EF6 the framework now provides:</span></span>  

1. <span data-ttu-id="4916a-126">**Database. BeginTransaction ()** : méthode plus simple permettant à un utilisateur de démarrer et d’exécuter des transactions proprement dites à l’intérieur d’un DbContext existant, ce qui permet de combiner plusieurs opérations au sein de la même transaction et, par conséquent, toutes validées ou toutes restaurées comme une seule.</span><span class="sxs-lookup"><span data-stu-id="4916a-126">**Database.BeginTransaction()** : An easier method for a user to start and complete transactions themselves within an existing DbContext – allowing several operations to be combined within the same transaction and hence either all committed or all rolled back as one.</span></span> <span data-ttu-id="4916a-127">Il permet également à l’utilisateur de spécifier plus facilement le niveau d’isolation de la transaction.</span><span class="sxs-lookup"><span data-stu-id="4916a-127">It also allows the user to more easily specify the isolation level for the transaction.</span></span>  
2. <span data-ttu-id="4916a-128">**Database. UseTransaction ()** : qui permet à DbContext d’utiliser une transaction qui a été démarrée en dehors de la Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="4916a-128">**Database.UseTransaction()** : which allows the DbContext to use a transaction which was started outside of the Entity Framework.</span></span>  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a><span data-ttu-id="4916a-129">Combinaison de plusieurs opérations en une seule transaction dans le même contexte</span><span class="sxs-lookup"><span data-stu-id="4916a-129">Combining several operations into one transaction within the same context</span></span>  

<span data-ttu-id="4916a-130">**Database. BeginTransaction ()** a deux remplacements : un qui accepte un [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) explicite et un qui n’accepte aucun argument et utilise la valeur IsolationLevel par défaut du fournisseur de base de données sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="4916a-130">**Database.BeginTransaction()** has two overrides – one which takes an explicit [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) and one which takes no arguments and uses the default IsolationLevel from the underlying database provider.</span></span> <span data-ttu-id="4916a-131">Les deux substitutions retournent un objet **DbContextTransaction** qui fournit des méthodes **Commit ()** et **Rollback ()** qui effectuent des validations et des restaurations sur la transaction de magasin sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="4916a-131">Both overrides return a **DbContextTransaction** object which provides **Commit()** and **Rollback()** methods which perform commit and rollback on the underlying store transaction.</span></span>  

<span data-ttu-id="4916a-132">Le **DbContextTransaction** est destiné à être supprimé une fois qu’il a été validé ou restauré.</span><span class="sxs-lookup"><span data-stu-id="4916a-132">The **DbContextTransaction** is meant to be disposed once it has been committed or rolled back.</span></span> <span data-ttu-id="4916a-133">Pour ce faire, une méthode simple consiste à **utiliser (...) {...}**</span><span class="sxs-lookup"><span data-stu-id="4916a-133">One easy way to accomplish this is the **using(…) {…}**</span></span> <span data-ttu-id="4916a-134">syntaxe qui appellera automatiquement **dispose ()** lorsque le bloc using se termine :</span><span class="sxs-lookup"><span data-stu-id="4916a-134">syntax which will automatically call **Dispose()** when the using block completes:</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void StartOwnTransactionWithinContext()
        {
            using (var context = new BloggingContext())
            {
                using (var dbContextTransaction = context.Database.BeginTransaction())
                {
                    context.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'"
                        );

                    var query = context.Posts.Where(p => p.Blog.Rating >= 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }

                    context.SaveChanges();

                    dbContextTransaction.Commit();
                }
            }
        }
    }
}
```  

> [!NOTE]
> <span data-ttu-id="4916a-135">Le démarrage d’une transaction nécessite que la connexion à la banque sous-jacente soit ouverte.</span><span class="sxs-lookup"><span data-stu-id="4916a-135">Beginning a transaction requires that the underlying store connection is open.</span></span> <span data-ttu-id="4916a-136">L’appel de Database. BeginTransaction () entraîne l’ouverture de la connexion si elle n’est pas déjà ouverte.</span><span class="sxs-lookup"><span data-stu-id="4916a-136">So calling Database.BeginTransaction() will open the connection  if it is not already opened.</span></span> <span data-ttu-id="4916a-137">Si DbContextTransaction a ouvert la connexion, il la ferme quand la méthode Dispose () est appelée.</span><span class="sxs-lookup"><span data-stu-id="4916a-137">If DbContextTransaction opened the connection then it will close it when Dispose() is called.</span></span>  

### <a name="passing-an-existing-transaction-to-the-context"></a><span data-ttu-id="4916a-138">Passage d’une transaction existante au contexte</span><span class="sxs-lookup"><span data-stu-id="4916a-138">Passing an existing transaction to the context</span></span>  

<span data-ttu-id="4916a-139">Parfois, vous souhaitez une transaction qui est encore plus large dans l’étendue et qui comprend des opérations sur la même base de données mais en dehors d’EF.</span><span class="sxs-lookup"><span data-stu-id="4916a-139">Sometimes you would like a transaction which is even broader in scope and which includes operations on the same database but outside of EF completely.</span></span> <span data-ttu-id="4916a-140">Pour ce faire, vous devez ouvrir la connexion et démarrer la transaction vous-même, puis indiquer à EF a) d’utiliser la connexion de base de données déjà ouverte et b) pour utiliser la transaction existante sur cette connexion.</span><span class="sxs-lookup"><span data-stu-id="4916a-140">To accomplish this you must open the connection and start the transaction yourself and then tell EF a) to use the already-opened database connection, and b) to use the existing transaction on that connection.</span></span>  

<span data-ttu-id="4916a-141">Pour ce faire, vous devez définir et utiliser un constructeur sur votre classe de contexte qui hérite de l’un des constructeurs DbContext qui prend i) un paramètre de connexion existant et II) la valeur booléenne contextOwnsConnection.</span><span class="sxs-lookup"><span data-stu-id="4916a-141">To do this you must define and use a constructor on your context class which inherits from one of the DbContext constructors which take i) an existing connection parameter and ii) the contextOwnsConnection boolean.</span></span>  

> [!NOTE]
> <span data-ttu-id="4916a-142">L’indicateur contextOwnsConnection doit avoir la valeur false lorsqu’il est appelé dans ce scénario.</span><span class="sxs-lookup"><span data-stu-id="4916a-142">The contextOwnsConnection flag must be set to false when called in this scenario.</span></span> <span data-ttu-id="4916a-143">Cela est important car il informe Entity Framework qu’il ne doit pas fermer la connexion quand elle l’a fait (par exemple, consultez la ligne 4 ci-dessous) :</span><span class="sxs-lookup"><span data-stu-id="4916a-143">This is important as it informs Entity Framework that it should not close the connection when it is done with it (for example, see line 4 below):</span></span>  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

<span data-ttu-id="4916a-144">En outre, vous devez démarrer la transaction vous-même (y compris la fonction IsolationLevel si vous souhaitez éviter le paramètre par défaut) et laisser Entity Framework savoir qu’une transaction existante a déjà démarré sur la connexion (voir la ligne 33 ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="4916a-144">Furthermore, you must start the transaction yourself (including the IsolationLevel if you want to avoid the default setting) and let Entity Framework know that there is an existing transaction already started on the connection (see line 33 below).</span></span>  

<span data-ttu-id="4916a-145">Vous êtes alors libre d’exécuter des opérations de base de données directement sur le SqlConnection lui-même ou sur DbContext.</span><span class="sxs-lookup"><span data-stu-id="4916a-145">Then you are free to execute database operations either directly on the SqlConnection itself, or on the DbContext.</span></span> <span data-ttu-id="4916a-146">Toutes ces opérations sont exécutées dans une transaction.</span><span class="sxs-lookup"><span data-stu-id="4916a-146">All such operations are executed within one transaction.</span></span> <span data-ttu-id="4916a-147">Vous êtes responsable de la validation ou de la restauration de la transaction et de l’appel de dispose () sur celle-ci, ainsi que pour la fermeture et la suppression de la connexion de base de données.</span><span class="sxs-lookup"><span data-stu-id="4916a-147">You take responsibility for committing or rolling back the transaction and for calling Dispose() on it, as well as for closing and disposing the database connection.</span></span> <span data-ttu-id="4916a-148">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4916a-148">For example:</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
     class TransactionsExample
     {
        static void UsingExternalTransaction()
        {
            using (var conn = new SqlConnection("..."))
            {
               conn.Open();

               using (var sqlTxn = conn.BeginTransaction(System.Data.IsolationLevel.Snapshot))
               {
                   var sqlCommand = new SqlCommand();
                   sqlCommand.Connection = conn;
                   sqlCommand.Transaction = sqlTxn;
                   sqlCommand.CommandText =
                       @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'";
                   sqlCommand.ExecuteNonQuery();

                   using (var context =  
                     new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        context.Database.UseTransaction(sqlTxn);

                        var query =  context.Posts.Where(p => p.Blog.Rating >= 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                       context.SaveChanges();
                    }

                    sqlTxn.Commit();
                }
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a><span data-ttu-id="4916a-149">Effacement de la transaction</span><span class="sxs-lookup"><span data-stu-id="4916a-149">Clearing up the transaction</span></span>

<span data-ttu-id="4916a-150">Vous pouvez passer la valeur null à Database. UseTransaction () pour effacer Entity Framework connaissance de la transaction actuelle.</span><span class="sxs-lookup"><span data-stu-id="4916a-150">You can pass null to Database.UseTransaction() to clear Entity Framework’s knowledge of the current transaction.</span></span> <span data-ttu-id="4916a-151">Entity Framework ne valide pas ou ne restaure pas la transaction existante lorsque vous procédez ainsi, utilisez avec précaution et uniquement si vous êtes sûr que c’est ce que vous voulez faire.</span><span class="sxs-lookup"><span data-stu-id="4916a-151">Entity Framework will neither commit nor rollback the existing transaction when you do this, so use with care and only if you’re sure this is what you want to do.</span></span>  

### <a name="errors-in-usetransaction"></a><span data-ttu-id="4916a-152">Erreurs dans UseTransaction</span><span class="sxs-lookup"><span data-stu-id="4916a-152">Errors in UseTransaction</span></span>

<span data-ttu-id="4916a-153">Vous verrez une exception de Database. UseTransaction () si vous transmettez une transaction lorsque :</span><span class="sxs-lookup"><span data-stu-id="4916a-153">You will see an exception from Database.UseTransaction() if you pass a transaction when:</span></span>  
- <span data-ttu-id="4916a-154">Entity Framework a déjà une transaction existante</span><span class="sxs-lookup"><span data-stu-id="4916a-154">Entity Framework already has an existing transaction</span></span>  
- <span data-ttu-id="4916a-155">Entity Framework est déjà en cours d’utilisation dans un TransactionScope</span><span class="sxs-lookup"><span data-stu-id="4916a-155">Entity Framework is already operating within a TransactionScope</span></span>  
- <span data-ttu-id="4916a-156">L’objet de connexion dans la transaction passée a la valeur null.</span><span class="sxs-lookup"><span data-stu-id="4916a-156">The connection object in the transaction passed is null.</span></span> <span data-ttu-id="4916a-157">Autrement dit, la transaction n’est pas associée à une connexion. il s’agit généralement d’un signe que la transaction est déjà terminée.</span><span class="sxs-lookup"><span data-stu-id="4916a-157">That is, the transaction is not associated with a connection – usually this is a sign that that transaction has already completed</span></span>  
- <span data-ttu-id="4916a-158">L’objet de connexion dans la transaction passée ne correspond pas à la connexion de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="4916a-158">The connection object in the transaction passed does not match the Entity Framework’s connection.</span></span>  

## <a name="using-transactions-with-other-features"></a><span data-ttu-id="4916a-159">Utilisation de transactions avec d’autres fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="4916a-159">Using transactions with other features</span></span>  

<span data-ttu-id="4916a-160">Cette section explique en détail comment les transactions ci-dessus interagissent avec :</span><span class="sxs-lookup"><span data-stu-id="4916a-160">This section details how the above transactions interact with:</span></span>  

- <span data-ttu-id="4916a-161">Résilience de connexion</span><span class="sxs-lookup"><span data-stu-id="4916a-161">Connection resiliency</span></span>  
- <span data-ttu-id="4916a-162">Méthodes asynchrones</span><span class="sxs-lookup"><span data-stu-id="4916a-162">Asynchronous methods</span></span>  
- <span data-ttu-id="4916a-163">Transactions TransactionScope</span><span class="sxs-lookup"><span data-stu-id="4916a-163">TransactionScope transactions</span></span>  

### <a name="connection-resiliency"></a><span data-ttu-id="4916a-164">Résilience des connexions</span><span class="sxs-lookup"><span data-stu-id="4916a-164">Connection Resiliency</span></span>  

<span data-ttu-id="4916a-165">La nouvelle fonctionnalité de résilience de connexion ne fonctionne pas avec les transactions initiées par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4916a-165">The new Connection Resiliency feature does not work with user-initiated transactions.</span></span> <span data-ttu-id="4916a-166">Pour plus d’informations, consultez [nouvelle tentative de stratégies d’exécution](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).</span><span class="sxs-lookup"><span data-stu-id="4916a-166">For details, see [Retrying Execution Strategies](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).</span></span>  

### <a name="asynchronous-programming"></a><span data-ttu-id="4916a-167">Programmation asynchrone</span><span class="sxs-lookup"><span data-stu-id="4916a-167">Asynchronous Programming</span></span>  

<span data-ttu-id="4916a-168">L’approche décrite dans les sections précédentes ne nécessite pas d’options ou de paramètres supplémentaires pour fonctionner avec la [requête asynchrone et les méthodes d’enregistrement](xref:ef6/fundamentals/async
).</span><span class="sxs-lookup"><span data-stu-id="4916a-168">The approach outlined in the previous sections needs no further options or settings to work with the [asynchronous query and save methods](xref:ef6/fundamentals/async
).</span></span> <span data-ttu-id="4916a-169">Toutefois, sachez que, en fonction de ce que vous faites dans les méthodes asynchrones, cela peut entraîner des transactions de longue durée, qui peuvent à leur tour entraîner des blocages ou des blocages, ce qui est incorrect pour les performances de l’application globale.</span><span class="sxs-lookup"><span data-stu-id="4916a-169">But be aware that, depending on what you do within the asynchronous methods, this may result in long-running transactions – which can in turn cause deadlocks or blocking which is bad for the performance of the overall application.</span></span>  

### <a name="transactionscope-transactions"></a><span data-ttu-id="4916a-170">Transactions TransactionScope</span><span class="sxs-lookup"><span data-stu-id="4916a-170">TransactionScope Transactions</span></span>  

<span data-ttu-id="4916a-171">Avant EF6, la méthode recommandée pour fournir des transactions plus étendues consistait à utiliser un objet TransactionScope :</span><span class="sxs-lookup"><span data-stu-id="4916a-171">Prior to EF6 the recommended way of providing larger scope transactions was to use a TransactionScope object:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void UsingTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeOption.Required))
            {
                using (var conn = new SqlConnection("..."))
                {
                    conn.Open();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    sqlCommand.ExecuteNonQuery();

                    using (var context =
                        new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                        context.SaveChanges();
                    }
                }

                scope.Complete();
            }
        }
    }
}
```  

<span data-ttu-id="4916a-172">SqlConnection et Entity Framework utiliseraient tous deux la transaction TransactionScope ambiante et seront donc validés ensemble.</span><span class="sxs-lookup"><span data-stu-id="4916a-172">The SqlConnection and Entity Framework would both use the ambient TransactionScope transaction and hence be committed together.</span></span>  

<span data-ttu-id="4916a-173">À compter de .NET 4.5.1, TransactionScope a été mis à jour pour utiliser également des méthodes asynchrones à l’aide de l’énumération [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) :</span><span class="sxs-lookup"><span data-stu-id="4916a-173">Starting with .NET 4.5.1 TransactionScope has been updated to also work with asynchronous methods via the use of the [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) enumeration:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        public static void AsyncTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
            {
                using (var conn = new SqlConnection("..."))
                {
                    await conn.OpenAsync();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    await sqlCommand.ExecuteNonQueryAsync();

                    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }

                        await context.SaveChangesAsync();
                    }
                }
            }
        }
    }
}
```  

<span data-ttu-id="4916a-174">Il existe toujours des limitations à l’approche TransactionScope :</span><span class="sxs-lookup"><span data-stu-id="4916a-174">There are still some limitations to the TransactionScope approach:</span></span>  

- <span data-ttu-id="4916a-175">Nécessite .NET 4.5.1 ou version ultérieure pour utiliser des méthodes asynchrones.</span><span class="sxs-lookup"><span data-stu-id="4916a-175">Requires .NET 4.5.1 or greater to work with asynchronous methods.</span></span>  
- <span data-ttu-id="4916a-176">Elle ne peut pas être utilisée dans les scénarios de Cloud, sauf si vous êtes sûr de disposer d’une seule connexion (les scénarios Cloud ne prennent pas en charge les transactions distribuées).</span><span class="sxs-lookup"><span data-stu-id="4916a-176">It cannot be used in cloud scenarios unless you are sure you have one and only one connection (cloud scenarios do not support distributed transactions).</span></span>  
- <span data-ttu-id="4916a-177">Elle ne peut pas être associée à l’approche Database. UseTransaction () des sections précédentes.</span><span class="sxs-lookup"><span data-stu-id="4916a-177">It cannot be combined with the Database.UseTransaction() approach of the previous sections.</span></span>  
- <span data-ttu-id="4916a-178">Elle lèvera des exceptions si vous émettez une DDL et que vous n’avez pas activé les transactions distribuées par le biais du service MSDTC.</span><span class="sxs-lookup"><span data-stu-id="4916a-178">It will throw exceptions if you issue any DDL and have not enabled distributed transactions through the MSDTC Service.</span></span>  

<span data-ttu-id="4916a-179">Avantages de l’approche TransactionScope :</span><span class="sxs-lookup"><span data-stu-id="4916a-179">Advantages of the TransactionScope approach:</span></span>  

- <span data-ttu-id="4916a-180">Elle met automatiquement à niveau une transaction locale vers une transaction distribuée si vous créez plusieurs connexions à une base de données spécifique ou associez une connexion à une base de données avec une connexion à une autre base de données au sein de la même transaction (Remarque : le service MSDTC doit être configuré pour autoriser les transactions distribuées pour que cela fonctionne).</span><span class="sxs-lookup"><span data-stu-id="4916a-180">It will automatically upgrade a local transaction to a distributed transaction if you make more than one connection to a given database or combine a connection to one database with a connection to a different database within the same transaction (note: you must have the MSDTC service configured to allow distributed transactions for this to work).</span></span>  
- <span data-ttu-id="4916a-181">Facilité de codage.</span><span class="sxs-lookup"><span data-stu-id="4916a-181">Ease of coding.</span></span> <span data-ttu-id="4916a-182">Si vous préférez que la transaction soit ambiante et traitée implicitement en arrière-plan plutôt que explicitement sous contrôle, l’approche TransactionScope peut vous convenir mieux.</span><span class="sxs-lookup"><span data-stu-id="4916a-182">If you prefer the transaction to be ambient and dealt with implicitly in the background rather than explicitly under you control then the TransactionScope approach may suit you better.</span></span>  

<span data-ttu-id="4916a-183">En résumé, avec les API New Database. BeginTransaction () et Database. UseTransaction () ci-dessus, l’approche TransactionScope n’est plus nécessaire pour la plupart des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="4916a-183">In summary, with the new Database.BeginTransaction() and Database.UseTransaction() APIs above, the TransactionScope approach is no longer necessary for most users.</span></span> <span data-ttu-id="4916a-184">Si vous continuez à utiliser TransactionScope, tenez compte des limitations ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="4916a-184">If you do continue to use TransactionScope then be aware of the above limitations.</span></span> <span data-ttu-id="4916a-185">Nous vous recommandons d’utiliser à la place l’approche décrite dans les sections précédentes dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="4916a-185">We recommend using the approach outlined in the previous sections instead where possible.</span></span>  
