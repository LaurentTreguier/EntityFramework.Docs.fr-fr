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
# <a name="using-transactions"></a>Utilisation de transactions

Les transactions permettent à plusieurs opérations de base de données d’être traitées de manière atomique. Si la transaction est validée, toutes les opérations sont appliquées avec succès à la base de données. Si la transaction est restaurée, aucune des opérations n’est appliquée à la base de données.

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/Transactions/) sur GitHub.

## <a name="default-transaction-behavior"></a>Comportement de transaction par défaut

Par défaut, si le fournisseur de base de données prend en charge les transactions, toutes les modifications dans un seul appel à `SaveChanges` sont appliquées à une transaction. Si certaines des modifications échouent, la transaction est annulée et aucune des modifications n’est appliquée à la base de données. Cela signifie que `SaveChanges` réussit complètement ou laisse la base de données non modifiée si une erreur se produit.

Pour la plupart des applications, ce comportement par défaut est suffisant. Vous devez uniquement contrôler manuellement les transactions si les exigences de votre application le jugent nécessaire.

## <a name="controlling-transactions"></a>Contrôle des transactions

Vous pouvez utiliser l’API `DbContext.Database` pour commencer, valider et annuler les transactions. L’exemple suivant montre deux `SaveChanges` opérations et une requête LINQ en cours d’exécution dans une transaction unique :

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

Bien que tous les fournisseurs de base de données relationnelle prennent en charge les transactions, les autres types de fournisseurs peuvent lever ou ne pas être opérationnels lorsque les API de transaction sont appelées.

## <a name="savepoints"></a>Points

> [!NOTE]
> Cette fonctionnalité a été introduite dans EF Core 5,0.

Lorsque `SaveChanges` est appelé et qu’une transaction est déjà en cours sur le contexte, EF crée automatiquement un *point* de sauvegarde avant d’enregistrer les données. Les points d’enregistrement sont des points dans une transaction de base de données qui peuvent être restaurés ultérieurement vers, si une erreur se produit ou pour toute autre raison. Si `SaveChanges` rencontre une erreur, il restaure automatiquement la transaction jusqu’au point de sauvegarde, ce qui laisse la transaction dans le même État que si elle n’avait jamais démarré. Cela vous permet de corriger éventuellement des problèmes et de réessayer d’enregistrer, en particulier lorsque des problèmes d' [accès concurrentiel optimiste](xref:core/saving/concurrency) se produisent.

> [!WARNING]
> Les points de sauvegarde sont incompatibles avec les jeux de résultats actifs multiples de SQL Server et ne sont pas utilisés. Si une erreur se produit pendant `SaveChanges` , la transaction peut être laissée dans un état inconnu.

Il est également possible de gérer manuellement les points de enregistrement, comme c’est le cas avec les transactions. L’exemple suivant crée un point de sauvegarde dans une transaction, puis le restaure en cas d’échec :

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a>Transaction de contexte croisé

Vous pouvez également partager une transaction sur plusieurs instances de contexte. Cette fonctionnalité est disponible uniquement lorsque vous utilisez un fournisseur de base de données relationnelle, car elle requiert l’utilisation de `DbTransaction` et `DbConnection`, qui sont propres aux bases de données relationnelles.

Pour partager une transaction, les contextes doivent partager une `DbConnection` et une `DbTransaction`.

### <a name="allow-connection-to-be-externally-provided"></a>Autoriser la fourniture externe de la connexion

Le partage d’une `DbConnection` nécessite la possibilité de passer une connexion dans un contexte lors de la construction.

Le moyen le plus simple pour autoriser la `DbConnection` à être fournie en externe, arrêtez d’utiliser la méthode `DbContext.OnConfiguring` pour configurer le contexte et créez les `DbContextOptions` en externe avant de les passer au constructeur de contexte.

> [!TIP]
> `DbContextOptionsBuilder` est l’API que vous avez utilisée dans `DbContext.OnConfiguring` pour configurer le contexte. Vous allez maintenant l’utiliser en externe pour créer `DbContextOptions`.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

Une alternative consiste à continuer à utiliser `DbContext.OnConfiguring`, mais accepte une `DbConnection` qui est enregistrée et ensuite utilisée dans `DbContext.OnConfiguring`.

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

### <a name="share-connection-and-transaction"></a>Partage de connexions et transactions

Vous pouvez désormais créer plusieurs instances de contexte qui partagent la même connexion. Utilisez ensuite l’API `DbContext.Database.UseTransaction(DbTransaction)` pour inscrire les deux contextes dans la même transaction.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>Utilisation de DbTransactions externes (bases de données relationnelles uniquement)

Si vous utilisez plusieurs technologies d’accès aux données pour accéder à une base de données relationnelle, vous souhaiterez partager une transaction entre les opérations effectuées par ces différentes technologies.

L’exemple suivant montre comment effectuer une opération ADO.NET SqlClient et une opération Entity Framework Core dans la même transaction.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a>Utilisation de System.Transactions

Il est possible d’utiliser les transactions ambiantes si vous avez besoin de coordonner sur une plus grande portée.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

Il est également possible de s’inscrire dans une transaction explicite.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a>Limitations de System.Transactions

1. EF Core s’appuie sur les fournisseurs de base de données pour implémenter la prise en charge de System.Transactions. Si un fournisseur n’implémente pas la prise en charge de System.Transactions, il est possible que les appels à ces API soient complètement ignorés. SqlClient le prend en charge.

   > [!IMPORTANT]
   > Il est recommandé de vérifier que l’API se comporte correctement avec votre fournisseur avant de l’utiliser pour la gestion des transactions. Nous vous invitons à contacter le chargé de maintenance du fournisseur de base de données si ce n’est pas le cas.

2. L’implémentation .NET Core de System. transactions n’inclut pas actuellement la prise en charge des transactions distribuées. par conséquent, vous ne pouvez pas utiliser `TransactionScope` ou `CommittableTransaction` pour coordonner des transactions sur plusieurs gestionnaires de ressources. Le support est suivi par [ce problème](https://github.com/dotnet/runtime/issues/715).
