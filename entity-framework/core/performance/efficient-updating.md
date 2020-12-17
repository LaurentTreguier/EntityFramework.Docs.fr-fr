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
# <a name="efficient-updating"></a>Mise à jour efficace

## <a name="batching"></a>Traitement par lot

EF Core permet de réduire les boucles en regroupant automatiquement toutes les mises à jour dans un seul aller-retour. Tenez compte des éléments suivants :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

La version ci-dessus charge un blog à partir de la base de données, modifie son nom, puis ajoute deux nouveaux blogs. pour cela, deux instructions SQL INSERT et une instruction UPDATE sont envoyées à la base de données. Au lieu de les envoyer une par une, lorsque des instances de blog sont ajoutées, EF Core effectue le suivi de ces modifications en interne et les exécute dans un seul aller-retour lorsque <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> est appelé.

Le nombre d’instructions que les lots EF dans un seul aller-retour dépend du fournisseur de base de données utilisé. Par exemple, l’analyse des performances a montré que le traitement par lots est généralement moins efficace pour SQL Server lorsque moins de 4 instructions sont impliquées. De même, les avantages du traitement par lot se dégradent après environ 40 instructions pour SQL Server. par conséquent, EF Core n’exécutera par défaut que jusqu’à 42 instructions dans un lot unique et exécutera des instructions supplémentaires dans des allers-retours distincts.

Les utilisateurs peuvent également ajuster ces seuils pour obtenir des performances potentiellement supérieures, mais avec soin avant de les modifier :

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a>Mises à jour en bloc

Supposons que vous souhaitiez donner une augmentation à tous vos employés. Une implémentation classique de cette EF Core ressemble à ce qui suit :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

Bien qu’il s’agisse d’un code parfaitement valide, analysons ce qu’il fait du point de vue des performances :

* Un aller-retour de base de données est effectué pour charger tous les employés concernés. Notez que cette opération remet toutes les données de ligne des employés au client, même si seul le salaire est nécessaire.
* Le suivi des modifications de EF Core crée des instantanés lors du chargement des entités, puis compare ces instantanés aux instances pour déterminer quelles propriétés ont changé.
* Une deuxième base de données est exécutée pour enregistrer toutes les modifications. Bien que toutes les modifications soient effectuées dans un seul aller-retour grâce au traitement par lot, EF Core envoie toujours une instruction de mise à jour par employé, qui doit être exécutée par la base de données.

Les bases de données relationnelles prennent également en charge les *mises à jour en bloc*. par conséquent, vous pouvez réécrire le code ci-dessus comme instruction SQL unique suivante :

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

Cela effectue la totalité de l’opération dans un seul aller-retour, sans charger ou envoyer de données réelles à la base de données, et sans utiliser le mécanisme de suivi des modifications d’EF, ce qui impose une surcharge supplémentaire.

Malheureusement, EF ne fournit pas actuellement d’API pour effectuer des mises à jour en bloc. Tant que ces derniers n’ont pas été introduits, vous pouvez utiliser des données SQL brutes pour effectuer l’opération lorsque les performances sont sensibles :

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
