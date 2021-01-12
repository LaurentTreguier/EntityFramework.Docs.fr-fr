---
title: Test avec la base de données In-Memory EF-EF Core
description: Utilisation de la base de données en mémoire EF pour tester une application Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: 78dcac3d0fd69110986c99a097a864104caa1951
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128808"
---
# <a name="testing-with-the-ef-in-memory-database"></a>Test avec la base de données In-Memory EF

> [!WARNING]
> La base de données en mémoire EF se comporte souvent différemment des bases de données relationnelles.
> Utilisez uniquement la base de données en mémoire EF après avoir pleinement compris les problèmes et les compromis impliqués, comme indiqué dans [test de code qui utilise EF Core](xref:core/testing/index).

> [!TIP]
> SQLite est un fournisseur relationnel et peut également utiliser des bases de données en mémoire.
> Envisagez de l’utiliser pour le test afin de mieux correspondre aux comportements de base de données relationnelle courants.
> Ce sujet est abordé dans [utilisation de SQLite pour tester une application EF Core](xref:core/testing/sqlite).

Les informations de cette page se trouvent désormais dans d’autres emplacements :

* Consultez [test du code qui utilise EF Core](xref:core/testing/index) pour obtenir des informations générales sur les tests avec la base de données EF en mémoire.
* Consultez l' [exemple illustrant comment tester des applications qui utilisent EF Core](xref:core/testing/testing-sample) pour obtenir un exemple utilisant la base de données EF en mémoire.
* Pour obtenir des informations générales sur la base de données EF en mémoire, consultez [le fournisseur de base de données en mémoire EF](xref:core/providers/in-memory/index) .
