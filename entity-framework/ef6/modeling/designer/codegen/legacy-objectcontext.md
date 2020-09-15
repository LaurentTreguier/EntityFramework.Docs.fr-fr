---
title: Retour à ObjectContext dans Entity Framework Designer-EF6
description: Retour à ObjectContext dans Entity Framework Designer dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/codegen/legacy-objectcontext
ms.openlocfilehash: 9ceb8ef43e4df083fe3cc5e63862ba2eb338f659
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069962"
---
# <a name="reverting-to-objectcontext-in-entity-framework-designer"></a>Retour à ObjectContext dans Entity Framework Designer
Avec la version précédente de Entity Framework un modèle créé avec le concepteur EF générait un contexte dérivé d’ObjectContext et des classes d’entité dérivées de EntityObject.

À compter d’EF 4.1, nous vous recommandons de passer à un modèle de génération de code qui génère un contexte dérivant des classes d’entité DbContext et POCO.

Dans Visual Studio 2012, vous recevez le code DbContext généré par défaut pour tous les nouveaux modèles créés à l’aide du concepteur EF. Les modèles existants continuent à générer du code basé sur ObjectContext, sauf si vous décidez de basculer vers le générateur de code DbContext.

## <a name="reverting-back-to-objectcontext-code-generation"></a>Retour à la génération de code ObjectContext

### <a name="1-disable-dbcontext-code-generation"></a>1. désactiver la génération de code DbContext

La génération des classes DbContext et POCO dérivées est gérée par deux fichiers. TT dans votre projet. Si vous développez le fichier. edmx dans l’Explorateur de solutions, vous verrez ces fichiers. Supprimez ces deux fichiers de votre projet.

![Fichiers de génération de code](~/ef6/media/codegenfiles.png)

Si vous utilisez VB.NET, vous devez sélectionner le bouton **Afficher tous les fichiers** pour afficher les fichiers imbriqués.

![Afficher tous les fichiers](~/ef6/media/showallfiles.png)

### <a name="2-re-enable-objectcontext-code-generation"></a>2. réactiver la génération de code ObjectContext

Ouvrez votre modèle dans le concepteur EF, cliquez avec le bouton droit sur une section vide de l’aire de conception, puis sélectionnez **Propriétés**.

Dans la Fenêtre Propriétés modifiez la **stratégie de génération de code** de **None** en **default**.

![Stratégie de génération de code](~/ef6/media/codegenstrategy.png)
