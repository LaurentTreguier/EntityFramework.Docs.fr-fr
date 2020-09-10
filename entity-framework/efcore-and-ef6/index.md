---
title: Comparer EF6 et EF Core
description: Conseils pour choisir entre EF6 et EF Core.
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 8e99cd9863d9f206ef3ae4d68226283019db835f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619742"
---
# <a name="compare-ef-core--ef6"></a>Comparer EF Core et EF6

## <a name="ef-core"></a>EF Core

Entity Framework Core ([EF Core](xref:core/index)) est un mappeur de base de données objet moderne pour .NET. Il prend en charge les requêtes LINQ, le suivi des modifications, les mises à jour et les migrations de schéma.

EF Core fonctionne avec SQL Server/SQL Azure, SQLite, Azure Cosmos DB, MySQL, PostgreSQL et de nombreuses autres bases de données via un [modèle de plug-in de fournisseur de base de données](xref:core/providers/index).

## <a name="ef6"></a>EF6

Entity Framework 6 ([EF6](xref:ef6/index)) est un mappeur relationnel objet conçu pour .NET Framework, mais avec une prise en charge de .NET Core. EF6 est un produit stable et pris en charge, mais il n’est plus développé de façon active.

## <a name="feature-comparison"></a>Comparaison des fonctionnalités

EF Core offre de nouvelles fonctionnalités qui ne seront pas implémentées dans EF6. Cependant, toutes les fonctionnalités de EF6 ne sont pas implémentées actuellement dans EF Core.

Les tableaux suivants comparent les fonctionnalités disponibles dans EF Core et EF6. Il s’agit d’une comparaison générale qui ne répertorie pas toutes les fonctionnalités et n’explique pas les différences entre la même fonctionnalité dans les différentes versions d’EF.

La colonne EF Core indique la version du produit dans laquelle la fonctionnalité a été introduite pour la première fois.

### <a name="creating-a-model"></a>Création d’un modèle

| **Fonctionnalité**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Mappage des classes de base                                   | Oui      | 1.0                                   |
| Constructeurs avec des paramètres                          |          | 2.1                                   |
| Conversions de valeurs de propriété                            |          | 2.1                                   |
| Types mappés sans clé                             |          | 2.1                                   |
| Conventions                                           | Oui      | 1.0                                   |
| Conventions personnalisées                                    | Oui      | 1.0 (partielle ; [#214](https://github.com/dotnet/efcore/issues/214)) |
| Annotations de données                                      | Oui      | 1.0                                   |
| API Fluent                                            | Oui      | 1.0                                   |
| Héritage : table par hiérarchie (TPH)                | Oui      | 1.0                                   |
| Héritage : table par type (TPT)                     | Oui      | Prévu pour 5.0 ([#2266](https://github.com/dotnet/efcore/issues/2266)) |
| Héritage : table par classe concrète (TPC)           | Oui      | Stretch pour 5.0 ([#3170](https://github.com/dotnet/efcore/issues/3170)) <sup>(1)</sup> |
| Propriétés d’état de clichés instantanés                               |          | 1.0                                   |
| Clés secondaires                                        |          | 1.0                                   |
| Navigations plusieurs-à-plusieurs                              | Oui      | Prévu pour 5.0 ([#19003](https://github.com/dotnet/efcore/issues/19003)) |
| Plusieurs-à-plusieurs sans entité de jonction                      | Oui      | Dans le journal des travaux en souffrance ([#1368](https://github.com/dotnet/efcore/issues/1368)) |
| Génération de clés : base de données                              | Oui      | 1.0                                   |
| Génération de clés : client                                |          | 1.0                                   |
| Types complexes/détenus                                   | Oui      | 2,0                                   |
| Données spatiales                                          | Oui      | 2.2                                   |
| Format de modèle : code                                    | Oui      | 1.0                                   |
| Création d’un modèle à partir d’une base de données : ligne de commande              | Oui      | 1.0                                   |
| Mise à jour d’un modèle à partir d’une base de données                            | Partial  | Dans le journal des travaux en souffrance ([#831](https://github.com/dotnet/efcore/issues/831)) |
| Filtres de requête globale                                  |          | 2,0                                   |
| Fractionnement de table                                       | Oui      | 2,0                                   |
| Fractionnement d'entité                                      | Oui      | Stretch pour 5.0 ([#620](https://github.com/dotnet/efcore/issues/620)) <sup>(1)</sup> |
| Mappage de fonctions scalaires de base de données                      | Médiocre     | 2,0                                   |
| Mappages de champs                                         |          | 1.1                                   |
| Types références Nullables (C# 8.0)                     |          | 3.0                                   |
| Visualisation graphique de modèle                      | Oui      | Aucune prise en charge planifiée <sup>(2)</sup>     |
| Éditeur de modèle graphique                                | Oui      | Aucune prise en charge planifiée <sup>(2)</sup>     |
| Format de modèle : EDMX (XML)                              | Oui      | Aucune prise en charge planifiée <sup>(2)</sup>     |
| Création d’un modèle à partir d’une base de données : assistant VS                 | Oui      | Aucune prise en charge planifiée <sup>(2)</sup>     |

### <a name="querying-data"></a>Interrogation des données

| **Fonctionnalité**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Requêtes LINQ                                          | Oui      | 1.0                                   |
| Code SQL généré lisible                                | Médiocre     | 1.0                                   |
| Traduction GroupBy                                   | Oui      | 2.1                                   |
| Chargement des données associées : hâtif                           | Oui      | 1.0                                   |
| Chargement de données associées : chargement hâtif pour les types dérivés |          | 2.1                                   |
| Chargement des données associées : différé                            | Oui      | 2.1                                   |
| Chargement des données associées : explicite                        | Oui      | 1.1                                   |
| Requêtes SQL brutes : types d’entités                         | Oui      | 1.0                                   |
| Requêtes SQL brutes : Types d’entité sans clé                 | Oui      | 2.1                                   |
| Requêtes SQL brutes : composition avec LINQ                  |          | 1.0                                   |
| Requêtes compilées explicitement                           | Médiocre     | 2,0                                   |
| await foreach (C# 8.0)                                |          | 3.0                                   |
| Langage de requête textuel (Entity SQL)                | Oui      | Aucune prise en charge planifiée <sup>(2)</sup>     |

### <a name="saving-data"></a>Enregistrement des données

| **Fonctionnalité**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Suivi des modifications : instantané                             | Oui      | 1.0                                   |
| Suivi des modifications : notification                         | Oui      | 1.0                                   |
| Suivi des modifications : proxys                              | Oui      | Fusionné pour 5.0 ([#10949](https://github.com/dotnet/efcore/issues/10949)) |
| État du suivi de l’accès                               | Oui      | 1.0                                   |
| Accès concurrentiel optimiste                                | Oui      | 1.0                                   |
| Transactions                                          | Oui      | 1.0                                   |
| Traitement par lot d’instructions                                |          | 1.0                                   |
| Mappage de procédure stockée                              | Oui      | Dans le journal des travaux en souffrance ([#245](https://github.com/dotnet/efcore/issues/245)) |
| API de bas niveau à graphes déconnectés                     | Médiocre     | 1.0                                   |
| De bout en bout à graphes déconnectés                         |          | 1.0 (partielle ; [#5536](https://github.com/dotnet/efcore/issues/5536)) |

### <a name="other-features"></a>Autres fonctionnalités

| **Fonctionnalité**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Migrations                                            | Oui      | 1.0                                   |
| API de création/suppression de base de données                       | Oui      | 1.0                                   |
| Données seed                                             | Oui      | 2.1                                   |
| Résilience de connexion                                 | Oui      | 1.1                                   |
| Intercepteurs                                          | Oui      | 3.0                                   |
| Événements                                                | Oui      | 3.0 (partielle ; [#626](https://github.com/dotnet/efcore/issues/626)) |
| Journalisation simple (Database.Log)                         | Oui      | Fusionné pour 5.0 ([#1199](https://github.com/dotnet/efcore/issues/1199)) |
| Regroupement DbContext                                     |          | 2,0                                   |

### <a name="database-providers-sup3sup"></a>Fournisseurs de bases de données <sup>(3)</sup>

| **Fonctionnalité**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| SQL Server                                            | Oui      | 1.0                                   |
| MySQL                                                 | Oui      | 1.0                                   |
| PostgreSQL                                            | Oui      | 1.0                                   |
| Oracle                                                | Oui      | 1.0                                   |
| SQLite                                                | Oui      | 1.0                                   |
| SQL Server Compact                                    | Oui      | 1.0 <sup>(4)</sup>                    |
| DB2                                                   | Oui      | 1.0                                   |
| Firebird                                              | Oui      | 2,0                                   |
| Jet (Microsoft Access)                                |          | 2.0 <sup>(4)</sup>                    |
| Azure Cosmos DB                                       |          | 3.0                                   |
| In-memory (pour les tests)                               |          | 1.0                                   |

<sup>1</sup> Les objectifs Stretch ne seront probablement pas atteints pour une version donnée. Toutefois, si les choses se déroulent correctement, nous tenterons de le faire.

<sup>2</sup> Certaines fonctionnalités EF6 ne seront pas implémentées dans EF Core. Ces fonctionnalités dépendent de l’Entity Data Model (EDM) sous-jacent de EF6 et/ou constituent des fonctionnalités complexes avec un retour sur investissement relativement faible. Vos commentaires sont toujours les bienvenus, mais même si EF Core permet d’effectuer de nombreuses opérations irréalisables dans EF6, à l'inverse, EF Core ne prend pas en charge toutes les fonctionnalités de EF6.

<sup>3</sup> Les fournisseurs de bases de données EF Core implémentés par des tiers peuvent être retardés lors de la mise à jour vers de nouvelles versions majeures de EF Core. Consultez [Fournisseurs de bases de données](xref:core/providers/index) pour plus d’informations.

<sup>4</sup> Les fournisseurs SQL Server Compact et Jet fonctionnent uniquement sur le .NET Framework (et non sur .NET Core).

### <a name="supported-platforms"></a>Plateformes prises en charge

EF Core 3.1 s’exécute sur .NET Core et .NET Framework, à l’aide de .NET Standard 2.0. Toutefois, EF Core 5.0 ne s’exécutera pas sur .NET Framework. Consultez [Plateformes](xref:core/platforms/index) pour plus d’informations.

EF 6.4 s’exécute sur .NET Core et .NET Framework, via le multi-ciblage.

## <a name="guidance-for-new-applications"></a>Conseils pour les nouvelles applications

Utilisez EF Core sur .NET Core pour toutes les nouvelles applications, sauf si l’application a besoin d’une fonctionnalité [uniquement prise en charge sur .NET Framework](/dotnet/standard/choosing-core-framework-server).

## <a name="guidance-for-existing-ef6-applications"></a>Conseils pour les applications EF6 existantes

EF Core n’est pas un remplacement immédiat de EF6. Le passage de EF6 à EF Core nécessitera probablement des modifications de votre application.

Lors du déplacement d’une application EF6 vers .NET Core :

* Continuez à utiliser EF6 si le code d’accès aux données est stable et n’est pas susceptible d’évoluer ou de nécessiter de nouvelles fonctionnalités.
* Effectuez un portage vers EF Core si le code d’accès aux données est en cours d’évolution ou si l’application a besoin de nouvelles fonctionnalités uniquement disponibles dans EF Core.
* Le portage vers EF Core est également souvent effectué pour améliorer les performances. Toutefois, tous les scénarios n’étant pas forcément plus rapides, procédez d’abord à un profilage.

Pour plus d’informations, consultez [Portage d’EF6 vers EF Core](xref:efcore-and-ef6/porting/index).
