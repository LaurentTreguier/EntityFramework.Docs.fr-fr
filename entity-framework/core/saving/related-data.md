---
title: 'Enregistrement des données associées : EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 07b6680f-ffcf-412c-9857-f997486b386c
uid: core/saving/related-data
ms.openlocfilehash: 86d32b6172ee21c12a15e9ed4bb0142afc99c8bd
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2020
ms.locfileid: "78417544"
---
# <a name="saving-related-data"></a>Enregistrement des données associées

En plus des entités isolées, vous pouvez également utiliser les relations définies dans votre modèle.

> [!TIP]  
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/) sur GitHub.

## <a name="adding-a-graph-of-new-entities"></a>Ajout d’un graphique de nouvelles entités

Si vous créez plusieurs nouvelles entités associées, l’ajout d’une d’elles au contexte provoquera l’ajout des autres.

Dans l’exemple suivant, le blog et trois billets associés sont tous insérés dans la base de données. Les billets sont trouvés et ajoutés, car ils sont accessibles via la propriété de navigation `Blog.Posts`.

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]  
> Utilisez la propriété EntityEntry.State pour définir l’état d’une seule entité. Par exemple : `context.Entry(blog).State = EntityState.Modified`.

## <a name="adding-a-related-entity"></a>Ajout d’une entité associée

Si vous référencez une nouvelle entité à partir de la propriété de navigation d’une entité qui est déjà suivie par le contexte, l’entité est découverte et insérée dans la base de données.

Dans l’exemple suivant, l’entité `post` est insérée car elle est ajoutée à la propriété `Posts` de l’entité `blog` qui a été récupérée à partir de la base de données.

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a>Modification de relations

Si vous modifiez la propriété de navigation d’une entité, les modifications correspondantes porteront sur la colonne de clé étrangère dans la base de données.

Dans l’exemple suivant, l’entité `post` est mise à jour pour appartenir à la nouvelle entité `blog`, car sa propriété de navigation `Blog` est définie pour pointer vers `blog`. Notez que `blog` est également inséré dans la base de données, car il s’agit d’une nouvelle entité qui est référencée par la propriété de navigation d’une entité déjà suivie par le contexte (`post`).

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a>Suppression de relations

Vous pouvez supprimer une relation en définissant une navigation de référence sur `null`, ou en supprimant de l’entité associée à partir d’une navigation de collection.

Supprimer une relation peut avoir des effets secondaires sur l’entité dépendante, en fonction du comportement de suppression en cascade configuré dans la relation.

Par défaut, pour les relations requises, un comportement de suppression en cascade est configuré et l’entité dépendante/enfant est supprimée de la base de données. La suppression en cascade n’est pas configurée pour les relations facultatives par défaut, mais la propriété de clé étrangère est définie avec la valeur null.

Consultez [Relations obligatoires et facultatives](../modeling/relationships.md#required-and-optional-relationships) pour en savoir plus sur la configuration de la nécessité des relations.

Consultez [Suppression en cascade](cascade-delete.md) pour plus d’informations sur la façon dont les comportements de suppression en cascade fonctionnent, dont ils peuvent être configurés explicitement ou encore être sélectionnés par convention.

Dans l’exemple suivant, une suppression en cascade est configurée sur la relation entre `Blog` et `Post`, donc l’entité `post` est supprimée de la base de données.

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#RemovingRelationships)]
