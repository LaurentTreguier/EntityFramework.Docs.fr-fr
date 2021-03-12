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
# <a name="loading-related-data"></a><span data-ttu-id="70700-103">Chargement des données associées</span><span class="sxs-lookup"><span data-stu-id="70700-103">Loading Related Data</span></span>

<span data-ttu-id="70700-104">Entity Framework Core vous permet d’utiliser les propriétés de navigation dans votre modèle pour charger des entités associées.</span><span class="sxs-lookup"><span data-stu-id="70700-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="70700-105">Il existe trois modèles O/RM communs utilisés pour charger les données associées.</span><span class="sxs-lookup"><span data-stu-id="70700-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="70700-106">Le **[chargement hâtif](xref:core/querying/related-data/eager)** signifie que les données associées sont chargées à partir de la base de données dans le cadre de la requête initiale.</span><span class="sxs-lookup"><span data-stu-id="70700-106">**[Eager loading](xref:core/querying/related-data/eager)** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="70700-107">Le **[chargement explicite](xref:core/querying/related-data/explicit)** signifie que les données associées sont chargées explicitement à partir de la base de données à un moment ultérieur.</span><span class="sxs-lookup"><span data-stu-id="70700-107">**[Explicit loading](xref:core/querying/related-data/explicit)** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="70700-108">Le **[chargement différé](xref:core/querying/related-data/lazy)** signifie que les données associées sont chargées de façon transparente à partir de la base de données lors de l’accès à la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="70700-108">**[Lazy loading](xref:core/querying/related-data/lazy)** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="70700-109">Vous pouvez consulter les [exemples](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/RelatedData) dans cette section sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="70700-109">You can view the [samples](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/RelatedData) under this section on GitHub.</span></span>
