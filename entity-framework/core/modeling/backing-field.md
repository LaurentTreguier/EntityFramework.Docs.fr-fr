---
title: Champs de stockage-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: e015c4f3fca767d25bee179c027813bd9fcf4c07
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526756"
---
# <a name="backing-fields"></a><span data-ttu-id="66dc9-102">Champs de stockage</span><span class="sxs-lookup"><span data-stu-id="66dc9-102">Backing Fields</span></span>

<span data-ttu-id="66dc9-103">Les champs de stockage permettent à EF de lire et/ou d’écrire dans un champ plutôt que sur une propriété.</span><span class="sxs-lookup"><span data-stu-id="66dc9-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="66dc9-104">Cela peut être utile lorsque l’encapsulation dans la classe est utilisée pour limiter l’utilisation de et/ou améliorer la sémantique de l’accès aux données par code d’application, mais la valeur doit être lue à partir de la base de données et/ou écrite sans utiliser ces restrictions/améliorations.</span><span class="sxs-lookup"><span data-stu-id="66dc9-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="66dc9-105">Configuration de base</span><span class="sxs-lookup"><span data-stu-id="66dc9-105">Basic configuration</span></span>

<span data-ttu-id="66dc9-106">Par Convention, les champs suivants sont découverts en tant que champs de stockage pour une propriété donnée (liste dans l’ordre de priorité).</span><span class="sxs-lookup"><span data-stu-id="66dc9-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="66dc9-107">Dans l’exemple suivant, la `Url` propriété est configurée pour avoir `_url` comme champ de stockage :</span><span class="sxs-lookup"><span data-stu-id="66dc9-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="66dc9-108">Notez que les champs de stockage sont uniquement détectés pour les propriétés incluses dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="66dc9-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="66dc9-109">Pour plus d’informations sur les propriétés incluses dans le modèle, consultez [inclusion de & exclusion de propriétés](included-properties.md).</span><span class="sxs-lookup"><span data-stu-id="66dc9-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="66dc9-110">Vous pouvez également configurer des champs de stockage à l’aide d’une annotation de données (disponible dans EFCore 5,0) ou de l’API Fluent, par exemple, si le nom du champ ne correspond pas aux conventions ci-dessus :</span><span class="sxs-lookup"><span data-stu-id="66dc9-110">You can also configure backing fields by using a Data Annotation (available in EFCore 5.0) or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="66dc9-111">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="66dc9-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="66dc9-112">API Fluent</span><span class="sxs-lookup"><span data-stu-id="66dc9-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="66dc9-113">Accès aux champs et aux propriétés</span><span class="sxs-lookup"><span data-stu-id="66dc9-113">Field and property access</span></span>

<span data-ttu-id="66dc9-114">Par défaut, EF lit et écrit toujours dans le champ de stockage (en supposant que l’un d’eux a été correctement configuré et n’utilise jamais la propriété).</span><span class="sxs-lookup"><span data-stu-id="66dc9-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="66dc9-115">Toutefois, EF prend également en charge d’autres modèles d’accès.</span><span class="sxs-lookup"><span data-stu-id="66dc9-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="66dc9-116">Par exemple, l’exemple suivant indique à EF d’écrire dans le champ de stockage uniquement lors de la matérialisation, et d’utiliser la propriété dans tous les autres cas :</span><span class="sxs-lookup"><span data-stu-id="66dc9-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="66dc9-117">Consultez l' [énumération PropertyAccessMode](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) pour obtenir l’ensemble complet des options prises en charge.</span><span class="sxs-lookup"><span data-stu-id="66dc9-117">See the [PropertyAccessMode enum](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="66dc9-118">Avec EF Core 3,0, le mode d’accès à la propriété par défaut a été remplacé par `PreferFieldDuringConstruction` `PreferField` .</span><span class="sxs-lookup"><span data-stu-id="66dc9-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="66dc9-119">Propriétés de champ uniquement</span><span class="sxs-lookup"><span data-stu-id="66dc9-119">Field-only properties</span></span>

<span data-ttu-id="66dc9-120">Vous pouvez également créer une propriété conceptuelle dans votre modèle qui n’a pas de propriété CLR correspondante dans la classe d’entité, mais utilise à la place un champ pour stocker les données dans l’entité.</span><span class="sxs-lookup"><span data-stu-id="66dc9-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="66dc9-121">Cela diffère des [Propriétés Shadow](shadow-properties.md), où les données sont stockées dans le dispositif de suivi des modifications, plutôt que dans le type CLR de l’entité.</span><span class="sxs-lookup"><span data-stu-id="66dc9-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="66dc9-122">Les propriétés de champ uniquement sont couramment utilisées lorsque la classe d’entité utilise des méthodes à la place de propriétés pour récupérer/définir des valeurs, ou dans les cas où les champs ne doivent pas être exposés du tout dans le modèle de domaine (par exemple, les clés primaires).</span><span class="sxs-lookup"><span data-stu-id="66dc9-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="66dc9-123">Vous pouvez configurer une propriété de champ uniquement en fournissant un nom dans l' `Property(...)` API :</span><span class="sxs-lookup"><span data-stu-id="66dc9-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="66dc9-124">EF tente de trouver une propriété CLR portant le nom donné, ou un champ si aucune propriété n’est trouvée.</span><span class="sxs-lookup"><span data-stu-id="66dc9-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="66dc9-125">Si aucune propriété ni aucun champ n’est trouvé, une propriété Shadow est configurée à la place.</span><span class="sxs-lookup"><span data-stu-id="66dc9-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="66dc9-126">Vous devrez peut-être faire référence à une propriété de champ uniquement à partir de requêtes LINQ, mais ces champs sont généralement privés.</span><span class="sxs-lookup"><span data-stu-id="66dc9-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="66dc9-127">Vous pouvez utiliser la `EF.Property(...)` méthode dans une requête LINQ pour faire référence au champ :</span><span class="sxs-lookup"><span data-stu-id="66dc9-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
