---
title: Accès aux entités suivies-EF Core
description: Utilisation de EntityEntry, DbContext. Entries et DbSet. local pour accéder aux entités suivies
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: f385016aba61535f33e34c622dd43ce6dc823fc5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129734"
---
# <a name="accessing-tracked-entities"></a><span data-ttu-id="22386-103">Accès aux entités suivies</span><span class="sxs-lookup"><span data-stu-id="22386-103">Accessing Tracked Entities</span></span>

<span data-ttu-id="22386-104">Il existe quatre API principales pour accéder aux entités suivies par <xref:Microsoft.EntityFrameworkCore.DbContext> :</span><span class="sxs-lookup"><span data-stu-id="22386-104">There are four main APIs for accessing entities tracked by a <xref:Microsoft.EntityFrameworkCore.DbContext>:</span></span>

- <span data-ttu-id="22386-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> retourne une <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instance pour une instance d’entité donnée.</span><span class="sxs-lookup"><span data-stu-id="22386-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> returns an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instance for a given entity instance.</span></span>
- <span data-ttu-id="22386-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> retourne des <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances pour toutes les entités suivies, ou pour toutes les entités suivies d’un type donné.</span><span class="sxs-lookup"><span data-stu-id="22386-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> returns <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances for all tracked entities, or for all tracked entities of a given type.</span></span>
- <span data-ttu-id="22386-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> recherchent une entité unique par clé primaire, en examinant d’abord les entités suivies, puis en interrogeant la base de données si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="22386-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> find a single entity by primary key, first looking in tracked entities, and then querying the database if needed.</span></span>
- <span data-ttu-id="22386-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> retourne des entités réelles (et non des instances EntityEntry) pour les entités du type d’entité représenté par DbSet.</span><span class="sxs-lookup"><span data-stu-id="22386-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns actual entities (not EntityEntry instances) for entities of the entity type represented by the DbSet.</span></span>

<span data-ttu-id="22386-109">Chacun d’eux est décrit plus en détail dans les sections ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="22386-109">Each of these is described in more detail in the sections below.</span></span>

> [!TIP]
> <span data-ttu-id="22386-110">Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris.</span><span class="sxs-lookup"><span data-stu-id="22386-110">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="22386-111">Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="22386-111">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="22386-112">Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).</span><span class="sxs-lookup"><span data-stu-id="22386-112">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).</span></span>

## <a name="using-dbcontextentry-and-entityentry-instances"></a><span data-ttu-id="22386-113">Utilisation d’instances DbContext. Entry et EntityEntry</span><span class="sxs-lookup"><span data-stu-id="22386-113">Using DbContext.Entry and EntityEntry instances</span></span>

<span data-ttu-id="22386-114">Pour chaque entité suivie, Entity Framework Core (EF Core) effectue le suivi des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="22386-114">For each tracked entity, Entity Framework Core (EF Core) keeps track of:</span></span>

- <span data-ttu-id="22386-115">État global de l’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-115">The overall state of the entity.</span></span> <span data-ttu-id="22386-116">Il s’agit de `Unchanged` , `Modified` , `Added` ou `Deleted` ; pour plus d’informations, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="22386-116">This is one of `Unchanged`, `Modified`, `Added`, or `Deleted`; see [Change Tracking in EF Core](xref:core/change-tracking/index) for more information.</span></span>
- <span data-ttu-id="22386-117">Relations entre les entités suivies.</span><span class="sxs-lookup"><span data-stu-id="22386-117">The relationships between tracked entities.</span></span> <span data-ttu-id="22386-118">Par exemple, le blog auquel appartient une publication.</span><span class="sxs-lookup"><span data-stu-id="22386-118">For example, the blog to which a post belongs.</span></span>
- <span data-ttu-id="22386-119">« Valeurs actuelles » des propriétés.</span><span class="sxs-lookup"><span data-stu-id="22386-119">The "current values" of properties.</span></span>
- <span data-ttu-id="22386-120">Les « valeurs d’origine » des propriétés, lorsque ces informations sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="22386-120">The "original values" of properties, when this information is available.</span></span> <span data-ttu-id="22386-121">Les valeurs d’origine sont les valeurs de propriété qui existaient lors de l’interrogation d’une entité à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="22386-121">Original values are the property values that existed when entity was queried from the database.</span></span>
- <span data-ttu-id="22386-122">Valeurs de propriété qui ont été modifiées depuis leur interrogation.</span><span class="sxs-lookup"><span data-stu-id="22386-122">Which property values have been modified since they were queried.</span></span>
- <span data-ttu-id="22386-123">Autres informations sur les valeurs de propriété, par exemple si la valeur est [temporaire](xref:core/change-tracking/miscellaneous#temporary-values)ou non.</span><span class="sxs-lookup"><span data-stu-id="22386-123">Other information about property values, such as whether or not the value is [temporary](xref:core/change-tracking/miscellaneous#temporary-values).</span></span>

<span data-ttu-id="22386-124">Le passage d’une instance d’entité à <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> entraîne <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> la fourniture d’un accès à ces informations pour l’entité donnée.</span><span class="sxs-lookup"><span data-stu-id="22386-124">Passing an entity instance to <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> results in an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> providing access to this information for the given entity.</span></span> <span data-ttu-id="22386-125">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-125">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

<span data-ttu-id="22386-126">Les sections suivantes montrent comment utiliser un EntityEntry pour accéder à l’état de l’entité et le manipuler, ainsi que l’état des propriétés et des navigations de l’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-126">The following sections show how to use an EntityEntry to access and manipulate entity state, as well as the state of the entity's properties and navigations.</span></span>

### <a name="working-with-the-entity"></a><span data-ttu-id="22386-127">Utilisation de l’entité</span><span class="sxs-lookup"><span data-stu-id="22386-127">Working with the entity</span></span>

<span data-ttu-id="22386-128">L’utilisation la plus courante de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> est d’accéder au actuel <xref:Microsoft.EntityFrameworkCore.EntityState> d’une entité.</span><span class="sxs-lookup"><span data-stu-id="22386-128">The most common use of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> is to access the current <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity.</span></span> <span data-ttu-id="22386-129">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-129">For example:</span></span>

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

<span data-ttu-id="22386-130">La méthode d’entrée peut également être utilisée sur des entités qui ne sont pas encore suivies.</span><span class="sxs-lookup"><span data-stu-id="22386-130">The Entry method can also be used on entities that are not yet tracked.</span></span> <span data-ttu-id="22386-131">Cela _ne démarre pas le suivi de l’entité_; l’état de l’entité est toujours `Detatched` .</span><span class="sxs-lookup"><span data-stu-id="22386-131">This _does not start tracking the entity_; the state of the entity is still `Detatched`.</span></span> <span data-ttu-id="22386-132">Toutefois, le EntityEntry retourné peut ensuite être utilisé pour modifier l’état de l’entité. à partir de là, l’entité sera suivie dans l’État donné.</span><span class="sxs-lookup"><span data-stu-id="22386-132">However, the returned EntityEntry can then be used to change the entity state, at which point the entity will become tracked in the given state.</span></span> <span data-ttu-id="22386-133">Par exemple, le code suivant démarre le suivi d’une instance de blog comme `Added` suit :</span><span class="sxs-lookup"><span data-stu-id="22386-133">For example, the following code will start tracking a Blog instance as `Added`:</span></span>

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> <span data-ttu-id="22386-134">Contrairement à EF6, la définition de l’état d’une entité individuelle n’entraîne pas le suivi de toutes les entités connectées.</span><span class="sxs-lookup"><span data-stu-id="22386-134">Unlike in EF6, setting the state of an individual entity will not cause all connected entities to be tracked.</span></span> <span data-ttu-id="22386-135">Cela permet de définir l’état de manière à ce qu’une opération de niveau inférieur appelle `Add` , `Attach` ou `Update` , qui opère sur l’intégralité d’un graphique d’entités.</span><span class="sxs-lookup"><span data-stu-id="22386-135">This makes setting the state this way a lower-level operation than calling `Add`, `Attach`, or `Update`, which operate on an entire graph of entities.</span></span>

<span data-ttu-id="22386-136">Le tableau suivant récapitule les façons d’utiliser un EntityEntry pour travailler avec une entité entière :</span><span class="sxs-lookup"><span data-stu-id="22386-136">The following table summarizes ways to use an EntityEntry to work with an entire entity:</span></span>

| <span data-ttu-id="22386-137">Membre EntityEntry</span><span class="sxs-lookup"><span data-stu-id="22386-137">EntityEntry member</span></span>                                                                                         | <span data-ttu-id="22386-138">Description</span><span class="sxs-lookup"><span data-stu-id="22386-138">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | <span data-ttu-id="22386-139">Obtient et définit le <xref:Microsoft.EntityFrameworkCore.EntityState> de l’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-139">Gets and sets the <xref:Microsoft.EntityFrameworkCore.EntityState> of the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | <span data-ttu-id="22386-140">Obtient l’instance d’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-140">Gets the entity instance.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | <span data-ttu-id="22386-141"><xref:Microsoft.EntityFrameworkCore.DbContext>Qui effectue le suivi de cette entité.</span><span class="sxs-lookup"><span data-stu-id="22386-141">The <xref:Microsoft.EntityFrameworkCore.DbContext> that is tracking this entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <span data-ttu-id="22386-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> métadonnées pour le type d’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata for the type of entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | <span data-ttu-id="22386-143">Indique si la valeur de clé de l’entité a été définie.</span><span class="sxs-lookup"><span data-stu-id="22386-143">Whether or not the entity has had its key value set.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | <span data-ttu-id="22386-144">Remplace les valeurs de propriété par des valeurs lues à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="22386-144">Overwrites property values with values read from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | <span data-ttu-id="22386-145">Force la détection des modifications pour cette entité uniquement ; consultez [modification de la détection et des notifications](xref:core/change-tracking/change-detection).</span><span class="sxs-lookup"><span data-stu-id="22386-145">Forces detection of changes for this entity only; see [Change Detection and Notifications](xref:core/change-tracking/change-detection).</span></span>

### <a name="working-with-a-single-property"></a><span data-ttu-id="22386-146">Utilisation d’une propriété unique</span><span class="sxs-lookup"><span data-stu-id="22386-146">Working with a single property</span></span>

<span data-ttu-id="22386-147">Plusieurs surcharges de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> permettent d’accéder aux informations relatives à une propriété individuelle d’une entité.</span><span class="sxs-lookup"><span data-stu-id="22386-147">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> allow access to information about an individual property of an entity.</span></span> <span data-ttu-id="22386-148">Par exemple, à l’aide d’une API fortement typée et de type Fluent :</span><span class="sxs-lookup"><span data-stu-id="22386-148">For example, using a strongly-typed, fluent-like API:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

<span data-ttu-id="22386-149">Le nom de la propriété peut être passé en tant que chaîne.</span><span class="sxs-lookup"><span data-stu-id="22386-149">The property name can instead be passed as a string.</span></span> <span data-ttu-id="22386-150">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-150">For example:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

<span data-ttu-id="22386-151">Le retourné <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> peut ensuite être utilisé pour accéder aux informations sur la propriété.</span><span class="sxs-lookup"><span data-stu-id="22386-151">The returned <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> can then be used to access information about the property.</span></span> <span data-ttu-id="22386-152">Par exemple, il peut être utilisé pour obtenir et définir la valeur actuelle de la propriété sur cette entité :</span><span class="sxs-lookup"><span data-stu-id="22386-152">For example, it can be used to get and set the current value of the property on this entity:</span></span>

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

<span data-ttu-id="22386-153">Les deux méthodes de propriété utilisées ci-dessus retournent une instance générique fortement typée <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> .</span><span class="sxs-lookup"><span data-stu-id="22386-153">Both of the Property methods used above return a strongly-typed generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> instance.</span></span> <span data-ttu-id="22386-154">L’utilisation de ce type générique est préférable, car elle permet d’accéder aux valeurs de propriété sans [types valeur de boxing](/dotnet/csharp/programming-guide/types/boxing-and-unboxing).</span><span class="sxs-lookup"><span data-stu-id="22386-154">Using this generic type is preferred because it allows access to property values without [boxing value types](/dotnet/csharp/programming-guide/types/boxing-and-unboxing).</span></span> <span data-ttu-id="22386-155">Toutefois, si le type de l’entité ou de la propriété n’est pas connu au moment de la compilation, un non générique <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> peut être obtenu à la place :</span><span class="sxs-lookup"><span data-stu-id="22386-155">However, if the type of entity or property is not known at compile-time, then a non-generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> can be obtained instead:</span></span>

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

<span data-ttu-id="22386-156">Cela permet d’accéder aux informations de propriété pour toute propriété, quel que soit son type, aux dépens des types valeur Boxing.</span><span class="sxs-lookup"><span data-stu-id="22386-156">This allows access to property information for any property regardless of its type, at the expense of boxing value types.</span></span> <span data-ttu-id="22386-157">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-157">For example:</span></span>

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

<span data-ttu-id="22386-158">Le tableau suivant récapitule les informations sur les propriétés exposées par PropertyEntry :</span><span class="sxs-lookup"><span data-stu-id="22386-158">The following table summarizes property information exposed by PropertyEntry:</span></span>

| <span data-ttu-id="22386-159">Membre PropertyEntry</span><span class="sxs-lookup"><span data-stu-id="22386-159">PropertyEntry member</span></span>                               | <span data-ttu-id="22386-160">Description</span><span class="sxs-lookup"><span data-stu-id="22386-160">Description</span></span>
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | <span data-ttu-id="22386-161">Obtient et définit la valeur actuelle de la propriété.</span><span class="sxs-lookup"><span data-stu-id="22386-161">Gets and sets the current value of the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | <span data-ttu-id="22386-162">Obtient et définit la valeur d’origine de la propriété, si elle est disponible.</span><span class="sxs-lookup"><span data-stu-id="22386-162">Gets and sets the original value of the property, if available.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | <span data-ttu-id="22386-163">Référence arrière à l' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> de l’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-163">A back reference to the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> for the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <span data-ttu-id="22386-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> métadonnées de la propriété.</span><span class="sxs-lookup"><span data-stu-id="22386-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> metadata for the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | <span data-ttu-id="22386-165">Indique si cette propriété est marquée comme modifiée et autorise la modification de cet État.</span><span class="sxs-lookup"><span data-stu-id="22386-165">Indicates whether this property is marked as modified, and allows this state to be changed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | <span data-ttu-id="22386-166">Indique si cette propriété est marquée comme [temporaire](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)et autorise la modification de cet État.</span><span class="sxs-lookup"><span data-stu-id="22386-166">Indicates whether this property is marked as [temporary](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values), and allows this state to be changed.</span></span>

<span data-ttu-id="22386-167">Remarques :</span><span class="sxs-lookup"><span data-stu-id="22386-167">Notes:</span></span>

- <span data-ttu-id="22386-168">La valeur d’origine d’une propriété est la valeur de la propriété lors de l’interrogation de l’entité à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="22386-168">The original value of a property is the value that the property had when the entity was queried from the database.</span></span> <span data-ttu-id="22386-169">Toutefois, les valeurs d’origine ne sont pas disponibles si l’entité a été déconnectée, puis attachée explicitement à un autre DbContext, par exemple avec `Attach` ou `Update` .</span><span class="sxs-lookup"><span data-stu-id="22386-169">However, original values are not available if the entity was disconnected and then explicitly attached to another DbContext, for example with `Attach` or `Update`.</span></span> <span data-ttu-id="22386-170">Dans ce cas, la valeur d’origine retournée est identique à la valeur actuelle.</span><span class="sxs-lookup"><span data-stu-id="22386-170">In this case, the original value returned will be the same as the current value.</span></span>
- <span data-ttu-id="22386-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> met uniquement à jour les propriétés marquées comme modifiées.</span><span class="sxs-lookup"><span data-stu-id="22386-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> will only update properties marked as modified.</span></span> <span data-ttu-id="22386-172">Affectez <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> la valeur true pour forcer EF Core à mettre à jour une valeur de propriété donnée, ou affectez-lui la valeur false pour empêcher EF Core de mettre à jour la valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="22386-172">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> to true to force EF Core to update a given property value, or set it to false to prevent EF Core from updating the property value.</span></span>
- <span data-ttu-id="22386-173">Les [valeurs temporaires](xref:core/change-tracking/miscellaneous) sont généralement générées par les [générateurs de valeurs](xref:core/modeling/generated-properties)EF Core.</span><span class="sxs-lookup"><span data-stu-id="22386-173">[Temporary values](xref:core/change-tracking/miscellaneous) are typically generated by EF Core [value generators](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="22386-174">La définition de la valeur actuelle d’une propriété remplace la valeur temporaire par la valeur donnée et marque la propriété comme non temporaire.</span><span class="sxs-lookup"><span data-stu-id="22386-174">Setting the current value of a property will replace the temporary value with the given value and mark the property as not temporary.</span></span> <span data-ttu-id="22386-175">Affectez <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> la valeur true pour forcer une valeur temporaire, même après qu’elle a été définie explicitement.</span><span class="sxs-lookup"><span data-stu-id="22386-175">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> to true to force a value to be temporary even after it has been explicitly set.</span></span>

### <a name="working-with-a-single-navigation"></a><span data-ttu-id="22386-176">Utilisation d’une seule navigation</span><span class="sxs-lookup"><span data-stu-id="22386-176">Working with a single navigation</span></span>

<span data-ttu-id="22386-177">Plusieurs surcharges de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> autorisent l’accès aux informations relatives à une navigation individuelle.</span><span class="sxs-lookup"><span data-stu-id="22386-177">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> allow access to information about an individual navigation.</span></span>

<span data-ttu-id="22386-178">Les navigations de référence vers une entité associée unique sont accessibles par le biais des <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> méthodes.</span><span class="sxs-lookup"><span data-stu-id="22386-178">Reference navigations to a single related entity are accessed through the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> methods.</span></span> <span data-ttu-id="22386-179">Les navigations de référence pointent vers les côtés « un » d’une relation un-à-plusieurs, et les deux côtés d’une relation un-à-un.</span><span class="sxs-lookup"><span data-stu-id="22386-179">Reference navigations point to the "one" sides of one-to-many relationships, and both sides of one-to-one relationships.</span></span> <span data-ttu-id="22386-180">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-180">For example:</span></span>

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

<span data-ttu-id="22386-181">Les navigations peuvent également être des collections d’entités associées lorsqu’elles sont utilisées pour les côtés « plusieurs » des relations un-à-plusieurs et plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="22386-181">Navigations can also be collections of related entities when used for the "many" sides of one-to-many and many-to-many relationships.</span></span> <span data-ttu-id="22386-182">Les <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> méthodes sont utilisées pour accéder aux navigations de collection.</span><span class="sxs-lookup"><span data-stu-id="22386-182">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> methods are used to access collection navigations.</span></span> <span data-ttu-id="22386-183">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-183">For example:</span></span>

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

<span data-ttu-id="22386-184">Certaines opérations sont courantes pour toutes les navigations.</span><span class="sxs-lookup"><span data-stu-id="22386-184">Some operations are common for all navigations.</span></span> <span data-ttu-id="22386-185">Ils sont accessibles à la fois pour les navigations de référence et de collection à l’aide de la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> méthode.</span><span class="sxs-lookup"><span data-stu-id="22386-185">These can be accessed for both reference and collection navigations using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> method.</span></span> <span data-ttu-id="22386-186">Notez que seul un accès non générique est disponible lorsque vous accédez à toutes les navigations ensemble.</span><span class="sxs-lookup"><span data-stu-id="22386-186">Note that only non-generic access is available when accessing all navigations together.</span></span> <span data-ttu-id="22386-187">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-187">For example:</span></span>

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

<span data-ttu-id="22386-188">Le tableau suivant récapitule les différentes façons d’utiliser <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> :</span><span class="sxs-lookup"><span data-stu-id="22386-188">The following table summarizes ways to use <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry>:</span></span>

| <span data-ttu-id="22386-189">Membre NavigationEntry</span><span class="sxs-lookup"><span data-stu-id="22386-189">NavigationEntry member</span></span>                                                                                    | <span data-ttu-id="22386-190">Description</span><span class="sxs-lookup"><span data-stu-id="22386-190">Description</span></span>
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | <span data-ttu-id="22386-191">Obtient et définit la valeur actuelle de la navigation.</span><span class="sxs-lookup"><span data-stu-id="22386-191">Gets and sets the current value of the navigation.</span></span> <span data-ttu-id="22386-192">Il s’agit de la collection entière pour les navigations de collection.</span><span class="sxs-lookup"><span data-stu-id="22386-192">This is the entire collection for collection navigations.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <span data-ttu-id="22386-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> métadonnées pour la navigation.</span><span class="sxs-lookup"><span data-stu-id="22386-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> metadata for the navigation.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | <span data-ttu-id="22386-194">Obtient ou définit une valeur indiquant si l’entité ou la collection associée a été entièrement chargée à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="22386-194">Gets or sets a value indicating whether the related entity or collection has been fully loaded from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | <span data-ttu-id="22386-195">Charge l’entité ou la collection associée à partir de la base de données ; consultez [chargement explicite de données associées](xref:core/querying/related-data/explicit).</span><span class="sxs-lookup"><span data-stu-id="22386-195">Loads the related entity or collection from the database; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | <span data-ttu-id="22386-196">La EF Core de requête utilise pour charger cette navigation comme un `IQueryable` qui peut être composé davantage ; consultez [chargement explicite de données associées](xref:core/querying/related-data/explicit).</span><span class="sxs-lookup"><span data-stu-id="22386-196">The query EF Core would use to load this navigation as an `IQueryable` that can be further composed; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>

### <a name="working-with-all-properties-of-an-entity"></a><span data-ttu-id="22386-197">Utilisation de toutes les propriétés d’une entité</span><span class="sxs-lookup"><span data-stu-id="22386-197">Working with all properties of an entity</span></span>

<span data-ttu-id="22386-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> retourne un <xref:System.Collections.Generic.IEnumerable%601> de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> pour chaque propriété de l’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> for every property of the entity.</span></span> <span data-ttu-id="22386-199">Cela peut être utilisé pour exécuter une action pour chaque propriété de l’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-199">This can be used to perform an action for every property of the entity.</span></span> <span data-ttu-id="22386-200">Par exemple, pour définir une propriété DateTime sur `DateTime.Now` :</span><span class="sxs-lookup"><span data-stu-id="22386-200">For example, to set any DateTime property to `DateTime.Now`:</span></span>

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

<span data-ttu-id="22386-201">En outre, EntityEntry contient plusieurs méthodes pour récupérer et définir toutes les valeurs de propriété en même temps.</span><span class="sxs-lookup"><span data-stu-id="22386-201">In addition, EntityEntry contains several methods to get and set all property values at the same time.</span></span> <span data-ttu-id="22386-202">Ces méthodes utilisent la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> classe, qui représente une collection de propriétés et leurs valeurs.</span><span class="sxs-lookup"><span data-stu-id="22386-202">These methods use the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> class, which represents a collection of properties and their values.</span></span> <span data-ttu-id="22386-203">PropertyValues peut être obtenu pour les valeurs actuelles ou d’origine, ou pour les valeurs qui sont actuellement stockées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="22386-203">PropertyValues can be obtained for current or original values, or for the values as currently stored in the database.</span></span> <span data-ttu-id="22386-204">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-204">For example:</span></span>

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

<span data-ttu-id="22386-205">Ces objets PropertyValues ne sont pas très utiles.</span><span class="sxs-lookup"><span data-stu-id="22386-205">These PropertyValues objects are not very useful on their own.</span></span> <span data-ttu-id="22386-206">Toutefois, elles peuvent être combinées pour effectuer des opérations courantes nécessaires à la manipulation d’entités.</span><span class="sxs-lookup"><span data-stu-id="22386-206">However, they can be combined to perform common operations needed when manipulating entities.</span></span> <span data-ttu-id="22386-207">Cela est utile lors de l’utilisation d’objets de transfert de données et lors de la résolution de [conflits d’accès concurrentiel optimiste](xref:core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="22386-207">This is useful when working with data transfer objects and when resolving [optimistic concurrency conflicts](xref:core/saving/concurrency).</span></span> <span data-ttu-id="22386-208">Les sections suivantes présentent quelques exemples.</span><span class="sxs-lookup"><span data-stu-id="22386-208">The following sections show some examples.</span></span>

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a><span data-ttu-id="22386-209">Définition des valeurs actuelles ou d’origine à partir d’une entité ou d’un DTO</span><span class="sxs-lookup"><span data-stu-id="22386-209">Setting current or original values from an entity or DTO</span></span>

<span data-ttu-id="22386-210">Les valeurs actuelles ou d’origine d’une entité peuvent être mises à jour en copiant des valeurs à partir d’un autre objet.</span><span class="sxs-lookup"><span data-stu-id="22386-210">The current or original values of an entity can be updated by copying values from another object.</span></span> <span data-ttu-id="22386-211">Par exemple, considérez un `BlogDto` objet de transfert de données (DTO) avec les mêmes propriétés que le type d’entité :</span><span class="sxs-lookup"><span data-stu-id="22386-211">For example, consider a `BlogDto` data transfer object (DTO) with the same properties as the entity type:</span></span>

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

<span data-ttu-id="22386-212">Cela peut être utilisé pour définir les valeurs actuelles d’une entité suivie à l’aide de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="22386-212">This can be used to set the current values of a tracked entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

<span data-ttu-id="22386-213">Cette technique est parfois utilisée lors de la mise à jour d’une entité avec des valeurs obtenues à partir d’un appel de service ou d’un client dans une application multiniveau.</span><span class="sxs-lookup"><span data-stu-id="22386-213">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="22386-214">Notez que l’objet utilisé n’a pas besoin d’être du même type que l’entité, à condition qu’il possède des propriétés dont les noms correspondent à ceux de l’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-214">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="22386-215">Dans l’exemple ci-dessus, une instance du DTO `BlogDto` est utilisée pour définir les valeurs actuelles d’une `Blog` entité suivie.</span><span class="sxs-lookup"><span data-stu-id="22386-215">In the example above, an instance of the DTO `BlogDto` is used to set the current values of a tracked `Blog` entity.</span></span>

<span data-ttu-id="22386-216">Notez que les propriétés ne sont marquées comme modifiées que si la valeur définie est différente de la valeur actuelle.</span><span class="sxs-lookup"><span data-stu-id="22386-216">Note that properties will only be marked as modified if the value set differs from the current value.</span></span>

#### <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="22386-217">Définition des valeurs actuelles ou d’origine à partir d’un dictionnaire</span><span class="sxs-lookup"><span data-stu-id="22386-217">Setting current or original values from a dictionary</span></span>

<span data-ttu-id="22386-218">L’exemple précédent définit des valeurs à partir d’une instance d’entité ou de DTO.</span><span class="sxs-lookup"><span data-stu-id="22386-218">The previous example set values from an entity or DTO instance.</span></span> <span data-ttu-id="22386-219">Le même comportement est disponible lorsque les valeurs de propriété sont stockées sous forme de paires nom/valeur dans un dictionnaire.</span><span class="sxs-lookup"><span data-stu-id="22386-219">The same behavior is available when property values are stored as name/value pairs in a dictionary.</span></span> <span data-ttu-id="22386-220">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-220">For example:</span></span>

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a><span data-ttu-id="22386-221">Définition des valeurs actuelles ou d’origine de la base de données</span><span class="sxs-lookup"><span data-stu-id="22386-221">Setting current or original values from the database</span></span>

<span data-ttu-id="22386-222">Les valeurs actuelles ou d’origine d’une entité peuvent être mises à jour avec les valeurs les plus récentes de la base de données en appelant <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> ou <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> et en utilisant l’objet retourné pour définir les valeurs actuelles ou d’origine, ou les deux.</span><span class="sxs-lookup"><span data-stu-id="22386-222">The current or original values of an entity can be updated with the latest values from the database by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> and using the returned object to set current or original values, or both.</span></span> <span data-ttu-id="22386-223">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-223">For example:</span></span>

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="22386-224">Création d’un objet cloné contenant les valeurs actuelles, d’origine ou de base de données</span><span class="sxs-lookup"><span data-stu-id="22386-224">Creating a cloned object containing current, original, or database values</span></span>

<span data-ttu-id="22386-225">L’objet PropertyValues retourné par CurrentValues, OriginalValues ou GetDatabaseValues peut être utilisé pour créer un clone de l’entité à l’aide de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="22386-225">The PropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType>.</span></span> <span data-ttu-id="22386-226">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-226">For example:</span></span>

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

<span data-ttu-id="22386-227">Notez que `ToObject` retourne une nouvelle instance qui n’est pas suivie par DbContext.</span><span class="sxs-lookup"><span data-stu-id="22386-227">Note that `ToObject` returns a new instance that is not tracked by the DbContext.</span></span> <span data-ttu-id="22386-228">L’objet retourné n’a pas non plus de relations définies pour d’autres entités.</span><span class="sxs-lookup"><span data-stu-id="22386-228">The returned object also does not have any relationships set to other entities.</span></span>

<span data-ttu-id="22386-229">L’objet cloné peut être utile pour résoudre les problèmes liés aux mises à jour simultanées de la base de données, en particulier lors de la liaison de données à des objets d’un certain type.</span><span class="sxs-lookup"><span data-stu-id="22386-229">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially when data binding to objects of a certain type.</span></span> <span data-ttu-id="22386-230">Pour plus d’informations, consultez [accès concurrentiel optimiste](xref:core/saving/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="22386-230">See [optimistic concurrency](xref:core/saving/concurrency) for more information.</span></span>

### <a name="working-with-all-navigations-of-an-entity"></a><span data-ttu-id="22386-231">Utilisation de toutes les navigations d’une entité</span><span class="sxs-lookup"><span data-stu-id="22386-231">Working with all navigations of an entity</span></span>

<span data-ttu-id="22386-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> retourne un <xref:System.Collections.Generic.IEnumerable%601> de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> pour chaque navigation de l’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> for every navigation of the entity.</span></span> <span data-ttu-id="22386-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> font la même chose, mais limité aux navigations de référence ou de collection, respectivement.</span><span class="sxs-lookup"><span data-stu-id="22386-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> do the same thing, but restricted to reference or collection navigations respectively.</span></span> <span data-ttu-id="22386-234">Cela peut être utilisé pour effectuer une action pour chaque navigation de l’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-234">This can be used to perform an action for every navigation of the entity.</span></span> <span data-ttu-id="22386-235">Par exemple, pour forcer le chargement de toutes les entités associées :</span><span class="sxs-lookup"><span data-stu-id="22386-235">For example, to force loading of all related entities:</span></span>

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a><span data-ttu-id="22386-236">Utilisation de tous les membres d’une entité</span><span class="sxs-lookup"><span data-stu-id="22386-236">Working with all members of an entity</span></span>

<span data-ttu-id="22386-237">Les propriétés normales et les propriétés de navigation ont un État et un comportement différents.</span><span class="sxs-lookup"><span data-stu-id="22386-237">Regular properties and navigation properties have different state and behavior.</span></span> <span data-ttu-id="22386-238">Il est donc courant de traiter les navigations et les non-navigations séparément, comme indiqué dans les sections ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="22386-238">It is therefore common to process navigations and non-navigations separately, as shown in the sections above.</span></span> <span data-ttu-id="22386-239">Toutefois, il peut parfois être utile d’effectuer une opération avec n’importe quel membre de l’entité, qu’il s’agisse d’une propriété normale ou d’une navigation.</span><span class="sxs-lookup"><span data-stu-id="22386-239">However, sometimes it can be useful to do something with any member of the entity, regardless of whether it is a regular property or navigation.</span></span> <span data-ttu-id="22386-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> sont fournis à cet effet.</span><span class="sxs-lookup"><span data-stu-id="22386-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> are provided for this purpose.</span></span> <span data-ttu-id="22386-241">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-241">For example:</span></span>

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

<span data-ttu-id="22386-242">L’exécution de ce code sur un blog à partir de l’exemple génère la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="22386-242">Running this code on a blog from the sample generates the following output:</span></span>

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> <span data-ttu-id="22386-243">La [vue de débogage du dispositif de suivi des modifications](xref:core/change-tracking/debug-views) affiche des informations de ce type.</span><span class="sxs-lookup"><span data-stu-id="22386-243">The [change tracker debug view](xref:core/change-tracking/debug-views) shows information like this.</span></span> <span data-ttu-id="22386-244">La vue de débogage de l’ensemble du dispositif de suivi des modifications est générée à partir de la personne <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> de chaque entité suivie.</span><span class="sxs-lookup"><span data-stu-id="22386-244">The debug view for the entire change tracker is generated from the individual <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> of each tracked entity.</span></span>

## <a name="find-and-findasync"></a><span data-ttu-id="22386-245">Rechercher et FindAsync</span><span class="sxs-lookup"><span data-stu-id="22386-245">Find and FindAsync</span></span>

<span data-ttu-id="22386-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> et <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> sont conçus pour une recherche efficace d’une entité unique lorsque sa clé primaire est connue.</span><span class="sxs-lookup"><span data-stu-id="22386-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> are designed for efficient lookup of a single entity when its primary key is known.</span></span> <span data-ttu-id="22386-247">Rechercher commence par vérifier si l’entité est déjà suivie et, si tel est le cas, retourne l’entité immédiatement.</span><span class="sxs-lookup"><span data-stu-id="22386-247">Find first checks if the entity is already tracked, and if so returns the entity immediately.</span></span> <span data-ttu-id="22386-248">Une requête de base de données est effectuée uniquement si l’entité n’est pas suivie localement.</span><span class="sxs-lookup"><span data-stu-id="22386-248">A database query is only made if the entity is not tracked locally.</span></span> <span data-ttu-id="22386-249">Par exemple, considérez ce code qui appelle Find deux fois pour la même entité :</span><span class="sxs-lookup"><span data-stu-id="22386-249">For example, consider this code that calls Find twice for the same entity:</span></span>

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

<span data-ttu-id="22386-250">La sortie de ce code (y compris EF Core Logging) lors de l’utilisation de SQLite est la suivante :</span><span class="sxs-lookup"><span data-stu-id="22386-250">The output from this code (including EF Core logging) when using SQLite is:</span></span>

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

<span data-ttu-id="22386-251">Notez que le premier appel ne trouve pas l’entité localement et exécute donc une requête de base de données.</span><span class="sxs-lookup"><span data-stu-id="22386-251">Notice that the first call does not find the entity locally and so executes a database query.</span></span> <span data-ttu-id="22386-252">À l’inverse, le deuxième appel retourne la même instance sans interroger la base de données, car elle fait déjà l’objet d’un suivi.</span><span class="sxs-lookup"><span data-stu-id="22386-252">Conversely, the second call returns the same instance without querying the database because it is already being tracked.</span></span>

<span data-ttu-id="22386-253">Find retourne la valeur null si une entité avec la clé donnée n’est pas suivie localement et n’existe pas dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="22386-253">Find returns null if an entity with the given key is not tracked locally and does not exist in the database.</span></span>

### <a name="composite-keys"></a><span data-ttu-id="22386-254">Clés composites</span><span class="sxs-lookup"><span data-stu-id="22386-254">Composite keys</span></span>

<span data-ttu-id="22386-255">La fonction find peut également être utilisée avec des clés composites.</span><span class="sxs-lookup"><span data-stu-id="22386-255">Find can also be used with composite keys.</span></span> <span data-ttu-id="22386-256">Par exemple, considérez une `OrderLine` entité avec une clé composite composée de l’ID de commande et de l’ID de produit :</span><span class="sxs-lookup"><span data-stu-id="22386-256">For example, consider an `OrderLine` entity with a composite key consisting of the order ID and the product ID:</span></span>

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

<span data-ttu-id="22386-257">La clé composite doit être configurée dans <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> pour définir les éléments clés _et leur ordre_.</span><span class="sxs-lookup"><span data-stu-id="22386-257">The composite key must be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> to define the key parts _and their order_.</span></span> <span data-ttu-id="22386-258">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-258">For example:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="22386-259">Notez que `OrderId` est la première partie de la clé et `ProductId` est la deuxième partie de la clé.</span><span class="sxs-lookup"><span data-stu-id="22386-259">Notice that `OrderId` is the first part of the key and `ProductId` is the second part of the key.</span></span> <span data-ttu-id="22386-260">Cet ordre doit être utilisé lors du passage de valeurs de clés à rechercher.</span><span class="sxs-lookup"><span data-stu-id="22386-260">This order must be used when passing key values to Find.</span></span> <span data-ttu-id="22386-261">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-261">For example:</span></span>

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a><span data-ttu-id="22386-262">Utilisation de ChangeTracker. Entries pour accéder à toutes les entités suivies</span><span class="sxs-lookup"><span data-stu-id="22386-262">Using ChangeTracker.Entries to access all tracked entities</span></span>

<span data-ttu-id="22386-263">Jusqu’à présent, nous n’avons accédé qu’à un seul <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> à la fois.</span><span class="sxs-lookup"><span data-stu-id="22386-263">So far we have accessed only a single <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> at a time.</span></span> <span data-ttu-id="22386-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> retourne un EntityEntry pour chaque entité actuellement suivie par DbContext.</span><span class="sxs-lookup"><span data-stu-id="22386-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> returns an EntityEntry for every entity currently tracked by the DbContext.</span></span> <span data-ttu-id="22386-265">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-265">For example:</span></span>

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

<span data-ttu-id="22386-266">Ce code génère la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="22386-266">This code generates the following output:</span></span>

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="22386-267">Notez que les entrées pour les blogs et les publications sont retournées.</span><span class="sxs-lookup"><span data-stu-id="22386-267">Notice that entries for both blogs and posts are returned.</span></span> <span data-ttu-id="22386-268">Les résultats peuvent plutôt être filtrés sur un type d’entité spécifique à l’aide de la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> surcharge générique :</span><span class="sxs-lookup"><span data-stu-id="22386-268">The results can instead be filtered to a specific entity type using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> generic overload:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

<span data-ttu-id="22386-269">La sortie de ce code indique que seules les publications sont retournées :</span><span class="sxs-lookup"><span data-stu-id="22386-269">The output from this code shows that only posts are returned:</span></span>

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="22386-270">En outre, l’utilisation de la surcharge générique retourne des <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances génériques.</span><span class="sxs-lookup"><span data-stu-id="22386-270">Also, using the generic overload returns generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances.</span></span> <span data-ttu-id="22386-271">C’est ce qui permet un accès de type Fluent à la `Id` propriété dans cet exemple.</span><span class="sxs-lookup"><span data-stu-id="22386-271">This is what allows that fluent-like access to the `Id` property in this example.</span></span>

<span data-ttu-id="22386-272">Le type générique utilisé pour le filtrage ne doit pas nécessairement être un type d’entité mappé ; un type de base ou une interface non mappé peut être utilisé à la place.</span><span class="sxs-lookup"><span data-stu-id="22386-272">The generic type used for filtering does not have to be a mapped entity type; an unmapped base type or interface can be used instead.</span></span> <span data-ttu-id="22386-273">Par exemple, si tous les types d’entités du modèle implémentent une interface définissant leur propriété de clé :</span><span class="sxs-lookup"><span data-stu-id="22386-273">For example, if all the entity types in the model implement an interface defining their key property:</span></span>

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

<span data-ttu-id="22386-274">Cette interface peut être utilisée pour travailler avec la clé d’une entité suivie d’une manière fortement typée.</span><span class="sxs-lookup"><span data-stu-id="22386-274">Then this interface can be used to work with the key of any tracked entity in a strongly-typed manner.</span></span> <span data-ttu-id="22386-275">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-275">For example:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a><span data-ttu-id="22386-276">Utilisation de DbSet. local pour interroger les entités suivies</span><span class="sxs-lookup"><span data-stu-id="22386-276">Using DbSet.Local to query tracked entities</span></span>

<span data-ttu-id="22386-277">Les requêtes EF Core sont toujours exécutées sur la base de données et retournent uniquement les entités qui ont été enregistrées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="22386-277">EF Core queries are always executed on the database, and only return entities that have been saved to the database.</span></span> <span data-ttu-id="22386-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> fournit un mécanisme pour interroger le DbContext pour les entités locales et suivies.</span><span class="sxs-lookup"><span data-stu-id="22386-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> provides a mechanism to query the DbContext for local, tracked entities.</span></span>

<span data-ttu-id="22386-279">Étant donné que `DbSet.Local` est utilisé pour interroger des entités suivies, il est courant de charger des entités dans la DbContext, puis de travailler avec ces entités chargées.</span><span class="sxs-lookup"><span data-stu-id="22386-279">Since `DbSet.Local` is used to query tracked entities, it is typical to load entities into the DbContext and then work with those loaded entities.</span></span> <span data-ttu-id="22386-280">Cela est particulièrement vrai pour la liaison de données, mais peut également être utile dans d’autres situations.</span><span class="sxs-lookup"><span data-stu-id="22386-280">This is especially true for data binding, but can also be useful in other situations.</span></span> <span data-ttu-id="22386-281">Par exemple, dans le code suivant, la base de données est d’abord interrogée pour tous les blogs et toutes les publications.</span><span class="sxs-lookup"><span data-stu-id="22386-281">For example, in the following code the database is first queried for all blogs and posts.</span></span> <span data-ttu-id="22386-282">La <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> méthode d’extension est utilisée pour exécuter cette requête avec les résultats suivis par le contexte sans être retourné directement à l’application.</span><span class="sxs-lookup"><span data-stu-id="22386-282">The <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> extension method is used to execute this query with the results tracked by the context without being returned directly to the application.</span></span> <span data-ttu-id="22386-283">(L’utilisation `ToList` de ou similaire a le même effet, mais avec la surcharge liée à la création de la liste retournée, ce qui n’est pas nécessaire ici). L’exemple utilise ensuite `DbSet.Local` pour accéder aux entités suivies localement :</span><span class="sxs-lookup"><span data-stu-id="22386-283">(Using `ToList` or similar has the same effect but with the overhead of creating the returned list, which is not needed here.) The example then uses `DbSet.Local` to access the locally tracked entities:</span></span>

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

<span data-ttu-id="22386-284">Notez que, contrairement à <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> , `DbSet.Local` retourne directement les instances d’entité.</span><span class="sxs-lookup"><span data-stu-id="22386-284">Notice that, unlike <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType>, `DbSet.Local` returns entity instances directly.</span></span> <span data-ttu-id="22386-285">Un EntityEntry peut, bien sûr, être obtenu pour l’entité retournée en appelant <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="22386-285">An EntityEntry can, of course, always be obtained for the returned entity by calling <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>.</span></span>

### <a name="the-local-view"></a><span data-ttu-id="22386-286">Vue locale</span><span class="sxs-lookup"><span data-stu-id="22386-286">The local view</span></span>

<span data-ttu-id="22386-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> retourne une vue des entités suivies localement qui reflète le actuel <xref:Microsoft.EntityFrameworkCore.EntityState> de ces entités.</span><span class="sxs-lookup"><span data-stu-id="22386-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns a view of locally tracked entities that reflects the current <xref:Microsoft.EntityFrameworkCore.EntityState> of those entities.</span></span> <span data-ttu-id="22386-288">Plus précisément, cela signifie que :</span><span class="sxs-lookup"><span data-stu-id="22386-288">Specifically, this means that:</span></span>

- <span data-ttu-id="22386-289">`Added` les entités sont incluses.</span><span class="sxs-lookup"><span data-stu-id="22386-289">`Added` entities are included.</span></span> <span data-ttu-id="22386-290">Notez que ce n’est pas le cas pour les requêtes de EF Core normales, car `Added` les entités n’existent pas encore dans la base de données et ne sont donc jamais retournées par une requête de base de données.</span><span class="sxs-lookup"><span data-stu-id="22386-290">Note that this is not the case for normal EF Core queries, since `Added` entities do not yet exist in the database and so are therefore never returned by a database query.</span></span>
- <span data-ttu-id="22386-291">`Deleted` les entités sont exclues.</span><span class="sxs-lookup"><span data-stu-id="22386-291">`Deleted` entities are excluded.</span></span> <span data-ttu-id="22386-292">Notez que ce n’est pas le cas pour les requêtes de EF Core normales, car `Deleted` les entités existent toujours dans la base de données et _sont_ retournées par les requêtes de base de données.</span><span class="sxs-lookup"><span data-stu-id="22386-292">Note that this is again not the case for normal EF Core queries, since `Deleted` entities still exist in the database and so _are_ returned by database queries.</span></span>

<span data-ttu-id="22386-293">Tout cela signifie que `DbSet.Local` est une vue des données qui reflète l’État conceptuel actuel du graphique d’entité, avec les `Added` entités incluses et les `Deleted` entités exclues.</span><span class="sxs-lookup"><span data-stu-id="22386-293">All of this means that `DbSet.Local` is view over the data that reflects the current conceptual state of the entity graph, with `Added` entities included and `Deleted` entities excluded.</span></span> <span data-ttu-id="22386-294">Cela correspond à l’État attendu de la base de données après l’appel de SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="22386-294">This matches what database state is expected to be after SaveChanges is called.</span></span>

<span data-ttu-id="22386-295">Il s’agit généralement de la vue idéale pour la liaison de données, car elle présente à l’utilisateur les données à mesure qu’elles le comprennent en fonction des modifications apportées par l’application.</span><span class="sxs-lookup"><span data-stu-id="22386-295">This is typically the ideal view for data binding, since it presents to the user the data as they understand it based on the changes made by the application.</span></span>

<span data-ttu-id="22386-296">L’exemple de code suivant montre comment marquer une publication comme, `Deleted` puis ajouter une nouvelle publication, en la marquant comme `Added` suit :</span><span class="sxs-lookup"><span data-stu-id="22386-296">The following code demonstrates this my marking one post as `Deleted` and then adding a new post, marking it as `Added`:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

<span data-ttu-id="22386-297">Le résultat de ce code est le suivant :</span><span class="sxs-lookup"><span data-stu-id="22386-297">The output from this code is:</span></span>

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

<span data-ttu-id="22386-298">Notez que la publication supprimée est supprimée de la vue locale et que la publication ajoutée est incluse.</span><span class="sxs-lookup"><span data-stu-id="22386-298">Notice that the deleted post is removed from the local view, and the added post is included.</span></span>

### <a name="using-local-to-add-and-remove-entities"></a><span data-ttu-id="22386-299">Utilisation de local pour ajouter et supprimer des entités</span><span class="sxs-lookup"><span data-stu-id="22386-299">Using Local to add and remove entities</span></span>

<span data-ttu-id="22386-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> retourne une instance de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>.</span><span class="sxs-lookup"><span data-stu-id="22386-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns an instance of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>.</span></span> <span data-ttu-id="22386-301">Il s’agit d’une implémentation de <xref:System.Collections.Generic.ICollection%601> qui génère et répond aux notifications lorsque des entités sont ajoutées et supprimées de la collection.</span><span class="sxs-lookup"><span data-stu-id="22386-301">This is an implementation of <xref:System.Collections.Generic.ICollection%601> that generates and responds to notifications when entities are added and removed from the collection.</span></span> <span data-ttu-id="22386-302">(Il s’agit du même concept que <xref:System.Collections.ObjectModel.ObservableCollection%601> , mais implémenté comme une projection sur des entrées de suivi des modifications EF Core existantes, plutôt que comme une collection indépendante.)</span><span class="sxs-lookup"><span data-stu-id="22386-302">(This is the same concept as <xref:System.Collections.ObjectModel.ObservableCollection%601>, but implemented as a projection over existing EF Core change tracking entries, rather than as an independent collection.)</span></span>

<span data-ttu-id="22386-303">Les notifications de la vue locale sont raccordées au suivi des modifications DbContext, de telle sorte que la vue locale reste synchronisée avec DbContext.</span><span class="sxs-lookup"><span data-stu-id="22386-303">The local view's notifications are hooked into DbContext change tracking such that the local view stays in sync with the DbContext.</span></span> <span data-ttu-id="22386-304">Plus précisément :</span><span class="sxs-lookup"><span data-stu-id="22386-304">Specifically:</span></span>

- <span data-ttu-id="22386-305">L’ajout d’une nouvelle entité à `DbSet.Local` entraîne son suivi par DbContext, en général dans l' `Added` État.</span><span class="sxs-lookup"><span data-stu-id="22386-305">Adding a new entity to `DbSet.Local` causes it to be tracked by the DbContext, typically in the `Added` state.</span></span> <span data-ttu-id="22386-306">(Si l’entité a déjà une valeur de clé générée, elle est suivie à la `Unchanged` place.)</span><span class="sxs-lookup"><span data-stu-id="22386-306">(If the entity already has a generated key value, then it is tracked as `Unchanged` instead.)</span></span>
- <span data-ttu-id="22386-307">La suppression d’une entité de `DbSet.Local` entraîne son marquage comme `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="22386-307">Removing an entity from `DbSet.Local` causes it to be marked as `Deleted`.</span></span>
- <span data-ttu-id="22386-308">Une entité qui est suivie par DbContext s’affiche automatiquement dans la `DbSet.Local` collection.</span><span class="sxs-lookup"><span data-stu-id="22386-308">An entity that becomes tracked by the DbContext will automatically appear in the `DbSet.Local` collection.</span></span> <span data-ttu-id="22386-309">Par exemple, l’exécution d’une requête pour importer automatiquement d’autres entités entraîne la mise à jour de la vue locale.</span><span class="sxs-lookup"><span data-stu-id="22386-309">For example, executing a query to bring in more entities automatically causes the local view to be updated.</span></span>
- <span data-ttu-id="22386-310">Une entité marquée comme `Deleted` sera supprimée automatiquement de la collection locale.</span><span class="sxs-lookup"><span data-stu-id="22386-310">An entity that is marked as `Deleted` will be removed from the local collection automatically.</span></span>

<span data-ttu-id="22386-311">Cela signifie que la vue locale peut être utilisée pour manipuler des entités suivies simplement en ajoutant et en supprimant de la collection.</span><span class="sxs-lookup"><span data-stu-id="22386-311">This means the local view can be used to manipulate tracked entities simply by adding and removing from the collection.</span></span> <span data-ttu-id="22386-312">Par exemple, permet de modifier l’exemple de code précédent pour ajouter et supprimer des publications de la collection locale :</span><span class="sxs-lookup"><span data-stu-id="22386-312">For example, lets modify the previous example code to add and remove posts from the local collection:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

<span data-ttu-id="22386-313">La sortie reste inchangée par rapport à l’exemple précédent, car les modifications apportées à la vue locale sont synchronisées avec DbContext.</span><span class="sxs-lookup"><span data-stu-id="22386-313">The output remains unchanged from the previous example because changes made to the local view are synced with the DbContext.</span></span>

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a><span data-ttu-id="22386-314">Utilisation de l’affichage local pour Windows Forms ou la liaison de données WPF</span><span class="sxs-lookup"><span data-stu-id="22386-314">Using the local view for Windows Forms or WPF data binding</span></span>

<span data-ttu-id="22386-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> constitue la base de la liaison de données aux entités EF Core.</span><span class="sxs-lookup"><span data-stu-id="22386-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> forms the basis for data binding to EF Core entities.</span></span> <span data-ttu-id="22386-316">Toutefois, les Windows Forms et WPF fonctionnent mieux lorsqu’ils sont utilisés avec le type spécifique de notification de collection qu’ils attendent.</span><span class="sxs-lookup"><span data-stu-id="22386-316">However, both Windows Forms and WPF work best when used with the specific type of notifying collection that they expect.</span></span> <span data-ttu-id="22386-317">L’affichage local prend en charge la création de ces types de collections spécifiques :</span><span class="sxs-lookup"><span data-stu-id="22386-317">The local view supports creating these specific collection types:</span></span>

- <span data-ttu-id="22386-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> retourne un <xref:System.Collections.ObjectModel.ObservableCollection%601> pour la liaison de données WPF.</span><span class="sxs-lookup"><span data-stu-id="22386-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> returns an <xref:System.Collections.ObjectModel.ObservableCollection%601> for WPF data binding.</span></span>
- <span data-ttu-id="22386-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> retourne un <xref:System.ComponentModel.BindingList%601> pour Windows Forms la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="22386-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> returns a <xref:System.ComponentModel.BindingList%601> for Windows Forms data binding.</span></span>

<span data-ttu-id="22386-320">Exemple :</span><span class="sxs-lookup"><span data-stu-id="22386-320">For example:</span></span>

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

<span data-ttu-id="22386-321">Pour plus d’informations sur la liaison de données WPF avec EF Core, consultez [prise en main de WPF](xref:core/get-started/wpf) .</span><span class="sxs-lookup"><span data-stu-id="22386-321">See [Get Started with WPF](xref:core/get-started/wpf) for more information on WPF data binding with EF Core.</span></span>

> [!TIP]
> <span data-ttu-id="22386-322">La vue locale pour une instance de DbSet donnée est créée tardivement lors de son premier accès, puis mise en cache.</span><span class="sxs-lookup"><span data-stu-id="22386-322">The local view for a given DbSet instance is created lazily when first accessed and then cached.</span></span> <span data-ttu-id="22386-323">La création de LocalView elle-même est rapide et n’utilise pas de mémoire importante.</span><span class="sxs-lookup"><span data-stu-id="22386-323">LocalView creation itself is fast and it does not use significant memory.</span></span> <span data-ttu-id="22386-324">Toutefois, elle appelle [DetectChanges](xref:core/change-tracking/change-detection), qui peut être lente pour un grand nombre d’entités.</span><span class="sxs-lookup"><span data-stu-id="22386-324">However, it does call [DetectChanges](xref:core/change-tracking/change-detection), which can be slow for large numbers of entities.</span></span> <span data-ttu-id="22386-325">Les collections créées par `ToObservableCollection` et `ToBindingList` sont également créées tardivement, puis mises en cache.</span><span class="sxs-lookup"><span data-stu-id="22386-325">The collections created by `ToObservableCollection` and `ToBindingList` are also created lazily and and then cached.</span></span> <span data-ttu-id="22386-326">Ces deux méthodes créent des collections, qui peuvent être lentes et utiliser une grande quantité de mémoire lorsque des milliers d’entités sont impliquées.</span><span class="sxs-lookup"><span data-stu-id="22386-326">Both of these methods create new collections, which can be slow and use a lot of memory when thousands of entities are involved.</span></span>
