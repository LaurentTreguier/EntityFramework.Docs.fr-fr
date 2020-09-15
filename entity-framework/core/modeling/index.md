---
title: Création et configuration d’un modèle – EF Core
description: Vue d’ensemble de la création et de la configuration d’un modèle avec Entity Framework Core.
author: rowanmiller
ms.date: 11/05/2019
uid: core/modeling/index
ms.openlocfilehash: 1380ed0c1af0fdd3ae24de311bea455cb57a7dcd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071418"
---
# <a name="creating-and-configuring-a-model"></a>Création et configuration d’un modèle

Entity Framework utilise un ensemble de conventions pour créer un modèle basé sur la forme de vos classes d’entité. Vous pouvez spécifier une configuration supplémentaire pour compléter et/ou remplacer ce qui a été découvert par convention.

Cet article traite de la configuration qui peut être appliquée à un modèle ciblant n’importe quel magasin de données et qui peut être appliquée pendant le ciblage d’une base de données relationnelle. Les fournisseurs peuvent également activer la configuration qui est spécifique à un magasin de données particulier. Pour plus d’informations sur la configuration spécifique du fournisseur, consultez la section  [Fournisseurs de base de données](xref:core/providers/index) .

> [!TIP]  
> Vous pouvez voir l’ [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples)  de cet article sur GitHub.

## <a name="use-fluent-api-to-configure-a-model"></a>Utiliser l’API Fluent pour configurer un modèle

Vous pouvez substituer la méthode  `OnModelCreating`  dans votre contexte dérivé et utiliser  `ModelBuilder API` pour configurer votre modèle. Il s’agit de la méthode de configuration la plus puissante, qui permet de spécifier une configuration sans modifier les classes d’entité. Dotée du niveau de priorité le plus élevé, la configuration de l’API Fluent remplace les conventions et les annotations de données.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

## <a name="use-data-annotations-to-configure-a-model"></a>Utiliser des annotations de données pour configurer un modèle

Vous pouvez également appliquer des attributs (également appelés annotations de données) à vos classes et propriétés. Les annotations de données remplacent les conventions, mais elles sont remplacées par la configuration de l’API Fluent.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
