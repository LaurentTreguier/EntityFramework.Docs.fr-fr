---
title: Mappage des fonctions définies par l’utilisateur-EF Core
description: Mappage de fonctions définies par l’utilisateur à des fonctions de base de données
author: maumar
ms.date: 11/23/2020
uid: core/user-defined-function-mapping
ms.openlocfilehash: ba60abdc9c81b34b8f4ed8f501cf2f7e52ba9d7d
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657818"
---
# <a name="user-defined-function-mapping"></a>Mappage des fonctions définies par l’utilisateur

EF Core permet d’utiliser des fonctions SQL définies par l’utilisateur dans des requêtes. Pour ce faire, les fonctions doivent être mappées à une méthode CLR lors de la configuration du modèle. Lors de la traduction de la requête LINQ en SQL, la fonction définie par l’utilisateur est appelée à la place de la fonction CLR à laquelle elle a été mappée.

## <a name="mapping-a-method-to-a-sql-function"></a>Mappage d’une méthode à une fonction SQL

Pour illustrer le fonctionnement du mappage des fonctions définies par l’utilisateur, nous allons définir les entités suivantes :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

Et la configuration de modèle suivante :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

Le blog peut comporter de nombreuses publications et chaque billet peut comporter de nombreux commentaires.

Ensuite, créez la fonction définie par l’utilisateur `CommentedPostCountForBlog` , qui retourne le nombre de publications avec au moins un commentaire pour un blog donné, en fonction du blog `Id` :

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

Pour utiliser cette fonction dans EF Core, nous définissons la méthode CLR suivante, que nous allons mapper à la fonction définie par l’utilisateur :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

Le corps de la méthode CLR n’est pas important. La méthode n’est pas appelée côté client, sauf si EF Core ne peut pas traduire ses arguments. Si les arguments peuvent être traduits, EF Core uniquement la signature de la méthode.

> [!NOTE]
> Dans l’exemple, la méthode est définie sur `DbContext` , mais elle peut également être définie en tant que méthode statique dans d’autres classes.

Cette définition de fonction peut désormais être associée à une fonction définie par l’utilisateur dans la configuration du modèle :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

Par défaut, EF Core tente de mapper la fonction CLR à une fonction définie par l’utilisateur portant le même nom. Si les noms diffèrent, nous pouvons utiliser `HasName` pour fournir le nom correct de la fonction définie par l’utilisateur à mapper.

À présent, exécutez la requête suivante :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

Produira ce SQL :

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a>Mappage d’une méthode à un SQL personnalisé

EF Core autorise également les fonctions définies par l’utilisateur qui sont converties en un SQL spécifique. L’expression SQL est fournie à l’aide de la `HasTranslation` méthode pendant la configuration de la fonction définie par l’utilisateur.

Dans l’exemple ci-dessous, nous allons créer une fonction qui calcule le pourcentage de différence entre deux entiers.

La méthode CLR est la suivante :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

La définition de la fonction est la suivante :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

Une fois la fonction définie, elle peut être utilisée dans la requête. Au lieu d’appeler la fonction de base de données, EF Core traduira le corps de la méthode directement en SQL en fonction de l’arborescence de l’expression SQL construite à partir de HasTranslation. La requête LINQ suivante :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

Génère l’instruction SQL suivante :

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a>Mappage d’une fonction interrogeable à une fonction table

EF Core prend également en charge le mappage à une fonction table à l’aide d’une méthode CLR définie par l’utilisateur retournant un `IQueryable` de types d’entité, ce qui permet à EF Core de mapper les TVF avec les paramètres. Le processus est similaire au mappage d’une fonction scalaire définie par l’utilisateur à une fonction SQL : nous avons besoin d’une TVF dans la base de données, d’une fonction CLR utilisée dans les requêtes LINQ et d’un mappage entre les deux.

À titre d’exemple, nous allons utiliser une fonction table qui retourne toutes les publications contenant au moins un commentaire qui correspond à un seuil « like » donné :

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

La signature de la méthode CLR est la suivante :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> L' `FromExpression` appel dans le corps de la fonction CLR permet d’utiliser la fonction à la place d’un DbSet normal.

Le mappage est le suivant :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> Jusqu’à ce que le [problème 23408](https://github.com/dotnet/efcore/issues/23408) soit résolu, le mappage à un `IQueryable` de types d’entité remplace le mappage par défaut par une table pour le DbSet. Si nécessaire (par exemple, lorsque l’entité n’est pas de clé sans clé), le mappage à la table doit être spécifié explicitement à l’aide de la `ToTable` méthode.

> [!NOTE]
> Une fonction interrogeable doit être mappée à une fonction table et ne peut pas utiliser `HasTranslation` .

Lorsque la fonction est mappée, la requête suivante :

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

Tiges

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
