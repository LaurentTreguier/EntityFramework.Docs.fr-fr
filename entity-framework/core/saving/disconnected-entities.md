---
title: 'Entités déconnectées : EF Core'
description: Utilisation d’entités déconnectées et non suivies sur plusieurs instances de contexte dans Entity Framework Core
author: ajcvickers
ms.author: avickers
ms.date: 10/27/2016
ms.assetid: 2533b195-d357-4056-b0e0-8698971bc3b0
uid: core/saving/disconnected-entities
ms.openlocfilehash: 52ba838c4d54771c51737c3940e5a88659f94144
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617270"
---
# <a name="disconnected-entities"></a><span data-ttu-id="95f26-103">Entités déconnectées</span><span class="sxs-lookup"><span data-stu-id="95f26-103">Disconnected entities</span></span>

<span data-ttu-id="95f26-104">Une instance de DbContext suit automatiquement les entités retournées à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="95f26-104">A DbContext instance will automatically track entities returned from the database.</span></span> <span data-ttu-id="95f26-105">Les modifications apportées à ces entités seront ensuite détectées lorsque SaveChanges est appelé, et la base de données sera mise à jour en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="95f26-105">Changes made to these entities will then be detected when SaveChanges is called and the database will be updated as needed.</span></span> <span data-ttu-id="95f26-106">Consultez [Enregistrement de base](xref:core/saving/basic) et [Données associées](xref:core/saving/related-data) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="95f26-106">See [Basic Save](xref:core/saving/basic) and [Related Data](xref:core/saving/related-data) for details.</span></span>

<span data-ttu-id="95f26-107">Toutefois, parfois, les entités sont interrogées par une instance de contexte, puis enregistrées à l’aide d’une autre instance.</span><span class="sxs-lookup"><span data-stu-id="95f26-107">However, sometimes entities are queried using one context instance and then saved using a different instance.</span></span> <span data-ttu-id="95f26-108">Cela se produit souvent dans les scénarios « déconnectés », par exemple une application web où les entités sont interrogées, envoyées au client, modifiées, envoyées sur le serveur dans une demande et puis enregistrées.</span><span class="sxs-lookup"><span data-stu-id="95f26-108">This often happens in "disconnected" scenarios such as a web application where the entities are queried, sent to the client, modified, sent back to the server in a request, and then saved.</span></span> <span data-ttu-id="95f26-109">Dans ce cas, la deuxième instance de contexte doit savoir si les entités sont nouvelles (doivent être insérées) ou existantes (doivent être mises à jour).</span><span class="sxs-lookup"><span data-stu-id="95f26-109">In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).</span></span>

<!-- markdownlint-disable MD028 -->
> [!TIP]
> <span data-ttu-id="95f26-110">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="95f26-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) on GitHub.</span></span>

> [!TIP]
> <span data-ttu-id="95f26-111">EF Core peut suivre une seule instance d’une entité avec une valeur de clé primaire donnée.</span><span class="sxs-lookup"><span data-stu-id="95f26-111">EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="95f26-112">La meilleure façon d’éviter ce problème consiste à utiliser un contexte de courte durée de vie pour chaque unité de travail, de sorte que le contexte commence vide, ait des entités associées, enregistre ces entités, puis soit supprimé.</span><span class="sxs-lookup"><span data-stu-id="95f26-112">The best way to avoid this being an issue is to use a short-lived context for each unit-of-work such that the context starts empty, has entities attached to it, saves those entities, and then the context is disposed and discarded.</span></span>
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a><span data-ttu-id="95f26-113">Identification des nouvelles entités</span><span class="sxs-lookup"><span data-stu-id="95f26-113">Identifying new entities</span></span>

### <a name="client-identifies-new-entities"></a><span data-ttu-id="95f26-114">Le client identifie de nouvelles entités</span><span class="sxs-lookup"><span data-stu-id="95f26-114">Client identifies new entities</span></span>

<span data-ttu-id="95f26-115">Le cas le plus simple à gérer est lorsque le client indique au serveur si l’entité est nouvelle ou existante.</span><span class="sxs-lookup"><span data-stu-id="95f26-115">The simplest case to deal with is when the client informs the server whether the entity is new or existing.</span></span> <span data-ttu-id="95f26-116">Par exemple, la requête d’insertion d’une nouvelle entité diffère souvent de la requête pour mettre à jour une entité existante.</span><span class="sxs-lookup"><span data-stu-id="95f26-116">For example, often the request to insert a new entity is different from the request to update an existing entity.</span></span>

<span data-ttu-id="95f26-117">Le reste de cette section couvre les cas où il est nécessaire de déterminer, d’une autre manière, s’il faut insérer ou mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="95f26-117">The remainder of this section covers the cases where it necessary to determine in some other way whether to insert or update.</span></span>

### <a name="with-auto-generated-keys"></a><span data-ttu-id="95f26-118">Avec des clés générées automatiquement</span><span class="sxs-lookup"><span data-stu-id="95f26-118">With auto-generated keys</span></span>

<span data-ttu-id="95f26-119">La valeur d’une clé générée automatiquement peut souvent être utilisée pour déterminer si une entité doit être insérée ou mise à jour.</span><span class="sxs-lookup"><span data-stu-id="95f26-119">The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.</span></span> <span data-ttu-id="95f26-120">Si la clé n’a pas été définie (autrement dit si elle a toujours la valeur CLR par défaut de null, zéro, etc.), alors l’entité doit être nouvelle et a besoin d’être insérée.</span><span class="sxs-lookup"><span data-stu-id="95f26-120">If the key has not been set (that is, it still has the CLR default value of null, zero, etc.), then the entity must be new and needs inserting.</span></span> <span data-ttu-id="95f26-121">En revanche, si la valeur de clé a été définie, l’entité doit avoir déjà été précédemment enregistrée et doit être mise à jour.</span><span class="sxs-lookup"><span data-stu-id="95f26-121">On the other hand, if the key value has been set, then it must have already been previously saved and now needs updating.</span></span> <span data-ttu-id="95f26-122">En d’autres termes, si la clé a une valeur, cette entité a été interrogée, envoyée au client et est maintenant revenue pour être mise à jour.</span><span class="sxs-lookup"><span data-stu-id="95f26-122">In other words, if the key has a value, then the entity was queried, sent to the client, and has now come back to be updated.</span></span>

<span data-ttu-id="95f26-123">Il est facile de rechercher une clé non définie lorsque le type d’entité est connu :</span><span class="sxs-lookup"><span data-stu-id="95f26-123">It is easy to check for an unset key when the entity type is known:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

<span data-ttu-id="95f26-124">Toutefois, EF a également un moyen intégré de faire cela pour n’importe quel type d’entité et type de clé :</span><span class="sxs-lookup"><span data-stu-id="95f26-124">However, EF also has a built-in way to do this for any entity type and key type:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> <span data-ttu-id="95f26-125">Les clés sont définies dès que les entités sont suivies par le contexte, même si l’entité est dans l’état Ajouté.</span><span class="sxs-lookup"><span data-stu-id="95f26-125">Keys are set as soon as entities are tracked by the context, even if the entity is in the Added state.</span></span> <span data-ttu-id="95f26-126">Cela est utile lors du parcours d’un graphique d’entités pour décider quelles actions effectuer avec chacune, par exemple lors de l’utilisation de l’API TrackGraph.</span><span class="sxs-lookup"><span data-stu-id="95f26-126">This helps when traversing a graph of entities and deciding what to do with each, such as when using the TrackGraph API.</span></span> <span data-ttu-id="95f26-127">La valeur de clé doit uniquement être utilisée de la manière illustrée ici _avant_ qu’un appel soit envoyé pour effectuer le suivi de l’entité.</span><span class="sxs-lookup"><span data-stu-id="95f26-127">The key value should only be used in the way shown here _before_ any call is made to track the entity.</span></span>

### <a name="with-other-keys"></a><span data-ttu-id="95f26-128">Avec d’autres clés</span><span class="sxs-lookup"><span data-stu-id="95f26-128">With other keys</span></span>

<span data-ttu-id="95f26-129">Un autre mécanisme est nécessaire pour identifier les nouvelles entités lorsque les valeurs des clés ne sont pas générées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="95f26-129">Some other mechanism is needed to identify new entities when key values are not generated automatically.</span></span> <span data-ttu-id="95f26-130">Il existe deux approches générales pour cela :</span><span class="sxs-lookup"><span data-stu-id="95f26-130">There are two general approaches to this:</span></span>

* <span data-ttu-id="95f26-131">Requête pour l'entité</span><span class="sxs-lookup"><span data-stu-id="95f26-131">Query for the entity</span></span>
* <span data-ttu-id="95f26-132">Passez un indicateur à partir du client</span><span class="sxs-lookup"><span data-stu-id="95f26-132">Pass a flag from the client</span></span>

<span data-ttu-id="95f26-133">Pour rechercher l’entité, utilisez simplement la méthode Find :</span><span class="sxs-lookup"><span data-stu-id="95f26-133">To query for the entity, just use the Find method:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

<span data-ttu-id="95f26-134">L’affichage du code complet pour le passage d’un indicateur depuis un client n’entre pas dans la portée de ce document.</span><span class="sxs-lookup"><span data-stu-id="95f26-134">It is beyond the scope of this document to show the full code for passing a flag from a client.</span></span> <span data-ttu-id="95f26-135">Dans une application web, cela consiste généralement à effectuer des demandes différentes pour différentes actions, ou à passer un état dans la demande, puis l’extraire dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="95f26-135">In a web app, it usually means making different requests for different actions, or passing some state in the request then extracting it in the controller.</span></span>

## <a name="saving-single-entities"></a><span data-ttu-id="95f26-136">Enregistrement d’entités uniques</span><span class="sxs-lookup"><span data-stu-id="95f26-136">Saving single entities</span></span>

<span data-ttu-id="95f26-137">Si on sait si une insertion ou une mise à jour est nécessaire, vous pouvez ajouter Add ou Update de manière appropriée :</span><span class="sxs-lookup"><span data-stu-id="95f26-137">If it is known whether or not an insert or update is needed, then either Add or Update can be used appropriately:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

<span data-ttu-id="95f26-138">Toutefois, si l’entité utilise les valeurs de clé générées automatiquement, la méthode Update peut être utilisée pour les deux cas :</span><span class="sxs-lookup"><span data-stu-id="95f26-138">However, if the entity uses auto-generated key values, then the Update method can be used for both cases:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

<span data-ttu-id="95f26-139">Normalement, la méthode Update marque l’entité pour la mise à jour, et non l’insertion.</span><span class="sxs-lookup"><span data-stu-id="95f26-139">The Update method normally marks the entity for update, not insert.</span></span> <span data-ttu-id="95f26-140">Toutefois, si l’entité a une clé générée automatiquement, et qu’aucune valeur de clé n’a été définie, l’entité est automatiquement marquée pour insertion.</span><span class="sxs-lookup"><span data-stu-id="95f26-140">However, if the entity has a auto-generated key, and no key value has been set, then the entity is instead automatically marked for insert.</span></span>

> [!TIP]  
> <span data-ttu-id="95f26-141">Ce comportement a été introduit dans EF Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="95f26-141">This behavior was introduced in EF Core 2.0.</span></span> <span data-ttu-id="95f26-142">Pour les versions antérieures, il est toujours nécessaire de choisir explicitement Add ou Update.</span><span class="sxs-lookup"><span data-stu-id="95f26-142">For earlier releases it is always necessary to explicitly choose either Add or Update.</span></span>

<span data-ttu-id="95f26-143">Si l’entité n’utilise pas les clés générées automatiquement, l’application doit décider si l’entité doit être insérée ou mise à jour. Par exemple :</span><span class="sxs-lookup"><span data-stu-id="95f26-143">If the entity is not using auto-generated keys, then the application must decide whether the entity should be inserted or updated: For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

<span data-ttu-id="95f26-144">Les étapes ici sont :</span><span class="sxs-lookup"><span data-stu-id="95f26-144">The steps here are:</span></span>

* <span data-ttu-id="95f26-145">Si Find retourne null, la base de données ne contient pas encore le blog avec cet ID, nous appelons donc Add pour le marquer pour insertion.</span><span class="sxs-lookup"><span data-stu-id="95f26-145">If Find returns null, then the database doesn't already contain the blog with this ID, so we call Add mark it for insertion.</span></span>
* <span data-ttu-id="95f26-146">Si la recherche retourne une entité, il existe dans la base de données et le contexte suit maintenant l’entité existante</span><span class="sxs-lookup"><span data-stu-id="95f26-146">If Find returns an entity, then it exists in the database and the context is now tracking the existing entity</span></span>
  * <span data-ttu-id="95f26-147">Ensuite, nous utilisons SetValues pour définir les valeurs de toutes les propriétés de cette entité sur celles envoyées par le client.</span><span class="sxs-lookup"><span data-stu-id="95f26-147">We then use SetValues to set the values for all properties on this entity to those that came from the client.</span></span>
  * <span data-ttu-id="95f26-148">L’appel à SetValues marque l’entité à mettre à jour en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="95f26-148">The SetValues call will mark the entity to be updated as needed.</span></span>

> [!TIP]  
> <span data-ttu-id="95f26-149">SetValues marque uniquement comme modifiées les propriétés qui ont des valeurs différentes de celles de l’entité suivie.</span><span class="sxs-lookup"><span data-stu-id="95f26-149">SetValues will only mark as modified the properties that have different values to those in the tracked entity.</span></span> <span data-ttu-id="95f26-150">Cela signifie que lorsque la mise à jour est envoyée, seules les colonnes qui ont été modifiées seront mises à jour.</span><span class="sxs-lookup"><span data-stu-id="95f26-150">This means that when the update is sent, only those columns that have actually changed will be updated.</span></span> <span data-ttu-id="95f26-151">(Et si rien n’a changé, alors aucune mise à jour ne sera envoyée du tout).</span><span class="sxs-lookup"><span data-stu-id="95f26-151">(And if nothing has changed, then no update will be sent at all.)</span></span>

## <a name="working-with-graphs"></a><span data-ttu-id="95f26-152">Travail avec les graphiques</span><span class="sxs-lookup"><span data-stu-id="95f26-152">Working with graphs</span></span>

### <a name="identity-resolution"></a><span data-ttu-id="95f26-153">Résolution de l'identité</span><span class="sxs-lookup"><span data-stu-id="95f26-153">Identity resolution</span></span>

<span data-ttu-id="95f26-154">Comme indiqué ci-dessus, EF Core peut suivre une seule instance d’une entité avec une valeur de clé primaire donnée.</span><span class="sxs-lookup"><span data-stu-id="95f26-154">As noted above, EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="95f26-155">Lorsque vous travaillez avec des graphiques, le graphique doit idéalement être créé de sorte que cet invariant est géré, et le contexte doit être utilisé pour une seule unité de travail.</span><span class="sxs-lookup"><span data-stu-id="95f26-155">When working with graphs the graph should ideally be created such that this invariant is maintained, and the context should be used for only one unit-of-work.</span></span> <span data-ttu-id="95f26-156">Si le graphique contient des doublons, il sera nécessaire de traiter le graphique avant de l’envoyer à EF pour consolider les instances multiples en une seule.</span><span class="sxs-lookup"><span data-stu-id="95f26-156">If the graph does contain duplicates, then it will be necessary to process the graph before sending it to EF to consolidate multiple instances into one.</span></span> <span data-ttu-id="95f26-157">Cela peut être difficile lorsque les instances ont des valeurs et relations en conflit. La consolidation des doublons doit donc être effectuée dès que possible dans le pipeline de votre application afin d’éviter la résolution des conflits.</span><span class="sxs-lookup"><span data-stu-id="95f26-157">This may not be trivial where instances have conflicting values and relationships, so consolidating duplicates should be done as soon as possible in your application pipeline to avoid conflict resolution.</span></span>

### <a name="all-newall-existing-entities"></a><span data-ttu-id="95f26-158">Toutes les entités nouvelles/toutes les entités existantes</span><span class="sxs-lookup"><span data-stu-id="95f26-158">All new/all existing entities</span></span>

<span data-ttu-id="95f26-159">Un exemple d’utilisation des graphiques est l’insertion ou la mise à jour d’un blog ainsi que de sa collection de billets associés.</span><span class="sxs-lookup"><span data-stu-id="95f26-159">An example of working with graphs is inserting or updating a blog together with its collection of associated posts.</span></span> <span data-ttu-id="95f26-160">Si toutes les entités dans le graphique doivent être insérées, ou toutes doivent être mises à jour, le processus est identique à celui décrit ci-dessus pour les entités uniques.</span><span class="sxs-lookup"><span data-stu-id="95f26-160">If all the entities in the graph should be inserted, or all should be updated, then the process is the same as described above for single entities.</span></span> <span data-ttu-id="95f26-161">Par exemple, un graphique de blogs et publications créé comme suit :</span><span class="sxs-lookup"><span data-stu-id="95f26-161">For example, a graph of blogs and posts created like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

<span data-ttu-id="95f26-162">peut être inséré comme suit :</span><span class="sxs-lookup"><span data-stu-id="95f26-162">can be inserted like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

<span data-ttu-id="95f26-163">L’appel à Add marque le blog et tous les billets à insérer.</span><span class="sxs-lookup"><span data-stu-id="95f26-163">The call to Add will mark the blog and all the posts to be inserted.</span></span>

<span data-ttu-id="95f26-164">De même, si toutes les entités dans un graphique doivent être mises à jour, alors Update peut être utilisé :</span><span class="sxs-lookup"><span data-stu-id="95f26-164">Likewise, if all the entities in a graph need to be updated, then Update can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

<span data-ttu-id="95f26-165">Le blog et tous ses billets seront marqués pour être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="95f26-165">The blog and all its posts will be marked to be updated.</span></span>

### <a name="mix-of-new-and-existing-entities"></a><span data-ttu-id="95f26-166">Combinaison d’entités nouvelles et existantes</span><span class="sxs-lookup"><span data-stu-id="95f26-166">Mix of new and existing entities</span></span>

<span data-ttu-id="95f26-167">Avec les clés générées automatiquement, Update est de nouveau utilisable à la fois pour les insertions et pour les mises à jour, même si le graphique contient un mélange d’entités qui nécessitent l’insertion et d’autres qui nécessitent la mise à jour :</span><span class="sxs-lookup"><span data-stu-id="95f26-167">With auto-generated keys, Update can again be used for both inserts and updates, even if the graph contains a mix of entities that require inserting and those that require updating:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

<span data-ttu-id="95f26-168">Update marque une entité dans le graphique, le blog ou un billet, pour insertion si elle ne dispose pas d’un ensemble clé-valeur, tandis que toutes les autres entités sont marquées pour mise à jour.</span><span class="sxs-lookup"><span data-stu-id="95f26-168">Update will mark any entity in the graph, blog or post, for insertion if it does not have a key value set, while all other entities are marked for update.</span></span>

<span data-ttu-id="95f26-169">Comme précédemment, lorsque vous n’utilisez pas les clés générées automatiquement, vous pouvez utiliser une requête et un traitement :</span><span class="sxs-lookup"><span data-stu-id="95f26-169">As before, when not using auto-generated keys, a query and some processing can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a><span data-ttu-id="95f26-170">Gestion des suppressions</span><span class="sxs-lookup"><span data-stu-id="95f26-170">Handling deletes</span></span>

<span data-ttu-id="95f26-171">La suppression peut être compliquée à gérer, car souvent l’absence d’une entité signifie qu’elle droit être supprimée.</span><span class="sxs-lookup"><span data-stu-id="95f26-171">Delete can be tricky to handle since often the absence of an entity means that it should be deleted.</span></span> <span data-ttu-id="95f26-172">Une façon de gérer cela consiste est d’utiliser des « suppressions récupérables » par exemple en marquant l’entité comme supprimée plutôt que la supprimer réellement.</span><span class="sxs-lookup"><span data-stu-id="95f26-172">One way to deal with this is to use "soft deletes" such that the entity is marked as deleted rather than actually being deleted.</span></span> <span data-ttu-id="95f26-173">Les suppressions s’apparentent alors à des mises à jour.</span><span class="sxs-lookup"><span data-stu-id="95f26-173">Deletes then becomes the same as updates.</span></span> <span data-ttu-id="95f26-174">Les suppressions récupérables peuvent être implémentées à l’aide de [filtres de requête](xref:core/querying/filters).</span><span class="sxs-lookup"><span data-stu-id="95f26-174">Soft deletes can be implemented in using [query filters](xref:core/querying/filters).</span></span>

<span data-ttu-id="95f26-175">Pour les vraies suppressions, il est courant d’utiliser une extension du modèle de requête pour effectuer ce qui est essentiellement une comparaison de graphique.</span><span class="sxs-lookup"><span data-stu-id="95f26-175">For true deletes, a common pattern is to use an extension of the query pattern to perform what is essentially a graph diff.</span></span> <span data-ttu-id="95f26-176">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="95f26-176">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a><span data-ttu-id="95f26-177">TrackGraph</span><span class="sxs-lookup"><span data-stu-id="95f26-177">TrackGraph</span></span>

<span data-ttu-id="95f26-178">En interne, Add, Attach et Update utilisent la traversée de graphique en déterminant pour chaque entité si elle doit être marquée comme Added (à insérer), Modified (à mettre à jour), Unchanged (ne rien faire), ou Deleted (à supprimer).</span><span class="sxs-lookup"><span data-stu-id="95f26-178">Internally, Add, Attach, and Update use graph-traversal with a determination made for each entity as to whether it should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).</span></span> <span data-ttu-id="95f26-179">Ce mécanisme est exposé via l’API TrackGraph.</span><span class="sxs-lookup"><span data-stu-id="95f26-179">This mechanism is exposed via the TrackGraph API.</span></span> <span data-ttu-id="95f26-180">Par exemple, supposons que, lorsque le client envoie un graphique d’entités, il définit certains indicateurs sur chaque entité indiquant comment elle doit être gérée.</span><span class="sxs-lookup"><span data-stu-id="95f26-180">For example, let's assume that when the client sends back a graph of entities it sets some flag on each entity indicating how it should be handled.</span></span> <span data-ttu-id="95f26-181">TrackGraph peut ensuite être utilisé pour traiter cet indicateur :</span><span class="sxs-lookup"><span data-stu-id="95f26-181">TrackGraph can then be used to process this flag:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

<span data-ttu-id="95f26-182">Les indicateurs sont uniquement affichés dans le cadre de l’entité pour simplifier l’exemple.</span><span class="sxs-lookup"><span data-stu-id="95f26-182">The flags are only shown as part of the entity for simplicity of the example.</span></span> <span data-ttu-id="95f26-183">En général, les indicateurs feraient partie d’un DTO ou d’un autre état inclus dans la demande.</span><span class="sxs-lookup"><span data-stu-id="95f26-183">Typically the flags would be part of a DTO or some other state included in the request.</span></span>
