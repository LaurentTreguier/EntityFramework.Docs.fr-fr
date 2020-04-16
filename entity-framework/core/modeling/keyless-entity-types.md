---
title: Types d’entités sans clé - EF Core
description: Comment configurer les types d’entités sans clé à l’aide de l’entité Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434212"
---
# <a name="keyless-entity-types"></a>Types d’entité sans clé

> [!NOTE]
> Cette fonctionnalité a été ajoutée dans EF Core 2.1 sous le nom de types de requêtes. Dans EF Core 3.0, le concept a été rebaptisé en types d’entités sans clé.

En plus des types d’entités ordinaires, un modèle EF Core peut contenir des _types d’entités sans clé,_ qui peuvent être utilisés pour effectuer des requêtes de base de données contre des données qui ne contiennent pas de valeurs clés.

## <a name="defining-keyless-entity-types"></a>Définir les types d’entités sans clé

Les types d’entités sans clé peuvent être définis à l’aide de l’annotation des données ou de l’API fluide :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>Caractéristiques des types d’entités sans clé

Les types d’entités sans clé prennent en charge bon nombre des mêmes capacités de cartographie que les types d’entités ordinaires, comme la cartographie des héritages et les propriétés de navigation. Sur les magasins relationnels, ils peuvent configurer les objets et colonnes de base de données cibles via des méthodes D’API fluides ou des annotations de données.

Cependant, ils sont différents des types d’entités régulières en ce qu’ils :

- Je ne peux pas avoir une clé définie.
- Ne sont jamais suivis pour les modifications du _DbContext_ et ne sont donc jamais insérés, mis à jour ou supprimés sur la base de données.
- Ne sont jamais découverts par convention.
- Ne supportez qu’un sous-ensemble de capacités de cartographie de navigation, en particulier :
  - Ils ne peuvent jamais agir comme la principale fin d’une relation.
  - Il se peut qu’ils n’aient pas de navigations vers des entités possédées
  - Ils ne peuvent contenir que des propriétés de navigation de référence pointant vers des entités régulières.
  - Les entités ne peuvent pas contenir de propriétés de navigation à des types d’entités sans clé.
- Besoin d’être configuré avec `[Keyless]` une `.HasNoKey()` annotation de données ou un appel de méthode.
- Peut être cartographié à une _requête déterminante_. Une requête déterminante est une requête déclarée dans le modèle qui agit comme source de données pour un type d’entité sans clé.

## <a name="usage-scenarios"></a>Scénarios d’usage

Voici quelques-uns des principaux scénarios d’utilisation pour les types d’entités sans clé :

- Servir de type de retour pour [les requêtes SQL brutes](xref:core/querying/raw-sql).
- Cartographier les vues de base de données qui ne contiennent pas de clé principale.
- Cartographier les tableaux qui n’ont pas de clé primaire définie.
- Cartographie aux requêtes définies dans le modèle.

## <a name="mapping-to-database-objects"></a>Cartographier les objets de base de données

La cartographie d’un type d’entité `ToTable` sans `ToView` clé à un objet de base de données est réalisée à l’aide de l’API ou couramment. Du point de vue d’EF Core, l’objet de base de données spécifié dans cette méthode est une _vue,_ ce qui signifie qu’il est traité comme une source de requête la plus lisante et ne peut pas être la cible de mises à jour, d’insertion ou de suppression d’opérations. Toutefois, cela ne signifie pas que l’objet de base de données est réellement nécessaire pour être une vue de base de données. Il peut être alternativement un tableau de base de données qui sera traité comme lu uniquement. Inversement, pour les types d’entités régulières, `ToTable` EF Core suppose qu’un objet de base de données spécifié dans la méthode peut être traité comme un _tableau,_ ce qui signifie qu’il peut être utilisé comme source de requête, mais aussi ciblé par la mise à jour, supprimer et insérer les opérations. En fait, vous pouvez spécifier `ToTable` le nom d’une vue de base de données dans et tout devrait fonctionner bien tant que la vue est configurée pour être updatable sur la base de données.

> [!NOTE]
> `ToView`suppose que l’objet existe déjà dans la base de données et qu’il ne sera pas créé par les migrations.

## <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser des types d’entités sans clé pour interroger une vue de base de données.

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) sur GitHub.

Tout d’abord, nous définissons un modèle simple de blog et de poteau :

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

Ensuite, nous définissons une simple vue de base de données qui nous permettra d’interroger le nombre de messages associés à chaque blog:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

Ensuite, nous définissons une classe pour tenir le résultat de la vue de base de données:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

Ensuite, nous configurons le type d’entité `HasNoKey` sans clé dans _OnModelCreating_ à l’aide de l’API.
Nous utilisons l’API de configuration fluide pour configurer la cartographie pour le type d’entité sans clé :

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

Ensuite, nous `DbContext` configurons `DbSet<T>`le pour inclure le :

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

Enfin, nous pouvons interroger la vue de base de données de la manière standard :

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> Notez que nous avons également défini une propriété de requête de niveau contexte (DbSet) pour agir comme une racine pour les requêtes contre ce type.
