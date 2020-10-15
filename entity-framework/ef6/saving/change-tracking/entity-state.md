---
title: Utilisation des États d’entité-EF6
description: Utilisation des États d’entité dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 5373f7157882062e8a73b2bd414c6a8b9accdba4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064456"
---
# <a name="working-with-entity-states"></a>Utilisation des États d’entité
Cette rubrique explique comment ajouter et attacher des entités à un contexte et comment Entity Framework les traite au cours de l’opération SaveChanges.
Entity Framework effectue le suivi de l’état des entités pendant qu’elles sont connectées à un contexte, mais dans les scénarios déconnectés ou multiniveaux, vous pouvez laisser EF déterminer l’état de vos entités.
Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.  

## <a name="entity-states-and-savechanges"></a>États d’entité et SaveChanges

Une entité peut être dans l’un des cinq États définis par l’énumération EntityState. Ces états sont :  

- Ajouté : l’entité fait l’objet d’un suivi par le contexte, mais n’existe pas encore dans la base de données  
- Inchangé : l’entité fait l’objet d’un suivi par le contexte et existe dans la base de données, et ses valeurs de propriété n’ont pas changé par rapport aux valeurs de la base de données.  
- Modifié : l’entité fait l’objet d’un suivi par le contexte et existe dans la base de données, et une partie ou la totalité de ses valeurs de propriété a été modifiée.  
- Supprimé : l’entité est suivie par le contexte et existe dans la base de données, mais elle a été marquée pour suppression de la base de données la prochaine fois que SaveChanges est appelé  
- Détaché : l’entité n’est pas suivie par le contexte  

SaveChanges effectue différentes opérations pour les entités dans différents États :  

- Les entités inchangées ne sont pas touchées par SaveChanges. Les mises à jour ne sont pas envoyées à la base de données pour les entités à l’État inchangé.  
- Les entités ajoutées sont insérées dans la base de données, puis deviennent inchangées lorsque SaveChanges retourne.  
- Les entités modifiées sont mises à jour dans la base de données, puis deviennent inchangées lorsque SaveChanges retourne.  
- Les entités supprimées sont supprimées de la base de données et sont ensuite détachées du contexte.  

Les exemples suivants montrent comment l’état d’une entité ou d’un graphique d’entité peut être modifié.  

## <a name="adding-a-new-entity-to-the-context"></a>Ajout d’une nouvelle entité au contexte  

Une nouvelle entité peut être ajoutée au contexte en appelant la méthode Add sur DbSet.
L’état de l’entité est alors ajouté, ce qui signifie qu’elle sera insérée dans la base de données la prochaine fois que SaveChanges sera appelé.
Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

Une autre façon d’ajouter une nouvelle entité au contexte consiste à changer son état en ajouté. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

Enfin, vous pouvez ajouter une nouvelle entité au contexte en la raccordant à une autre entité qui fait déjà l’objet d’un suivi.
Cela peut être en ajoutant la nouvelle entité à la propriété de navigation de collection d’une autre entité ou en définissant une propriété de navigation de référence d’une autre entité pour pointer vers la nouvelle entité. Par exemple :  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

Notez que pour tous ces exemples, si l’entité ajoutée a des références à d’autres entités qui ne sont pas encore suivies, ces nouvelles entités seront également ajoutées au contexte et seront insérées dans la base de données la prochaine fois que SaveChanges sera appelé.  

## <a name="attaching-an-existing-entity-to-the-context"></a>Attachement d’une entité existante au contexte  

Si vous avez une entité que vous connaissez déjà dans la base de données mais qui n’est pas actuellement suivie par le contexte, vous pouvez indiquer au contexte d’effectuer le suivi de l’entité à l’aide de la méthode Attach sur DbSet. L’entité sera à l’État inchangé dans le contexte. Par exemple :  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

Notez qu’aucune modification n’est apportée à la base de données si SaveChanges est appelé sans effectuer d’autres manipulations de l’entité attachée. Cela est dû au fait que l’entité est dans l’État inchangé.  

Une autre façon d’attacher une entité existante au contexte est de changer son état en inchangé. Par exemple :  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

Notez que, pour ces deux exemples, si l’entité attachée a des références à d’autres entités qui ne sont pas encore suivies, ces nouvelles entités seront également attachées au contexte dans l’État inchangé.  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a>Attachement d’une entité existante mais modifiée au contexte  

Si vous avez une entité que vous connaissez déjà dans la base de données mais dans laquelle des modifications ont pu être apportées, vous pouvez indiquer au contexte d’attacher l’entité et de définir son état sur modifié.
Par exemple :  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

Lorsque vous remplacez l’État par modifié, toutes les propriétés de l’entité sont marquées comme étant modifiées et toutes les valeurs de propriété sont envoyées à la base de données lorsque SaveChanges est appelé.  

Notez que si l’entité attachée a des références à d’autres entités qui ne sont pas encore suivies, ces nouvelles entités seront attachées au contexte à l’État inchangé, elles ne seront pas automatiquement modifiées.
Si vous avez plusieurs entités qui doivent être marquées comme étant modifiées, vous devez définir l’état de chacune de ces entités individuellement.  

## <a name="changing-the-state-of-a-tracked-entity"></a>Modification de l’état d’une entité suivie  

Vous pouvez modifier l’état d’une entité qui fait déjà l’objet d’un suivi en définissant la propriété State sur son entrée. Par exemple :  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

Notez que l’appel de Add ou Attach pour une entité déjà suivie peut également être utilisé pour modifier l’état de l’entité. Par exemple, l’appel de Attach pour une entité qui est actuellement à l’état Added passe à l’État inchangé.  

## <a name="insert-or-update-pattern"></a>Insérer ou mettre à jour le modèle  

Un modèle commun pour certaines applications consiste à ajouter une entité en tant que nouvelle (ce qui entraîne l’insertion d’une base de données) ou à attacher une entité comme existante et à la marquer comme modifiée (entraînant une mise à jour de la base de données) en fonction de la valeur de la clé primaire.
Par exemple, lorsque vous utilisez des clés primaires entières générées par la base de données, il est courant de traiter une entité avec une clé de zéro comme nouvelle et une entité avec une clé non nulle comme existante.
Ce modèle peut être obtenu en définissant l’état de l’entité sur la base d’une vérification de la valeur de la clé primaire. Par exemple :  

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

Notez que lorsque vous remplacez l’État par modifié, toutes les propriétés de l’entité sont marquées comme étant modifiées et toutes les valeurs de propriété sont envoyées à la base de données lorsque SaveChanges est appelé.  
