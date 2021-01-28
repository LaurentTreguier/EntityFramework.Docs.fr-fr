---
title: Valeurs générées-EF Core
description: Comment configurer la génération de valeurs pour les propriétés lors de l’utilisation de Entity Framework Core
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: 76fa4454c88a5ef7afb9864c2a4b1063ac75e37e
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983545"
---
# <a name="generated-values"></a><span data-ttu-id="d439b-103">Valeurs générées</span><span class="sxs-lookup"><span data-stu-id="d439b-103">Generated Values</span></span>

<span data-ttu-id="d439b-104">Les valeurs des colonnes de base de données peuvent être générées de différentes façons : les colonnes clés primaires sont souvent des entiers à incrémentation automatique, les autres colonnes ont des valeurs par défaut ou calculées, etc. Cette page détaille les différents modèles de génération de valeur de configuration avec EF Core.</span><span class="sxs-lookup"><span data-stu-id="d439b-104">Database columns can have their values generated in various ways: primary key columns are frequently auto-incrementing integers, other columns have default or computed values, etc. This page details various patterns for configuration value generation with EF Core.</span></span>

## <a name="default-values"></a><span data-ttu-id="d439b-105">Valeurs par défaut</span><span class="sxs-lookup"><span data-stu-id="d439b-105">Default values</span></span>

<span data-ttu-id="d439b-106">Sur les bases de données relationnelles, une colonne peut être configurée avec une valeur par défaut ; Si une ligne est insérée sans valeur pour cette colonne, la valeur par défaut est utilisée.</span><span class="sxs-lookup"><span data-stu-id="d439b-106">On relational databases, a column can be configured with a default value; if a row is inserted without a value for that column, the default value will be used.</span></span>

<span data-ttu-id="d439b-107">Vous pouvez configurer une valeur par défaut sur une propriété :</span><span class="sxs-lookup"><span data-stu-id="d439b-107">You can configure a default value on a property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

<span data-ttu-id="d439b-108">Vous pouvez également spécifier un fragment SQL utilisé pour calculer la valeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="d439b-108">You can also specify a SQL fragment that is used to calculate the default value:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a><span data-ttu-id="d439b-109">Colonnes calculées</span><span class="sxs-lookup"><span data-stu-id="d439b-109">Computed columns</span></span>

<span data-ttu-id="d439b-110">Sur la plupart des bases de données relationnelles, une colonne peut être configurée de manière à ce que sa valeur soit calculée dans la base de données, généralement avec une expression faisant référence à d’autres colonnes :</span><span class="sxs-lookup"><span data-stu-id="d439b-110">On most relational databases, a column can be configured to have its value computed in the database, typically with an expression referring to other columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

<span data-ttu-id="d439b-111">L’instruction ci-dessus crée une colonne calculée *virtuelle* , dont la valeur est calculée chaque fois qu’elle est extraite de la base de données.</span><span class="sxs-lookup"><span data-stu-id="d439b-111">The above creates a *virtual* computed column, whose value is computed every time it is fetched from the database.</span></span> <span data-ttu-id="d439b-112">Vous pouvez également spécifier qu’une colonne calculée soit *stockée* (parfois appelée *PERSISTED*), ce qui signifie qu’elle est calculée à chaque mise à jour de la ligne et qu’elle est stockée sur le disque avec les colonnes normales :</span><span class="sxs-lookup"><span data-stu-id="d439b-112">You may also specify that a computed column be *stored* (sometimes called *persisted*), meaning that it is computed on every update of the row, and is stored on disk alongside regular columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> <span data-ttu-id="d439b-113">La prise en charge de la création de colonnes calculées stockées a été ajoutée dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="d439b-113">Support for creating stored computed columns was added in EF Core 5.0.</span></span>

## <a name="primary-keys"></a><span data-ttu-id="d439b-114">Clés primaires</span><span class="sxs-lookup"><span data-stu-id="d439b-114">Primary keys</span></span>

<span data-ttu-id="d439b-115">Par Convention, les clés primaires non composites de type short, int, long ou GUID sont configurées pour avoir des valeurs générées pour les entités insérées si une valeur n’est pas fournie par l’application.</span><span class="sxs-lookup"><span data-stu-id="d439b-115">By convention, non-composite primary keys of type short, int, long, or Guid are set up to have values generated for inserted entities if a value isn't provided by the application.</span></span> <span data-ttu-id="d439b-116">Votre fournisseur de base de données prend généralement en charge la configuration requise ; par exemple, une clé primaire numérique dans SQL Server est automatiquement configurée pour être une colonne d’identité.</span><span class="sxs-lookup"><span data-stu-id="d439b-116">Your database provider typically takes care of the necessary configuration; for example, a numeric primary key in SQL Server is automatically set up to be an IDENTITY column.</span></span>

<span data-ttu-id="d439b-117">Pour plus d’informations, [consultez la documentation sur les clés](xref:core/modeling/keys).</span><span class="sxs-lookup"><span data-stu-id="d439b-117">For more information, [see the documentation about keys](xref:core/modeling/keys).</span></span>

## <a name="explicitly-configuring-value-generation"></a><span data-ttu-id="d439b-118">Configuration explicite de la génération de valeurs</span><span class="sxs-lookup"><span data-stu-id="d439b-118">Explicitly configuring value generation</span></span>

<span data-ttu-id="d439b-119">Nous avons vu plus haut que EF Core configure automatiquement la génération de valeurs pour les clés primaires, mais nous pouvons souhaiter faire de même pour les propriétés non-clés.</span><span class="sxs-lookup"><span data-stu-id="d439b-119">We saw above that EF Core automatically sets up value generation for primary keys - but we may want to do the same for non-key properties.</span></span> <span data-ttu-id="d439b-120">Vous pouvez configurer n’importe quelle propriété pour que sa valeur soit générée pour les entités insérées comme suit :</span><span class="sxs-lookup"><span data-stu-id="d439b-120">You can configure any property to have its value generated for inserted entities as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d439b-121">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="d439b-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[<span data-ttu-id="d439b-122">API Fluent</span><span class="sxs-lookup"><span data-stu-id="d439b-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

<span data-ttu-id="d439b-123">De même, une propriété peut être configurée de manière à ce que sa valeur soit générée lors de l’ajout ou de la mise à jour :</span><span class="sxs-lookup"><span data-stu-id="d439b-123">Similarly, a property can be configured to have its value generated on add or update:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d439b-124">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="d439b-124">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[<span data-ttu-id="d439b-125">API Fluent</span><span class="sxs-lookup"><span data-stu-id="d439b-125">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

<span data-ttu-id="d439b-126">\*\*_</span><span class="sxs-lookup"><span data-stu-id="d439b-126">\*\*_</span></span>

> [!WARNING]
> <span data-ttu-id="d439b-127">Contrairement aux valeurs par défaut ou aux colonnes calculées, nous ne spécifions pas _how \* les valeurs doivent être générées ; Cela dépend du fournisseur de base de données utilisé.</span><span class="sxs-lookup"><span data-stu-id="d439b-127">Unlike with default values or computed columns, we are not specifying _how\* the values are to be generated; that depends on the database provider being used.</span></span> <span data-ttu-id="d439b-128">Les fournisseurs de bases de données peuvent configurer automatiquement la génération de valeur pour certains types de propriété, mais d’autres peuvent vous obliger à configurer manuellement la manière dont la valeur est générée.</span><span class="sxs-lookup"><span data-stu-id="d439b-128">Database providers may automatically set up value generation for some property types, but others may require you to manually set up how the value is generated.</span></span>
>
> <span data-ttu-id="d439b-129">Par exemple, sur SQL Server, quand une propriété GUID est configurée en tant que valeur générée lors de l’ajout, le fournisseur effectue automatiquement la génération de valeur côté client, à l’aide d’un algorithme pour générer des valeurs GUID séquentielles optimales.</span><span class="sxs-lookup"><span data-stu-id="d439b-129">For example, on SQL Server, when a GUID property is configured as value generated on add, the provider automatically performs value generation client-side, using an algorithm to generate optimal sequential GUID values.</span></span> <span data-ttu-id="d439b-130">Toutefois, `ValueGeneratedOnAdd()` la spécification de sur une propriété DateTime n’a aucun effet ([consultez la section ci-dessous pour la génération de la valeur DateTime](#datetime-value-generation)).</span><span class="sxs-lookup"><span data-stu-id="d439b-130">However, specifying `ValueGeneratedOnAdd()` on a DateTime property will have no effect ([see the section below for DateTime value generation](#datetime-value-generation)).</span></span>
>
> <span data-ttu-id="d439b-131">De même, les propriétés Byte [] qui sont configurées comme générées lors de l’ajout ou de la mise à jour et marquées comme jetons d’accès concurrentiel sont configurées avec le type de données rowversion, afin que les valeurs soient générées automatiquement dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="d439b-131">Similarly, byte[] properties that are configured as generated on add or update and marked as concurrency tokens are set up with the rowversion data type, so that values are automatically generated in the database.</span></span> <span data-ttu-id="d439b-132">Toutefois, si vous spécifiez, l’opération n’aura `ValueGeneratedOnAddOrUpdate()` aucun effet.</span><span class="sxs-lookup"><span data-stu-id="d439b-132">However, specifying `ValueGeneratedOnAddOrUpdate()` will again have no effect.</span></span>
>
> [!NOTE]
> <span data-ttu-id="d439b-133">Selon le fournisseur de base de données utilisé, les valeurs peuvent être générées côté client par EF ou dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="d439b-133">Depending on the database provider being used, values may be generated client side by EF or in the database.</span></span> <span data-ttu-id="d439b-134">Si la valeur est générée par la base de données, EF peut affecter une valeur temporaire lorsque vous ajoutez l’entité au contexte. Cette valeur temporaire sera ensuite remplacée par la valeur générée par la base de données pendant `SaveChanges()` .</span><span class="sxs-lookup"><span data-stu-id="d439b-134">If the value is generated by the database, then EF may assign a temporary value when you add the entity to the context; this temporary value will then be replaced by the database generated value during `SaveChanges()`.</span></span> <span data-ttu-id="d439b-135">Pour plus d’informations, [consultez la documentation sur les valeurs temporaires](xref:core/change-tracking/explicit-tracking#temporary-values).</span><span class="sxs-lookup"><span data-stu-id="d439b-135">For more information, [see the docs on temporary values](xref:core/change-tracking/explicit-tracking#temporary-values).</span></span>

## <a name="datetime-value-generation"></a><span data-ttu-id="d439b-136">Génération de la valeur de date/heure</span><span class="sxs-lookup"><span data-stu-id="d439b-136">Date/time value generation</span></span>

<span data-ttu-id="d439b-137">Une demande courante consiste à avoir une colonne de base de données qui contient la date/l’heure de la première insertion de la colonne (valeur générée lors de l’ajout), ou la date de dernière mise à jour (valeur générée lors de l’ajout ou de la mise à jour).</span><span class="sxs-lookup"><span data-stu-id="d439b-137">A common request is to have a database column which contains the date/time for when the column was first inserted (value generated on add), or for when it was last updated (value generated on add or update).</span></span> <span data-ttu-id="d439b-138">Comme il existe plusieurs stratégies pour ce faire, les fournisseurs de EF Core ne configurent pas automatiquement la génération de valeur pour les colonnes de date/heure : vous devez configurer cela vous-même.</span><span class="sxs-lookup"><span data-stu-id="d439b-138">As there are various strategies to do this, EF Core providers usually don't set up value generation automatically for date/time columns - you have to configure this yourself.</span></span>

### <a name="creation-timestamp"></a><span data-ttu-id="d439b-139">Horodateur de création</span><span class="sxs-lookup"><span data-stu-id="d439b-139">Creation timestamp</span></span>

<span data-ttu-id="d439b-140">La configuration d’une colonne de date/heure pour avoir l’horodateur de création de la ligne est généralement une question de configuration d’une valeur par défaut avec la fonction SQL appropriée.</span><span class="sxs-lookup"><span data-stu-id="d439b-140">Configuring a date/time column to have the creation timestamp of the row is usually a matter of configuring a default value with the appropriate SQL function.</span></span> <span data-ttu-id="d439b-141">Par exemple, sur SQL Server vous pouvez utiliser les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="d439b-141">For example, on SQL Server you may use the following:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

<span data-ttu-id="d439b-142">Veillez à sélectionner la fonction appropriée, car plusieurs peuvent exister (par exemple, `GETDATE()` vs. `GETUTCDATE()` ).</span><span class="sxs-lookup"><span data-stu-id="d439b-142">Be sure to select the appropriate function, as several may exist (e.g. `GETDATE()` vs. `GETUTCDATE()`).</span></span>

### <a name="update-timestamp"></a><span data-ttu-id="d439b-143">Horodateur de la mise à jour</span><span class="sxs-lookup"><span data-stu-id="d439b-143">Update timestamp</span></span>

<span data-ttu-id="d439b-144">Bien que les colonnes calculées stockées semblent être une bonne solution pour gérer les horodateurs de la dernière mise à jour, les bases de données ne permettent généralement pas de spécifier des fonctions telles que `GETDATE()` dans une colonne calculée.</span><span class="sxs-lookup"><span data-stu-id="d439b-144">Although stored computed columns seem like a good solution for managing last-updated timestamps, databases usually don't allow specifying functions such as `GETDATE()` in a computed column.</span></span> <span data-ttu-id="d439b-145">Vous pouvez également définir un déclencheur de base de données pour obtenir le même effet :</span><span class="sxs-lookup"><span data-stu-id="d439b-145">As an alternative, you can set up a database trigger to achieve the same effect:</span></span>

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

<span data-ttu-id="d439b-146">Pour plus d’informations sur la création de déclencheurs, [consultez la documentation sur l’utilisation des données SQL brutes dans les migrations](xref:core/managing-schemas/migrations/managing#adding-raw-sql).</span><span class="sxs-lookup"><span data-stu-id="d439b-146">For information on creating triggers, [see the documentation on using raw SQL in migrations](xref:core/managing-schemas/migrations/managing#adding-raw-sql).</span></span>

## <a name="overriding-value-generation"></a><span data-ttu-id="d439b-147">Remplacer la génération de valeurs</span><span class="sxs-lookup"><span data-stu-id="d439b-147">Overriding value generation</span></span>

<span data-ttu-id="d439b-148">Bien qu’une propriété soit configurée pour la génération de valeurs, dans de nombreux cas, vous pouvez toujours spécifier explicitement une valeur pour celle-ci.</span><span class="sxs-lookup"><span data-stu-id="d439b-148">Although a property is configured for value generation, in many cases you may still explicitly specify a value for it.</span></span> <span data-ttu-id="d439b-149">Le fonctionnement de cette opération dépend du mécanisme de génération de valeur spécifique qui a été configuré ; vous pouvez spécifier une valeur explicite au lieu d’utiliser la valeur par défaut d’une colonne, mais cela ne peut pas être fait avec des colonnes calculées.</span><span class="sxs-lookup"><span data-stu-id="d439b-149">Whether this will actually work depends on the specific value generation mechanism that has been configured; while you may specify an explicit value instead of using a column's default value, the same cannot be done with computed columns.</span></span>

<span data-ttu-id="d439b-150">Pour remplacer la génération de valeur par une valeur explicite, affectez simplement à la propriété une valeur qui n’est pas la valeur CLR par défaut pour le type de cette propriété ( `null` pour, pour, pour, `string` `0` `int` `Guid.Empty` `Guid` etc.).</span><span class="sxs-lookup"><span data-stu-id="d439b-150">To override value generation with an explicit value, simply set the property to any value that is not the CLR default value for that property's type (`null` for `string`, `0` for `int`, `Guid.Empty` for `Guid`, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="d439b-151">La tentative d’insertion de valeurs explicites dans SQL Server identité échoue par défaut ; [consultez ces documents pour obtenir une solution de contournement](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).</span><span class="sxs-lookup"><span data-stu-id="d439b-151">Trying to insert explicit values into SQL Server IDENTITY fails by default; [see these docs for a workaround](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).</span></span>

<span data-ttu-id="d439b-152">Pour fournir une valeur explicite pour les propriétés qui ont été configurées comme valeur générées lors de l’ajout ou de la mise à jour, vous devez également configurer la propriété comme suit :</span><span class="sxs-lookup"><span data-stu-id="d439b-152">To provide an explicit value for properties that have been configured as value generated on add or update, you must also configure the property as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a><span data-ttu-id="d439b-153">Aucune génération de valeur</span><span class="sxs-lookup"><span data-stu-id="d439b-153">No value generation</span></span>

<span data-ttu-id="d439b-154">Outre les scénarios spécifiques tels que ceux décrits ci-dessus, les propriétés n’ont généralement pas de génération de valeur configurée ; Cela signifie qu’il revient à l’application de toujours fournir une valeur à enregistrer dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="d439b-154">Apart from specific scenarios such as those described above, properties typically have no value generation configured; this means that it's up to the application to always supply a value to be saved to the database.</span></span> <span data-ttu-id="d439b-155">Cette valeur doit être assignée aux nouvelles entités avant d’être ajoutées au contexte.</span><span class="sxs-lookup"><span data-stu-id="d439b-155">This value must be assigned to new entities before they are added to the context.</span></span>

<span data-ttu-id="d439b-156">Toutefois, dans certains cas, vous souhaiterez peut-être désactiver la génération de valeur qui a été configurée par Convention.</span><span class="sxs-lookup"><span data-stu-id="d439b-156">However, in some cases you may want to disable value generation that has been set up by convention.</span></span> <span data-ttu-id="d439b-157">Par exemple, une clé primaire de type int est généralement configurée implicitement comme valeur générée à l’ajout (par exemple, colonne d’identité sur SQL Server).</span><span class="sxs-lookup"><span data-stu-id="d439b-157">For example, a primary key of type int is usually implicitly configured as value-generated-on-add (e.g. identity column on SQL Server).</span></span> <span data-ttu-id="d439b-158">Vous pouvez désactiver ce code à l’aide des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="d439b-158">You can disable this via the following:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d439b-159">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="d439b-159">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="d439b-160">API Fluent</span><span class="sxs-lookup"><span data-stu-id="d439b-160">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
