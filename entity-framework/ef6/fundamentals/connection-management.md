---
title: Gestion des connexions-EF6
description: Gestion des connexions dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-management
ms.openlocfilehash: e2097f5ed0471d309292625990cc5f9a598c5b2a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070560"
---
# <a name="connection-management"></a>Gestion des connexions
Cette page décrit le comportement de Entity Framework en ce qui concerne le passage des connexions au contexte et les fonctionnalités de l’API **Database. Connection. Open ()** .  

## <a name="passing-connections-to-the-context"></a>Transmission des connexions au contexte  

### <a name="behavior-for-ef5-and-earlier-versions"></a>Comportement pour EF5 et les versions antérieures  

Deux constructeurs acceptent les connexions :  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

Il est possible d’utiliser ces éléments, mais vous devez contourner deux limitations :  

1. Si vous transmettez une connexion ouverte à l’un de ces deux, la première fois que l’infrastructure tente de l’utiliser, une exception InvalidOperationException est levée, indiquant qu’elle ne peut pas rouvrir une connexion déjà ouverte.  
2. L’indicateur contextOwnsConnection est interprété pour indiquer si la connexion de la banque sous-jacente doit être supprimée lorsque le contexte est supprimé. Toutefois, quelle que soit la valeur de ce paramètre, la connexion du magasin est toujours fermée lorsque le contexte est supprimé. Par conséquent, si vous avez plusieurs DbContext avec la même connexion quel que soit le contexte supprimé en premier, la connexion est fermée (de même si vous avez mélangé une connexion ADO.NET existante à un DbContext, DbContext fermera toujours la connexion lorsqu’elle sera supprimée).  

Il est possible de contourner la première limitation ci-dessus en passant une connexion fermée et en exécutant uniquement le code qui l’ouvre une fois que tous les contextes ont été créés :  

``` csharp
using System.Collections.Generic;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Linq;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExampleEF5
    {         
        public static void TwoDbContextsOneConnection()
        {
            using (var context1 = new BloggingContext())
            {
                var conn =
                    ((EntityConnection)  
                        ((IObjectContextAdapter)context1).ObjectContext.Connection)  
                            .StoreConnection;

                using (var context2 = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    context2.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'");

                    var query = context1.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context1.SaveChanges();
                }
            }
        }
    }
}
```  

La deuxième limitation signifie simplement que vous devez vous abstenir de supprimer les objets DbContext jusqu’à ce que vous soyez prêt à fermer la connexion.  

### <a name="behavior-in-ef6-and-future-versions"></a>Comportement dans EF6 et les versions ultérieures  

Dans EF6 et les versions ultérieures, DbContext a les deux constructeurs, mais n’exige plus que la connexion passée au constructeur soit fermée lors de sa réception. C’est maintenant possible :  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExample
    {
        public static void PassingAnOpenConnection()
        {
            using (var conn = new SqlConnection("{connectionString}"))
            {
                conn.Open();

                var sqlCommand = new SqlCommand();
                sqlCommand.Connection = conn;
                sqlCommand.CommandText =
                    @"UPDATE Blogs SET Rating = 5" +
                     " WHERE Name LIKE '%Entity Framework%'";
                sqlCommand.ExecuteNonQuery();

                using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    var query = context.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context.SaveChanges();
                }

                var sqlCommand2 = new SqlCommand();
                sqlCommand2.Connection = conn;
                sqlCommand2.CommandText =
                    @"UPDATE Blogs SET Rating = 7" +
                     " WHERE Name LIKE '%Entity Framework Rocks%'";
                sqlCommand2.ExecuteNonQuery();
            }
        }
    }
}
```  

En outre, l’indicateur contextOwnsConnection contrôle désormais si la connexion est fermée et supprimée lorsque DbContext est supprimé. Ainsi, dans l’exemple ci-dessus, la connexion n’est pas fermée lorsque le contexte est supprimé (ligne 32) comme c’était le cas dans les versions précédentes d’EF, mais plutôt lorsque la connexion elle-même est supprimée (ligne 40).  

Bien entendu, il est toujours possible pour DbContext de prendre le contrôle de la connexion (il suffit de définir contextOwnsConnection sur true ou d’utiliser l’un des autres constructeurs) si vous le souhaitez.  

> [!NOTE]
> Des considérations supplémentaires sont à prendre en compte lors de l’utilisation de transactions avec ce nouveau modèle. Pour plus d’informations, consultez [utilisation des transactions](xref:ef6/saving/transactions).  

## <a name="databaseconnectionopen"></a>Database. Connection. Open ()  

### <a name="behavior-for-ef5-and-earlier-versions"></a>Comportement pour EF5 et les versions antérieures  

Dans EF5 et les versions antérieures, il existe un bogue tel que **ObjectContext. Connection. State** n’a pas été mis à jour pour refléter l’état réel de la connexion du magasin sous-jacent. Par exemple, si vous avez exécuté le code suivant, vous pouvez retourner l’état **Closed** même si la connexion à la banque sous-jacente est **ouverte**.  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

Séparément, si vous ouvrez la connexion à la base de données en appelant Database. Connection. Open (), elle est ouverte jusqu’à la prochaine exécution d’une requête ou l’appel de tout ce qui nécessite une connexion à la base de données (par exemple, SaveChanges ()), mais après que la connexion à la banque sous-jacente sera fermée. Le contexte réouvre et referme la connexion chaque fois qu’une opération de base de données est requise :  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;

namespace ConnectionManagementExamples
{
    public class DatabaseOpenConnectionBehaviorEF5
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open  
                // (though ObjectContext.Connection.State will report closed)

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);

                // The underlying store connection is still open  

                context.SaveChanges();

                // After SaveChanges() the underlying store connection is closed  
                // Each SaveChanges() / query etc now opens and immediately closes
                // the underlying store connection

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();
            }
        }
    }
}
```  

### <a name="behavior-in-ef6-and-future-versions"></a>Comportement dans EF6 et les versions ultérieures  

Pour les versions EF6 et futures, nous avons pris l’approche selon laquelle le code appelant choisit d’ouvrir la connexion en appelant Context. Database. Connection. Open () ensuite, il a une bonne raison de le faire et le Framework suppose qu’il souhaite contrôler l’ouverture et la fermeture de la connexion et ne fermera plus la connexion automatiquement.  

> [!NOTE]
> Cela peut aboutir à des connexions qui sont ouvertes pendant une longue période. Utilisez-les avec précaution.  

Nous avons également mis à jour le code afin qu’ObjectContext. Connection. State effectue désormais le suivi correct de l’état de la connexion sous-jacente.  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Core.EntityClient;
using System.Data.Entity.Infrastructure;

namespace ConnectionManagementExamples
{
    internal class DatabaseOpenConnectionBehaviorEF6
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open and the
                // ObjectContext.Connection.State correctly reports open too

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection remains open for the next operation  

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection is still open

           } // The context is disposed – so now the underlying store connection is closed
        }
    }
}
```  
