---
title: Gestion des connexions-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: ecaa5a27-b19e-4bf9-8142-a3fb00642270
ms.openlocfilehash: a6352bbbc38c38bd5f30536736ec969056df2c7d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417986"
---
# <a name="connection-management"></a><span data-ttu-id="7d8d8-102">Gestion des connexions</span><span class="sxs-lookup"><span data-stu-id="7d8d8-102">Connection management</span></span>
<span data-ttu-id="7d8d8-103">Cette page décrit le comportement de Entity Framework en ce qui concerne le passage des connexions au contexte et les fonctionnalités de l’API **Database. Connection. Open ()** .</span><span class="sxs-lookup"><span data-stu-id="7d8d8-103">This page describes the behavior of Entity Framework with regard to passing connections to the context and the functionality of the **Database.Connection.Open()** API.</span></span>  

## <a name="passing-connections-to-the-context"></a><span data-ttu-id="7d8d8-104">Transmission des connexions au contexte</span><span class="sxs-lookup"><span data-stu-id="7d8d8-104">Passing Connections to the Context</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="7d8d8-105">Comportement pour EF5 et les versions antérieures</span><span class="sxs-lookup"><span data-stu-id="7d8d8-105">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="7d8d8-106">Deux constructeurs acceptent les connexions :</span><span class="sxs-lookup"><span data-stu-id="7d8d8-106">There are two constructors which accept connections:</span></span>  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

<span data-ttu-id="7d8d8-107">Il est possible d’utiliser ces éléments, mais vous devez contourner deux limitations :</span><span class="sxs-lookup"><span data-stu-id="7d8d8-107">It is possible to use these but you have to work around a couple of limitations:</span></span>  

1. <span data-ttu-id="7d8d8-108">Si vous transmettez une connexion ouverte à l’un de ces deux, la première fois que l’infrastructure tente de l’utiliser, une exception InvalidOperationException est levée, indiquant qu’elle ne peut pas rouvrir une connexion déjà ouverte.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-108">If you pass an open connection to either of these then the first time the framework attempts to use it an InvalidOperationException is thrown saying it cannot re-open an already open connection.</span></span>  
2. <span data-ttu-id="7d8d8-109">L’indicateur contextOwnsConnection est interprété pour indiquer si la connexion de la banque sous-jacente doit être supprimée lorsque le contexte est supprimé.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-109">The contextOwnsConnection flag is interpreted to mean whether or not the underlying store connection should be disposed when the context is disposed.</span></span> <span data-ttu-id="7d8d8-110">Toutefois, quelle que soit la valeur de ce paramètre, la connexion du magasin est toujours fermée lorsque le contexte est supprimé.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-110">But, regardless of that setting, the store connection is always closed when the context is disposed.</span></span> <span data-ttu-id="7d8d8-111">Par conséquent, si vous avez plusieurs DbContext avec la même connexion quel que soit le contexte supprimé en premier, la connexion est fermée (de même si vous avez mélangé une connexion ADO.NET existante à un DbContext, DbContext fermera toujours la connexion lorsqu’elle sera supprimée) .</span><span class="sxs-lookup"><span data-stu-id="7d8d8-111">So if you have more than one DbContext with the same connection whichever context is disposed first will close the connection (similarly if you have mixed an existing ADO.NET connection with a DbContext, DbContext will always close the connection when it is disposed).</span></span>  

<span data-ttu-id="7d8d8-112">Il est possible de contourner la première limitation ci-dessus en passant une connexion fermée et en exécutant uniquement le code qui l’ouvre une fois que tous les contextes ont été créés :</span><span class="sxs-lookup"><span data-stu-id="7d8d8-112">It is possible to work around the first limitation above by passing a closed connection and only executing code that would open it once all contexts have been created:</span></span>  

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

<span data-ttu-id="7d8d8-113">La deuxième limitation signifie simplement que vous devez vous abstenir de supprimer les objets DbContext jusqu’à ce que vous soyez prêt à fermer la connexion.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-113">The second limitation just means you need to refrain from disposing any of your DbContext objects until you are ready for the connection to be closed.</span></span>  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="7d8d8-114">Comportement dans EF6 et les versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="7d8d8-114">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="7d8d8-115">Dans EF6 et les versions ultérieures, DbContext a les deux constructeurs, mais n’exige plus que la connexion passée au constructeur soit fermée lors de sa réception.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-115">In EF6 and future versions the DbContext has the same two constructors but no longer requires that the connection passed to the constructor be closed when it is received.</span></span> <span data-ttu-id="7d8d8-116">C’est maintenant possible :</span><span class="sxs-lookup"><span data-stu-id="7d8d8-116">So this is now possible:</span></span>  

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

<span data-ttu-id="7d8d8-117">En outre, l’indicateur contextOwnsConnection contrôle désormais si la connexion est fermée et supprimée lorsque DbContext est supprimé.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-117">Also the contextOwnsConnection flag now controls whether or not the connection is both closed and disposed when the DbContext is disposed.</span></span> <span data-ttu-id="7d8d8-118">Ainsi, dans l’exemple ci-dessus, la connexion n’est pas fermée lorsque le contexte est supprimé (ligne 32) comme c’était le cas dans les versions précédentes d’EF, mais plutôt lorsque la connexion elle-même est supprimée (ligne 40).</span><span class="sxs-lookup"><span data-stu-id="7d8d8-118">So in the above example the connection is not closed when the context is disposed (line 32) as it would have been in previous versions of EF, but rather when the connection itself is disposed (line 40).</span></span>  

<span data-ttu-id="7d8d8-119">Bien entendu, il est toujours possible pour DbContext de prendre le contrôle de la connexion (il suffit de définir contextOwnsConnection sur true ou d’utiliser l’un des autres constructeurs) si vous le souhaitez.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-119">Of course it is still possible for the DbContext to take control of the connection (just set contextOwnsConnection to true or use one of the other constructors) if you so wish.</span></span>  

> [!NOTE]
> <span data-ttu-id="7d8d8-120">Des considérations supplémentaires sont à prendre en compte lors de l’utilisation de transactions avec ce nouveau modèle.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-120">There are some additional considerations when using transactions with this new model.</span></span> <span data-ttu-id="7d8d8-121">Pour plus d’informations, consultez [utilisation des transactions](~/ef6/saving/transactions.md).</span><span class="sxs-lookup"><span data-stu-id="7d8d8-121">For details see [Working with Transactions](~/ef6/saving/transactions.md).</span></span>  

## <a name="databaseconnectionopen"></a><span data-ttu-id="7d8d8-122">Database. Connection. Open ()</span><span class="sxs-lookup"><span data-stu-id="7d8d8-122">Database.Connection.Open()</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="7d8d8-123">Comportement pour EF5 et les versions antérieures</span><span class="sxs-lookup"><span data-stu-id="7d8d8-123">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="7d8d8-124">Dans EF5 et les versions antérieures, il existe un bogue tel que **ObjectContext. Connection. State** n’a pas été mis à jour pour refléter l’état réel de la connexion du magasin sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-124">In EF5 and earlier versions there is a bug such that the **ObjectContext.Connection.State** was not updated to reflect the true state of the underlying store connection.</span></span> <span data-ttu-id="7d8d8-125">Par exemple, si vous avez exécuté le code suivant, vous pouvez retourner l’état **Closed** même si la connexion à la banque sous-jacente est **ouverte**.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-125">For example, if you executed the following code you can be returned the status **Closed** even though in fact the underlying store connection is **Open**.</span></span>  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

<span data-ttu-id="7d8d8-126">Séparément, si vous ouvrez la connexion à la base de données en appelant Database. Connection. Open (), elle est ouverte jusqu’à la prochaine exécution d’une requête ou l’appel de tout ce qui nécessite une connexion à la base de données (par exemple, SaveChanges ()) mais après que le magasin sous-jacent la connexion va être fermée.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-126">Separately, if you open the database connection by calling Database.Connection.Open() it will be open until the next time you execute a query or call anything which requires a database connection (for example, SaveChanges()) but after that the underlying store connection will be closed.</span></span> <span data-ttu-id="7d8d8-127">Le contexte réouvre et referme la connexion chaque fois qu’une opération de base de données est requise :</span><span class="sxs-lookup"><span data-stu-id="7d8d8-127">The context will then re-open and re-close the connection any time another database operation is required:</span></span>  

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

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="7d8d8-128">Comportement dans EF6 et les versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="7d8d8-128">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="7d8d8-129">Pour les versions EF6 et futures, nous avons pris l’approche selon laquelle le code appelant choisit d’ouvrir la connexion en appelant Context. Database. Connection. Open () ensuite, il a une bonne raison de le faire et le Framework suppose qu’il souhaite contrôler l’ouverture et la fermeture de la connexion et ne fermera plus la connexion automatiquement.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-129">For EF6 and future versions we have taken the approach that if the calling code chooses to open the connection by calling context.Database.Connection.Open() then it has a good reason for doing so and the framework will assume that it wants control over opening and closing of the connection and will no longer close the connection automatically.</span></span>  

> [!NOTE]
> <span data-ttu-id="7d8d8-130">Cela peut aboutir à des connexions qui sont ouvertes pendant une longue période. Utilisez-les avec précaution.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-130">This can potentially lead to connections which are open for a long time so use with care.</span></span>  

<span data-ttu-id="7d8d8-131">Nous avons également mis à jour le code afin qu’ObjectContext. Connection. State effectue désormais le suivi correct de l’état de la connexion sous-jacente.</span><span class="sxs-lookup"><span data-stu-id="7d8d8-131">We also updated the code so that ObjectContext.Connection.State now keeps track of the state of the underlying connection correctly.</span></span>  

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
