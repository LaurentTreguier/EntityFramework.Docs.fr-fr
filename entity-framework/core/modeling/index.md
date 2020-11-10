---
title: Création et configuration d’un modèle – EF Core
description: Vue d’ensemble de la création et de la configuration d’un modèle avec Entity Framework Core.
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: b18db0059efd335abe2fc44bbc78e0106717e058
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429609"
---
# <a name="creating-and-configuring-a-model"></a>Création et configuration d’un modèle

Entity Framework utilise un ensemble de conventions pour créer un modèle basé sur la forme de vos classes d’entité. Vous pouvez spécifier une configuration supplémentaire pour compléter et/ou remplacer ce qui a été découvert par convention.

Cet article traite de la configuration qui peut être appliquée à un modèle ciblant n’importe quel magasin de données et qui peut être appliquée pendant le ciblage d’une base de données relationnelle. Les fournisseurs peuvent également activer la configuration qui est spécifique à un magasin de données particulier. Pour plus d’informations sur la configuration spécifique du fournisseur, consultez la section [Fournisseurs de base de données](xref:core/providers/index).

> [!TIP]  
> Vous pouvez consulter l' [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) de cet article sur GitHub.

## <a name="use-fluent-api-to-configure-a-model"></a>Utiliser l’API Fluent pour configurer un modèle

Vous pouvez substituer la méthode `OnModelCreating` dans le contexte dérivé et utiliser `ModelBuilder API` pour configurer votre modèle. Il s’agit de la méthode de configuration la plus puissante, qui permet de spécifier une configuration sans modifier les classes d’entité. Dotée du niveau de priorité le plus élevé, la configuration de l’API Fluent remplace les conventions et les annotations de données.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a>Configuration du regroupement

Pour réduire la taille de la <xref:System.Data.Entity.DbContext.OnModelCreating%2A> méthode, toute la configuration d’un type d’entité peut être extraite dans une classe distincte qui implémente <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=IEntityTypeConfiguration)]

Appelez ensuite simplement la `Configure` méthode à partir de `OnModelCreating` .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyIEntityTypeConfiguration)]

Il est possible d’appliquer l’ensemble de la configuration spécifiée dans les types qui implémentent `IEntityTypeConfiguration` dans un assembly donné.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyConfigurationsFromAssembly)]

> [!NOTE]
> L’ordre dans lequel les configurations seront appliquées n’est pas défini. par conséquent, cette méthode ne doit être utilisée que lorsque l’ordre n’a pas d’importance.

## <a name="use-data-annotations-to-configure-a-model"></a>Utiliser des annotations de données pour configurer un modèle

Vous pouvez également appliquer des attributs (également appelés annotations de données) à vos classes et propriétés. Les annotations de données remplacent les conventions, mais elles sont remplacées par la configuration de l’API Fluent.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
