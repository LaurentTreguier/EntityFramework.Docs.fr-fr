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
# <a name="backing-fields"></a>Champs de stockage

Les champs de soutien permettent à EF de lire et/ou d’écrire sur un champ plutôt qu’une propriété. Cela peut être utile lorsque l’encapsulation dans la classe est utilisée pour restreindre l’utilisation et / ou d’améliorer la sémantique autour de l’accès aux données par code d’application, mais la valeur doit être lu à partir et / ou écrit à la base de données sans utiliser ces restrictions / améliorations.

## <a name="basic-configuration"></a>Configuration de base

Par convention, les champs suivants seront découverts comme champs de soutien pour une propriété donnée (listé dans l’ordre de préséance). 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

Dans l’échantillon `Url` suivant, la propriété `_url` est configurée pour avoir comme champ d’appui:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

Notez que les champs de soutien ne sont découverts que pour les propriétés qui sont incluses dans le modèle. Pour plus d’informations sur les propriétés incluses dans le modèle, voir [y compris & à l’exclusion des propriétés](included-properties.md).

Vous pouvez également configurer des champs de support en utilisant une annotation de données ou l’API fluente, par exemple si le nom de champ ne correspond pas aux conventions ci-dessus :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>Accès au champ et à la propriété

Par défaut, EF sera toujours lire et écrire sur le champ de soutien - en supposant que l’on a été correctement configuré - et n’utilisera jamais la propriété. Toutefois, EF prend également en charge d’autres modèles d’accès. Par exemple, l’échantillon suivant demande à EF d’écrire au champ d’appui uniquement tout en se matérialisant et d’utiliser la propriété dans tous les autres cas :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

Consultez [l’enum PropertyAccessMode](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) pour l’ensemble complet des options prises en charge.

> [!NOTE]
> Avec EF Core 3.0, le mode `PreferFieldDuringConstruction` `PreferField`d’accès à la propriété par défaut est passé de .

## <a name="field-only-properties"></a>Propriétés sur le terrain seulement

Vous pouvez également créer une propriété conceptuelle dans votre modèle qui n’a pas une propriété CLR correspondante dans la catégorie d’entités, mais utilise plutôt un champ pour stocker les données dans l’entité. Ceci est différent de [Shadow Properties](shadow-properties.md), où les données sont stockées dans le tracker de changement, plutôt que dans le type CLR de l’entité. Les propriétés sur le terrain seulement sont couramment utilisées lorsque la classe d’entité utilise des méthodes au lieu de propriétés pour obtenir/définir des valeurs, ou dans les cas où les champs ne devraient pas être exposés du tout dans le modèle de domaine (par exemple les clés primaires).

Vous pouvez configurer une propriété sur le `Property(...)` terrain uniquement en fournissant un nom dans l’API :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF tentera de trouver une propriété CLR avec le nom donné, ou un champ si une propriété n’est pas trouvée. Si ni une propriété ni un champ ne sont trouvés, une propriété d’ombre sera mise en place à la place.

Vous devrez peut-être vous référer à une propriété réservée sur le terrain à partir de requêtes LINQ, mais ces champs sont généralement privés. Vous pouvez `EF.Property(...)` utiliser la méthode dans une requête LINQ pour vous référer au champ :

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
