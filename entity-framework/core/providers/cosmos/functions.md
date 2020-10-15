---
title: Mappages de fonction-fournisseur Azure Cosmos DB-EF Core
description: Mappages de fonctions du fournisseur de EF Core Azure Cosmos DB
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066607"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Mappages de fonctions du fournisseur de EF Core Azure Cosmos DB

Cette page affiche les membres .NET qui sont traduits en fonctions SQL lors de l’utilisation du fournisseur Azure Cosmos DB.

.NET                          | SQL                              | Ajouté à
----------------------------- | -------------------------------- | --------
collecte. Contains (Item)     | @item DANS @collection
stringValue. Contains (valeur)   | CONTIENT ( @stringValue , @value )   | EF Core 5.0
stringValue. EndsWith (valeur)   | ENDSWITH ( @stringValue , @value )   | EF Core 5.0
stringValue. FirstOrDefault ()  | GAUCHE ( @stringValue , 1)            | EF Core 5.0
stringValue. LastOrDefault ()   | DROITE ( @stringValue , 1)           | EF Core 5.0
stringValue. StartsWith (valeur) | STARTSWITH ( @stringValue , @value ) | EF Core 5.0
