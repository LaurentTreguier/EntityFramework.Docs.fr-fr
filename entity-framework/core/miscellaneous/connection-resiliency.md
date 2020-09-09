---
title: Résilience de connexion-EF Core
description: Utilisation de la résilience des connexions pour retenter automatiquement les commandes ayant échoué avec Entity Framework Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 6dd3d3eadb218ab32f373e44e2013d017e2966d8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617764"
---
# <a name="connection-resiliency"></a>Résilience des connexions

La résilience des connexions réessaie automatiquement les commandes de base de données ayant échoué. La fonctionnalité peut être utilisée avec n’importe quelle base de données en fournissant une « stratégie d’exécution », qui encapsule la logique nécessaire pour détecter les échecs et les commandes de nouvelle tentative. Les fournisseurs de EF Core peuvent fournir des stratégies d’exécution adaptées à leurs conditions d’échec de base de données spécifiques et aux stratégies de nouvelle tentative optimales.

Par exemple, le fournisseur SQL Server inclut une stratégie d’exécution spécifiquement adaptée à SQL Server (y compris SQL Azure). Il reconnaît les types d’exception qui peuvent être retentés et qui ont des valeurs par défaut sensibles pour les nouvelles tentatives, le délai entre les nouvelles tentatives, etc.

Une stratégie d’exécution est spécifiée lors de la configuration des options de votre contexte. En général, il s’agit de la `OnConfiguring` méthode de votre contexte dérivé :

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

ou dans `Startup.cs` pour une application ASP.net Core :

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a>Stratégie d’exécution personnalisée

Il existe un mécanisme permettant d’inscrire votre propre stratégie d’exécution personnalisée si vous souhaitez modifier les valeurs par défaut.

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a>Stratégies et transactions d’exécution

Une stratégie d’exécution qui effectue automatiquement de nouvelles tentatives en cas de défaillance doit pouvoir lire chaque opération dans un bloc de nouvelle tentative qui échoue. Lorsque les nouvelles tentatives sont activées, chaque opération que vous effectuez via EF Core devient sa propre opération reproductibles. Autrement dit, chaque requête et chaque appel à `SaveChanges()` est retentée en tant qu’unité en cas de défaillance temporaire.

Toutefois, si votre code initie une transaction à l’aide de `BeginTransaction()` , vous définissez votre propre groupe d’opérations qui doivent être traitées comme une unité, et tout ce qui se trouve à l’intérieur de la transaction doit être lu en cas de défaillance. Si vous tentez d’effectuer cette opération lors de l’utilisation d’une stratégie d’exécution, vous recevrez une exception semblable à la suivante :

> InvalidOperationException : la stratégie d’exécution configurée’SqlServerRetryingExecutionStrategy’ne prend pas en charge les transactions initiées par l’utilisateur. Utilisez la stratégie d’exécution retournée par « DbContext.Database.CreateExecutionStrategy() » pour exécuter toutes les opérations de la transaction en tant qu’ensemble pouvant être retenté.

La solution consiste à appeler manuellement la stratégie d’exécution avec un délégué représentant tout ce qui doit être exécuté. En cas d’échec passager, la stratégie d’exécution appelle de nouveau le délégué.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

Cette approche peut également être utilisée avec les transactions ambiantes.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>Échec de validation de transaction et problème idempotence

En général, en cas d’échec de la connexion, la transaction actuelle est annulée. Toutefois, si la connexion est abandonnée pendant la validation de la transaction, l’état résultant de la transaction est inconnu. 

Par défaut, la stratégie d’exécution retentera l’opération comme si la transaction était restaurée, mais si ce n’est pas le cas, cela entraînera une exception si le nouvel état de la base de données est incompatible ou risque d' **endommager les données** si l’opération ne repose pas sur un état particulier, par exemple lors de l’insertion d’une nouvelle ligne avec des valeurs de clé générées

Il existe plusieurs façons de traiter ce problème.

### <a name="option-1---do-almost-nothing"></a>Option 1-do (presque) Nothing

La probabilité d’un échec de connexion pendant la validation de la transaction est faible, ce qui peut être acceptable pour votre application d’échouer si cette condition se produit réellement.

Toutefois, vous devez éviter d’utiliser des clés générées par le magasin afin de garantir qu’une exception est levée au lieu d’ajouter une ligne dupliquée. Envisagez d’utiliser une valeur de GUID générée par le client ou un générateur de valeur côté client.

### <a name="option-2---rebuild-application-state"></a>Option 2-reconstruire l’état de l’application

1. Ignore le actuel `DbContext` .
2. Créez un nouvel `DbContext` et restaurez l’état de votre application à partir de la base de données.
3. Informe l’utilisateur que la dernière opération n’a peut-être pas été effectuée avec succès.

### <a name="option-3---add-state-verification"></a>Option 3-Ajouter une vérification de l’État

Pour la plupart des opérations qui modifient l’état de la base de données, il est possible d’ajouter du code qui vérifie si elle a réussi. EF fournit une méthode d’extension pour faciliter cette tâche `IExecutionStrategy.ExecuteInTransaction` .

Cette méthode commence et valide une transaction et accepte également une fonction dans le `verifySucceeded` paramètre qui est appelée lorsqu’une erreur temporaire se produit pendant la validation de la transaction.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> Ici `SaveChanges` est appelé avec `acceptAllChangesOnSuccess` défini sur `false` pour éviter de modifier l’état de l' `Blog` entité en si la méthode est `Unchanged` `SaveChanges` réussie. Cela permet à de retenter la même opération si la validation échoue et que la transaction est restaurée.

### <a name="option-4---manually-track-the-transaction"></a>Option 4 : suivre manuellement la transaction

Si vous devez utiliser des clés générées par le magasin ou si vous avez besoin d’une méthode générique pour gérer les échecs de validation qui ne dépendent pas de l’opération effectuée, il se peut qu’un ID qui est vérifié lorsque la validation échoue.

1. Ajoutez une table à la base de données utilisée pour suivre l’état des transactions.
2. Insérez une ligne dans la table au début de chaque transaction.
3. Si la connexion échoue pendant la validation, vérifiez la présence de la ligne correspondante dans la base de données.
4. Si la validation réussit, supprimez la ligne correspondante pour éviter la croissance de la table.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> Assurez-vous que le contexte utilisé pour la vérification a une stratégie d’exécution définie, car la connexion risque d’échouer à nouveau lors de la vérification en cas d’échec lors de la validation de la transaction.
