---
title: Requêtes SQL brutes-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
ms.openlocfilehash: 168aee67186535bf2a50705e86bfc5a88147e369
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417094"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="7ccd7-102">Requêtes SQL brutes</span><span class="sxs-lookup"><span data-stu-id="7ccd7-102">Raw SQL Queries</span></span>
<span data-ttu-id="7ccd7-103">Entity Framework vous permet d’interroger à l’aide de LINQ avec vos classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-103">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="7ccd7-104">Toutefois, il peut arriver que vous souhaitiez exécuter des requêtes à l’aide de SQL brut directement sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-104">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="7ccd7-105">Cela comprend l’appel de procédures stockées, qui peuvent être utiles pour les modèles de Code First qui ne prennent pas actuellement en charge le mappage à des procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-105">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="7ccd7-106">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="7ccd7-107">Écriture de requêtes SQL pour des entités</span><span class="sxs-lookup"><span data-stu-id="7ccd7-107">Writing SQL queries for entities</span></span>  

<span data-ttu-id="7ccd7-108">La méthode SqlQuery sur DbSet permet l’écriture d’une requête SQL brute qui renverra des instances d’entité.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-108">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="7ccd7-109">Les objets retournés feront l’objet d’un suivi par le contexte comme s’ils étaient retournés par une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-109">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="7ccd7-110">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="7ccd7-110">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="7ccd7-111">Notez que, tout comme pour les requêtes LINQ, la requête n’est pas exécutée tant que les résultats ne sont pas énumérés. dans l’exemple ci-dessus, cette opération est effectuée avec l’appel à ToList.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-111">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="7ccd7-112">Il convient de prendre des précautions à chaque fois que des requêtes SQL brutes sont écrites pour deux raisons.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-112">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="7ccd7-113">Premièrement, la requête doit être écrite pour s’assurer qu’elle retourne uniquement les entités qui sont vraiment du type demandé.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-113">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="7ccd7-114">Par exemple, lorsque vous utilisez des fonctionnalités telles que l’héritage, il est facile d’écrire une requête qui créera des entités dont le type CLR est incorrect.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-114">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="7ccd7-115">Deuxièmement, certains types de requêtes SQL brutes présentent des risques de sécurité potentiels, en particulier autour des attaques par injection SQL.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-115">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="7ccd7-116">Veillez à utiliser les paramètres de votre requête de manière appropriée pour vous protéger contre ces attaques.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-116">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="7ccd7-117">Chargement d’entités à partir de procédures stockées</span><span class="sxs-lookup"><span data-stu-id="7ccd7-117">Loading entities from stored procedures</span></span>  

<span data-ttu-id="7ccd7-118">Vous pouvez utiliser DbSet. SqlQuery pour charger des entités à partir des résultats d’une procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-118">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="7ccd7-119">Par exemple, le code suivant appelle dbo. Procédure GetBlogs dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="7ccd7-119">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="7ccd7-120">Vous pouvez également transmettre des paramètres à une procédure stockée à l’aide de la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="7ccd7-120">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="7ccd7-121">Écriture de requêtes SQL pour les types non-entité</span><span class="sxs-lookup"><span data-stu-id="7ccd7-121">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="7ccd7-122">Une requête SQL qui retourne des instances de tout type, y compris des types primitifs, peut être créée à l’aide de la méthode SqlQuery sur la classe de base de données.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-122">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="7ccd7-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="7ccd7-123">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="7ccd7-124">Les résultats retournés à partir de SqlQuery sur la base de données ne feront jamais l’objet d’un suivi par le contexte même si les objets sont des instances d’un type d’entité.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-124">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="7ccd7-125">Envoi de commandes brutes à la base de données</span><span class="sxs-lookup"><span data-stu-id="7ccd7-125">Sending raw commands to the database</span></span>  

<span data-ttu-id="7ccd7-126">Les commandes qui ne sont pas des requêtes peuvent être envoyées à la base de données à l’aide de la méthode ExecuteSqlCommand sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-126">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="7ccd7-127">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="7ccd7-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="7ccd7-128">Notez que toutes les modifications apportées aux données de la base de données à l’aide de ExecuteSqlCommand sont opaques dans le contexte jusqu’à ce que les entités soient chargées ou rechargées à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-128">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="7ccd7-129">Paramètres de sortie</span><span class="sxs-lookup"><span data-stu-id="7ccd7-129">Output Parameters</span></span>  

<span data-ttu-id="7ccd7-130">Si des paramètres de sortie sont utilisés, leurs valeurs ne sont pas disponibles tant que les résultats n’ont pas été entièrement lus.</span><span class="sxs-lookup"><span data-stu-id="7ccd7-130">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="7ccd7-131">Cela est dû au comportement sous-jacent de DbDataReader. pour plus d’informations, consultez [récupération de données à l’aide d’un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) .</span><span class="sxs-lookup"><span data-stu-id="7ccd7-131">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  
