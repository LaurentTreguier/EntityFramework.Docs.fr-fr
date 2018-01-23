---
title: Sauvegarde des champs - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
ms.technology: entity-framework-core
uid: core/modeling/backing-field
ms.openlocfilehash: e95417b3368d09a64f9ec02ae40c38dc770c2e6b
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2017
---
# <a name="backing-fields"></a><span data-ttu-id="4d22c-102">Champs de stockage</span><span class="sxs-lookup"><span data-stu-id="4d22c-102">Backing Fields</span></span>

> [!NOTE]  
> <span data-ttu-id="4d22c-103">Cette fonctionnalité est une nouveauté dans EF Core 1.1.</span><span class="sxs-lookup"><span data-stu-id="4d22c-103">This feature is new in EF Core 1.1.</span></span>

<span data-ttu-id="4d22c-104">Champs de stockage permettent EF lire et/ou écrire à un champ au lieu d’une propriété.</span><span class="sxs-lookup"><span data-stu-id="4d22c-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="4d22c-105">Cela peut être utile lors de l’encapsulation de la classe est utilisée pour limiter l’utilisation d’et/ou d’améliorer la sémantique autour de l’accès aux données par code d’application, mais la valeur doit être lus ou écrite à la base de données sans l’aide de ces restrictions / améliorations.</span><span class="sxs-lookup"><span data-stu-id="4d22c-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="conventions"></a><span data-ttu-id="4d22c-106">Conventions</span><span class="sxs-lookup"><span data-stu-id="4d22c-106">Conventions</span></span>

<span data-ttu-id="4d22c-107">Par convention, les champs suivants seront détectés en tant que la sauvegarde des champs pour une propriété donnée (répertoriées dans l’ordre de priorité).</span><span class="sxs-lookup"><span data-stu-id="4d22c-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> <span data-ttu-id="4d22c-108">Les champs sont détectés uniquement pour les propriétés qui sont incluses dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="4d22c-108">Fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="4d22c-109">Pour plus d’informations sur lequel les propriétés sont incluses dans le modèle, consultez [notamment & exclusion des propriétés](included-properties.md).</span><span class="sxs-lookup"><span data-stu-id="4d22c-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/BackingField.cs#Sample)]

<span data-ttu-id="4d22c-110">Lorsqu’un champ de stockage est configuré, EF écrit directement dans ce champ lors de la matérialisation des instances d’entité à partir de la base de données (plutôt qu’à l’aide de l’accesseur Set de propriété).</span><span class="sxs-lookup"><span data-stu-id="4d22c-110">When a backing field is configured, EF will write directly to that field when materializing entity instances from the database (rather than using the property setter).</span></span> <span data-ttu-id="4d22c-111">Si EF doit lire ou écrire la valeur à d’autres moments, il utilise la propriété si possible.</span><span class="sxs-lookup"><span data-stu-id="4d22c-111">If EF needs to read or write the value at other times, it will use the property if possible.</span></span> <span data-ttu-id="4d22c-112">Par exemple, si EF doit mettre à jour la valeur d’une propriété, il utilise la méthode setter de propriété si celle-ci est définie.</span><span class="sxs-lookup"><span data-stu-id="4d22c-112">For example, if EF needs to update the value for a property, it will use the property setter if one is defined.</span></span> <span data-ttu-id="4d22c-113">Si la propriété est en lecture seule, puis elle écrit dans le champ.</span><span class="sxs-lookup"><span data-stu-id="4d22c-113">If the property is read-only, then it will write to the field.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="4d22c-114">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="4d22c-114">Data Annotations</span></span>

<span data-ttu-id="4d22c-115">Champs de stockage ne peut pas être configuré avec des annotations de données.</span><span class="sxs-lookup"><span data-stu-id="4d22c-115">Backing fields cannot be configured with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="4d22c-116">API Fluent</span><span class="sxs-lookup"><span data-stu-id="4d22c-116">Fluent API</span></span>

<span data-ttu-id="4d22c-117">Vous pouvez utiliser l’API Fluent pour configurer un champ de stockage pour une propriété.</span><span class="sxs-lookup"><span data-stu-id="4d22c-117">You can use the Fluent API to configure a backing field for a property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a><span data-ttu-id="4d22c-118">Contrôle lorsque le champ est utilisé.</span><span class="sxs-lookup"><span data-stu-id="4d22c-118">Controlling when the field is used</span></span>

<span data-ttu-id="4d22c-119">Vous pouvez configurer quand EF utilise le champ ou la propriété.</span><span class="sxs-lookup"><span data-stu-id="4d22c-119">You can configure when EF uses the field or property.</span></span> <span data-ttu-id="4d22c-120">Consultez le [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) pour les options prises en charge.</span><span class="sxs-lookup"><span data-stu-id="4d22c-120">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the supported options.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a><span data-ttu-id="4d22c-121">Champs sans une propriété</span><span class="sxs-lookup"><span data-stu-id="4d22c-121">Fields without a property</span></span>

<span data-ttu-id="4d22c-122">Vous pouvez également créer une propriété conceptuelle dans votre modèle qui n’a pas d’une propriété CLR correspondante dans la classe d’entité, mais utilise à la place d’un champ pour stocker les données dans l’entité.</span><span class="sxs-lookup"><span data-stu-id="4d22c-122">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="4d22c-123">Cela est différent de [occulter les propriétés](shadow-properties.md), où les données sont stockées dans le traceur de modifications.</span><span class="sxs-lookup"><span data-stu-id="4d22c-123">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker.</span></span> <span data-ttu-id="4d22c-124">Cela doit généralement être utilisé si la classe d’entité utilise des méthodes pour obtenir/définir des valeurs.</span><span class="sxs-lookup"><span data-stu-id="4d22c-124">This would typically be used if the entity class uses methods to get/set values.</span></span>

<span data-ttu-id="4d22c-125">Vous pouvez donner à EF le nom du champ dans le `Property(...)` API.</span><span class="sxs-lookup"><span data-stu-id="4d22c-125">You can give EF the name of the field in the `Property(...)` API.</span></span> <span data-ttu-id="4d22c-126">S’il n’existe aucune propriété portant le nom donné, puis EF recherchera un champ.</span><span class="sxs-lookup"><span data-stu-id="4d22c-126">If there is no property with the given name, then EF will look for a field.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="4d22c-127">Vous pouvez également attribuer la propriété à un nom, autre que le nom du champ.</span><span class="sxs-lookup"><span data-stu-id="4d22c-127">You can also choose to give the property a name, other than the field name.</span></span> <span data-ttu-id="4d22c-128">Ce nom est ensuite utilisé lors de la création du modèle, notamment il sera utilisé pour le nom de colonne qui est mappé à la base de données.</span><span class="sxs-lookup"><span data-stu-id="4d22c-128">This name is then used when creating the model, most notably it will be used for the column name that is mapped to in the database.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldConceptualProperty.cs#Sample)]

<span data-ttu-id="4d22c-129">Lorsqu’il n’y a aucune propriété dans la classe d’entité, vous pouvez utiliser la `EF.Property(...)` méthode dans une requête LINQ pour faire référence à la propriété qui fait partie du modèle.</span><span class="sxs-lookup"><span data-stu-id="4d22c-129">When there is no property in the entity class, you can use the `EF.Property(...)` method in a LINQ query to refer to the property that is conceptually part of the model.</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```