---
title: Utilisation des États d’entité-EF6
description: Utilisation des États d’entité dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 88c1b67b3eda02e79f7d10d5e46fdd3566361634
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073767"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="45c0b-103">Utilisation des États d’entité</span><span class="sxs-lookup"><span data-stu-id="45c0b-103">Working with entity states</span></span>
<span data-ttu-id="45c0b-104">Cette rubrique explique comment ajouter et attacher des entités à un contexte et comment Entity Framework les traite au cours de l’opération SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="45c0b-104">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="45c0b-105">Entity Framework effectue le suivi de l’état des entités pendant qu’elles sont connectées à un contexte, mais dans les scénarios déconnectés ou multiniveaux, vous pouvez laisser EF déterminer l’état de vos entités.</span><span class="sxs-lookup"><span data-stu-id="45c0b-105">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="45c0b-106">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="45c0b-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="45c0b-107">États d’entité et SaveChanges</span><span class="sxs-lookup"><span data-stu-id="45c0b-107">Entity states and SaveChanges</span></span>

<span data-ttu-id="45c0b-108">Une entité peut être dans l’un des cinq États définis par l’énumération EntityState.</span><span class="sxs-lookup"><span data-stu-id="45c0b-108">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="45c0b-109">Ces états sont :</span><span class="sxs-lookup"><span data-stu-id="45c0b-109">These states are:</span></span>  

- <span data-ttu-id="45c0b-110">Ajouté : l’entité fait l’objet d’un suivi par le contexte, mais n’existe pas encore dans la base de données</span><span class="sxs-lookup"><span data-stu-id="45c0b-110">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="45c0b-111">Inchangé : l’entité fait l’objet d’un suivi par le contexte et existe dans la base de données, et ses valeurs de propriété n’ont pas changé par rapport aux valeurs de la base de données.</span><span class="sxs-lookup"><span data-stu-id="45c0b-111">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="45c0b-112">Modifié : l’entité fait l’objet d’un suivi par le contexte et existe dans la base de données, et une partie ou la totalité de ses valeurs de propriété a été modifiée.</span><span class="sxs-lookup"><span data-stu-id="45c0b-112">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="45c0b-113">Supprimé : l’entité est suivie par le contexte et existe dans la base de données, mais elle a été marquée pour suppression de la base de données la prochaine fois que SaveChanges est appelé</span><span class="sxs-lookup"><span data-stu-id="45c0b-113">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="45c0b-114">Détaché : l’entité n’est pas suivie par le contexte</span><span class="sxs-lookup"><span data-stu-id="45c0b-114">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="45c0b-115">SaveChanges effectue différentes opérations pour les entités dans différents États :</span><span class="sxs-lookup"><span data-stu-id="45c0b-115">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="45c0b-116">Les entités inchangées ne sont pas touchées par SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="45c0b-116">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="45c0b-117">Les mises à jour ne sont pas envoyées à la base de données pour les entités à l’État inchangé.</span><span class="sxs-lookup"><span data-stu-id="45c0b-117">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="45c0b-118">Les entités ajoutées sont insérées dans la base de données, puis deviennent inchangées lorsque SaveChanges retourne.</span><span class="sxs-lookup"><span data-stu-id="45c0b-118">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="45c0b-119">Les entités modifiées sont mises à jour dans la base de données, puis deviennent inchangées lorsque SaveChanges retourne.</span><span class="sxs-lookup"><span data-stu-id="45c0b-119">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="45c0b-120">Les entités supprimées sont supprimées de la base de données et sont ensuite détachées du contexte.</span><span class="sxs-lookup"><span data-stu-id="45c0b-120">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="45c0b-121">Les exemples suivants montrent comment l’état d’une entité ou d’un graphique d’entité peut être modifié.</span><span class="sxs-lookup"><span data-stu-id="45c0b-121">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="45c0b-122">Ajout d’une nouvelle entité au contexte</span><span class="sxs-lookup"><span data-stu-id="45c0b-122">Adding a new entity to the context</span></span>  

<span data-ttu-id="45c0b-123">Une nouvelle entité peut être ajoutée au contexte en appelant la méthode Add sur DbSet.</span><span class="sxs-lookup"><span data-stu-id="45c0b-123">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="45c0b-124">L’état de l’entité est alors ajouté, ce qui signifie qu’elle sera insérée dans la base de données la prochaine fois que SaveChanges sera appelé.</span><span class="sxs-lookup"><span data-stu-id="45c0b-124">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="45c0b-125">Exemple :</span><span class="sxs-lookup"><span data-stu-id="45c0b-125">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="45c0b-126">Une autre façon d’ajouter une nouvelle entité au contexte consiste à changer son état en ajouté.</span><span class="sxs-lookup"><span data-stu-id="45c0b-126">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="45c0b-127">Exemple :</span><span class="sxs-lookup"><span data-stu-id="45c0b-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="45c0b-128">Enfin, vous pouvez ajouter une nouvelle entité au contexte en la raccordant à une autre entité qui fait déjà l’objet d’un suivi.</span><span class="sxs-lookup"><span data-stu-id="45c0b-128">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="45c0b-129">Cela peut être en ajoutant la nouvelle entité à la propriété de navigation de collection d’une autre entité ou en définissant une propriété de navigation de référence d’une autre entité pour pointer vers la nouvelle entité.</span><span class="sxs-lookup"><span data-stu-id="45c0b-129">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="45c0b-130">Exemple :</span><span class="sxs-lookup"><span data-stu-id="45c0b-130">For example:</span></span>  

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

<span data-ttu-id="45c0b-131">Notez que pour tous ces exemples, si l’entité ajoutée a des références à d’autres entités qui ne sont pas encore suivies, ces nouvelles entités seront également ajoutées au contexte et seront insérées dans la base de données la prochaine fois que SaveChanges sera appelé.</span><span class="sxs-lookup"><span data-stu-id="45c0b-131">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="45c0b-132">Attachement d’une entité existante au contexte</span><span class="sxs-lookup"><span data-stu-id="45c0b-132">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="45c0b-133">Si vous avez une entité que vous connaissez déjà dans la base de données mais qui n’est pas actuellement suivie par le contexte, vous pouvez indiquer au contexte d’effectuer le suivi de l’entité à l’aide de la méthode Attach sur DbSet.</span><span class="sxs-lookup"><span data-stu-id="45c0b-133">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="45c0b-134">L’entité sera à l’État inchangé dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="45c0b-134">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="45c0b-135">Exemple :</span><span class="sxs-lookup"><span data-stu-id="45c0b-135">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="45c0b-136">Notez qu’aucune modification n’est apportée à la base de données si SaveChanges est appelé sans effectuer d’autres manipulations de l’entité attachée.</span><span class="sxs-lookup"><span data-stu-id="45c0b-136">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="45c0b-137">Cela est dû au fait que l’entité est dans l’État inchangé.</span><span class="sxs-lookup"><span data-stu-id="45c0b-137">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="45c0b-138">Une autre façon d’attacher une entité existante au contexte est de changer son état en inchangé.</span><span class="sxs-lookup"><span data-stu-id="45c0b-138">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="45c0b-139">Exemple :</span><span class="sxs-lookup"><span data-stu-id="45c0b-139">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="45c0b-140">Notez que, pour ces deux exemples, si l’entité attachée a des références à d’autres entités qui ne sont pas encore suivies, ces nouvelles entités seront également attachées au contexte dans l’État inchangé.</span><span class="sxs-lookup"><span data-stu-id="45c0b-140">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="45c0b-141">Attachement d’une entité existante mais modifiée au contexte</span><span class="sxs-lookup"><span data-stu-id="45c0b-141">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="45c0b-142">Si vous avez une entité que vous connaissez déjà dans la base de données mais dans laquelle des modifications ont pu être apportées, vous pouvez indiquer au contexte d’attacher l’entité et de définir son état sur modifié.</span><span class="sxs-lookup"><span data-stu-id="45c0b-142">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="45c0b-143">Exemple :</span><span class="sxs-lookup"><span data-stu-id="45c0b-143">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="45c0b-144">Lorsque vous remplacez l’État par modifié, toutes les propriétés de l’entité sont marquées comme étant modifiées et toutes les valeurs de propriété sont envoyées à la base de données lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="45c0b-144">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="45c0b-145">Notez que si l’entité attachée a des références à d’autres entités qui ne sont pas encore suivies, ces nouvelles entités seront attachées au contexte à l’État inchangé, elles ne seront pas automatiquement modifiées.</span><span class="sxs-lookup"><span data-stu-id="45c0b-145">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="45c0b-146">Si vous avez plusieurs entités qui doivent être marquées comme étant modifiées, vous devez définir l’état de chacune de ces entités individuellement.</span><span class="sxs-lookup"><span data-stu-id="45c0b-146">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="45c0b-147">Modification de l’état d’une entité suivie</span><span class="sxs-lookup"><span data-stu-id="45c0b-147">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="45c0b-148">Vous pouvez modifier l’état d’une entité qui fait déjà l’objet d’un suivi en définissant la propriété State sur son entrée.</span><span class="sxs-lookup"><span data-stu-id="45c0b-148">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="45c0b-149">Exemple :</span><span class="sxs-lookup"><span data-stu-id="45c0b-149">For example:</span></span>  

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

<span data-ttu-id="45c0b-150">Notez que l’appel de Add ou Attach pour une entité déjà suivie peut également être utilisé pour modifier l’état de l’entité.</span><span class="sxs-lookup"><span data-stu-id="45c0b-150">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="45c0b-151">Par exemple, l’appel de Attach pour une entité qui est actuellement à l’état Added passe à l’État inchangé.</span><span class="sxs-lookup"><span data-stu-id="45c0b-151">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="45c0b-152">Insérer ou mettre à jour le modèle</span><span class="sxs-lookup"><span data-stu-id="45c0b-152">Insert or update pattern</span></span>  

<span data-ttu-id="45c0b-153">Un modèle commun pour certaines applications consiste à ajouter une entité en tant que nouvelle (ce qui entraîne l’insertion d’une base de données) ou à attacher une entité comme existante et à la marquer comme modifiée (entraînant une mise à jour de la base de données) en fonction de la valeur de la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="45c0b-153">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="45c0b-154">Par exemple, lorsque vous utilisez des clés primaires entières générées par la base de données, il est courant de traiter une entité avec une clé de zéro comme nouvelle et une entité avec une clé non nulle comme existante.</span><span class="sxs-lookup"><span data-stu-id="45c0b-154">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="45c0b-155">Ce modèle peut être obtenu en définissant l’état de l’entité sur la base d’une vérification de la valeur de la clé primaire.</span><span class="sxs-lookup"><span data-stu-id="45c0b-155">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="45c0b-156">Exemple :</span><span class="sxs-lookup"><span data-stu-id="45c0b-156">For example:</span></span>  

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

<span data-ttu-id="45c0b-157">Notez que lorsque vous remplacez l’État par modifié, toutes les propriétés de l’entité sont marquées comme étant modifiées et toutes les valeurs de propriété sont envoyées à la base de données lorsque SaveChanges est appelé.</span><span class="sxs-lookup"><span data-stu-id="45c0b-157">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
