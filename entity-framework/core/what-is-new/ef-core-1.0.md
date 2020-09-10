---
title: Nouveautés d’EF Core 1.0 - EF Core
description: Modifications et améliorations dans Entity Framework Core 1,0
author: divega
ms.date: 10/27/2016
ms.assetid: 20A25111-AEBE-4BC2-83A5-3F651952DF72
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: adde26095a11016796f6bd616978c3ccc0fe4c18
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618852"
---
# <a name="features-included-in-ef-core-10"></a>Fonctionnalités incluses dans EF Core 1.0

## <a name="platforms"></a>Plateformes

### <a name="net-framework-451"></a>.NET Framework 4.5.1

Inclut la console, WPF, WinForms, ASP.NET 4, etc.

### <a name="net-standard-13"></a>.NET Standard 1.3

Inclut ASP.NET Core ciblant à la fois .NET Framework et .NET Core sur Windows, OSX et Linux.

## <a name="modelling"></a>Modélisation

### <a name="basic-modelling"></a>Modélisation de base

Basée sur les entités OCT avec des propriétés get/set de types scalaires communs (`int`, `string`, etc.).

### <a name="relationships-and-navigation-properties"></a>Relations et propriétés de navigation

Les relations un-à-plusieurs et un-à-zéro-ou un-à-un peuvent être spécifiées dans le modèle en fonction d’une clé étrangère. Les propriétés de navigation de types de collection ou de référence simples peuvent être associées à ces relations.

### <a name="built-in-conventions"></a>Conventions intégrées

Ces conventions génèrent un modèle initial basé sur la forme des classes d’entité.

### <a name="fluent-api"></a>API Fluent

Permet de remplacer la méthode `OnModelCreating` sur votre contexte pour configurer davantage le modèle détecté par convention.

### <a name="data-annotations"></a>Annotations de données

Il s’agit d’attributs qui peuvent être ajoutés à vos propriétés/classes d’entité et qui influenceront le modèle EF. Par exemple, l’ajout de la mention `[Required]` informera EF qu’une propriété est obligatoire.

### <a name="relational-table-mapping"></a>Mappage de tables relationnelles

Permet de mapper des entités à des tables ou des colonnes.

### <a name="key-value-generation"></a>Génération de valeur de clé

Inclut la génération côté client et la génération de base de données.

### <a name="database-generated-values"></a>Valeurs générées de base de données

Permet à la base de données de générer des valeurs par insertion (valeurs par défaut) ou par mise à jour (colonnes calculées).

### <a name="sequences-in-sql-server"></a>Séquences dans SQL Server

Permet de définir des objets de la séquence dans le modèle.

### <a name="unique-constraints"></a>Contraintes uniques

Permet de définir d’autres clés ainsi que les relations ciblant ces clés.

### <a name="indexes"></a>Index

La définition d’index dans le modèle introduit automatiquement les index dans la base de données. Les index uniques sont également pris en charge.

### <a name="shadow-state-properties"></a>Propriétés d’état de clichés instantanés

Permet de définir dans le modèle des propriétés qui ne sont pas déclarées ni stockées dans la classe .NET, mais qui peuvent être suivies et mises à jour par EF Core. Elles sont couramment utilisées pour les propriétés de clé étrangère quand l’exposition de ces dernières dans l’objet n’est pas souhaitée.

### <a name="table-per-hierarchy-inheritance-pattern"></a>Modèle d'héritage de table par hiérarchie

Permet d’enregistrer les entités d’une hiérarchie d’héritage dans une table unique à l’aide d’une colonne de discriminateur pour identifier le type d’entité pour un enregistrement donné dans la base de données.

### <a name="model-validation"></a>Validation du modèle

Détecte les modèles non valides dans le modèle et fournit des messages d’erreur utiles.

## <a name="change-tracking"></a>Suivi des modifications

### <a name="snapshot-change-tracking"></a>Suivi des modifications par instantané

Permet de détecter automatiquement les modifications apportées aux entités en comparant l’état actuel avec une copie (instantané) de l’état d’origine.

### <a name="notification-change-tracking"></a>Suivi des modifications par notification

Permet aux entités d’avertir le traceur de modifications dès que des valeurs de propriété sont modifiées.

### <a name="accessing-tracked-state"></a>État du suivi de l’accès

Via `DbContext.Entry` et `DbContext.ChangeTracker`.

### <a name="attaching-detached-entitiesgraphs"></a>Attachement d’entités/graphes détachés

La nouvelle API `DbContext.AttachGraph` permet de rattacher des entités à un contexte pour pouvoir enregistrer des entités modifiées ou de nouvelles entités.

## <a name="saving-data"></a>Enregistrement des données

### <a name="basic-save-functionality"></a>Fonctionnalité d’enregistrement de base

Permet de conserver les modifications apportées aux instances dans la base de données.

### <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste

Évite le remplacement de modifications apportées par un autre utilisateur, sachant que les données ont été extraites de la base de données.

### <a name="async-savechanges"></a>SaveChanges asynchrone

Peut libérer le thread actuel pour traiter d’autres requêtes pendant que la base de données traite les commandes émises à partir de `SaveChanges`.

### <a name="database-transactions"></a>Transactions de base de données

Signifie que `SaveChanges` est toujours atomique (en d’autres termes, soit sa réussite est complète, soit aucune modification n’est apportée à la base de données). Il existe également des API liées aux transactions pour autoriser le partage de transactions entre des instances de contexte, etc.

### <a name="relational-batching-of-statements"></a>Relationnel : traitement par lot d’instructions

Offre de meilleures performances en regroupant les différentes commandes INSERT/UPDATE/DELETE dans une seule boucle pour la base de données.

## <a name="query"></a>Requête

### <a name="basic-linq-support"></a>Prise en charge de base de LINQ

Offre la possibilité d’utiliser LINQ pour récupérer des données à partir de la base de données.

### <a name="mixed-clientserver-evaluation"></a>Évaluation du client/serveur mixte

Permet aux requêtes de contenir la logique qui ne peut pas être évaluée dans la base de données et qui doit par conséquent être évaluée une fois les données récupérées dans la mémoire.

### <a name="notracking"></a>NoTracking

Permet d’accélérer l’exécution des requêtes quand le contexte n’a pas besoin de surveiller les changements apportés aux instances d’entité (cela s’avère utile si les résultats en lecture seule).

### <a name="eager-loading"></a>Chargement hâtif

Fournit les méthodes `Include` et `ThenInclude` pour identifier les données associées qui doivent également être extraites durant l’interrogation.

### <a name="async-query"></a>Requête asynchrone

Peut libérer le thread actuel (et ses ressources associées) pour traiter d’autres requêtes pendant que la base de données traite la requête.

### <a name="raw-sql-queries"></a>Requêtes SQL brutes

Fournit la méthode `DbSet.FromSql` pour utiliser des requêtes SQL brutes pour extraire des données. Ces requêtes peuvent également être composées à l’aide de LINQ.

## <a name="database-schema-management"></a>Gestion du schéma de base de données

### <a name="database-creationdeletion-apis"></a>API de création/suppression de base de données

Elles sont principalement conçues tester l’emplacement où vous souhaitez créer/supprimer rapidement la base de données sans utiliser de migrations.

### <a name="relational-database-migrations"></a>Migrations de base de données relationnelle

Permet à un schéma de base de données relationnelle d’évoluer à travers le temps au fur et à mesure que votre modèle change.

### <a name="reverse-engineer-from-database"></a>Ingénierie à rebours à partir de la base de données

Permet de générer automatiquement un modèle EF basé sur un schéma de base de données relationnelle existante.

## <a name="database-providers"></a>Fournisseurs de bases de données

### <a name="sql-server"></a>SQL Server

Se connecte à Microsoft SQL Server 2008 et versions ultérieures.

### <a name="sqlite"></a>SQLite

Se connecte à une base de données SQLite 3.

### <a name="in-memory"></a>En mémoire

Fonctionnalité conçue pour tester facilement sans vous connecter à une base de données réelle.

### <a name="3rd-party-providers"></a>Fournisseurs tiers

Plusieurs fournisseurs sont disponibles pour d’autres moteurs de base de données. Consultez [Fournisseurs de bases de données](xref:core/providers/index) pour en obtenir la liste complète.
