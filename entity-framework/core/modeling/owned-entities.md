---
title: Types d’entités détenues-EF Core
description: Comment configurer des agrégats ou des types d’entités détenus lors de l’utilisation de Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 36f756b70c9ad1727c48b5c789fd324c9dc6cd29
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429435"
---
# <a name="owned-entity-types"></a><span data-ttu-id="3c807-103">Types d’entité détenus</span><span class="sxs-lookup"><span data-stu-id="3c807-103">Owned Entity Types</span></span>

<span data-ttu-id="3c807-104">EF Core vous permet de modéliser des types d’entité qui peuvent uniquement apparaître dans les propriétés de navigation d’autres types d’entités.</span><span class="sxs-lookup"><span data-stu-id="3c807-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="3c807-105">Il s’agit de _types d’entités détenues_.</span><span class="sxs-lookup"><span data-stu-id="3c807-105">These are called _owned entity types_.</span></span> <span data-ttu-id="3c807-106">L’entité contenant un type d’entité détenu est son _propriétaire_.</span><span class="sxs-lookup"><span data-stu-id="3c807-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="3c807-107">Les entités détenues sont essentiellement une partie du propriétaire et ne peuvent pas exister sans elles, elles sont conceptuellement similaires aux [agrégats](https://martinfowler.com/bliki/DDD_Aggregate.html).</span><span class="sxs-lookup"><span data-stu-id="3c807-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span> <span data-ttu-id="3c807-108">Cela signifie que l’entité détenue est par définition sur le côté dépendant de la relation avec le propriétaire.</span><span class="sxs-lookup"><span data-stu-id="3c807-108">This means that the owned entity is by definition on the dependent side of the relationship with the owner.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="3c807-109">Configuration explicite</span><span class="sxs-lookup"><span data-stu-id="3c807-109">Explicit configuration</span></span>

<span data-ttu-id="3c807-110">Les types d’entités détenus ne sont jamais inclus par EF Core dans le modèle par Convention.</span><span class="sxs-lookup"><span data-stu-id="3c807-110">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="3c807-111">Vous pouvez utiliser la `OwnsOne` méthode dans `OnModelCreating` ou annoter le type avec `OwnedAttribute` pour configurer le type en tant que type détenu.</span><span class="sxs-lookup"><span data-stu-id="3c807-111">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` to configure the type as an owned type.</span></span>

<span data-ttu-id="3c807-112">Dans cet exemple, `StreetAddress` est un type sans propriété d’identité.</span><span class="sxs-lookup"><span data-stu-id="3c807-112">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="3c807-113">Il est utilisé comme propriété du type Order pour spécifier l’adresse d’expédition d’une commande particulière.</span><span class="sxs-lookup"><span data-stu-id="3c807-113">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="3c807-114">Nous pouvons utiliser le `OwnedAttribute` pour le traiter en tant qu’entité possédée lorsqu’elle est référencée à partir d’un autre type d’entité :</span><span class="sxs-lookup"><span data-stu-id="3c807-114">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="3c807-115">Il est également possible d’utiliser la `OwnsOne` méthode dans `OnModelCreating` pour spécifier que la `ShippingAddress` propriété est une entité appartenant au `Order` type d’entité et pour configurer des facettes supplémentaires, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="3c807-115">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="3c807-116">Si la `ShippingAddress` propriété est privée dans le `Order` type, vous pouvez utiliser la version de chaîne de la `OwnsOne` méthode :</span><span class="sxs-lookup"><span data-stu-id="3c807-116">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="3c807-117">Le modèle ci-dessus est mappé au schéma de base de données suivant :</span><span class="sxs-lookup"><span data-stu-id="3c807-117">The model above is mapped to the following database schema:</span></span>

![Sceenshot du modèle de base de données pour l’entité contenant la référence d’appartenance](_static/owned-entities-ownsone.png)

<span data-ttu-id="3c807-119">Pour plus de contexte, consultez l' [exemple de projet complet](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) .</span><span class="sxs-lookup"><span data-stu-id="3c807-119">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

> [!TIP]
> <span data-ttu-id="3c807-120">Le type d’entité détenu peut être marqué comme requis, voir les [dépendants un-à-un requis](xref:core/modeling/relationships#one-to-one) pour plus d’informations.</span><span class="sxs-lookup"><span data-stu-id="3c807-120">The owned entity type can be marked as required, see [Required one-to-one dependents](xref:core/modeling/relationships#one-to-one) for more information.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="3c807-121">Clés implicites</span><span class="sxs-lookup"><span data-stu-id="3c807-121">Implicit keys</span></span>

<span data-ttu-id="3c807-122">Les types détenus configurés `OwnsOne` ou découverts via une navigation de référence ont toujours une relation un-à-un avec le propriétaire, donc ils n’ont pas besoin de leurs propres valeurs de clés, car les valeurs de clé étrangère sont uniques.</span><span class="sxs-lookup"><span data-stu-id="3c807-122">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="3c807-123">Dans l’exemple précédent, le `StreetAddress` type n’a pas besoin de définir une propriété de clé.</span><span class="sxs-lookup"><span data-stu-id="3c807-123">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="3c807-124">Pour comprendre comment EF Core effectue le suivi de ces objets, il est utile de savoir qu’une clé primaire est créée en tant que [propriété Shadow](xref:core/modeling/shadow-properties) pour le type détenu.</span><span class="sxs-lookup"><span data-stu-id="3c807-124">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="3c807-125">La valeur de la clé d’une instance du type détenu sera identique à la valeur de la clé de l’instance propriétaire.</span><span class="sxs-lookup"><span data-stu-id="3c807-125">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="3c807-126">Collections de types détenus</span><span class="sxs-lookup"><span data-stu-id="3c807-126">Collections of owned types</span></span>

<span data-ttu-id="3c807-127">Pour configurer une collection de types détenus `OwnsMany` , utilisez dans `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="3c807-127">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="3c807-128">Les types détenus nécessitent une clé primaire.</span><span class="sxs-lookup"><span data-stu-id="3c807-128">Owned types need a primary key.</span></span> <span data-ttu-id="3c807-129">S’il n’existe aucune propriété candidate correcte sur le type .NET, EF Core pouvez essayer d’en créer un.</span><span class="sxs-lookup"><span data-stu-id="3c807-129">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="3c807-130">Toutefois, lorsque des types détenus sont définis par le biais d’une collection, il suffit de créer une propriété Shadow pour agir à la fois comme clé étrangère dans le propriétaire et la clé primaire de l’instance appartenant, comme c’est le cas pour `OwnsOne` : il peut y avoir plusieurs instances de type détenu pour chaque propriétaire, et la clé du propriétaire n’est donc pas suffisante pour fournir une identité unique pour chaque instance</span><span class="sxs-lookup"><span data-stu-id="3c807-130">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="3c807-131">Les deux solutions les plus simples à ce niveau sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="3c807-131">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="3c807-132">Définition d’une clé primaire de substitution sur une nouvelle propriété indépendante de la clé étrangère qui pointe vers le propriétaire.</span><span class="sxs-lookup"><span data-stu-id="3c807-132">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="3c807-133">Les valeurs contenues doivent être uniques parmi tous les propriétaires (par exemple, si le parent {1} a {1} un enfant, alors {2} le parent ne peut pas avoir {1} d’enfant), de sorte que la valeur n’a pas de signification inhérente.</span><span class="sxs-lookup"><span data-stu-id="3c807-133">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="3c807-134">Étant donné que la clé étrangère ne fait pas partie de la clé primaire, ses valeurs peuvent être modifiées. vous pouvez donc déplacer un enfant d’un parent à un autre, mais cela est généralement dû à une sémantique d’agrégation.</span><span class="sxs-lookup"><span data-stu-id="3c807-134">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="3c807-135">En utilisant la clé étrangère et une propriété supplémentaire comme clé composite.</span><span class="sxs-lookup"><span data-stu-id="3c807-135">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="3c807-136">La valeur de propriété supplémentaire ne doit désormais être unique que pour un parent donné (par conséquent, si le parent a un enfant, le {1} {1,1} parent {2} peut encore avoir un enfant {2,1} ).</span><span class="sxs-lookup"><span data-stu-id="3c807-136">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="3c807-137">En faisant de la clé étrangère de la clé primaire, la relation entre le propriétaire et l’entité détenue devient immuable et reflète mieux la sémantique d’agrégation.</span><span class="sxs-lookup"><span data-stu-id="3c807-137">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="3c807-138">C’est ce que EF Core par défaut.</span><span class="sxs-lookup"><span data-stu-id="3c807-138">This is what EF Core does by default.</span></span>

<span data-ttu-id="3c807-139">Dans cet exemple, nous allons utiliser la `Distributor` classe.</span><span class="sxs-lookup"><span data-stu-id="3c807-139">In this example we'll use the `Distributor` class.</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="3c807-140">Par défaut, la clé primaire utilisée pour le type détenu référencé via la `ShippingCenters` propriété de navigation sera `("DistributorId", "Id")` where `"DistributorId"` et `"Id"` est une `int` valeur unique.</span><span class="sxs-lookup"><span data-stu-id="3c807-140">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="3c807-141">Pour configurer un autre appel de clé primaire `HasKey` .</span><span class="sxs-lookup"><span data-stu-id="3c807-141">To configure a different primary key call `HasKey`.</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

<span data-ttu-id="3c807-142">Le modèle ci-dessus est mappé au schéma de base de données suivant :</span><span class="sxs-lookup"><span data-stu-id="3c807-142">The model above is mapped to the following database schema:</span></span>

![Sceenshot du modèle de base de données pour l’entité contenant la collection détenue](_static/owned-entities-ownsmany.png)

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="3c807-144">Mappage de types détenus avec le fractionnement de table</span><span class="sxs-lookup"><span data-stu-id="3c807-144">Mapping owned types with table splitting</span></span>

<span data-ttu-id="3c807-145">Lorsque vous utilisez des bases de données relationnelles, par défaut, les types appartenant à la référence sont mappés à la même table que le propriétaire.</span><span class="sxs-lookup"><span data-stu-id="3c807-145">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="3c807-146">Cela nécessite le fractionnement de la table en deux : certaines colonnes seront utilisées pour stocker les données du propriétaire, et certaines colonnes seront utilisées pour stocker les données de l’entité détenue.</span><span class="sxs-lookup"><span data-stu-id="3c807-146">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="3c807-147">Il s’agit d’une fonctionnalité courante connue sous le nom de [fractionnement de table](xref:core/modeling/table-splitting).</span><span class="sxs-lookup"><span data-stu-id="3c807-147">This is a common feature known as [table splitting](xref:core/modeling/table-splitting).</span></span>

<span data-ttu-id="3c807-148">Par défaut, EF Core nommera les colonnes de base de données pour les propriétés du type d’entité détenu, en suivant le modèle _Navigation_OwnedEntityProperty_.</span><span class="sxs-lookup"><span data-stu-id="3c807-148">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="3c807-149">Par conséquent, les `StreetAddress` Propriétés s’affichent dans la table Orders avec les noms « ShippingAddress_Street » et « ShippingAddress_City ».</span><span class="sxs-lookup"><span data-stu-id="3c807-149">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="3c807-150">Vous pouvez utiliser la `HasColumnName` méthode pour renommer ces colonnes.</span><span class="sxs-lookup"><span data-stu-id="3c807-150">You can use the `HasColumnName` method to rename those columns.</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> <span data-ttu-id="3c807-151">La plupart des méthodes de configuration de type d’entité normales telles que [ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) peuvent être appelées de la même façon.</span><span class="sxs-lookup"><span data-stu-id="3c807-151">Most of the normal entity type configuration methods like [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) can be called in the same way.</span></span>

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="3c807-152">Partage du même type .NET entre plusieurs types détenus</span><span class="sxs-lookup"><span data-stu-id="3c807-152">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="3c807-153">Un type d’entité détenu peut être du même type .NET qu’un autre type d’entité détenu. par conséquent, le type .NET peut ne pas être suffisant pour identifier un type détenu.</span><span class="sxs-lookup"><span data-stu-id="3c807-153">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="3c807-154">Dans ce cas, la propriété qui pointe du propriétaire vers l’entité détenue devient la définition de la _navigation_ du type d’entité détenu.</span><span class="sxs-lookup"><span data-stu-id="3c807-154">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="3c807-155">Du point de vue de EF Core, la navigation de définition fait partie de l’identité du type en même temps que le type .NET.</span><span class="sxs-lookup"><span data-stu-id="3c807-155">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="3c807-156">Par exemple, dans la classe suivante `ShippingAddress` et `BillingAddress` sont tous deux du même type .net, `StreetAddress` .</span><span class="sxs-lookup"><span data-stu-id="3c807-156">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="3c807-157">Pour comprendre comment EF Core doit distinguer les instances suivies de ces objets, il peut être utile de penser que la navigation de définition est devenue partie intégrante de la clé de l’instance parallèlement à la valeur de la clé du propriétaire et du type .NET du type détenu.</span><span class="sxs-lookup"><span data-stu-id="3c807-157">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="3c807-158">Types détenus imbriqués</span><span class="sxs-lookup"><span data-stu-id="3c807-158">Nested owned types</span></span>

<span data-ttu-id="3c807-159">Dans cet exemple `OrderDetails` `BillingAddress` , est propriétaire de et `ShippingAddress` , qui sont tous deux des `StreetAddress` types.</span><span class="sxs-lookup"><span data-stu-id="3c807-159">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="3c807-160">`OrderDetails` est alors détenu par le type `DetailedOrder`.</span><span class="sxs-lookup"><span data-stu-id="3c807-160">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="3c807-161">Chaque navigation vers un type détenu définit un type d’entité distinct avec une configuration totalement indépendante.</span><span class="sxs-lookup"><span data-stu-id="3c807-161">Each navigation to an owned type defines a separate entity type with completely independent configuration.</span></span>

<span data-ttu-id="3c807-162">En plus des types détenus imbriqués, un type détenu peut faire référence à une entité normale qui peut être soit le propriétaire, soit une entité différente tant que l’entité détenue est sur le côté dépendant.</span><span class="sxs-lookup"><span data-stu-id="3c807-162">In addition to nested owned types, an owned type can reference a regular entity which can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="3c807-163">Cette fonctionnalité définit les types d’entités détenues, à l’exception des types complexes dans EF6.</span><span class="sxs-lookup"><span data-stu-id="3c807-163">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a><span data-ttu-id="3c807-164">Configuration de types détenus</span><span class="sxs-lookup"><span data-stu-id="3c807-164">Configuring Owned Types</span></span>

<span data-ttu-id="3c807-165">Il est possible de chaîner la `OwnsOne` méthode dans un appel Fluent pour configurer ce modèle :</span><span class="sxs-lookup"><span data-stu-id="3c807-165">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="3c807-166">Notez l' `WithOwner` appel utilisé pour définir la propriété de navigation qui pointe vers le propriétaire.</span><span class="sxs-lookup"><span data-stu-id="3c807-166">Notice the `WithOwner` call used to define the navigation property pointing back at the owner.</span></span> <span data-ttu-id="3c807-167">Pour définir une navigation vers le type d’entité propriétaire qui ne fait pas partie de la relation de propriété, `WithOwner()` il est recommandé d’appeler sans argument.</span><span class="sxs-lookup"><span data-stu-id="3c807-167">To define a navigation to the owner entity type that's not part of the ownership relationship `WithOwner()` should be called without any arguments.</span></span>

<span data-ttu-id="3c807-168">Il est également possible d’obtenir ce résultat à l’aide `OwnedAttribute` de sur `OrderDetails` et `StreetAddress` .</span><span class="sxs-lookup"><span data-stu-id="3c807-168">It is also possible to achieve this result using `OwnedAttribute` on both `OrderDetails` and `StreetAddress`.</span></span>

<span data-ttu-id="3c807-169">Notez également l' `Navigation` appel.</span><span class="sxs-lookup"><span data-stu-id="3c807-169">In addition, notice the `Navigation` call.</span></span> <span data-ttu-id="3c807-170">Dans EFCore 5,0, les propriétés de navigation vers les types détenus peuvent être configurées de manière plus approfondie [comme pour les propriétés de navigation non détenues](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="3c807-170">In EFCore 5.0, navigation properties to owned types can be further configured [as for non-owned navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

<span data-ttu-id="3c807-171">Le modèle ci-dessus est mappé au schéma de base de données suivant :</span><span class="sxs-lookup"><span data-stu-id="3c807-171">The model above is mapped to the following database schema:</span></span>

![Sceenshot du modèle de base de données pour l’entité contenant des références d’appartenance imbriquées](_static/owned-entities-nested.png)

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="3c807-173">Stockage des types détenus dans des tables distinctes</span><span class="sxs-lookup"><span data-stu-id="3c807-173">Storing owned types in separate tables</span></span>

<span data-ttu-id="3c807-174">Contrairement aux types complexes EF6, les types détenus peuvent être stockés dans une table distincte du propriétaire.</span><span class="sxs-lookup"><span data-stu-id="3c807-174">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="3c807-175">Pour remplacer la Convention qui mappe un type détenu à la même table que le propriétaire, vous pouvez simplement appeler `ToTable` et fournir un autre nom de table.</span><span class="sxs-lookup"><span data-stu-id="3c807-175">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="3c807-176">L’exemple suivant mappe `OrderDetails` et ses deux adresses à une table distincte de `DetailedOrder` :</span><span class="sxs-lookup"><span data-stu-id="3c807-176">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

<span data-ttu-id="3c807-177">Il est également possible d’utiliser `TableAttribute` pour accomplir cela, mais notez que cela échoue s’il existe plusieurs navigations dans le type détenu, car dans ce cas, plusieurs types d’entité sont mappés à la même table.</span><span class="sxs-lookup"><span data-stu-id="3c807-177">It is also possible to use the `TableAttribute` to accomplish this, but note that this would fail if there are multiple navigations to the owned type since in that case multiple entity types would be mapped to the same table.</span></span>

## <a name="querying-owned-types"></a><span data-ttu-id="3c807-178">Interrogation des types détenus</span><span class="sxs-lookup"><span data-stu-id="3c807-178">Querying owned types</span></span>

<span data-ttu-id="3c807-179">Quand le propriétaire fait l’objet d’une interrogation, les types détenus sont inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="3c807-179">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="3c807-180">Il n’est pas nécessaire d’utiliser la `Include` méthode, même si les types détenus sont stockés dans une table distincte.</span><span class="sxs-lookup"><span data-stu-id="3c807-180">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="3c807-181">En fonction du modèle décrit précédemment, la requête suivante est obtenue `Order` `OrderDetails` et les deux sont détenues `StreetAddresses` de la base de données :</span><span class="sxs-lookup"><span data-stu-id="3c807-181">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="3c807-182">Limites</span><span class="sxs-lookup"><span data-stu-id="3c807-182">Limitations</span></span>

<span data-ttu-id="3c807-183">Certaines de ces limitations sont essentielles à la façon dont les types d’entités détenus fonctionnent, mais d’autres sont des restrictions que nous pouvons être en mesure de supprimer dans les versions ultérieures :</span><span class="sxs-lookup"><span data-stu-id="3c807-183">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="3c807-184">Restrictions par conception</span><span class="sxs-lookup"><span data-stu-id="3c807-184">By-design restrictions</span></span>

- <span data-ttu-id="3c807-185">Vous ne pouvez pas créer un `DbSet<T>` pour un type détenu.</span><span class="sxs-lookup"><span data-stu-id="3c807-185">You cannot create a `DbSet<T>` for an owned type.</span></span>
- <span data-ttu-id="3c807-186">Vous ne pouvez pas appeler `Entity<T>()` avec un type détenu sur `ModelBuilder` .</span><span class="sxs-lookup"><span data-stu-id="3c807-186">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`.</span></span>
- <span data-ttu-id="3c807-187">Les instances de types d’entité possédées ne peuvent pas être partagées par plusieurs propriétaires (il s’agit d’un scénario connu pour les objets de valeur qui ne peuvent pas être implémentés à l’aide des types d’entité détenus).</span><span class="sxs-lookup"><span data-stu-id="3c807-187">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types).</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="3c807-188">Lacunes actuelles</span><span class="sxs-lookup"><span data-stu-id="3c807-188">Current shortcomings</span></span>

- <span data-ttu-id="3c807-189">Les types d’entités détenues ne peuvent pas avoir de hiérarchies d’héritage</span><span class="sxs-lookup"><span data-stu-id="3c807-189">Owned entity types cannot have inheritance hierarchies</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="3c807-190">Lacunes dans les versions précédentes</span><span class="sxs-lookup"><span data-stu-id="3c807-190">Shortcomings in previous versions</span></span>

- <span data-ttu-id="3c807-191">Dans EF Core 2. x, les navigations de référence vers les types d’entités détenus ne peuvent pas avoir la valeur null, sauf si elles sont explicitement mappées à une table distincte du propriétaire.</span><span class="sxs-lookup"><span data-stu-id="3c807-191">In EF Core 2.x reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner.</span></span>
- <span data-ttu-id="3c807-192">Dans EF Core 3. x, les colonnes des types d’entité détenus mappés à la même table que le propriétaire sont toujours marquées comme Nullable.</span><span class="sxs-lookup"><span data-stu-id="3c807-192">In EF Core 3.x the columns for owned entity types mapped to the same table as the owner are always marked as nullable.</span></span>
