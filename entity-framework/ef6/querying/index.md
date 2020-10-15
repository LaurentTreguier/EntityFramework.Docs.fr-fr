---
title: Interrogation et recherche d’entités - EF6
description: Interrogation et recherche d’entités dans Entity Framework 6.
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/index
ms.openlocfilehash: ec0239ed54099226d009031af79388f5ae00cdc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065891"
---
# <a name="querying-and-finding-entities"></a>Interrogation et recherche d’entités
Cette rubrique décrit les différentes méthodes de recherche de données à l’aide d’Entity Framework, y compris LINQ et la méthode Find. Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.  

## <a name="finding-entities-using-a-query"></a>Recherche d’entités à l’aide d’une requête  

DbSet et IDbSet implémentent IQueryable et peuvent donc servir de point de départ pour écrire une requête LINQ sur la base de données. Nous ne décrirons pas LINQ en détail ici, mais voici quelques exemples simples :  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

Notez que DbSet et IDbSet créent toujours des requêtes sur la base de données et impliquent toujours un aller-retour vers la base de données même si les entités retournées existent déjà dans le contexte. Une requête est exécutée sur la base de données quand :  

- Elle est énumérée par une instruction **foreach** (C#) ou **For Each** (Visual Basic).  
- Elle est énumérée par une opération de collection comme [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary) ou [ToList](https://msdn.microsoft.com/library/bb342261).  
- Des opérateurs LINQ, comme [First](https://msdn.microsoft.com/library/bb291976) ou [Any](https://msdn.microsoft.com/library/bb337697) sont spécifiés dans la partie la plus extérieure de la requête.  
- Les méthodes suivantes sont appelées : la méthode d’extension [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) sur DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx) et Database.ExecuteSqlCommand.  

Quand les résultats sont retournés à partir de la base de données, les objets qui n’existent pas dans le contexte sont attachés au contexte. Si un objet est déjà dans le contexte, l’objet existant est retourné (les valeurs actuelles et d’origine des propriétés de l’objet dans l’entrée ne sont **pas** remplacées par les valeurs de la base de données).  

Quand vous exécutez une requête, les entités qui ont été ajoutées au contexte, mais n’ont pas encore été enregistrées dans la base de données, ne sont pas retournées dans le jeu de résultats. Pour obtenir les données du contexte, consultez [Données locales](xref:ef6/querying/local-data).  

Si une requête ne retourne aucune ligne de la base de données, le résultat est une collection vide au lieu de **null**.  

## <a name="finding-entities-using-primary-keys"></a>Recherche d’entités à l’aide de clés primaires  

La méthode Find sur DbSet utilise la valeur de clé primaire pour tenter de trouver une entité suivie par le contexte. Si l’entité est introuvable dans le contexte, une requête est envoyée à la base de données pour y rechercher l’entité. Null est retourné si l’entité est introuvable dans le contexte ou dans la base de données.  

Find est différent de l’utilisation d’une requête pour deux raisons :  

- Un aller-retour vers la base de données est effectué uniquement si l’entité avec la clé spécifiée est introuvable dans le contexte.  
- Find retourne les entités qui sont dans l’état Added. Autrement dit, Find retourne les entités qui ont été ajoutées au contexte, mais n’ont pas encore été enregistrées dans la base de données.  
### <a name="finding-an-entity-by-primary-key"></a>Recherche d’une entité par clé primaire  

Le code suivant montre des exemples d’utilisation de Find :  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a>Recherche d’une entité par clé primaire composite  

Entity Framework permet à vos entités d’avoir des clés composites, c’est-à-dire des clés composées de plusieurs propriétés. Par exemple, vous avez une entité BlogSettings qui représente des paramètres utilisateur pour un blog particulier. Comme un utilisateur n’aura jamais une entité BlogSettings pour chaque blog, vous pouvez choisir pour BlogSettings une clé primaire qui est une combinaison de BlogId et Username. Le code suivant tente de rechercher l’entité BlogSettings avec BlogId = 3 et Username = "johndoe1987" :  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

Quand vous avez des clés composites, vous devez utiliser ColumnAttribute ou l’API Fluent pour spécifier l’ordre des propriétés de la clé composite. L’appel de Find doit utiliser cet ordre quand vous spécifiez les valeurs qui forment la clé.  
