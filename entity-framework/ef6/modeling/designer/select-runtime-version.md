---
title: Sélection de Entity Framework version du runtime pour les modèles du concepteur EF-EF6
description: Sélection de Entity Framework version du runtime pour les modèles du concepteur EF dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 2025ca5e46c7d9a38ba596d57c023a2c1b224129
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073264"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a>Sélection de Entity Framework version du runtime pour les modèles du concepteur EF
> [!NOTE]
> **EF6 et versions ultérieures uniquement** : Les fonctionnalités, les API, etc. décrites dans cette page ont été introduites dans Entity Framework 6. Si vous utilisez une version antérieure, certaines ou toutes les informations ne s’appliquent pas.

À partir de EF6, l’écran suivant a été ajouté au concepteur EF pour vous permettre de sélectionner la version du runtime que vous souhaitez cibler lors de la création d’un modèle. L’écran s’affiche lorsque la dernière version de Entity Framework n’est pas déjà installée dans le projet. Si la version la plus récente est déjà installée, elle est utilisée par défaut.

![Sélectionner la version du Runtime](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a>Ciblage de EF6. x

Vous pouvez choisir EF6 dans l’écran « choisir votre version » pour ajouter le runtime EF6 à votre projet. Une fois que vous avez ajouté EF6, vous ne voyez plus cet écran dans le projet actuel.

EF6 sera désactivé si vous avez déjà installé une version antérieure d’EF (puisque vous ne pouvez pas cibler plusieurs versions du runtime à partir du même projet). Si l’option EF6 n’est pas activée ici, procédez comme suit pour mettre à niveau votre projet vers EF6 :

1.  Dans Explorateur de solutions, cliquez avec le bouton droit sur votre projet, puis sélectionnez **gérer les packages NuGet...**
2.  Sélectionner les **mises à jour**
3.  Sélectionnez **EntityFramework** (Assurez-vous qu’il va le mettre à jour vers la version de votre choix)
4.  Cliquez sur **mettre à jour**

 

## <a name="targeting-ef5x"></a>Ciblage de EF5. x

Vous pouvez choisir EF5 dans l’écran « choisir votre version » pour ajouter le runtime EF5 à votre projet. Une fois que vous avez ajouté EF5, vous voyez toujours l’écran avec l’option EF6 désactivée.

Si vous avez une version EF4. x du runtime déjà installée, vous verrez que la version d’EF est indiquée à l’écran plutôt que EF5. Dans ce cas, vous pouvez effectuer la mise à niveau vers EF5 en procédant comme suit :

1.  Sélectionnez **outils- &gt; bibliothèque gestionnaire de package- &gt; console du gestionnaire de package**
2.  Exécuter l' **Install-Package EntityFramework-version 5.0.0**

 

## <a name="targeting-ef4x"></a>Ciblage de EF4. x

Vous pouvez installer le runtime EF4. x dans votre projet en procédant comme suit :

1.  Sélectionnez **outils- &gt; bibliothèque gestionnaire de package- &gt; console du gestionnaire de package**
2.  Exécuter l' **Install-Package EntityFramework-version 4.3.0**
