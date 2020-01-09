---
title: Séquences-EF Core
author: roji
ms.date: 12/18/2019
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/sequences
ms.openlocfilehash: 6343e58dd79837cc69308a070c9814030505d7dd
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502480"
---
# <a name="sequences"></a>Séquences

> [!NOTE]  
> Les séquences sont une fonctionnalité généralement prise en charge uniquement par les bases de données relationnelles. Si vous utilisez une base de données non relationnelle telle que Cosmos, consultez la documentation de votre base de données sur la génération de valeurs uniques.

Une séquence génère des valeurs numériques séquentielles uniques dans la base de données. Les séquences ne sont pas associées à une table spécifique, et plusieurs tables peuvent être configurées pour dessiner des valeurs à partir de la même séquence.

## <a name="basic-usage"></a>Utilisation de base

Vous pouvez configurer une séquence dans le modèle, puis l’utiliser pour générer des valeurs pour les propriétés :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

Notez que le SQL spécifique utilisé pour générer une valeur à partir d’une séquence est spécifique à la base de données ; l’exemple ci-dessus fonctionne sur SQL Server mais échoue sur les autres bases de données. Pour plus d’informations, consultez la documentation de votre base de données spécifique.

## <a name="configuring-sequence-settings"></a>Configuration des paramètres de séquence

Vous pouvez également configurer d’autres aspects de la séquence, tels que son schéma, sa valeur de départ, son incrément, etc. :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
