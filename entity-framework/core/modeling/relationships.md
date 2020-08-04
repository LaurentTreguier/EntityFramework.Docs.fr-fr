---
title: Relations-EF Core
description: Comment configurer des relations entre des types d’entités lors de l’utilisation de Entity Framework Core
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relationships
ms.openlocfilehash: 927457c2a5b5ef4a5061fe2e5d28f864eb95c55f
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526730"
---
# <a name="relationships"></a><span data-ttu-id="aabec-103">Relations</span><span class="sxs-lookup"><span data-stu-id="aabec-103">Relationships</span></span>

<span data-ttu-id="aabec-104">Une relation définit la relation entre deux entités.</span><span class="sxs-lookup"><span data-stu-id="aabec-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="aabec-105">Dans une base de données relationnelle, il est représenté par une contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="aabec-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="aabec-106">La plupart des exemples de cet article utilisent une relation un-à-plusieurs pour illustrer les concepts.</span><span class="sxs-lookup"><span data-stu-id="aabec-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="aabec-107">Pour obtenir des exemples de relations un-à-un et plusieurs-à-plusieurs, consultez la section [autres modèles de relation](#other-relationship-patterns) à la fin de l’article.</span><span class="sxs-lookup"><span data-stu-id="aabec-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="aabec-108">Définition des termes</span><span class="sxs-lookup"><span data-stu-id="aabec-108">Definition of terms</span></span>

<span data-ttu-id="aabec-109">Il existe un certain nombre de termes utilisés pour décrire les relations</span><span class="sxs-lookup"><span data-stu-id="aabec-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="aabec-110">**Entité dépendante :** Il s’agit de l’entité qui contient les propriétés de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="aabec-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="aabec-111">Parfois appelé « enfant » de la relation.</span><span class="sxs-lookup"><span data-stu-id="aabec-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="aabec-112">**Entité principale :** Il s’agit de l’entité qui contient les propriétés de clé primaire/secondaire.</span><span class="sxs-lookup"><span data-stu-id="aabec-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="aabec-113">Parfois appelé « parent » de la relation.</span><span class="sxs-lookup"><span data-stu-id="aabec-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="aabec-114">**Clé principale :** Propriétés qui identifient de façon unique l’entité principale.</span><span class="sxs-lookup"><span data-stu-id="aabec-114">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="aabec-115">Il peut s’agir de la clé primaire ou d’une clé secondaire.</span><span class="sxs-lookup"><span data-stu-id="aabec-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="aabec-116">**Clé étrangère :** Propriétés de l’entité dépendante utilisées pour stocker les valeurs de clé principale de l’entité associée.</span><span class="sxs-lookup"><span data-stu-id="aabec-116">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="aabec-117">**Propriété de navigation :** Propriété définie sur le principal et/ou sur l’entité dépendante qui référence l’entité associée.</span><span class="sxs-lookup"><span data-stu-id="aabec-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="aabec-118">**Propriété de navigation de la collection :** Propriété de navigation qui contient des références à de nombreuses entités associées.</span><span class="sxs-lookup"><span data-stu-id="aabec-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="aabec-119">**Propriété de navigation de référence :** Propriété de navigation qui contient une référence à une entité associée unique.</span><span class="sxs-lookup"><span data-stu-id="aabec-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="aabec-120">**Propriété de navigation inverse :** Lorsque vous discutez d’une propriété de navigation particulière, ce terme fait référence à la propriété de navigation à l’autre extrémité de la relation.</span><span class="sxs-lookup"><span data-stu-id="aabec-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="aabec-121">**Relation à référence automatique :** Relation dans laquelle les types d’entités dependent et principal sont identiques.</span><span class="sxs-lookup"><span data-stu-id="aabec-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="aabec-122">Le code suivant illustre une relation un-à-plusieurs entre `Blog` et`Post`</span><span class="sxs-lookup"><span data-stu-id="aabec-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* <span data-ttu-id="aabec-123">`Post`est l’entité dépendante</span><span class="sxs-lookup"><span data-stu-id="aabec-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="aabec-124">`Blog`est l’entité principale</span><span class="sxs-lookup"><span data-stu-id="aabec-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="aabec-125">`Blog.BlogId`est la clé principale (dans le cas présent, il s’agit d’une clé primaire plutôt que d’une clé secondaire)</span><span class="sxs-lookup"><span data-stu-id="aabec-125">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="aabec-126">`Post.BlogId`est la clé étrangère</span><span class="sxs-lookup"><span data-stu-id="aabec-126">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="aabec-127">`Post.Blog`est une propriété de navigation de référence</span><span class="sxs-lookup"><span data-stu-id="aabec-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="aabec-128">`Blog.Posts`est une propriété de navigation de collection</span><span class="sxs-lookup"><span data-stu-id="aabec-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="aabec-129">`Post.Blog`est la propriété de navigation inverse de `Blog.Posts` (et vice versa)</span><span class="sxs-lookup"><span data-stu-id="aabec-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="aabec-130">Conventions</span><span class="sxs-lookup"><span data-stu-id="aabec-130">Conventions</span></span>

<span data-ttu-id="aabec-131">Par défaut, une relation est créée lorsqu’une propriété de navigation est détectée sur un type.</span><span class="sxs-lookup"><span data-stu-id="aabec-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="aabec-132">Une propriété est considérée comme une propriété de navigation si le type vers lequel elle pointe ne peut pas être mappé en tant que type scalaire par le fournisseur de base de données actuel.</span><span class="sxs-lookup"><span data-stu-id="aabec-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="aabec-133">Les relations découvertes par convention cibleront toujours la clé primaire de l’entité principale.</span><span class="sxs-lookup"><span data-stu-id="aabec-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="aabec-134">Pour cibler une clé secondaire, une configuration supplémentaire doit être effectuée à l’aide de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="aabec-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="aabec-135">Relations entièrement définies</span><span class="sxs-lookup"><span data-stu-id="aabec-135">Fully defined relationships</span></span>

<span data-ttu-id="aabec-136">Le modèle le plus courant pour les relations est d’avoir des propriétés de navigation définies aux deux extrémités de la relation et une propriété de clé étrangère définie dans la classe d’entité dépendante.</span><span class="sxs-lookup"><span data-stu-id="aabec-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="aabec-137">Si une paire de propriétés de navigation est trouvée entre deux types, ils sont configurés en tant que propriétés de navigation inverse de la même relation.</span><span class="sxs-lookup"><span data-stu-id="aabec-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="aabec-138">Si l’entité dépendante contient une propriété dont le nom correspond à l’un de ces modèles, elle est configurée en tant que clé étrangère :</span><span class="sxs-lookup"><span data-stu-id="aabec-138">If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

<span data-ttu-id="aabec-139">Dans cet exemple, les propriétés mises en surbrillance sont utilisées pour configurer la relation.</span><span class="sxs-lookup"><span data-stu-id="aabec-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="aabec-140">Si la propriété est la clé primaire ou si elle est d’un type qui n’est pas compatible avec la clé principale, elle ne sera pas configurée en tant que clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="aabec-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="aabec-141">Avant le EF Core 3,0, la propriété nommée exactement comme la propriété de clé principale [était également mise en correspondance comme clé étrangère](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span><span class="sxs-lookup"><span data-stu-id="aabec-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="aabec-142">Aucune propriété de clé étrangère</span><span class="sxs-lookup"><span data-stu-id="aabec-142">No foreign key property</span></span>

<span data-ttu-id="aabec-143">Bien qu’il soit recommandé d’avoir une propriété de clé étrangère définie dans la classe d’entité dépendante, elle n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="aabec-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="aabec-144">Si aucune propriété de clé étrangère n’est trouvée, une [propriété de clé étrangère Shadow](shadow-properties.md) est introduite avec le nom `<navigation property name><principal key property name>` ou `<principal entity name><principal key property name>` si aucune navigation n’est présente sur le type dépendant.</span><span class="sxs-lookup"><span data-stu-id="aabec-144">If no foreign key property is found, a [shadow foreign key property](shadow-properties.md) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

<span data-ttu-id="aabec-145">Dans cet exemple, la clé étrangère Shadow est en `BlogId` raison du fait que le nom de navigation est redondant.</span><span class="sxs-lookup"><span data-stu-id="aabec-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="aabec-146">Si une propriété du même nom existe déjà, le nom de la propriété Shadow sera suivi d’un nombre.</span><span class="sxs-lookup"><span data-stu-id="aabec-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="aabec-147">Propriété de navigation unique</span><span class="sxs-lookup"><span data-stu-id="aabec-147">Single navigation property</span></span>

<span data-ttu-id="aabec-148">L’inclusion d’une seule propriété de navigation (aucune navigation inverse et aucune propriété de clé étrangère) suffit à avoir une relation définie par Convention.</span><span class="sxs-lookup"><span data-stu-id="aabec-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="aabec-149">Vous pouvez également avoir une propriété de navigation unique et une propriété de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="aabec-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="aabec-150">Limites</span><span class="sxs-lookup"><span data-stu-id="aabec-150">Limitations</span></span>

<span data-ttu-id="aabec-151">Lorsqu’il existe plusieurs propriétés de navigation définies entre deux types (autrement dit, plusieurs paires de navigation qui pointent les unes vers les autres), les relations représentées par les propriétés de navigation sont ambiguës.</span><span class="sxs-lookup"><span data-stu-id="aabec-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="aabec-152">Vous devrez les configurer manuellement pour résoudre l’ambiguïté.</span><span class="sxs-lookup"><span data-stu-id="aabec-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="aabec-153">Suppression en cascade</span><span class="sxs-lookup"><span data-stu-id="aabec-153">Cascade delete</span></span>

<span data-ttu-id="aabec-154">Par Convention, la suppression en cascade sera définie sur *cascade* pour les relations requises et *ClientSetNull* pour les relations facultatives.</span><span class="sxs-lookup"><span data-stu-id="aabec-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="aabec-155">*Cascade* signifie que les entités dépendantes sont également supprimées.</span><span class="sxs-lookup"><span data-stu-id="aabec-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="aabec-156">*ClientSetNull* signifie que les entités dépendantes qui ne sont pas chargées en mémoire restent inchangées et doivent être supprimées manuellement ou mises à jour pour pointer vers une entité principale valide.</span><span class="sxs-lookup"><span data-stu-id="aabec-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="aabec-157">Pour les entités chargées en mémoire, EF Core tente de définir les propriétés de clé étrangère sur la valeur null.</span><span class="sxs-lookup"><span data-stu-id="aabec-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="aabec-158">Consultez la section [relations obligatoires et facultatives](#required-and-optional-relationships) pour connaître la différence entre les relations obligatoires et facultatives.</span><span class="sxs-lookup"><span data-stu-id="aabec-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="aabec-159">Pour plus d’informations sur les différents comportements de suppression et les valeurs par défaut utilisées par Convention, consultez [suppression en cascade](../saving/cascade-delete.md) .</span><span class="sxs-lookup"><span data-stu-id="aabec-159">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="aabec-160">Configuration manuelle</span><span class="sxs-lookup"><span data-stu-id="aabec-160">Manual configuration</span></span>

### <a name="fluent-api"></a>[<span data-ttu-id="aabec-161">API Fluent</span><span class="sxs-lookup"><span data-stu-id="aabec-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="aabec-162">Pour configurer une relation dans l’API Fluent, commencez par identifier les propriétés de navigation qui composent la relation.</span><span class="sxs-lookup"><span data-stu-id="aabec-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="aabec-163">`HasOne`ou `HasMany` identifie la propriété de navigation sur le type d’entité sur lequel vous commencez la configuration.</span><span class="sxs-lookup"><span data-stu-id="aabec-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="aabec-164">Vous enchaînez ensuite un appel à `WithOne` ou `WithMany` pour identifier la navigation inverse.</span><span class="sxs-lookup"><span data-stu-id="aabec-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="aabec-165">`HasOne`/`WithOne`sont utilisés pour les propriétés de navigation de référence et `HasMany` / `WithMany` sont utilisés pour les propriétés de navigation de collection.</span><span class="sxs-lookup"><span data-stu-id="aabec-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[<span data-ttu-id="aabec-166">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="aabec-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="aabec-167">Vous pouvez utiliser les annotations de données pour configurer la combinaison des propriétés de navigation sur les entités dépendantes et principales.</span><span class="sxs-lookup"><span data-stu-id="aabec-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="aabec-168">Cela s’effectue généralement quand il existe plusieurs paires de propriétés de navigation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="aabec-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> <span data-ttu-id="aabec-169">Vous pouvez uniquement utiliser [required] sur les propriétés de l’entité dépendante pour avoir un impact sur la nécessité de la relation.</span><span class="sxs-lookup"><span data-stu-id="aabec-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="aabec-170">[Obligatoire] dans la navigation de l’entité principale, est généralement ignoré, mais l’entité peut devenir la dépendante.</span><span class="sxs-lookup"><span data-stu-id="aabec-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="aabec-171">Les annotations `[ForeignKey]` de données et `[InverseProperty]` sont disponibles dans l' `System.ComponentModel.DataAnnotations.Schema` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="aabec-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="aabec-172">`[Required]`est disponible dans l' `System.ComponentModel.DataAnnotations` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="aabec-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="aabec-173">Propriété de navigation unique</span><span class="sxs-lookup"><span data-stu-id="aabec-173">Single navigation property</span></span>

<span data-ttu-id="aabec-174">Si vous n’avez qu’une seule propriété de navigation, il y a des surcharges sans paramètre de `WithOne` et `WithMany` .</span><span class="sxs-lookup"><span data-stu-id="aabec-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="aabec-175">Cela indique qu’il existe conceptuellement une référence ou une collection à l’autre extrémité de la relation, mais qu’il n’y a aucune propriété de navigation incluse dans la classe d’entité.</span><span class="sxs-lookup"><span data-stu-id="aabec-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a><span data-ttu-id="aabec-176">Configuration des propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="aabec-176">Configuring navigation properties</span></span>

<span data-ttu-id="aabec-177">Une fois la propriété de navigation créée, vous devrez peut-être la configurer ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="aabec-177">After the navigation property has been created, you may need to further configure it.</span></span> <span data-ttu-id="aabec-178">Dans EFCore 5,0, une nouvelle API Fluent est ajoutée pour vous permettre d’effectuer cette configuration.</span><span class="sxs-lookup"><span data-stu-id="aabec-178">In EFCore 5.0, new Fluent API is added to allow you to perform that configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> <span data-ttu-id="aabec-179">Cet appel ne peut pas être utilisé pour créer une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="aabec-179">This call cannot be used to create a navigation property.</span></span> <span data-ttu-id="aabec-180">Elle est utilisée uniquement pour configurer une propriété de navigation qui a été créée précédemment en définissant une relation ou à partir d’une convention.</span><span class="sxs-lookup"><span data-stu-id="aabec-180">It is only used to configure a navigation property which has been previously created by defining a relationship or from a convention.</span></span>

### <a name="foreign-key"></a><span data-ttu-id="aabec-181">Clé étrangère</span><span class="sxs-lookup"><span data-stu-id="aabec-181">Foreign key</span></span>

#### <a name="fluent-api-simple-key"></a>[<span data-ttu-id="aabec-182">API Fluent (clé simple)</span><span class="sxs-lookup"><span data-stu-id="aabec-182">Fluent API (simple key)</span></span>](#tab/fluent-api-simple-key)

<span data-ttu-id="aabec-183">Vous pouvez utiliser l’API Fluent pour configurer la propriété à utiliser comme propriété de clé étrangère pour une relation donnée :</span><span class="sxs-lookup"><span data-stu-id="aabec-183">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[<span data-ttu-id="aabec-184">API Fluent (clé composite)</span><span class="sxs-lookup"><span data-stu-id="aabec-184">Fluent API (composite key)</span></span>](#tab/fluent-api-composite-key)

<span data-ttu-id="aabec-185">Vous pouvez utiliser l’API Fluent pour configurer les propriétés à utiliser comme propriétés de clé étrangère composite pour une relation donnée :</span><span class="sxs-lookup"><span data-stu-id="aabec-185">You can use the Fluent API to configure which properties should be used as the composite foreign key properties for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[<span data-ttu-id="aabec-186">Annotations de données (clé simple)</span><span class="sxs-lookup"><span data-stu-id="aabec-186">Data annotations (simple key)</span></span>](#tab/data-annotations-simple-key)

<span data-ttu-id="aabec-187">Vous pouvez utiliser les annotations de données pour configurer la propriété à utiliser comme propriété de clé étrangère pour une relation donnée.</span><span class="sxs-lookup"><span data-stu-id="aabec-187">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="aabec-188">Cela s’effectue généralement lorsque la propriété de clé étrangère n’est pas détectée par Convention :</span><span class="sxs-lookup"><span data-stu-id="aabec-188">This is typically done when the foreign key property is not discovered by convention:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> <span data-ttu-id="aabec-189">L' `[ForeignKey]` annotation peut être placée sur l’une des propriétés de navigation de la relation.</span><span class="sxs-lookup"><span data-stu-id="aabec-189">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="aabec-190">Elle n’a pas besoin de se trouver dans la propriété de navigation de la classe d’entité dépendante.</span><span class="sxs-lookup"><span data-stu-id="aabec-190">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="aabec-191">La propriété spécifiée à l’aide `[ForeignKey]` de sur une propriété de navigation n’a pas besoin d’exister sur le type dépendant.</span><span class="sxs-lookup"><span data-stu-id="aabec-191">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist on the dependent type.</span></span> <span data-ttu-id="aabec-192">Dans ce cas, le nom spécifié est utilisé pour créer une clé étrangère cachée.</span><span class="sxs-lookup"><span data-stu-id="aabec-192">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

#### <a name="shadow-foreign-key"></a><span data-ttu-id="aabec-193">Masquer la clé étrangère</span><span class="sxs-lookup"><span data-stu-id="aabec-193">Shadow foreign key</span></span>

<span data-ttu-id="aabec-194">Vous pouvez utiliser la surcharge de chaîne de `HasForeignKey(...)` pour configurer une propriété Shadow comme clé étrangère (pour plus d’informations, consultez [Propriétés Shadow](shadow-properties.md) ).</span><span class="sxs-lookup"><span data-stu-id="aabec-194">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="aabec-195">Nous vous recommandons d’ajouter explicitement la propriété Shadow au modèle avant de l’utiliser comme clé étrangère (comme indiqué ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="aabec-195">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a><span data-ttu-id="aabec-196">Nom de la contrainte de clé étrangère</span><span class="sxs-lookup"><span data-stu-id="aabec-196">Foreign key constraint name</span></span>

<span data-ttu-id="aabec-197">Par Convention, lorsque vous ciblez une base de données relationnelle, les contraintes de clé étrangère sont nommées FK_ <dependent type name> _<principal type name>_ <foreign key property name> .</span><span class="sxs-lookup"><span data-stu-id="aabec-197">By convention, when targeting a relational database, foreign key constraints are named FK_<dependent type name>_<principal type name>_<foreign key property name>.</span></span> <span data-ttu-id="aabec-198">Pour les clés étrangères composites <foreign key property name> devient une liste de noms de propriétés de clé étrangère séparés par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="aabec-198">For composite foreign keys <foreign key property name> becomes an underscore separated list of foreign key property names.</span></span>

<span data-ttu-id="aabec-199">Vous pouvez également configurer le nom de la contrainte comme suit :</span><span class="sxs-lookup"><span data-stu-id="aabec-199">You can also configure the constraint name as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a><span data-ttu-id="aabec-200">Sans propriété de navigation</span><span class="sxs-lookup"><span data-stu-id="aabec-200">Without navigation property</span></span>

<span data-ttu-id="aabec-201">Vous n’avez pas nécessairement besoin de fournir une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="aabec-201">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="aabec-202">Vous pouvez simplement fournir une clé étrangère d’un côté de la relation.</span><span class="sxs-lookup"><span data-stu-id="aabec-202">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a><span data-ttu-id="aabec-203">Clé principale</span><span class="sxs-lookup"><span data-stu-id="aabec-203">Principal key</span></span>

<span data-ttu-id="aabec-204">Si vous souhaitez que la clé étrangère fasse référence à une propriété autre que la clé primaire, vous pouvez utiliser l’API Fluent pour configurer la propriété de clé principale de la relation.</span><span class="sxs-lookup"><span data-stu-id="aabec-204">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="aabec-205">La propriété que vous configurez en tant que clé principale est automatiquement configurée en tant que [clé secondaire](alternate-keys.md).</span><span class="sxs-lookup"><span data-stu-id="aabec-205">The property that you configure as the principal key will automatically be setup as an [alternate key](alternate-keys.md).</span></span>

#### <a name="simple-key"></a>[<span data-ttu-id="aabec-206">Clé simple</span><span class="sxs-lookup"><span data-stu-id="aabec-206">Simple key</span></span>](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[<span data-ttu-id="aabec-207">Clé composite</span><span class="sxs-lookup"><span data-stu-id="aabec-207">Composite key</span></span>](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="aabec-208">L’ordre dans lequel vous spécifiez les propriétés de clé principale doit correspondre à l’ordre dans lequel elles sont spécifiées pour la clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="aabec-208">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

---

### <a name="required-and-optional-relationships"></a><span data-ttu-id="aabec-209">Relations obligatoires et facultatives</span><span class="sxs-lookup"><span data-stu-id="aabec-209">Required and optional relationships</span></span>

<span data-ttu-id="aabec-210">Vous pouvez utiliser l’API Fluent pour déterminer si la relation est obligatoire ou facultative.</span><span class="sxs-lookup"><span data-stu-id="aabec-210">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="aabec-211">Au final, cela contrôle si la propriété de clé étrangère est obligatoire ou facultative.</span><span class="sxs-lookup"><span data-stu-id="aabec-211">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="aabec-212">Cela est particulièrement utile lorsque vous utilisez une clé étrangère d’État Shadow.</span><span class="sxs-lookup"><span data-stu-id="aabec-212">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="aabec-213">Si vous avez une propriété de clé étrangère dans votre classe d’entité, le caractère requis de la relation est déterminé selon que la propriété de clé étrangère est obligatoire ou facultative (pour plus d’informations, consultez [propriétés requises et facultatives](required-optional.md) ).</span><span class="sxs-lookup"><span data-stu-id="aabec-213">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> <span data-ttu-id="aabec-214">`IsRequired(false)`L’appel rend également la propriété de clé étrangère facultative, sauf si elle est configurée dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="aabec-214">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="aabec-215">Suppression en cascade</span><span class="sxs-lookup"><span data-stu-id="aabec-215">Cascade delete</span></span>

<span data-ttu-id="aabec-216">Vous pouvez utiliser l’API Fluent pour configurer explicitement le comportement de suppression en cascade pour une relation donnée.</span><span class="sxs-lookup"><span data-stu-id="aabec-216">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="aabec-217">Pour une présentation détaillée de chaque option, consultez [suppression en cascade](../saving/cascade-delete.md) .</span><span class="sxs-lookup"><span data-stu-id="aabec-217">See [Cascade Delete](../saving/cascade-delete.md) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="aabec-218">Autres modèles de relation</span><span class="sxs-lookup"><span data-stu-id="aabec-218">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="aabec-219">Un à un</span><span class="sxs-lookup"><span data-stu-id="aabec-219">One-to-one</span></span>

<span data-ttu-id="aabec-220">Les relations un-à-un ont une propriété de navigation de référence des deux côtés.</span><span class="sxs-lookup"><span data-stu-id="aabec-220">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="aabec-221">Ils suivent les mêmes conventions que les relations un-à-plusieurs, mais un index unique est introduit sur la propriété de clé étrangère pour s’assurer qu’un seul dépendant est lié à chaque principal.</span><span class="sxs-lookup"><span data-stu-id="aabec-221">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> <span data-ttu-id="aabec-222">EF choisit l’une des entités à dépendre en fonction de sa capacité à détecter une propriété de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="aabec-222">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="aabec-223">Si l’entité incorrecte est choisie comme dépendant, vous pouvez utiliser l’API Fluent pour corriger ce problème.</span><span class="sxs-lookup"><span data-stu-id="aabec-223">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="aabec-224">Quand vous configurez la relation avec l’API Fluent, vous utilisez les `HasOne` `WithOne` méthodes et.</span><span class="sxs-lookup"><span data-stu-id="aabec-224">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="aabec-225">Quand vous configurez la clé étrangère, vous devez spécifier le type d’entité dépendant. Notez le paramètre générique fourni à `HasForeignKey` dans la liste ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="aabec-225">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="aabec-226">Dans une relation un-à-plusieurs, il est clair que l’entité avec la navigation de référence est la dépendance et celle avec la collection est le principal.</span><span class="sxs-lookup"><span data-stu-id="aabec-226">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="aabec-227">Mais ce n’est pas le cas dans une relation un-à-un, c’est pourquoi il est nécessaire de la définir explicitement.</span><span class="sxs-lookup"><span data-stu-id="aabec-227">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a><span data-ttu-id="aabec-228">Plusieurs à plusieurs</span><span class="sxs-lookup"><span data-stu-id="aabec-228">Many-to-many</span></span>

<span data-ttu-id="aabec-229">Les relations plusieurs-à-plusieurs sans classe d’entité pour représenter la table de jointure ne sont pas encore prises en charge.</span><span class="sxs-lookup"><span data-stu-id="aabec-229">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="aabec-230">Toutefois, vous pouvez représenter une relation plusieurs-à-plusieurs en incluant une classe d’entité pour la table de jointure et en mappant deux relations un-à-plusieurs distinctes.</span><span class="sxs-lookup"><span data-stu-id="aabec-230">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]
