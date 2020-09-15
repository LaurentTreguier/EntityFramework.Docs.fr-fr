---
title: Résilience des connexions et logique de nouvelle tentative-EF6
description: Résilience des connexions et logique de nouvelle tentative dans Entity Framework 6
author: AndriySvyryd
ms.date: 11/20/2019
uid: ef6/fundamentals/connection-resiliency/retry-logic
ms.openlocfilehash: 3e71e973be5a23c86f873e9adf1bf00f4b6def58
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073186"
---
# <a name="connection-resiliency-and-retry-logic"></a>Résilience des connexions et logique de nouvelle tentative
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

Les applications se connectant à un serveur de base de données ont toujours été vulnérables aux interruptions de connexion en raison de défaillances du serveur principal et de l’instabilité du réseau. Toutefois, dans un environnement LAN qui fonctionne sur des serveurs de base de données dédiés, ces erreurs sont assez rares, car une logique supplémentaire pour gérer ces échecs n’est souvent pas nécessaire. Avec la montée en charge des serveurs de base de données Cloud tels que Windows Azure SQL Database et des connexions sur des réseaux moins fiables, il est désormais plus courant de rencontrer des interruptions de connexion. Cela peut être dû à des techniques défensives utilisées par les bases de données Cloud pour garantir l’équité du service, telles que la limitation de la connexion, ou l’instabilité du réseau provoquant des délais d’attente intermittents et d’autres erreurs temporaires.  

La résilience de connexion fait référence à la possibilité pour EF de retenter automatiquement les commandes qui échouent en raison de ces interruptions de connexion.  

## <a name="execution-strategies"></a>Stratégies d’exécution  

La nouvelle tentative de connexion est prise en charge par une implémentation de l’interface IDbExecutionStrategy. Les implémentations de IDbExecutionStrategy sont chargées d’accepter une opération et, si une exception se produit, de déterminer si une nouvelle tentative est appropriée et de retenter si elle est. Il existe quatre stratégies d’exécution fournies avec EF :  

1. **DefaultExecutionStrategy**: cette stratégie d’exécution ne réessaye aucune opération, il s’agit de la valeur par défaut pour les bases de données autres que SQL Server.  
2. **Defaultsqlexecutionstrategy,**: il s’agit d’une stratégie d’exécution interne qui est utilisée par défaut. Cette stratégie ne réessaye pas du tout, mais elle encapsule toutes les exceptions qui peuvent être temporaires pour informer les utilisateurs qu’ils peuvent souhaiter activer la résilience des connexions.  
3. **DbExecutionStrategy**: cette classe est appropriée comme classe de base pour d’autres stratégies d’exécution, y compris celles personnalisées. Il implémente une stratégie de nouvelle tentative exponentielle, où la nouvelle tentative initiale a lieu sans délai et le délai augmente de façon exponentielle jusqu’à ce que le nombre maximal de tentatives soit atteint. Cette classe possède une méthode ShouldRetryOn abstraite qui peut être implémentée dans des stratégies d’exécution dérivées pour contrôler les exceptions qui doivent être retentées.  
4. **SqlAzureExecutionStrategy**: cette stratégie d’exécution hérite de DbExecutionStrategy et réessaie sur les exceptions connues comme pouvant être transitoires lorsque vous utilisez Azure SQL Database.

> [!NOTE]
> Les stratégies d’exécution 2 et 4 sont incluses dans le fournisseur SQL Server fourni avec EF, qui se trouve dans l’assembly EntityFramework. SqlServer et qui sont conçus pour fonctionner avec SQL Server.  

## <a name="enabling-an-execution-strategy"></a>Activation d’une stratégie d’exécution  

Le moyen le plus simple pour indiquer à EF d’utiliser une stratégie d’exécution consiste à utiliser la méthode SetExecutionStrategy de la classe [DbConfiguration](xref:ef6/fundamentals/configuring/code-based) :  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

Ce code indique à EF d’utiliser le SqlAzureExecutionStrategy lors de la connexion à SQL Server.  

## <a name="configuring-the-execution-strategy"></a>Configuration de la stratégie d’exécution  

Le constructeur de SqlAzureExecutionStrategy peut accepter deux paramètres, MaxRetryCount et MaxDelay. Le nombre de MaxRetry est le nombre maximal de tentatives de la stratégie. Le MaxDelay est un intervalle de temps qui représente le délai maximal entre les nouvelles tentatives que la stratégie d’exécution doit utiliser.  

Pour définir le nombre maximal de nouvelles tentatives sur 1 et le délai maximal à 30 secondes, vous devez exécuter la commande suivante :  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy(
            "System.Data.SqlClient",
            () => new SqlAzureExecutionStrategy(1, TimeSpan.FromSeconds(30)));
    }
}
```  

Le SqlAzureExecutionStrategy réessaie instantanément la première fois qu’un échec temporaire se produit, mais il retardera entre chaque nouvelle tentative jusqu’à ce que le nombre maximal de nouvelles tentatives soit dépassé ou que le délai total atteigne le délai maximal.  

Les stratégies d’exécution renouvellent uniquement un nombre limité d’exceptions qui sont généralement temporaires, vous devrez toujours gérer d’autres erreurs et intercepter l’exception RetryLimitExceeded pour le cas où une erreur n’est pas temporaire ou prend trop de temps pour se résoudre elle-même.  

Il existe des limitations connues en cas d’utilisation d’une stratégie de nouvelle tentative d’exécution :  

## <a name="streaming-queries-are-not-supported"></a>Les requêtes de streaming ne sont pas prises en charge  

Par défaut, EF6 et versions ultérieures effectuent la mise en mémoire tampon des résultats de la requête au lieu de les diffuser. Si vous souhaitez que les résultats soient diffusés en continu, vous pouvez utiliser la méthode AsStreaming pour modifier une requête LINQ to Entities en streaming.  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

La diffusion en continu n’est pas prise en charge lorsqu’une stratégie d’exécution de nouvelle tentative est inscrite. Cette limitation existe, car la connexion peut supprimer de façon partielle les résultats retournés. Dans ce cas, EF doit réexécuter l’intégralité de la requête, mais n’a pas de moyen fiable de savoir quels résultats ont déjà été retournés (les données ont peut-être été modifiées depuis l’envoi de la requête initiale, les résultats peuvent être renvoyés dans un ordre différent, les résultats peuvent ne pas avoir un identificateur unique, etc.).  

## <a name="user-initiated-transactions-are-not-supported"></a>Les transactions initiées par l’utilisateur ne sont pas prises en charge  

Lorsque vous avez configuré une stratégie d’exécution qui se traduit par de nouvelles tentatives, il existe des restrictions concernant l’utilisation des transactions.  

Par défaut, EF effectue toutes les mises à jour de la base de données dans une transaction. Vous n’avez rien à faire pour activer cela, EF le fait toujours automatiquement.  

Par exemple, dans le code suivant, SaveChanges est exécuté automatiquement dans une transaction. Si SaveChanges devait échouer après l’insertion de l’un des nouveaux sites, la transaction est restaurée et aucune modification n’est appliquée à la base de données. Le contexte est également laissé dans un État qui permet d’appeler à nouveau SaveChanges pour réessayer d’appliquer les modifications.  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

Lorsque vous n’utilisez pas une stratégie de nouvelle tentative d’exécution, vous pouvez encapsuler plusieurs opérations dans une transaction unique. Par exemple, le code suivant encapsule deux appels de SaveChanges dans une transaction unique. Si une partie de l’une ou l’autre des opérations échoue, aucune des modifications n’est appliquée.  

``` csharp
using (var db = new BloggingContext())
{
    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Site { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }
}
```  

Cela n’est pas pris en charge lors de l’utilisation d’une stratégie de nouvelle tentative d’exécution, car EF n’est pas conscient des opérations précédentes et de la manière de les réessayer. Par exemple, si le deuxième SaveChanges a échoué, EF n’a plus les informations requises pour réessayer le premier appel SaveChanges.  

### <a name="solution-manually-call-execution-strategy"></a>Solution : appeler manuellement la stratégie d’exécution  

La solution consiste à utiliser manuellement la stratégie d’exécution et à lui attribuer l’ensemble de la logique à exécuter, afin qu’elle puisse réessayer tout en cas d’échec de l’une des opérations. Lorsqu’une stratégie d’exécution dérivée de DbExecutionStrategy est en cours d’exécution, elle interrompt la stratégie d’exécution implicite utilisée dans SaveChanges.  

Notez que tous les contextes doivent être construits dans le bloc de code pour être retentés. Cela garantit que nous démarrons avec un état propre pour chaque nouvelle tentative.  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

executionStrategy.Execute(
    () =>
    {
        using (var db = new BloggingContext())
        {
            using (var trn = db.Database.BeginTransaction())
            {
                db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                db.SaveChanges();

                db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
                db.SaveChanges();

                trn.Commit();
            }
        }
    });
```  
