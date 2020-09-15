---
title: Types d’entité-EF Core
description: Comment configurer et mapper des types d’entité à l’aide de Entity Framework Core
author: roji
ms.date: 12/03/2019
uid: core/modeling/entity-types
ms.openlocfilehash: fead7f9e37efb7f674f429acbfd16c2ca78480d4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071509"
---
# <a name="entity-types"></a>Types d'entités

L’inclusion d’un DbSet d’un type dans votre contexte signifie qu’il est inclus dans le modèle de EF Core ; Nous faisons généralement référence à un type de ce type en tant qu' *entité*. EF Core pouvez lire et écrire des instances d’entité à partir de/vers la base de données, et si vous utilisez une base de données relationnelle, EF Core pouvez créer des tables pour vos entités via des migrations.

## <a name="including-types-in-the-model"></a>Inclusion de types dans le modèle

Par Convention, les types qui sont exposés dans les propriétés DbSet de votre contexte sont inclus dans le modèle en tant qu’entités. Les types d’entités qui sont spécifiés dans la `OnModelCreating` méthode sont également inclus, comme tous les types trouvés en explorant de manière récursive les propriétés de navigation d’autres types d’entités découverts.

Dans l’exemple de code ci-dessous, tous les types sont inclus :

* `Blog` est inclus, car il est exposé dans une propriété DbSet sur le contexte.
* `Post` est inclus, car il est découvert via la `Blog.Posts` propriété de navigation.
* `AuditEntry` parce qu’il est spécifié dans `OnModelCreating` .

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>Exclusion de types du modèle

Si vous ne souhaitez pas qu’un type soit inclus dans le modèle, vous pouvez l’exclure :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

## <a name="table-name"></a>Nom de la table

Par Convention, chaque type d’entité sera configuré pour être mappé à une table de base de données portant le même nom que la propriété DbSet qui expose l’entité. S’il n’existe aucun DbSet pour l’entité donnée, le nom de la classe est utilisé.

Vous pouvez configurer manuellement le nom de la table :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a>Schéma de table

Lorsque vous utilisez une base de données relationnelle, les tables sont par convention créées dans le schéma par défaut de votre base de données. Par exemple, Microsoft SQL Server utilise le `dbo` schéma (SQLite ne prend pas en charge les schémas).

Vous pouvez configurer des tables à créer dans un schéma spécifique comme suit :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

Au lieu de spécifier le schéma pour chaque table, vous pouvez également définir le schéma par défaut au niveau du modèle à l’aide de l’API Fluent :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

Notez que la définition du schéma par défaut affectera également d’autres objets de base de données, tels que les séquences.
