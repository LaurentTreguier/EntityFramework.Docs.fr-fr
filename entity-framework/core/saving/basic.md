---
title: 'Enregistrement de base : EF Core'
description: Informations de base sur l’ajout, la mise à jour et la suppression de données avec Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/basic
ms.openlocfilehash: 8c98f95dee85641f11af716290333b47f340bff8
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129107"
---
# <a name="basic-save"></a>Enregistrement de base

Découvrez comment ajouter, modifier et supprimer des données à l’aide de vos classes de contexte et d’entité.

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) sur GitHub.

## <a name="adding-data"></a>Ajout de données

Utilisez la méthode *DbSet.Add* pour ajouter de nouvelles instances de vos classes d’entité. Les données seront insérées dans la base de données lorsque vous appelez *SaveChanges*.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]
> Les méthodes Add, Attach et Update fonctionnent toutes sur le graphique complet des entités passées, comme décrit dans la section [Données associées](xref:core/saving/related-data). Vous pouvez aussi utiliser la propriété EntityEntry.State pour définir l’état d’une seule entité. Par exemple : `context.Entry(blog).State = EntityState.Modified`.

## <a name="updating-data"></a>Mise à jour des données

EF détecte automatiquement les modifications apportées à une entité existante qui est suivie par le contexte. Cela inclut les entités que vous chargez/demandez à partir de la base de données et des entités qui ont été précédemment ajoutées et enregistrées dans la base de données.

Modifiez simplement les valeurs affectées aux propriétés, puis appelez *SaveChanges*.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a>Suppression de données

Utilisez la méthode *DbSet.Remove* pour supprimer les instances de vos classes d’entité.

Si l’entité existe déjà dans la base de données, elle sera supprimée pendant *SaveChanges*. Si l’entité n’a pas encore été enregistrée dans la base de données (autrement dit, si elle est suivie comme ajoutée), elle sera supprimée du contexte et ne pourra plus être insérée lorsque *SaveChanges* est appelé.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a>Plusieurs opérations dans un seul SaveChanges

Vous pouvez combiner plusieurs opérations d’ajout/mise à jour/suppression en un seul appel à *SaveChanges*.

> [!NOTE]
> Pour la plupart des fournisseurs de base de données, *SaveChanges* est transactionnel. Cela signifie que toutes les opérations réussissent ou échouent complètement et que les opérations ne sont jamais partiellement appliquées.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
