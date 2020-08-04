---
title: Fractionnement de table-EF Core
description: Comment configurer le fractionnement de table à l’aide de Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: e7428bc516a69310b6a6f521acc49aee0ba9f802
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526497"
---
# <a name="table-splitting"></a>Fractionnement de table

EF Core permet de mapper deux entités ou plus sur une seule ligne. C’est ce que l’on appelle le _fractionnement de table_ ou le _partage de table_.

## <a name="configuration"></a>Configuration

Pour utiliser le fractionnement de table, les types d’entités doivent être mappés à la même table, les clés primaires sont mappées aux mêmes colonnes et au moins une relation configurée entre la clé primaire d’un type d’entité et une autre dans la même table.

Un scénario courant de fractionnement de table n’utilise qu’un sous-ensemble des colonnes de la table pour améliorer les performances ou l’encapsulation.

Dans cet exemple, `Order` représente un sous-ensemble de `DetailedOrder` .

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

Outre la configuration requise, nous appelons `Property(o => o.Status).HasColumnName("Status")` pour mapper `DetailedOrder.Status` à la même colonne que `Order.Status` .

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> Pour plus de contexte, consultez l' [exemple de projet complet](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) .

## <a name="usage"></a>Utilisation

L’enregistrement et l’interrogation d’entités à l’aide du fractionnement de table s’effectuent de la même façon que les autres entités :

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a>Entité dépendante facultative

> [!NOTE]
> Cette fonctionnalité a été introduite dans EF Core 3,0.

Si toutes les colonnes utilisées par une entité dépendante se trouvent `NULL` dans la base de données, aucune instance n’est créée lors de la requête. Cela permet de modéliser une entité dépendante facultative, où la propriété de relation sur le principal est null. Notez que cela se produit également si toutes les propriétés dépendantes sont facultatives et ont la valeur `null` , ce qui n’est peut-être pas prévu.

## <a name="concurrency-tokens"></a>Jetons d’accès concurrentiel

Si l’un des types d’entité partageant une table a un jeton d’accès concurrentiel, il doit également être inclus dans tous les autres types d’entités. Cela est nécessaire pour éviter une valeur de jeton d’accès concurrentiel obsolète lorsqu’une seule des entités mappées à la même table est mise à jour.

Pour éviter d’exposer le jeton d’accès concurrentiel au code consommateur, il est possible de créer un en tant que [propriété Shadow](xref:core/modeling/shadow-properties):

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
