---
title: Versions de Visual Studio-EF6
author: divega
ms.date: 07/05/2018
ms.assetid: 028FF890-4EDB-4F03-AE53-72F9C33EC92F
ms.openlocfilehash: 16bcdc6d0e7c5632d4f4c06ba285a7a666f24204
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416939"
---
# <a name="visual-studio-releases"></a>Versions de Visual Studio

Nous vous recommandons de toujours utiliser la dernière version de Visual Studio, car elle contient les outils les plus récents pour .NET, NuGet et Entity Framework.
En fait, les différents exemples et procédures pas à pas dans la documentation de Entity Framework supposent que vous utilisez une version récente de Visual Studio.

Il est toutefois possible d’utiliser des versions antérieures de Visual Studio avec différentes versions de Entity Framework tant que vous prenez en compte certaines différences :

## <a name="visual-studio-2017-157-and-newer"></a>Visual Studio 2017 15,7 et versions ultérieures

- Cette version de Visual Studio comprend la dernière version de Entity Framework Tools et le runtime EF 6,2 et ne requiert pas d’étapes de configuration supplémentaires.
Pour plus d’informations sur ces versions, consultez [Nouveautés](~/ef6/what-is-new/index.md) .
- L’ajout d’Entity Framework aux nouveaux projets à l’aide des outils EF ajoute automatiquement le package NuGet EF 6,2.
Vous pouvez effectuer l’installation ou la mise à niveau manuellement vers n’importe quel package NuGet EF disponible en ligne.
- Par défaut, l’instance SQL Server disponible avec cette version de Visual Studio est une instance de base de données locale appelée MSSQLLocalDB.
La section serveur de la chaîne de connexion que vous devez utiliser est « (base de données locale)\\MSSQLLocalDB ».
N’oubliez pas d’utiliser une chaîne Verbatim précédée de `@` ou de deux barres obliques inverses «\\\\» C# lors de la spécification d’une chaîne de connexion dans le code.  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a>Visual Studio 2015 vers Visual Studio 2017 15,6

- Ces versions de Visual Studio incluent les outils de Entity Framework et le runtime 6.1.3.
Pour plus d’informations sur ces versions, consultez [versions antérieures](~/ef6/what-is-new/past-releases.md#ef-613) .
- L’ajout d’Entity Framework aux nouveaux projets à l’aide des outils EF ajoute automatiquement le package d’EF 6.1.3 NuGet.
Vous pouvez effectuer l’installation ou la mise à niveau manuellement vers n’importe quel package NuGet EF disponible en ligne.
- Par défaut, l’instance SQL Server disponible avec cette version de Visual Studio est une instance de base de données locale appelée MSSQLLocalDB.
La section serveur de la chaîne de connexion que vous devez utiliser est « (base de données locale)\\MSSQLLocalDB ».
N’oubliez pas d’utiliser une chaîne Verbatim précédée de `@` ou de deux barres obliques inverses «\\\\» C# lors de la spécification d’une chaîne de connexion dans le code.  


## <a name="visual-studio-2013"></a>Visual Studio 2013
- Cette version de Visual Studio comprend et une version plus ancienne de Entity Framework outils et du Runtime.
Il est recommandé d’effectuer la mise à niveau vers Entity Framework Tools 6.1.3, à l’aide [du programme d’installation](https://www.microsoft.com/download/details.aspx?id=40762) disponible dans le centre de téléchargement Microsoft.
Pour plus d’informations sur ces versions, consultez [versions antérieures](~/ef6/what-is-new/past-releases.md#ef-613) .
- L’ajout d’Entity Framework aux nouveaux projets à l’aide des outils EF mis à niveau entraîne l’ajout automatique du package d’EF 6.1.3 NuGet.
Vous pouvez effectuer l’installation ou la mise à niveau manuellement vers n’importe quel package NuGet EF disponible en ligne.
- Par défaut, l’instance SQL Server disponible avec cette version de Visual Studio est une instance de base de données locale appelée MSSQLLocalDB.
La section serveur de la chaîne de connexion que vous devez utiliser est « (base de données locale)\\MSSQLLocalDB ».
N’oubliez pas d’utiliser une chaîne Verbatim précédée de `@` ou de deux barres obliques inverses «\\\\» C# lors de la spécification d’une chaîne de connexion dans le code.  

## <a name="visual-studio-2012"></a>Visual Studio 2012

- Cette version de Visual Studio comprend et une version plus ancienne de Entity Framework outils et du Runtime.
Il est recommandé d’effectuer la mise à niveau vers Entity Framework Tools 6.1.3, à l’aide [du programme d’installation](https://www.microsoft.com/download/details.aspx?id=40762) disponible dans le centre de téléchargement Microsoft.
Pour plus d’informations sur ces versions, consultez [versions antérieures](~/ef6/what-is-new/past-releases.md#ef-613) .
- L’ajout d’Entity Framework aux nouveaux projets à l’aide des outils EF mis à niveau entraîne l’ajout automatique du package d’EF 6.1.3 NuGet.
Vous pouvez effectuer l’installation ou la mise à niveau manuellement vers n’importe quel package NuGet EF disponible en ligne.
- Par défaut, l’instance SQL Server disponible avec cette version de Visual Studio est une instance de base de données locale appelée v 11.0.
La section serveur de la chaîne de connexion que vous devez utiliser est « (base de données locale)\\v 11.0 ».
N’oubliez pas d’utiliser une chaîne Verbatim précédée de `@` ou de deux barres obliques inverses «\\\\» C# lors de la spécification d’une chaîne de connexion dans le code.  

## <a name="visual-studio-2010"></a>Visual Studio 2010

- La version de Entity Framework Tools disponible avec cette version de Visual Studio n’est pas compatible avec le runtime Entity Framework 6 et ne peut pas être mise à niveau.
- Par défaut, les outils de Entity Framework ajouteront Entity Framework 4,0 à vos projets.
Pour créer des applications à l’aide de toutes les versions plus récentes d’EF, vous devez d’abord installer l' [extension du gestionnaire de package NuGet](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager).
- Par défaut, toute la génération de code dans la version des outils EF est basée sur EntityObject et Entity Framework 4.
Nous vous recommandons de basculer la génération de code en fonction de DbContext et de Entity Framework 5, en installant les modèles de [C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC) génération de code DbContext pour ou [Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET).
- Une fois que vous avez installé les extensions du gestionnaire de package NuGet, vous pouvez effectuer manuellement l’installation ou la mise à niveau vers n’importe quel package NuGet EF disponible en ligne et utiliser EF6 avec Code First, ce qui ne nécessite pas de concepteur.
- Par défaut, l’instance SQL Server disponible avec cette version de Visual Studio est SQL Server Express nommée SQLEXPRESS.
La section serveur de la chaîne de connexion que vous devez utiliser est «.\\SQLEXPRESS».
N’oubliez pas d’utiliser une chaîne Verbatim précédée de `@` ou de deux barres obliques inverses «\\\\» C# lors de la spécification d’une chaîne de connexion dans le code.
