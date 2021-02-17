---
title: Résolution d’identité-EF Core
description: Résolution de plusieurs instances d’entité en une seule instance à l’aide des valeurs de clé primaire
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: d4c8f935c8d0ab92eaecd8fc7a4156bd824713d4
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543612"
---
# <a name="identity-resolution-in-ef-core"></a><span data-ttu-id="8a46d-103">Résolution d’identité dans EF Core</span><span class="sxs-lookup"><span data-stu-id="8a46d-103">Identity Resolution in EF Core</span></span>

<span data-ttu-id="8a46d-104">Un <xref:Microsoft.EntityFrameworkCore.DbContext> peut uniquement effectuer le suivi d’une instance d’entité avec une valeur de clé primaire donnée.</span><span class="sxs-lookup"><span data-stu-id="8a46d-104">A <xref:Microsoft.EntityFrameworkCore.DbContext> can only track one entity instance with any given primary key value.</span></span> <span data-ttu-id="8a46d-105">Cela signifie que plusieurs instances d’une entité avec la même valeur de clé doivent être résolues en une seule instance.</span><span class="sxs-lookup"><span data-stu-id="8a46d-105">This means multiple instances of an entity with the same key value must be resolved to a single instance.</span></span> <span data-ttu-id="8a46d-106">C’est ce que l’on appelle la « résolution d’identité ».</span><span class="sxs-lookup"><span data-stu-id="8a46d-106">This is called "identity resolution".</span></span> <span data-ttu-id="8a46d-107">La résolution d’identité garantit Entity Framework Core (EF Core) effectue le suivi d’un graphique cohérent sans ambiguïté quant aux relations ou valeurs de propriété des entités.</span><span class="sxs-lookup"><span data-stu-id="8a46d-107">Identity resolution ensures Entity Framework Core (EF Core) is tracking a consistent graph with no ambiguities about the relationships or property values of the entities.</span></span>

> [!TIP]
> <span data-ttu-id="8a46d-108">Ce document suppose que les États d’entité et les principes de base du suivi des modifications de EF Core sont compris.</span><span class="sxs-lookup"><span data-stu-id="8a46d-108">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="8a46d-109">Pour plus d’informations sur ces rubriques, consultez [change Tracking dans EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="8a46d-109">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="8a46d-110">Vous pouvez exécuter et déboguer dans tout le code de ce document en [téléchargeant l’exemple de code à partir de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore).</span><span class="sxs-lookup"><span data-stu-id="8a46d-110">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore).</span></span>

## <a name="introduction"></a><span data-ttu-id="8a46d-111">Introduction</span><span class="sxs-lookup"><span data-stu-id="8a46d-111">Introduction</span></span>

<span data-ttu-id="8a46d-112">Le code suivant interroge une entité, puis tente d’attacher une instance différente avec la même valeur de clé primaire :</span><span class="sxs-lookup"><span data-stu-id="8a46d-112">The following code queries for an entity and then attempts to attach a different instance with the same primary key value:</span></span>

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

<span data-ttu-id="8a46d-113">L’exécution de ce code entraîne l’exception suivante :</span><span class="sxs-lookup"><span data-stu-id="8a46d-113">Running this code results in the following exception:</span></span>

> <span data-ttu-id="8a46d-114">System. InvalidOperationException : impossible de suivre l’instance du type d’entité « blog », car une autre instance avec la valeur de clé « {ID : 1} » fait déjà l’objet d’un suivi.</span><span class="sxs-lookup"><span data-stu-id="8a46d-114">System.InvalidOperationException: The instance of entity type 'Blog' cannot be tracked because another instance with the key value '{Id: 1}' is already being tracked.</span></span> <span data-ttu-id="8a46d-115">Lors de l’attachement d’entités existantes, assurez-vous qu’une seule instance d’entité avec une valeur de clé donnée est attachée.</span><span class="sxs-lookup"><span data-stu-id="8a46d-115">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="8a46d-116">EF Core nécessite une seule instance pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="8a46d-116">EF Core requires a single instance because:</span></span>

- <span data-ttu-id="8a46d-117">Les valeurs de propriété peuvent être différentes entre plusieurs instances.</span><span class="sxs-lookup"><span data-stu-id="8a46d-117">Property values may be different between multiple instances.</span></span> <span data-ttu-id="8a46d-118">Lors de la mise à jour de la base de données, EF Core doit connaître les valeurs de propriété à utiliser.</span><span class="sxs-lookup"><span data-stu-id="8a46d-118">When updating the database, EF Core needs to know which property values to use.</span></span>
- <span data-ttu-id="8a46d-119">Les relations avec d’autres entités peuvent être différentes entre plusieurs instances.</span><span class="sxs-lookup"><span data-stu-id="8a46d-119">Relationships with other entities may be different between multiple instances.</span></span> <span data-ttu-id="8a46d-120">Par exemple, « Bloga » peut être lié à une autre collection de publications que « blogB ».</span><span class="sxs-lookup"><span data-stu-id="8a46d-120">For example, "blogA" may be related to a different collection of posts than "blogB".</span></span>

<span data-ttu-id="8a46d-121">L’exception ci-dessus est généralement rencontrée dans les situations suivantes :</span><span class="sxs-lookup"><span data-stu-id="8a46d-121">The exception above is commonly encountered in these situations:</span></span>

- <span data-ttu-id="8a46d-122">Lors d’une tentative de mise à jour d’une entité</span><span class="sxs-lookup"><span data-stu-id="8a46d-122">When attempting to update an entity</span></span>
- <span data-ttu-id="8a46d-123">Lors d’une tentative de suivi d’un graphique sérialisé d’entités</span><span class="sxs-lookup"><span data-stu-id="8a46d-123">When attempting to track a serialized graph of entities</span></span>
- <span data-ttu-id="8a46d-124">En cas d’échec de définition d’une valeur de clé qui n’est pas générée automatiquement</span><span class="sxs-lookup"><span data-stu-id="8a46d-124">When failing to set a key value that is not automatically generated</span></span>
- <span data-ttu-id="8a46d-125">Lors de la réutilisation d’une instance de DbContext pour plusieurs unités de travail</span><span class="sxs-lookup"><span data-stu-id="8a46d-125">When reusing a DbContext instance for multiple units-of-work</span></span>

<span data-ttu-id="8a46d-126">Chacune de ces situations est décrite dans les sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="8a46d-126">Each of these situations is discussed in the following sections.</span></span>

## <a name="updating-an-entity"></a><span data-ttu-id="8a46d-127">Mise à jour d’une entité</span><span class="sxs-lookup"><span data-stu-id="8a46d-127">Updating an entity</span></span>

<span data-ttu-id="8a46d-128">Il existe plusieurs approches pour mettre à jour une entité avec de nouvelles valeurs, comme décrit dans [change Tracking dans EF Core](xref:core/change-tracking/index) et [suivre explicitement des entités](xref:core/change-tracking/explicit-tracking).</span><span class="sxs-lookup"><span data-stu-id="8a46d-128">There are several different approaches to update an entity with new values, as covered in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking).</span></span> <span data-ttu-id="8a46d-129">Ces approches sont décrites ci-dessous dans le contexte de la résolution d’identité.</span><span class="sxs-lookup"><span data-stu-id="8a46d-129">These approaches are outlined below in the context of identity resolution.</span></span> <span data-ttu-id="8a46d-130">Un point important à noter est que chacune des approches utilise une requête ou un appel à l’une ou l' `Update` autre `Attach` , mais **_jamais les deux_** à la fois.</span><span class="sxs-lookup"><span data-stu-id="8a46d-130">An important point to notice is that each of the approaches use either a query or a call to one of `Update` or `Attach`, but **_never both_**.</span></span>

### <a name="call-update"></a><span data-ttu-id="8a46d-131">Appel de mise à jour</span><span class="sxs-lookup"><span data-stu-id="8a46d-131">Call Update</span></span>

<span data-ttu-id="8a46d-132">Souvent, l’entité à mettre à jour ne provient pas d’une requête sur le DbContext que nous allons utiliser pour SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="8a46d-132">Often the entity to update does not come from a query on the DbContext that we are going to use for SaveChanges.</span></span> <span data-ttu-id="8a46d-133">Par exemple, dans une application Web, une instance d’entité peut être créée à partir des informations contenues dans une demande de publication.</span><span class="sxs-lookup"><span data-stu-id="8a46d-133">For example, in a web application, an entity instance may be created from the information in a POST request.</span></span> <span data-ttu-id="8a46d-134">La façon la plus simple de gérer cela consiste à utiliser <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> ou <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8a46d-134">The simplest way to handle this is to use <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="8a46d-135">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8a46d-135">For example:</span></span>

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

<span data-ttu-id="8a46d-136">Dans ce cas :</span><span class="sxs-lookup"><span data-stu-id="8a46d-136">In this case:</span></span>

- <span data-ttu-id="8a46d-137">Une seule instance de l’entité est créée.</span><span class="sxs-lookup"><span data-stu-id="8a46d-137">Only a single instance of the entity is created.</span></span>
- <span data-ttu-id="8a46d-138">L’instance d’entité n’est **pas** interrogée à partir de la base de données dans le cadre de la mise à jour.</span><span class="sxs-lookup"><span data-stu-id="8a46d-138">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="8a46d-139">Toutes les valeurs de propriété seront mises à jour dans la base de données, qu’elles aient ou non été modifiées.</span><span class="sxs-lookup"><span data-stu-id="8a46d-139">All property values will be updated in the database, regardless of whether they have actually changed or not.</span></span>
- <span data-ttu-id="8a46d-140">Un aller-retour de base de données est effectué.</span><span class="sxs-lookup"><span data-stu-id="8a46d-140">One database round-trip is made.</span></span>

### <a name="query-then-apply-changes"></a><span data-ttu-id="8a46d-141">Requête, appliquer les modifications</span><span class="sxs-lookup"><span data-stu-id="8a46d-141">Query then apply changes</span></span>

<span data-ttu-id="8a46d-142">En règle générale, il n’est pas possible de connaître les valeurs de propriété qui ont été réellement modifiées lorsqu’une entité est créée à partir d’informations dans une requête de publication ou similaire.</span><span class="sxs-lookup"><span data-stu-id="8a46d-142">Usually it is not known which property values have actually been changed when an entity is created from information in a POST request or similar.</span></span> <span data-ttu-id="8a46d-143">Il est souvent plus simple de mettre à jour toutes les valeurs de la base de données, comme nous l’avons fait dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="8a46d-143">Often it is fine to just update all values in the database, as we did in the previous example.</span></span> <span data-ttu-id="8a46d-144">Toutefois, si l’application gère de nombreuses entités et qu’un petit nombre de celles-ci ont des modifications réelles, il peut être utile de limiter les mises à jour envoyées.</span><span class="sxs-lookup"><span data-stu-id="8a46d-144">However, if the application is handling many entities and only a small number of those have actual changes, then it may be useful to limit the updates sent.</span></span> <span data-ttu-id="8a46d-145">Pour ce faire, vous pouvez exécuter une requête pour suivre les entités telles qu’elles existent actuellement dans la base de données, puis appliquer les modifications à ces entités suivies.</span><span class="sxs-lookup"><span data-stu-id="8a46d-145">This can be achieved by executing a query to track the entities as they currently exist in the database, and then applying changes to these tracked entities.</span></span> <span data-ttu-id="8a46d-146">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8a46d-146">For example:</span></span>

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

<span data-ttu-id="8a46d-147">Dans ce cas :</span><span class="sxs-lookup"><span data-stu-id="8a46d-147">In this case:</span></span>

- <span data-ttu-id="8a46d-148">Une seule instance de l’entité est suivie ; celui qui est retourné par la requête de la base de données `Find` .</span><span class="sxs-lookup"><span data-stu-id="8a46d-148">Only a single instance of the entity is tracked; the one that is returned from the database by the `Find` query.</span></span>
- <span data-ttu-id="8a46d-149">`Update`, `Attach` , etc. ne sont **pas** utilisés.</span><span class="sxs-lookup"><span data-stu-id="8a46d-149">`Update`, `Attach`, etc. are **not** used.</span></span>
- <span data-ttu-id="8a46d-150">Seules les valeurs de propriété qui ont été modifiées sont mises à jour dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8a46d-150">Only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="8a46d-151">Deux allers-retours de base de données sont effectués.</span><span class="sxs-lookup"><span data-stu-id="8a46d-151">Two database round-trips are made.</span></span>

<span data-ttu-id="8a46d-152">EF Core a des assistances pour transférer des valeurs de propriété comme celle-ci.</span><span class="sxs-lookup"><span data-stu-id="8a46d-152">EF Core has some helpers for transferring property values like this.</span></span> <span data-ttu-id="8a46d-153">Par exemple, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> copie toutes les valeurs de l’objet donné et les définit sur l’objet suivi :</span><span class="sxs-lookup"><span data-stu-id="8a46d-153">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> will copy all the values from the given object and set them on the tracked object:</span></span>

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

<span data-ttu-id="8a46d-154">`SetValues` accepte divers types d’objets, y compris les objets DTO (Data Transfer Objects) avec des noms de propriété qui correspondent aux propriétés du type d’entité.</span><span class="sxs-lookup"><span data-stu-id="8a46d-154">`SetValues` accepts various object types, including data transfer objects (DTOs) with property names that match the properties of the entity type.</span></span> <span data-ttu-id="8a46d-155">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8a46d-155">For example:</span></span>

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

<span data-ttu-id="8a46d-156">Ou un dictionnaire avec des entrées nom/valeur pour les valeurs de propriété :</span><span class="sxs-lookup"><span data-stu-id="8a46d-156">Or a dictionary with name/value entries for the property values:</span></span>

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

<span data-ttu-id="8a46d-157">Pour plus d’informations sur l’utilisation des valeurs de propriété, consultez [accès aux entités suivies](xref:core/change-tracking/entity-entries) .</span><span class="sxs-lookup"><span data-stu-id="8a46d-157">See [Accessing tracked entities](xref:core/change-tracking/entity-entries) for more information on working with property values like this.</span></span>

### <a name="use-original-values"></a><span data-ttu-id="8a46d-158">Utiliser les valeurs d’origine</span><span class="sxs-lookup"><span data-stu-id="8a46d-158">Use original values</span></span>

<span data-ttu-id="8a46d-159">Jusqu’à présent, chaque approche a exécuté une requête avant d’effectuer la mise à jour, ou mis à jour toutes les valeurs de propriété, qu’elles aient ou non été modifiées.</span><span class="sxs-lookup"><span data-stu-id="8a46d-159">So far each approach has either executed a query before making the update, or updated all property values regardless of whether or not they have changed.</span></span> <span data-ttu-id="8a46d-160">Pour mettre à jour uniquement les valeurs qui ont été modifiées sans interroger dans le cadre de la mise à jour, vous devez disposer d’informations spécifiques sur les valeurs de propriété qui ont changé.</span><span class="sxs-lookup"><span data-stu-id="8a46d-160">To update only values that have changed without querying as part of the update requires specific information about which property values have changed.</span></span> <span data-ttu-id="8a46d-161">Pour obtenir ces informations, il est courant de renvoyer les valeurs actuelles et d’origine dans la publication HTTP ou de manière similaire.</span><span class="sxs-lookup"><span data-stu-id="8a46d-161">A common way to get this information is to send back both the current and original values in the HTTP Post or similar.</span></span> <span data-ttu-id="8a46d-162">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8a46d-162">For example:</span></span>

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

<span data-ttu-id="8a46d-163">Dans ce code, l’entité avec des valeurs modifiées est attachée pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="8a46d-163">In this code the entity with modified values is first attached.</span></span> <span data-ttu-id="8a46d-164">EF Core effectue le suivi de l’entité dans l' `Unchanged` État, c’est-à-dire sans aucune valeur de propriété marquée comme modifiée.</span><span class="sxs-lookup"><span data-stu-id="8a46d-164">This causes EF Core to track the entity in the `Unchanged` state; that is, with no property values marked as modified.</span></span> <span data-ttu-id="8a46d-165">Le dictionnaire des valeurs d’origine est ensuite appliqué à cette entité suivie.</span><span class="sxs-lookup"><span data-stu-id="8a46d-165">The dictionary of original values is then applied to this tracked entity.</span></span> <span data-ttu-id="8a46d-166">Cela sera marqué comme propriétés modifiées avec différentes valeurs actuelles et d’origine.</span><span class="sxs-lookup"><span data-stu-id="8a46d-166">This will mark as modified properties with different current and original values.</span></span> <span data-ttu-id="8a46d-167">Les propriétés qui ont les mêmes valeurs actuelles et d’origine ne sont pas marquées comme modifiées.</span><span class="sxs-lookup"><span data-stu-id="8a46d-167">Properties that have the same current and original values will not be marked as modified.</span></span>

<span data-ttu-id="8a46d-168">Dans ce cas :</span><span class="sxs-lookup"><span data-stu-id="8a46d-168">In this case:</span></span>

- <span data-ttu-id="8a46d-169">Une seule instance de l’entité est suivie, en utilisant Attach.</span><span class="sxs-lookup"><span data-stu-id="8a46d-169">Only a single instance of the entity is tracked, using Attach.</span></span>
- <span data-ttu-id="8a46d-170">L’instance d’entité n’est **pas** interrogée à partir de la base de données dans le cadre de la mise à jour.</span><span class="sxs-lookup"><span data-stu-id="8a46d-170">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="8a46d-171">L’application des valeurs d’origine permet de s’assurer que seules les valeurs de propriété qui ont réellement changé sont mises à jour dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8a46d-171">Applying the original values ensures that only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="8a46d-172">Un aller-retour de base de données est effectué.</span><span class="sxs-lookup"><span data-stu-id="8a46d-172">One database round-trip is made.</span></span>

<span data-ttu-id="8a46d-173">Comme pour les exemples de la section précédente, les valeurs d’origine n’ont pas besoin d’être passées en tant que dictionnaire ; une instance d’entité ou un DTO fonctionne également.</span><span class="sxs-lookup"><span data-stu-id="8a46d-173">As with the examples in the previous section, the original values do not have to passed as a dictionary; an entity instance or DTO will also work.</span></span>

> [!TIP]
> <span data-ttu-id="8a46d-174">Bien que cette approche ait des caractéristiques attrayantes, elle nécessite l’envoi des valeurs d’origine de l’entité vers et depuis le client Web.</span><span class="sxs-lookup"><span data-stu-id="8a46d-174">While this approach has appealing characteristics, it does require sending the entity's original values to and from the web client.</span></span> <span data-ttu-id="8a46d-175">Déterminez avec soin si cette complexité supplémentaire mérite les avantages. pour de nombreuses applications, une des approches les plus simples est plus pragmatique.</span><span class="sxs-lookup"><span data-stu-id="8a46d-175">Carefully consider whether this extra complexity is worth the benefits; for many applications one of the simpler approaches is more pragmatic.</span></span>

## <a name="attaching-a-serialized-graph"></a><span data-ttu-id="8a46d-176">Attachement d’un graphique sérialisé</span><span class="sxs-lookup"><span data-stu-id="8a46d-176">Attaching a serialized graph</span></span>

<span data-ttu-id="8a46d-177">EF Core fonctionne avec les graphiques des entités connectées via les clés étrangères et les propriétés de navigation, comme décrit dans [modification des clés étrangères et des navigations](xref:core/change-tracking/relationship-changes).</span><span class="sxs-lookup"><span data-stu-id="8a46d-177">EF Core works with graphs of entities connected via foreign keys and navigation properties, as described in [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="8a46d-178">Si ces graphiques sont créés en dehors de EF Core utilisant, par exemple, à partir d’un fichier JSON, ils peuvent avoir plusieurs instances de la même entité.</span><span class="sxs-lookup"><span data-stu-id="8a46d-178">If these graphs are created outside of EF Core using, for example, from a JSON file, then they can have multiple instances of the same entity.</span></span> <span data-ttu-id="8a46d-179">Ces doublons doivent être résolus en instances uniques pour que le graphique puisse être suivi.</span><span class="sxs-lookup"><span data-stu-id="8a46d-179">These duplicates need to be resolved into single instances before the graph can be tracked.</span></span>

### <a name="graphs-with-no-duplicates"></a><span data-ttu-id="8a46d-180">Graphiques sans doublons</span><span class="sxs-lookup"><span data-stu-id="8a46d-180">Graphs with no duplicates</span></span>

<span data-ttu-id="8a46d-181">Avant de poursuivre, il est important de reconnaître que :</span><span class="sxs-lookup"><span data-stu-id="8a46d-181">Before going any further it is important to recognize that:</span></span>

- <span data-ttu-id="8a46d-182">Les sérialiseurs ont souvent des options pour gérer les boucles et les instances dupliquées dans le graphique.</span><span class="sxs-lookup"><span data-stu-id="8a46d-182">Serializers often have options for handling loops and duplicate instances in the graph.</span></span>
- <span data-ttu-id="8a46d-183">Le choix de l’objet utilisé comme racine du graphique peut souvent permettre de réduire ou de supprimer les doublons.</span><span class="sxs-lookup"><span data-stu-id="8a46d-183">The choice of object used as the graph root can often help reduce or remove duplicates.</span></span>

<span data-ttu-id="8a46d-184">Si possible, utilisez les options de sérialisation et choisissez les racines qui n’entraînent pas de doublons.</span><span class="sxs-lookup"><span data-stu-id="8a46d-184">If possible, use serialization options and choose roots that do not result in duplicates.</span></span> <span data-ttu-id="8a46d-185">Par exemple, le code suivant utilise [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/) pour sérialiser une liste de blogs chacun avec les publications qui lui sont associées :</span><span class="sxs-lookup"><span data-stu-id="8a46d-185">For example, the following code uses [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) to serialize a list of blogs each with its associated posts:</span></span>

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

<span data-ttu-id="8a46d-186">Le code JSON généré à partir de ce code est le suivant :</span><span class="sxs-lookup"><span data-stu-id="8a46d-186">The JSON generated from this code is:</span></span>

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

<span data-ttu-id="8a46d-187">Notez qu’il n’existe aucun blog ou billet en double dans le JSON.</span><span class="sxs-lookup"><span data-stu-id="8a46d-187">Notice that there are no duplicate blogs or posts in the JSON.</span></span> <span data-ttu-id="8a46d-188">Cela signifie que les appels simples à `Update` fonctionnent pour mettre à jour ces entités dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="8a46d-188">This means that simple calls to `Update` will work to update these entities in the database:</span></span>

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a><span data-ttu-id="8a46d-189">Gestion des doublons</span><span class="sxs-lookup"><span data-stu-id="8a46d-189">Handling duplicates</span></span>

<span data-ttu-id="8a46d-190">Le code de l’exemple précédent a sérialisé chaque blog avec les publications qui lui sont associées.</span><span class="sxs-lookup"><span data-stu-id="8a46d-190">The code in the previous example serialized each blog with its associated posts.</span></span> <span data-ttu-id="8a46d-191">Si vous modifiez cette valeur pour sérialiser chaque publication avec le blog qui lui est associé, les doublons sont introduits dans le JSON sérialisé.</span><span class="sxs-lookup"><span data-stu-id="8a46d-191">If this is changed to serialize each post with its associated blog, then duplicates are introduced into the serialized JSON.</span></span> <span data-ttu-id="8a46d-192">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8a46d-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

<span data-ttu-id="8a46d-193">Le JSON sérialisé se présente désormais comme suit :</span><span class="sxs-lookup"><span data-stu-id="8a46d-193">The serialized JSON now looks like this:</span></span>

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

<span data-ttu-id="8a46d-194">Notez que le graphique comprend maintenant plusieurs instances de blog avec la même valeur de clé, ainsi qu’une instance de publication multiple avec la même valeur de clé.</span><span class="sxs-lookup"><span data-stu-id="8a46d-194">Notice that the graph now includes multiple Blog instances with the same key value, as well as multiple Post instance with the same key value.</span></span> <span data-ttu-id="8a46d-195">Toute tentative d’effectuer le suivi de ce graphique comme nous l’avons fait dans l’exemple précédent lèvera :</span><span class="sxs-lookup"><span data-stu-id="8a46d-195">Attempting to track this graph like we did in the previous example will throw:</span></span>

> <span data-ttu-id="8a46d-196">System. InvalidOperationException : impossible de suivre l’instance du type d’entité « publication », car une autre instance avec la valeur de clé « {ID : 2} » fait déjà l’objet d’un suivi.</span><span class="sxs-lookup"><span data-stu-id="8a46d-196">System.InvalidOperationException: The instance of entity type 'Post' cannot be tracked because another instance with the key value '{Id: 2}' is already being tracked.</span></span> <span data-ttu-id="8a46d-197">Lors de l’attachement d’entités existantes, assurez-vous qu’une seule instance d’entité avec une valeur de clé donnée est attachée.</span><span class="sxs-lookup"><span data-stu-id="8a46d-197">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="8a46d-198">Nous pouvons résoudre ce problème de deux manières :</span><span class="sxs-lookup"><span data-stu-id="8a46d-198">We can fix this in two ways:</span></span>

- <span data-ttu-id="8a46d-199">Utiliser les options de sérialisation JSON qui préservent les références</span><span class="sxs-lookup"><span data-stu-id="8a46d-199">Use JSON serialization options that preserve references</span></span>
- <span data-ttu-id="8a46d-200">Effectuer la résolution de l’identité pendant le suivi du graphique</span><span class="sxs-lookup"><span data-stu-id="8a46d-200">Perform identity resolution while the graph is being tracked</span></span>

#### <a name="preserve-references"></a><span data-ttu-id="8a46d-201">Préserver les références</span><span class="sxs-lookup"><span data-stu-id="8a46d-201">Preserve references</span></span>

<span data-ttu-id="8a46d-202">Json.NET offre la `PreserveReferencesHandling` possibilité de gérer cela.</span><span class="sxs-lookup"><span data-stu-id="8a46d-202">Json.NET provides the `PreserveReferencesHandling` option to handle this.</span></span> <span data-ttu-id="8a46d-203">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8a46d-203">For example:</span></span>

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

<span data-ttu-id="8a46d-204">Le JSON obtenu se présente désormais comme suit :</span><span class="sxs-lookup"><span data-stu-id="8a46d-204">The resulting JSON now looks like this:</span></span>

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

<span data-ttu-id="8a46d-205">Notez que ce JSON a remplacé les doublons par des références telles `"$ref": "5"` que qui font référence à l’instance déjà existante dans le graphique.</span><span class="sxs-lookup"><span data-stu-id="8a46d-205">Notice that this JSON has replaced duplicates with references like `"$ref": "5"` that refer to the already existing instance in the graph.</span></span> <span data-ttu-id="8a46d-206">Ce graphique peut encore être suivi à l’aide des appels simples à `Update` , comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="8a46d-206">This graph can again be tracked using the simple calls to `Update`, as shown above.</span></span>

<span data-ttu-id="8a46d-207">La <xref:System.Text.Json> prise en charge dans les bibliothèques de classes de base .net (BCL) a une option similaire qui produit le même résultat.</span><span class="sxs-lookup"><span data-stu-id="8a46d-207">The <xref:System.Text.Json> support in the .NET base class libraries (BCL) has a similar option which produces the same result.</span></span> <span data-ttu-id="8a46d-208">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8a46d-208">For example:</span></span>

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a><span data-ttu-id="8a46d-209">Résoudre les doublons</span><span class="sxs-lookup"><span data-stu-id="8a46d-209">Resolve duplicates</span></span>

<span data-ttu-id="8a46d-210">S’il n’est pas possible d’éliminer les doublons dans le processus de sérialisation, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> fournit un moyen de gérer cela.</span><span class="sxs-lookup"><span data-stu-id="8a46d-210">If it is not possible to eliminate duplicates in the serialization process, then <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> provides a way to handle this.</span></span> <span data-ttu-id="8a46d-211">TrackGraph fonctionne comme `Add` `Attach` et, `Update` à ceci près qu’il génère un rappel pour chaque instance d’entité avant de le suivre.</span><span class="sxs-lookup"><span data-stu-id="8a46d-211">TrackGraph works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="8a46d-212">Ce rappel peut être utilisé pour effectuer le suivi de l’entité ou l’ignorer.</span><span class="sxs-lookup"><span data-stu-id="8a46d-212">This callback can be used to either track the entity or ignore it.</span></span> <span data-ttu-id="8a46d-213">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8a46d-213">For example:</span></span>

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

<span data-ttu-id="8a46d-214">Pour chaque entité du graphique, ce code effectue les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="8a46d-214">For each entity in the graph, this code will:</span></span>

- <span data-ttu-id="8a46d-215">Rechercher le type d’entité et la valeur de clé de l’entité</span><span class="sxs-lookup"><span data-stu-id="8a46d-215">Find the entity type and key value of the entity</span></span>
- <span data-ttu-id="8a46d-216">Rechercher l’entité avec cette clé dans le dispositif de suivi des modifications</span><span class="sxs-lookup"><span data-stu-id="8a46d-216">Lookup the entity with this key in the change tracker</span></span>
  - <span data-ttu-id="8a46d-217">Si l’entité est trouvée, aucune action supplémentaire n’est prise, car l’entité est un doublon</span><span class="sxs-lookup"><span data-stu-id="8a46d-217">If the entity is found, then no further action is taken as the entity is a duplicate</span></span>
  - <span data-ttu-id="8a46d-218">Si l’entité est introuvable, elle est suivie en affectant à l’état la valeur `Modified`</span><span class="sxs-lookup"><span data-stu-id="8a46d-218">If the entity is not found, then it is tracked by setting the state to `Modified`</span></span>

<span data-ttu-id="8a46d-219">La sortie de l’exécution de ce code est la suivante :</span><span class="sxs-lookup"><span data-stu-id="8a46d-219">The output from running this code is:</span></span>

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> <span data-ttu-id="8a46d-220">Ce code **suppose que tous les doublons sont identiques**.</span><span class="sxs-lookup"><span data-stu-id="8a46d-220">This code **assumes that all duplicates are identical**.</span></span> <span data-ttu-id="8a46d-221">Cela permet de choisir arbitrairement l’un des doublons à suivre tout en ignorant les autres.</span><span class="sxs-lookup"><span data-stu-id="8a46d-221">This makes it safe to arbitrarily choose one of the duplicates to track while discarding the others.</span></span> <span data-ttu-id="8a46d-222">Si les doublons peuvent différer, le code doit décider de la manière de déterminer celui à utiliser et comment combiner les valeurs de propriété et de navigation ensemble.</span><span class="sxs-lookup"><span data-stu-id="8a46d-222">If the duplicates can differ, then the code will need to decide how to determine which one to use, and how to combine property and navigation values together.</span></span>

> [!NOTE]
> <span data-ttu-id="8a46d-223">Par souci de simplicité, ce code suppose que chaque entité a une propriété de clé primaire appelée `Id` .</span><span class="sxs-lookup"><span data-stu-id="8a46d-223">For simplicity, this code assumes each entity has a primary key property called `Id`.</span></span> <span data-ttu-id="8a46d-224">Cela peut être codifié dans une interface ou une classe de base abstraite.</span><span class="sxs-lookup"><span data-stu-id="8a46d-224">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="8a46d-225">Sinon, la ou les propriétés de clé primaire peuvent être obtenues à partir des <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> métadonnées afin que ce code fonctionne avec n’importe quel type d’entité.</span><span class="sxs-lookup"><span data-stu-id="8a46d-225">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

## <a name="failing-to-set-key-values"></a><span data-ttu-id="8a46d-226">Échec de la définition des valeurs de clé</span><span class="sxs-lookup"><span data-stu-id="8a46d-226">Failing to set key values</span></span>

<span data-ttu-id="8a46d-227">Les types d’entités sont souvent configurés pour utiliser des [valeurs de clés générées automatiquement](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="8a46d-227">Entity types are often configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="8a46d-228">Il s’agit de la valeur par défaut pour les propriétés de type entier et GUID des clés non composites.</span><span class="sxs-lookup"><span data-stu-id="8a46d-228">This is the default for integer and GUID properties of non-composite keys.</span></span> <span data-ttu-id="8a46d-229">Toutefois, si le type d’entité n’est pas configuré pour utiliser des valeurs de clés générées automatiquement, une valeur de clé explicite doit être définie avant le suivi de l’entité.</span><span class="sxs-lookup"><span data-stu-id="8a46d-229">However, if the entity type is not configured to use automatically generated key values, then an explicit key value must be set before tracking the entity.</span></span> <span data-ttu-id="8a46d-230">Par exemple, à l’aide du type d’entité suivant :</span><span class="sxs-lookup"><span data-stu-id="8a46d-230">For example, using the following entity type:</span></span>

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

<span data-ttu-id="8a46d-231">Envisagez du code qui tente de Tracker deux nouvelles instances d’entité sans définir de valeurs de clés :</span><span class="sxs-lookup"><span data-stu-id="8a46d-231">Consider code that attempts to tracker two new entity instances without setting key values:</span></span>

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

<span data-ttu-id="8a46d-232">Ce code lèvera ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="8a46d-232">This code will throw:</span></span>

> <span data-ttu-id="8a46d-233">System. InvalidOperationException : impossible de suivre l’instance du type d’entité « PET », car une autre instance avec la valeur de clé « {ID : 0} » fait déjà l’objet d’un suivi.</span><span class="sxs-lookup"><span data-stu-id="8a46d-233">System.InvalidOperationException: The instance of entity type 'Pet' cannot be tracked because another instance with the key value '{Id: 0}' is already being tracked.</span></span> <span data-ttu-id="8a46d-234">Lors de l’attachement d’entités existantes, assurez-vous qu’une seule instance d’entité avec une valeur de clé donnée est attachée.</span><span class="sxs-lookup"><span data-stu-id="8a46d-234">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="8a46d-235">Pour résoudre ce problème, vous pouvez soit définir explicitement des valeurs de clés, soit configurer la propriété de clé pour utiliser les valeurs de clé générées.</span><span class="sxs-lookup"><span data-stu-id="8a46d-235">The fix for this is to either to set key values explicitly or configure the key property to use generated key values.</span></span> <span data-ttu-id="8a46d-236">Pour plus d’informations, consultez [valeurs générées](xref:core/modeling/generated-properties) .</span><span class="sxs-lookup"><span data-stu-id="8a46d-236">See [Generated Values](xref:core/modeling/generated-properties) for more information.</span></span>

## <a name="overusing-a-single-dbcontext-instance"></a><span data-ttu-id="8a46d-237">Utilisation d’une seule instance DbContext</span><span class="sxs-lookup"><span data-stu-id="8a46d-237">Overusing a single DbContext instance</span></span>

<span data-ttu-id="8a46d-238"><xref:Microsoft.EntityFrameworkCore.DbContext> est conçu pour représenter une unité de travail à courte durée de vie, comme décrit dans [initialisation et configuration de DbContext](xref:core/dbcontext-configuration/index), et élaboré dans [change Tracking dans EF Core](xref:core/change-tracking/index).</span><span class="sxs-lookup"><span data-stu-id="8a46d-238"><xref:Microsoft.EntityFrameworkCore.DbContext> is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span> <span data-ttu-id="8a46d-239">Si vous ne suivez pas ce guide, il est facile de rencontrer des situations où une tentative est effectuée pour effectuer le suivi de plusieurs instances de la même entité.</span><span class="sxs-lookup"><span data-stu-id="8a46d-239">Not following this guidance makes it is easy to run into situations where an attempt is made to track multiple instances of the same entity.</span></span> <span data-ttu-id="8a46d-240">Voici des exemples courants :</span><span class="sxs-lookup"><span data-stu-id="8a46d-240">Common examples are:</span></span>

- <span data-ttu-id="8a46d-241">Utilisez la même instance DbContext pour configurer l’état de test, puis exécutez le test.</span><span class="sxs-lookup"><span data-stu-id="8a46d-241">Using the same DbContext instance to both set up test state and then execute the test.</span></span> <span data-ttu-id="8a46d-242">Il en résulte souvent que la DbContext effectue le suivi d’une instance d’entité à partir de la configuration de test, tout en tentant d’attacher une nouvelle instance au test.</span><span class="sxs-lookup"><span data-stu-id="8a46d-242">This often results in the DbContext still tracking one entity instance from test setup, while then attempting to attach a new instance in the test proper.</span></span> <span data-ttu-id="8a46d-243">Au lieu de cela, utilisez une autre instance de DbContext pour configurer l’état de test et le code de test.</span><span class="sxs-lookup"><span data-stu-id="8a46d-243">Instead, use a different DbContext instance for setting up test state and the test code proper.</span></span>
- <span data-ttu-id="8a46d-244">Utilisation d’une instance DbContext partagée dans un référentiel ou du code similaire.</span><span class="sxs-lookup"><span data-stu-id="8a46d-244">Using a shared DbContext instance in a repository or similar code.</span></span> <span data-ttu-id="8a46d-245">Au lieu de cela, assurez-vous que votre référentiel utilise une seule instance DbContext pour chaque unité de travail.</span><span class="sxs-lookup"><span data-stu-id="8a46d-245">Instead, make sure your repository uses a single DbContext instance for each unit-of-work.</span></span>

## <a name="identity-resolution-and-queries"></a><span data-ttu-id="8a46d-246">Résolution et requêtes d’identité</span><span class="sxs-lookup"><span data-stu-id="8a46d-246">Identity resolution and queries</span></span>

<span data-ttu-id="8a46d-247">La résolution d’identité s’effectue automatiquement lorsque les entités sont suivies à partir d’une requête.</span><span class="sxs-lookup"><span data-stu-id="8a46d-247">Identity resolution happens automatically when entities are tracked from a query.</span></span> <span data-ttu-id="8a46d-248">Cela signifie que si une instance d’entité avec une valeur de clé donnée est déjà suivie, cette instance suivie existante est utilisée au lieu de créer une nouvelle instance.</span><span class="sxs-lookup"><span data-stu-id="8a46d-248">This means that if an entity instance with a given key value is already tracked, then this existing tracked instance is used instead of creating a new instance.</span></span> <span data-ttu-id="8a46d-249">Cela a une conséquence importante : si les données ont été modifiées dans la base de données, elles ne seront pas reflétées dans les résultats de la requête.</span><span class="sxs-lookup"><span data-stu-id="8a46d-249">This has an important consequence: if the data has changed in the database, then this will not be reflected in the results of the query.</span></span> <span data-ttu-id="8a46d-250">Il s’agit d’une bonne raison d’utiliser une nouvelle instance de DbContext pour chaque unité de travail, comme décrit dans [initialisation et configuration de DbContext](xref:core/dbcontext-configuration/index), et élaborée dans [change Tracking dans EF Core](xref:core/change-tracking/index).</span><span class="sxs-lookup"><span data-stu-id="8a46d-250">This is a good reason to use a new DbContext instance for each unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8a46d-251">Il est important de comprendre que EF Core exécute toujours une requête LINQ sur un DbSet sur la base de données et renvoie uniquement les résultats en fonction de ce qui se trouve dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8a46d-251">It is important to understand that EF Core always executes a LINQ query on a DbSet against the database and only returns results based on what is in the database.</span></span> <span data-ttu-id="8a46d-252">Toutefois, pour une requête de suivi, si les entités retournées sont déjà suivies, les instances suivies sont utilisées au lieu de créer des instances à partir des données de la base de données.</span><span class="sxs-lookup"><span data-stu-id="8a46d-252">However, for a tracking query, if the entities returned are already tracked, then the tracked instances are used instead of creating a instances from the data in the database.</span></span>

<span data-ttu-id="8a46d-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> ou <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> peut être utilisé lorsque les entités suivies doivent être actualisées avec les données les plus récentes de la base de données.</span><span class="sxs-lookup"><span data-stu-id="8a46d-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> can be used when tracked entities need to be refreshed with the latest data from the database.</span></span> <span data-ttu-id="8a46d-254">Pour plus d’informations, consultez [accès aux entités suivies](xref:core/change-tracking/entity-entries) .</span><span class="sxs-lookup"><span data-stu-id="8a46d-254">See [Accessing Tracked Entities](xref:core/change-tracking/entity-entries) for more information.</span></span>

<span data-ttu-id="8a46d-255">Contrairement aux requêtes de suivi, les requêtes sans suivi n’effectuent pas de résolution d’identité.</span><span class="sxs-lookup"><span data-stu-id="8a46d-255">In contrast to tracking queries, no-tracking queries do not perform identity resolution.</span></span> <span data-ttu-id="8a46d-256">Cela signifie qu’aucune requête de suivi ne peut retourner des doublons, comme dans le cas de sérialisation JSON décrit précédemment.</span><span class="sxs-lookup"><span data-stu-id="8a46d-256">This means that no-tracking queries can return duplicates just like in the JSON serialization case described earlier.</span></span> <span data-ttu-id="8a46d-257">Ce n’est généralement pas un problème si les résultats de la requête vont être sérialisés et envoyés au client.</span><span class="sxs-lookup"><span data-stu-id="8a46d-257">This is usually not an issue if the query results are going to be serialized and sent to the client.</span></span>

> [!TIP]
> <span data-ttu-id="8a46d-258">N’effectuez pas régulièrement une requête de non-suivi, puis attachez les entités retournées au même contexte.</span><span class="sxs-lookup"><span data-stu-id="8a46d-258">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="8a46d-259">Cela sera à la fois plus lent et plus difficile à utiliser qu’une requête de suivi.</span><span class="sxs-lookup"><span data-stu-id="8a46d-259">This will be both slower and harder to get right than using a tracking query.</span></span>

<span data-ttu-id="8a46d-260">Les requêtes sans suivi n’effectuent pas de résolution d’identité, car cela a un impact sur les performances de diffusion en continu d’un grand nombre d’entités à partir d’une requête.</span><span class="sxs-lookup"><span data-stu-id="8a46d-260">No-tracking queries do not perform identity resolution because doing so impacts the performance of streaming a large number of entities from a query.</span></span> <span data-ttu-id="8a46d-261">Cela est dû au fait que la résolution d’identité requiert le suivi de chaque instance renvoyée afin qu’elle puisse être utilisée au lieu de créer un doublon ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="8a46d-261">This is because identity resolution requires keeping track of each instance returned so that it can be used instead of later creating a duplicate.</span></span>

<span data-ttu-id="8a46d-262">À compter de EF Core 5,0, aucune requête de suivi ne peut être forcée pour effectuer la résolution d’identité à l’aide de <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> .</span><span class="sxs-lookup"><span data-stu-id="8a46d-262">Starting with EF Core 5.0, no-tracking queries can be forced to perform identity resolution by using <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})>.</span></span> <span data-ttu-id="8a46d-263">La requête effectue ensuite le suivi des instances retournées (sans les suivre de manière normale) et s’assure qu’aucun doublon n’est créé dans les résultats de la requête.</span><span class="sxs-lookup"><span data-stu-id="8a46d-263">The query will then keep track of returned instances (without tracking them in the normal way) and ensure no duplicates are created in the query results.</span></span>

## <a name="overriding-object-equality"></a><span data-ttu-id="8a46d-264">Substitution de l’égalité des objets</span><span class="sxs-lookup"><span data-stu-id="8a46d-264">Overriding object equality</span></span>

<span data-ttu-id="8a46d-265">EF Core utilise l' [égalité de référence](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) lors de la comparaison d’instances d’entité.</span><span class="sxs-lookup"><span data-stu-id="8a46d-265">EF Core uses [reference equality](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) when comparing entity instances.</span></span> <span data-ttu-id="8a46d-266">C’est le cas même si les types d’entités substituent <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> ou modifient sinon l’égalité des objets.</span><span class="sxs-lookup"><span data-stu-id="8a46d-266">This is the case even if the entity types override <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> or otherwise change object equality.</span></span> <span data-ttu-id="8a46d-267">Toutefois, il existe un emplacement dans lequel l’égalité de substitution peut avoir un impact sur le comportement EF Core : lorsque les navigations de collections utilisent l’égalité substituée au lieu de l’égalité de référence, et signalent donc plusieurs instances comme identiques.</span><span class="sxs-lookup"><span data-stu-id="8a46d-267">However, there is one place where overriding equality can impact EF Core behavior: when collection navigations use the overridden equality instead of reference equality, and hence report multiple instances as the same.</span></span>

<span data-ttu-id="8a46d-268">Pour cette raison, il est recommandé d’éviter l’égalité des entités de substitution.</span><span class="sxs-lookup"><span data-stu-id="8a46d-268">Because of this it is recommended that overriding entity equality should be avoided.</span></span> <span data-ttu-id="8a46d-269">S’il est utilisé, assurez-vous de créer des navigations de collection qui forcent l’égalité des références.</span><span class="sxs-lookup"><span data-stu-id="8a46d-269">If it is used, then make sure to create collection navigations that force reference equality.</span></span> <span data-ttu-id="8a46d-270">Par exemple, créez un comparateur d’égalité qui utilise l’égalité de référence :</span><span class="sxs-lookup"><span data-stu-id="8a46d-270">For example, create an equality comparer that uses reference equality:</span></span>

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

<span data-ttu-id="8a46d-271">(À compter de .NET 5, il est inclus dans la bibliothèque de classes de base en tant que <xref:System.Collections.Generic.ReferenceEqualityComparer> .)</span><span class="sxs-lookup"><span data-stu-id="8a46d-271">(Starting with .NET 5, this is included in the BCL as <xref:System.Collections.Generic.ReferenceEqualityComparer>.)</span></span>

<span data-ttu-id="8a46d-272">Ce comparateur peut ensuite être utilisé lors de la création de navigations de collection.</span><span class="sxs-lookup"><span data-stu-id="8a46d-272">This comparer can then be used when creating collection navigations.</span></span> <span data-ttu-id="8a46d-273">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8a46d-273">For example:</span></span>

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a><span data-ttu-id="8a46d-274">Comparaison des propriétés de clé</span><span class="sxs-lookup"><span data-stu-id="8a46d-274">Comparing key properties</span></span>

<span data-ttu-id="8a46d-275">Outre les comparaisons d’égalité, les valeurs de clé doivent également être triées.</span><span class="sxs-lookup"><span data-stu-id="8a46d-275">In addition to equality comparisons, key values also need to be ordered.</span></span> <span data-ttu-id="8a46d-276">Cela est important pour éviter les interblocages lors de la mise à jour de plusieurs entités dans un seul appel à SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="8a46d-276">This is important for avoiding deadlocks when updating multiple entities in a single call to SaveChanges.</span></span> <span data-ttu-id="8a46d-277">Tous les types utilisés pour les propriétés de clé primaire, secondaire ou étrangère, ainsi que ceux utilisés pour les index uniques, doivent implémenter <xref:System.IComparable%601> et <xref:System.IEquatable%601> .</span><span class="sxs-lookup"><span data-stu-id="8a46d-277">All types used for primary, alternate, or foreign key properties, as well as those used for unique indexes, must implement <xref:System.IComparable%601> and <xref:System.IEquatable%601>.</span></span> <span data-ttu-id="8a46d-278">Les types habituellement utilisés en tant que clés (int, Guid, String, etc.) prennent déjà en charge ces interfaces.</span><span class="sxs-lookup"><span data-stu-id="8a46d-278">Types normally used as keys (int, Guid, string, etc.) already support these interfaces.</span></span> <span data-ttu-id="8a46d-279">Les types de clé personnalisés peuvent ajouter ces interfaces.</span><span class="sxs-lookup"><span data-stu-id="8a46d-279">Custom key types may to add these interfaces.</span></span>
