---
title: Test avec la base de données en mémoire EF-EF Core
description: Utilisation de la base de données en mémoire EF pour tester une application Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 353f67fb0e78fefa74fc77d302e505bacb692ed4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619398"
---
# <a name="testing-with-the-ef-in-memory-database"></a>Test avec la base de données EF en mémoire

> [!WARNING]
> La base de données en mémoire EF se comporte souvent différemment des bases de données relationnelles.
> Utilisez uniquement la base de données en mémoire EF après avoir pleinement compris les problèmes et les compromis impliqués, comme indiqué dans [test de code qui utilise EF Core](xref:core/miscellaneous/testing/index).  

> [!TIP]
> SQLite est un fournisseur relationnel et peut également utiliser des bases de données en mémoire.
> Envisagez de l’utiliser pour le test afin de mieux correspondre aux comportements de base de données relationnelle courants.
> Ce sujet est abordé dans [utilisation de SQLite pour tester une application EF Core](xref:core/miscellaneous/testing/sqlite).   

Les informations de cette page se trouvent désormais dans d’autres emplacements :
* Consultez [test du code qui utilise EF Core](xref:core/miscellaneous/testing/index) pour obtenir des informations générales sur les tests avec la base de données EF en mémoire.
* Consultez l' [exemple illustrant comment tester des applications qui utilisent EF Core](xref:core/miscellaneous/testing/testing-sample) pour obtenir un exemple utilisant la base de données EF en mémoire.
* Pour obtenir des informations générales sur la base de données EF en mémoire, consultez [le fournisseur de base de données en mémoire EF](xref:core/providers/in-memory/index) .
