---
title: Champs de soutien - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434173"
---
# <a name="backing-fields"></a><span data-ttu-id="6c718-102">Champs de stockage</span><span class="sxs-lookup"><span data-stu-id="6c718-102">Backing Fields</span></span>

<span data-ttu-id="6c718-103">Les champs de soutien permettent à EF de lire et/ou d’écrire sur un champ plutôt qu’une propriété.</span><span class="sxs-lookup"><span data-stu-id="6c718-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="6c718-104">Cela peut être utile lorsque l’encapsulation dans la classe est utilisée pour restreindre l’utilisation et / ou d’améliorer la sémantique autour de l’accès aux données par code d’application, mais la valeur doit être lu à partir et / ou écrit à la base de données sans utiliser ces restrictions / améliorations.</span><span class="sxs-lookup"><span data-stu-id="6c718-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="6c718-105">Configuration de base</span><span class="sxs-lookup"><span data-stu-id="6c718-105">Basic configuration</span></span>

<span data-ttu-id="6c718-106">Par convention, les champs suivants seront découverts comme champs de soutien pour une propriété donnée (listé dans l’ordre de préséance).</span><span class="sxs-lookup"><span data-stu-id="6c718-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="6c718-107">Dans l’échantillon `Url` suivant, la propriété `_url` est configurée pour avoir comme champ d’appui:</span><span class="sxs-lookup"><span data-stu-id="6c718-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="6c718-108">Notez que les champs de soutien ne sont découverts que pour les propriétés qui sont incluses dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="6c718-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="6c718-109">Pour plus d’informations sur les propriétés incluses dans le modèle, voir [y compris & à l’exclusion des propriétés](included-properties.md).</span><span class="sxs-lookup"><span data-stu-id="6c718-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="6c718-110">Vous pouvez également configurer des champs de support en utilisant une annotation de données ou l’API fluente, par exemple si le nom de champ ne correspond pas aux conventions ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="6c718-110">You can also configure backing fields by using a Data Annotation or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="6c718-111">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="6c718-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="6c718-112">API Fluent</span><span class="sxs-lookup"><span data-stu-id="6c718-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="6c718-113">Accès au champ et à la propriété</span><span class="sxs-lookup"><span data-stu-id="6c718-113">Field and property access</span></span>

<span data-ttu-id="6c718-114">Par défaut, EF sera toujours lire et écrire sur le champ de soutien - en supposant que l’on a été correctement configuré - et n’utilisera jamais la propriété.</span><span class="sxs-lookup"><span data-stu-id="6c718-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="6c718-115">Toutefois, EF prend également en charge d’autres modèles d’accès.</span><span class="sxs-lookup"><span data-stu-id="6c718-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="6c718-116">Par exemple, l’échantillon suivant demande à EF d’écrire au champ d’appui uniquement tout en se matérialisant et d’utiliser la propriété dans tous les autres cas :</span><span class="sxs-lookup"><span data-stu-id="6c718-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="6c718-117">Consultez [l’enum PropertyAccessMode](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) pour l’ensemble complet des options prises en charge.</span><span class="sxs-lookup"><span data-stu-id="6c718-117">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="6c718-118">Avec EF Core 3.0, le mode `PreferFieldDuringConstruction` `PreferField`d’accès à la propriété par défaut est passé de .</span><span class="sxs-lookup"><span data-stu-id="6c718-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="6c718-119">Propriétés sur le terrain seulement</span><span class="sxs-lookup"><span data-stu-id="6c718-119">Field-only properties</span></span>

<span data-ttu-id="6c718-120">Vous pouvez également créer une propriété conceptuelle dans votre modèle qui n’a pas une propriété CLR correspondante dans la catégorie d’entités, mais utilise plutôt un champ pour stocker les données dans l’entité.</span><span class="sxs-lookup"><span data-stu-id="6c718-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="6c718-121">Ceci est différent de [Shadow Properties](shadow-properties.md), où les données sont stockées dans le tracker de changement, plutôt que dans le type CLR de l’entité.</span><span class="sxs-lookup"><span data-stu-id="6c718-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="6c718-122">Les propriétés sur le terrain seulement sont couramment utilisées lorsque la classe d’entité utilise des méthodes au lieu de propriétés pour obtenir/définir des valeurs, ou dans les cas où les champs ne devraient pas être exposés du tout dans le modèle de domaine (par exemple les clés primaires).</span><span class="sxs-lookup"><span data-stu-id="6c718-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="6c718-123">Vous pouvez configurer une propriété sur le `Property(...)` terrain uniquement en fournissant un nom dans l’API :</span><span class="sxs-lookup"><span data-stu-id="6c718-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="6c718-124">EF tentera de trouver une propriété CLR avec le nom donné, ou un champ si une propriété n’est pas trouvée.</span><span class="sxs-lookup"><span data-stu-id="6c718-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="6c718-125">Si ni une propriété ni un champ ne sont trouvés, une propriété d’ombre sera mise en place à la place.</span><span class="sxs-lookup"><span data-stu-id="6c718-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="6c718-126">Vous devrez peut-être vous référer à une propriété réservée sur le terrain à partir de requêtes LINQ, mais ces champs sont généralement privés.</span><span class="sxs-lookup"><span data-stu-id="6c718-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="6c718-127">Vous pouvez `EF.Property(...)` utiliser la méthode dans une requête LINQ pour vous référer au champ :</span><span class="sxs-lookup"><span data-stu-id="6c718-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
