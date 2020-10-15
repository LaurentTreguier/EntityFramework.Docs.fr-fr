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
# <a name="get-started-with-entity-framework-6"></a>Bien démarrer avec Entity Framework 6

Ce guide contient un ensemble de liens vers des articles de documentation sélectionnés, des procédures pas à pas et des vidéos qui peuvent vous aider à démarrer rapidement.

## <a name="fundamentals"></a>Fondamentaux

* [Obtenir Entity Framework](xref:ef6/fundamentals/install)

  Vous apprendrez ici à ajouter Entity Framework à vos applications. Par ailleurs, si vous voulez utiliser EF Designer, vérifiez qu’il est installé dans Visual Studio.

* [Création d’un modèle : Code First, EF Designer et les flux de travail EF](xref:ef6/modeling/index)

  Préférez-vous spécifier votre modèle EF en écrivant du code ou en traçant des zones et des lignes ?
Allez-vous utiliser EF pour mapper vos objets à une base de données existante ou voulez-vous qu’EF crée une base de données adaptée à vos objets ?
Vous découvrirez ici deux approches différentes pour utiliser EF6 : EF designer et Code First.
Veillez à suivre la discussion et à regarder la vidéo présentant la différence.

* [Utilisation de DbContext](xref:ef6/fundamentals/working-with-dbcontext)

  DbContext est le premier et le plus important type EF dont vous avez besoin pour apprendre à utiliser Entity Framework. Il sert de tremplin pour les requêtes de base de données et effectue le suivi des changements que vous apportez aux objets afin qu’ils puissent être conservés dans la base de données.

* [Poser une question](xref:ef6/resources/get-help)

  Découvrez comment obtenir de l’aide des experts et fournir vos propres réponses à la Communauté.

* [Collaboration](https://github.com/aspnet/EntityFramework6/)

  Entity Framework 6 utilise un modèle de développement ouvert. Découvrez comment vous pouvez aider à améliorer EF en consultant notre dépôt GitHub.

## <a name="code-first-resources"></a>Ressources Code First

  - [Code First ciblant un flux de travail de base de données existant](xref:ef6/modeling/code-first/workflows/existing-database)
  - [Code First ciblant un nouveau flux de travail de base de données](xref:ef6/modeling/code-first/workflows/new-database)
  - [Mappage d’enums avec Code First](xref:ef6/modeling/code-first/data-types/enums)
  - [Mappage de types spatiaux avec Code First](xref:ef6/modeling/code-first/data-types/spatial)
  - [Écriture de conventions Code First personnalisées](xref:ef6/modeling/code-first/conventions/custom)
  - [Utilisation de la configuration Fluent Code First avec Visual Basic](xref:ef6/modeling/code-first/fluent/vb)
  - [Migrations Code First](xref:ef6/modeling/code-first/migrations/index)
  - [Migrations Code First dans les environnements d’équipe](xref:ef6/modeling/code-first/migrations/teams)
  - [Migrations Code First automatiques](xref:ef6/modeling/code-first/migrations/automatic) (désormais déconseillées)

## <a name="ef-designer-resources"></a>Ressources EF Designer
  - [Flux de travail Database First](xref:ef6/modeling/designer/workflows/database-first)
  - [Flux de travail Model First](xref:ef6/modeling/designer/workflows/model-first)
  - [Mappage d’enums](xref:ef6/modeling/designer/data-types/enums)
  - [Mappage de types spatiaux](xref:ef6/modeling/designer/data-types/spatial)
  - [Mappage d’héritage de table par hiérarchie](xref:ef6/modeling/designer/inheritance/tph)
  - [Mappage d’héritage de table par type](xref:ef6/modeling/designer/inheritance/tpt)
  - [Mappage de procédures stockées pour les mises à jour](xref:ef6/modeling/designer/stored-procedures/cud)
  - [Mappage de procédures stockées pour la requête](xref:ef6/modeling/designer/stored-procedures/query)
  - [Fractionnement d’entité](xref:ef6/modeling/designer/entity-splitting)
  - [Fractionnement de table](xref:ef6/modeling/designer/table-splitting)
  - [Requête de définition](xref:ef6/modeling/designer/advanced/defining-query) (Avancé)
  - [Fonctions table](xref:ef6/modeling/designer/advanced/tvfs) (Avancé)

## <a name="other-resources"></a>Autres ressources
  - [Requête et enregistrement asynchrones](xref:ef6/fundamentals/async)
  - [Liaison de données avec WinForms](xref:ef6/fundamentals/databinding/winforms)
  - [Liaison de données avec WPF](xref:ef6/fundamentals/databinding/wpf)
  - [Scénarios déconnectés avec des entités de suivi automatique](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (désormais déconseillés)
