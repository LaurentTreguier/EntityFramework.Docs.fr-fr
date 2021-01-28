---
title: Suivi et requêtes de No-Tracking-EF Core
description: Informations sur le suivi et les requêtes sans suivi dans Entity Framework Core
author: smitpatel
ms.date: 11/09/2020
uid: core/querying/tracking
ms.openlocfilehash: cb18125fb3453bb533981afb36480b12727cd6f2
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983415"
---
# <a name="tracking-vs-no-tracking-queries"></a>Suivi et requêtes de No-Tracking

Suivi des contrôles de comportement si Entity Framework Core conservera des informations sur une instance d’entité dans son dispositif de suivi des modifications. Si une entité est suivie, toutes les modifications détectées dans l’entité sont rendues persistantes dans la base de données pendant `SaveChanges()`. EF Core corrigera également les propriétés de navigation entre les entités dans un résultat de requête de suivi et les entités qui se trouvent dans le dispositif de suivi des modifications.

> [!NOTE]
> Les [types d’entité sans clé](xref:core/modeling/keyless-entity-types) ne sont jamais suivis. Chaque fois que cet article mentionne des types d’entités, il fait référence aux types d’entité qui ont une clé définie.

> [!TIP]
> Vous pouvez afficher cet [exemple](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tracking) sur GitHub.

## <a name="tracking-queries"></a>Requêtes avec suivi

Par défaut, les requêtes qui retournent des types d’entités ont le suivi activé. Cela signifie que vous pouvez apporter des modifications à ces instances d’entité et faire en sorte que ces modifications soient rendues persistantes par `SaveChanges()` . Dans l’exemple suivant, la modification de l’évaluation des blogs sera détectée et rendue persistante dans la base de données pendant `SaveChanges()`.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#Tracking)]

Lorsque les résultats sont retournés dans une requête de suivi, EF Core vérifie si l’entité est déjà dans le contexte. Si EF Core trouve une entité existante, la même instance est retournée. EF Core ne remplacera pas les valeurs actuelles et d’origine des propriétés de l’entité dans l’entrée avec les valeurs de la base de données. Si l’entité est introuvable dans le contexte, EF Core crée une nouvelle instance d’entité et l’attache au contexte. Les résultats de la requête ne contiennent pas d’entité, qui est ajoutée au contexte mais pas encore enregistrée dans la base de données.

## <a name="no-tracking-queries"></a>Pas de suivi des requêtes

Les requêtes sans suivi sont utiles lorsque les résultats sont utilisés dans un scénario en lecture seule. Elles sont plus rapides à exécuter, car il n’est pas nécessaire de configurer les informations de suivi des modifications. Si vous n’avez pas besoin de mettre à jour les entités récupérées de la base de données, une requête de non-suivi doit être utilisée. Vous pouvez permuter une requête individuelle pour qu’elle soit sans suivi. Aucune requête de suivi ne vous donnera également des résultats en fonction de ce qui se trouve dans la base de données, en ce qui concerne les modifications locales ou les entités ajoutées.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTracking)]

Vous pouvez également modifier le comportement de suivi par défaut au niveau de l’instance du contexte :

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a>Résolution de l'identité

Dans la mesure où une requête de suivi utilise le suivi des modifications, EF Core effectue la résolution d’identité dans une requête de suivi. Lors de la matérialisation d’une entité, EF Core retourne la même instance d’entité à partir du dispositif de suivi des modifications si elle fait déjà l’objet d’un suivi. Si le résultat contient plusieurs fois la même entité, vous obtenez la même instance pour chaque occurrence. Les requêtes de non-suivi n’utilisent pas le dispositif de suivi des modifications et n’effectuent pas de résolution d’identité. Ainsi, vous obtenez une nouvelle instance de l’entité même lorsque la même entité est contenue plusieurs fois dans le résultat. Ce comportement est différent dans les versions antérieures à EF Core 3,0, consultez [versions précédentes](#previous-versions).

À compter de EF Core 5,0, vous pouvez combiner les deux comportements ci-dessus dans la même requête. Autrement dit, vous pouvez avoir une requête aucune suivi, ce qui entraîne la résolution de l’identité dans les résultats. Tout comme l' `AsNoTracking()` opérateur interrogeable, nous avons ajouté un autre opérateur `AsNoTrackingWithIdentityResolution()` . Une entrée associée à l’enum est également ajoutée <xref:Microsoft.EntityFrameworkCore.QueryTrackingBehavior> . Quand vous configurez la requête pour qu’elle utilise la résolution d’identité sans suivi, nous utilisons un dispositif de suivi des modifications autonome en arrière-plan lors de la génération des résultats de la requête afin que chaque instance ne soit matérialisée qu’une seule fois. Étant donné que ce dispositif de suivi des modifications est différent de celui du contexte, les résultats ne sont pas suivis par le contexte. Une fois la requête entièrement énumérée, le dispositif de suivi des modifications est hors de portée et récupéré par le garbage collector en fonction des besoins.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTrackingWithIdentityResolution)]

## <a name="tracking-and-custom-projections"></a>Suivi et projections personnalisées

Même si le type de résultat de la requête n’est pas un type d’entité, EF Core effectue toujours le suivi des types d’entité contenus dans le résultat par défaut. Dans la requête suivante, qui retourne un type anonyme, les instances de `Blog` dans le jeu de résultats sont suivies.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection1)]

Si le jeu de résultats contient des types d’entités provenant de la composition LINQ, EF Core les suit.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

Si le jeu de résultats ne contient aucun type d’entité, aucun suivi n’est effectué. Dans la requête suivante, nous retournons un type anonyme avec certaines des valeurs de l’entité (mais aucune instance du type d’entité réel). Aucune entité suivie n’est en provenance de la requête.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection3)]

 EF Core prend en charge l’évaluation du client dans la projection de niveau supérieur. Si EF Core matérialise une instance d’entité pour l’évaluation du client, elle est suivie. Ici, puisque nous passons des `blog` entités à la méthode cliente `StandardizeURL` , EF Core effectuera le suivi des instances de blog.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientMethod)]

EF Core n’effectue pas le suivi des instances d’entité keymoins contenues dans le résultat. Mais EF Core effectue le suivi de toutes les autres instances des types d’entités avec une clé selon les règles ci-dessus.

Certaines des règles ci-dessus ont fonctionné différemment avant EF Core 3,0. Pour plus d’informations, consultez [versions précédentes](#previous-versions).

## <a name="previous-versions"></a>Versions précédentes

Avant la version 3,0, les EF Core présentaient certaines différences quant à la façon dont le suivi a été effectué. Les différences notables sont les suivantes :

- Comme expliqué dans la page [évaluation du client vs Server](xref:core/querying/client-eval) , EF Core évaluation du client prise en charge dans n’importe quelle partie de la requête avant la version 3,0. L’évaluation du client a provoqué la matérialisation des entités, ce qui n’a pas fait partie du résultat. Par conséquent, EF Core analysé le résultat pour détecter les éléments à suivre. Cette conception présentait certaines différences, comme suit :
  - L’évaluation du client dans la projection, qui provoquait la matérialisation mais n’a pas retourné l’instance d’entité matérialisée n’a pas été suivie. L’exemple suivant n’a pas effectué le suivi des `blog` entités.
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

  - Dans certains cas, EF Core n’avez pas suivi les objets en provenance de la composition LINQ. L’exemple suivant n’a pas été suivi `Post` .
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

- Chaque fois que les résultats de la requête contiennent des types d’entité sans clé, l’ensemble de la requête a été rendu non suivi. Cela signifie que les types d’entité avec des clés, qui sont dans le résultat n’ont pas été suivis.
- EF Core utilisé pour effectuer la résolution d’identité dans des requêtes sans suivi. Elle utilisait des références faibles pour effectuer le suivi des entités qui avaient déjà été retournées. Par conséquent, si un jeu de résultats contenait la même entité plusieurs fois, vous obtiendriez la même instance pour chaque occurrence. Toutefois, si un résultat précédent avec la même identité est hors de portée et qu’il a été récupéré par le garbage collector, EF Core retourné une nouvelle instance.
