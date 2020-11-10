---
title: Relations-EF Core
description: Comment configurer des relations entre des types d’entités lors de l’utilisation de Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/relationships
ms.openlocfilehash: 567d212ddf41f33ee32443d85d2a17234fbc026b
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430181"
---
# <a name="relationships"></a><span data-ttu-id="4a094-103">Relations</span><span class="sxs-lookup"><span data-stu-id="4a094-103">Relationships</span></span>

<span data-ttu-id="4a094-104">Une relation définit la relation entre deux entités.</span><span class="sxs-lookup"><span data-stu-id="4a094-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="4a094-105">Dans une base de données relationnelle, il est représenté par une contrainte de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="4a094-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="4a094-106">La plupart des exemples de cet article utilisent une relation un-à-plusieurs pour illustrer les concepts.</span><span class="sxs-lookup"><span data-stu-id="4a094-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="4a094-107">Pour obtenir des exemples de relations un-à-un et plusieurs-à-plusieurs, consultez la section [autres modèles de relation](#other-relationship-patterns) à la fin de l’article.</span><span class="sxs-lookup"><span data-stu-id="4a094-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="4a094-108">Définition des termes</span><span class="sxs-lookup"><span data-stu-id="4a094-108">Definition of terms</span></span>

<span data-ttu-id="4a094-109">Il existe un certain nombre de termes utilisés pour décrire les relations</span><span class="sxs-lookup"><span data-stu-id="4a094-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="4a094-110">**Entité dépendante :** Il s’agit de l’entité qui contient les propriétés de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="4a094-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="4a094-111">Parfois appelé « enfant » de la relation.</span><span class="sxs-lookup"><span data-stu-id="4a094-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="4a094-112">**Entité principale :** Il s’agit de l’entité qui contient les propriétés de clé primaire/secondaire.</span><span class="sxs-lookup"><span data-stu-id="4a094-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="4a094-113">Parfois appelé « parent » de la relation.</span><span class="sxs-lookup"><span data-stu-id="4a094-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="4a094-114">**Clé principale :** Propriétés qui identifient de façon unique l’entité principale.</span><span class="sxs-lookup"><span data-stu-id="4a094-114">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="4a094-115">Il peut s’agir de la clé primaire ou d’une clé secondaire.</span><span class="sxs-lookup"><span data-stu-id="4a094-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="4a094-116">**Clé étrangère :** Propriétés de l’entité dépendante utilisées pour stocker les valeurs de clé principale de l’entité associée.</span><span class="sxs-lookup"><span data-stu-id="4a094-116">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="4a094-117">**Propriété de navigation :** Propriété définie sur le principal et/ou sur l’entité dépendante qui référence l’entité associée.</span><span class="sxs-lookup"><span data-stu-id="4a094-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="4a094-118">**Propriété de navigation de la collection :** Propriété de navigation qui contient des références à de nombreuses entités associées.</span><span class="sxs-lookup"><span data-stu-id="4a094-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="4a094-119">**Propriété de navigation de référence :** Propriété de navigation qui contient une référence à une entité associée unique.</span><span class="sxs-lookup"><span data-stu-id="4a094-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="4a094-120">**Propriété de navigation inverse :** Lorsque vous discutez d’une propriété de navigation particulière, ce terme fait référence à la propriété de navigation à l’autre extrémité de la relation.</span><span class="sxs-lookup"><span data-stu-id="4a094-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="4a094-121">**Relation à référence automatique :** Relation dans laquelle les types d’entités dependent et principal sont identiques.</span><span class="sxs-lookup"><span data-stu-id="4a094-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="4a094-122">Le code suivant illustre une relation un-à-plusieurs entre `Blog` et `Post`</span><span class="sxs-lookup"><span data-stu-id="4a094-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* <span data-ttu-id="4a094-123">`Post` est l’entité dépendante</span><span class="sxs-lookup"><span data-stu-id="4a094-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="4a094-124">`Blog` est l’entité principale</span><span class="sxs-lookup"><span data-stu-id="4a094-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="4a094-125">`Blog.BlogId` est la clé principale (dans le cas présent, il s’agit d’une clé primaire plutôt que d’une clé secondaire)</span><span class="sxs-lookup"><span data-stu-id="4a094-125">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="4a094-126">`Post.BlogId` est la clé étrangère</span><span class="sxs-lookup"><span data-stu-id="4a094-126">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="4a094-127">`Post.Blog` est une propriété de navigation de référence</span><span class="sxs-lookup"><span data-stu-id="4a094-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="4a094-128">`Blog.Posts` est une propriété de navigation de collection</span><span class="sxs-lookup"><span data-stu-id="4a094-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="4a094-129">`Post.Blog` est la propriété de navigation inverse de `Blog.Posts` (et vice versa)</span><span class="sxs-lookup"><span data-stu-id="4a094-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="4a094-130">Conventions</span><span class="sxs-lookup"><span data-stu-id="4a094-130">Conventions</span></span>

<span data-ttu-id="4a094-131">Par défaut, une relation est créée lorsqu’une propriété de navigation est détectée sur un type.</span><span class="sxs-lookup"><span data-stu-id="4a094-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="4a094-132">Une propriété est considérée comme une propriété de navigation si le type vers lequel elle pointe ne peut pas être mappé en tant que type scalaire par le fournisseur de base de données actuel.</span><span class="sxs-lookup"><span data-stu-id="4a094-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="4a094-133">Les relations découvertes par convention cibleront toujours la clé primaire de l’entité principale.</span><span class="sxs-lookup"><span data-stu-id="4a094-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="4a094-134">Pour cibler une clé secondaire, une configuration supplémentaire doit être effectuée à l’aide de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="4a094-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="4a094-135">Relations entièrement définies</span><span class="sxs-lookup"><span data-stu-id="4a094-135">Fully defined relationships</span></span>

<span data-ttu-id="4a094-136">Le modèle le plus courant pour les relations est d’avoir des propriétés de navigation définies aux deux extrémités de la relation et une propriété de clé étrangère définie dans la classe d’entité dépendante.</span><span class="sxs-lookup"><span data-stu-id="4a094-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="4a094-137">Si une paire de propriétés de navigation est trouvée entre deux types, ils sont configurés en tant que propriétés de navigation inverse de la même relation.</span><span class="sxs-lookup"><span data-stu-id="4a094-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="4a094-138">Si l’entité dépendante contient une propriété dont le nom correspond à l’un de ces modèles, elle est configurée en tant que clé étrangère :</span><span class="sxs-lookup"><span data-stu-id="4a094-138">If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

<span data-ttu-id="4a094-139">Dans cet exemple, les propriétés mises en surbrillance sont utilisées pour configurer la relation.</span><span class="sxs-lookup"><span data-stu-id="4a094-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="4a094-140">Si la propriété est la clé primaire ou si elle est d’un type qui n’est pas compatible avec la clé principale, elle ne sera pas configurée en tant que clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="4a094-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="4a094-141">Avant le EF Core 3,0, la propriété nommée exactement comme la propriété de clé principale [était également mise en correspondance comme clé étrangère](https://github.com/dotnet/efcore/issues/13274)</span><span class="sxs-lookup"><span data-stu-id="4a094-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/dotnet/efcore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="4a094-142">Aucune propriété de clé étrangère</span><span class="sxs-lookup"><span data-stu-id="4a094-142">No foreign key property</span></span>

<span data-ttu-id="4a094-143">Bien qu’il soit recommandé d’avoir une propriété de clé étrangère définie dans la classe d’entité dépendante, elle n’est pas obligatoire.</span><span class="sxs-lookup"><span data-stu-id="4a094-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="4a094-144">Si aucune propriété de clé étrangère n’est trouvée, une [propriété de clé étrangère Shadow](xref:core/modeling/shadow-properties) est introduite avec le nom `<navigation property name><principal key property name>` ou `<principal entity name><principal key property name>` si aucune navigation n’est présente sur le type dépendant.</span><span class="sxs-lookup"><span data-stu-id="4a094-144">If no foreign key property is found, a [shadow foreign key property](xref:core/modeling/shadow-properties) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

<span data-ttu-id="4a094-145">Dans cet exemple, la clé étrangère Shadow est en `BlogId` raison du fait que le nom de navigation est redondant.</span><span class="sxs-lookup"><span data-stu-id="4a094-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="4a094-146">Si une propriété du même nom existe déjà, le nom de la propriété Shadow sera suivi d’un nombre.</span><span class="sxs-lookup"><span data-stu-id="4a094-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="4a094-147">Propriété de navigation unique</span><span class="sxs-lookup"><span data-stu-id="4a094-147">Single navigation property</span></span>

<span data-ttu-id="4a094-148">L’inclusion d’une seule propriété de navigation (aucune navigation inverse et aucune propriété de clé étrangère) suffit à avoir une relation définie par Convention.</span><span class="sxs-lookup"><span data-stu-id="4a094-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="4a094-149">Vous pouvez également avoir une propriété de navigation unique et une propriété de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="4a094-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="4a094-150">Limites</span><span class="sxs-lookup"><span data-stu-id="4a094-150">Limitations</span></span>

<span data-ttu-id="4a094-151">Lorsqu’il existe plusieurs propriétés de navigation définies entre deux types (autrement dit, plusieurs paires de navigation qui pointent les unes vers les autres), les relations représentées par les propriétés de navigation sont ambiguës.</span><span class="sxs-lookup"><span data-stu-id="4a094-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="4a094-152">Vous devrez les configurer manuellement pour résoudre l’ambiguïté.</span><span class="sxs-lookup"><span data-stu-id="4a094-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="4a094-153">Suppression en cascade</span><span class="sxs-lookup"><span data-stu-id="4a094-153">Cascade delete</span></span>

<span data-ttu-id="4a094-154">Par Convention, la suppression en cascade sera définie sur *cascade* pour les relations requises et *ClientSetNull* pour les relations facultatives.</span><span class="sxs-lookup"><span data-stu-id="4a094-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="4a094-155">*Cascade* signifie que les entités dépendantes sont également supprimées.</span><span class="sxs-lookup"><span data-stu-id="4a094-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="4a094-156">*ClientSetNull* signifie que les entités dépendantes qui ne sont pas chargées en mémoire restent inchangées et doivent être supprimées manuellement ou mises à jour pour pointer vers une entité principale valide.</span><span class="sxs-lookup"><span data-stu-id="4a094-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="4a094-157">Pour les entités chargées en mémoire, EF Core tente de définir les propriétés de clé étrangère sur la valeur null.</span><span class="sxs-lookup"><span data-stu-id="4a094-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="4a094-158">Consultez la section [relations obligatoires et facultatives](#required-and-optional-relationships) pour connaître la différence entre les relations obligatoires et facultatives.</span><span class="sxs-lookup"><span data-stu-id="4a094-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="4a094-159">Pour plus d’informations sur les différents comportements de suppression et les valeurs par défaut utilisées par Convention, consultez [suppression en cascade](xref:core/saving/cascade-delete) .</span><span class="sxs-lookup"><span data-stu-id="4a094-159">See [Cascade Delete](xref:core/saving/cascade-delete) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="4a094-160">Configuration manuelle</span><span class="sxs-lookup"><span data-stu-id="4a094-160">Manual configuration</span></span>

### <a name="fluent-api"></a>[<span data-ttu-id="4a094-161">API Fluent</span><span class="sxs-lookup"><span data-stu-id="4a094-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="4a094-162">Pour configurer une relation dans l’API Fluent, commencez par identifier les propriétés de navigation qui composent la relation.</span><span class="sxs-lookup"><span data-stu-id="4a094-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="4a094-163">`HasOne` ou `HasMany` identifie la propriété de navigation sur le type d’entité sur lequel vous commencez la configuration.</span><span class="sxs-lookup"><span data-stu-id="4a094-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="4a094-164">Vous enchaînez ensuite un appel à `WithOne` ou `WithMany` pour identifier la navigation inverse.</span><span class="sxs-lookup"><span data-stu-id="4a094-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="4a094-165">`HasOne`/`WithOne`sont utilisés pour les propriétés de navigation de référence et `HasMany` / `WithMany` sont utilisés pour les propriétés de navigation de collection.</span><span class="sxs-lookup"><span data-stu-id="4a094-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[<span data-ttu-id="4a094-166">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="4a094-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="4a094-167">Vous pouvez utiliser les annotations de données pour configurer la combinaison des propriétés de navigation sur les entités dépendantes et principales.</span><span class="sxs-lookup"><span data-stu-id="4a094-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="4a094-168">Cela s’effectue généralement quand il existe plusieurs paires de propriétés de navigation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="4a094-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> <span data-ttu-id="4a094-169">Vous pouvez uniquement utiliser [required] sur les propriétés de l’entité dépendante pour avoir un impact sur la nécessité de la relation.</span><span class="sxs-lookup"><span data-stu-id="4a094-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="4a094-170">[Obligatoire] dans la navigation de l’entité principale, est généralement ignoré, mais l’entité peut devenir la dépendante.</span><span class="sxs-lookup"><span data-stu-id="4a094-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="4a094-171">Les annotations `[ForeignKey]` de données et `[InverseProperty]` sont disponibles dans l' `System.ComponentModel.DataAnnotations.Schema` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4a094-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="4a094-172">`[Required]` est disponible dans l' `System.ComponentModel.DataAnnotations` espace de noms.</span><span class="sxs-lookup"><span data-stu-id="4a094-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="4a094-173">Propriété de navigation unique</span><span class="sxs-lookup"><span data-stu-id="4a094-173">Single navigation property</span></span>

<span data-ttu-id="4a094-174">Si vous n’avez qu’une seule propriété de navigation, il y a des surcharges sans paramètre de `WithOne` et `WithMany` .</span><span class="sxs-lookup"><span data-stu-id="4a094-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="4a094-175">Cela indique qu’il existe conceptuellement une référence ou une collection à l’autre extrémité de la relation, mais qu’il n’y a aucune propriété de navigation incluse dans la classe d’entité.</span><span class="sxs-lookup"><span data-stu-id="4a094-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a><span data-ttu-id="4a094-176">Configuration des propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="4a094-176">Configuring navigation properties</span></span>

> [!NOTE]
> <span data-ttu-id="4a094-177">Cette fonctionnalité a été ajoutée dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="4a094-177">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="4a094-178">Une fois la propriété de navigation créée, vous devrez peut-être la configurer ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="4a094-178">After the navigation property has been created, you may need to further configure it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> <span data-ttu-id="4a094-179">Cet appel ne peut pas être utilisé pour créer une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="4a094-179">This call cannot be used to create a navigation property.</span></span> <span data-ttu-id="4a094-180">Elle est utilisée uniquement pour configurer une propriété de navigation qui a été créée précédemment en définissant une relation ou à partir d’une convention.</span><span class="sxs-lookup"><span data-stu-id="4a094-180">It is only used to configure a navigation property which has been previously created by defining a relationship or from a convention.</span></span>

### <a name="foreign-key"></a><span data-ttu-id="4a094-181">Clé étrangère</span><span class="sxs-lookup"><span data-stu-id="4a094-181">Foreign key</span></span>

#### <a name="fluent-api-simple-key"></a>[<span data-ttu-id="4a094-182">API Fluent (clé simple)</span><span class="sxs-lookup"><span data-stu-id="4a094-182">Fluent API (simple key)</span></span>](#tab/fluent-api-simple-key)

<span data-ttu-id="4a094-183">Vous pouvez utiliser l’API Fluent pour configurer la propriété à utiliser comme propriété de clé étrangère pour une relation donnée :</span><span class="sxs-lookup"><span data-stu-id="4a094-183">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[<span data-ttu-id="4a094-184">API Fluent (clé composite)</span><span class="sxs-lookup"><span data-stu-id="4a094-184">Fluent API (composite key)</span></span>](#tab/fluent-api-composite-key)

<span data-ttu-id="4a094-185">Vous pouvez utiliser l’API Fluent pour configurer les propriétés à utiliser comme propriétés de clé étrangère composite pour une relation donnée :</span><span class="sxs-lookup"><span data-stu-id="4a094-185">You can use the Fluent API to configure which properties should be used as the composite foreign key properties for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[<span data-ttu-id="4a094-186">Annotations de données (clé simple)</span><span class="sxs-lookup"><span data-stu-id="4a094-186">Data annotations (simple key)</span></span>](#tab/data-annotations-simple-key)

<span data-ttu-id="4a094-187">Vous pouvez utiliser les annotations de données pour configurer la propriété à utiliser comme propriété de clé étrangère pour une relation donnée.</span><span class="sxs-lookup"><span data-stu-id="4a094-187">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="4a094-188">Cela s’effectue généralement lorsque la propriété de clé étrangère n’est pas détectée par Convention :</span><span class="sxs-lookup"><span data-stu-id="4a094-188">This is typically done when the foreign key property is not discovered by convention:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> <span data-ttu-id="4a094-189">L' `[ForeignKey]` annotation peut être placée sur l’une des propriétés de navigation de la relation.</span><span class="sxs-lookup"><span data-stu-id="4a094-189">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="4a094-190">Elle n’a pas besoin de se trouver dans la propriété de navigation de la classe d’entité dépendante.</span><span class="sxs-lookup"><span data-stu-id="4a094-190">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="4a094-191">La propriété spécifiée à l’aide `[ForeignKey]` de sur une propriété de navigation n’a pas besoin d’exister sur le type dépendant.</span><span class="sxs-lookup"><span data-stu-id="4a094-191">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist on the dependent type.</span></span> <span data-ttu-id="4a094-192">Dans ce cas, le nom spécifié est utilisé pour créer une clé étrangère cachée.</span><span class="sxs-lookup"><span data-stu-id="4a094-192">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

#### <a name="shadow-foreign-key"></a><span data-ttu-id="4a094-193">Masquer la clé étrangère</span><span class="sxs-lookup"><span data-stu-id="4a094-193">Shadow foreign key</span></span>

<span data-ttu-id="4a094-194">Vous pouvez utiliser la surcharge de chaîne de `HasForeignKey(...)` pour configurer une propriété Shadow comme clé étrangère (pour plus d’informations, consultez [Propriétés Shadow](xref:core/modeling/shadow-properties) ).</span><span class="sxs-lookup"><span data-stu-id="4a094-194">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](xref:core/modeling/shadow-properties) for more information).</span></span> <span data-ttu-id="4a094-195">Nous vous recommandons d’ajouter explicitement la propriété Shadow au modèle avant de l’utiliser comme clé étrangère (comme indiqué ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="4a094-195">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a><span data-ttu-id="4a094-196">Nom de la contrainte de clé étrangère</span><span class="sxs-lookup"><span data-stu-id="4a094-196">Foreign key constraint name</span></span>

<span data-ttu-id="4a094-197">Par Convention, lorsque vous ciblez une base de données relationnelle, les contraintes de clé étrangère sont nommées FK \_ \<dependent type name> \_ \<principal type name> \_ \<foreign key property name> .</span><span class="sxs-lookup"><span data-stu-id="4a094-197">By convention, when targeting a relational database, foreign key constraints are named FK\_\<dependent type name>\_\<principal type name>\_\<foreign key property name>.</span></span> <span data-ttu-id="4a094-198">Pour les clés étrangères composites, \<foreign key property name> devient une liste de noms de propriétés de clé étrangère séparés par un trait de soulignement.</span><span class="sxs-lookup"><span data-stu-id="4a094-198">For composite foreign keys, \<foreign key property name> becomes an underscore separated list of foreign key property names.</span></span>

<span data-ttu-id="4a094-199">Vous pouvez également configurer le nom de la contrainte comme suit :</span><span class="sxs-lookup"><span data-stu-id="4a094-199">You can also configure the constraint name as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a><span data-ttu-id="4a094-200">Sans propriété de navigation</span><span class="sxs-lookup"><span data-stu-id="4a094-200">Without navigation property</span></span>

<span data-ttu-id="4a094-201">Vous n’avez pas nécessairement besoin de fournir une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="4a094-201">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="4a094-202">Vous pouvez simplement fournir une clé étrangère d’un côté de la relation.</span><span class="sxs-lookup"><span data-stu-id="4a094-202">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a><span data-ttu-id="4a094-203">Clé principale</span><span class="sxs-lookup"><span data-stu-id="4a094-203">Principal key</span></span>

<span data-ttu-id="4a094-204">Si vous souhaitez que la clé étrangère fasse référence à une propriété autre que la clé primaire, vous pouvez utiliser l’API Fluent pour configurer la propriété de clé principale de la relation.</span><span class="sxs-lookup"><span data-stu-id="4a094-204">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="4a094-205">La propriété que vous configurez en tant que clé principale est automatiquement configurée en tant que [clé secondaire](xref:core/modeling/keys#alternate-keys).</span><span class="sxs-lookup"><span data-stu-id="4a094-205">The property that you configure as the principal key will automatically be setup as an [alternate key](xref:core/modeling/keys#alternate-keys).</span></span>

#### <a name="simple-key"></a>[<span data-ttu-id="4a094-206">Clé simple</span><span class="sxs-lookup"><span data-stu-id="4a094-206">Simple key</span></span>](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[<span data-ttu-id="4a094-207">Clé composite</span><span class="sxs-lookup"><span data-stu-id="4a094-207">Composite key</span></span>](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="4a094-208">L’ordre dans lequel vous spécifiez les propriétés de clé principale doit correspondre à l’ordre dans lequel elles sont spécifiées pour la clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="4a094-208">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

---

### <a name="required-and-optional-relationships"></a><span data-ttu-id="4a094-209">Relations obligatoires et facultatives</span><span class="sxs-lookup"><span data-stu-id="4a094-209">Required and optional relationships</span></span>

<span data-ttu-id="4a094-210">Vous pouvez utiliser l’API Fluent pour déterminer si la relation est obligatoire ou facultative.</span><span class="sxs-lookup"><span data-stu-id="4a094-210">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="4a094-211">Au final, cela contrôle si la propriété de clé étrangère est obligatoire ou facultative.</span><span class="sxs-lookup"><span data-stu-id="4a094-211">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="4a094-212">Cela est particulièrement utile lorsque vous utilisez une clé étrangère d’État Shadow.</span><span class="sxs-lookup"><span data-stu-id="4a094-212">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="4a094-213">Si vous avez une propriété de clé étrangère dans votre classe d’entité, le caractère requis de la relation est déterminé selon que la propriété de clé étrangère est obligatoire ou facultative (pour plus d’informations, consultez [propriétés requises et facultatives](xref:core/modeling/entity-properties#required-and-optional-properties) ).</span><span class="sxs-lookup"><span data-stu-id="4a094-213">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](xref:core/modeling/entity-properties#required-and-optional-properties) for more information).</span></span>

<span data-ttu-id="4a094-214">Les propriétés de clé étrangère se trouvent sur le type d’entité dépendant. par conséquent, si elles sont configurées comme obligatoires, cela signifie que chaque entité dépendante doit avoir une entité principale correspondante.</span><span class="sxs-lookup"><span data-stu-id="4a094-214">The foreign key properties are located on the dependent entity type, so if they are configured as required it means that every dependent entity is required to have a corresponding principal entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> <span data-ttu-id="4a094-215">`IsRequired(false)`L’appel rend également la propriété de clé étrangère facultative, sauf si elle est configurée dans le cas contraire.</span><span class="sxs-lookup"><span data-stu-id="4a094-215">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="4a094-216">Suppression en cascade</span><span class="sxs-lookup"><span data-stu-id="4a094-216">Cascade delete</span></span>

<span data-ttu-id="4a094-217">Vous pouvez utiliser l’API Fluent pour configurer explicitement le comportement de suppression en cascade pour une relation donnée.</span><span class="sxs-lookup"><span data-stu-id="4a094-217">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="4a094-218">Pour une présentation détaillée de chaque option, consultez [suppression en cascade](xref:core/saving/cascade-delete) .</span><span class="sxs-lookup"><span data-stu-id="4a094-218">See [Cascade Delete](xref:core/saving/cascade-delete) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="4a094-219">Autres modèles de relation</span><span class="sxs-lookup"><span data-stu-id="4a094-219">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="4a094-220">Un-à-un</span><span class="sxs-lookup"><span data-stu-id="4a094-220">One-to-one</span></span>

<span data-ttu-id="4a094-221">Les relations un-à-un ont une propriété de navigation de référence des deux côtés.</span><span class="sxs-lookup"><span data-stu-id="4a094-221">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="4a094-222">Ils suivent les mêmes conventions que les relations un-à-plusieurs, mais un index unique est introduit sur la propriété de clé étrangère pour s’assurer qu’un seul dépendant est lié à chaque principal.</span><span class="sxs-lookup"><span data-stu-id="4a094-222">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> <span data-ttu-id="4a094-223">EF choisit l’une des entités à dépendre en fonction de sa capacité à détecter une propriété de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="4a094-223">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="4a094-224">Si l’entité incorrecte est choisie comme dépendant, vous pouvez utiliser l’API Fluent pour corriger ce problème.</span><span class="sxs-lookup"><span data-stu-id="4a094-224">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="4a094-225">Quand vous configurez la relation avec l’API Fluent, vous utilisez les `HasOne` `WithOne` méthodes et.</span><span class="sxs-lookup"><span data-stu-id="4a094-225">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="4a094-226">Quand vous configurez la clé étrangère, vous devez spécifier le type d’entité dépendant. Notez le paramètre générique fourni à `HasForeignKey` dans la liste ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="4a094-226">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="4a094-227">Dans une relation un-à-plusieurs, il est clair que l’entité avec la navigation de référence est la dépendance et celle avec la collection est le principal.</span><span class="sxs-lookup"><span data-stu-id="4a094-227">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="4a094-228">Mais ce n’est pas le cas dans une relation un-à-un, c’est pourquoi il est nécessaire de la définir explicitement.</span><span class="sxs-lookup"><span data-stu-id="4a094-228">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

<span data-ttu-id="4a094-229">Le côté dépendant est considéré comme facultatif par défaut, mais peut être configuré en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="4a094-229">The dependent side is considered optional by default, but can be configured as required.</span></span> <span data-ttu-id="4a094-230">Toutefois, EF ne vérifie pas si une entité dépendante a été fournie, donc cette configuration ne fait qu’une différence lorsque le mappage de base de données lui permet d’être appliqué.</span><span class="sxs-lookup"><span data-stu-id="4a094-230">However EF will not validate whether a dependent entity was provided, so this configuration will only make a difference when the database mapping allows it to be enforced.</span></span> <span data-ttu-id="4a094-231">Un scénario courant pour ce sont les types détenues par référence qui utilisent le fractionnement de table par défaut.</span><span class="sxs-lookup"><span data-stu-id="4a094-231">A common scenario for this are reference owned types that use table splitting by default.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=Required&highlight=11-12)]

<span data-ttu-id="4a094-232">Avec cette configuration, les colonnes correspondantes `ShippingAddress` sont marquées comme n’acceptant pas les valeurs NULL dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="4a094-232">With this configuration the columns corresponding to `ShippingAddress` will be marked as non-nullable in the database.</span></span>

> [!NOTE]
> <span data-ttu-id="4a094-233">Si vous utilisez des [types de référence n’acceptant pas les valeurs NULL,](/dotnet/csharp/nullable-references) l’appel de `IsRequired` n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="4a094-233">If you are using [non-nullable reference types](/dotnet/csharp/nullable-references) calling `IsRequired` is not necessary.</span></span>

> [!NOTE]
> <span data-ttu-id="4a094-234">La possibilité de configurer si le dépendant est requis a été ajoutée dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="4a094-234">The ability to configure whether the dependent is required was added in EF Core 5.0.</span></span>

### <a name="many-to-many"></a><span data-ttu-id="4a094-235">Plusieurs-à-plusieurs</span><span class="sxs-lookup"><span data-stu-id="4a094-235">Many-to-many</span></span>

<span data-ttu-id="4a094-236">Les relations plusieurs-à-plusieurs requièrent une propriété de navigation de collection des deux côtés.</span><span class="sxs-lookup"><span data-stu-id="4a094-236">Many to many relationships require a collection navigation property on both sides.</span></span> <span data-ttu-id="4a094-237">Ils seront découverts par convention comme d’autres types de relations.</span><span class="sxs-lookup"><span data-stu-id="4a094-237">They will be discovered by convention like other types of relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=ManyToManyShared)]

<span data-ttu-id="4a094-238">La façon dont cette relation est implémentée dans la base de données est une table de jointure qui contient des clés étrangères pour `Post` et `Tag` .</span><span class="sxs-lookup"><span data-stu-id="4a094-238">The way this relationship is implemented in the database is by a join table that contains foreign keys to both `Post` and `Tag`.</span></span> <span data-ttu-id="4a094-239">Par exemple, il s’agit de ce que EF crée dans une base de données relationnelle pour le modèle ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="4a094-239">For example this is what EF will create in a relational database for the above model.</span></span>

```sql
CREATE TABLE [Posts] (
    [PostId] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([PostId])
);

CREATE TABLE [Tags] (
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_Tags] PRIMARY KEY ([TagId])
);

CREATE TABLE [PostTag] (
    [PostId] int NOT NULL,
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostId], [TagId]),
    CONSTRAINT [FK_PostTag_Posts_PostId] FOREIGN KEY ([PostId]) REFERENCES [Posts] ([PostId]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tags_TagId] FOREIGN KEY ([TagId]) REFERENCES [Tags] ([TagId]) ON DELETE CASCADE
);
```

<span data-ttu-id="4a094-240">En interne, EF crée un type d’entité pour représenter la table de jointure qui sera désignée comme type d’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="4a094-240">Internally, EF creates an entity type to represent the join table that will be referred to as the join entity type.</span></span> <span data-ttu-id="4a094-241">Il n’existe pas de type CLR spécifique qui peut être utilisé pour ce. par conséquent, `Dictionary<string, object>` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="4a094-241">There is no specific CLR type that can be used for this, so `Dictionary<string, object>` is used.</span></span> <span data-ttu-id="4a094-242">Plus d’une relation plusieurs-à-plusieurs peut exister dans le modèle ; par conséquent, le type d’entité de jointure doit avoir un nom unique, dans ce cas `PostTag` .</span><span class="sxs-lookup"><span data-stu-id="4a094-242">More than one many-to-many relationships can exist in the model, therefore the join entity type must be given a unique name, in this case `PostTag`.</span></span> <span data-ttu-id="4a094-243">La fonctionnalité qui permet cela est appelée type d’entité de type partagé.</span><span class="sxs-lookup"><span data-stu-id="4a094-243">The feature that allows this is called shared-type entity type.</span></span>

<span data-ttu-id="4a094-244">Les navigations plusieurs-à-plusieurs sont appelées ignorer les navigations, car elles ignorent le type d’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="4a094-244">The many to many navigations are called skip navigations as they effectively skip over the join entity type.</span></span> <span data-ttu-id="4a094-245">Si vous utilisez la configuration en bloc, toutes les navigations ignorées peuvent être obtenues à partir de `GetSkipNavigations` .</span><span class="sxs-lookup"><span data-stu-id="4a094-245">If you are employing bulk configuration all skip navigations can be obtained from `GetSkipNavigations`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Metadata)]

<span data-ttu-id="4a094-246">Il est courant d’appliquer la configuration au type d’entité de jointure.</span><span class="sxs-lookup"><span data-stu-id="4a094-246">It is common to apply configuration to the join entity type.</span></span> <span data-ttu-id="4a094-247">Cette action peut être effectuée via `UsingEntity` .</span><span class="sxs-lookup"><span data-stu-id="4a094-247">This action can be accomplished via `UsingEntity`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=SharedConfiguration)]

<span data-ttu-id="4a094-248">Des [données de valeur de départ de modèle](xref:core/modeling/data-seeding) peuvent être fournies pour le type d’entité de jointure à l’aide de types anonymes.</span><span class="sxs-lookup"><span data-stu-id="4a094-248">[Model seed data](xref:core/modeling/data-seeding) can be provided for the join entity type by using anonymous types.</span></span> <span data-ttu-id="4a094-249">Vous pouvez examiner la vue de débogage du modèle pour déterminer les noms de propriété créés par Convention.</span><span class="sxs-lookup"><span data-stu-id="4a094-249">You can examine the model debug view to determine the property names created by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Seeding)]

<span data-ttu-id="4a094-250">Des données supplémentaires peuvent être stockées dans le type d’entité de jointure, mais pour cela, il est préférable de créer un type CLR Spoke.</span><span class="sxs-lookup"><span data-stu-id="4a094-250">Additional data can be stored in the join entity type, but for this it's best to create a bespoke CLR type.</span></span> <span data-ttu-id="4a094-251">Lors de la configuration de la relation avec un type d’entité de jointure personnalisée, les deux clés étrangères doivent être spécifiées explicitement.</span><span class="sxs-lookup"><span data-stu-id="4a094-251">When configuring the relationship with a custom join entity type both foreign keys need to be specified explicitly.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyPayload.cs?name=ManyToManyPayload)]

> [!NOTE]
> <span data-ttu-id="4a094-252">La possibilité de configurer des relations plusieurs-à-plusieurs a été ajoutée dans EF Core 5,0, pour la version précédente, utilisez l’approche suivante.</span><span class="sxs-lookup"><span data-stu-id="4a094-252">The ability to configure many-to-many relationships was added in EF Core 5.0, for previous version use the following approach.</span></span>

<span data-ttu-id="4a094-253">Vous pouvez également représenter une relation plusieurs-à-plusieurs en ajoutant simplement le type d’entité de jointure et en mappant deux relations un-à-plusieurs distinctes.</span><span class="sxs-lookup"><span data-stu-id="4a094-253">You can also represent a many-to-many relationship by just adding the join entity type and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]

> [!NOTE]
> <span data-ttu-id="4a094-254">La prise en charge de la génération de modèles automatique de relations plusieurs-à-plusieurs à partir de la base de données n’est pas encore ajoutée.</span><span class="sxs-lookup"><span data-stu-id="4a094-254">Support for scaffolding many-to-many relationships from the database is not yet added.</span></span> <span data-ttu-id="4a094-255">Voir le [problème de suivi](https://github.com/dotnet/efcore/issues/22475).</span><span class="sxs-lookup"><span data-stu-id="4a094-255">See [tracking issue](https://github.com/dotnet/efcore/issues/22475).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a094-256">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4a094-256">Additional resources</span></span>

* <span data-ttu-id="4a094-257">[EF Core session réunions](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32)de la Communauté, avec une présentation approfondie des plusieurs-à-plusieurs et de l’infrastructure qui les étaye.</span><span class="sxs-lookup"><span data-stu-id="4a094-257">[EF Core Community Standup session](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32), with a deep dive into Many-to-many and the infrastructure underpinning it.</span></span>
