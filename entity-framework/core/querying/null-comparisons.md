---
title: Comparaisons avec les valeurs NULL dans les requêtes
description: Informations sur la façon dont Entity Framework Core gère les comparaisons de valeurs NULL dans les requêtes
author: maumar
ms.date: 11/11/2020
uid: core/querying/null-comparisons
ms.openlocfilehash: fc63d0e0e6aea09e46b1700152312d4b74270219
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983350"
---
# <a name="query-null-semantics"></a><span data-ttu-id="3be7b-103">Sémantique de requête null</span><span class="sxs-lookup"><span data-stu-id="3be7b-103">Query null semantics</span></span>

## <a name="introduction"></a><span data-ttu-id="3be7b-104">Introduction</span><span class="sxs-lookup"><span data-stu-id="3be7b-104">Introduction</span></span>

<span data-ttu-id="3be7b-105">Les bases de données SQL opèrent sur une logique à 3 valeurs ( `true` , `false` , `null` ) lors d’opérations de comparaison, par opposition à la logique booléenne de C#.</span><span class="sxs-lookup"><span data-stu-id="3be7b-105">SQL databases operate on 3-valued logic (`true`, `false`, `null`) when performing comparisons, as opposed to the boolean logic of C#.</span></span> <span data-ttu-id="3be7b-106">Lors de la traduction de requêtes LINQ en SQL, EF Core tente de compenser la différence en introduisant des vérifications de valeur null supplémentaires pour certains éléments de la requête.</span><span class="sxs-lookup"><span data-stu-id="3be7b-106">When translating LINQ queries to SQL, EF Core tries to compensate for the difference by introducing additional null checks for some elements of the query.</span></span>
<span data-ttu-id="3be7b-107">Pour illustrer cela, nous allons définir l’entité suivante :</span><span class="sxs-lookup"><span data-stu-id="3be7b-107">To illustrate this, let's define the following entity:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

<span data-ttu-id="3be7b-108">et émettez plusieurs requêtes :</span><span class="sxs-lookup"><span data-stu-id="3be7b-108">and issue several queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

<span data-ttu-id="3be7b-109">Les deux premières requêtes produisent des comparaisons simples.</span><span class="sxs-lookup"><span data-stu-id="3be7b-109">The first two queries produce simple comparisons.</span></span> <span data-ttu-id="3be7b-110">Dans la première requête, les deux colonnes n’acceptent pas les valeurs NULL, de sorte que les contrôles NULL ne sont pas nécessaires.</span><span class="sxs-lookup"><span data-stu-id="3be7b-110">In the first query, both columns are non-nullable so null checks are not needed.</span></span> <span data-ttu-id="3be7b-111">Dans la deuxième requête, `NullableInt` peut contenir `null` , mais `Id` n’accepte pas les valeurs `null` null ; la comparaison avec une valeur non null est `null` un résultat, qui serait filtré par `WHERE` opération.</span><span class="sxs-lookup"><span data-stu-id="3be7b-111">In the second query, `NullableInt` could contain `null`, but `Id` is non-nullable; comparing `null` to non-null yields `null` as a result, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="3be7b-112">Par conséquent, aucun terme supplémentaire n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="3be7b-112">So no additional terms are needed either.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

<span data-ttu-id="3be7b-113">La troisième requête introduit une vérification de valeur null.</span><span class="sxs-lookup"><span data-stu-id="3be7b-113">The third query introduces a null check.</span></span> <span data-ttu-id="3be7b-114">Lorsque `NullableInt` est `null` , la comparaison `Id <> NullableInt` produit `null` , qui serait filtrée par `WHERE` opération.</span><span class="sxs-lookup"><span data-stu-id="3be7b-114">When `NullableInt` is `null` the comparison `Id <> NullableInt` yields `null`, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="3be7b-115">Toutefois, dans la perspective logique booléenne, ce cas doit être retourné dans le cadre du résultat.</span><span class="sxs-lookup"><span data-stu-id="3be7b-115">However, from the boolean logic perspective this case should be returned as part of the result.</span></span> <span data-ttu-id="3be7b-116">Par conséquent, EF Core ajoute la vérification nécessaire pour garantir cela.</span><span class="sxs-lookup"><span data-stu-id="3be7b-116">Hence EF Core adds the necessary check to ensure that.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

<span data-ttu-id="3be7b-117">Les requêtes quatre et cinq affichent le modèle lorsque les deux colonnes acceptent les valeurs NULL.</span><span class="sxs-lookup"><span data-stu-id="3be7b-117">Queries four and five show the pattern when both columns are nullable.</span></span> <span data-ttu-id="3be7b-118">Il est à noter que l' `<>` opération produit une requête plus compliquée (et potentiellement plus lente) que l' `==` opération.</span><span class="sxs-lookup"><span data-stu-id="3be7b-118">It's worth noting that the `<>` operation produces more complicated (and potentially slower) query than the `==` operation.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a><span data-ttu-id="3be7b-119">Traitement des valeurs Nullable dans les fonctions</span><span class="sxs-lookup"><span data-stu-id="3be7b-119">Treatment of nullable values in functions</span></span>

<span data-ttu-id="3be7b-120">De nombreuses fonctions de SQL peuvent retourner un `null` résultat uniquement si certains de leurs arguments sont `null` .</span><span class="sxs-lookup"><span data-stu-id="3be7b-120">Many functions in SQL can only return a `null` result if some of their arguments are `null`.</span></span> <span data-ttu-id="3be7b-121">EF Core tire parti de cette solution pour produire des requêtes plus efficaces.</span><span class="sxs-lookup"><span data-stu-id="3be7b-121">EF Core takes advantage of this to produce more efficient queries.</span></span>
<span data-ttu-id="3be7b-122">La requête ci-dessous illustre l’optimisation :</span><span class="sxs-lookup"><span data-stu-id="3be7b-122">The query below illustrates the optimization:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

<span data-ttu-id="3be7b-123">Le SQL généré est le suivant (il n’est pas nécessaire d’évaluer la `SUBSTRING` fonction, car elle sera uniquement NULL lorsque l’un des arguments est null.) :</span><span class="sxs-lookup"><span data-stu-id="3be7b-123">The generated SQL is as follows (we don't need to evaluate the `SUBSTRING` function since it will be only null when either of the arguments to it is null.):</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

<span data-ttu-id="3be7b-124">L’optimisation peut également être utilisée pour les fonctions définies par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3be7b-124">The optimization can also be used for user-defined functions.</span></span> <span data-ttu-id="3be7b-125">Pour plus d’informations, consultez la page [mappage des fonctions définies](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3be7b-125">See [user defined function mapping](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) page for more details.</span></span>

## <a name="writing-performant-queries"></a><span data-ttu-id="3be7b-126">Écriture de requêtes performantes</span><span class="sxs-lookup"><span data-stu-id="3be7b-126">Writing performant queries</span></span>

- <span data-ttu-id="3be7b-127">La comparaison de colonnes n’acceptant pas les valeurs NULL est plus simple et plus rapide que la comparaison des colonnes Nullable.</span><span class="sxs-lookup"><span data-stu-id="3be7b-127">Comparing non-nullable columns is simpler and faster than comparing nullable columns.</span></span> <span data-ttu-id="3be7b-128">Envisagez de marquer les colonnes comme n’acceptant pas les valeurs NULL chaque fois que possible.</span><span class="sxs-lookup"><span data-stu-id="3be7b-128">Consider marking columns as non-nullable whenever possible.</span></span>

- <span data-ttu-id="3be7b-129">La vérification de l’égalité ( `==` ) est plus simple et plus rapide que la vérification de l’absence d’égalité ( `!=` ), car la requête n’a pas besoin de faire la distinction entre `null` et le `false` résultat.</span><span class="sxs-lookup"><span data-stu-id="3be7b-129">Checking for equality (`==`) is simpler and faster than checking for non-equality (`!=`), because query doesn't need to distinguish between `null` and `false` result.</span></span> <span data-ttu-id="3be7b-130">Utilisez la comparaison d’égalité chaque fois que possible.</span><span class="sxs-lookup"><span data-stu-id="3be7b-130">Use equality comparison whenever possible.</span></span> <span data-ttu-id="3be7b-131">Toutefois, le fait de nier simplement `==` la comparaison est identique à `!=` , donc cela n’entraîne pas d’amélioration des performances.</span><span class="sxs-lookup"><span data-stu-id="3be7b-131">However, simply negating `==` comparison is effectively the same as `!=`, so it doesn't result in performance improvement.</span></span>

- <span data-ttu-id="3be7b-132">Dans certains cas, il est possible de simplifier une comparaison complexe en filtrant les `null` valeurs d’une colonne de manière explicite, par exemple, si aucune `null` valeur n’est présente ou si ces valeurs ne sont pas pertinentes dans le résultat.</span><span class="sxs-lookup"><span data-stu-id="3be7b-132">In some cases, it is possible to simplify a complex comparison by filtering out `null` values from a column explicitly - for example when no `null` values are present or these values are not relevant in the result.</span></span> <span data-ttu-id="3be7b-133">Prenons l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="3be7b-133">Consider the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

<span data-ttu-id="3be7b-134">Ces requêtes produisent le code SQL suivant :</span><span class="sxs-lookup"><span data-stu-id="3be7b-134">These queries produce the following SQL:</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

<span data-ttu-id="3be7b-135">Dans la deuxième requête, les `null` résultats sont filtrés à partir de la `String1` colonne de manière explicite.</span><span class="sxs-lookup"><span data-stu-id="3be7b-135">In the second query, `null` results are filtered out from `String1` column explicitly.</span></span> <span data-ttu-id="3be7b-136">EF Core pouvez traiter en toute sécurité la `String1` colonne comme n’acceptant pas les valeurs null pendant la comparaison, ce qui se traduit par une requête plus simple.</span><span class="sxs-lookup"><span data-stu-id="3be7b-136">EF Core can safely treat the `String1` column as non-nullable during comparison, resulting in a simpler query.</span></span>

## <a name="using-relational-null-semantics"></a><span data-ttu-id="3be7b-137">Utilisation de la sémantique de valeurs NULL relationnelles</span><span class="sxs-lookup"><span data-stu-id="3be7b-137">Using relational null semantics</span></span>

<span data-ttu-id="3be7b-138">Il est possible de désactiver la compensation de comparaison null et d’utiliser directement la sémantique de valeurs NULL relationnelles.</span><span class="sxs-lookup"><span data-stu-id="3be7b-138">It's possible to disable the null comparison compensation and use relational null semantics directly.</span></span> <span data-ttu-id="3be7b-139">Pour ce faire, appelez `UseRelationalNulls(true)` la méthode sur le générateur d’options à l’intérieur de la `OnConfiguring` méthode :</span><span class="sxs-lookup"><span data-stu-id="3be7b-139">This can be done by calling `UseRelationalNulls(true)` method on the options builder inside `OnConfiguring` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> <span data-ttu-id="3be7b-140">Lorsque vous utilisez la sémantique de valeurs NULL relationnelles, vos requêtes LINQ n’ont plus la même signification que dans C# et peuvent produire des résultats différents de ceux attendus.</span><span class="sxs-lookup"><span data-stu-id="3be7b-140">When using relational null semantics, your LINQ queries no longer have the same meaning as they do in C#, and may yield different results than expected.</span></span> <span data-ttu-id="3be7b-141">Soyez prudent lorsque vous utilisez ce mode.</span><span class="sxs-lookup"><span data-stu-id="3be7b-141">Exercise caution when using this mode.</span></span>
