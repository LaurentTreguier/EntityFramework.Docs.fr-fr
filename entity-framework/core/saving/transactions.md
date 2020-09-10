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
# <a name="using-transactions"></a>Utilisation de transactions

Les transactions permettent à plusieurs opérations de base de données d’être traitées de manière atomique. Si la transaction est validée, toutes les opérations sont appliquées avec succès à la base de données. Si la transaction est restaurée, aucune des opérations n’est appliquée à la base de données.

> [!TIP]  
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) sur GitHub.

## <a name="default-transaction-behavior"></a>Comportement de transaction par défaut

Par défaut, si le fournisseur de base de données prend en charge les transactions, toutes les modifications dans un seul appel à `SaveChanges()` sont appliquées à une transaction. Si certaines des modifications échouent, la transaction est annulée et aucune des modifications n’est appliquée à la base de données. Cela signifie que `SaveChanges()` réussit complètement ou laisse la base de données non modifiée si une erreur se produit.

Pour la plupart des applications, ce comportement par défaut est suffisant. Vous devez uniquement contrôler manuellement les transactions si les exigences de votre application le jugent nécessaire.

## <a name="controlling-transactions"></a>Contrôle des transactions

Vous pouvez utiliser l’API `DbContext.Database` pour commencer, valider et annuler les transactions. L’exemple suivant montre deux opérations `SaveChanges()` et une requête LINQ en cours d’exécution dans une transaction unique.

Tous les fournisseurs de base de données ne prennent pas en charge les transactions. Certains fournisseurs peuvent lever une exception ou ne pas exécuter d’opération lorsque des API de transaction sont appelées.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a>Transaction à contexte croisé (bases de données relationnelles uniquement)

Vous pouvez également partager une transaction sur plusieurs instances de contexte. Cette fonctionnalité est disponible uniquement lorsque vous utilisez un fournisseur de base de données relationnelle, car elle requiert l’utilisation de `DbTransaction` et `DbConnection`, qui sont propres aux bases de données relationnelles.

Pour partager une transaction, les contextes doivent partager une `DbConnection` et une `DbTransaction`.

### <a name="allow-connection-to-be-externally-provided"></a>Autoriser la fourniture externe de la connexion

Le partage d’une `DbConnection` nécessite la possibilité de passer une connexion dans un contexte lors de la construction.

Le moyen le plus simple pour autoriser la `DbConnection` à être fournie en externe, arrêtez d’utiliser la méthode `DbContext.OnConfiguring` pour configurer le contexte et créez les `DbContextOptions` en externe avant de les passer au constructeur de contexte.

> [!TIP]  
> `DbContextOptionsBuilder` est l’API que vous avez utilisée dans `DbContext.OnConfiguring` pour configurer le contexte. Vous allez maintenant l’utiliser en externe pour créer `DbContextOptions`.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

Une alternative consiste à continuer à utiliser `DbContext.OnConfiguring`, mais accepte une `DbConnection` qui est enregistrée et ensuite utilisée dans `DbContext.OnConfiguring`.

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

### <a name="share-connection-and-transaction"></a>Partage de connexions et transactions

Vous pouvez désormais créer plusieurs instances de contexte qui partagent la même connexion. Utilisez ensuite l’API `DbContext.Database.UseTransaction(DbTransaction)` pour inscrire les deux contextes dans la même transaction.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>Utilisation de DbTransactions externes (bases de données relationnelles uniquement)

Si vous utilisez plusieurs technologies d’accès aux données pour accéder à une base de données relationnelle, vous souhaiterez partager une transaction entre les opérations effectuées par ces différentes technologies.

L’exemple suivant montre comment effectuer une opération ADO.NET SqlClient et une opération Entity Framework Core dans la même transaction.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a>Utilisation de System.Transactions

> [!NOTE]  
> Cette fonctionnalité est une nouveauté d’EF Core 2.1.

Il est possible d’utiliser les transactions ambiantes si vous avez besoin de coordonner sur une plus grande portée.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

Il est également possible de s’inscrire dans une transaction explicite.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a>Limitations de System.Transactions  

1. EF Core s’appuie sur les fournisseurs de base de données pour implémenter la prise en charge de System.Transactions. Bien que la prise en charge est assez courante parmi les fournisseurs ADO.NET de .NET Framework, l’API n’a été que récemment ajoutée à .NET Core et n’est par conséquent pas aussi répandue. Si un fournisseur n’implémente pas la prise en charge de System.Transactions, il est possible que les appels à ces API soient complètement ignorés. SqlClient pour .NET Core le prend en charge à compter de la version 2.1 et versions ultérieures. SqlClient pour .NET Core 2.0 lève une exception en cas de tentative d’utilisation de la fonctionnalité.

   > [!IMPORTANT]  
   > Il est recommandé de vérifier que l’API se comporte correctement avec votre fournisseur avant de l’utiliser pour la gestion des transactions. Nous vous invitons à contacter le chargé de maintenance du fournisseur de base de données si ce n’est pas le cas.

2. À compter de la version 2.1, l’implémentation de System.Transactions dans .NET Core n’inclut pas de prise en charge des transactions distribuées. Par conséquent, vous ne pouvez pas utiliser `TransactionScope` ou `CommittableTransaction` pour coordonner des transactions sur plusieurs gestionnaires de ressources.
