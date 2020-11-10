---
title: Portage à partir de EF6 vers EF Core-Portage d’un EDMX-Based modèle-EF
description: Informations spécifiques sur le portage d’une application de modèle Entity Framework 6 basée sur EDMX pour Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 9c1b308318c9bf0325ece0b60ace646becc8de39
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429219"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>Portage d’un modèle de EDMX-Based EF6 vers EF Core

EF Core ne prend pas en charge le format de fichier EDMX pour les modèles. La meilleure option pour porter ces modèles consiste à générer un nouveau modèle basé sur du code à partir de la base de données de votre application.

## <a name="install-ef-core-nuget-packages"></a>Installer EF Core des packages NuGet

Installez le package NuGet `Microsoft.EntityFrameworkCore.Tools`.

## <a name="regenerate-the-model"></a>Régénérer le modèle

Vous pouvez maintenant utiliser la fonctionnalité d’ingénierie à rebours pour créer un modèle basé sur votre base de données existante.

Exécutez la commande suivante dans la console du gestionnaire de package (outils – > gestionnaire de package NuGet – > console du gestionnaire de package). Consultez [console du gestionnaire de package (Visual Studio)](xref:core/cli/powershell) pour obtenir des options de commande permettant d’effectuer une génération de modèles automatique d’un sous-ensemble de tables.

```powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

Par exemple, voici la commande pour générer un modèle de structure à partir de la base de données de blogs sur votre instance SQL Server de base de données locale.

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>Supprimer le modèle EF6

Vous pouvez maintenant supprimer le modèle EF6 de votre application.

Il est parfait de conserver le package NuGet EF6 (EntityFramework) installé, car EF Core et EF6 peuvent être utilisés côte à côte dans la même application. Toutefois, si vous n’avez pas l’intention d’utiliser EF6 dans les zones de votre application, la désinstallation du package permet d’obtenir des erreurs de compilation sur des portions de code nécessitant votre attention.

## <a name="update-your-code"></a>Mettre à jour votre code

À ce stade, il s’agit de traiter les erreurs de compilation et de consulter le code pour voir si les changements de comportement entre EF6 et EF Core ont un impact sur vous.

## <a name="test-the-port"></a>Tester le port

Simplement parce que votre application est compilée, ne signifie pas qu’elle est correctement reportée vers EF Core. Vous devrez tester toutes les zones de votre application pour vous assurer qu’aucune des modifications de comportement n’a un impact négatif sur votre application.
