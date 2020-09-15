---
title: Gestion des schémas de base de données - EF Core
description: Vue d’ensemble des stratégies de gestion des schémas de la base de données avec Entity Framework Core.
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/index
ms.openlocfilehash: e4f8c82125534e9e7e0c2de552bce336a544a2aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619500"
---
# <a name="managing-database-schemas"></a>Gestion des schémas de base de données

EF Core propose deux méthodes pour que votre modèle EF Core et le schéma de base de données restent synchronisés. Pour choisir entre les deux, décidez si votre modèle EF Core ou le schéma de base de données est la source de vérité.

Si vous souhaitez que votre modèle EF Core soit la source de vérité, utilisez [Migrations][1]. Quand vous apportez des modifications à votre modèle EF Core, cette approche applique progressivement les modifications de schéma correspondantes à votre base de données afin qu’elle reste compatible avec votre modèle EF Core.

Utilisez [l’ingénierie à rebours][2] si vous souhaitez que votre schéma de base de données soit la source de vérité. Cette approche vous permet de structurer un DbContext et les classes de type d’entité en reconstituant la logique de votre schéma de base de données dans un modèle EF Core.

> [!NOTE]
> Les [API de création et de suppression][3] peuvent également créer le schéma de base de données à partir de votre modèle EF Core. Toutefois, elles servent principalement pour des tâches de test, de prototypage et d’autres scénarios où la suppression de la base de données est acceptable.

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
