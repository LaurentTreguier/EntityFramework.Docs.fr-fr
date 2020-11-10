---
title: Test avec la base de données In-Memory EF-EF Core
description: Utilisation de la base de données en mémoire EF pour tester une application Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: db91570dc9d5a4b95d513df509867e9bca406356
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431509"
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
