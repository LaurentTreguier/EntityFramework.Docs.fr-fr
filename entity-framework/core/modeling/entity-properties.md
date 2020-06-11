---
title: Propriétés de l’entité-EF Core
description: Comment configurer et mapper des propriétés d’entité à l’aide de Entity Framework Core
author: roji
ms.date: 12/10/2019
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: e4a1867a90df1fb277e7dd44b93d6c2d47895030
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664154"
---
# <a name="entity-properties"></a>Propriétés d'entité

Chaque type d’entité dans votre modèle a un ensemble de propriétés, qui EF Core lit et écrit à partir de la base de données. Si vous utilisez une base de données relationnelle, les propriétés d’entité sont mappées à des colonnes de table.

## <a name="included-and-excluded-properties"></a>Propriétés incluses et exclues

Par Convention, toutes les propriétés publiques avec un accesseur get et un accesseur Set seront incluses dans le modèle.

Les propriétés spécifiques peuvent être exclues comme suit :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a>Noms des colonnes

Par Convention, lors de l’utilisation d’une base de données relationnelle, les propriétés d’entité sont mappées à des colonnes de table portant le même nom que la propriété.

Si vous préférez configurer vos colonnes avec des noms différents, vous pouvez le faire comme suit :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a>Types de données de colonne

Lors de l’utilisation d’une base de données relationnelle, le fournisseur de base de données sélectionne un type de données en fonction du type .NET de la propriété. Il prend également en compte d’autres métadonnées, telles que la [longueur maximale](#maximum-length)configurée, si la propriété fait partie d’une clé primaire, etc.

Par exemple, SQL Server mappe les `DateTime` Propriétés aux `datetime2(7)` colonnes et `string` aux propriétés `nvarchar(max)` des colonnes (ou à `nvarchar(450)` pour les propriétés utilisées comme clé).

Vous pouvez également configurer vos colonnes pour spécifier un type de données exact pour une colonne. Par exemple, le code suivant configure `Url` en tant que chaîne non-Unicode avec une longueur maximale de `200` et `Rating` en tant que valeur décimale avec une précision `5` et une échelle de `2` :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a>Longueur maximale

La configuration d’une longueur maximale fournit aux fournisseurs de bases de données un indicateur sur le type de données de colonne approprié à choisir pour une propriété donnée. La longueur maximale s’applique uniquement aux types de données de tableau, tels que `string` et `byte[]` .

> [!NOTE]
> Entity Framework n’effectue aucune validation de longueur maximale avant de transmettre des données au fournisseur. Il revient au fournisseur ou au magasin de données de valider le cas échéant. Par exemple, lorsque vous ciblez SQL Server, le dépassement de la longueur maximale entraîne une exception, car le type de données de la colonne sous-jacente n’autorise pas le stockage des données excédentaires.

Dans l’exemple suivant, la configuration d’une longueur maximale de 500 entraîne la création d’une colonne de type `nvarchar(500)` sur SQL Server :

#### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a>Propriétés obligatoires et facultatives

Une propriété est considérée comme facultative si elle est valide pour contenir `null` . Si `null` n’est pas une valeur valide à assigner à une propriété, elle est considérée comme étant une propriété obligatoire. Lors du mappage à un schéma de base de données relationnelle, les propriétés requises sont créées en tant que colonnes n’acceptant pas les valeurs NULL, et les propriétés facultatives sont créées en tant que colonnes Nullable.

### <a name="conventions"></a>Conventions

Par Convention, une propriété dont le type .NET peut contenir une valeur null sera configurée comme étant facultative, alors que les propriétés dont le type .NET ne peut pas contenir de valeur null seront configurées selon les besoins. Par exemple, toutes les propriétés avec des types valeur .net ( `int` , `decimal` , `bool` , etc.) sont configurées en fonction des besoins, et toutes les propriétés avec des types valeur .net Nullable ( `int?` , `decimal?` ,, `bool?` etc.) sont configurées comme étant facultatives.

C# 8 a introduit une nouvelle fonctionnalité appelée [types de référence Nullable](/dotnet/csharp/tutorials/nullable-reference-types), qui permet d’annoter les types de référence, ce qui indique s’il est valide qu’ils contiennent ou non null. Cette fonctionnalité est désactivée par défaut et, si elle est activée, elle modifie le comportement de EF Core de la façon suivante :

* Si les types de référence Nullable sont désactivés (valeur par défaut), toutes les propriétés avec des types de référence .NET sont configurées comme étant facultatives par convention (par exemple `string` ,).
* Si les types de référence Nullable sont activés, les propriétés seront configurées en fonction de la possibilité de valeur null C# de leur type .NET : `string?` sera configuré comme étant facultatif, tandis que `string` sera configuré comme requis.

L’exemple suivant montre un type d’entité avec des propriétés obligatoires et facultatives, avec la fonctionnalité de référence Nullable désactivée (valeur par défaut) et activée :

#### <a name="without-nullable-reference-types-default"></a>[Sans types référence Nullable (valeur par défaut)](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[Avec les types de référence Nullable](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

L’utilisation de types de référence Nullable est recommandée, car elle transmet la possibilité de valeur null exprimée en code C# au modèle de EF Core et à la base de données, et évite l’utilisation de l’API Fluent ou des annotations de données pour exprimer deux fois le même concept.

> [!NOTE]
> Soyez prudent lorsque vous activez les types de référence Nullable sur un projet existant : les propriétés de type de référence qui ont été précédemment configurées comme étant facultatives sont maintenant configurées comme obligatoires, sauf si elles sont explicitement annotées comme nullables. Lors de la gestion d’un schéma de base de données relationnelle, des migrations peuvent être générées, ce qui modifie la possibilité de valeur null de la colonne de base de données.

Pour plus d’informations sur les types de référence Nullable et leur utilisation avec EF Core, [consultez la page de documentation dédiée pour cette fonctionnalité](xref:core/miscellaneous/nullable-reference-types).

### <a name="explicit-configuration"></a>Configuration explicite

Une propriété qui serait facultative par convention peut être configurée comme suit :

#### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a>Classements de colonnes

> [!NOTE]
> Cette fonctionnalité est introduite dans EF Core 5,0.

Un classement peut être défini sur des colonnes de texte, en déterminant comment elles sont comparées et triées. Par exemple, le code suivant configure un SQL Server colonne pour qu’il ne respecte pas la casse :

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

Si toutes les colonnes d’une base de données doivent utiliser un classement donné, définissez plutôt le classement au niveau de la base de données.

Vous trouverez des informations générales sur la prise en charge de EF Core pour les classements dans la [page de documentation collation](xref:core/miscellaneous/collations-and-case-sensitivity.md).