---
title: 'Chargement des données associées : EF Core'
description: Différentes stratégies de chargement des données associées avec Entity Framework Core
author: roji
ms.date: 9/11/2020
uid: core/querying/related-data
ms.openlocfilehash: a86cae91dbfbf5ebcf820c6afb72d812e278dc66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063671"
---
# <a name="loading-related-data"></a>Chargement des données associées

Entity Framework Core vous permet d’utiliser les propriétés de navigation dans votre modèle pour charger des entités associées. Il existe trois modèles O/RM communs utilisés pour charger les données associées.

* Le **[chargement hâtif](xref:core/querying/related-data/eager)** signifie que les données associées sont chargées à partir de la base de données dans le cadre de la requête initiale.
* Le **[chargement explicite](xref:core/querying/related-data/explicit)** signifie que les données associées sont chargées explicitement à partir de la base de données à un moment ultérieur.
* Le **[chargement différé](xref:core/querying/related-data/lazy)** signifie que les données associées sont chargées de façon transparente à partir de la base de données lors de l’accès à la propriété de navigation.

> [!TIP]
> Vous pouvez consulter les [exemples](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData) dans cette section sur GitHub.
