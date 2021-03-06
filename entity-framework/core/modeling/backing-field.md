---
title: Champs de stockage-EF Core
description: Configuration de champs de stockage pour les propriétés d’un modèle de Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/modeling/backing-field
ms.openlocfilehash: 0df54192da7365451e5213739df8117e66c5cd0f
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429661"
---
# <a name="backing-fields"></a>Champs de stockage

Les champs de stockage permettent à EF de lire et/ou d’écrire dans un champ plutôt que sur une propriété. Cela peut être utile lorsque l’encapsulation dans la classe est utilisée pour limiter l’utilisation de et/ou améliorer la sémantique de l’accès aux données par code d’application, mais la valeur doit être lue à partir de la base de données et/ou écrite sans utiliser ces restrictions/améliorations.

## <a name="basic-configuration"></a>Configuration de base

Par Convention, les champs suivants sont découverts en tant que champs de stockage pour une propriété donnée (liste dans l’ordre de priorité).

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

Dans l’exemple suivant, la `Url` propriété est configurée pour avoir `_url` comme champ de stockage :

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

Notez que les champs de stockage sont uniquement détectés pour les propriétés incluses dans le modèle. Pour plus d’informations sur les propriétés incluses dans le modèle, consultez [inclusion de & exclusion de propriétés](xref:core/modeling/entity-properties).

Vous pouvez également configurer des champs de stockage à l’aide d’une annotation de données (disponible dans EFCore 5,0) ou de l’API Fluent, par exemple, si le nom du champ ne correspond pas aux conventions ci-dessus :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

***

## <a name="field-and-property-access"></a>Accès aux champs et aux propriétés

Par défaut, EF lit et écrit toujours dans le champ de stockage (en supposant que l’un d’eux a été correctement configuré et n’utilise jamais la propriété). Toutefois, EF prend également en charge d’autres modèles d’accès. Par exemple, l’exemple suivant indique à EF d’écrire dans le champ de stockage uniquement lors de la matérialisation, et d’utiliser la propriété dans tous les autres cas :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

Consultez l' [énumération PropertyAccessMode](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) pour obtenir l’ensemble complet des options prises en charge.

> [!NOTE]
> Avec EF Core 3,0, le mode d’accès à la propriété par défaut a été remplacé par `PreferFieldDuringConstruction` `PreferField` .

## <a name="field-only-properties"></a>Propriétés de champ uniquement

Vous pouvez également créer une propriété conceptuelle dans votre modèle qui n’a pas de propriété CLR correspondante dans la classe d’entité, mais utilise à la place un champ pour stocker les données dans l’entité. Cela diffère des [Propriétés Shadow](xref:core/modeling/shadow-properties), où les données sont stockées dans le dispositif de suivi des modifications, plutôt que dans le type CLR de l’entité. Les propriétés de champ uniquement sont couramment utilisées lorsque la classe d’entité utilise des méthodes à la place de propriétés pour récupérer/définir des valeurs, ou dans les cas où les champs ne doivent pas être exposés du tout dans le modèle de domaine (par exemple, les clés primaires).

Vous pouvez configurer une propriété de champ uniquement en fournissant un nom dans l' `Property(...)` API :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF tente de trouver une propriété CLR portant le nom donné, ou un champ si aucune propriété n’est trouvée. Si aucune propriété ni aucun champ n’est trouvé, une propriété Shadow est configurée à la place.

Vous devrez peut-être faire référence à une propriété de champ uniquement à partir de requêtes LINQ, mais ces champs sont généralement privés. Vous pouvez utiliser la `EF.Property(...)` méthode dans une requête LINQ pour faire référence au champ :

```csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
