---
title: Mise à jour efficace-EF Core
description: Guide des performances pour une mise à jour efficace à l’aide de Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-updating
ms.openlocfilehash: 92766d2339fb04ed5ebc3123429171cc9be424b1
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657826"
---
# <a name="efficient-updating"></a><span data-ttu-id="b0489-103">Mise à jour efficace</span><span class="sxs-lookup"><span data-stu-id="b0489-103">Efficient Updating</span></span>

## <a name="batching"></a><span data-ttu-id="b0489-104">Traitement par lot</span><span class="sxs-lookup"><span data-stu-id="b0489-104">Batching</span></span>

<span data-ttu-id="b0489-105">EF Core permet de réduire les boucles en regroupant automatiquement toutes les mises à jour dans un seul aller-retour.</span><span class="sxs-lookup"><span data-stu-id="b0489-105">EF Core helps minimize roundtrips by automatically batching together all updates in a single roundtrip.</span></span> <span data-ttu-id="b0489-106">Tenez compte des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b0489-106">Consider the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

<span data-ttu-id="b0489-107">La version ci-dessus charge un blog à partir de la base de données, modifie son nom, puis ajoute deux nouveaux blogs. pour cela, deux instructions SQL INSERT et une instruction UPDATE sont envoyées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="b0489-107">The above loads a blog from the database, changes its name, and then adds two new blogs; to apply this, two SQL INSERT statements and one UPDATE statement are sent to the database.</span></span> <span data-ttu-id="b0489-108">Au lieu de les envoyer une par une, lorsque des instances de blog sont ajoutées, EF Core effectue le suivi de ces modifications en interne et les exécute dans un seul aller-retour lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé.</span><span class="sxs-lookup"><span data-stu-id="b0489-108">Rather than sending them one by one, as Blog instances are added, EF Core tracks these changes internally, and executes them in a single roundtrip when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="b0489-109">Le nombre d’instructions que les lots EF dans un seul aller-retour dépend du fournisseur de base de données utilisé.</span><span class="sxs-lookup"><span data-stu-id="b0489-109">The number of statements that EF batches in a single roundtrip depends on the database provider being used.</span></span> <span data-ttu-id="b0489-110">Par exemple, l’analyse des performances a montré que le traitement par lots est généralement moins efficace pour SQL Server lorsque moins de 4 instructions sont impliquées.</span><span class="sxs-lookup"><span data-stu-id="b0489-110">For example, performance analysis has shown batching to be generally less efficient for SQL Server when less than 4 statements are involved.</span></span> <span data-ttu-id="b0489-111">De même, les avantages du traitement par lot se dégradent après environ 40 instructions pour SQL Server. par conséquent, EF Core n’exécutera par défaut que jusqu’à 42 instructions dans un lot unique et exécutera des instructions supplémentaires dans des allers-retours distincts.</span><span class="sxs-lookup"><span data-stu-id="b0489-111">Similarly, the benefits of batching degrade after around 40 statements for SQL Server, so EF Core will by default only execute up to 42 statements in a single batch, and execute additional statements in separate roundtrips.</span></span>

<span data-ttu-id="b0489-112">Les utilisateurs peuvent également ajuster ces seuils pour obtenir des performances potentiellement supérieures, mais avec soin avant de les modifier :</span><span class="sxs-lookup"><span data-stu-id="b0489-112">Users can also tweak these thresholds to achieve potentially higher performance - but benchmark carefully before modifying these:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a><span data-ttu-id="b0489-113">Mises à jour en bloc</span><span class="sxs-lookup"><span data-stu-id="b0489-113">Bulk updates</span></span>

<span data-ttu-id="b0489-114">Supposons que vous souhaitiez donner une augmentation à tous vos employés.</span><span class="sxs-lookup"><span data-stu-id="b0489-114">Let's assume you want to give all your employees a raise.</span></span> <span data-ttu-id="b0489-115">Une implémentation classique de cette EF Core ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="b0489-115">A typical implementation for this in EF Core would look like the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

<span data-ttu-id="b0489-116">Bien qu’il s’agisse d’un code parfaitement valide, analysons ce qu’il fait du point de vue des performances :</span><span class="sxs-lookup"><span data-stu-id="b0489-116">While this is perfectly valid code, let's analyze what it does from a performance perspective:</span></span>

* <span data-ttu-id="b0489-117">Un aller-retour de base de données est effectué pour charger tous les employés concernés. Notez que cette opération remet toutes les données de ligne des employés au client, même si seul le salaire est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b0489-117">A database roundtrip is performed, to load all the relevant employees; note that this brings all the Employees' row data to the client, even if only the salary will be needed.</span></span>
* <span data-ttu-id="b0489-118">Le suivi des modifications de EF Core crée des instantanés lors du chargement des entités, puis compare ces instantanés aux instances pour déterminer quelles propriétés ont changé.</span><span class="sxs-lookup"><span data-stu-id="b0489-118">EF Core's change tracking creates snapshots when loading the entities, and then compares those snapshots to the instances to find out which properties changed.</span></span>
* <span data-ttu-id="b0489-119">Une deuxième base de données est exécutée pour enregistrer toutes les modifications.</span><span class="sxs-lookup"><span data-stu-id="b0489-119">A second database roundtrip is performed to save all the changes.</span></span> <span data-ttu-id="b0489-120">Bien que toutes les modifications soient effectuées dans un seul aller-retour grâce au traitement par lot, EF Core envoie toujours une instruction de mise à jour par employé, qui doit être exécutée par la base de données.</span><span class="sxs-lookup"><span data-stu-id="b0489-120">While all changes are done in a single roundtrip thanks to batching, EF Core still sends an UPDATE statement per employee, which must be executed by the database.</span></span>

<span data-ttu-id="b0489-121">Les bases de données relationnelles prennent également en charge les *mises à jour en bloc*. par conséquent, vous pouvez réécrire le code ci-dessus comme instruction SQL unique suivante :</span><span class="sxs-lookup"><span data-stu-id="b0489-121">Relational databases also support *bulk updates*, so the above could be rewritten as the following single SQL statement:</span></span>

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

<span data-ttu-id="b0489-122">Cela effectue la totalité de l’opération dans un seul aller-retour, sans charger ou envoyer de données réelles à la base de données, et sans utiliser le mécanisme de suivi des modifications d’EF, ce qui impose une surcharge supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="b0489-122">This performs the entire operation in a single roundtrip, without loading or sending any actual data to the database, and without making use of EF's change tracking machinery, which imposes an additional overhead.</span></span>

<span data-ttu-id="b0489-123">Malheureusement, EF ne fournit pas actuellement d’API pour effectuer des mises à jour en bloc.</span><span class="sxs-lookup"><span data-stu-id="b0489-123">Unfortunately, EF doesn't currently provide APIs for performing bulk updates.</span></span> <span data-ttu-id="b0489-124">Tant que ces derniers n’ont pas été introduits, vous pouvez utiliser des données SQL brutes pour effectuer l’opération lorsque les performances sont sensibles :</span><span class="sxs-lookup"><span data-stu-id="b0489-124">Until these are introduced, you can use raw SQL to perform the operation where performance is sensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
