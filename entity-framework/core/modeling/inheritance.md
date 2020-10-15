---
title: Héritage-EF Core
description: Comment configurer l’héritage de type d’entité à l’aide de Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 47aae0d57d7203f0e6da5868bdc082ad85d59620
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063866"
---
# <a name="inheritance"></a><span data-ttu-id="e09b8-103">Héritage</span><span class="sxs-lookup"><span data-stu-id="e09b8-103">Inheritance</span></span>

<span data-ttu-id="e09b8-104">EF peut mapper une hiérarchie de types .NET à une base de données.</span><span class="sxs-lookup"><span data-stu-id="e09b8-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="e09b8-105">Cela vous permet d’écrire vos entités .NET dans le code comme d’habitude, à l’aide de types de base et dérivés, et de faire en sorte que EF crée en toute transparence le schéma de base de données approprié, les requêtes d’émission, etc. Les détails réels de la façon dont une hiérarchie de types est mappée dépendent du fournisseur ; Cette page décrit la prise en charge de l’héritage dans le contexte d’une base de données relationnelle.</span><span class="sxs-lookup"><span data-stu-id="e09b8-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="e09b8-106">Mappage de la hiérarchie des types d’entités</span><span class="sxs-lookup"><span data-stu-id="e09b8-106">Entity type hierarchy mapping</span></span>

<span data-ttu-id="e09b8-107">Par Convention, EF ne recherche pas automatiquement les types de base ou dérivés ; Cela signifie que si vous souhaitez qu’un type CLR de votre hiérarchie soit mappé, vous devez spécifier explicitement ce type sur votre modèle.</span><span class="sxs-lookup"><span data-stu-id="e09b8-107">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="e09b8-108">Par exemple, si vous spécifiez uniquement le type de base d’une hiérarchie, EF Core n’inclura pas tous ses sous-types de manière implicite.</span><span class="sxs-lookup"><span data-stu-id="e09b8-108">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="e09b8-109">L’exemple suivant expose un DbSet pour `Blog` et sa sous-classe `RssBlog` .</span><span class="sxs-lookup"><span data-stu-id="e09b8-109">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="e09b8-110">Si `Blog` a une autre sous-classe, il ne sera pas inclus dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="e09b8-110">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> <span data-ttu-id="e09b8-111">Les colonnes de base de données deviennent automatiquement Nullable si nécessaire lors de l’utilisation du mappage TPH.</span><span class="sxs-lookup"><span data-stu-id="e09b8-111">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="e09b8-112">Par exemple, la `RssUrl` colonne accepte les valeurs NULL, car les instances régulières `Blog` n’ont pas cette propriété.</span><span class="sxs-lookup"><span data-stu-id="e09b8-112">For example, the `RssUrl` column is nullable because regular `Blog` instances do not have that property.</span></span>

<span data-ttu-id="e09b8-113">Si vous ne souhaitez pas exposer un `DbSet` pour une ou plusieurs entités dans la hiérarchie, vous pouvez également utiliser l’API Fluent pour vous assurer qu’elles sont incluses dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="e09b8-113">If you don't want to expose a `DbSet` for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="e09b8-114">Si vous ne vous fiez pas aux conventions, vous pouvez spécifier explicitement le type de base à l’aide de `HasBaseType` .</span><span class="sxs-lookup"><span data-stu-id="e09b8-114">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="e09b8-115">Vous pouvez également utiliser `.HasBaseType((Type)null)` pour supprimer un type d’entité de la hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="e09b8-115">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="table-per-hierarchy-and-discriminator-configuration"></a><span data-ttu-id="e09b8-116">Configuration TPH (table par hiérarchie) et discriminateur</span><span class="sxs-lookup"><span data-stu-id="e09b8-116">Table-per-hierarchy and discriminator configuration</span></span>

<span data-ttu-id="e09b8-117">Par défaut, EF mappe l’héritage à l’aide du modèle TPH ( *table par hiérarchie* ).</span><span class="sxs-lookup"><span data-stu-id="e09b8-117">By default, EF maps the inheritance using the *table-per-hierarchy* (TPH) pattern.</span></span> <span data-ttu-id="e09b8-118">TPH utilise une table unique pour stocker les données de tous les types dans la hiérarchie, et une colonne de discriminateur est utilisée pour identifier le type représenté par chaque ligne.</span><span class="sxs-lookup"><span data-stu-id="e09b8-118">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="e09b8-119">Le modèle ci-dessus est mappé au schéma de base de données suivant (Notez la colonne créée implicitement `Discriminator` , qui identifie le type de qui `Blog` est stocké dans chaque ligne).</span><span class="sxs-lookup"><span data-stu-id="e09b8-119">The model above is mapped to the following database schema (note the implicitly-created `Discriminator` column, which identifies which type of `Blog` is stored in each row).</span></span>

![image](_static/inheritance-tph-data.png)

<span data-ttu-id="e09b8-121">Vous pouvez configurer le nom et le type de la colonne de discriminateur et les valeurs utilisées pour identifier chaque type dans la hiérarchie :</span><span class="sxs-lookup"><span data-stu-id="e09b8-121">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="e09b8-122">Dans les exemples ci-dessus, EF a ajouté le discriminateur implicitement en tant que [propriété Shadow](xref:core/modeling/shadow-properties) sur l’entité de base de la hiérarchie.</span><span class="sxs-lookup"><span data-stu-id="e09b8-122">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="e09b8-123">Cette propriété peut être configurée comme n’importe quelle autre :</span><span class="sxs-lookup"><span data-stu-id="e09b8-123">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="e09b8-124">Enfin, le discriminateur peut également être mappé à une propriété .NET normale dans votre entité :</span><span class="sxs-lookup"><span data-stu-id="e09b8-124">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

### <a name="shared-columns"></a><span data-ttu-id="e09b8-125">Colonnes partagées</span><span class="sxs-lookup"><span data-stu-id="e09b8-125">Shared columns</span></span>

<span data-ttu-id="e09b8-126">Par défaut, lorsque deux types d’entités frères dans la hiérarchie ont une propriété portant le même nom, ils sont mappés à deux colonnes distinctes.</span><span class="sxs-lookup"><span data-stu-id="e09b8-126">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="e09b8-127">Toutefois, si leur type est identique, ils peuvent être mappés à la même colonne de base de données :</span><span class="sxs-lookup"><span data-stu-id="e09b8-127">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a><span data-ttu-id="e09b8-128">Configuration TPT (table par type)</span><span class="sxs-lookup"><span data-stu-id="e09b8-128">Table-per-type configuration</span></span>

> [!NOTE]
> <span data-ttu-id="e09b8-129">La table par type (TPT) est une nouvelle fonctionnalité de EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="e09b8-129">The table-per-type (TPT) is a new feature in EF Core 5.0.</span></span> <span data-ttu-id="e09b8-130">Le type de table par point concret (TPC) est pris en charge par EF6, mais n’est pas encore pris en charge par EF Core.</span><span class="sxs-lookup"><span data-stu-id="e09b8-130">Table-per-concrete-type (TPC) is supported by EF6, but is not yet supported by EF Core.</span></span>

<span data-ttu-id="e09b8-131">Dans le modèle de mappage TPT, tous les types sont mappés à des tables individuelles.</span><span class="sxs-lookup"><span data-stu-id="e09b8-131">In the TPT mapping pattern, all the types are mapped to individual tables.</span></span> <span data-ttu-id="e09b8-132">Les propriétés qui appartiennent uniquement à un type de base ou à un type dérivé sont stockées dans une table qui mappe à ce type.</span><span class="sxs-lookup"><span data-stu-id="e09b8-132">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="e09b8-133">Les tables mappées à des types dérivés stockent également une clé étrangère qui joint la table dérivée à la table de base.</span><span class="sxs-lookup"><span data-stu-id="e09b8-133">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

<span data-ttu-id="e09b8-134">EF crée le schéma de base de données suivant pour le modèle ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="e09b8-134">EF will create the following database schema for the model above.</span></span>

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> <span data-ttu-id="e09b8-135">Si la contrainte de clé primaire est renommée, le nouveau nom est appliqué à toutes les tables mappées à la hiérarchie. les futures versions EF autorisent le changement de nom de la contrainte uniquement pour une table particulière lorsque le [problème 19970](https://github.com/dotnet/efcore/issues/19970) est résolu.</span><span class="sxs-lookup"><span data-stu-id="e09b8-135">If the primary key constraint is renamed the new name will be applied to all the tables mapped to the hierarchy, future EF versions will allow renaming the constraint only for a particular table when [issue 19970](https://github.com/dotnet/efcore/issues/19970) is fixed.</span></span>

<span data-ttu-id="e09b8-136">Si vous employez la configuration en bloc, vous pouvez récupérer le nom de colonne d’une table spécifique en appelant <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A> .</span><span class="sxs-lookup"><span data-stu-id="e09b8-136">If you are employing bulk configuration you can retrieve the column name for a specific table by calling <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]
