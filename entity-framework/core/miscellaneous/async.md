---
title: Programmation asynchrone-EF Core
description: Interrogation et enregistrement de données de façon asynchrone avec Entity Framework Core
author: roji
ms.date: 9/2/2020
uid: core/miscellaneous/async
ms.openlocfilehash: d887bf898d741070f3f3d64b3baf0311b0ab2b63
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635794"
---
# <a name="asynchronous-programming"></a>Programmation asynchrone

Les opérations asynchrones évitent de bloquer un thread pendant que la requête est exécutée dans la base de données. Les opérations asynchrones sont importantes pour conserver une interface utilisateur réactive dans des applications clientes riches, et peuvent également augmenter le débit dans les applications Web où elles libèrent le thread pour traiter d’autres requêtes dans des applications Web.

À la suite de .NET standard, EF Core fournit des équivalents asynchrones à toutes les méthodes synchrones qui effectuent des e/s. Celles-ci ont les mêmes effets que les méthodes de synchronisation et peuvent être utilisées avec `async` les `await` Mots clés C# et. Par exemple, au lieu d’utiliser DbContext. SaveChanges, qui bloque un thread pendant l’exécution des e/s de la base de données, DbContext. SaveChangesAsync peut être utilisé :

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#SaveChangesAsync)]

Pour plus d’informations, consultez [les documents généraux sur la programmation asynchrone en C#](/dotnet/csharp/async).

> [!WARNING]
> EF Core ne prend pas en charge l’exécution de plusieurs opérations parallèles sur la même instance de contexte. Vous devez toujours attendre qu’opération se termine avant de commencer l’opération suivante. Cela s’effectue généralement en utilisant le `await` mot clé sur chaque opération asynchrone.

> [!WARNING]
> L’implémentation Async de [Microsoft. Data. SqlClient](https://github.com/dotnet/SqlClient) présente malheureusement des problèmes connus (par exemple, [#593](https://github.com/dotnet/SqlClient/issues/593), [#601](https://github.com/dotnet/SqlClient/issues/601)et d’autres).

> [!NOTE]
> EF Core passe des jetons d’annulation au fournisseur de base de données sous-jacent en cours d’utilisation (par exemple, Microsoft. Data. SqlClient). Ces jetons peuvent être ou non honorés-consultez la documentation de votre fournisseur de base de données.  

## <a name="async-linq-operators"></a>Opérateurs LINQ asynchrones

Pour prendre en charge l’exécution de requêtes LINQ de manière asynchrone, EF Core fournit un ensemble de méthodes d’extension Async qui exécutent la requête et retournent des résultats. Ces équivalents aux opérateurs LINQ standard et synchrones sont ToListAsync, SingleAsync, AsAsyncEnumerable, etc. :

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#ToListAsync)]

Notez qu’il n’existe aucune version asynchrone de certains opérateurs LINQ, tels que Where ou OrderBy, car ils génèrent uniquement l’arborescence de l’expression LINQ et n’entraînent pas l’exécution de la requête dans la base de données. Seuls les opérateurs qui entraînent l’exécution de la requête ont des équivalents asynchrones.

> [!IMPORTANT]
> Les méthodes d’extension EF Core asynchrones sont définies dans l’espace de noms `Microsoft.EntityFrameworkCore`. Cet espace de noms doit être importé pour que les méthodes soient disponibles.

## <a name="client-side-async-linq-operators"></a>Opérateurs LINQ Async côté client

Les opérateurs LINQ asynchrones décrits ci-dessus ne peuvent être utilisés que sur des requêtes EF. vous ne pouvez pas les utiliser avec une requête de LINQ to Objects côté client. Pour effectuer des opérations LINQ asynchrones côté client en dehors d’EF, utilisez le [package System. interactive. Async.](https://www.nuget.org/packages/System.Interactive.Async) Ce package peut être particulièrement utile pour effectuer des opérations sur le client qui ne peuvent pas être traduites à des fins d’évaluation sur le serveur.

Malheureusement, la référencement de System. interactive. Async provoque des erreurs de compilation d’appel ambigu sur les opérateurs LINQ appliqués au DbSets d’EF. Cela rend difficile l’utilisation à la fois d’EF et de System. interactive. Async dans le même projet. Pour contourner ce problème, ajoutez AsQueryable à votre DbSet :

[!code-csharp[Main](../../../samples/core/Miscellaneous/AsyncWithSystemInteractive/Program.cs#SystemInteractiveAsync)]
