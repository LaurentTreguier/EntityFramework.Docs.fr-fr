---
title: Installation d’Entity Framework Core - EF Core
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: 1121b2bde1ada74ee189287501bc770aeb65e358
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824439"
---
# <a name="installing-entity-framework-core"></a>Installation d’Entity Framework Core

## <a name="prerequisites"></a>Prérequis

* EF Core est une bibliothèque [.NET Standard 2.1](/dotnet/standard/net-standard). Ainsi, l’exécution d’EF Core nécessite une implémentation .NET prenant en charge .NET Standard 2.1. EF Core peut aussi être référencé par d’autres bibliothèques .NET Standard 2.1.

* Par exemple, vous pouvez utiliser EF Core pour développer des applications qui ciblent .NET Core. La création d’applications .NET Core nécessite le [SDK .NET Core](https://dotnet.microsoft.com/download). Vous pouvez aussi utiliser un environnement de développement comme [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac) ou [Visual Studio Code](https://code.visualstudio.com). Pour plus d’informations, consultez [Bien démarrer avec .NET Core](/dotnet/core/get-started).

* Vous pouvez utiliser EF Core pour développer des applications sur Windows à l’aide de Visual Studio. La dernière version de [Visual Studio](https://visualstudio.microsoft.com/vs) est recommandée.

* EF Core peut s’exécuter sur d’autres implémentations .NET comme [Xamarin](https://dotnet.microsoft.com/apps/xamarin) et .NET Native. Toutefois, dans la pratique, ces implémentations ont des limitations de runtime qui peuvent affecter le fonctionnement d’EF Core sur votre application. Pour plus d’informations, consultez [Implémentations .NET prises en charge par EF Core](xref:core/platforms/index).

* Enfin, différents fournisseurs de base de données peuvent nécessiter des versions de moteur de base de données, des implémentations .NET ou des systèmes d’exploitation spécifiques. Vérifiez qu’un [fournisseur de base de données EF Core](xref:core/providers/index) qui prend en charge l’environnement approprié pour votre application est disponible.

## <a name="get-the-entity-framework-core-runtime"></a>Obtenir le runtime Entity Framework Core

Pour ajouter EF Core à une application, installez le package NuGet du fournisseur de base de données à utiliser.

Si vous créez une application ASP.NET Core, vous n’avez pas besoin d’installer les fournisseurs en mémoire et SQL Server. Ces fournisseurs sont inclus dans les versions actuelles d’ASP.NET Core, en même temps que le runtime EF Core.  

Pour installer ou mettre à jour les packages NuGet, vous pouvez utiliser l’interface de ligne de commande (CLI) .NET Core, la boîte de dialogue Gestionnaire de package Visual Studio ou la console du Gestionnaire de package Visual Studio.

### <a name="net-core-cli"></a>CLI .NET Core

* Utilisez la commande CLI .NET Core suivante à partir de la ligne de commande du système d’exploitation pour installer ou mettre à jour le fournisseur EF Core SQL Server :

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* Vous pouvez indiquer une version spécifique dans la commande `dotnet add package`, à l’aide du modificateur `-v`. Par exemple, pour installer des packages EF Core 2.2.0, ajoutez `-v 2.2.0` à la commande.

Pour plus d’informations, consultez [Outils de l’interface de ligne de commande (CLI) .NET](/dotnet/core/tools/).

### <a name="visual-studio-nuget-package-manager-dialog"></a>Boîte de dialogue Gestionnaire de package NuGet Visual Studio

* Dans le menu Visual Studio, sélectionnez **Projet > Gérer les packages NuGet**

* Cliquez sur l’onglet **Parcourir** ou **Mises à jour**.

* Pour installer ou mettre à jour le fournisseur SQL Server, sélectionnez le package `Microsoft.EntityFrameworkCore.SqlServer` et confirmez.

Pour plus d’informations, consultez [Boîte de dialogue Gestionnaire de package NuGet](/nuget/tools/package-manager-ui).

### <a name="visual-studio-nuget-package-manager-console"></a>Console du Gestionnaire de package NuGet Visual Studio

* Dans le menu Visual Studio, sélectionnez **Outils > Gestionnaire de package NuGet > Console du Gestionnaire de package**

* Pour installer le fournisseur SQL Server, exécutez la commande suivante dans la Console du Gestionnaire de package :

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* Pour mettre à jour le fournisseur, utilisez la commande `Update-Package`.

* Pour indiquer une version spécifique, utilisez le modificateur `-Version`. Par exemple, pour installer les packages EF Core 2.2.0, ajoutez `-Version 2.2.0` aux commandes

Pour plus d’informations, consultez [Console Gestionnaire de package](/nuget/tools/package-manager-console).

## <a name="get-the-entity-framework-core-tools"></a>Obtenir les outils Entity Framework Core

Vous pouvez installer des outils pour effectuer des tâches liées à EF Core dans votre projet, comme créer et appliquer des migrations de base de données, ou créer un modèle EF Core basé sur une base de données existante.

Deux ensembles d’outils sont disponibles :

* Les [outils de l’interface de ligne de commande (CLI) .NET Core](xref:core/miscellaneous/cli/dotnet) peuvent être utilisés sur Windows, Linux ou macOS. Ces commandes commencent par `dotnet ef`.

* Les [outils de la console du Gestionnaire de package](xref:core/miscellaneous/cli/powershell) s’exécutent dans Visual Studio sur Windows. Ces commandes commencent par un verbe, par exemple `Add-Migration`, `Update-Database`.

Même si vous pouvez utiliser les commandes `dotnet ef` à partir de la console du Gestionnaire de package, nous vous recommandons d’utiliser les outils de la console du Gestionnaire de package avec Visual Studio :

* Ils fonctionnent automatiquement avec le projet sélectionné dans la console du Gestionnaire de package dans Visual Studio, sans que vous ayez besoin de changer manuellement les répertoires.  

* Elles ouvrent automatiquement les fichiers générés par les commandes de Visual Studio après avoir été exécutées.

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a>Obtenir les outils CLI .NET Core

Les outils CLI .NET Core nécessitent le SDK .NET Core, mentionné précédemment dans les [Prérequis](#prerequisites).

Les commandes `dotnet ef` sont incluses dans les versions actuelles du SDK .NET Core, mais pour les activer sur un projet spécifique, vous devez installer le package `Microsoft.EntityFrameworkCore.Design` :

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

Pour les applications ASP.NET Core, ce package est inclus automatiquement.

> [!IMPORTANT]
> Utilisez toujours la version des packages d’outils qui correspond à la version principale des packages du runtime.

### <a name="get-the-package-manager-console-tools"></a>Obtenir les outils de la console du Gestionnaire de package

Pour obtenir les outils de la console du Gestionnaire de package pour EF Core, installez le package `Microsoft.EntityFrameworkCore.Tools`. Par exemple, à partir de Visual Studio :

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Pour les applications ASP.NET Core, ce package est inclus automatiquement.

## <a name="upgrading-to-the-latest-ef-core"></a>Mise à niveau vers la dernière version EF Core

* Chaque fois que nous publions une nouvelle version d’EF Core, nous publions aussi une nouvelle version des fournisseurs qui font partie du projet EF Core, comme Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite et Microsoft.EntityFrameworkCore.InMemory. Vous pouvez simplement passer à la nouvelle version du fournisseur pour obtenir toutes les améliorations.

* EF Core ainsi que les fournisseurs en mémoire et SQL Server sont inclus dans les versions actuelles d’ASP.NET Core. Pour mettre à niveau une application ASP.NET Core existante vers une version plus récente d’EF Core, mettez toujours à niveau la version d’ASP.NET Core.

* Si vous devez mettre à jour une application qui utilise un fournisseur de base de données tiers, recherchez toujours une mise à jour du fournisseur qui est compatible avec la version d’EF Core à utiliser. Par exemple, les fournisseurs de base de données pour les versions antérieures ne sont pas compatibles avec la version 2.0 du runtime EF Core.

* Les fournisseurs tiers d’EF Core ne publient généralement pas de versions correctives en même temps que le runtime EF Core. Pour mettre à niveau une application qui utilise un fournisseur tiers vers une version corrective d’EF Core, vous devez peut-être ajouter une référence directe à des composants d’exécution EF Core individuels, comme Microsoft.EntityFrameworkCore et Microsoft.EntityFrameworkCore.Relational.

* Si vous mettez à niveau une application existante vers la dernière version d’EF Core, vous pouvez être amené à supprimer manuellement des références à des packages EF Core plus anciens :

  * Les packages design-time des fournisseurs de base de données comme `Microsoft.EntityFrameworkCore.SqlServer.Design` ne sont plus obligatoires ou pris en charge dans EF Core 2.0, mais ne sont pas automatiquement supprimés durant la mise à niveau des autres packages.

  * Les outils CLI .NET étant inclus dans le SDK .NET depuis la version 2.1, la référence à ce package peut être supprimée du fichier projet :

    ``` xml
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
    ```
