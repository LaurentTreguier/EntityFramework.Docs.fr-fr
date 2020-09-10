---
title: Portage à partir de EF6 vers EF Core-Portage d’un modèle basé sur EDMX-EF
description: Informations spécifiques sur le portage d’une application de modèle Entity Framework 6 basée sur EDMX pour Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 3767b1dc083ec886115cea9b0750101fb49ad84c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619607"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>Portage d’un modèle basé sur EF6 EDMX vers EF Core

EF Core ne prend pas en charge le format de fichier EDMX pour les modèles. La meilleure option pour porter ces modèles consiste à générer un nouveau modèle basé sur du code à partir de la base de données de votre application.

## <a name="install-ef-core-nuget-packages"></a>Installer EF Core des packages NuGet

Installez le package NuGet `Microsoft.EntityFrameworkCore.Tools`.

## <a name="regenerate-the-model"></a>Régénérer le modèle

Vous pouvez maintenant utiliser la fonctionnalité d’ingénierie à rebours pour créer un modèle basé sur votre base de données existante.

Exécutez la commande suivante dans la console du gestionnaire de package (outils – > gestionnaire de package NuGet – > console du gestionnaire de package). Consultez [console du gestionnaire de package (Visual Studio)](xref:core/miscellaneous/cli/powershell) pour obtenir des options de commande permettant d’effectuer une génération de modèles automatique d’un sous-ensemble de tables.

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

Par exemple, voici la commande pour générer un modèle de structure à partir de la base de données de blogs sur votre instance SQL Server de base de données locale.

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>Supprimer le modèle EF6

Vous pouvez maintenant supprimer le modèle EF6 de votre application.

Il est parfait de conserver le package NuGet EF6 (EntityFramework) installé, car EF Core et EF6 peuvent être utilisés côte à côte dans la même application. Toutefois, si vous n’avez pas l’intention d’utiliser EF6 dans les zones de votre application, la désinstallation du package permet d’obtenir des erreurs de compilation sur des portions de code nécessitant votre attention.

## <a name="update-your-code"></a>Mettre à jour votre code

À ce stade, il s’agit de traiter les erreurs de compilation et de consulter le code pour voir si les changements de comportement entre EF6 et EF Core ont un impact sur vous.

## <a name="test-the-port"></a>Tester le port

Simplement parce que votre application est compilée, ne signifie pas qu’elle est correctement reportée vers EF Core. Vous devrez tester toutes les zones de votre application pour vous assurer qu’aucune des modifications de comportement n’a un impact négatif sur votre application.
