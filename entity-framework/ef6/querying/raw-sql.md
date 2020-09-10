---
title: Requêtes SQL brutes-EF6
description: Requêtes SQL brutes dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
uid: ef6/querying/raw-sql
ms.openlocfilehash: 0db5069b23ff9aa65ced2dbe48694e4f20c98e51
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620285"
---
# <a name="raw-sql-queries-ef6"></a><span data-ttu-id="d8cb4-103">Requêtes SQL brutes (EF6)</span><span class="sxs-lookup"><span data-stu-id="d8cb4-103">Raw SQL Queries (EF6)</span></span>

<span data-ttu-id="d8cb4-104">Entity Framework vous permet d’interroger à l’aide de LINQ avec vos classes d’entité.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-104">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="d8cb4-105">Toutefois, il peut arriver que vous souhaitiez exécuter des requêtes à l’aide de SQL brut directement sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-105">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="d8cb4-106">Cela comprend l’appel de procédures stockées, qui peuvent être utiles pour les modèles de Code First qui ne prennent pas actuellement en charge le mappage à des procédures stockées.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-106">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="d8cb4-107">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="d8cb4-108">Écriture de requêtes SQL pour des entités</span><span class="sxs-lookup"><span data-stu-id="d8cb4-108">Writing SQL queries for entities</span></span>  

<span data-ttu-id="d8cb4-109">La méthode SqlQuery sur DbSet permet l’écriture d’une requête SQL brute qui renverra des instances d’entité.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-109">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="d8cb4-110">Les objets retournés feront l’objet d’un suivi par le contexte comme s’ils étaient retournés par une requête LINQ.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-110">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="d8cb4-111">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d8cb4-111">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="d8cb4-112">Notez que, tout comme pour les requêtes LINQ, la requête n’est pas exécutée tant que les résultats ne sont pas énumérés. dans l’exemple ci-dessus, cette opération est effectuée avec l’appel à ToList.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-112">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="d8cb4-113">Il convient de prendre des précautions à chaque fois que des requêtes SQL brutes sont écrites pour deux raisons.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-113">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="d8cb4-114">Premièrement, la requête doit être écrite pour s’assurer qu’elle retourne uniquement les entités qui sont vraiment du type demandé.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-114">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="d8cb4-115">Par exemple, lorsque vous utilisez des fonctionnalités telles que l’héritage, il est facile d’écrire une requête qui créera des entités dont le type CLR est incorrect.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-115">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="d8cb4-116">Deuxièmement, certains types de requêtes SQL brutes présentent des risques de sécurité potentiels, en particulier autour des attaques par injection SQL.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-116">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="d8cb4-117">Veillez à utiliser les paramètres de votre requête de manière appropriée pour vous protéger contre ces attaques.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-117">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="d8cb4-118">Chargement d’entités à partir de procédures stockées</span><span class="sxs-lookup"><span data-stu-id="d8cb4-118">Loading entities from stored procedures</span></span>  

<span data-ttu-id="d8cb4-119">Vous pouvez utiliser DbSet. SqlQuery pour charger des entités à partir des résultats d’une procédure stockée.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-119">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="d8cb4-120">Par exemple, le code suivant appelle dbo. Procédure GetBlogs dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="d8cb4-120">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="d8cb4-121">Vous pouvez également transmettre des paramètres à une procédure stockée à l’aide de la syntaxe suivante :</span><span class="sxs-lookup"><span data-stu-id="d8cb4-121">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="d8cb4-122">Écriture de requêtes SQL pour les types non-entité</span><span class="sxs-lookup"><span data-stu-id="d8cb4-122">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="d8cb4-123">Une requête SQL qui retourne des instances de tout type, y compris des types primitifs, peut être créée à l’aide de la méthode SqlQuery sur la classe de base de données.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-123">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="d8cb4-124">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d8cb4-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="d8cb4-125">Les résultats retournés à partir de SqlQuery sur la base de données ne feront jamais l’objet d’un suivi par le contexte même si les objets sont des instances d’un type d’entité.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-125">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="d8cb4-126">Envoi de commandes brutes à la base de données</span><span class="sxs-lookup"><span data-stu-id="d8cb4-126">Sending raw commands to the database</span></span>  

<span data-ttu-id="d8cb4-127">Les commandes qui ne sont pas des requêtes peuvent être envoyées à la base de données à l’aide de la méthode ExecuteSqlCommand sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-127">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="d8cb4-128">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d8cb4-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="d8cb4-129">Notez que toutes les modifications apportées aux données de la base de données à l’aide de ExecuteSqlCommand sont opaques dans le contexte jusqu’à ce que les entités soient chargées ou rechargées à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-129">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="d8cb4-130">Paramètres de sortie</span><span class="sxs-lookup"><span data-stu-id="d8cb4-130">Output Parameters</span></span>  

<span data-ttu-id="d8cb4-131">Si des paramètres de sortie sont utilisés, leurs valeurs ne sont pas disponibles tant que les résultats n’ont pas été entièrement lus.</span><span class="sxs-lookup"><span data-stu-id="d8cb4-131">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="d8cb4-132">Cela est dû au comportement sous-jacent de DbDataReader. pour plus d’informations, consultez [récupération de données à l’aide d’un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) .</span><span class="sxs-lookup"><span data-stu-id="d8cb4-132">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  
