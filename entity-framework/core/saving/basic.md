---
title: 'Enregistrement de base : EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
uid: core/saving/basic
ms.openlocfilehash: 066d67d6104316832a33f5a3648f1f2fa6cc9c50
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417634"
---
# <a name="basic-save"></a>Enregistrement de base

Découvrez comment ajouter, modifier et supprimer des données à l’aide de vos classes de contexte et d’entité.

> [!TIP]  
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) sur GitHub.

## <a name="adding-data"></a>Ajout de données

Utilisez la méthode *DbSet.Add* pour ajouter de nouvelles instances de vos classes d’entité. Les données seront insérées dans la base de données lorsque vous appelez *SaveChanges*.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> Les méthodes Add, Attach et Update fonctionnent toutes sur le graphique complet des entités passées, comme décrit dans la section [Données associées](related-data.md). Vous pouvez aussi utiliser la propriété EntityEntry.State pour définir l’état d’une seule entité. Par exemple : `context.Entry(blog).State = EntityState.Modified`.

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
