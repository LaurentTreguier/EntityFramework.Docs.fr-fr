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
# <a name="migrations-in-team-environments"></a><span data-ttu-id="4bd47-103">Migrations dans les environnements d’équipe</span><span class="sxs-lookup"><span data-stu-id="4bd47-103">Migrations in Team Environments</span></span>

<span data-ttu-id="4bd47-104">Lorsque vous travaillez avec des migrations dans des environnements d’équipe, portez une attention particulière au fichier d’instantané de modèle.</span><span class="sxs-lookup"><span data-stu-id="4bd47-104">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="4bd47-105">Ce fichier peut vous indiquer si la migration de votre coéquipier fusionne correctement avec vous ou si vous devez résoudre un conflit en recréant la migration avant de la partager.</span><span class="sxs-lookup"><span data-stu-id="4bd47-105">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

## <a name="merging"></a><span data-ttu-id="4bd47-106">fusion</span><span class="sxs-lookup"><span data-stu-id="4bd47-106">Merging</span></span>

<span data-ttu-id="4bd47-107">Lorsque vous fusionnez des migrations à partir de vos coéquipiers, vous pouvez recevoir des conflits dans votre fichier d’instantané de modèle.</span><span class="sxs-lookup"><span data-stu-id="4bd47-107">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="4bd47-108">Si les deux modifications ne sont pas liées, la fusion est triviale et les deux migrations peuvent coexister.</span><span class="sxs-lookup"><span data-stu-id="4bd47-108">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="4bd47-109">Par exemple, vous pouvez obtenir un conflit de fusion dans la configuration du type d’entité client qui ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="4bd47-109">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

``` output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

<span data-ttu-id="4bd47-110">Étant donné que ces deux propriétés doivent exister dans le modèle final, effectuez la fusion en ajoutant les deux propriétés.</span><span class="sxs-lookup"><span data-stu-id="4bd47-110">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="4bd47-111">Dans de nombreux cas, votre système de contrôle de version peut automatiquement fusionner ces modifications pour vous.</span><span class="sxs-lookup"><span data-stu-id="4bd47-111">In many cases, your version control system may automatically merge such changes for you.</span></span>

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="4bd47-112">Dans ce cas, votre migration et la migration de votre coéquipier sont indépendantes les unes des autres.</span><span class="sxs-lookup"><span data-stu-id="4bd47-112">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="4bd47-113">Dans la mesure où l’une d’entre elles peut être appliquée en premier, vous n’avez pas besoin d’apporter des modifications supplémentaires à votre migration avant de la partager avec votre équipe.</span><span class="sxs-lookup"><span data-stu-id="4bd47-113">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

## <a name="resolving-conflicts"></a><span data-ttu-id="4bd47-114">Résolution des conflits</span><span class="sxs-lookup"><span data-stu-id="4bd47-114">Resolving conflicts</span></span>

<span data-ttu-id="4bd47-115">Parfois, vous rencontrez un vrai conflit lors de la fusion du modèle d’instantané de modèle.</span><span class="sxs-lookup"><span data-stu-id="4bd47-115">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="4bd47-116">Par exemple, vous et votre coéquipier pouvez avoir renommé la même propriété.</span><span class="sxs-lookup"><span data-stu-id="4bd47-116">For example, you and your teammate may each have renamed the same property.</span></span>

``` output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

<span data-ttu-id="4bd47-117">Si vous rencontrez ce type de conflit, résolvez-le en recréant votre migration.</span><span class="sxs-lookup"><span data-stu-id="4bd47-117">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="4bd47-118">Procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="4bd47-118">Follow these steps:</span></span>

1. <span data-ttu-id="4bd47-119">Abandonner la fusion et la restauration dans votre répertoire de travail avant la fusion</span><span class="sxs-lookup"><span data-stu-id="4bd47-119">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="4bd47-120">Supprimer votre migration (mais conserver les modifications de votre modèle)</span><span class="sxs-lookup"><span data-stu-id="4bd47-120">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="4bd47-121">Fusionner les modifications de votre coéquipier dans votre répertoire de travail</span><span class="sxs-lookup"><span data-stu-id="4bd47-121">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="4bd47-122">Rajouter votre migration</span><span class="sxs-lookup"><span data-stu-id="4bd47-122">Re-add your migration</span></span>

<span data-ttu-id="4bd47-123">Après cela, les deux migrations peuvent être appliquées dans le bon ordre.</span><span class="sxs-lookup"><span data-stu-id="4bd47-123">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="4bd47-124">Leur migration est appliquée en premier, en renommant la colonne en *alias*. par la suite, votre migration le renomme nom *d’utilisateur*.</span><span class="sxs-lookup"><span data-stu-id="4bd47-124">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="4bd47-125">Votre migration peut être partagée en toute sécurité avec le reste de l’équipe.</span><span class="sxs-lookup"><span data-stu-id="4bd47-125">Your migration can safely be shared with the rest of the team.</span></span>
