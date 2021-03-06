---
title: Types d’entités détenues-EF Core
description: Comment configurer des agrégats ou des types d’entités détenus lors de l’utilisation de Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 4175c281254c25e957fd701c671f2d75c7789aab
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023950"
---
# <a name="owned-entity-types"></a>Types d’entité détenus

EF Core vous permet de modéliser des types d’entité qui peuvent uniquement apparaître dans les propriétés de navigation d’autres types d’entités. Il s’agit de _types d’entités détenues_. L’entité contenant un type d’entité détenu est son _propriétaire_.

Les entités détenues sont essentiellement une partie du propriétaire et ne peuvent pas exister sans elles, elles sont conceptuellement similaires aux [agrégats](https://martinfowler.com/bliki/DDD_Aggregate.html). Cela signifie que l’entité détenue est par définition sur le côté dépendant de la relation avec le propriétaire.

## <a name="explicit-configuration"></a>Configuration explicite

Les types d’entités détenus ne sont jamais inclus par EF Core dans le modèle par Convention. Vous pouvez utiliser la `OwnsOne` méthode dans `OnModelCreating` ou annoter le type avec `OwnedAttribute` pour configurer le type en tant que type détenu.

Dans cet exemple, `StreetAddress` est un type sans propriété d’identité. Il est utilisé comme propriété du type Order pour spécifier l’adresse d’expédition d’une commande particulière.

Nous pouvons utiliser le `OwnedAttribute` pour le traiter en tant qu’entité possédée lorsqu’elle est référencée à partir d’un autre type d’entité :

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

Il est également possible d’utiliser la `OwnsOne` méthode dans `OnModelCreating` pour spécifier que la `ShippingAddress` propriété est une entité appartenant au `Order` type d’entité et pour configurer des facettes supplémentaires, si nécessaire.

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

Si la `ShippingAddress` propriété est privée dans le `Order` type, vous pouvez utiliser la version de chaîne de la `OwnsOne` méthode :

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

Le modèle ci-dessus est mappé au schéma de base de données suivant :

![Sceenshot du modèle de base de données pour l’entité contenant la référence d’appartenance](_static/owned-entities-ownsone.png)

Pour plus de contexte, consultez l' [exemple de projet complet](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/OwnedEntities) .

> [!TIP]
> Le type d’entité détenu peut être marqué comme requis, voir les [dépendants un-à-un requis](xref:core/modeling/relationships#one-to-one) pour plus d’informations.

## <a name="implicit-keys"></a>Clés implicites

Les types détenus configurés `OwnsOne` ou découverts via une navigation de référence ont toujours une relation un-à-un avec le propriétaire, donc ils n’ont pas besoin de leurs propres valeurs de clés, car les valeurs de clé étrangère sont uniques. Dans l’exemple précédent, le `StreetAddress` type n’a pas besoin de définir une propriété de clé.

Pour comprendre comment EF Core effectue le suivi de ces objets, il est utile de savoir qu’une clé primaire est créée en tant que [propriété Shadow](xref:core/modeling/shadow-properties) pour le type détenu. La valeur de la clé d’une instance du type détenu sera identique à la valeur de la clé de l’instance propriétaire.

## <a name="collections-of-owned-types"></a>Collections de types détenus

Pour configurer une collection de types détenus `OwnsMany` , utilisez dans `OnModelCreating` .

Les types détenus nécessitent une clé primaire. S’il n’existe aucune propriété candidate correcte sur le type .NET, EF Core pouvez essayer d’en créer un. Toutefois, lorsque des types détenus sont définis par le biais d’une collection, il suffit de créer une propriété Shadow pour agir à la fois comme clé étrangère dans le propriétaire et la clé primaire de l’instance appartenant, comme c’est le cas pour `OwnsOne` : il peut y avoir plusieurs instances de type détenu pour chaque propriétaire, et la clé du propriétaire n’est donc pas suffisante pour fournir une identité unique pour chaque instance

Les deux solutions les plus simples à ce niveau sont les suivantes :

- Définition d’une clé primaire de substitution sur une nouvelle propriété indépendante de la clé étrangère qui pointe vers le propriétaire. Les valeurs contenues doivent être uniques parmi tous les propriétaires (par exemple, si le parent {1} a {1} un enfant, alors {2} le parent ne peut pas avoir {1} d’enfant), de sorte que la valeur n’a pas de signification inhérente. Étant donné que la clé étrangère ne fait pas partie de la clé primaire, ses valeurs peuvent être modifiées. vous pouvez donc déplacer un enfant d’un parent à un autre, mais cela est généralement dû à une sémantique d’agrégation.
- En utilisant la clé étrangère et une propriété supplémentaire comme clé composite. La valeur de propriété supplémentaire ne doit désormais être unique que pour un parent donné (par conséquent, si le parent a un enfant, le {1} {1,1} parent {2} peut encore avoir un enfant {2,1} ). En faisant de la clé étrangère de la clé primaire, la relation entre le propriétaire et l’entité détenue devient immuable et reflète mieux la sémantique d’agrégation. C’est ce que EF Core par défaut.

Dans cet exemple, nous allons utiliser la `Distributor` classe.

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

Par défaut, la clé primaire utilisée pour le type détenu référencé via la `ShippingCenters` propriété de navigation sera `("DistributorId", "Id")` where `"DistributorId"` et `"Id"` est une `int` valeur unique.

Pour configurer un autre appel de clé primaire `HasKey` .

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

Le modèle ci-dessus est mappé au schéma de base de données suivant :

![Sceenshot du modèle de base de données pour l’entité contenant la collection détenue](_static/owned-entities-ownsmany.png)

## <a name="mapping-owned-types-with-table-splitting"></a>Mappage de types détenus avec le fractionnement de table

Lorsque vous utilisez des bases de données relationnelles, par défaut, les types appartenant à la référence sont mappés à la même table que le propriétaire. Cela nécessite le fractionnement de la table en deux : certaines colonnes seront utilisées pour stocker les données du propriétaire, et certaines colonnes seront utilisées pour stocker les données de l’entité détenue. Il s’agit d’une fonctionnalité courante connue sous le nom de [fractionnement de table](xref:core/modeling/table-splitting).

Par défaut, EF Core nommera les colonnes de base de données pour les propriétés du type d’entité détenu, en suivant le modèle _Navigation_OwnedEntityProperty_. Par conséquent, les `StreetAddress` Propriétés s’affichent dans la table Orders avec les noms « ShippingAddress_Street » et « ShippingAddress_City ».

Vous pouvez utiliser la `HasColumnName` méthode pour renommer ces colonnes.

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> La plupart des méthodes de configuration de type d’entité normales telles que [ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) peuvent être appelées de la même façon.

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>Partage du même type .NET entre plusieurs types détenus

Un type d’entité détenu peut être du même type .NET qu’un autre type d’entité détenu. par conséquent, le type .NET peut ne pas être suffisant pour identifier un type détenu.

Dans ce cas, la propriété qui pointe du propriétaire vers l’entité détenue devient la définition de la _navigation_ du type d’entité détenu. Du point de vue de EF Core, la navigation de définition fait partie de l’identité du type en même temps que le type .NET.

Par exemple, dans la classe suivante `ShippingAddress` et `BillingAddress` sont tous deux du même type .net, `StreetAddress` .

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

Pour comprendre comment EF Core doit distinguer les instances suivies de ces objets, il peut être utile de penser que la navigation de définition est devenue partie intégrante de la clé de l’instance parallèlement à la valeur de la clé du propriétaire et du type .NET du type détenu.

## <a name="nested-owned-types"></a>Types détenus imbriqués

Dans cet exemple `OrderDetails` `BillingAddress` , est propriétaire de et `ShippingAddress` , qui sont tous deux des `StreetAddress` types. `OrderDetails` est alors détenu par le type `DetailedOrder`.

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

Chaque navigation vers un type détenu définit un type d’entité distinct avec une configuration totalement indépendante.

En plus des types détenus imbriqués, un type détenu peut faire référence à une entité normale qui peut être soit le propriétaire, soit une entité différente tant que l’entité détenue est sur le côté dépendant. Cette fonctionnalité définit les types d’entités détenues, à l’exception des types complexes dans EF6.

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a>Configuration de types détenus

Il est possible de chaîner la `OwnsOne` méthode dans un appel Fluent pour configurer ce modèle :

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

Notez l' `WithOwner` appel utilisé pour définir la propriété de navigation qui pointe vers le propriétaire. Pour définir une navigation vers le type d’entité propriétaire qui ne fait pas partie de la relation de propriété, `WithOwner()` il est recommandé d’appeler sans argument.

Il est également possible d’obtenir ce résultat à l’aide `OwnedAttribute` de sur `OrderDetails` et `StreetAddress` .

Notez également l' `Navigation` appel. Dans EFCore 5,0, les propriétés de navigation vers les types détenus peuvent être configurées de manière plus approfondie [comme pour les propriétés de navigation non détenues](xref:core/modeling/relationships#configuring-navigation-properties).

Le modèle ci-dessus est mappé au schéma de base de données suivant :

![Capture d’écran du modèle de base de données pour l’entité contenant des références d’appartenance imbriquées](_static/owned-entities-nested.png)

## <a name="storing-owned-types-in-separate-tables"></a>Stockage des types détenus dans des tables distinctes

Contrairement aux types complexes EF6, les types détenus peuvent être stockés dans une table distincte du propriétaire. Pour remplacer la Convention qui mappe un type détenu à la même table que le propriétaire, vous pouvez simplement appeler `ToTable` et fournir un autre nom de table. L’exemple suivant mappe `OrderDetails` et ses deux adresses à une table distincte de `DetailedOrder` :

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

Il est également possible d’utiliser `TableAttribute` pour accomplir cela, mais notez que cela échoue s’il existe plusieurs navigations dans le type détenu, car dans ce cas, plusieurs types d’entité sont mappés à la même table.

## <a name="querying-owned-types"></a>Interrogation des types détenus

Quand le propriétaire fait l’objet d’une interrogation, les types détenus sont inclus par défaut. Il n’est pas nécessaire d’utiliser la `Include` méthode, même si les types détenus sont stockés dans une table distincte. En fonction du modèle décrit précédemment, la requête suivante est obtenue `Order` `OrderDetails` et les deux sont détenues `StreetAddresses` de la base de données :

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>Limites

Certaines de ces limitations sont essentielles à la façon dont les types d’entités détenus fonctionnent, mais d’autres sont des restrictions que nous pouvons être en mesure de supprimer dans les versions ultérieures :

### <a name="by-design-restrictions"></a>Restrictions par conception

- Vous ne pouvez pas créer un `DbSet<T>` pour un type détenu.
- Vous ne pouvez pas appeler `Entity<T>()` avec un type détenu sur `ModelBuilder` .
- Les instances de types d’entité possédées ne peuvent pas être partagées par plusieurs propriétaires (il s’agit d’un scénario connu pour les objets de valeur qui ne peuvent pas être implémentés à l’aide des types d’entité détenus).

### <a name="current-shortcomings"></a>Lacunes actuelles

- Les types d’entités détenues ne peuvent pas avoir de hiérarchies d’héritage

### <a name="shortcomings-in-previous-versions"></a>Lacunes dans les versions précédentes

- Dans EF Core 2. x, les navigations de référence vers les types d’entités détenus ne peuvent pas avoir la valeur null, sauf si elles sont explicitement mappées à une table distincte du propriétaire.
- Dans EF Core 3. x, les colonnes des types d’entité détenus mappés à la même table que le propriétaire sont toujours marquées comme Nullable.
