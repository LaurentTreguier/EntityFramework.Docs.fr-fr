---
title: Index-EF Core
description: Configuration des index dans un modèle de Entity Framework Core
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: 3a89f1ae9727dcd8f086e915e666721572636314
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071405"
---
# <a name="indexes"></a><span data-ttu-id="7e7e0-103">Index</span><span class="sxs-lookup"><span data-stu-id="7e7e0-103">Indexes</span></span>

<span data-ttu-id="7e7e0-104">Les index sont un concept commun entre de nombreux magasins de données.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-104">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="7e7e0-105">Bien que leur implémentation dans le magasin de données puisse varier, elles sont utilisées pour rendre les recherches basées sur une colonne (ou un ensemble de colonnes) plus efficaces.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-105">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

<span data-ttu-id="7e7e0-106">Les index ne peuvent pas être créés à l’aide d’annotations de données.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-106">Indexes cannot be created using data annotations.</span></span> <span data-ttu-id="7e7e0-107">Vous pouvez utiliser l’API Fluent pour spécifier un index sur une seule colonne comme suit :</span><span class="sxs-lookup"><span data-stu-id="7e7e0-107">You can use the Fluent API to specify an index on a single column as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

<span data-ttu-id="7e7e0-108">Vous pouvez également spécifier un index sur plusieurs colonnes :</span><span class="sxs-lookup"><span data-stu-id="7e7e0-108">You can also specify an index over more than one column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> <span data-ttu-id="7e7e0-109">Par Convention, un index est créé dans chaque propriété (ou ensemble de propriétés) utilisée comme clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-109">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="7e7e0-110">EF Core ne prend en charge qu’un seul index par jeu de propriétés distinct.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-110">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="7e7e0-111">Si vous utilisez l’API Fluent pour configurer un index sur un ensemble de propriétés pour lequel un index est déjà défini, soit par Convention, soit par configuration précédente, vous allez modifier la définition de cet index.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-111">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="7e7e0-112">Cela est utile si vous souhaitez configurer davantage un index qui a été créé par Convention.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-112">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="7e7e0-113">Unicité de l’index</span><span class="sxs-lookup"><span data-stu-id="7e7e0-113">Index uniqueness</span></span>

<span data-ttu-id="7e7e0-114">Par défaut, les index ne sont pas uniques : plusieurs lignes peuvent avoir la même valeur (s) pour le jeu de colonnes de l’index.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-114">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="7e7e0-115">Vous pouvez rendre un index unique comme suit :</span><span class="sxs-lookup"><span data-stu-id="7e7e0-115">You can make an index unique as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

<span data-ttu-id="7e7e0-116">Toute tentative d’insertion de plusieurs entités avec les mêmes valeurs pour le jeu de colonnes de l’index entraîne la levée d’une exception.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-116">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="7e7e0-117">Nom d’index</span><span class="sxs-lookup"><span data-stu-id="7e7e0-117">Index name</span></span>

<span data-ttu-id="7e7e0-118">Par Convention, les index créés dans une base de données relationnelle sont nommés `IX_<type name>_<property name>` .</span><span class="sxs-lookup"><span data-stu-id="7e7e0-118">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="7e7e0-119">Pour les index composites, `<property name>` devient une liste de noms de propriétés séparés par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-119">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="7e7e0-120">Vous pouvez utiliser l’API Fluent pour définir le nom de l’index créé dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="7e7e0-120">You can use the Fluent API to set the name of the index created in the database:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a><span data-ttu-id="7e7e0-121">Filtre d’index</span><span class="sxs-lookup"><span data-stu-id="7e7e0-121">Index filter</span></span>

<span data-ttu-id="7e7e0-122">Certaines bases de données relationnelles vous permettent de spécifier un index filtré ou partiel.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-122">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="7e7e0-123">Cela vous permet d’indexer uniquement un sous-ensemble des valeurs d’une colonne, en réduisant la taille de l’index et en améliorant l’utilisation des performances et de l’espace disque.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-123">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="7e7e0-124">Pour plus d’informations sur les index filtrés SQL Server, [consultez la documentation](/sql/relational-databases/indexes/create-filtered-indexes).</span><span class="sxs-lookup"><span data-stu-id="7e7e0-124">For more information on SQL Server filtered indexes, [see the documentation](/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="7e7e0-125">Vous pouvez utiliser l’API Fluent pour spécifier un filtre sur un index, fourni sous la forme d’une expression SQL :</span><span class="sxs-lookup"><span data-stu-id="7e7e0-125">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="7e7e0-126">Lorsque vous utilisez le SQL Server le fournisseur EF ajoute un `'IS NOT NULL'` filtre pour toutes les colonnes Nullable qui font partie d’un index unique.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-126">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="7e7e0-127">Pour remplacer cette Convention, vous pouvez fournir une `null` valeur.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-127">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="7e7e0-128">Colonnes incluses</span><span class="sxs-lookup"><span data-stu-id="7e7e0-128">Included columns</span></span>

<span data-ttu-id="7e7e0-129">Certaines bases de données relationnelles vous permettent de configurer un ensemble de colonnes qui sont incluses dans l’index, mais qui ne font pas partie de sa « clé ».</span><span class="sxs-lookup"><span data-stu-id="7e7e0-129">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="7e7e0-130">Cela peut améliorer considérablement les performances des requêtes lorsque toutes les colonnes de la requête sont incluses dans l’index en tant que colonnes clés ou non-clés, car la table elle-même n’a pas besoin d’être accessible.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-130">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="7e7e0-131">Pour plus d’informations sur SQL Server colonnes incluses, [consultez la documentation](/sql/relational-databases/indexes/create-indexes-with-included-columns).</span><span class="sxs-lookup"><span data-stu-id="7e7e0-131">For more information on SQL Server included columns, [see the documentation](/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="7e7e0-132">Dans l’exemple suivant, la `Url` colonne fait partie de la clé d’index, de sorte que tout filtrage de requête sur cette colonne peut utiliser l’index.</span><span class="sxs-lookup"><span data-stu-id="7e7e0-132">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="7e7e0-133">En outre, les requêtes qui accèdent uniquement aux `Title` colonnes et n' `PublishedOn` ont pas besoin d’accéder à la table et s’exécutent plus efficacement :</span><span class="sxs-lookup"><span data-stu-id="7e7e0-133">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
