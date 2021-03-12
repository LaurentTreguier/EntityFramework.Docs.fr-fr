---
title: Création et configuration d’un modèle – EF Core
description: Vue d’ensemble de la création et de la configuration d’un modèle avec Entity Framework Core.
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: f8ab985b8f17617ca30e7a528ebaac773e789f27
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023976"
---
# <a name="creating-and-configuring-a-model"></a>Création et configuration d’un modèle

Entity Framework Core utilise un ensemble de conventions pour générer un modèle en fonction de la forme de vos classes d’entité. Vous pouvez spécifier une configuration supplémentaire pour compléter et/ou remplacer ce qui a été découvert par convention.

Cet article traite de la configuration qui peut être appliquée à un modèle ciblant n’importe quel magasin de données et qui peut être appliquée pendant le ciblage d’une base de données relationnelle. Les fournisseurs peuvent également activer la configuration qui est spécifique à un magasin de données particulier. Pour plus d’informations sur la configuration spécifique du fournisseur, consultez la section [Fournisseurs de base de données](xref:core/providers/index).

> [!TIP]
> Vous pouvez consulter l' [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples) de cet article sur GitHub.

## <a name="use-fluent-api-to-configure-a-model"></a>Utiliser l’API Fluent pour configurer un modèle

Vous pouvez substituer la méthode `OnModelCreating` dans le contexte dérivé et utiliser `ModelBuilder API` pour configurer votre modèle. Il s’agit de la méthode de configuration la plus puissante, qui permet de spécifier une configuration sans modifier les classes d’entité. Dotée du niveau de priorité le plus élevé, la configuration de l’API Fluent remplace les conventions et les annotations de données.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a>Configuration du regroupement

Pour réduire la taille de la <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> méthode, toute la configuration d’un type d’entité peut être extraite dans une classe distincte qui implémente <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> .

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
