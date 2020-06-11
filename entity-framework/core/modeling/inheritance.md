---
title: Héritage-EF Core
description: Comment configurer l’héritage de type d’entité à l’aide de Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 0e94013a0b894b162f4bb3ca8e7acb1aca349011
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664050"
---
# <a name="inheritance"></a><span data-ttu-id="114d3-103">Héritage</span><span class="sxs-lookup"><span data-stu-id="114d3-103">Inheritance</span></span>

<span data-ttu-id="114d3-104">EF peut mapper une hiérarchie de types .NET à une base de données.</span><span class="sxs-lookup"><span data-stu-id="114d3-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="114d3-105">Cela vous permet d’écrire vos entités .NET dans le code comme d’habitude, à l’aide de types de base et dérivés, et de faire en sorte que EF crée en toute transparence le schéma de base de données approprié, les requêtes d’émission, etc. Les détails réels de la façon dont une hiérarchie de types est mappée dépendent du fournisseur ; Cette page décrit la prise en charge de l’héritage dans le contexte d’une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="114d3-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

<span data-ttu-id="114d3-106">Pour le moment, EF Core ne prend en charge que le modèle TPH (table par hiérarchie).</span><span class="sxs-lookup"><span data-stu-id="114d3-106">At the moment, EF Core only supports the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="114d3-107">TPH utilise une table unique pour stocker les données de tous les types dans la hiérarchie, et une colonne de discriminateur est utilisée pour identifier le type représenté par chaque ligne.</span><span class="sxs-lookup"><span data-stu-id="114d3-107">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

> [!NOTE]
> <span data-ttu-id="114d3-108">La table par type (TPT) et la table par type (TPC), qui sont prises en charge par EF6, ne sont pas encore prises en charge par EF Core.</span><span class="sxs-lookup"><span data-stu-id="114d3-108">The table-per-type (TPT) and table-per-concrete-type (TPC), which are supported by EF6, are not yet supported by EF Core.</span></span> <span data-ttu-id="114d3-109">TPT est une fonctionnalité majeure prévue pour EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="114d3-109">TPT is a major feature planned for EF Core 5.0.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="114d3-110">Mappage de la hiérarchie des types d’entités</span><span class="sxs-lookup"><span data-stu-id="114d3-110">Entity type hierarchy mapping</span></span>

<span data-ttu-id="114d3-111">Par Convention, EF ne recherche pas automatiquement les types de base ou dérivés ; Cela signifie que si vous souhaitez qu’un type CLR de votre hiérarchie soit mappé, vous devez spécifier explicitement ce type sur votre modèle.</span><span class="sxs-lookup"><span data-stu-id="114d3-111">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="114d3-112">Par exemple, si vous spécifiez uniquement le type de base d’une hiérarchie, EF Core n’inclura pas tous ses sous-types de manière implicite.</span><span class="sxs-lookup"><span data-stu-id="114d3-112">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="114d3-113">L’exemple suivant expose un DbSet pour `Blog` et sa sous-classe `RssBlog` .</span><span class="sxs-lookup"><span data-stu-id="114d3-113">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="114d3-114">Si `Blog` a une autre sous-classe, il ne sera pas inclus dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="114d3-114">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

<span data-ttu-id="114d3-115">Ce modèle est mappé au schéma de base de données suivant (Notez la colonne de *discriminateur* créée implicitement, qui identifie le type de *blog* stocké dans chaque ligne) :</span><span class="sxs-lookup"><span data-stu-id="114d3-115">This model is mapped to the following database schema (note the implicitly-created *Discriminator* column, which identifies which type of *Blog* is stored in each row):</span></span>

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="114d3-117">Les colonnes de base de données deviennent automatiquement Nullable si nécessaire lors de l’utilisation du mappage TPH.</span><span class="sxs-lookup"><span data-stu-id="114d3-117">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="114d3-118">Par exemple, la colonne *RssUrl* accepte les valeurs NULL, car les instances de *blog* ordinaires n’ont pas cette propriété.</span><span class="sxs-lookup"><span data-stu-id="114d3-118">For example, the *RssUrl* column is nullable because regular *Blog* instances do not have that property.</span></span>

<span data-ttu-id="114d3-119">Si vous ne souhaitez pas exposer un DbSet pour une ou plusieurs entités dans la hiérarchie, vous pouvez également utiliser l’API Fluent pour vous assurer qu’elles sont incluses dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="114d3-119">If you don't want to expose a DbSet for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="114d3-120">Si vous ne vous fiez pas aux conventions, vous pouvez spécifier explicitement le type de base à l’aide de `HasBaseType` .</span><span class="sxs-lookup"><span data-stu-id="114d3-120">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="114d3-121">Vous pouvez également utiliser `.HasBaseType((Type)null)` pour supprimer un type d’entité de la hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="114d3-121">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="discriminator-configuration"></a><span data-ttu-id="114d3-122">Configuration de discriminateur</span><span class="sxs-lookup"><span data-stu-id="114d3-122">Discriminator configuration</span></span>

<span data-ttu-id="114d3-123">Vous pouvez configurer le nom et le type de la colonne de discriminateur et les valeurs utilisées pour identifier chaque type dans la hiérarchie :</span><span class="sxs-lookup"><span data-stu-id="114d3-123">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="114d3-124">Dans les exemples ci-dessus, EF a ajouté le discriminateur implicitement en tant que [propriété Shadow](xref:core/modeling/shadow-properties) sur l’entité de base de la hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="114d3-124">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="114d3-125">Cette propriété peut être configurée comme n’importe quelle autre :</span><span class="sxs-lookup"><span data-stu-id="114d3-125">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="114d3-126">Enfin, le discriminateur peut également être mappé à une propriété .NET normale dans votre entité :</span><span class="sxs-lookup"><span data-stu-id="114d3-126">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a><span data-ttu-id="114d3-127">Colonnes partagées</span><span class="sxs-lookup"><span data-stu-id="114d3-127">Shared columns</span></span>

<span data-ttu-id="114d3-128">Par défaut, lorsque deux types d’entités frères dans la hiérarchie ont une propriété portant le même nom, ils sont mappés à deux colonnes distinctes.</span><span class="sxs-lookup"><span data-stu-id="114d3-128">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="114d3-129">Toutefois, si leur type est identique, ils peuvent être mappés à la même colonne de base de données :</span><span class="sxs-lookup"><span data-stu-id="114d3-129">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
