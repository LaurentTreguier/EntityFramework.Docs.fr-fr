---
title: Interrogation des données - EF Core
description: Vue d’ensemble des informations sur les requêtes dans Entity Framework Core.
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: 733b44e2f571bd32689b9d8e5d7507bd90e7848d
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023729"
---
# <a name="querying-data"></a>Interrogation des données

Entity Framework Core utilise la requête Language-Integrated (LINQ) pour interroger les données de la base de données. LINQ vous permet d’utiliser C# (ou le langage .NET de votre choix) pour écrire des requêtes fortement typées. Il utilise vos classes de contexte et d’entité dérivées pour référencer les objet de base de données. EF Core passe une représentation de la requête LINQ au fournisseur de bases de données. Les fournisseurs de bases de données la traduisent à leur tour en langage de requête spécifique aux bases de données (par exemple, SQL pour une base de données relationnelle). Les requêtes sont toujours exécutées sur la base de données même si les entités retournées dans le résultat existent déjà dans le contexte.

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Querying/Overview) sur GitHub.

Les extraits de code montrent quelques exemples illustrant comment accomplir des tâches courantes avec Entity Framework Core.

## <a name="loading-all-data"></a>Chargement de toutes les données

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a>Chargement d’une seule entité

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a>Filtrage

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a>Pour aller plus loin

- Découvrir les [expressions de requête LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
- Pour plus d’informations sur le traitement des requêtes dans EF Core, consultez [Fonctionnement des requêtes](xref:core/querying/how-query-works).
