---
title: 'Chargement des données associées : EF Core'
description: Différentes stratégies de chargement des données associées avec Entity Framework Core
author: roji
ms.date: 9/11/2020
uid: core/querying/related-data
ms.openlocfilehash: 8d7fa1ac5673fe4289b18c5b8e12563683463fe8
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023716"
---
# <a name="loading-related-data"></a>Chargement des données associées

Entity Framework Core vous permet d’utiliser les propriétés de navigation dans votre modèle pour charger des entités associées. Il existe trois modèles O/RM communs utilisés pour charger les données associées.

* Le **[chargement hâtif](xref:core/querying/related-data/eager)** signifie que les données associées sont chargées à partir de la base de données dans le cadre de la requête initiale.
* Le **[chargement explicite](xref:core/querying/related-data/explicit)** signifie que les données associées sont chargées explicitement à partir de la base de données à un moment ultérieur.
* Le **[chargement différé](xref:core/querying/related-data/lazy)** signifie que les données associées sont chargées de façon transparente à partir de la base de données lors de l’accès à la propriété de navigation.

> [!TIP]
> Vous pouvez consulter les [exemples](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/RelatedData) dans cette section sur GitHub.
