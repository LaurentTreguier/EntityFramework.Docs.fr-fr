---
title: Clés-EF Core
description: Comment configurer des clés pour des types d’entités lors de l’utilisation de Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: ea744315514b19465aba9a311055a9b5e49fa3f8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063788"
---
# <a name="keys"></a>Keys

Une clé sert d’identificateur unique pour chaque instance d’entité. La plupart des entités dans EF ont une clé unique, qui correspond au concept d’une *clé primaire* dans les bases de données relationnelles (pour les entités sans clés, consultez la rubrique ne pas utiliser les [entités](xref:core/modeling/keyless-entity-types)). Les entités peuvent avoir des clés supplémentaires au-delà de la clé primaire (pour plus d’informations, consultez [autres clés](#alternate-keys) ).

Par Convention, une propriété nommée `Id` ou `<type name>Id` sera configurée comme clé primaire d’une entité.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> Les [types d’entités détenues](xref:core/modeling/owned-entities) utilisent des règles différentes pour définir des clés.

Vous pouvez configurer une propriété unique comme clé primaire d’une entité comme suit :

## <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

Vous pouvez également configurer plusieurs propriétés comme clé d’une entité. il s’agit d’une clé composite. Les clés composites ne peuvent être configurées qu’à l’aide de l’API Fluent. les conventions ne configureront jamais de clé composite et vous ne pourrez pas utiliser d’annotations de données pour en configurer une.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a>Nom de la clé primaire

Par Convention, sur les bases de données relationnelles, les clés primaires sont créées avec le nom `PK_<type name>` . Vous pouvez configurer le nom de la contrainte de clé primaire comme suit :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a>Types et valeurs de clé

Si EF Core prend en charge l’utilisation de propriétés de n’importe quel type primitif comme clé primaire, y compris `string` , `Guid` `byte[]` et d’autres, toutes les bases de données ne prennent pas en charge tous les types en tant que clés. Dans certains cas, les valeurs de clé peuvent être converties automatiquement en un type pris en charge ; sinon, la conversion doit être [spécifiée manuellement](xref:core/modeling/value-conversions).

Les propriétés de clé doivent toujours avoir une valeur non définie par défaut lors de l’ajout d’une nouvelle entité au contexte, mais certains types sont [générés par la base de données](xref:core/modeling/generated-properties). Dans ce cas, EF tente de générer une valeur temporaire lorsque l’entité est ajoutée à des fins de suivi. Une fois [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) appelé, la valeur temporaire est remplacée par la valeur générée par la base de données.

> [!Important]
> Si une propriété de clé a sa valeur générée par la base de données et qu’une valeur non définie par défaut est spécifiée lors de l’ajout d’une entité, EF suppose que l’entité existe déjà dans la base de données et essaiera de la mettre à jour au lieu d’en insérer une nouvelle. Pour éviter cela, désactivez la génération de valeur ou consultez [comment spécifier des valeurs explicites pour les propriétés générées](xref:core/saving/explicit-values-generated-properties).

## <a name="alternate-keys"></a>Clés secondaires

Une autre clé sert d’identificateur unique alternatif pour chaque instance d’entité en plus de la clé primaire. Il peut être utilisé comme cible d’une relation. Lors de l’utilisation d’une base de données relationnelle, cela correspond au concept d’index/de contrainte unique sur la ou les colonnes clés de remplacement et une ou plusieurs contraintes de clé étrangère qui référencent la ou les colonnes.

> [!TIP]
> Si vous souhaitez simplement garantir l’unicité d’une colonne, définissez un index unique plutôt qu’une autre clé (voir [index](xref:core/modeling/indexes)). Dans EF, les clés secondaires sont en lecture seule et fournissent une sémantique supplémentaire sur les index uniques, car elles peuvent être utilisées comme cible d’une clé étrangère.

Des clés alternatives sont généralement introduites pour vous si nécessaire et vous n’avez pas besoin de les configurer manuellement. Par Convention, une clé secondaire est introduite pour vous lorsque vous identifiez une propriété qui n’est pas la clé primaire comme la cible d’une relation.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

Vous pouvez également configurer une seule propriété pour qu’elle soit une clé secondaire :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

Vous pouvez également configurer plusieurs propriétés en tant que clé secondaire (appelée clé de remplacement composite) :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

Enfin, par Convention, l’index et la contrainte qui sont introduits pour une clé secondaire sont nommés `AK_<type name>_<property name>` (pour les clés de remplacement composites `<property name>` deviennent une liste de noms de propriété séparés par un trait de soulignement). Vous pouvez configurer le nom de l’index et de la contrainte unique de la clé secondaire :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
