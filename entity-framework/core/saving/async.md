---
title: 'Enregistrement asynchrone : EF Core'
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
uid: core/saving/async
ms.openlocfilehash: aa1fbfdc44895eace04354063435f98370aed2c4
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526912"
---
# <a name="asynchronous-saving"></a>Enregistrement asynchrone

L’enregistrement asynchrone évite de bloquer un thread lorsque les modifications sont écrites dans la base de données. Cela peut être utile pour éviter le gel de l’interface utilisateur d’une application client lourde. Les opérations asynchrones peuvent également augmenter le débit dans une application web, où le thread peut être libéré pour d’autres demandes de service lors de la fin de l’opération de base de données. Pour plus d’informations, consultez [programmation asynchrone en C#](/dotnet/csharp/async).

> [!WARNING]  
> EF Core ne prend pas en charge les opérations parallèles multiples en cours d’exécution sur la même instance de contexte. Vous devez toujours attendre qu’opération se termine avant de commencer l’opération suivante. Cela est généralement effectué à l’aide du mot-clé `await` sur chaque opération asynchrone.

Entity Framework Core fournit `DbContext.SaveChangesAsync()` comme alternative asynchrone à `DbContext.SaveChanges()`.

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
