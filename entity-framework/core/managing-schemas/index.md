---
title: Gestion des schémas de base de données - EF Core
description: Vue d’ensemble des stratégies de gestion des schémas de la base de données avec Entity Framework Core.
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/index
ms.openlocfilehash: e4f8c82125534e9e7e0c2de552bce336a544a2aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619500"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="2b5d6-103">Gestion des schémas de base de données</span><span class="sxs-lookup"><span data-stu-id="2b5d6-103">Managing Database Schemas</span></span>

<span data-ttu-id="2b5d6-104">EF Core propose deux méthodes pour que votre modèle EF Core et le schéma de base de données restent synchronisés. Pour choisir entre les deux, décidez si votre modèle EF Core ou le schéma de base de données est la source de vérité.</span><span class="sxs-lookup"><span data-stu-id="2b5d6-104">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="2b5d6-105">Si vous souhaitez que votre modèle EF Core soit la source de vérité, utilisez [Migrations][1].</span><span class="sxs-lookup"><span data-stu-id="2b5d6-105">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="2b5d6-106">Quand vous apportez des modifications à votre modèle EF Core, cette approche applique progressivement les modifications de schéma correspondantes à votre base de données afin qu’elle reste compatible avec votre modèle EF Core.</span><span class="sxs-lookup"><span data-stu-id="2b5d6-106">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="2b5d6-107">Utilisez [l’ingénierie à rebours][2] si vous souhaitez que votre schéma de base de données soit la source de vérité.</span><span class="sxs-lookup"><span data-stu-id="2b5d6-107">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="2b5d6-108">Cette approche vous permet de structurer un DbContext et les classes de type d’entité en reconstituant la logique de votre schéma de base de données dans un modèle EF Core.</span><span class="sxs-lookup"><span data-stu-id="2b5d6-108">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="2b5d6-109">Les [API de création et de suppression][3] peuvent également créer le schéma de base de données à partir de votre modèle EF Core.</span><span class="sxs-lookup"><span data-stu-id="2b5d6-109">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="2b5d6-110">Toutefois, elles servent principalement pour des tâches de test, de prototypage et d’autres scénarios où la suppression de la base de données est acceptable.</span><span class="sxs-lookup"><span data-stu-id="2b5d6-110">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
