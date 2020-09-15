---
title: Interrogation des données - EF Core
description: Vue d’ensemble des informations sur les requêtes dans Entity Framework Core.
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 028c640a17c4946158c86bdf1a663a4050f55921
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616322"
---
# <a name="querying-data"></a>Interrogation des données

Entity Framework Core utilise LINQ (Language Integrated Query) pour interroger les données de la base de données. LINQ vous permet d’utiliser C# (ou le langage .NET de votre choix) pour écrire des requêtes fortement typées. Il utilise vos classes de contexte et d’entité dérivées pour référencer les objet de base de données. EF Core passe une représentation de la requête LINQ au fournisseur de bases de données. Les fournisseurs de bases de données la traduisent à leur tour en langage de requête spécifique aux bases de données (par exemple, SQL pour une base de données relationnelle).

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) sur GitHub.

Les extraits de code montrent quelques exemples illustrant comment accomplir des tâches courantes avec Entity Framework Core.

## <a name="loading-all-data"></a>Chargement de toutes les données

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a>Chargement d’une seule entité

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a>Filtrage

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a>Pour aller plus loin

- Découvrir les [expressions de requête LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
- Pour plus d’informations sur le traitement des requêtes dans EF Core, consultez [Fonctionnement des requêtes](xref:core/querying/how-query-works).
