---
title: Fournisseur de base de données Microsoft SQL Server-index-EF Core
description: Fonctionnalités d’index spécifiques au fournisseur de SQL Server Entity Framework Core
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 42411a562b4741ba39b4eb855bb84c66e100456b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129159"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a><span data-ttu-id="f3fc0-103">Fonctionnalités d’index spécifiques au fournisseur de SQL Server Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f3fc0-103">Index features specific to the Entity Framework Core SQL Server provider</span></span>

<span data-ttu-id="f3fc0-104">Cette page décrit les options de configuration d’index qui sont spécifiques au fournisseur de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-104">This page details index configuration options that are specific to the SQL Server provider.</span></span>

## <a name="clustering"></a><span data-ttu-id="f3fc0-105">Clustering</span><span class="sxs-lookup"><span data-stu-id="f3fc0-105">Clustering</span></span>

<span data-ttu-id="f3fc0-106">Les index cluster trient et stockent les lignes de données de la table ou la vue en fonction de leurs valeurs de clé.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-106">Clustered indexes sort and store the data rows in the table or view based on their key values.</span></span> <span data-ttu-id="f3fc0-107">La création de l’index cluster approprié pour votre table peut améliorer considérablement la vitesse de vos requêtes, car les données sont déjà disposées dans l’ordre optimal.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-107">Creating the right clustered index for your table can significantly improve the speed of your queries, as data is already laid out in the optimal order.</span></span> <span data-ttu-id="f3fc0-108">Il ne peut y avoir qu’un index cluster par table car les lignes de données peuvent être stockées dans un seul ordre.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-108">There can be only one clustered index per table, because the data rows themselves can be stored in only one order.</span></span> <span data-ttu-id="f3fc0-109">Pour plus d’informations, consultez [la documentation SQL Server sur les index cluster et non cluster](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span><span class="sxs-lookup"><span data-stu-id="f3fc0-109">For more information, see [the SQL Server documentation on clustered and non-clustered indexes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span></span>

<span data-ttu-id="f3fc0-110">Par défaut, la colonne de clé primaire d’une table est implicitement sauvegardée par un index cluster, et tous les autres index ne sont pas mis en cluster.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-110">By default, the primary key column of a table is implicitly backed by a clustered index, and all other indexes are non-clustered.</span></span>

<span data-ttu-id="f3fc0-111">Vous pouvez configurer un index ou une clé à mettre en cluster comme suit :</span><span class="sxs-lookup"><span data-stu-id="f3fc0-111">You can configure an index or key to be clustered as follows:</span></span>

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

> [!NOTE]
> <span data-ttu-id="f3fc0-112">SQL Server ne prend en charge qu’un seul index cluster par table, et la clé primaire est par défaut en cluster.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-112">SQL Server only supports one clustered index per table, and the primary key is by default clustered.</span></span> <span data-ttu-id="f3fc0-113">Si vous souhaitez avoir un index cluster sur une colonne non-clé, vous devez explicitement rendre votre clé non cluster.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-113">If you'd like to have a clustered index on a non-key column, you must explicitly make your key non-clustered.</span></span>

## <a name="fill-factor"></a><span data-ttu-id="f3fc0-114">Facteur de remplissage</span><span class="sxs-lookup"><span data-stu-id="f3fc0-114">Fill factor</span></span>

> [!NOTE]
> <span data-ttu-id="f3fc0-115">Cette fonctionnalité a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-115">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="f3fc0-116">L’option de facteur de remplissage d’index est fournie pour optimiser le stockage et les performances des données d’index.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-116">The index fill-factor option is provided for fine-tuning index data storage and performance.</span></span> <span data-ttu-id="f3fc0-117">Pour plus d’informations, consultez [la documentation SQL Server sur le facteur de remplissage](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span><span class="sxs-lookup"><span data-stu-id="f3fc0-117">For more information, see [the SQL Server documentation on fill factor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span></span>

<span data-ttu-id="f3fc0-118">Vous pouvez configurer le facteur de remplissage d’un index comme suit :</span><span class="sxs-lookup"><span data-stu-id="f3fc0-118">You can configure an index's fill factor as follows:</span></span>

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a><span data-ttu-id="f3fc0-119">Création en ligne</span><span class="sxs-lookup"><span data-stu-id="f3fc0-119">Online creation</span></span>

<span data-ttu-id="f3fc0-120">L’option ONLINE permet l’accès simultané des utilisateurs à la table sous-jacente ou aux données d’index cluster et aux index non cluster associés lors de la création d’index, afin que les utilisateurs puissent continuer à mettre à jour et interroger les données sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-120">The ONLINE option allows concurrent user access to the underlying table or clustered index data and any associated nonclustered indexes during index creation, so that users can continue to update and query the underlying data.</span></span> <span data-ttu-id="f3fc0-121">Lorsque vous effectuez des opérations DDL (Data Definition Language) en mode hors connexion, comme la construction ou la reconstruction d'un index cluster, ces opérations posent des verrous exclusifs sur les données sous-jacentes et les index associés.</span><span class="sxs-lookup"><span data-stu-id="f3fc0-121">When you perform data definition language (DDL) operations offline, such as building or rebuilding a clustered index; these operations hold exclusive locks on the underlying data and associated indexes.</span></span> <span data-ttu-id="f3fc0-122">Pour plus d’informations, consultez [la documentation SQL Server sur l’option d’index en ligne](/sql/relational-databases/indexes/perform-index-operations-online).</span><span class="sxs-lookup"><span data-stu-id="f3fc0-122">For more information, see [the SQL Server documentation on the ONLINE index option](/sql/relational-databases/indexes/perform-index-operations-online).</span></span>

<span data-ttu-id="f3fc0-123">Vous pouvez configurer un index avec l’option ONLINE comme suit :</span><span class="sxs-lookup"><span data-stu-id="f3fc0-123">You can configure an index with the ONLINE option as follows:</span></span>

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
