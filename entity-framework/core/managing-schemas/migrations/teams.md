---
title: Migrations dans les environnements d’équipe-EF Core
description: Meilleures pratiques pour la gestion des migrations et la résolution des conflits dans les environnements d’équipe avec Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 1fbb7173a52218a4d00780ebc76e33600f3558c1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619200"
---
# <a name="migrations-in-team-environments"></a>Migrations dans les environnements d’équipe

Lorsque vous travaillez avec des migrations dans des environnements d’équipe, portez une attention particulière au fichier d’instantané de modèle. Ce fichier peut vous indiquer si la migration de votre coéquipier fusionne correctement avec vous ou si vous devez résoudre un conflit en recréant la migration avant de la partager.

## <a name="merging"></a>fusion

Lorsque vous fusionnez des migrations à partir de vos coéquipiers, vous pouvez recevoir des conflits dans votre fichier d’instantané de modèle. Si les deux modifications ne sont pas liées, la fusion est triviale et les deux migrations peuvent coexister. Par exemple, vous pouvez obtenir un conflit de fusion dans la configuration du type d’entité client qui ressemble à ceci :

``` output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

Étant donné que ces deux propriétés doivent exister dans le modèle final, effectuez la fusion en ajoutant les deux propriétés. Dans de nombreux cas, votre système de contrôle de version peut automatiquement fusionner ces modifications pour vous.

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

Dans ce cas, votre migration et la migration de votre coéquipier sont indépendantes les unes des autres. Dans la mesure où l’une d’entre elles peut être appliquée en premier, vous n’avez pas besoin d’apporter des modifications supplémentaires à votre migration avant de la partager avec votre équipe.

## <a name="resolving-conflicts"></a>Résolution des conflits

Parfois, vous rencontrez un vrai conflit lors de la fusion du modèle d’instantané de modèle. Par exemple, vous et votre coéquipier pouvez avoir renommé la même propriété.

``` output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

Si vous rencontrez ce type de conflit, résolvez-le en recréant votre migration. Procédez comme suit :

1. Abandonner la fusion et la restauration dans votre répertoire de travail avant la fusion
2. Supprimer votre migration (mais conserver les modifications de votre modèle)
3. Fusionner les modifications de votre coéquipier dans votre répertoire de travail
4. Rajouter votre migration

Après cela, les deux migrations peuvent être appliquées dans le bon ordre. Leur migration est appliquée en premier, en renommant la colonne en *alias*. par la suite, votre migration le renomme nom *d’utilisateur*.

Votre migration peut être partagée en toute sécurité avec le reste de l’équipe.
