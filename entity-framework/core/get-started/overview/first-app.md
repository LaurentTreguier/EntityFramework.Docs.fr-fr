---
title: Bien démarrer - EF Core
description: Tutoriel de prise en main d’Entity Framework Core.
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/overview/first-app
ms.openlocfilehash: 560a6f293e32f4e1f75c13367291c4e31e83b157
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024340"
---
# <a name="getting-started-with-ef-core"></a>Bien démarrer avec EF Core

Dans ce tutoriel, vous allez créer une application console .NET Core qui effectue un accès aux données d’une base de données SQLite à l’aide d’Entity Framework Core.

Vous pouvez suivre le tutoriel à l’aide de Visual Studio sur Windows, ou à l’aide de l’interface CLI .NET Core sur Windows, macOS ou Linux.

[Affichez l’exemple proposé dans cet article sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/GetStarted).

## <a name="prerequisites"></a>Prérequis

Installez les logiciels suivants :

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

* [SDK .NET Core](https://www.microsoft.com/net/download/core).

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 version 16.3 ou ultérieure](https://www.visualstudio.com/downloads/) avec cette charge de travail :
  * **Développement multiplateforme .NET Core** (sous **Autres ensembles d’outils**)

---

## <a name="create-a-new-project"></a>Créer un projet

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ouvrir Visual Studio
* Cliquez sur **Créer un nouveau projet**.
* Sélectionnez **Application console (.NET Core)** avec l’étiquette **C#** , puis cliquez sur **Suivant**.
* Entrez le nom **EFGetStarted** et cliquez sur **Créer**.

---

## <a name="install-entity-framework-core"></a>Installer Entity Framework Core

Pour installer EF Core, installez le package pour le ou les fournisseurs de bases de données EF Core à cibler. Ce tutoriel utilise SQLite, car il s’exécute sur toutes les plateformes prises en charge par .NET Core. Pour obtenir la liste des fournisseurs disponibles, consultez [Fournisseurs de bases de données](xref:core/providers/index).

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Outils > Gestionnaire de package NuGet > Console du Gestionnaire de package**
* Exécutez les commandes suivantes :

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

Conseil : Vous pouvez également installer des packages en cliquant avec le bouton droit sur le projet et en sélectionnant **Gérer les packages NuGet**.

---

## <a name="create-the-model"></a>Créer le modèle

Définissez une classe de contexte et des classes d’entité qui composent le modèle.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

* Dans le répertoire du projet, créez **Model.cs** avec le code suivant.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter > Classe**.
* Entrez le nom **Model.cs** et cliquez sur **Ajouter**.
* Remplacez le contenu du fichier par le code suivant.

---

[!code-csharp[Main](../../../../samples/core/GetStarted/Model.cs)]

EF Core peut également [rétroconcevoir](xref:core/managing-schemas/scaffolding) un modèle à partir d’une base de données existante.

Conseil : Cette application garde intentionnellement les choses simples par souci de clarté. Les [chaînes de connexion](xref:core/miscellaneous/connection-strings) ne doivent pas être stockées dans le code pour les applications de production. Vous pouvez également fractionner les différentes classes C# dans leur propre fichier.

## <a name="create-the-database"></a>Créer la base de données

Les étapes suivantes utilisent des [migrations](xref:core/managing-schemas/migrations/index) pour créer une base de données.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

* Exécutez les commandes suivantes :

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  Cette action installe [dotnet ef](xref:core/cli/dotnet) et le package de conception requis pour exécuter la commande sur un projet. La commande `migrations` structure une migration afin de créer le jeu initial de tables du modèle. La commande `database update` crée la base de données et lui applique la nouvelle migration.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Exécutez les commandes suivantes dans la **console du Gestionnaire de package (PMC)** .

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  Cette action installe les [outils PMC pour EF Core](xref:core/cli/powershell). La commande `Add-Migration` structure une migration afin de créer le jeu initial de tables du modèle. La commande `Update-Database` crée la base de données et lui applique la nouvelle migration.

---

## <a name="create-read-update--delete"></a>Créer, lire, mettre à jour et supprimer

* Ouvrez le fichier *Program.cs* et remplacez le contenu par le code suivant :

  [!code-csharp[Main](../../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a>Exécuter l'application

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Déboguer > Démarrer sans débogage**

---

## <a name="next-steps"></a>Étapes suivantes

* Suivre le [tutoriel ASP.NET Core](/aspnet/core/data/ef-rp/intro) pour utiliser EF Core dans une application web
* Découvrir les [expressions de requête LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [Configurer votre modèle](xref:core/modeling/index) pour spécifier des éléments tels que la longueur [requise](xref:core/modeling/entity-properties#required-and-optional-properties) et la longueur [maximale](xref:core/modeling/entity-properties#maximum-length)
* Utiliser des [migrations](xref:core/managing-schemas/migrations/index) pour mettre à jour le schéma de base de données après avoir changé votre modèle
