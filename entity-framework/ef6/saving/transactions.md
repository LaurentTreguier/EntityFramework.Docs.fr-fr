---
title: Utilisation des transactions-EF6
description: Utilisation des transactions dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/saving/transactions
ms.openlocfilehash: 15cc2171338defe482767114c58afe16cc1ffe21
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073715"
---
# <a name="working-with-transactions"></a>Utilisation des transactions
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.  

Ce document décrit l’utilisation des transactions dans EF6, y compris les améliorations que nous avons ajoutées depuis EF5 pour faciliter l’utilisation des transactions.  

## <a name="what-ef-does-by-default"></a>Par défaut, EF  

Dans toutes les versions de Entity Framework, chaque fois que vous exécutez **SaveChanges ()** pour insérer, mettre à jour ou supprimer sur la base de données, l’infrastructure encapsule cette opération dans une transaction. Cette transaction ne dure que suffisamment longtemps pour exécuter l’opération, puis se termine. Lorsque vous exécutez une autre opération, une nouvelle transaction est démarrée.  

À compter de EF6 **Database.ExecuteSqlCommand ()** par défaut encapsule la commande dans une transaction, si celle-ci n’était pas déjà présente. Il existe des surcharges de cette méthode qui vous permettent de substituer ce comportement si vous le souhaitez. En outre, dans EF6, l’exécution de procédures stockées incluses dans le modèle via des API telles que **ObjectContext.ExecuteFunction ()** fait de même (sauf que le comportement par défaut ne peut pas être remplacé).  

Dans les deux cas, le niveau d’isolation de la transaction est le niveau d’isolation que le fournisseur de base de données considère comme son paramètre par défaut. Par défaut, par exemple, sur SQL Server il s’agit de READ COMMITTED.  

Entity Framework n’encapsule pas les requêtes dans une transaction.  

Cette fonctionnalité par défaut convient à un grand nombre d’utilisateurs et, si tel est le cas, il n’est pas nécessaire de faire quelque chose de différent dans EF6. il vous suffit d’écrire le code comme vous l’avez toujours fait.  

Toutefois, certains utilisateurs ont besoin d’un meilleur contrôle sur leurs transactions. cela est abordé dans les sections suivantes.  

## <a name="how-the-apis-work"></a>Fonctionnement des API  

Avant EF6 Entity Framework insistait sur l’ouverture de la connexion de base de données elle-même (une exception a été levée si une connexion déjà ouverte était passée). Étant donné qu’une transaction ne peut être démarrée que sur une connexion ouverte, cela signifiait que le seul moyen pour un utilisateur d’encapsuler plusieurs opérations dans une seule transaction était d’utiliser [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) ou d’utiliser la propriété **ObjectContext. Connection** et de commencer à appeler **Open ()** et **BeginTransaction ()** directement sur l’objet **EntityConnection** renvoyé. En outre, les appels d’API qui ont contacté la base de données échouent si vous avez démarré une transaction sur la connexion de base de données sous-jacente.  

> [!NOTE]
> La limitation de l’acceptation des connexions fermées a été supprimée dans Entity Framework 6. Pour plus d’informations, consultez [gestion des connexions](xref:ef6/fundamentals/connection-management).  

À compter de EF6, le Framework fournit désormais :  

1. **Database. BeginTransaction ()** : méthode plus simple permettant à un utilisateur de démarrer et d’exécuter des transactions proprement dites à l’intérieur d’un DbContext existant, ce qui permet de combiner plusieurs opérations au sein de la même transaction et, par conséquent, toutes validées ou toutes restaurées comme une seule. Il permet également à l’utilisateur de spécifier plus facilement le niveau d’isolation de la transaction.  
2. **Database. UseTransaction ()** : qui permet à DbContext d’utiliser une transaction qui a été démarrée en dehors de la Entity Framework.  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a>Combinaison de plusieurs opérations en une seule transaction dans le même contexte  

**Database. BeginTransaction ()** a deux remplacements : un qui accepte un [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) explicite et un qui n’accepte aucun argument et utilise la valeur IsolationLevel par défaut du fournisseur de base de données sous-jacent. Les deux substitutions retournent un objet **DbContextTransaction** qui fournit des méthodes **Commit ()** et **Rollback ()** qui effectuent des validations et des restaurations sur la transaction de magasin sous-jacente.  

Le **DbContextTransaction** est destiné à être supprimé une fois qu’il a été validé ou restauré. Pour ce faire, une méthode simple consiste à **utiliser (...) {...}** syntaxe qui appellera automatiquement **dispose ()** lorsque le bloc using se termine :  

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
> Le démarrage d’une transaction nécessite que la connexion à la banque sous-jacente soit ouverte. L’appel de Database. BeginTransaction () entraîne l’ouverture de la connexion si elle n’est pas déjà ouverte. Si DbContextTransaction a ouvert la connexion, il la ferme quand la méthode Dispose () est appelée.  

### <a name="passing-an-existing-transaction-to-the-context"></a>Passage d’une transaction existante au contexte  

Parfois, vous souhaitez une transaction qui est encore plus large dans l’étendue et qui comprend des opérations sur la même base de données mais en dehors d’EF. Pour ce faire, vous devez ouvrir la connexion et démarrer la transaction vous-même, puis indiquer à EF a) d’utiliser la connexion de base de données déjà ouverte et b) pour utiliser la transaction existante sur cette connexion.  

Pour ce faire, vous devez définir et utiliser un constructeur sur votre classe de contexte qui hérite de l’un des constructeurs DbContext qui prend i) un paramètre de connexion existant et II) la valeur booléenne contextOwnsConnection.  

> [!NOTE]
> L’indicateur contextOwnsConnection doit avoir la valeur false lorsqu’il est appelé dans ce scénario. Cela est important car il informe Entity Framework qu’il ne doit pas fermer la connexion quand elle l’a fait (par exemple, consultez la ligne 4 ci-dessous) :  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

En outre, vous devez démarrer la transaction vous-même (y compris la fonction IsolationLevel si vous souhaitez éviter le paramètre par défaut) et laisser Entity Framework savoir qu’une transaction existante a déjà démarré sur la connexion (voir la ligne 33 ci-dessous).  

Vous êtes alors libre d’exécuter des opérations de base de données directement sur le SqlConnection lui-même ou sur DbContext. Toutes ces opérations sont exécutées dans une transaction. Vous êtes responsable de la validation ou de la restauration de la transaction et de l’appel de dispose () sur celle-ci, ainsi que pour la fermeture et la suppression de la connexion de base de données. Exemple :  

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

### <a name="clearing-up-the-transaction"></a>Effacement de la transaction

Vous pouvez passer la valeur null à Database. UseTransaction () pour effacer Entity Framework connaissance de la transaction actuelle. Entity Framework ne valide pas ou ne restaure pas la transaction existante lorsque vous procédez ainsi, utilisez avec précaution et uniquement si vous êtes sûr que c’est ce que vous voulez faire.  

### <a name="errors-in-usetransaction"></a>Erreurs dans UseTransaction

Vous verrez une exception de Database. UseTransaction () si vous transmettez une transaction lorsque :  
- Entity Framework a déjà une transaction existante  
- Entity Framework est déjà en cours d’utilisation dans un TransactionScope  
- L’objet de connexion dans la transaction passée a la valeur null. Autrement dit, la transaction n’est pas associée à une connexion. il s’agit généralement d’un signe que la transaction est déjà terminée.  
- L’objet de connexion dans la transaction passée ne correspond pas à la connexion de Entity Framework.  

## <a name="using-transactions-with-other-features"></a>Utilisation de transactions avec d’autres fonctionnalités  

Cette section explique en détail comment les transactions ci-dessus interagissent avec :  

- Résilience de connexion  
- Méthodes asynchrones  
- Transactions TransactionScope  

### <a name="connection-resiliency"></a>Résilience des connexions  

La nouvelle fonctionnalité de résilience de connexion ne fonctionne pas avec les transactions initiées par l’utilisateur. Pour plus d’informations, consultez [nouvelle tentative de stratégies d’exécution](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).  

### <a name="asynchronous-programming"></a>Programmation asynchrone  

L’approche décrite dans les sections précédentes ne nécessite pas d’options ou de paramètres supplémentaires pour fonctionner avec la [requête asynchrone et les méthodes d’enregistrement](xref:ef6/fundamentals/async
). Toutefois, sachez que, en fonction de ce que vous faites dans les méthodes asynchrones, cela peut entraîner des transactions de longue durée, qui peuvent à leur tour entraîner des blocages ou des blocages, ce qui est incorrect pour les performances de l’application globale.  

### <a name="transactionscope-transactions"></a>Transactions TransactionScope  

Avant EF6, la méthode recommandée pour fournir des transactions plus étendues consistait à utiliser un objet TransactionScope :  

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

SqlConnection et Entity Framework utiliseraient tous deux la transaction TransactionScope ambiante et seront donc validés ensemble.  

À compter de .NET 4.5.1, TransactionScope a été mis à jour pour utiliser également des méthodes asynchrones à l’aide de l’énumération [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) :  

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

Il existe toujours des limitations à l’approche TransactionScope :  

- Nécessite .NET 4.5.1 ou version ultérieure pour utiliser des méthodes asynchrones.  
- Elle ne peut pas être utilisée dans les scénarios de Cloud, sauf si vous êtes sûr de disposer d’une seule connexion (les scénarios Cloud ne prennent pas en charge les transactions distribuées).  
- Elle ne peut pas être associée à l’approche Database. UseTransaction () des sections précédentes.  
- Elle lèvera des exceptions si vous émettez une DDL et que vous n’avez pas activé les transactions distribuées par le biais du service MSDTC.  

Avantages de l’approche TransactionScope :  

- Elle met automatiquement à niveau une transaction locale vers une transaction distribuée si vous créez plusieurs connexions à une base de données spécifique ou associez une connexion à une base de données avec une connexion à une autre base de données au sein de la même transaction (Remarque : le service MSDTC doit être configuré pour autoriser les transactions distribuées pour que cela fonctionne).  
- Facilité de codage. Si vous préférez que la transaction soit ambiante et traitée implicitement en arrière-plan plutôt que explicitement sous contrôle, l’approche TransactionScope peut vous convenir mieux.  

En résumé, avec les API New Database. BeginTransaction () et Database. UseTransaction () ci-dessus, l’approche TransactionScope n’est plus nécessaire pour la plupart des utilisateurs. Si vous continuez à utiliser TransactionScope, tenez compte des limitations ci-dessus. Nous vous recommandons d’utiliser à la place l’approche décrite dans les sections précédentes dans la mesure du possible.  
