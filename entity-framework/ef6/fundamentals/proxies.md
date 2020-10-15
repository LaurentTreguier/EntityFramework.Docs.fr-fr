---
title: Utilisation des proxies-EF6
description: Utilisation des proxies dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/proxies
ms.openlocfilehash: 741fd72ee66b98ab132fb85f71c3101712e433fa
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063203"
---
# <a name="working-with-proxies"></a>Utilisation des proxies
Lors de la création d’instances de types d’entités POCO, Entity Framework crée souvent des instances d’un type dérivé généré de manière dynamique qui agit comme un proxy pour l’entité. Ce proxy remplace certaines propriétés virtuelles de l’entité pour insérer des raccordements pour l’exécution automatique des actions lors de l’accès à la propriété. Par exemple, ce mécanisme est utilisé pour prendre en charge le chargement différé des relations. Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.  

## <a name="disabling-proxy-creation"></a>Désactivation de la création de proxy  

Il est parfois utile d’empêcher Entity Framework de créer des instances de proxy. Par exemple, la sérialisation des instances non proxy est beaucoup plus facile que la sérialisation des instances de proxy. La création de proxy peut être désactivée en désactivant l’indicateur ProxyCreationEnabled. Vous pouvez effectuer cette opération dans le constructeur de votre contexte. Par exemple :  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

Notez que le EF ne crée pas de proxy pour les types où il n’y a rien à faire pour le proxy. Cela signifie que vous pouvez également éviter les proxies en ayant des types qui sont sealed et/ou n’ont pas de propriétés virtuelles.  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a>Création explicite d’une instance d’un proxy  

Une instance de proxy ne sera pas créée si vous créez une instance d’une entité à l’aide de l’opérateur New. Ce n’est peut-être pas un problème, mais si vous avez besoin de créer une instance de proxy (par exemple, afin que le chargement différé ou le suivi des modifications de proxy fonctionnent), vous pouvez le faire à l’aide de la méthode Create de DbSet. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

La version générique de Create peut être utilisée si vous souhaitez créer une instance d’un type d’entité dérivé. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

Notez que la méthode Create n’ajoute pas ou n’attache pas l’entité créée au contexte.  

Notez que la méthode Create crée simplement une instance du type d’entité lui-même si la création d’un type de proxy pour l’entité n’aurait aucune valeur, car elle ne fait rien. Par exemple, si le type d’entité est sealed et/ou n’a pas de propriétés virtuelles, Create crée simplement une instance du type d’entité.  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a>Obtention du type d’entité réel à partir d’un type de proxy  

Les types de proxy ont des noms qui ressemblent à ceci :  

System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6  

Vous pouvez trouver le type d’entité pour ce type de proxy à l’aide de la méthode GetObjectType d’ObjectContext. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

Notez que si le type passé à GetObjectType est une instance d’un type d’entité qui n’est pas un type de proxy, le type d’entité est toujours retourné. Cela signifie que vous pouvez toujours utiliser cette méthode pour obtenir le type d’entité réel sans autre vérification pour vérifier si le type est un type de proxy ou non.  
