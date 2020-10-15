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
# <a name="custom-migrations-history-table"></a><span data-ttu-id="2b587-103">Table d’historique des migrations personnalisées</span><span class="sxs-lookup"><span data-stu-id="2b587-103">Custom Migrations History Table</span></span>

<span data-ttu-id="2b587-104">Par défaut, EF Core effectue le suivi des migrations qui ont été appliquées à la base de données en les enregistrant dans une table nommée `__EFMigrationsHistory` .</span><span class="sxs-lookup"><span data-stu-id="2b587-104">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="2b587-105">Pour différentes raisons, vous souhaiterez peut-être personnaliser ce tableau pour mieux répondre à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="2b587-105">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2b587-106">Si vous personnalisez la table d’historique des migrations *après avoir* appliqué des migrations, vous êtes responsable de la mise à jour de la table existante dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="2b587-106">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="2b587-107">Nom du schéma et de la table</span><span class="sxs-lookup"><span data-stu-id="2b587-107">Schema and table name</span></span>

<span data-ttu-id="2b587-108">Vous pouvez modifier le schéma et le nom de la table à l’aide `MigrationsHistoryTable()` de la méthode dans `OnConfiguring()` (ou `ConfigureServices()` sur ASP.net Core).</span><span class="sxs-lookup"><span data-stu-id="2b587-108">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="2b587-109">Voici un exemple d’utilisation du fournisseur SQL Server EF Core.</span><span class="sxs-lookup"><span data-stu-id="2b587-109">Here is an example using the SQL Server EF Core provider.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a><span data-ttu-id="2b587-110">Autres modifications</span><span class="sxs-lookup"><span data-stu-id="2b587-110">Other changes</span></span>

<span data-ttu-id="2b587-111">Pour configurer des aspects supplémentaires de la table, substituez et remplacez le service spécifique au fournisseur `IHistoryRepository` .</span><span class="sxs-lookup"><span data-stu-id="2b587-111">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="2b587-112">Voici un exemple de modification du nom de la colonne MigrationId en *ID* sur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2b587-112">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> <span data-ttu-id="2b587-113">`SqlServerHistoryRepository` est à l’intérieur d’un espace de noms interne et peut changer dans les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="2b587-113">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
