---
title: Propriétés Shadow-EF Core
description: Configuration des propriétés Shadow dans un modèle Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 06dbba7a4f6da247e6afde96debb0762fa55ce7e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616720"
---
# <a name="shadow-properties"></a>Propriétés cachées

Les propriétés Shadow sont des propriétés qui ne sont pas définies dans votre classe d’entité .NET, mais qui sont définies pour ce type d’entité dans le modèle EF Core. La valeur et l’état de ces propriétés sont gérés uniquement dans le dispositif de suivi des modifications. Les propriétés Shadow sont utiles lorsque la base de données contient des données qui ne doivent pas être exposées sur les types d’entités mappés.

## <a name="foreign-key-shadow-properties"></a>Propriétés de l’ombre de la clé étrangère

Les propriétés Shadow sont le plus souvent utilisées pour les propriétés de clé étrangère, où la relation entre deux entités est représentée par une valeur de clé étrangère dans la base de données, mais la relation est gérée sur les types d’entité à l’aide des propriétés de navigation entre les types d’entités. Par Convention, EF introduit une propriété Shadow lorsqu’une relation est détectée, mais qu’aucune propriété de clé étrangère n’est trouvée dans la classe d’entité dépendante.

La propriété sera nommée `<navigation property name><principal key property name>` (la navigation sur l’entité dépendante, qui pointe vers l’entité principale, est utilisée pour le nommage). Si le nom de la propriété de clé principale comprend le nom de la propriété de navigation, le nom sera simplement `<principal key property name>` . S’il n’existe aucune propriété de navigation sur l’entité dépendante, le nom du type de principal est utilisé à la place.

Par exemple, la liste de code suivante entraînera l' `BlogId` introduction d’une propriété Shadow à l' `Post` entité :

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>Configuration des propriétés Shadow

Vous pouvez utiliser l’API Fluent pour configurer les propriétés Shadow. Une fois que vous avez appelé la surcharge de chaîne de `Property` , vous pouvez chaîner n’importe quel appel de configuration que vous feriez pour d’autres propriétés. Dans l’exemple suivant, étant donné que n' `Blog` a aucune propriété CLR nommée `LastUpdated` , une propriété Shadow est créée :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

Si le nom fourni à la `Property` méthode correspond au nom d’une propriété existante (une propriété Shadow ou une propriété Shadow définie sur la classe d’entité), le code configurera cette propriété existante au lieu d’introduire une nouvelle propriété Shadow.

## <a name="accessing-shadow-properties"></a>Accès aux propriétés Shadow

Les valeurs de propriété Shadow peuvent être obtenues et modifiées par le biais de l' `ChangeTracker` API :

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

Les propriétés Shadow peuvent être référencées dans des requêtes LINQ via la `EF.Property` méthode statique :

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

Les propriétés Shadow ne sont pas accessibles après une requête de non-suivi, car les entités retournées ne sont pas suivies par le dispositif de suivi des modifications.
