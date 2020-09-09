---
title: Gestion des échecs de validation de transaction-EF6
description: Gestion des échecs de validation de transaction dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: 10cb67837264bea37a0621aa078e1753af954c2f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616285"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="e971e-103">Gestion des échecs de validation de transaction</span><span class="sxs-lookup"><span data-stu-id="e971e-103">Handling transaction commit failures</span></span>

> [!NOTE]
> <span data-ttu-id="e971e-104">**EF 6.1 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 6,1.</span><span class="sxs-lookup"><span data-stu-id="e971e-104">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="e971e-105">Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.</span><span class="sxs-lookup"><span data-stu-id="e971e-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="e971e-106">Dans le cadre de 6,1, nous proposons une nouvelle fonctionnalité de résilience des connexions pour EF : la possibilité de détecter et de récupérer automatiquement quand des échecs de connexion temporaires affectent l’accusé de validation des transactions.</span><span class="sxs-lookup"><span data-stu-id="e971e-106">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="e971e-107">Les détails complets du scénario sont décrits plus en détail dans le billet de blog [SQL Database connectivité et le problème idempotence](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span><span class="sxs-lookup"><span data-stu-id="e971e-107">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="e971e-108">En résumé, le scénario est que lorsqu’une exception est levée pendant une validation de transaction, deux causes sont possibles :</span><span class="sxs-lookup"><span data-stu-id="e971e-108">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="e971e-109">La validation de la transaction a échoué sur le serveur</span><span class="sxs-lookup"><span data-stu-id="e971e-109">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="e971e-110">La validation de la transaction a réussi sur le serveur, mais un problème de connectivité a empêché la notification de réussite d’atteindre le client</span><span class="sxs-lookup"><span data-stu-id="e971e-110">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="e971e-111">Lorsque la première situation se produit, l’application ou l’utilisateur peut réessayer l’opération, mais lorsque la deuxième situation se produit, les nouvelles tentatives doivent être évitées et l’application peut effectuer une récupération automatique.</span><span class="sxs-lookup"><span data-stu-id="e971e-111">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="e971e-112">Le défi est que, sans la possibilité de détecter la raison pour laquelle une exception a été signalée lors de la validation, l’application ne peut pas choisir la bonne marche à suivre.</span><span class="sxs-lookup"><span data-stu-id="e971e-112">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="e971e-113">La nouvelle fonctionnalité d’EF 6,1 permet à EF de double-vérifier avec la base de données si la transaction a réussi et de prendre la bonne marche de l’action en toute transparence.</span><span class="sxs-lookup"><span data-stu-id="e971e-113">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="e971e-114">Utilisation de la fonctionnalité</span><span class="sxs-lookup"><span data-stu-id="e971e-114">Using the feature</span></span>  

<span data-ttu-id="e971e-115">Pour activer la fonctionnalité dont vous avez besoin, incluez un appel à [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) dans le constructeur de votre **DbConfiguration**.</span><span class="sxs-lookup"><span data-stu-id="e971e-115">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="e971e-116">Si vous n’êtes pas familiarisé avec **DbConfiguration**, consultez [configuration basée sur le code](xref:ef6/fundamentals/configuring/code-based).</span><span class="sxs-lookup"><span data-stu-id="e971e-116">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span> <span data-ttu-id="e971e-117">Cette fonctionnalité peut être utilisée conjointement avec les nouvelles tentatives automatiques que nous avons introduites dans EF6, ce qui vous aide dans la situation dans laquelle la transaction n’a pas réellement pu être validée sur le serveur en raison d’une défaillance temporaire :</span><span class="sxs-lookup"><span data-stu-id="e971e-117">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

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

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="e971e-118">Mode de suivi des transactions</span><span class="sxs-lookup"><span data-stu-id="e971e-118">How transactions are tracked</span></span>  

<span data-ttu-id="e971e-119">Lorsque la fonctionnalité est activée, EF ajoute automatiquement une nouvelle table à la base de données appelée **__Transactions**.</span><span class="sxs-lookup"><span data-stu-id="e971e-119">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="e971e-120">Une nouvelle ligne est insérée dans cette table chaque fois qu’une transaction est créée par EF et l’existence de cette ligne est vérifiée en cas d’échec de la transaction pendant la validation.</span><span class="sxs-lookup"><span data-stu-id="e971e-120">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="e971e-121">Bien que EF fasse le meilleur effort pour nettoyer les lignes de la table lorsque celles-ci ne sont plus nécessaires, la table peut croître si l’application se ferme prématurément et, pour cette raison, vous devrez peut-être vider la table manuellement dans certains cas.</span><span class="sxs-lookup"><span data-stu-id="e971e-121">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="e971e-122">Gestion des échecs de validation avec les versions précédentes</span><span class="sxs-lookup"><span data-stu-id="e971e-122">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="e971e-123">Avant EF 6,1, il n’existait pas de mécanisme pour gérer les échecs de validation dans le produit EF.</span><span class="sxs-lookup"><span data-stu-id="e971e-123">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="e971e-124">Il existe plusieurs façons de traiter cette situation qui peut être appliquée aux versions précédentes de EF6 :</span><span class="sxs-lookup"><span data-stu-id="e971e-124">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="e971e-125">Option 1-ne rien faire</span><span class="sxs-lookup"><span data-stu-id="e971e-125">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="e971e-126">La probabilité d’un échec de connexion pendant la validation de la transaction est faible, ce qui peut être acceptable pour votre application d’échouer si cette condition se produit réellement.</span><span class="sxs-lookup"><span data-stu-id="e971e-126">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="e971e-127">Option 2-utiliser la base de données pour réinitialiser l’État</span><span class="sxs-lookup"><span data-stu-id="e971e-127">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="e971e-128">Supprimer le DbContext actuel</span><span class="sxs-lookup"><span data-stu-id="e971e-128">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="e971e-129">Créer un DbContext et restaurer l’état de votre application à partir de la base de données</span><span class="sxs-lookup"><span data-stu-id="e971e-129">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="e971e-130">Informe l’utilisateur que la dernière opération n’a peut-être pas été effectuée avec succès</span><span class="sxs-lookup"><span data-stu-id="e971e-130">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="e971e-131">Option 3 : suivre manuellement la transaction</span><span class="sxs-lookup"><span data-stu-id="e971e-131">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="e971e-132">Ajoutez une table non suivie à la base de données utilisée pour suivre l’état des transactions.</span><span class="sxs-lookup"><span data-stu-id="e971e-132">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="e971e-133">Insérez une ligne dans la table au début de chaque transaction.</span><span class="sxs-lookup"><span data-stu-id="e971e-133">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="e971e-134">Si la connexion échoue pendant la validation, vérifiez la présence de la ligne correspondante dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="e971e-134">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     * <span data-ttu-id="e971e-135">Si la ligne est présente, continue normalement, car la transaction a été validée avec succès</span><span class="sxs-lookup"><span data-stu-id="e971e-135">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     * <span data-ttu-id="e971e-136">Si la ligne est absente, utilisez une stratégie d’exécution pour retenter l’opération en cours.</span><span class="sxs-lookup"><span data-stu-id="e971e-136">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="e971e-137">Si la validation réussit, supprimez la ligne correspondante pour éviter la croissance de la table.</span><span class="sxs-lookup"><span data-stu-id="e971e-137">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="e971e-138">[Ce](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) billet de blog contient un exemple de code pour effectuer cette réalisation sur SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="e971e-138">[This blog post](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
