---
title: Enregistrement de données - EF Core
description: Vue d’ensemble de l’enregistrement des données avec Entity Framework Core.
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 7a31f92aff20f4026b984065e53a2dcc77ed46e7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063541"
---
# <a name="saving-data"></a><span data-ttu-id="5fafa-103">Enregistrement de données</span><span class="sxs-lookup"><span data-stu-id="5fafa-103">Saving Data</span></span>

<span data-ttu-id="5fafa-104">Chaque instance de contexte a un `ChangeTracker` qui est responsable du suivi des modifications à écrire dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="5fafa-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="5fafa-105">Quand vous apportez des modifications à des instances de vos classes d’entité, ces modifications sont enregistrées dans le `ChangeTracker`, puis écrites dans la base de données quand vous appelez `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="5fafa-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="5fafa-106">Le fournisseur de base de données est chargé de traduire les modifications en opérations de base de données (par exemple les commandes `INSERT`, `UPDATE` et `DELETE` pour une base de données relationnelle).</span><span class="sxs-lookup"><span data-stu-id="5fafa-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
