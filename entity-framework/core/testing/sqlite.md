---
title: Test avec SQLite-EF Core
description: Utilisation de SQLite pour tester une application Entity Framework Core
author: ajcvickers
ms.date: 04/24/2020
uid: core/testing/sqlite
ms.openlocfilehash: ebfcd36bf236cb83cab8683a8c31d4752d437998
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431613"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a>Utilisation de SQLite pour tester une application EF Core

> [!WARNING]
> L’utilisation de SQLite peut être un moyen efficace de tester une application EF Core.
> Toutefois, des problèmes peuvent survenir lorsque SQLite se comporte différemment des autres systèmes de base de données.
> Consultez [test du code qui utilise EF Core](xref:core/testing/index) pour en savoir plus sur les problèmes et les compromis.  

Ce document génère des utilisations sur les concepts introduits dans l' [exemple illustrant comment tester des applications qui utilisent EF Core](xref:core/testing/testing-sample).
Les exemples de code présentés ici proviennent de cet exemple.

## <a name="using-sqlite-in-memory-databases"></a>Utilisation de bases de données en mémoire SQLite

Normalement, SQLite crée des bases de données en tant que fichiers simples et accède au fichier in-process avec votre application.
C’est très rapide, en particulier lors de l’utilisation d’un [SSD](https://en.wikipedia.org/wiki/Solid-state_drive)rapide.

SQLite peut également utiliser des bases de données créées uniquement en mémoire.
Cela est facile à utiliser avec EF Core tant que vous comprenez la durée de vie de la base de données en mémoire :

* La base de données est créée lors de l’ouverture de la connexion à celle-ci.
* La base de données est supprimée lors de la fermeture de la connexion à celle-ci

EF Core utilisera une connexion déjà ouverte lorsque vous en aurez donné une, et ne tentera pas de la fermer.
La clé d’utilisation d’EF Core avec une base de données SQLite en mémoire consiste donc à ouvrir la connexion avant de la transmettre à EF.  

L' [exemple](xref:core/testing/testing-sample) atteint ce code avec le code suivant :

[!code-csharp[SqliteInMemory](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

Remarques :

* La `CreateInMemoryDatabase` méthode crée une base de données en mémoire SQLite et ouvre la connexion à celle-ci.
* Le créé `DbConnection` est extrait du `ContextOptions` et enregistré.
* La connexion est supprimée lorsque le test est supprimé afin que les ressources ne soient pas divulguées.

> [!NOTE]
> [Problème #16103](https://github.com/dotnet/efcore/issues/16103) effectue le suivi des méthodes pour faciliter cette gestion des connexions.
