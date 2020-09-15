---
title: Jetons d’accès concurrentiel-EF Core
description: Configuration des jetons d’accès concurrentiel pour le contrôle d’accès concurrentiel optimiste dans un modèle de Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/concurrency
ms.openlocfilehash: fab9ad99609cd8e724b550000f6f06ef8c172c81
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071613"
---
# <a name="concurrency-tokens"></a>Jetons d'accès concurrentiel

> [!NOTE]
> Cette page décrit comment configurer des jetons d’accès concurrentiel. Consultez [gestion des conflits d’accès concurrentiel](xref:core/saving/concurrency) pour obtenir une explication détaillée du fonctionnement du contrôle d’accès concurrentiel sur EF Core et des exemples montrant comment gérer les conflits d’accès concurrentiel dans votre application.

Les propriétés configurées en tant que jetons d’accès concurrentiel permettent d’implémenter le contrôle d’accès concurrentiel optimiste.

## <a name="configuration"></a>Configuration

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs?name=Concurrency&highlight=5)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs?name=Concurrency&highlight=5)]

***

## <a name="timestamprowversion"></a>Horodateur/rowversion

Un timestamp/rowversion est une propriété pour laquelle une nouvelle valeur est générée automatiquement par la base de données chaque fois qu’une ligne est insérée ou mise à jour. La propriété est également traitée comme un jeton d’accès concurrentiel, s’assurant que vous recevez une exception si une ligne que vous mettez à jour a changé depuis que vous l’avez interrogée. Les détails précis dépendent du fournisseur de base de données utilisé ; par SQL Server, une propriété *Byte []* est généralement utilisée, qui sera configurée en tant que colonne *rowversion* dans la base de données.

Vous pouvez configurer une propriété pour qu’elle soit de type timestamp/rowversion comme suit :

### <a name="data-annotations"></a>[Annotations de données](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs?name=Timestamp&highlight=7)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs?name=Timestamp&highlight=9,17)]

***
