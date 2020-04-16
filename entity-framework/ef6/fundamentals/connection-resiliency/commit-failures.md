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
# <a name="handling-transaction-commit-failures"></a>Le traitement des transactions commettent des défaillances
> [!NOTE]
> **EF6.1 En avant seulement** - Les fonctionnalités, API, etc. discutées dans cette page ont été introduites dans le cadre de l’entité 6.1. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

Dans le cadre de 6.1, nous introduisons une nouvelle fonctionnalité de résilience de connexion pour EF : la capacité de détecter et de récupérer automatiquement lorsque les défaillances transitoires de connexion affectent la reconnaissance des commits de transaction. Les détails complets du scénario sont mieux décrits dans le blog post [SQL Database Connectivity et la question de l’Idempotency](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).  En résumé, le scénario est que lorsqu’une exception est soulevée au cours d’une transaction s’engage, il y a deux causes possibles :  

1. L’engagement de transaction a échoué sur le serveur
2. L’engagement de transaction a réussi sur le serveur, mais un problème de connectivité a empêché la notification de succès d’atteindre le client  

Lorsque la première situation se produit l’application ou l’utilisateur peut retenter l’opération, mais lorsque la deuxième situation se produit retries doit être évité et l’application pourrait récupérer automatiquement. Le défi est que sans la capacité de détecter quelle était la raison réelle pour laquelle une exception a été signalée au cours de la validation, l’application ne peut pas choisir la bonne ligne de conduite. La nouvelle fonctionnalité d’EF 6.1 permet à EF de vérifier auprès de la base de données si la transaction réussit et de prendre la bonne ligne de conduite de manière transparente.  

## <a name="using-the-feature"></a>Utilisation de la fonctionnalité  

Afin d’activer la fonctionnalité dont vous avez besoin, vous pouvez inclure un appel à [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) dans le constructeur de votre **DbConfiguration**. Si vous n’êtes pas familier avec **DbConfiguration**, voir [Configuration basée sur le code](~/ef6/fundamentals/configuring/code-based.md). Cette fonctionnalité peut être utilisée en combinaison avec les retries automatiques que nous avons introduites dans EF6, qui aident dans la situation dans laquelle la transaction n’a pas réellement commis sur le serveur en raison d’une défaillance transitoire:  

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

## <a name="how-transactions-are-tracked"></a>Comment les transactions sont suivies  

Lorsque la fonctionnalité est activée, EF ajoutera automatiquement une nouvelle table à la base de données appelée **__Transactions**. Une nouvelle ligne est insérée dans ce tableau chaque fois qu’une transaction est créée par EF et que la ligne est vérifiée pour l’existence si une défaillance de transaction se produit pendant la validation.  

Bien que EF fera un meilleur effort pour tailler des rangées de la table quand ils ne sont plus nécessaires, la table peut croître si l’application sort prématurément et pour cette raison, vous devrez peut-être purger la table manuellement dans certains cas.  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>Comment gérer les échecs de validation avec les versions précédentes

Avant EF 6.1, il n’y avait pas de mécanisme pour gérer les défaillances de commit dans le produit EF. Il existe plusieurs façons de faire face à cette situation qui peuvent être appliquées aux versions précédentes de EF6 :  

* Option 1 - Ne rien faire  

  La probabilité d’une défaillance de connexion pendant la validation de transaction est faible de sorte qu’il peut être acceptable pour votre application de simplement échouer si cette condition se produit réellement.  

* Option 2 - Utiliser la base de données pour réinitialiser l’état  

  1. Rejetez le DbContext actuel  
  2. Créez un nouveau DbContext et restaurez l’état de votre application à partir de la base de données  
  3. Informez l’utilisateur que la dernière opération n’a peut-être pas été achevée avec succès  

* Option 3 - Suivi manuel de la transaction  

  1. Ajoutez une table non suivie à la base de données utilisée pour suivre l’état des transactions.  
  2. Insérez une ligne dans la table au début de chaque transaction.  
  3. Si la connexion échoue pendant la validation, vérifiez la présence de la ligne correspondante dans la base de données.  
     - Si la ligne est présente, continuez normalement, car la transaction a été engagée avec succès  
     - Si la ligne est absente, utilisez une stratégie d’exécution pour retenter l’opération en cours.  
  4. Si l’commit est réussi, supprimez la ligne correspondante pour éviter la croissance de la table.  

[Ce blog](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contient un exemple de code pour y parvenir sur SQL Azure.  
