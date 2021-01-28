---
title: Fournisseur de base de données Microsoft SQL Server-génération de valeur-EF Core
description: Modèles de génération de valeur spécifiques au fournisseur de base de données SQL Server Entity Framework Core
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987153"
---
# <a name="sql-server-value-generation"></a>Génération de valeurs de SQL Server

Cette page détaille la configuration de la génération de valeur et les modèles qui sont spécifiques au fournisseur SQL Server. Il est recommandé de lire d’abord [la page général sur la génération de valeurs](xref:core/modeling/generated-properties).

## <a name="identity-columns"></a>Colonnes IDENTITY

Par Convention, les colonnes numériques qui sont configurées pour générer leurs valeurs lors de l’ajout sont configurées en tant que [SQL Server colonnes d’identité](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).

### <a name="seed-and-increment"></a>Valeur initiale et incrément

Par défaut, les colonnes d’identité commencent à 1 (la valeur initiale) et sont incrémentées de 1 chaque fois qu’une ligne est ajoutée (l’incrément). Vous pouvez configurer une valeur initiale et un incrément différents comme suit :

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> La possibilité de configurer la valeur initiale et l’incrément de l’identité a été introduite dans EF Core 3,0.

### <a name="inserting-explicit-values-into-identity-columns"></a>Insertion de valeurs explicites dans des colonnes d’identité

Par défaut, SQL Server n’autorise pas l’insertion de valeurs explicites dans les colonnes d’identité. Pour ce faire, vous devez activer manuellement `IDENTITY_INSERT` avant d’appeler `SaveChanges()` , comme suit :

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> Nous avons une [demande de fonctionnalité](https://github.com/aspnet/EntityFramework/issues/703) dans notre backlog pour faire cela automatiquement dans le fournisseur SQL Server.

## <a name="sequences"></a>Séquences

En guise d’alternative aux colonnes d’identité, vous pouvez utiliser des séquences standard. Cela peut être utile dans différents scénarios. par exemple, vous souhaiterez peut-être que plusieurs colonnes dessinent leurs valeurs par défaut à partir d’une seule séquence.

SQL Server vous permet de créer des séquences et de les utiliser comme détaillé dans [la page général sur les séquences](xref:core/modeling/sequences). C’est à vous de configurer vos propriétés pour utiliser des séquences via `HasDefaultValueSql()` .
