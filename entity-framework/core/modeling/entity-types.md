---
title: Types d’entité-EF Core
description: Comment configurer et mapper des types d’entité à l’aide de Entity Framework Core
author: roji
ms.date: 12/03/2019
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/entity-types
ms.openlocfilehash: b3d9ad753637d021d9aa52965da38091ae690f77
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502452"
---
# <a name="entity-types"></a><span data-ttu-id="ee139-103">Types d'entité</span><span class="sxs-lookup"><span data-stu-id="ee139-103">Entity Types</span></span>

<span data-ttu-id="ee139-104">L’inclusion d’un DbSet d’un type dans votre contexte signifie qu’il est inclus dans le modèle de EF Core ; Nous faisons généralement référence à un type de ce type en tant qu' *entité*.</span><span class="sxs-lookup"><span data-stu-id="ee139-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="ee139-105">EF Core pouvez lire et écrire des instances d’entité à partir de/vers la base de données, et si vous utilisez une base de données relationnelle, EF Core pouvez créer des tables pour vos entités via des migrations.</span><span class="sxs-lookup"><span data-stu-id="ee139-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="ee139-106">Inclusion de types dans le modèle</span><span class="sxs-lookup"><span data-stu-id="ee139-106">Including types in the model</span></span>

<span data-ttu-id="ee139-107">Par Convention, les types qui sont exposés dans les propriétés DbSet de votre contexte sont inclus dans le modèle en tant qu’entités.</span><span class="sxs-lookup"><span data-stu-id="ee139-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="ee139-108">Les types d’entités qui sont spécifiés dans la méthode `OnModelCreating` sont également inclus, comme tous les types trouvés en explorant de manière récursive les propriétés de navigation d’autres types d’entités découverts.</span><span class="sxs-lookup"><span data-stu-id="ee139-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="ee139-109">Dans l’exemple de code ci-dessous, tous les types sont inclus :</span><span class="sxs-lookup"><span data-stu-id="ee139-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="ee139-110">`Blog` est inclus, car il est exposé dans une propriété DbSet sur le contexte.</span><span class="sxs-lookup"><span data-stu-id="ee139-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="ee139-111">`Post` est inclus, car il est découvert via la propriété de navigation `Blog.Posts`.</span><span class="sxs-lookup"><span data-stu-id="ee139-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="ee139-112">`AuditEntry`, car il est spécifié dans `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="ee139-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="ee139-113">Exclusion de types du modèle</span><span class="sxs-lookup"><span data-stu-id="ee139-113">Excluding types from the model</span></span>

<span data-ttu-id="ee139-114">Si vous ne souhaitez pas qu’un type soit inclus dans le modèle, vous pouvez l’exclure :</span><span class="sxs-lookup"><span data-stu-id="ee139-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="ee139-115">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="ee139-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="ee139-116">API Fluent</span><span class="sxs-lookup"><span data-stu-id="ee139-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a><span data-ttu-id="ee139-117">Nom du tableau</span><span class="sxs-lookup"><span data-stu-id="ee139-117">Table name</span></span>

<span data-ttu-id="ee139-118">Par Convention, chaque type d’entité sera configuré pour être mappé à une table de base de données portant le même nom que la propriété DbSet qui expose l’entité.</span><span class="sxs-lookup"><span data-stu-id="ee139-118">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="ee139-119">S’il n’existe aucun DbSet pour l’entité donnée, le nom de la classe est utilisé.</span><span class="sxs-lookup"><span data-stu-id="ee139-119">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="ee139-120">Vous pouvez configurer manuellement le nom de la table :</span><span class="sxs-lookup"><span data-stu-id="ee139-120">You can manually configure the table name:</span></span>

### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="ee139-121">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="ee139-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="ee139-122">API Fluent</span><span class="sxs-lookup"><span data-stu-id="ee139-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="ee139-123">Schéma de table</span><span class="sxs-lookup"><span data-stu-id="ee139-123">Table schema</span></span>

<span data-ttu-id="ee139-124">Lorsque vous utilisez une base de données relationnelle, les tables sont par convention créées dans le schéma par défaut de votre base de données.</span><span class="sxs-lookup"><span data-stu-id="ee139-124">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="ee139-125">Par exemple, Microsoft SQL Server utilise le schéma de `dbo` (SQLite ne prend pas en charge les schémas).</span><span class="sxs-lookup"><span data-stu-id="ee139-125">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="ee139-126">Vous pouvez configurer des tables à créer dans un schéma spécifique comme suit :</span><span class="sxs-lookup"><span data-stu-id="ee139-126">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="ee139-127">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="ee139-127">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="ee139-128">API Fluent</span><span class="sxs-lookup"><span data-stu-id="ee139-128">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="ee139-129">Au lieu de spécifier le schéma pour chaque table, vous pouvez également définir le schéma par défaut au niveau du modèle à l’aide de l’API Fluent :</span><span class="sxs-lookup"><span data-stu-id="ee139-129">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="ee139-130">Notez que la définition du schéma par défaut affectera également d’autres objets de base de données, tels que les séquences.</span><span class="sxs-lookup"><span data-stu-id="ee139-130">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>
