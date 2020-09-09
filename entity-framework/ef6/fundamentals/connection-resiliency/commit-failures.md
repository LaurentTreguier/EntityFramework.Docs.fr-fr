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
# <a name="handling-transaction-commit-failures"></a>Gestion des échecs de validation de transaction

> [!NOTE]
> **EF 6.1 uniquement** : les fonctionnalités, les API, etc. présentées dans cette page ont été introduites dans Entity Framework 6,1. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

Dans le cadre de 6,1, nous proposons une nouvelle fonctionnalité de résilience des connexions pour EF : la possibilité de détecter et de récupérer automatiquement quand des échecs de connexion temporaires affectent l’accusé de validation des transactions. Les détails complets du scénario sont décrits plus en détail dans le billet de blog [SQL Database connectivité et le problème idempotence](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).  En résumé, le scénario est que lorsqu’une exception est levée pendant une validation de transaction, deux causes sont possibles :  

1. La validation de la transaction a échoué sur le serveur
2. La validation de la transaction a réussi sur le serveur, mais un problème de connectivité a empêché la notification de réussite d’atteindre le client  

Lorsque la première situation se produit, l’application ou l’utilisateur peut réessayer l’opération, mais lorsque la deuxième situation se produit, les nouvelles tentatives doivent être évitées et l’application peut effectuer une récupération automatique. Le défi est que, sans la possibilité de détecter la raison pour laquelle une exception a été signalée lors de la validation, l’application ne peut pas choisir la bonne marche à suivre. La nouvelle fonctionnalité d’EF 6,1 permet à EF de double-vérifier avec la base de données si la transaction a réussi et de prendre la bonne marche de l’action en toute transparence.  

## <a name="using-the-feature"></a>Utilisation de la fonctionnalité  

Pour activer la fonctionnalité dont vous avez besoin, incluez un appel à [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) dans le constructeur de votre **DbConfiguration**. Si vous n’êtes pas familiarisé avec **DbConfiguration**, consultez [configuration basée sur le code](xref:ef6/fundamentals/configuring/code-based). Cette fonctionnalité peut être utilisée conjointement avec les nouvelles tentatives automatiques que nous avons introduites dans EF6, ce qui vous aide dans la situation dans laquelle la transaction n’a pas réellement pu être validée sur le serveur en raison d’une défaillance temporaire :  

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

## <a name="how-transactions-are-tracked"></a>Mode de suivi des transactions  

Lorsque la fonctionnalité est activée, EF ajoute automatiquement une nouvelle table à la base de données appelée **__Transactions**. Une nouvelle ligne est insérée dans cette table chaque fois qu’une transaction est créée par EF et l’existence de cette ligne est vérifiée en cas d’échec de la transaction pendant la validation.  

Bien que EF fasse le meilleur effort pour nettoyer les lignes de la table lorsque celles-ci ne sont plus nécessaires, la table peut croître si l’application se ferme prématurément et, pour cette raison, vous devrez peut-être vider la table manuellement dans certains cas.  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>Gestion des échecs de validation avec les versions précédentes

Avant EF 6,1, il n’existait pas de mécanisme pour gérer les échecs de validation dans le produit EF. Il existe plusieurs façons de traiter cette situation qui peut être appliquée aux versions précédentes de EF6 :  

* Option 1-ne rien faire  

  La probabilité d’un échec de connexion pendant la validation de la transaction est faible, ce qui peut être acceptable pour votre application d’échouer si cette condition se produit réellement.  

* Option 2-utiliser la base de données pour réinitialiser l’État  

  1. Supprimer le DbContext actuel  
  2. Créer un DbContext et restaurer l’état de votre application à partir de la base de données  
  3. Informe l’utilisateur que la dernière opération n’a peut-être pas été effectuée avec succès  

* Option 3 : suivre manuellement la transaction  

  1. Ajoutez une table non suivie à la base de données utilisée pour suivre l’état des transactions.  
  2. Insérez une ligne dans la table au début de chaque transaction.  
  3. Si la connexion échoue pendant la validation, vérifiez la présence de la ligne correspondante dans la base de données.  
     * Si la ligne est présente, continue normalement, car la transaction a été validée avec succès  
     * Si la ligne est absente, utilisez une stratégie d’exécution pour retenter l’opération en cours.  
  4. Si la validation réussit, supprimez la ligne correspondante pour éviter la croissance de la table.  

[Ce](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) billet de blog contient un exemple de code pour effectuer cette réalisation sur SQL Azure.  
