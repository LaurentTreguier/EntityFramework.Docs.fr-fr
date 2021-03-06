---
title: Fournisseur de base de données Microsoft SQL Server-index-EF Core
description: Fonctionnalités d’index spécifiques au fournisseur de SQL Server Entity Framework Core
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 42411a562b4741ba39b4eb855bb84c66e100456b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129159"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a>Fonctionnalités d’index spécifiques au fournisseur de SQL Server Entity Framework Core

Cette page décrit les options de configuration d’index qui sont spécifiques au fournisseur de SQL Server.

## <a name="clustering"></a>Clustering

Les index cluster trient et stockent les lignes de données de la table ou la vue en fonction de leurs valeurs de clé. La création de l’index cluster approprié pour votre table peut améliorer considérablement la vitesse de vos requêtes, car les données sont déjà disposées dans l’ordre optimal. Il ne peut y avoir qu’un index cluster par table car les lignes de données peuvent être stockées dans un seul ordre. Pour plus d’informations, consultez [la documentation SQL Server sur les index cluster et non cluster](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).

Par défaut, la colonne de clé primaire d’une table est implicitement sauvegardée par un index cluster, et tous les autres index ne sont pas mis en cluster.

Vous pouvez configurer un index ou une clé à mettre en cluster comme suit :

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

> [!NOTE]
> SQL Server ne prend en charge qu’un seul index cluster par table, et la clé primaire est par défaut en cluster. Si vous souhaitez avoir un index cluster sur une colonne non-clé, vous devez explicitement rendre votre clé non cluster.

## <a name="fill-factor"></a>Facteur de remplissage

> [!NOTE]
> Cette fonctionnalité a été introduite dans EF Core 5,0.

L’option de facteur de remplissage d’index est fournie pour optimiser le stockage et les performances des données d’index. Pour plus d’informations, consultez [la documentation SQL Server sur le facteur de remplissage](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).

Vous pouvez configurer le facteur de remplissage d’un index comme suit :

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a>Création en ligne

L’option ONLINE permet l’accès simultané des utilisateurs à la table sous-jacente ou aux données d’index cluster et aux index non cluster associés lors de la création d’index, afin que les utilisateurs puissent continuer à mettre à jour et interroger les données sous-jacentes. Lorsque vous effectuez des opérations DDL (Data Definition Language) en mode hors connexion, comme la construction ou la reconstruction d'un index cluster, ces opérations posent des verrous exclusifs sur les données sous-jacentes et les index associés. Pour plus d’informations, consultez [la documentation SQL Server sur l’option d’index en ligne](/sql/relational-databases/indexes/perform-index-operations-online).

Vous pouvez configurer un index avec l’option ONLINE comme suit :

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
