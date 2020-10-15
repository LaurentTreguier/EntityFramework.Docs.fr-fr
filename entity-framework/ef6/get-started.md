---
title: Bien démarrer avec Entity Framework 6 - EF6
description: Bien démarrer avec Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/get-started
ms.openlocfilehash: b518b8ec70045066ffce6ac2a32136df97e14e99
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062787"
---
# <a name="get-started-with-entity-framework-6"></a><span data-ttu-id="fe20d-103">Bien démarrer avec Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="fe20d-103">Get started with Entity Framework 6</span></span>

<span data-ttu-id="fe20d-104">Ce guide contient un ensemble de liens vers des articles de documentation sélectionnés, des procédures pas à pas et des vidéos qui peuvent vous aider à démarrer rapidement.</span><span class="sxs-lookup"><span data-stu-id="fe20d-104">This guide contains a collection of links to selected documentation articles, walkthroughs and videos that can help you get started quickly.</span></span>

## <a name="fundamentals"></a><span data-ttu-id="fe20d-105">Fondamentaux</span><span class="sxs-lookup"><span data-stu-id="fe20d-105">Fundamentals</span></span>

* [<span data-ttu-id="fe20d-106">Obtenir Entity Framework</span><span class="sxs-lookup"><span data-stu-id="fe20d-106">Get Entity Framework</span></span>](xref:ef6/fundamentals/install)

  <span data-ttu-id="fe20d-107">Vous apprendrez ici à ajouter Entity Framework à vos applications. Par ailleurs, si vous voulez utiliser EF Designer, vérifiez qu’il est installé dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fe20d-107">Here you will learn how to add Entity Framework to your applications and, if you want to use the EF Designer, make sure you get it installed in Visual Studio.</span></span>

* [<span data-ttu-id="fe20d-108">Création d’un modèle : Code First, EF Designer et les flux de travail EF</span><span class="sxs-lookup"><span data-stu-id="fe20d-108">Creating a Model: Code First, the EF Designer, and the EF Workflows</span></span>](xref:ef6/modeling/index)

  <span data-ttu-id="fe20d-109">Préférez-vous spécifier votre modèle EF en écrivant du code ou en traçant des zones et des lignes ?</span><span class="sxs-lookup"><span data-stu-id="fe20d-109">Do you prefer to specify your EF model writing code or drawing boxes and lines?</span></span>
<span data-ttu-id="fe20d-110">Allez-vous utiliser EF pour mapper vos objets à une base de données existante ou voulez-vous qu’EF crée une base de données adaptée à vos objets ?</span><span class="sxs-lookup"><span data-stu-id="fe20d-110">Are you going to use EF to map your objects to an existing database or would you like EF to create a database tailored for your objects?</span></span>
<span data-ttu-id="fe20d-111">Vous découvrirez ici deux approches différentes pour utiliser EF6 : EF designer et Code First.</span><span class="sxs-lookup"><span data-stu-id="fe20d-111">Here you learn about two different approaches to use EF6: EF Designer and Code First.</span></span>
<span data-ttu-id="fe20d-112">Veillez à suivre la discussion et à regarder la vidéo présentant la différence.</span><span class="sxs-lookup"><span data-stu-id="fe20d-112">Make sure you follow the discussion and watch the video about the difference.</span></span>

* [<span data-ttu-id="fe20d-113">Utilisation de DbContext</span><span class="sxs-lookup"><span data-stu-id="fe20d-113">Working with DbContext</span></span>](xref:ef6/fundamentals/working-with-dbcontext)

  <span data-ttu-id="fe20d-114">DbContext est le premier et le plus important type EF dont vous avez besoin pour apprendre à utiliser Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fe20d-114">DbContext is the first and most important EF type that you need to learn how to use.</span></span> <span data-ttu-id="fe20d-115">Il sert de tremplin pour les requêtes de base de données et effectue le suivi des changements que vous apportez aux objets afin qu’ils puissent être conservés dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="fe20d-115">It serves as the launchpad for database queries and keeps track of changes you make to objects so that they can be persisted back to the database.</span></span>

* [<span data-ttu-id="fe20d-116">Poser une question</span><span class="sxs-lookup"><span data-stu-id="fe20d-116">Ask a Question</span></span>](xref:ef6/resources/get-help)

  <span data-ttu-id="fe20d-117">Découvrez comment obtenir de l’aide des experts et fournir vos propres réponses à la Communauté.</span><span class="sxs-lookup"><span data-stu-id="fe20d-117">Find out how to get help from the experts and contribute your own answers to the community.</span></span>

* [<span data-ttu-id="fe20d-118">Collaboration</span><span class="sxs-lookup"><span data-stu-id="fe20d-118">Contribute</span></span>](https://github.com/aspnet/EntityFramework6/)

  <span data-ttu-id="fe20d-119">Entity Framework 6 utilise un modèle de développement ouvert.</span><span class="sxs-lookup"><span data-stu-id="fe20d-119">Entity Framework 6 uses an open development model.</span></span> <span data-ttu-id="fe20d-120">Découvrez comment vous pouvez aider à améliorer EF en consultant notre dépôt GitHub.</span><span class="sxs-lookup"><span data-stu-id="fe20d-120">Find out how you can help make EF even better by visiting our GitHub repository.</span></span>

## <a name="code-first-resources"></a><span data-ttu-id="fe20d-121">Ressources Code First</span><span class="sxs-lookup"><span data-stu-id="fe20d-121">Code First resources</span></span>

  - [<span data-ttu-id="fe20d-122">Code First ciblant un flux de travail de base de données existant</span><span class="sxs-lookup"><span data-stu-id="fe20d-122">Code First to an Existing Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/existing-database)
  - [<span data-ttu-id="fe20d-123">Code First ciblant un nouveau flux de travail de base de données</span><span class="sxs-lookup"><span data-stu-id="fe20d-123">Code First to a New Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/new-database)
  - [<span data-ttu-id="fe20d-124">Mappage d’enums avec Code First</span><span class="sxs-lookup"><span data-stu-id="fe20d-124">Mapping Enums Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/enums)
  - [<span data-ttu-id="fe20d-125">Mappage de types spatiaux avec Code First</span><span class="sxs-lookup"><span data-stu-id="fe20d-125">Mapping Spatial Types Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)
  - [<span data-ttu-id="fe20d-126">Écriture de conventions Code First personnalisées</span><span class="sxs-lookup"><span data-stu-id="fe20d-126">Writing Custom Code First Conventions</span></span>](xref:ef6/modeling/code-first/conventions/custom)
  - [<span data-ttu-id="fe20d-127">Utilisation de la configuration Fluent Code First avec Visual Basic</span><span class="sxs-lookup"><span data-stu-id="fe20d-127">Using Code First Fluent Configuration with Visual Basic</span></span>](xref:ef6/modeling/code-first/fluent/vb)
  - [<span data-ttu-id="fe20d-128">Migrations Code First</span><span class="sxs-lookup"><span data-stu-id="fe20d-128">Code First Migrations</span></span>](xref:ef6/modeling/code-first/migrations/index)
  - [<span data-ttu-id="fe20d-129">Migrations Code First dans les environnements d’équipe</span><span class="sxs-lookup"><span data-stu-id="fe20d-129">Code First Migrations in Team Environments</span></span>](xref:ef6/modeling/code-first/migrations/teams)
  - <span data-ttu-id="fe20d-130">[Migrations Code First automatiques](xref:ef6/modeling/code-first/migrations/automatic) (désormais déconseillées)</span><span class="sxs-lookup"><span data-stu-id="fe20d-130">[Automatic Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (This is no longer recommended)</span></span>

## <a name="ef-designer-resources"></a><span data-ttu-id="fe20d-131">Ressources EF Designer</span><span class="sxs-lookup"><span data-stu-id="fe20d-131">EF Designer resources</span></span>
  - [<span data-ttu-id="fe20d-132">Flux de travail Database First</span><span class="sxs-lookup"><span data-stu-id="fe20d-132">Database First Workflow</span></span>](xref:ef6/modeling/designer/workflows/database-first)
  - [<span data-ttu-id="fe20d-133">Flux de travail Model First</span><span class="sxs-lookup"><span data-stu-id="fe20d-133">Model First Workflow</span></span>](xref:ef6/modeling/designer/workflows/model-first)
  - [<span data-ttu-id="fe20d-134">Mappage d’enums</span><span class="sxs-lookup"><span data-stu-id="fe20d-134">Mapping Enums</span></span>](xref:ef6/modeling/designer/data-types/enums)
  - [<span data-ttu-id="fe20d-135">Mappage de types spatiaux</span><span class="sxs-lookup"><span data-stu-id="fe20d-135">Mapping Spatial Types</span></span>](xref:ef6/modeling/designer/data-types/spatial)
  - [<span data-ttu-id="fe20d-136">Mappage d’héritage de table par hiérarchie</span><span class="sxs-lookup"><span data-stu-id="fe20d-136">Table-Per Hierarchy Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tph)
  - [<span data-ttu-id="fe20d-137">Mappage d’héritage de table par type</span><span class="sxs-lookup"><span data-stu-id="fe20d-137">Table-Per Type Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tpt)
  - [<span data-ttu-id="fe20d-138">Mappage de procédures stockées pour les mises à jour</span><span class="sxs-lookup"><span data-stu-id="fe20d-138">Stored Procedure Mapping for Updates</span></span>](xref:ef6/modeling/designer/stored-procedures/cud)
  - [<span data-ttu-id="fe20d-139">Mappage de procédures stockées pour la requête</span><span class="sxs-lookup"><span data-stu-id="fe20d-139">Stored Procedure Mapping for Query</span></span>](xref:ef6/modeling/designer/stored-procedures/query)
  - [<span data-ttu-id="fe20d-140">Fractionnement d’entité</span><span class="sxs-lookup"><span data-stu-id="fe20d-140">Entity Splitting</span></span>](xref:ef6/modeling/designer/entity-splitting)
  - [<span data-ttu-id="fe20d-141">Fractionnement de table</span><span class="sxs-lookup"><span data-stu-id="fe20d-141">Table Splitting</span></span>](xref:ef6/modeling/designer/table-splitting)
  - <span data-ttu-id="fe20d-142">[Requête de définition](xref:ef6/modeling/designer/advanced/defining-query) (Avancé)</span><span class="sxs-lookup"><span data-stu-id="fe20d-142">[Defining Query](xref:ef6/modeling/designer/advanced/defining-query) (Advanced)</span></span>
  - <span data-ttu-id="fe20d-143">[Fonctions table](xref:ef6/modeling/designer/advanced/tvfs) (Avancé)</span><span class="sxs-lookup"><span data-stu-id="fe20d-143">[Table-Valued Functions](xref:ef6/modeling/designer/advanced/tvfs) (Advanced)</span></span>

## <a name="other-resources"></a><span data-ttu-id="fe20d-144">Autres ressources</span><span class="sxs-lookup"><span data-stu-id="fe20d-144">Other resources</span></span>
  - [<span data-ttu-id="fe20d-145">Requête et enregistrement asynchrones</span><span class="sxs-lookup"><span data-stu-id="fe20d-145">Async Query and Save</span></span>](xref:ef6/fundamentals/async)
  - [<span data-ttu-id="fe20d-146">Liaison de données avec WinForms</span><span class="sxs-lookup"><span data-stu-id="fe20d-146">Databinding with WinForms</span></span>](xref:ef6/fundamentals/databinding/winforms)
  - [<span data-ttu-id="fe20d-147">Liaison de données avec WPF</span><span class="sxs-lookup"><span data-stu-id="fe20d-147">Databinding with WPF</span></span>](xref:ef6/fundamentals/databinding/wpf)
  - <span data-ttu-id="fe20d-148">[Scénarios déconnectés avec des entités de suivi automatique](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (désormais déconseillés)</span><span class="sxs-lookup"><span data-stu-id="fe20d-148">[Disconnected scenarios with Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (This is no longer recommended)</span></span>
