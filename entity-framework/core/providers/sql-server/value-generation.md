---
title: Fournisseur de base de données Microsoft SQL Server-génération de valeur-EF Core
description: Modèles de génération de valeur spécifiques au fournisseur de base de données SQL Server Entity Framework Core
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987153"
---
# <a name="sql-server-value-generation"></a><span data-ttu-id="42199-103">Génération de valeurs de SQL Server</span><span class="sxs-lookup"><span data-stu-id="42199-103">SQL Server Value Generation</span></span>

<span data-ttu-id="42199-104">Cette page détaille la configuration de la génération de valeur et les modèles qui sont spécifiques au fournisseur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="42199-104">This page details value generation configuration  and patterns that are specific to the SQL Server provider.</span></span> <span data-ttu-id="42199-105">Il est recommandé de lire d’abord [la page général sur la génération de valeurs](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="42199-105">It's recommended to first read [the general page on value generation](xref:core/modeling/generated-properties).</span></span>

## <a name="identity-columns"></a><span data-ttu-id="42199-106">Colonnes IDENTITY</span><span class="sxs-lookup"><span data-stu-id="42199-106">IDENTITY columns</span></span>

<span data-ttu-id="42199-107">Par Convention, les colonnes numériques qui sont configurées pour générer leurs valeurs lors de l’ajout sont configurées en tant que [SQL Server colonnes d’identité](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).</span><span class="sxs-lookup"><span data-stu-id="42199-107">By convention, numeric columns that are configured to have their values generated on add are set up as [SQL Server IDENTITY columns](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).</span></span>

### <a name="seed-and-increment"></a><span data-ttu-id="42199-108">Valeur initiale et incrément</span><span class="sxs-lookup"><span data-stu-id="42199-108">Seed and increment</span></span>

<span data-ttu-id="42199-109">Par défaut, les colonnes d’identité commencent à 1 (la valeur initiale) et sont incrémentées de 1 chaque fois qu’une ligne est ajoutée (l’incrément).</span><span class="sxs-lookup"><span data-stu-id="42199-109">By default, IDENTITY columns start off at 1 (the seed), and increment by 1 each time a row is added (the increment).</span></span> <span data-ttu-id="42199-110">Vous pouvez configurer une valeur initiale et un incrément différents comme suit :</span><span class="sxs-lookup"><span data-stu-id="42199-110">You can configure a different seed and increment as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> <span data-ttu-id="42199-111">La possibilité de configurer la valeur initiale et l’incrément de l’identité a été introduite dans EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="42199-111">The ability to configure IDENTITY seed and increment was introduced in EF Core 3.0.</span></span>

### <a name="inserting-explicit-values-into-identity-columns"></a><span data-ttu-id="42199-112">Insertion de valeurs explicites dans des colonnes d’identité</span><span class="sxs-lookup"><span data-stu-id="42199-112">Inserting explicit values into IDENTITY columns</span></span>

<span data-ttu-id="42199-113">Par défaut, SQL Server n’autorise pas l’insertion de valeurs explicites dans les colonnes d’identité.</span><span class="sxs-lookup"><span data-stu-id="42199-113">By default, SQL Server doesn't allow inserting explicit values into IDENTITY columns.</span></span> <span data-ttu-id="42199-114">Pour ce faire, vous devez activer manuellement `IDENTITY_INSERT` avant d’appeler `SaveChanges()` , comme suit :</span><span class="sxs-lookup"><span data-stu-id="42199-114">To do so, you must manually enable `IDENTITY_INSERT` before calling `SaveChanges()`, as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> <span data-ttu-id="42199-115">Nous avons une [demande de fonctionnalité](https://github.com/aspnet/EntityFramework/issues/703) dans notre backlog pour faire cela automatiquement dans le fournisseur SQL Server.</span><span class="sxs-lookup"><span data-stu-id="42199-115">We have a [feature request](https://github.com/aspnet/EntityFramework/issues/703) on our backlog to do this automatically within the SQL Server provider.</span></span>

## <a name="sequences"></a><span data-ttu-id="42199-116">Séquences</span><span class="sxs-lookup"><span data-stu-id="42199-116">Sequences</span></span>

<span data-ttu-id="42199-117">En guise d’alternative aux colonnes d’identité, vous pouvez utiliser des séquences standard.</span><span class="sxs-lookup"><span data-stu-id="42199-117">As an alternative to IDENTITY columns, you can use standard sequences.</span></span> <span data-ttu-id="42199-118">Cela peut être utile dans différents scénarios. par exemple, vous souhaiterez peut-être que plusieurs colonnes dessinent leurs valeurs par défaut à partir d’une seule séquence.</span><span class="sxs-lookup"><span data-stu-id="42199-118">This can be useful in various scenarios; for example, you may want to have multiple columns drawing their default values from a single sequence.</span></span>

<span data-ttu-id="42199-119">SQL Server vous permet de créer des séquences et de les utiliser comme détaillé dans [la page général sur les séquences](xref:core/modeling/sequences).</span><span class="sxs-lookup"><span data-stu-id="42199-119">SQL Server allows you to create sequences and use them as detailed in [the general page on sequences](xref:core/modeling/sequences).</span></span> <span data-ttu-id="42199-120">C’est à vous de configurer vos propriétés pour utiliser des séquences via `HasDefaultValueSql()` .</span><span class="sxs-lookup"><span data-stu-id="42199-120">It's up to you to configure your properties to use sequences via `HasDefaultValueSql()`.</span></span>
