---
title: Nouveautés d’EF Core 1.1 - EF Core
description: Modifications et améliorations dans Entity Framework Core 1,1
author: divega
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: 5f81e8b25feba5cdf5ae5e84b1d3362912ab5b26
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072406"
---
# <a name="new-features-in-ef-core-11"></a>Nouvelles fonctionnalités d’EF Core 1.1

## <a name="modeling"></a>Modélisation

### <a name="field-mapping"></a>Mappages de champs

Permet de configurer un champ de stockage pour une propriété. Cela peut être utile pour les propriétés en lecture seule ou les données utilisant les méthodes Get/Set au lieu d’une propriété.

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a>Mappage de tables à mémoire optimisée dans SQL Server

Vous pouvez spécifier que la table à laquelle est mappée une entité a une mémoire optimisée. Quand EF Core est utilisé pour créer et gérer une base de données basée sur votre modèle (avec des migrations ou `Database.EnsureCreated()`), une table à mémoire optimisée est créée pour ces entités.

## <a name="change-tracking"></a>Change tracking

### <a name="additional-change-tracking-apis-from-ef6"></a>API de suivi des modifications supplémentaires dans EF6

Par exemple : `Reload`, `GetModifiedProperties`, `GetDatabaseValues`, etc.

## <a name="query"></a>Query

### <a name="explicit-loading"></a>Chargement explicite

Permet de déclencher le remplissage d’une propriété de navigation sur une entité qui a été précédemment chargée à partir de la base de données.

### <a name="dbsetfind"></a>DbSet.Find

Fournit un moyen simple de récupérer une entité en fonction de sa valeur de clé primaire.

## <a name="other"></a>Autre

### <a name="connection-resiliency"></a>Résilience de connexion

Effectue automatiquement de nouvelles tentatives de commandes de base de données ayant échoué. Cela est particulièrement utile durant la connexion à SQL Azure, où les défaillances passagères sont courantes.

### <a name="simplified-service-replacement"></a>Remplacement de services simplifié

Facilite le remplacement de services internes utilisés par EF.
