---
title: Prise en charge des fournisseurs pour les types spatiaux-EF6
description: Prise en charge des fournisseurs pour les types spatiaux dans Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 1097cb00-15f5-453d-90ed-bff9403d23e3
uid: ef6/fundamentals/providers/spatial-support
ms.openlocfilehash: 060d662aa8f03ea3510bd6b1fb7bdf904585efab
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89615797"
---
# <a name="provider-support-for-spatial-types"></a>Prise en charge des fournisseurs pour les types spatiaux
Entity Framework prend en charge l’utilisation des données spatiales via les classes DbGeography ou DbGeometry. Ces classes reposent sur les fonctionnalités spécifiques à la base de données offertes par le fournisseur Entity Framework. Tous les fournisseurs ne prennent pas en charge les données spatiales et ceux qui peuvent avoir des conditions préalables supplémentaires telles que l’installation d’assemblys de type spatial. Vous trouverez ci-dessous des informations supplémentaires sur la prise en charge des fournisseurs pour les types spatiaux.  

Vous trouverez des informations supplémentaires sur l’utilisation des types spatiaux dans une application dans deux procédures pas à pas, une pour Code First, l’autre pour Database First ou Model First :  

- [Types de données spatiales dans Code First](xref:ef6/modeling/code-first/data-types/spatial)  
- [Types de données spatiales dans le concepteur EF](xref:ef6/modeling/designer/data-types/spatial)  

## <a name="ef-releases-that-support-spatial-types"></a>Versions d’EF qui prennent en charge les types spatiaux  

La prise en charge des types spatiaux a été introduite dans EF5. Toutefois, dans les types spatiaux EF5 sont uniquement pris en charge lorsque l’application cible et s’exécute sur .NET 4,5.  

Le démarrage des types spatiaux EF6 est pris en charge pour les applications ciblant à la fois .NET 4 et .NET 4,5.  

## <a name="ef-providers-that-support-spatial-types"></a>Fournisseurs EF qui prennent en charge les types spatiaux  

### <a name="ef5"></a>EF5  

Les fournisseurs de Entity Framework pour EF5 que nous avons conscients de la prise en charge des types spatiaux sont :  

- Fournisseur Microsoft SQL Server  
    - Ce fournisseur est fourni dans le cadre de EF5.  
    - Ce fournisseur dépend de certaines bibliothèques de bas niveau supplémentaires qui devront peut-être être installées. pour plus d’informations, voir ci-dessous.  
- [Devart dotConnect pour Oracle](https://www.devart.com/dotconnect/oracle/)  
    - Il s’agit d’un fournisseur tiers de Devart.  

Si vous connaissez un fournisseur EF5 qui prend en charge les types spatiaux, contactez-nous et nous serons heureux de l’ajouter à cette liste.  

### <a name="ef6"></a>EF6  

Les fournisseurs de Entity Framework pour EF6 que nous avons conscients de la prise en charge des types spatiaux sont :  

- Fournisseur Microsoft SQL Server  
    - Ce fournisseur est fourni dans le cadre de EF6.  
    - Ce fournisseur dépend de certaines bibliothèques de bas niveau supplémentaires qui devront peut-être être installées. pour plus d’informations, voir ci-dessous.  
- [Devart dotConnect pour Oracle](https://www.devart.com/dotconnect/oracle/)  
    - Il s’agit d’un fournisseur tiers de Devart.  

Si vous connaissez un fournisseur EF6 qui prend en charge les types spatiaux, contactez-nous et nous serons heureux de l’ajouter à cette liste.  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a>Conditions préalables pour les types spatiaux avec Microsoft SQL Server  

SQL Server la prise en charge spatiale dépend des types de SQL Server de bas niveau, SqlGeography et SqlGeometry. Ces types se trouvent dans Microsoft.SqlServer.Types.dll assembly, et cet assembly n’est pas fourni avec EF ou dans le cadre du .NET Framework.  

Quand Visual Studio est installé, il installe également une version de SQL Server, ce qui inclut l’installation du Microsoft.SqlServer.Types.dll.  

Si SQL Server n’est pas installé sur l’ordinateur où vous souhaitez utiliser les types spatiaux, ou si les types spatiaux ont été exclus de l’installation SQL Server, vous devez les installer manuellement. Les types peuvent être installés à l’aide `SQLSysClrTypes.msi` de, qui fait partie de Microsoft SQL Server Feature Pack. Les types spatiaux étant spécifiques à la version SQL Server, nous vous recommandons de [Rechercher « SQL Server Feature Pack »](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) dans le centre de téléchargement Microsoft, puis de sélectionner et de télécharger l’option correspondant à la version de SQL Server que vous allez utiliser.
