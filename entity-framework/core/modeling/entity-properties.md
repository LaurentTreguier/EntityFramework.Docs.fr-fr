---
title: Propriétés de l’entité-EF Core
description: Comment configurer et mapper des propriétés d’entité à l’aide de Entity Framework Core
author: roji
ms.date: 05/27/2020
uid: core/modeling/entity-properties
ms.openlocfilehash: 55c6f31543d4ce3257cf203eaf9fd2191301ea7e
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429594"
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

Si vous préférez configurer vos colonnes avec des noms différents, vous pouvez le faire comme extrait de code suivant :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

**_

## <a name="column-data-types"></a>Types de données de colonne

Lors de l’utilisation d’une base de données relationnelle, le fournisseur de base de données sélectionne un type de données en fonction du type .NET de la propriété. Il prend également en compte d’autres métadonnées, telles que la [longueur maximale](#maximum-length)configurée, si la propriété fait partie d’une clé primaire, etc.

Par exemple, SQL Server mappe les `DateTime` Propriétés aux `datetime2(7)` colonnes et `string` aux propriétés `nvarchar(max)` des colonnes (ou à `nvarchar(450)` pour les propriétés utilisées comme clé).

Vous pouvez également configurer vos colonnes pour spécifier un type de données exact pour une colonne. Par exemple, le code suivant configure `Url` en tant que chaîne non-Unicode avec une longueur maximale de `200` et `Rating` en tant que valeur décimale avec une précision `5` et une échelle de `2` :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

_*_

### <a name="maximum-length"></a>Longueur maximale

La configuration d’une longueur maximale fournit aux fournisseurs de bases de données un indicateur sur le type de données de colonne approprié à choisir pour une propriété donnée. La longueur maximale s’applique uniquement aux types de données de tableau, tels que `string` et `byte[]` .

> [!NOTE]
> Entity Framework n’effectue aucune validation de longueur maximale avant de transmettre des données au fournisseur. Il revient au fournisseur ou au magasin de données de valider le cas échéant. Par exemple, lorsque vous ciblez SQL Server, le dépassement de la longueur maximale entraîne une exception, car le type de données de la colonne sous-jacente n’autorise pas le stockage des données excédentaires.

Dans l’exemple suivant, la configuration d’une longueur maximale de 500 entraîne la création d’une colonne de type `nvarchar(500)` sur SQL Server :

#### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

_*_

### <a name="precision-and-scale"></a>Précision et échelle

À compter de EFCore 5,0, vous pouvez utiliser l’API Fluent pour configurer la précision et l’échelle. Elle indique au fournisseur de base de données la quantité de stockage nécessaire pour une colonne donnée. Elle s’applique uniquement aux types de données où le fournisseur autorise la variation de la précision et de l’échelle, généralement `decimal` et `DateTime` .

Pour les `decimal` Propriétés, la précision définit le nombre maximal de chiffres requis pour exprimer toute valeur que la colonne contiendra, et Scale définit le nombre maximal de décimales nécessaires. Pour `DateTime` les propriétés, la précision définit le nombre maximal de chiffres requis pour exprimer des fractions de secondes et l’échelle n’est pas utilisée.

> [!NOTE]
> Entity Framework n’effectue aucune validation de précision ou d’échelle avant de transmettre des données au fournisseur. Il revient au fournisseur ou au magasin de données de valider le cas échéant. Par exemple, lorsque vous ciblez SQL Server, une colonne de type de données `datetime` n’autorise pas la définition de la précision, tandis que celle d’une colonne `datetime2` peut être comprise entre 0 et 7 inclus.

Dans l’exemple suivant, la configuration de la `Score` propriété pour qu’elle ait la précision 14 et l’échelle 2 entraîne la création d’une colonne de type `decimal(14,2)` sur SQL Server, et la configuration `LastUpdated` de la propriété avec une précision de 3 entraîne la création d’une colonne de type `datetime2(3)` :

#### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

La précision et l’échelle ne peuvent pas être configurées actuellement via des annotations de données.

#### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> L’échelle n’étant jamais définie sans définir au préalable la précision, l’API Fluent pour la définition de l’échelle est `HasPrecision(precision, scale)` .

_*_

## <a name="required-and-optional-properties"></a>Propriétés obligatoires et facultatives

Une propriété est considérée comme facultative si elle est valide pour contenir `null` . Si `null` n’est pas une valeur valide à assigner à une propriété, elle est considérée comme étant une propriété obligatoire. Lors du mappage à un schéma de base de données relationnelle, les propriétés requises sont créées en tant que colonnes n’acceptant pas les valeurs NULL, et les propriétés facultatives sont créées en tant que colonnes Nullable.

### <a name="conventions"></a>Conventions

Par Convention, une propriété dont le type .NET peut contenir une valeur null sera configurée comme étant facultative, alors que les propriétés dont le type .NET ne peut pas contenir de valeur null seront configurées selon les besoins. Par exemple, toutes les propriétés avec des types valeur .net ( `int` , `decimal` , `bool` , etc.) sont configurées en fonction des besoins, et toutes les propriétés avec des types valeur .net Nullable ( `int?` , `decimal?` ,, `bool?` etc.) sont configurées comme étant facultatives.

C# 8 a introduit une nouvelle fonctionnalité appelée [types de référence Nullable (Diagnostics proactifs NRT)](/dotnet/csharp/tutorials/nullable-reference-types), qui permet d’annoter des types de référence, indiquant s’il est valide qu’ils contiennent ou non des valeurs NULL. Cette fonctionnalité est désactivée par défaut et affecte le comportement de EF Core de la façon suivante :

_ Si les types de référence Nullable sont désactivés (valeur par défaut), toutes les propriétés avec des types de référence .NET sont configurées comme étant facultatives par convention (par exemple, `string` ).
* Si les types de référence Nullable sont activés, les propriétés seront configurées en fonction de la possibilité de valeur null C# de leur type .NET : `string?` sera configuré comme étant facultatif, mais `string` sera configuré comme requis.

L’exemple suivant montre un type d’entité avec des propriétés obligatoires et facultatives, avec la fonctionnalité de référence Nullable désactivée (valeur par défaut) et activée :

#### <a name="without-nrt-default"></a>[Sans Diagnostics proactifs NRT (par défaut)](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nrt"></a>[Avec Diagnostics proactifs NRT](#tab/with-nrt)

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

Un classement peut être défini sur des colonnes de texte, en déterminant comment elles sont comparées et triées. Par exemple, l’extrait de code suivant configure un SQL Server colonne pour qu’il ne respecte pas la casse :

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

Si toutes les colonnes d’une base de données doivent utiliser un classement donné, définissez plutôt le classement au niveau de la base de données.

Vous trouverez des informations générales sur la prise en charge de EF Core pour les classements dans la [page de documentation collation](xref:core/miscellaneous/collations-and-case-sensitivity).
