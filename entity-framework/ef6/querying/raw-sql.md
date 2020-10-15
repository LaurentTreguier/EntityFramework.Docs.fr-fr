---
title: Requêtes SQL brutes-EF6
description: Requêtes SQL brutes dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/raw-sql
ms.openlocfilehash: da813ede818b24a5e7930202bfa761d65e4e6b72
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064716"
---
# <a name="raw-sql-queries-ef6"></a>Requêtes SQL brutes (EF6)

Entity Framework vous permet d’interroger à l’aide de LINQ avec vos classes d’entité. Toutefois, il peut arriver que vous souhaitiez exécuter des requêtes à l’aide de SQL brut directement sur la base de données. Cela comprend l’appel de procédures stockées, qui peuvent être utiles pour les modèles de Code First qui ne prennent pas actuellement en charge le mappage à des procédures stockées. Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.  

## <a name="writing-sql-queries-for-entities"></a>Écriture de requêtes SQL pour des entités  

La méthode SqlQuery sur DbSet permet l’écriture d’une requête SQL brute qui renverra des instances d’entité. Les objets retournés feront l’objet d’un suivi par le contexte comme s’ils étaient retournés par une requête LINQ. Par exemple :  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

Notez que, tout comme pour les requêtes LINQ, la requête n’est pas exécutée tant que les résultats ne sont pas énumérés. dans l’exemple ci-dessus, cette opération est effectuée avec l’appel à ToList.  

Il convient de prendre des précautions à chaque fois que des requêtes SQL brutes sont écrites pour deux raisons. Premièrement, la requête doit être écrite pour s’assurer qu’elle retourne uniquement les entités qui sont vraiment du type demandé. Par exemple, lorsque vous utilisez des fonctionnalités telles que l’héritage, il est facile d’écrire une requête qui créera des entités dont le type CLR est incorrect.  

Deuxièmement, certains types de requêtes SQL brutes présentent des risques de sécurité potentiels, en particulier autour des attaques par injection SQL. Veillez à utiliser les paramètres de votre requête de manière appropriée pour vous protéger contre ces attaques.  

### <a name="loading-entities-from-stored-procedures"></a>Chargement d’entités à partir de procédures stockées  

Vous pouvez utiliser DbSet. SqlQuery pour charger des entités à partir des résultats d’une procédure stockée. Par exemple, le code suivant appelle dbo. Procédure GetBlogs dans la base de données :  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

Vous pouvez également transmettre des paramètres à une procédure stockée à l’aide de la syntaxe suivante :  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a>Écriture de requêtes SQL pour les types non-entité  

Une requête SQL qui retourne des instances de tout type, y compris des types primitifs, peut être créée à l’aide de la méthode SqlQuery sur la classe de base de données. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

Les résultats retournés à partir de SqlQuery sur la base de données ne feront jamais l’objet d’un suivi par le contexte même si les objets sont des instances d’un type d’entité.  

## <a name="sending-raw-commands-to-the-database"></a>Envoi de commandes brutes à la base de données  

Les commandes qui ne sont pas des requêtes peuvent être envoyées à la base de données à l’aide de la méthode ExecuteSqlCommand sur la base de données. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

Notez que toutes les modifications apportées aux données de la base de données à l’aide de ExecuteSqlCommand sont opaques dans le contexte jusqu’à ce que les entités soient chargées ou rechargées à partir de la base de données.  

### <a name="output-parameters"></a>Paramètres de sortie  

Si des paramètres de sortie sont utilisés, leurs valeurs ne sont pas disponibles tant que les résultats n’ont pas été entièrement lus. Cela est dû au comportement sous-jacent de DbDataReader. pour plus d’informations, consultez [récupération de données à l’aide d’un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) .  
