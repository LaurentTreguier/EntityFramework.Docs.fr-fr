---
title: Table d’historique des migrations personnalisées-EF Core
description: Personnalisation d’une table d’historique à utiliser pour les migrations avec Entity Framework Core
author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: 1f6dcb2fbb20e7dafed17160832ccec453839aa3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062306"
---
# <a name="custom-migrations-history-table"></a>Table d’historique des migrations personnalisées

Par défaut, EF Core effectue le suivi des migrations qui ont été appliquées à la base de données en les enregistrant dans une table nommée `__EFMigrationsHistory` . Pour différentes raisons, vous souhaiterez peut-être personnaliser ce tableau pour mieux répondre à vos besoins.

> [!IMPORTANT]
> Si vous personnalisez la table d’historique des migrations *après avoir* appliqué des migrations, vous êtes responsable de la mise à jour de la table existante dans la base de données.

## <a name="schema-and-table-name"></a>Nom du schéma et de la table

Vous pouvez modifier le schéma et le nom de la table à l’aide `MigrationsHistoryTable()` de la méthode dans `OnConfiguring()` (ou `ConfigureServices()` sur ASP.net Core). Voici un exemple d’utilisation du fournisseur SQL Server EF Core.

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a>Autres modifications

Pour configurer des aspects supplémentaires de la table, substituez et remplacez le service spécifique au fournisseur `IHistoryRepository` . Voici un exemple de modification du nom de la colonne MigrationId en *ID* sur SQL Server.

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> `SqlServerHistoryRepository` est à l’intérieur d’un espace de noms interne et peut changer dans les versions ultérieures.

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
