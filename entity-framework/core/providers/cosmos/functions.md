---
title: Mappages de fonction-fournisseur Azure Cosmos DB-EF Core
description: Mappages de fonctions du fournisseur de EF Core Azure Cosmos DB
author: bricelam
ms.date: 1/26/2021
uid: core/providers/cosmos/functions
ms.openlocfilehash: d4d45ba7db50befb5eb775feed0af44468ee3f74
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543586"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Mappages de fonctions du fournisseur de EF Core Azure Cosmos DB

Cette page affiche les membres .NET qui sont traduits en fonctions SQL lors de l’utilisation du fournisseur Azure Cosmos DB.

.NET                          | SQL                              | Ajouté à
----------------------------- | -------------------------------- | --------
collecte. Contains (Item)     | @item DANS @collection
EF. Functions. Random ()         | RAND ()                           | EF Core 6.0
stringValue. Contains (valeur)   | CONTIENT ( @stringValue , @value )   | EF Core 5.0
stringValue. EndsWith (valeur)   | ENDSWITH ( @stringValue , @value )   | EF Core 5.0
stringValue. FirstOrDefault ()  | GAUCHE ( @stringValue , 1)            | EF Core 5.0
stringValue. LastOrDefault ()   | DROITE ( @stringValue , 1)           | EF Core 5.0
stringValue. StartsWith (valeur) | STARTSWITH ( @stringValue , @value ) | EF Core 5.0
