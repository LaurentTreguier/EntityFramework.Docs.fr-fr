---
title: Le traitement des transactions commettent des défaillances - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: cf2722496e207a8ecaa9cfaa4ca61e7248e5e58f
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434134"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="4bafb-102">Le traitement des transactions commettent des défaillances</span><span class="sxs-lookup"><span data-stu-id="4bafb-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="4bafb-103">**EF6.1 En avant seulement** - Les fonctionnalités, API, etc. discutées dans cette page ont été introduites dans le cadre de l’entité 6.1.</span><span class="sxs-lookup"><span data-stu-id="4bafb-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="4bafb-104">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="4bafb-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="4bafb-105">Dans le cadre de 6.1, nous introduisons une nouvelle fonctionnalité de résilience de connexion pour EF : la capacité de détecter et de récupérer automatiquement lorsque les défaillances transitoires de connexion affectent la reconnaissance des commits de transaction.</span><span class="sxs-lookup"><span data-stu-id="4bafb-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="4bafb-106">Les détails complets du scénario sont mieux décrits dans le blog post [SQL Database Connectivity et la question de l’Idempotency](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span><span class="sxs-lookup"><span data-stu-id="4bafb-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="4bafb-107">En résumé, le scénario est que lorsqu’une exception est soulevée au cours d’une transaction s’engage, il y a deux causes possibles :</span><span class="sxs-lookup"><span data-stu-id="4bafb-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="4bafb-108">L’engagement de transaction a échoué sur le serveur</span><span class="sxs-lookup"><span data-stu-id="4bafb-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="4bafb-109">L’engagement de transaction a réussi sur le serveur, mais un problème de connectivité a empêché la notification de succès d’atteindre le client</span><span class="sxs-lookup"><span data-stu-id="4bafb-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="4bafb-110">Lorsque la première situation se produit l’application ou l’utilisateur peut retenter l’opération, mais lorsque la deuxième situation se produit retries doit être évité et l’application pourrait récupérer automatiquement.</span><span class="sxs-lookup"><span data-stu-id="4bafb-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="4bafb-111">Le défi est que sans la capacité de détecter quelle était la raison réelle pour laquelle une exception a été signalée au cours de la validation, l’application ne peut pas choisir la bonne ligne de conduite.</span><span class="sxs-lookup"><span data-stu-id="4bafb-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="4bafb-112">La nouvelle fonctionnalité d’EF 6.1 permet à EF de vérifier auprès de la base de données si la transaction réussit et de prendre la bonne ligne de conduite de manière transparente.</span><span class="sxs-lookup"><span data-stu-id="4bafb-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="4bafb-113">Utilisation de la fonctionnalité</span><span class="sxs-lookup"><span data-stu-id="4bafb-113">Using the feature</span></span>  

<span data-ttu-id="4bafb-114">Afin d’activer la fonctionnalité dont vous avez besoin, vous pouvez inclure un appel à [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) dans le constructeur de votre **DbConfiguration**.</span><span class="sxs-lookup"><span data-stu-id="4bafb-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="4bafb-115">Si vous n’êtes pas familier avec **DbConfiguration**, voir [Configuration basée sur le code](~/ef6/fundamentals/configuring/code-based.md).</span><span class="sxs-lookup"><span data-stu-id="4bafb-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="4bafb-116">Cette fonctionnalité peut être utilisée en combinaison avec les retries automatiques que nous avons introduites dans EF6, qui aident dans la situation dans laquelle la transaction n’a pas réellement commis sur le serveur en raison d’une défaillance transitoire:</span><span class="sxs-lookup"><span data-stu-id="4bafb-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

public class MyConfiguration : DbConfiguration  
{
  public MyConfiguration()  
  {  
    SetTransactionHandler(SqlProviderServices.ProviderInvariantName, () => new CommitFailureHandler());  
    SetExecutionStrategy(SqlProviderServices.ProviderInvariantName, () => new SqlAzureExecutionStrategy());  
  }  
}
```  

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="4bafb-117">Comment les transactions sont suivies</span><span class="sxs-lookup"><span data-stu-id="4bafb-117">How transactions are tracked</span></span>  

<span data-ttu-id="4bafb-118">Lorsque la fonctionnalité est activée, EF ajoutera automatiquement une nouvelle table à la base de données appelée **__Transactions**.</span><span class="sxs-lookup"><span data-stu-id="4bafb-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="4bafb-119">Une nouvelle ligne est insérée dans ce tableau chaque fois qu’une transaction est créée par EF et que la ligne est vérifiée pour l’existence si une défaillance de transaction se produit pendant la validation.</span><span class="sxs-lookup"><span data-stu-id="4bafb-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="4bafb-120">Bien que EF fera un meilleur effort pour tailler des rangées de la table quand ils ne sont plus nécessaires, la table peut croître si l’application sort prématurément et pour cette raison, vous devrez peut-être purger la table manuellement dans certains cas.</span><span class="sxs-lookup"><span data-stu-id="4bafb-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="4bafb-121">Comment gérer les échecs de validation avec les versions précédentes</span><span class="sxs-lookup"><span data-stu-id="4bafb-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="4bafb-122">Avant EF 6.1, il n’y avait pas de mécanisme pour gérer les défaillances de commit dans le produit EF.</span><span class="sxs-lookup"><span data-stu-id="4bafb-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="4bafb-123">Il existe plusieurs façons de faire face à cette situation qui peuvent être appliquées aux versions précédentes de EF6 :</span><span class="sxs-lookup"><span data-stu-id="4bafb-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="4bafb-124">Option 1 - Ne rien faire</span><span class="sxs-lookup"><span data-stu-id="4bafb-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="4bafb-125">La probabilité d’une défaillance de connexion pendant la validation de transaction est faible de sorte qu’il peut être acceptable pour votre application de simplement échouer si cette condition se produit réellement.</span><span class="sxs-lookup"><span data-stu-id="4bafb-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="4bafb-126">Option 2 - Utiliser la base de données pour réinitialiser l’état</span><span class="sxs-lookup"><span data-stu-id="4bafb-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="4bafb-127">Rejetez le DbContext actuel</span><span class="sxs-lookup"><span data-stu-id="4bafb-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="4bafb-128">Créez un nouveau DbContext et restaurez l’état de votre application à partir de la base de données</span><span class="sxs-lookup"><span data-stu-id="4bafb-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="4bafb-129">Informez l’utilisateur que la dernière opération n’a peut-être pas été achevée avec succès</span><span class="sxs-lookup"><span data-stu-id="4bafb-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="4bafb-130">Option 3 - Suivi manuel de la transaction</span><span class="sxs-lookup"><span data-stu-id="4bafb-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="4bafb-131">Ajoutez une table non suivie à la base de données utilisée pour suivre l’état des transactions.</span><span class="sxs-lookup"><span data-stu-id="4bafb-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="4bafb-132">Insérez une ligne dans la table au début de chaque transaction.</span><span class="sxs-lookup"><span data-stu-id="4bafb-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="4bafb-133">Si la connexion échoue pendant la validation, vérifiez la présence de la ligne correspondante dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="4bafb-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="4bafb-134">Si la ligne est présente, continuez normalement, car la transaction a été engagée avec succès</span><span class="sxs-lookup"><span data-stu-id="4bafb-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="4bafb-135">Si la ligne est absente, utilisez une stratégie d’exécution pour retenter l’opération en cours.</span><span class="sxs-lookup"><span data-stu-id="4bafb-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="4bafb-136">Si l’commit est réussi, supprimez la ligne correspondante pour éviter la croissance de la table.</span><span class="sxs-lookup"><span data-stu-id="4bafb-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="4bafb-137">[Ce blog](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contient un exemple de code pour y parvenir sur SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="4bafb-137">[This blog post](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
