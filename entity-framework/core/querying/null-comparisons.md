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
# <a name="query-null-semantics"></a>Sémantique de requête null

## <a name="introduction"></a>Introduction

Les bases de données SQL opèrent sur une logique à 3 valeurs ( `true` , `false` , `null` ) lors d’opérations de comparaison, par opposition à la logique booléenne de C#. Lors de la traduction de requêtes LINQ en SQL, EF Core tente de compenser la différence en introduisant des vérifications de valeur null supplémentaires pour certains éléments de la requête.
Pour illustrer cela, nous allons définir l’entité suivante :

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

et émettez plusieurs requêtes :

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

Les deux premières requêtes produisent des comparaisons simples. Dans la première requête, les deux colonnes n’acceptent pas les valeurs NULL, de sorte que les contrôles NULL ne sont pas nécessaires. Dans la deuxième requête, `NullableInt` peut contenir `null` , mais `Id` n’accepte pas les valeurs `null` null ; la comparaison avec une valeur non null est `null` un résultat, qui serait filtré par `WHERE` opération. Par conséquent, aucun terme supplémentaire n’est nécessaire.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

La troisième requête introduit une vérification de valeur null. Lorsque `NullableInt` est `null` , la comparaison `Id <> NullableInt` produit `null` , qui serait filtrée par `WHERE` opération. Toutefois, dans la perspective logique booléenne, ce cas doit être retourné dans le cadre du résultat. Par conséquent, EF Core ajoute la vérification nécessaire pour garantir cela.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

Les requêtes quatre et cinq affichent le modèle lorsque les deux colonnes acceptent les valeurs NULL. Il est à noter que l' `<>` opération produit une requête plus compliquée (et potentiellement plus lente) que l' `==` opération.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a>Traitement des valeurs Nullable dans les fonctions

De nombreuses fonctions de SQL peuvent retourner un `null` résultat uniquement si certains de leurs arguments sont `null` . EF Core tire parti de cette solution pour produire des requêtes plus efficaces.
La requête ci-dessous illustre l’optimisation :

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

Le SQL généré est le suivant (il n’est pas nécessaire d’évaluer la `SUBSTRING` fonction, car elle sera uniquement NULL lorsque l’un des arguments est null.) :

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

L’optimisation peut également être utilisée pour les fonctions définies par l’utilisateur. Pour plus d’informations, consultez la page [mappage des fonctions définies](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) par l’utilisateur.

## <a name="writing-performant-queries"></a>Écriture de requêtes performantes

- La comparaison de colonnes n’acceptant pas les valeurs NULL est plus simple et plus rapide que la comparaison des colonnes Nullable. Envisagez de marquer les colonnes comme n’acceptant pas les valeurs NULL chaque fois que possible.

- La vérification de l’égalité ( `==` ) est plus simple et plus rapide que la vérification de l’absence d’égalité ( `!=` ), car la requête n’a pas besoin de faire la distinction entre `null` et le `false` résultat. Utilisez la comparaison d’égalité chaque fois que possible. Toutefois, le fait de nier simplement `==` la comparaison est identique à `!=` , donc cela n’entraîne pas d’amélioration des performances.

- Dans certains cas, il est possible de simplifier une comparaison complexe en filtrant les `null` valeurs d’une colonne de manière explicite, par exemple, si aucune `null` valeur n’est présente ou si ces valeurs ne sont pas pertinentes dans le résultat. Prenons l’exemple suivant :

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

Ces requêtes produisent le code SQL suivant :

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

Dans la deuxième requête, les `null` résultats sont filtrés à partir de la `String1` colonne de manière explicite. EF Core pouvez traiter en toute sécurité la `String1` colonne comme n’acceptant pas les valeurs null pendant la comparaison, ce qui se traduit par une requête plus simple.

## <a name="using-relational-null-semantics"></a>Utilisation de la sémantique de valeurs NULL relationnelles

Il est possible de désactiver la compensation de comparaison null et d’utiliser directement la sémantique de valeurs NULL relationnelles. Pour ce faire, appelez `UseRelationalNulls(true)` la méthode sur le générateur d’options à l’intérieur de la `OnConfiguring` méthode :

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> Lorsque vous utilisez la sémantique de valeurs NULL relationnelles, vos requêtes LINQ n’ont plus la même signification que dans C# et peuvent produire des résultats différents de ceux attendus. Soyez prudent lorsque vous utilisez ce mode.
