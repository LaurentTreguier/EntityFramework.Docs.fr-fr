---
title: Valeurs générées-EF Core
description: Comment configurer la génération de valeurs pour les propriétés lors de l’utilisation de Entity Framework Core
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: a9e43f3b755bf028bc76581135988e831a42d0d1
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543339"
---
# <a name="generated-values"></a>Valeurs générées

Les valeurs des colonnes de base de données peuvent être générées de différentes façons : les colonnes clés primaires sont souvent des entiers à incrémentation automatique, les autres colonnes ont des valeurs par défaut ou calculées, etc. Cette page détaille les différents modèles de génération de valeur de configuration avec EF Core.

## <a name="default-values"></a>Valeurs par défaut

Sur les bases de données relationnelles, une colonne peut être configurée avec une valeur par défaut ; Si une ligne est insérée sans valeur pour cette colonne, la valeur par défaut est utilisée.

Vous pouvez configurer une valeur par défaut sur une propriété :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

Vous pouvez également spécifier un fragment SQL utilisé pour calculer la valeur par défaut :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a>Colonnes calculées

Sur la plupart des bases de données relationnelles, une colonne peut être configurée de manière à ce que sa valeur soit calculée dans la base de données, généralement avec une expression faisant référence à d’autres colonnes :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

L’instruction ci-dessus crée une colonne calculée *virtuelle* , dont la valeur est calculée chaque fois qu’elle est extraite de la base de données. Vous pouvez également spécifier qu’une colonne calculée soit *stockée* (parfois appelée *PERSISTED*), ce qui signifie qu’elle est calculée à chaque mise à jour de la ligne et qu’elle est stockée sur le disque avec les colonnes normales :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> La prise en charge de la création de colonnes calculées stockées a été ajoutée dans EF Core 5,0.

## <a name="primary-keys"></a>Clés primaires

Par Convention, les clés primaires non composites de type short, int, long ou GUID sont configurées pour avoir des valeurs générées pour les entités insérées si une valeur n’est pas fournie par l’application. Votre fournisseur de base de données prend généralement en charge la configuration requise ; par exemple, une clé primaire numérique dans SQL Server est automatiquement configurée pour être une colonne d’identité.

Pour plus d’informations, [consultez la documentation sur les clés](xref:core/modeling/keys).

## <a name="explicitly-configuring-value-generation"></a>Configuration explicite de la génération de valeurs

Nous avons vu plus haut que EF Core configure automatiquement la génération de valeurs pour les clés primaires, mais nous pouvons souhaiter faire de même pour les propriétés non-clés. Vous pouvez configurer n’importe quelle propriété pour que sa valeur soit générée pour les entités insérées comme suit :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=6)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

De même, une propriété peut être configurée de manière à ce que sa valeur soit générée lors de l’ajout ou de la mise à jour :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=6)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

***

> [!WARNING]
> Contrairement aux valeurs par défaut ou aux colonnes calculées, nous ne spécifions pas *Comment* les valeurs doivent être générées ; Cela dépend du fournisseur de base de données utilisé. Les fournisseurs de bases de données peuvent configurer automatiquement la génération de valeur pour certains types de propriété, mais d’autres peuvent vous obliger à configurer manuellement la manière dont la valeur est générée.
>
> Par exemple, sur SQL Server, quand une propriété GUID est configurée en tant que valeur générée lors de l’ajout, le fournisseur effectue automatiquement la génération de valeur côté client, à l’aide d’un algorithme pour générer des valeurs GUID séquentielles optimales. Toutefois, `ValueGeneratedOnAdd()` la spécification de sur une propriété DateTime n’a aucun effet ([consultez la section ci-dessous pour la génération de la valeur DateTime](#datetime-value-generation)).
>
> De même, les propriétés Byte [] qui sont configurées comme générées lors de l’ajout ou de la mise à jour et marquées comme jetons d’accès concurrentiel sont configurées avec le type de données rowversion, afin que les valeurs soient générées automatiquement dans la base de données. Toutefois, si vous spécifiez, l’opération n’aura `ValueGeneratedOnAddOrUpdate()` aucun effet.
>
> [!NOTE]
> Selon le fournisseur de base de données utilisé, les valeurs peuvent être générées côté client par EF ou dans la base de données. Si la valeur est générée par la base de données, EF peut affecter une valeur temporaire lorsque vous ajoutez l’entité au contexte. Cette valeur temporaire sera ensuite remplacée par la valeur générée par la base de données pendant `SaveChanges()` . Pour plus d’informations, [consultez la documentation sur les valeurs temporaires](xref:core/change-tracking/explicit-tracking#temporary-values).

## <a name="datetime-value-generation"></a>Génération de la valeur de date/heure

Une demande courante consiste à avoir une colonne de base de données qui contient la date/l’heure de la première insertion de la colonne (valeur générée lors de l’ajout), ou la date de dernière mise à jour (valeur générée lors de l’ajout ou de la mise à jour). Comme il existe plusieurs stratégies pour ce faire, les fournisseurs de EF Core ne configurent pas automatiquement la génération de valeur pour les colonnes de date/heure : vous devez configurer cela vous-même.

### <a name="creation-timestamp"></a>Horodateur de création

La configuration d’une colonne de date/heure pour avoir l’horodateur de création de la ligne est généralement une question de configuration d’une valeur par défaut avec la fonction SQL appropriée. Par exemple, sur SQL Server vous pouvez utiliser les éléments suivants :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

Veillez à sélectionner la fonction appropriée, car plusieurs peuvent exister (par exemple, `GETDATE()` vs. `GETUTCDATE()` ).

### <a name="update-timestamp"></a>Horodateur de la mise à jour

Bien que les colonnes calculées stockées semblent être une bonne solution pour gérer les horodateurs de la dernière mise à jour, les bases de données ne permettent généralement pas de spécifier des fonctions telles que `GETDATE()` dans une colonne calculée. Vous pouvez également définir un déclencheur de base de données pour obtenir le même effet :

```sql
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    DECLARE @Id INT

    SELECT @Id = INSERTED.BlogId
    FROM INSERTED

    UPDATE dbo.Blogs
    SET LastUpdated = GETDATE()
    WHERE BlogId = @Id
END
```

Pour plus d’informations sur la création de déclencheurs, [consultez la documentation sur l’utilisation des données SQL brutes dans les migrations](xref:core/managing-schemas/migrations/managing#adding-raw-sql).

## <a name="overriding-value-generation"></a>Remplacer la génération de valeurs

Bien qu’une propriété soit configurée pour la génération de valeurs, dans de nombreux cas, vous pouvez toujours spécifier explicitement une valeur pour celle-ci. Le fonctionnement de cette opération dépend du mécanisme de génération de valeur spécifique qui a été configuré ; vous pouvez spécifier une valeur explicite au lieu d’utiliser la valeur par défaut d’une colonne, mais cela ne peut pas être fait avec des colonnes calculées.

Pour remplacer la génération de valeur par une valeur explicite, affectez simplement à la propriété une valeur qui n’est pas la valeur CLR par défaut pour le type de cette propriété ( `null` pour, pour, pour, `string` `0` `int` `Guid.Empty` `Guid` etc.).

> [!NOTE]
> La tentative d’insertion de valeurs explicites dans SQL Server identité échoue par défaut ; [consultez ces documents pour obtenir une solution de contournement](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).

Pour fournir une valeur explicite pour les propriétés qui ont été configurées comme valeur générées lors de l’ajout ou de la mise à jour, vous devez également configurer la propriété comme suit :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a>Aucune génération de valeur

Outre les scénarios spécifiques tels que ceux décrits ci-dessus, les propriétés n’ont généralement pas de génération de valeur configurée ; Cela signifie qu’il revient à l’application de toujours fournir une valeur à enregistrer dans la base de données. Cette valeur doit être assignée aux nouvelles entités avant d’être ajoutées au contexte.

Toutefois, dans certains cas, vous souhaiterez peut-être désactiver la génération de valeur qui a été configurée par Convention. Par exemple, une clé primaire de type int est généralement configurée implicitement comme valeur générée à l’ajout (par exemple, colonne d’identité sur SQL Server). Vous pouvez désactiver ce code à l’aide des éléments suivants :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
