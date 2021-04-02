---
title: Mappage des fonctions définies par l’utilisateur-EF Core
description: Mappage de fonctions définies par l’utilisateur à des fonctions de base de données
author: maumar
ms.date: 11/23/2020
uid: core/querying/user-defined-function-mapping
ms.openlocfilehash: 3b581a3e70112a3b538b55e47ed63da6571a179b
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106165960"
---
# <a name="user-defined-function-mapping"></a><span data-ttu-id="31a0f-103">Mappage de fonctions définies par l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="31a0f-103">User-defined function mapping</span></span>

<span data-ttu-id="31a0f-104">EF Core permet d’utiliser des fonctions SQL définies par l’utilisateur dans des requêtes.</span><span class="sxs-lookup"><span data-stu-id="31a0f-104">EF Core allows for using user-defined SQL functions in queries.</span></span> <span data-ttu-id="31a0f-105">Pour ce faire, les fonctions doivent être mappées à une méthode CLR lors de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="31a0f-105">To do that, the functions need to be mapped to a CLR method during model configuration.</span></span> <span data-ttu-id="31a0f-106">Lors de la traduction de la requête LINQ en SQL, la fonction définie par l’utilisateur est appelée à la place de la fonction CLR à laquelle elle a été mappée.</span><span class="sxs-lookup"><span data-stu-id="31a0f-106">When translating the LINQ query to SQL, the user-defined function is called instead of the CLR function it has been mapped to.</span></span>

## <a name="mapping-a-method-to-a-sql-function"></a><span data-ttu-id="31a0f-107">Mappage d’une méthode à une fonction SQL</span><span class="sxs-lookup"><span data-stu-id="31a0f-107">Mapping a method to a SQL function</span></span>

<span data-ttu-id="31a0f-108">Pour illustrer le fonctionnement du mappage des fonctions définies par l’utilisateur, nous allons définir les entités suivantes :</span><span class="sxs-lookup"><span data-stu-id="31a0f-108">To illustrate how user-defined function mapping works, let's define the following entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

<span data-ttu-id="31a0f-109">Et la configuration de modèle suivante :</span><span class="sxs-lookup"><span data-stu-id="31a0f-109">And the following model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

<span data-ttu-id="31a0f-110">Le blog peut comporter de nombreuses publications et chaque billet peut comporter de nombreux commentaires.</span><span class="sxs-lookup"><span data-stu-id="31a0f-110">Blog can have many posts and each post can have many comments.</span></span>

<span data-ttu-id="31a0f-111">Ensuite, créez la fonction définie par l’utilisateur `CommentedPostCountForBlog` , qui retourne le nombre de publications avec au moins un commentaire pour un blog donné, en fonction du blog `Id` :</span><span class="sxs-lookup"><span data-stu-id="31a0f-111">Next, create the user-defined function `CommentedPostCountForBlog`, which returns the count of posts with at least one comment for a given blog, based on the blog `Id`:</span></span>

```sql
CREATE FUNCTION dbo.CommentedPostCountForBlog(@id int)
RETURNS int
AS
BEGIN
    RETURN (SELECT COUNT(*)
        FROM [Posts] AS [p]
        WHERE ([p].[BlogId] = @id) AND ((
            SELECT COUNT(*)
            FROM [Comments] AS [c]
            WHERE [p].[PostId] = [c].[PostId]) > 0));
END
```

<span data-ttu-id="31a0f-112">Pour utiliser cette fonction dans EF Core, nous définissons la méthode CLR suivante, que nous allons mapper à la fonction définie par l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="31a0f-112">To use this function in EF Core, we define the following CLR method, which we map to the user-defined function:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

<span data-ttu-id="31a0f-113">Le corps de la méthode CLR n’est pas important.</span><span class="sxs-lookup"><span data-stu-id="31a0f-113">The body of the CLR method is not important.</span></span> <span data-ttu-id="31a0f-114">La méthode n’est pas appelée côté client, sauf si EF Core ne peut pas traduire ses arguments.</span><span class="sxs-lookup"><span data-stu-id="31a0f-114">The method will not be invoked client-side, unless EF Core can't translate its arguments.</span></span> <span data-ttu-id="31a0f-115">Si les arguments peuvent être traduits, EF Core uniquement la signature de la méthode.</span><span class="sxs-lookup"><span data-stu-id="31a0f-115">If the arguments can be translated, EF Core only cares about the method signature.</span></span>

> [!NOTE]
> <span data-ttu-id="31a0f-116">Dans l’exemple, la méthode est définie sur `DbContext` , mais elle peut également être définie en tant que méthode statique dans d’autres classes.</span><span class="sxs-lookup"><span data-stu-id="31a0f-116">In the example, the method is defined on `DbContext`, but it can also be defined as a static method inside other classes.</span></span>

<span data-ttu-id="31a0f-117">Cette définition de fonction peut désormais être associée à une fonction définie par l’utilisateur dans la configuration du modèle :</span><span class="sxs-lookup"><span data-stu-id="31a0f-117">This function definition can now be associated with user-defined function in the model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

<span data-ttu-id="31a0f-118">Par défaut, EF Core tente de mapper la fonction CLR à une fonction définie par l’utilisateur portant le même nom.</span><span class="sxs-lookup"><span data-stu-id="31a0f-118">By default, EF Core tries to map CLR function to a user-defined function with the same name.</span></span> <span data-ttu-id="31a0f-119">Si les noms diffèrent, nous pouvons utiliser `HasName` pour fournir le nom correct de la fonction définie par l’utilisateur à mapper.</span><span class="sxs-lookup"><span data-stu-id="31a0f-119">If the names differ, we can use `HasName` to provide the correct name for the user-defined function we want to map to.</span></span>

<span data-ttu-id="31a0f-120">À présent, exécutez la requête suivante :</span><span class="sxs-lookup"><span data-stu-id="31a0f-120">Now, executing the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

<span data-ttu-id="31a0f-121">Produira ce SQL :</span><span class="sxs-lookup"><span data-stu-id="31a0f-121">Will produce this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a><span data-ttu-id="31a0f-122">Mappage d’une méthode à un SQL personnalisé</span><span class="sxs-lookup"><span data-stu-id="31a0f-122">Mapping a method to a custom SQL</span></span>

<span data-ttu-id="31a0f-123">EF Core autorise également les fonctions définies par l’utilisateur qui sont converties en un SQL spécifique.</span><span class="sxs-lookup"><span data-stu-id="31a0f-123">EF Core also allows for user-defined functions that get converted to a specific SQL.</span></span> <span data-ttu-id="31a0f-124">L’expression SQL est fournie à l’aide de la `HasTranslation` méthode pendant la configuration de la fonction définie par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="31a0f-124">The SQL expression is provided using `HasTranslation` method during user-defined function configuration.</span></span>

<span data-ttu-id="31a0f-125">Dans l’exemple ci-dessous, nous allons créer une fonction qui calcule le pourcentage de différence entre deux entiers.</span><span class="sxs-lookup"><span data-stu-id="31a0f-125">In the example below, we'll create a function that computes percentage difference between two integers.</span></span>

<span data-ttu-id="31a0f-126">La méthode CLR est la suivante :</span><span class="sxs-lookup"><span data-stu-id="31a0f-126">The CLR method is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

<span data-ttu-id="31a0f-127">La définition de la fonction est la suivante :</span><span class="sxs-lookup"><span data-stu-id="31a0f-127">The function definition is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

<span data-ttu-id="31a0f-128">Une fois la fonction définie, elle peut être utilisée dans la requête.</span><span class="sxs-lookup"><span data-stu-id="31a0f-128">Once we define the function, it can be used in the query.</span></span> <span data-ttu-id="31a0f-129">Au lieu d’appeler la fonction de base de données, EF Core traduira le corps de la méthode directement en SQL en fonction de l’arborescence de l’expression SQL construite à partir de HasTranslation.</span><span class="sxs-lookup"><span data-stu-id="31a0f-129">Instead of calling database function, EF Core will translate the method body directly into SQL based on the SQL expression tree constructed from the HasTranslation.</span></span> <span data-ttu-id="31a0f-130">La requête LINQ suivante :</span><span class="sxs-lookup"><span data-stu-id="31a0f-130">The following LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

<span data-ttu-id="31a0f-131">Génère l’instruction SQL suivante :</span><span class="sxs-lookup"><span data-stu-id="31a0f-131">Produces the following SQL:</span></span>

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="configuring-nullability-of-user-defined-function-based-on-its-arguments"></a><span data-ttu-id="31a0f-132">Configuration de la possibilité de valeur null d’une fonction définie par l’utilisateur en fonction de ses arguments</span><span class="sxs-lookup"><span data-stu-id="31a0f-132">Configuring nullability of user-defined function based on its arguments</span></span>

<span data-ttu-id="31a0f-133">Si la fonction définie par l’utilisateur ne peut retourner que `null` lorsqu’un ou plusieurs de ses arguments sont `null` , EFCore offre la possibilité de spécifier cela, ce qui génère un SQL plus performant.</span><span class="sxs-lookup"><span data-stu-id="31a0f-133">If the user-defined function can only return `null` when one or more of its arguments are `null`, EFCore provides way to specify that, resulting in more performant SQL.</span></span> <span data-ttu-id="31a0f-134">Pour ce faire, vous pouvez ajouter un `PropagatesNullability()` appel à la configuration de modèle de paramètres de fonction appropriée.</span><span class="sxs-lookup"><span data-stu-id="31a0f-134">It can be done by adding a `PropagatesNullability()` call to the relevant function parameters model configuration.</span></span>

<span data-ttu-id="31a0f-135">Pour illustrer cela, définissez la fonction utilisateur `ConcatStrings` :</span><span class="sxs-lookup"><span data-stu-id="31a0f-135">To illustrate this, define user function `ConcatStrings`:</span></span>

```sql
CREATE FUNCTION [dbo].[ConcatStrings] (@prm1 nvarchar(max), @prm2 nvarchar(max))
RETURNS nvarchar(max)
AS
BEGIN
    RETURN @prm1 + @prm2;
END
```

<span data-ttu-id="31a0f-136">et deux méthodes CLR qui sont mappées à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="31a0f-136">and two CLR methods that map to it:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationFunctionDefinition)]

<span data-ttu-id="31a0f-137">La configuration du modèle (à l’intérieur de la `OnModelCreating` méthode) est la suivante :</span><span class="sxs-lookup"><span data-stu-id="31a0f-137">The model configuration (inside `OnModelCreating` method) is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationModelConfiguration)]

<span data-ttu-id="31a0f-138">La première fonction est configurée de manière standard.</span><span class="sxs-lookup"><span data-stu-id="31a0f-138">The first function is configured in the standard way.</span></span> <span data-ttu-id="31a0f-139">La deuxième fonction est configurée pour tirer parti de l’optimisation de la propagation de la possibilité de valeur null, en fournissant plus d’informations sur le comportement de la fonction autour des paramètres null.</span><span class="sxs-lookup"><span data-stu-id="31a0f-139">The second function is configured to take advantage of the nullability propagation optimization, providing more information on how the function behaves around null parameters.</span></span>

<span data-ttu-id="31a0f-140">Lors de l’émission des requêtes suivantes :</span><span class="sxs-lookup"><span data-stu-id="31a0f-140">When issuing the following queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#NullabilityPropagationExamples)]

<span data-ttu-id="31a0f-141">Nous obtenons ce SQL :</span><span class="sxs-lookup"><span data-stu-id="31a0f-141">We get this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR [dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) IS NULL

SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR ([b].[Url] IS NULL OR [b].[Rating] IS NULL)
```

<span data-ttu-id="31a0f-142">La deuxième requête n’a pas besoin de réévaluer la fonction elle-même pour tester sa possibilité de valeur null.</span><span class="sxs-lookup"><span data-stu-id="31a0f-142">The second query doesn't need to re-evaluate the function itself to test its nullability.</span></span>

> [!NOTE]
> <span data-ttu-id="31a0f-143">Cette optimisation ne doit être utilisée que si la fonction ne peut être retournée que `null` lorsqu’il s’agit de paramètres `null` .</span><span class="sxs-lookup"><span data-stu-id="31a0f-143">This optimization should only be used if the function can only return `null` when it's parameters are `null`.</span></span>

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a><span data-ttu-id="31a0f-144">Mappage d’une fonction interrogeable à une fonction table</span><span class="sxs-lookup"><span data-stu-id="31a0f-144">Mapping a queryable function to a table-valued function</span></span>

<span data-ttu-id="31a0f-145">EF Core prend également en charge le mappage à une fonction table à l’aide d’une méthode CLR définie par l’utilisateur retournant un `IQueryable` de types d’entité, ce qui permet à EF Core de mapper les TVF avec les paramètres.</span><span class="sxs-lookup"><span data-stu-id="31a0f-145">EF Core also supports mapping to a table-valued function using a user-defined CLR method returning an `IQueryable` of entity types, allowing EF Core to map TVFs with parameters.</span></span> <span data-ttu-id="31a0f-146">Le processus est similaire au mappage d’une fonction scalaire définie par l’utilisateur à une fonction SQL : nous avons besoin d’une TVF dans la base de données, d’une fonction CLR utilisée dans les requêtes LINQ et d’un mappage entre les deux.</span><span class="sxs-lookup"><span data-stu-id="31a0f-146">The process is similar to mapping a scalar user-defined function to a SQL function: we need a TVF in the database, a CLR function that is used in the LINQ queries, and a mapping between the two.</span></span>

<span data-ttu-id="31a0f-147">À titre d’exemple, nous allons utiliser une fonction table qui retourne toutes les publications contenant au moins un commentaire qui correspond à un seuil « like » donné :</span><span class="sxs-lookup"><span data-stu-id="31a0f-147">As an example, we'll use a table-valued function that returns all posts having at least one comment that meets a given "Like" threshold:</span></span>

```sql
CREATE FUNCTION dbo.PostsWithPopularComments(@likeThreshold int)
RETURNS TABLE
AS
RETURN
(
    SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
    FROM [Posts] AS [p]
    WHERE (
        SELECT COUNT(*)
        FROM [Comments] AS [c]
        WHERE ([p].[PostId] = [c].[PostId]) AND ([c].[Likes] >= @likeThreshold)) > 0
)
```

<span data-ttu-id="31a0f-148">La signature de la méthode CLR est la suivante :</span><span class="sxs-lookup"><span data-stu-id="31a0f-148">The CLR method signature is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> <span data-ttu-id="31a0f-149">L' `FromExpression` appel dans le corps de la fonction CLR permet d’utiliser la fonction à la place d’un DbSet normal.</span><span class="sxs-lookup"><span data-stu-id="31a0f-149">The `FromExpression` call in the CLR function body allows for the function to be used instead of a regular DbSet.</span></span>

<span data-ttu-id="31a0f-150">Le mappage est le suivant :</span><span class="sxs-lookup"><span data-stu-id="31a0f-150">And below is the mapping:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> <span data-ttu-id="31a0f-151">Jusqu’à ce que le [problème 23408](https://github.com/dotnet/efcore/issues/23408) soit résolu, le mappage à un `IQueryable` de types d’entité remplace le mappage par défaut par une table pour le DbSet.</span><span class="sxs-lookup"><span data-stu-id="31a0f-151">Until [issue 23408](https://github.com/dotnet/efcore/issues/23408) is fixed, mapping to an `IQueryable` of entity types overrides the default mapping to a table for the DbSet.</span></span> <span data-ttu-id="31a0f-152">Si nécessaire (par exemple, lorsque l’entité n’est pas de clé sans clé), le mappage à la table doit être spécifié explicitement à l’aide de la `ToTable` méthode.</span><span class="sxs-lookup"><span data-stu-id="31a0f-152">If necessary - for example when the entity is not keyless - mapping to the table must be specified explicitly using `ToTable` method.</span></span>

> [!NOTE]
> <span data-ttu-id="31a0f-153">Une fonction interrogeable doit être mappée à une fonction table et ne peut pas utiliser `HasTranslation` .</span><span class="sxs-lookup"><span data-stu-id="31a0f-153">Queryable function must be mapped to a table-valued function and can't use of `HasTranslation`.</span></span>

<span data-ttu-id="31a0f-154">Lorsque la fonction est mappée, la requête suivante :</span><span class="sxs-lookup"><span data-stu-id="31a0f-154">When the function is mapped, the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

<span data-ttu-id="31a0f-155">Tiges</span><span class="sxs-lookup"><span data-stu-id="31a0f-155">Produces:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
