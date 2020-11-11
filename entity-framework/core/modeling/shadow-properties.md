---
title: Propriétés des ombres et des indexeurs-EF Core
description: Configuration des propriétés d’ombre et de l’indexeur dans un modèle de Entity Framework Core
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 180478212b683a271d2519cc1a4c79be5d3f11b9
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503187"
---
# <a name="shadow-and-indexer-properties"></a>Propriétés de l’instantané et de l’indexeur

Les propriétés Shadow sont des propriétés qui ne sont pas définies dans votre classe d’entité .NET, mais qui sont définies pour ce type d’entité dans le modèle EF Core. La valeur et l’état de ces propriétés sont gérés uniquement dans le dispositif de suivi des modifications. Les propriétés Shadow sont utiles quand il y a des données dans la base de données qui ne doivent pas être exposées sur les types d’entités mappés.

Les propriétés de l’indexeur sont des propriétés de type d’entité, qui sont sauvegardées par un [indexeur](/dotnet/csharp/programming-guide/indexers/) dans une classe d’entité .net. Ils sont accessibles à l’aide de l’indexeur sur les instances de classe .NET. Elle vous permet également d’ajouter des propriétés supplémentaires au type d’entité sans modifier la classe CLR.

## <a name="foreign-key-shadow-properties"></a>Propriétés de l’ombre de la clé étrangère

Les propriétés Shadow sont le plus souvent utilisées pour les propriétés de clé étrangère, où la relation entre deux entités est représentée par une valeur de clé étrangère dans la base de données, mais la relation est gérée sur les types d’entité à l’aide des propriétés de navigation entre les types d’entités. Par Convention, EF introduit une propriété Shadow lorsqu’une relation est détectée, mais qu’aucune propriété de clé étrangère n’est trouvée dans la classe d’entité dépendante.

La propriété sera nommée `<navigation property name><principal key property name>` (la navigation sur l’entité dépendante, qui pointe vers l’entité principale, est utilisée pour le nommage). Si le nom de la propriété de clé principale comprend le nom de la propriété de navigation, le nom sera simplement `<principal key property name>` . S’il n’existe aucune propriété de navigation sur l’entité dépendante, le nom du type de principal est utilisé à la place.

Par exemple, la liste de code suivante entraînera l' `BlogId` introduction d’une propriété Shadow à l' `Post` entité :

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>Configuration des propriétés Shadow

Vous pouvez utiliser l’API Fluent pour configurer les propriétés Shadow. Une fois que vous avez appelé la surcharge de chaîne de `Property` , vous pouvez chaîner n’importe quel appel de configuration que vous feriez pour d’autres propriétés. Dans l’exemple suivant, étant donné que n' `Blog` a aucune propriété CLR nommée `LastUpdated` , une propriété Shadow est créée :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

Si le nom fourni à la `Property` méthode correspond au nom d’une propriété existante (une propriété Shadow ou une propriété Shadow définie sur la classe d’entité), le code configurera cette propriété existante au lieu d’introduire une nouvelle propriété Shadow.

## <a name="accessing-shadow-properties"></a>Accès aux propriétés Shadow

Les valeurs de propriété Shadow peuvent être obtenues et modifiées par le biais de l' `ChangeTracker` API :

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

Les propriétés Shadow peuvent être référencées dans des requêtes LINQ via la `EF.Property` méthode statique :

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

Les propriétés Shadow ne sont pas accessibles après une requête de non-suivi, car les entités retournées ne sont pas suivies par le dispositif de suivi des modifications.

## <a name="configuring-indexer-properties"></a>Configuration des propriétés de l’indexeur

Vous pouvez utiliser l’API Fluent pour configurer les propriétés de l’indexeur. Une fois que vous avez appelé la méthode `IndexerProperty` , vous pouvez chaîner n’importe quel appel de configuration que vous feriez pour d’autres propriétés. Dans l’exemple suivant, `Blog` a un indexeur défini et il sera utilisé pour créer une propriété d’indexeur.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexerProperty.cs?name=ShadowProperty&highlight=3)]

Si le nom fourni à la `IndexerProperty` méthode correspond au nom d’une propriété d’indexeur existante, le code configurera cette propriété existante. Si le type d’entité a une propriété, qui est stockée par une propriété sur la classe d’entité, une exception est levée, car les propriétés de l’indexeur ne doivent être accessibles qu’à l’aide de l’indexeur.

## <a name="property-bag-entity-types"></a>Types d’entités du conteneur des propriétés

> [!NOTE]
> La prise en charge des types d’entités du conteneur de propriétés a été ajoutée dans EF Core 5,0.

Les types d’entités qui contiennent uniquement des propriétés d’indexeur sont connus sous le nom de types d’entité de conteneur de propriétés. Ces types d’entités n’ont pas de propriétés Shadow, à la place, EF crée des propriétés d’indexeur. Actuellement `Dictionary<string, object>` , seul est pris en charge en tant que type d’entité de conteneur de propriétés. Elle doit être configurée en tant que type d’entité partagée avec un nom unique et la `DbSet` propriété correspondante doit être implémentée à l’aide d’un `Set` appel.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
