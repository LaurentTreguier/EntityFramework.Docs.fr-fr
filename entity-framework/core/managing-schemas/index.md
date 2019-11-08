---
title: Gestion des schémas de base de données - EF Core
author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: 2da17865cb0192fb3e6e3396e4ca5f31fde9c52a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655644"
---
# <a name="managing-database-schemas"></a>Gestion des schémas de base de données

EF Core propose deux méthodes pour que votre modèle EF Core et le schéma de base de données restent synchronisés. Pour choisir entre les deux, décidez si votre modèle EF Core ou le schéma de base de données est la source de vérité.

Si vous souhaitez que votre modèle EF Core soit la source de vérité, utilisez [Migrations][1]. Quand vous apportez des modifications à votre modèle EF Core, cette approche applique progressivement les modifications de schéma correspondantes à votre base de données afin qu’elle reste compatible avec votre modèle EF Core.

Utilisez [l’ingénierie à rebours][2] si vous souhaitez que votre schéma de base de données soit la source de vérité. Cette approche vous permet de structurer un DbContext et les classes de type d’entité en reconstituant la logique de votre schéma de base de données dans un modèle EF Core.

> [!NOTE]
> Les [API de création et de suppression][3] peuvent également créer le schéma de base de données à partir de votre modèle EF Core. Toutefois, elles servent principalement pour des tâches de test, de prototypage et d’autres scénarios où la suppression de la base de données est acceptable.


  [1]: migrations/index.md
  [2]: scaffolding.md
  [3]: ensure-created.md
