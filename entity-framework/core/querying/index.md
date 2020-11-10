---
title: Interrogation des données - EF Core
description: Vue d’ensemble des informations sur les requêtes dans Entity Framework Core.
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: da5177dda4f2df6537ee9133edf4f1240a4b5e94
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430116"
---
# <a name="querying-data"></a>Interrogation des données

Entity Framework Core utilise la requête Language-Integrated (LINQ) pour interroger les données de la base de données. LINQ vous permet d’utiliser C# (ou le langage .NET de votre choix) pour écrire des requêtes fortement typées. Il utilise vos classes de contexte et d’entité dérivées pour référencer les objet de base de données. EF Core passe une représentation de la requête LINQ au fournisseur de bases de données. Les fournisseurs de bases de données la traduisent à leur tour en langage de requête spécifique aux bases de données (par exemple, SQL pour une base de données relationnelle). Les requêtes sont toujours exécutées sur la base de données même si les entités retournées dans le résultat existent déjà dans le contexte.

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) sur GitHub.

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
