---
title: Propriétés de l’entité-EF Core
description: Comment configurer et mapper des propriétés d’entité à l’aide de Entity Framework Core
author: roji
ms.date: 05/27/2020
uid: core/modeling/entity-properties
ms.openlocfilehash: fe6dd2c24b8f8ffffa8e0101f69966b7b7c74036
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635547"
---
# <a name="entity-properties"></a><span data-ttu-id="c0fc6-103">Propriétés d'entité</span><span class="sxs-lookup"><span data-stu-id="c0fc6-103">Entity Properties</span></span>

<span data-ttu-id="c0fc6-104">Chaque type d’entité dans votre modèle a un ensemble de propriétés, qui EF Core lit et écrit à partir de la base de données.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="c0fc6-105">Si vous utilisez une base de données relationnelle, les propriétés d’entité sont mappées à des colonnes de table.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="c0fc6-106">Propriétés incluses et exclues</span><span class="sxs-lookup"><span data-stu-id="c0fc6-106">Included and excluded properties</span></span>

<span data-ttu-id="c0fc6-107">Par Convention, toutes les propriétés publiques avec un accesseur get et un accesseur Set seront incluses dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="c0fc6-108">Les propriétés spécifiques peuvent être exclues comme suit :</span><span class="sxs-lookup"><span data-stu-id="c0fc6-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="c0fc6-109">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="c0fc6-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="c0fc6-110">API Fluent</span><span class="sxs-lookup"><span data-stu-id="c0fc6-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="c0fc6-111">Noms des colonnes</span><span class="sxs-lookup"><span data-stu-id="c0fc6-111">Column names</span></span>

<span data-ttu-id="c0fc6-112">Par Convention, lors de l’utilisation d’une base de données relationnelle, les propriétés d’entité sont mappées à des colonnes de table portant le même nom que la propriété.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="c0fc6-113">Si vous préférez configurer vos colonnes avec des noms différents, vous pouvez le faire comme extrait de code suivant :</span><span class="sxs-lookup"><span data-stu-id="c0fc6-113">If you prefer to configure your columns with different names, you can do so as following code snippet:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="c0fc6-114">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="c0fc6-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="c0fc6-115">API Fluent</span><span class="sxs-lookup"><span data-stu-id="c0fc6-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

<span data-ttu-id="c0fc6-116">\*\*_</span><span class="sxs-lookup"><span data-stu-id="c0fc6-116">\*\*_</span></span>

## <a name="column-data-types"></a><span data-ttu-id="c0fc6-117">Types de données de colonne</span><span class="sxs-lookup"><span data-stu-id="c0fc6-117">Column data types</span></span>

<span data-ttu-id="c0fc6-118">Lors de l’utilisation d’une base de données relationnelle, le fournisseur de base de données sélectionne un type de données en fonction du type .NET de la propriété.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-118">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="c0fc6-119">Il prend également en compte d’autres métadonnées, telles que la [longueur maximale](#maximum-length)configurée, si la propriété fait partie d’une clé primaire, etc.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-119">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="c0fc6-120">Par exemple, SQL Server mappe les `DateTime` Propriétés aux `datetime2(7)` colonnes et `string` aux propriétés `nvarchar(max)` des colonnes (ou à `nvarchar(450)` pour les propriétés utilisées comme clé).</span><span class="sxs-lookup"><span data-stu-id="c0fc6-120">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="c0fc6-121">Vous pouvez également configurer vos colonnes pour spécifier un type de données exact pour une colonne.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-121">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="c0fc6-122">Par exemple, le code suivant configure `Url` en tant que chaîne non-Unicode avec une longueur maximale de `200` et `Rating` en tant que valeur décimale avec une précision `5` et une échelle de `2` :</span><span class="sxs-lookup"><span data-stu-id="c0fc6-122">For example, the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="c0fc6-123">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="c0fc6-123">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[<span data-ttu-id="c0fc6-124">API Fluent</span><span class="sxs-lookup"><span data-stu-id="c0fc6-124">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

_*_

### <a name="maximum-length"></a><span data-ttu-id="c0fc6-125">Longueur maximale</span><span class="sxs-lookup"><span data-stu-id="c0fc6-125">Maximum length</span></span>

<span data-ttu-id="c0fc6-126">La configuration d’une longueur maximale fournit aux fournisseurs de bases de données un indicateur sur le type de données de colonne approprié à choisir pour une propriété donnée.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-126">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="c0fc6-127">La longueur maximale s’applique uniquement aux types de données de tableau, tels que `string` et `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="c0fc6-127">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="c0fc6-128">Entity Framework n’effectue aucune validation de longueur maximale avant de transmettre des données au fournisseur.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-128">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="c0fc6-129">Il revient au fournisseur ou au magasin de données de valider le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-129">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="c0fc6-130">Par exemple, lorsque vous ciblez SQL Server, le dépassement de la longueur maximale entraîne une exception, car le type de données de la colonne sous-jacente n’autorise pas le stockage des données excédentaires.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-130">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="c0fc6-131">Dans l’exemple suivant, la configuration d’une longueur maximale de 500 entraîne la création d’une colonne de type `nvarchar(500)` sur SQL Server :</span><span class="sxs-lookup"><span data-stu-id="c0fc6-131">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="c0fc6-132">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="c0fc6-132">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="c0fc6-133">API Fluent</span><span class="sxs-lookup"><span data-stu-id="c0fc6-133">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

_*_

### <a name="precision-and-scale"></a><span data-ttu-id="c0fc6-134">Précision et échelle</span><span class="sxs-lookup"><span data-stu-id="c0fc6-134">Precision and Scale</span></span>

<span data-ttu-id="c0fc6-135">À compter de EFCore 5,0, vous pouvez utiliser l’API Fluent pour configurer la précision et l’échelle.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-135">Starting with EFCore 5.0, you can use fluent API to configure the precision and scale.</span></span> <span data-ttu-id="c0fc6-136">Elle indique au fournisseur de base de données la quantité de stockage nécessaire pour une colonne donnée.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-136">It tells the database provider how much storage is needed for a given column.</span></span> <span data-ttu-id="c0fc6-137">Elle s’applique uniquement aux types de données où le fournisseur autorise la variation de la précision et de l’échelle, généralement `decimal` et `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="c0fc6-137">It only applies to data types where the provider allows the precision and scale to vary - usually `decimal` and `DateTime`.</span></span>

<span data-ttu-id="c0fc6-138">Pour les `decimal` Propriétés, la précision définit le nombre maximal de chiffres requis pour exprimer toute valeur que la colonne contiendra, et Scale définit le nombre maximal de décimales nécessaires.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-138">For `decimal` properties, precision defines the maximum number of digits needed to express any value the column will contain, and scale defines the maximum number of decimal places needed.</span></span> <span data-ttu-id="c0fc6-139">Pour `DateTime` les propriétés, la précision définit le nombre maximal de chiffres requis pour exprimer des fractions de secondes et l’échelle n’est pas utilisée.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-139">For `DateTime` properties, precision defines the maximum number of digits needed to express fractions of seconds, and scale is not used.</span></span>

> [!NOTE]
> <span data-ttu-id="c0fc6-140">Entity Framework n’effectue aucune validation de précision ou d’échelle avant de transmettre des données au fournisseur.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-140">Entity Framework does not do any validation of precision or scale before passing data to the provider.</span></span> <span data-ttu-id="c0fc6-141">Il revient au fournisseur ou au magasin de données de valider le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-141">It is up to the provider or data store to validate as appropriate.</span></span> <span data-ttu-id="c0fc6-142">Par exemple, lorsque vous ciblez SQL Server, une colonne de type de données `datetime` n’autorise pas la définition de la précision, tandis que celle d’une colonne `datetime2` peut être comprise entre 0 et 7 inclus.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-142">For example, when targeting SQL Server, a column of data type `datetime` does not allow the precision to be set, whereas a `datetime2` one can have precision between 0 and 7 inclusive.</span></span>

<span data-ttu-id="c0fc6-143">Dans l’exemple suivant, la configuration de la `Score` propriété pour qu’elle ait la précision 14 et l’échelle 2 entraîne la création d’une colonne de type `decimal(14,2)` sur SQL Server, et la configuration `LastUpdated` de la propriété avec une précision de 3 entraîne la création d’une colonne de type `datetime2(3)` :</span><span class="sxs-lookup"><span data-stu-id="c0fc6-143">In the following example, configuring the `Score` property to have precision 14 and scale 2 will cause a column of type `decimal(14,2)` to be created on SQL Server, and configuring the `LastUpdated` property to have precision 3 will cause a column of type `datetime2(3)`:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="c0fc6-144">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="c0fc6-144">Data Annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="c0fc6-145">La précision et l’échelle ne peuvent pas être configurées actuellement via des annotations de données.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-145">Precision and scale cannot currently be configured via data annotations.</span></span>

#### <a name="fluent-api"></a>[<span data-ttu-id="c0fc6-146">API Fluent</span><span class="sxs-lookup"><span data-stu-id="c0fc6-146">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> <span data-ttu-id="c0fc6-147">L’échelle n’étant jamais définie sans définir au préalable la précision, l’API Fluent pour la définition de l’échelle est `HasPrecision(precision, scale)` .</span><span class="sxs-lookup"><span data-stu-id="c0fc6-147">Scale is never defined without first defining precision, so the Fluent API for defining the scale is `HasPrecision(precision, scale)`.</span></span>

_*_

## <a name="required-and-optional-properties"></a><span data-ttu-id="c0fc6-148">Propriétés obligatoires et facultatives</span><span class="sxs-lookup"><span data-stu-id="c0fc6-148">Required and optional properties</span></span>

<span data-ttu-id="c0fc6-149">Une propriété est considérée comme facultative si elle est valide pour contenir `null` .</span><span class="sxs-lookup"><span data-stu-id="c0fc6-149">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="c0fc6-150">Si `null` n’est pas une valeur valide à assigner à une propriété, elle est considérée comme étant une propriété obligatoire.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-150">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="c0fc6-151">Lors du mappage à un schéma de base de données relationnelle, les propriétés requises sont créées en tant que colonnes n’acceptant pas les valeurs NULL, et les propriétés facultatives sont créées en tant que colonnes Nullable.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-151">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="c0fc6-152">Conventions</span><span class="sxs-lookup"><span data-stu-id="c0fc6-152">Conventions</span></span>

<span data-ttu-id="c0fc6-153">Par Convention, une propriété dont le type .NET peut contenir une valeur null sera configurée comme étant facultative, alors que les propriétés dont le type .NET ne peut pas contenir de valeur null seront configurées selon les besoins.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-153">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="c0fc6-154">Par exemple, toutes les propriétés avec des types valeur .net ( `int` , `decimal` , `bool` , etc.) sont configurées en fonction des besoins, et toutes les propriétés avec des types valeur .net Nullable ( `int?` , `decimal?` ,, `bool?` etc.) sont configurées comme étant facultatives.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-154">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="c0fc6-155">C# 8 a introduit une nouvelle fonctionnalité appelée [types de référence Nullable (Diagnostics proactifs NRT)](/dotnet/csharp/tutorials/nullable-reference-types), qui permet d’annoter des types de référence, indiquant s’il est valide qu’ils contiennent ou non des valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-155">C# 8 introduced a new feature called [nullable reference types (NRT)](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="c0fc6-156">Cette fonctionnalité est désactivée par défaut et affecte le comportement de EF Core de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="c0fc6-156">This feature is disabled by default, and affects EF Core's behavior in the following way:</span></span>

<span data-ttu-id="c0fc6-157">_ Si les types de référence Nullable sont désactivés (valeur par défaut), toutes les propriétés avec des types de référence .NET sont configurées comme étant facultatives par convention (par exemple, `string` ).</span><span class="sxs-lookup"><span data-stu-id="c0fc6-157">_ If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (for example, `string`).</span></span>
* <span data-ttu-id="c0fc6-158">Si les types de référence Nullable sont activés, les propriétés seront configurées en fonction de la possibilité de valeur null C# de leur type .NET : `string?` sera configuré comme étant facultatif, mais `string` sera configuré comme requis.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-158">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, but `string` will be configured as required.</span></span>

<span data-ttu-id="c0fc6-159">L’exemple suivant montre un type d’entité avec des propriétés obligatoires et facultatives, avec la fonctionnalité de référence Nullable désactivée (valeur par défaut) et activée :</span><span class="sxs-lookup"><span data-stu-id="c0fc6-159">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nrt-default"></a>[<span data-ttu-id="c0fc6-160">Sans Diagnostics proactifs NRT (par défaut)</span><span class="sxs-lookup"><span data-stu-id="c0fc6-160">Without NRT (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nrt"></a>[<span data-ttu-id="c0fc6-161">Avec Diagnostics proactifs NRT</span><span class="sxs-lookup"><span data-stu-id="c0fc6-161">With NRT</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="c0fc6-162">L’utilisation de types de référence Nullable est recommandée, car elle transmet la possibilité de valeur null exprimée en code C# au modèle de EF Core et à la base de données, et évite l’utilisation de l’API Fluent ou des annotations de données pour exprimer deux fois le même concept.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-162">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="c0fc6-163">Soyez prudent lorsque vous activez les types de référence Nullable sur un projet existant : les propriétés de type de référence qui ont été précédemment configurées comme étant facultatives sont maintenant configurées comme obligatoires, sauf si elles sont explicitement annotées comme nullables.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-163">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="c0fc6-164">Lors de la gestion d’un schéma de base de données relationnelle, des migrations peuvent être générées, ce qui modifie la possibilité de valeur null de la colonne de base de données.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-164">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="c0fc6-165">Pour plus d’informations sur les types de référence Nullable et leur utilisation avec EF Core, [consultez la page de documentation dédiée pour cette fonctionnalité](xref:core/miscellaneous/nullable-reference-types).</span><span class="sxs-lookup"><span data-stu-id="c0fc6-165">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="c0fc6-166">Configuration explicite</span><span class="sxs-lookup"><span data-stu-id="c0fc6-166">Explicit configuration</span></span>

<span data-ttu-id="c0fc6-167">Une propriété qui serait facultative par convention peut être configurée comme suit :</span><span class="sxs-lookup"><span data-stu-id="c0fc6-167">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="c0fc6-168">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="c0fc6-168">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="c0fc6-169">API Fluent</span><span class="sxs-lookup"><span data-stu-id="c0fc6-169">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

<span data-ttu-id="c0fc6-170">\*\*_</span><span class="sxs-lookup"><span data-stu-id="c0fc6-170">\*\*_</span></span>

## <a name="column-collations"></a><span data-ttu-id="c0fc6-171">Classements de colonnes</span><span class="sxs-lookup"><span data-stu-id="c0fc6-171">Column collations</span></span>

> [!NOTE]
> <span data-ttu-id="c0fc6-172">Cette fonctionnalité a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-172">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="c0fc6-173">Un classement peut être défini sur des colonnes de texte, en déterminant comment elles sont comparées et triées.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-173">A collation can be defined on text columns, determining how they are compared and ordered.</span></span> <span data-ttu-id="c0fc6-174">Par exemple, l’extrait de code suivant configure un SQL Server colonne pour qu’il ne respecte pas la casse :</span><span class="sxs-lookup"><span data-stu-id="c0fc6-174">For example, the following code snippet configures a SQL Server column to be case-insensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

<span data-ttu-id="c0fc6-175">Si toutes les colonnes d’une base de données doivent utiliser un classement donné, définissez plutôt le classement au niveau de la base de données.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-175">If all columns in a database need to use a certain collation, define the collation at the database level instead.</span></span>

<span data-ttu-id="c0fc6-176">Vous trouverez des informations générales sur la prise en charge de EF Core pour les classements dans la [page de documentation collation](xref:core/miscellaneous/collations-and-case-sensitivity).</span><span class="sxs-lookup"><span data-stu-id="c0fc6-176">General information about EF Core support for collations can be found in the [collation documentation page](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>

## <a name="column-comments"></a><span data-ttu-id="c0fc6-177">Commentaires de colonne</span><span class="sxs-lookup"><span data-stu-id="c0fc6-177">Column comments</span></span>

<span data-ttu-id="c0fc6-178">Vous pouvez définir un commentaire de texte arbitraire qui est défini sur la colonne de base de données, ce qui vous permet de documenter votre schéma dans la base de données :</span><span class="sxs-lookup"><span data-stu-id="c0fc6-178">You can set an arbitrary text comment that gets set on the database column, allowing you to document your schema in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="c0fc6-179">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="c0fc6-179">Data Annotations</span></span>](#tab/data-annotations)

> [!NOTE]
> <span data-ttu-id="c0fc6-180">La définition de commentaires via des annotations de données a été introduite dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="c0fc6-180">Setting comments via data annotations was introduced in EF Core 5.0.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnComment.cs?name=ColumnComment&highlight=4)]

### <a name="fluent-api"></a>[<span data-ttu-id="c0fc6-181">API Fluent</span><span class="sxs-lookup"><span data-stu-id="c0fc6-181">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnComment.cs?name=ColumnComment&highlight=5)]

<span data-ttu-id="c0fc6-182">_\*\*</span><span class="sxs-lookup"><span data-stu-id="c0fc6-182">_\*\*</span></span>
