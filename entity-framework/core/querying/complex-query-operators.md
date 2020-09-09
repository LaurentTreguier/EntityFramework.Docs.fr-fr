---
title: Opérateurs de requête complexes-EF Core
description: Informations détaillées sur les opérateurs de requête LINQ plus complexes lors de l’utilisation de Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 2e187a2a-4072-4198-9040-aaad68e424fd
uid: core/querying/complex-query-operators
ms.openlocfilehash: 61a46361cc4bc1635174aaf2ca3260fa67fc6aaf
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616445"
---
# <a name="complex-query-operators"></a><span data-ttu-id="85ced-103">Opérateurs de requête complexes</span><span class="sxs-lookup"><span data-stu-id="85ced-103">Complex Query Operators</span></span>

<span data-ttu-id="85ced-104">LINQ (Language Integrated Query) contient de nombreux opérateurs complexes, qui combinent plusieurs sources de données ou effectuent un traitement complexe.</span><span class="sxs-lookup"><span data-stu-id="85ced-104">Language Integrated Query (LINQ) contains many complex operators, which combine multiple data sources or does complex processing.</span></span> <span data-ttu-id="85ced-105">Les opérateurs LINQ n’ont pas tous des traductions appropriées côté serveur.</span><span class="sxs-lookup"><span data-stu-id="85ced-105">Not all LINQ operators have suitable translations on the server side.</span></span> <span data-ttu-id="85ced-106">Parfois, une requête dans un formulaire se traduit par le serveur, mais s’il est écrit dans un autre formulaire, même si le résultat est le même.</span><span class="sxs-lookup"><span data-stu-id="85ced-106">Sometimes, a query in one form translates to the server but if written in a different form doesn't translate even if the result is the same.</span></span> <span data-ttu-id="85ced-107">Cette page décrit certains des opérateurs complexes et leurs variations prises en charge.</span><span class="sxs-lookup"><span data-stu-id="85ced-107">This page describes some of the complex operators and their supported variations.</span></span> <span data-ttu-id="85ced-108">Dans les versions ultérieures, nous pouvons reconnaître plus de modèles et ajouter les traductions correspondantes.</span><span class="sxs-lookup"><span data-stu-id="85ced-108">In future releases, we may recognize more patterns and add their corresponding translations.</span></span> <span data-ttu-id="85ced-109">Il est également important de garder à l’esprit que la prise en charge de la traduction varie d’un fournisseur à l’autre.</span><span class="sxs-lookup"><span data-stu-id="85ced-109">It's also important to keep in mind that translation support varies between providers.</span></span> <span data-ttu-id="85ced-110">Une requête particulière, qui est traduite dans SqlServer, peut ne pas fonctionner pour les bases de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="85ced-110">A particular query, which is translated in SqlServer, may not work for SQLite databases.</span></span>

> [!TIP]
> <span data-ttu-id="85ced-111">Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="85ced-111">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="join"></a><span data-ttu-id="85ced-112">Join</span><span class="sxs-lookup"><span data-stu-id="85ced-112">Join</span></span>

<span data-ttu-id="85ced-113">L’opérateur de jointure LINQ vous permet de connecter deux sources de données en fonction du sélecteur de clé pour chaque source, en générant un tuple de valeurs lorsque la clé correspond.</span><span class="sxs-lookup"><span data-stu-id="85ced-113">The LINQ Join operator allows you to connect two data sources based on the key selector for each source, generating a tuple of values when the key matches.</span></span> <span data-ttu-id="85ced-114">Il se traduit naturellement par `INNER JOIN` sur les bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="85ced-114">It naturally translates to `INNER JOIN` on relational databases.</span></span> <span data-ttu-id="85ced-115">Alors que la jointure LINQ a des sélecteurs de clé externe et interne, la base de données requiert une seule condition de jointure.</span><span class="sxs-lookup"><span data-stu-id="85ced-115">While the LINQ Join has outer and inner key selectors, the database requires a single join condition.</span></span> <span data-ttu-id="85ced-116">Ainsi EF Core génère une condition de jointure en comparant le sélecteur de clé externe au sélecteur de clé interne pour l’égalité.</span><span class="sxs-lookup"><span data-stu-id="85ced-116">So EF Core generates a join condition by comparing the outer key selector to the inner key selector for equality.</span></span> <span data-ttu-id="85ced-117">En outre, si les sélecteurs de clé sont des types anonymes, EF Core génère une condition de jointure pour comparer l’égalité des composants.</span><span class="sxs-lookup"><span data-stu-id="85ced-117">Further, if the key selectors are anonymous types, EF Core generates a join condition to compare equality component wise.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a><span data-ttu-id="85ced-118">GroupJoin</span><span class="sxs-lookup"><span data-stu-id="85ced-118">GroupJoin</span></span>

<span data-ttu-id="85ced-119">L’opérateur LINQ GroupJoin vous permet de connecter deux sources de données similaires à Join, mais elle crée un groupe de valeurs internes pour les éléments externes correspondants.</span><span class="sxs-lookup"><span data-stu-id="85ced-119">The LINQ GroupJoin operator allows you to connect two data sources similar to Join, but it creates a group of inner values for matching outer elements.</span></span> <span data-ttu-id="85ced-120">L’exécution d’une requête comme l’exemple suivant génère un résultat de `Blog`  &  `IEnumerable<Post>` .</span><span class="sxs-lookup"><span data-stu-id="85ced-120">Executing a query like the following example generates a result of `Blog` & `IEnumerable<Post>`.</span></span> <span data-ttu-id="85ced-121">Étant donné que les bases de données (notamment les bases de données relationnelles) n’ont pas de moyen de représenter une collection d’objets côté client, GroupJoin ne se traduit pas par le serveur dans de nombreux cas.</span><span class="sxs-lookup"><span data-stu-id="85ced-121">Since databases (especially relational databases) don't have a way to represent a collection of client-side objects, GroupJoin doesn't translate to the server in many cases.</span></span> <span data-ttu-id="85ced-122">Elle nécessite que vous obteniez toutes les données du serveur pour effectuer la GroupJoin sans sélecteur spécial (première requête ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="85ced-122">It requires you to get all of the data from the server to do GroupJoin without a special selector (first query below).</span></span> <span data-ttu-id="85ced-123">Toutefois, si le sélecteur limite les données sélectionnées, l’extraction de toutes les données du serveur peut entraîner des problèmes de performances (deuxième requête ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="85ced-123">But if the selector is limiting data being selected then fetching all of the data from the server may cause performance issues (second query below).</span></span> <span data-ttu-id="85ced-124">C’est la raison pour laquelle EF Core ne traduit pas GroupJoin.</span><span class="sxs-lookup"><span data-stu-id="85ced-124">That's why EF Core doesn't translate GroupJoin.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a><span data-ttu-id="85ced-125">SelectMany</span><span class="sxs-lookup"><span data-stu-id="85ced-125">SelectMany</span></span>

<span data-ttu-id="85ced-126">L’opérateur SelectMany de LINQ vous permet d’énumérer un sélecteur de collection pour chaque élément externe et de générer des tuples de valeurs à partir de chaque source de données.</span><span class="sxs-lookup"><span data-stu-id="85ced-126">The LINQ SelectMany operator allows you to enumerate over a collection selector for each outer element and generate tuples of values from each data source.</span></span> <span data-ttu-id="85ced-127">En d’autres termes, il s’agit d’une jointure, mais sans aucune condition, chaque élément externe est connecté avec un élément de la source de la collection.</span><span class="sxs-lookup"><span data-stu-id="85ced-127">In a way, it's a join but without any condition so every outer element is connected with an element from the collection source.</span></span> <span data-ttu-id="85ced-128">Selon la façon dont le sélecteur de collection est lié à la source de données externe, SelectMany peut traduire en différentes requêtes différentes côté serveur.</span><span class="sxs-lookup"><span data-stu-id="85ced-128">Depending on how the collection selector is related to the outer data source, SelectMany can translate into various different queries on the server side.</span></span>

### <a name="collection-selector-doesnt-reference-outer"></a><span data-ttu-id="85ced-129">Le sélecteur de collection ne fait pas référence à l’extérieur</span><span class="sxs-lookup"><span data-stu-id="85ced-129">Collection selector doesn't reference outer</span></span>

<span data-ttu-id="85ced-130">Lorsque le sélecteur de collection ne référence rien de la source externe, le résultat est un produit cartésien des deux sources de données.</span><span class="sxs-lookup"><span data-stu-id="85ced-130">When the collection selector isn't referencing anything from the outer source, the result is a cartesian product of both data sources.</span></span> <span data-ttu-id="85ced-131">Il se traduit par `CROSS JOIN` dans les bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="85ced-131">It translates to `CROSS JOIN` in relational databases.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a><span data-ttu-id="85ced-132">Le sélecteur de collection fait référence à Outer dans une clause WHERE</span><span class="sxs-lookup"><span data-stu-id="85ced-132">Collection selector references outer in a where clause</span></span>

<span data-ttu-id="85ced-133">Lorsque le sélecteur de collection a une clause WHERE, qui fait référence à l’élément externe, EF Core le convertit en une jointure de base de données et utilise le prédicat comme condition de jointure.</span><span class="sxs-lookup"><span data-stu-id="85ced-133">When the collection selector has a where clause, which references the outer element, then EF Core translates it to a database join and uses the predicate as the join condition.</span></span> <span data-ttu-id="85ced-134">Ce cas se produit généralement lors de l’utilisation de la navigation de collection sur l’élément externe comme sélecteur de collection.</span><span class="sxs-lookup"><span data-stu-id="85ced-134">Normally this case arises when using collection navigation on the outer element as the collection selector.</span></span> <span data-ttu-id="85ced-135">Si la collection est vide pour un élément externe, aucun résultat ne sera généré pour cet élément externe.</span><span class="sxs-lookup"><span data-stu-id="85ced-135">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="85ced-136">Toutefois, si `DefaultIfEmpty` est appliqué au sélecteur de collection, l’élément externe est connecté avec une valeur par défaut de l’élément interne.</span><span class="sxs-lookup"><span data-stu-id="85ced-136">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="85ced-137">En raison de cette distinction, ce type de requêtes se traduit par `INNER JOIN` en l’absence de `DefaultIfEmpty` et `LEFT JOIN` lorsque `DefaultIfEmpty` est appliqué.</span><span class="sxs-lookup"><span data-stu-id="85ced-137">Because of this distinction, this kind of queries translates to `INNER JOIN` in the absence of `DefaultIfEmpty` and `LEFT JOIN` when `DefaultIfEmpty` is applied.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a><span data-ttu-id="85ced-138">Le sélecteur de collection fait référence à l’extérieur dans un cas autre que Where</span><span class="sxs-lookup"><span data-stu-id="85ced-138">Collection selector references outer in a non-where case</span></span>

<span data-ttu-id="85ced-139">Lorsque le sélecteur de collection fait référence à l’élément externe, qui n’est pas dans une clause WHERE (comme le cas ci-dessus), il ne se convertit pas en jointure de base de données.</span><span class="sxs-lookup"><span data-stu-id="85ced-139">When the collection selector references the outer element, which isn't in a where clause (as the case above), it doesn't translate to a database join.</span></span> <span data-ttu-id="85ced-140">C’est pourquoi nous devons évaluer le sélecteur de collection pour chaque élément externe.</span><span class="sxs-lookup"><span data-stu-id="85ced-140">That's why we need to evaluate the collection selector for each outer element.</span></span> <span data-ttu-id="85ced-141">Il se traduit par `APPLY` des opérations dans de nombreuses bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="85ced-141">It translates to `APPLY` operations in many relational databases.</span></span> <span data-ttu-id="85ced-142">Si la collection est vide pour un élément externe, aucun résultat ne sera généré pour cet élément externe.</span><span class="sxs-lookup"><span data-stu-id="85ced-142">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="85ced-143">Toutefois, si `DefaultIfEmpty` est appliqué au sélecteur de collection, l’élément externe est connecté avec une valeur par défaut de l’élément interne.</span><span class="sxs-lookup"><span data-stu-id="85ced-143">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="85ced-144">En raison de cette distinction, ce type de requêtes se traduit par `CROSS APPLY` en l’absence de `DefaultIfEmpty` et `OUTER APPLY` lorsque `DefaultIfEmpty` est appliqué.</span><span class="sxs-lookup"><span data-stu-id="85ced-144">Because of this distinction, this kind of queries translates to `CROSS APPLY` in the absence of `DefaultIfEmpty` and `OUTER APPLY` when `DefaultIfEmpty` is applied.</span></span> <span data-ttu-id="85ced-145">Certaines bases de données telles que SQLite ne prennent pas en charge les `APPLY` opérateurs, ce type de requête peut ne pas être traduit.</span><span class="sxs-lookup"><span data-stu-id="85ced-145">Certain databases like SQLite don't support `APPLY` operators so this kind of query may not be translated.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a><span data-ttu-id="85ced-146">GroupBy</span><span class="sxs-lookup"><span data-stu-id="85ced-146">GroupBy</span></span>

<span data-ttu-id="85ced-147">Les opérateurs LINQ GroupBy créent un résultat de type `IGrouping<TKey, TElement>` où `TKey` et `TElement` peut être n’importe quel type arbitraire.</span><span class="sxs-lookup"><span data-stu-id="85ced-147">LINQ GroupBy operators create a result of type `IGrouping<TKey, TElement>` where `TKey` and `TElement` could be any arbitrary type.</span></span> <span data-ttu-id="85ced-148">En outre, `IGrouping` implémente `IEnumerable<TElement>` , ce qui signifie que vous pouvez le composer à l’aide d’un opérateur LINQ après le regroupement.</span><span class="sxs-lookup"><span data-stu-id="85ced-148">Furthermore, `IGrouping` implements `IEnumerable<TElement>`, which means you can compose over it using any LINQ operator after the grouping.</span></span> <span data-ttu-id="85ced-149">Dans la mesure où aucune structure de base de données ne peut représenter un `IGrouping` , les opérateurs GroupBy n’ont pas de traduction dans la plupart des cas.</span><span class="sxs-lookup"><span data-stu-id="85ced-149">Since no database structure can represent an `IGrouping`, GroupBy operators have no translation in most cases.</span></span> <span data-ttu-id="85ced-150">Quand un opérateur d’agrégation est appliqué à chaque groupe, qui retourne une valeur scalaire, il peut être traduit en SQL `GROUP BY` dans des bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="85ced-150">When an aggregate operator is applied to each group, which returns a scalar, it can be translated to SQL `GROUP BY` in relational databases.</span></span> <span data-ttu-id="85ced-151">Le SQL `GROUP BY` est également restrictif.</span><span class="sxs-lookup"><span data-stu-id="85ced-151">The SQL `GROUP BY` is restrictive too.</span></span> <span data-ttu-id="85ced-152">Elle vous oblige à regrouper uniquement par valeurs scalaires.</span><span class="sxs-lookup"><span data-stu-id="85ced-152">It requires you to group only by scalar values.</span></span> <span data-ttu-id="85ced-153">La projection peut uniquement contenir des colonnes clés de regroupement ou n’importe quel agrégat appliqué à une colonne.</span><span class="sxs-lookup"><span data-stu-id="85ced-153">The projection can only contain grouping key columns or any aggregate applied over a column.</span></span> <span data-ttu-id="85ced-154">EF Core identifie ce modèle et le convertit en serveur, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="85ced-154">EF Core identifies this pattern and translates it to the server, as in the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

<span data-ttu-id="85ced-155">EF Core convertit également les requêtes où un opérateur d’agrégation sur le regroupement s’affiche dans un opérateur LINQ WHERE ou OrderBy (ou autre classement).</span><span class="sxs-lookup"><span data-stu-id="85ced-155">EF Core also translates queries where an aggregate operator on the grouping appears in a Where or OrderBy (or other ordering) LINQ operator.</span></span> <span data-ttu-id="85ced-156">Elle utilise `HAVING` la clause dans SQL pour la clause WHERE.</span><span class="sxs-lookup"><span data-stu-id="85ced-156">It uses `HAVING` clause in SQL for the where clause.</span></span> <span data-ttu-id="85ced-157">La partie de la requête avant l’application de l’opérateur GroupBy peut être une requête complexe, à condition qu’elle puisse être traduite en serveur.</span><span class="sxs-lookup"><span data-stu-id="85ced-157">The part of the query before applying the GroupBy operator can be any complex query as long as it can be translated to server.</span></span> <span data-ttu-id="85ced-158">En outre, une fois que vous appliquez des opérateurs d’agrégation sur une requête de regroupement pour supprimer des regroupements de la source résultante, vous pouvez composer en plus de celle-ci comme n’importe quelle autre requête.</span><span class="sxs-lookup"><span data-stu-id="85ced-158">Furthermore, once you apply aggregate operators on a grouping query to remove groupings from the resulting source, you can compose on top of it like any other query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

<span data-ttu-id="85ced-159">Les opérateurs d’agrégation EF Core prend en charge sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="85ced-159">The aggregate operators EF Core supports are as follows</span></span>

- <span data-ttu-id="85ced-160">Moyenne</span><span class="sxs-lookup"><span data-stu-id="85ced-160">Average</span></span>
- <span data-ttu-id="85ced-161">Count</span><span class="sxs-lookup"><span data-stu-id="85ced-161">Count</span></span>
- <span data-ttu-id="85ced-162">LongCount</span><span class="sxs-lookup"><span data-stu-id="85ced-162">LongCount</span></span>
- <span data-ttu-id="85ced-163">Max</span><span class="sxs-lookup"><span data-stu-id="85ced-163">Max</span></span>
- <span data-ttu-id="85ced-164">Min</span><span class="sxs-lookup"><span data-stu-id="85ced-164">Min</span></span>
- <span data-ttu-id="85ced-165">SUM</span><span class="sxs-lookup"><span data-stu-id="85ced-165">Sum</span></span>

## <a name="left-join"></a><span data-ttu-id="85ced-166">Jointure gauche</span><span class="sxs-lookup"><span data-stu-id="85ced-166">Left Join</span></span>

<span data-ttu-id="85ced-167">Alors que la jointure de gauche n’est pas un opérateur LINQ, les bases de données relationnelles ont le concept d’une jointure de gauche qui est fréquemment utilisée dans les requêtes.</span><span class="sxs-lookup"><span data-stu-id="85ced-167">While Left Join isn't a LINQ operator, relational databases have the concept of a Left Join which is frequently used in queries.</span></span> <span data-ttu-id="85ced-168">Un modèle particulier dans les requêtes LINQ donne le même résultat qu’un `LEFT JOIN` sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="85ced-168">A particular pattern in LINQ queries gives the same result as a `LEFT JOIN` on the server.</span></span> <span data-ttu-id="85ced-169">EF Core identifie de tels modèles et génère l’équivalent `LEFT JOIN` côté serveur.</span><span class="sxs-lookup"><span data-stu-id="85ced-169">EF Core identifies such patterns and generates the equivalent `LEFT JOIN` on the server side.</span></span> <span data-ttu-id="85ced-170">Le modèle implique la création d’une GroupJoin entre les sources de données, puis l’aplatissement du regroupement à l’aide de l’opérateur SelectMany avec DefaultIfEmpty sur la source de regroupement pour correspondre à NULL lorsque le interne n’a pas d’élément associé.</span><span class="sxs-lookup"><span data-stu-id="85ced-170">The pattern involves creating a GroupJoin between both the data sources and then flattening out the grouping by using the SelectMany operator with DefaultIfEmpty on the grouping source to match null when the inner doesn't have a related element.</span></span> <span data-ttu-id="85ced-171">L’exemple suivant montre à quoi ressemble ce modèle et ce qu’il génère.</span><span class="sxs-lookup"><span data-stu-id="85ced-171">The following example shows what that pattern looks like and what it generates.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

<span data-ttu-id="85ced-172">Le modèle ci-dessus crée une structure complexe dans l’arborescence de l’expression.</span><span class="sxs-lookup"><span data-stu-id="85ced-172">The above pattern creates a complex structure in the expression tree.</span></span> <span data-ttu-id="85ced-173">Pour cette raison, EF Core vous oblige à aplatir les résultats de regroupement de l’opérateur GroupJoin dans une étape qui suit immédiatement l’opérateur.</span><span class="sxs-lookup"><span data-stu-id="85ced-173">Because of that, EF Core requires you to flatten out the grouping results of the GroupJoin operator in a step immediately following the operator.</span></span> <span data-ttu-id="85ced-174">Même si GroupJoin-DefaultIfEmpty-SelectMany est utilisé, mais dans un modèle différent, il est possible qu’il ne soit pas identifié comme une jointure de gauche.</span><span class="sxs-lookup"><span data-stu-id="85ced-174">Even if the GroupJoin-DefaultIfEmpty-SelectMany is used but in a different pattern, we may not identify it as a Left Join.</span></span>
