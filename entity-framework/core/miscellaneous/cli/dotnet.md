---
title: Informations de référence sur les outils de EF Core (CLI .NET)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 07/11/2019
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 7dc7a4404820a7c935648169cc6ff8d0f0118d87
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416748"
---
# <a name="entity-framework-core-tools-reference---net-cli"></a>Informations de référence sur les outils Entity Framework Core-CLI .NET

Les outils de l’interface de ligne de commande (CLI) pour Entity Framework Core effectuer des tâches de développement au moment du Design. Par exemple, ils créent des [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), appliquent des migrations et génèrent du code pour un modèle basé sur une base de données existante. Les commandes sont une extension de la commande [dotnet](/dotnet/core/tools) multiplateforme, qui fait partie du [Kit SDK .net Core](https://www.microsoft.com/net/core). Ces outils fonctionnent avec les projets .NET Core.

Si vous utilisez Visual Studio, nous vous recommandons d’utiliser les outils de la [console du gestionnaire de package](powershell.md) à la place :

* Ils fonctionnent automatiquement avec le projet actif sélectionné dans la **console du gestionnaire de package** sans que vous ayez besoin de basculer manuellement les répertoires.
* Ils ouvrent automatiquement les fichiers générés par une commande une fois la commande terminée.

## <a name="installing-the-tools"></a>Installation des outils

La procédure d’installation dépend du type et de la version du projet :

* EF Core 3. x
* ASP.NET Core version 2,1 et versions ultérieures
* EF Core 2. x
* EF Core 1. x

### <a name="ef-core-3x"></a>EF Core 3. x

* `dotnet ef` doit être installé en tant qu’outil Global ou local. La plupart des développeurs installent `dotnet ef` comme un outil global avec la commande suivante :

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  Vous pouvez également utiliser `dotnet ef` en tant qu’outil local. Pour l’utiliser en tant qu’outil local, restaurez les dépendances d’un projet qui le déclare en tant que dépendance de l’outil à l’aide d’un [fichier manifeste d’outil](https://github.com/dotnet/cli/issues/10288).

* Installez le [kit de développement logiciel (SDK) .NET Core](https://www.microsoft.com/net/download/core).

* Installez le dernier package de `Microsoft.EntityFrameworkCore.Design`.

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="aspnet-core-21"></a>ASP.NET Core 2.1 +

* Installez le [Kit SDK .net Core](https://www.microsoft.com/net/download/core)actuel. Le kit SDK doit être installé même si vous disposez de la dernière version de Visual Studio 2017.

  C’est tout ce qui est nécessaire pour ASP.NET Core 2.1 +, car le package `Microsoft.EntityFrameworkCore.Design` est inclus dans le [AspNetCore](/aspnet/core/fundamentals/metapackage-app).

### <a name="ef-core-2x-not-aspnet-core"></a>EF Core 2. x (non ASP.NET Core)

Les commandes `dotnet ef` sont incluses dans le kit SDK .NET Core, mais pour activer les commandes dont vous avez besoin pour installer le package `Microsoft.EntityFrameworkCore.Design`.

* Installez le [Kit SDK .net Core](https://www.microsoft.com/net/download/core)actuel. Le kit de développement logiciel (SDK) doit être installé même si vous disposez de la dernière version de Visual Studio.

* Installez le dernier package `Microsoft.EntityFrameworkCore.Design` stable.

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="ef-core-1x"></a>EF Core 1. x

* Installez la version de kit SDK .NET Core 2.1.200. Les versions ultérieures ne sont pas compatibles avec les outils CLI pour EF Core 1,0 et 1,1.

* Configurez l’application pour utiliser la version du kit de développement logiciel (SDK) 2.1.200 en modifiant son fichier [global. JSON](/dotnet/core/tools/global-json) . Ce fichier est normalement inclus dans le répertoire de la solution (l’un au-dessus du projet).

* Modifiez le fichier projet et ajoutez `Microsoft.EntityFrameworkCore.Tools.DotNet` en tant qu’élément `DotNetCliToolReference`. Spécifiez la version 1. x la plus récente, par exemple : 1.1.6. Consultez l’exemple de fichier projet à la fin de cette section.

* Installez la dernière version 1. x du package `Microsoft.EntityFrameworkCore.Design`, par exemple :

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 1.1.6
  ```

  Une fois les deux références de package ajoutées, le fichier projet ressemble à ceci :

  ``` xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
      <OutputType>Exe</OutputType>
      <TargetFramework>netcoreapp1.1</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
      <PackageReference Include="Microsoft.EntityFrameworkCore.Design"
                        Version="1.1.6"
                         PrivateAssets="All" />
    </ItemGroup>
    <ItemGroup>
       <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
                              Version="1.1.6" />
    </ItemGroup>
  </Project>
  ```

  Une référence de package avec `PrivateAssets="All"` n’est pas exposée aux projets qui font référence à ce projet. Cette restriction est particulièrement utile pour les packages qui sont généralement utilisés uniquement pendant le développement.

### <a name="verify-installation"></a>Vérifier l'installation

Exécutez les commandes suivantes pour vérifier que EF Core outils CLI sont correctement installés :

  ```dotnetcli
  dotnet restore
  dotnet ef
  ```

La sortie de la commande identifie la version des outils en cours d’utilisation :

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="using-the-tools"></a>Utilisation des outils

Avant d’utiliser les outils, vous devrez peut-être créer un projet de démarrage ou définir l’environnement.

### <a name="target-project-and-startup-project"></a>Projet cible et projet de démarrage

Les commandes font référence à un *projet* et à un *projet de démarrage*.

* Le *projet* est également appelé *projet cible* , car il s’agit de l’emplacement où les commandes ajoutent ou suppriment des fichiers. Par défaut, le projet dans le répertoire actif est le projet cible. Vous pouvez spécifier un projet différent comme projet cible à l’aide de l’option <nobr>`--project`</nobr> .

* Le *projet de démarrage* est celui que les outils génèrent et exécutent. Les outils doivent exécuter le code de l’application au moment de la conception pour obtenir des informations sur le projet, telles que la chaîne de connexion à la base de données et la configuration du modèle. Par défaut, le projet dans le répertoire actif est le projet de démarrage. Vous pouvez spécifier un projet différent comme projet de démarrage à l’aide de l’option <nobr>`--startup-project`</nobr> .

Le projet de démarrage et le projet cible sont souvent le même projet. Un scénario classique dans lequel il s’agit de projets distincts est le suivant :

* Le contexte de EF Core et les classes d’entité se trouvent dans une bibliothèque de classes .NET Core.
* Une application console .NET Core ou une application Web fait référence à la bibliothèque de classes.

Il est également possible de [Placer le code de migrations dans une bibliothèque de classes distincte du contexte de EF Core](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Autres frameworks cibles

Les outils CLI fonctionnent avec les projets .NET Core et les projets de .NET Framework. Les applications qui ont le modèle EF Core dans une bibliothèque de classes .NET Standard peuvent ne pas avoir de projet .NET Core ou .NET Framework. C’est le cas, par exemple, des applications Xamarin et plateforme Windows universelle. Dans ce cas, vous pouvez créer un projet d’application console .NET Core dont le seul but est d’agir comme projet de démarrage pour les outils. Le projet peut être un projet factice sans code réel &mdash; il n’est nécessaire que pour fournir une cible pour les outils.

Pourquoi un projet factice est-il nécessaire ? Comme mentionné précédemment, les outils doivent exécuter le code de l’application au moment de la conception. Pour ce faire, ils doivent utiliser le Runtime .NET Core. Lorsque le modèle de EF Core se trouve dans un projet qui cible .NET Core ou .NET Framework, les outils de EF Core empruntent le runtime du projet. Ils ne peuvent pas le faire si le modèle de EF Core se trouve dans une bibliothèque de classes .NET Standard. Le .NET Standard n’est pas une implémentation .NET réelle. Il s’agit d’une spécification d’un ensemble d’API que les implémentations .NET doivent prendre en charge. Par conséquent, .NET Standard n’est pas suffisant pour que les outils de EF Core exécutent le code d’application. Le projet factice que vous créez à utiliser comme projet de démarrage fournit une plateforme cible concrète dans laquelle les outils peuvent charger la bibliothèque de classes .NET Standard.

### <a name="aspnet-core-environment"></a>Environnement de ASP.NET Core

Pour spécifier l’environnement pour les projets ASP.NET Core, définissez la variable d’environnement **ASPNETCORE_ENVIRONMENT** avant d’exécuter les commandes.

## <a name="common-options"></a>Options courantes

|                   | Option                            | Description                                                                                                                                                                                                                                                   |
|:------------------|:----------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                   | `--json`                          | Affichez la sortie JSON.                                                                                                                                                                                                                                             |
| <nobr>`-c`</nobr> | `--context <DBCONTEXT>`           | Classe `DbContext` à utiliser. Nom de classe uniquement ou qualifié complet avec des espaces de noms.  Si cette option est omise, EF Core trouvera la classe de contexte. S’il existe plusieurs classes de contexte, cette option est obligatoire.                                            |
| `-p`              | `--project <PROJECT>`             | Chemin d’accès relatif au dossier du projet cible.  La valeur par défaut est le dossier actif.                                                                                                                                                              |
| `-s`              | `--startup-project <PROJECT>`     | Chemin d’accès relatif au dossier du projet de démarrage. La valeur par défaut est le dossier actif.                                                                                                                                                              |
|                   | `--framework <FRAMEWORK>`         | [Moniker du Framework cible](/dotnet/standard/frameworks#supported-target-framework-versions) pour la version [cible du .NET Framework](/dotnet/standard/frameworks).  Utilisez lorsque le fichier projet spécifie plusieurs frameworks cibles et que vous souhaitez en sélectionner un. |
|                   | `--configuration <CONFIGURATION>` | Configuration de build, par exemple : `Debug` ou `Release`.                                                                                                                                                                                                   |
|                   | `--runtime <IDENTIFIER>`          | Identificateur du runtime cible pour lequel restaurer les packages. Pour connaître les identificateurs de runtime, consultez le [catalogue des identificateurs de runtime](/dotnet/core/rid-catalog).                                                                                                      |
| `-h`              | `--help`                          | Affichez les informations d’aide.                                                                                                                                                                                                                                        |
| `-v`              | `--verbose`                       | Affichez la sortie détaillée.                                                                                                                                                                                                                                          |
|                   | `--no-color`                      | Ne pas colorier la sortie.                                                                                                                                                                                                                                        |
|                   | `--prefix-output`                 | Sortie de préfixe avec niveau.                                                                                                                                                                                                                                     |

## <a name="dotnet-ef-database-drop"></a>dépôt de base de données EF dotnet

Supprime la base de données.

Options :

|                   | Option                   | Description                                              |
|:------------------|:-------------------------|:---------------------------------------------------------|
| <nobr>`-f`</nobr> | <nobr>`--force`</nobr>   | Ne pas confirmer.                                           |
|                   | <nobr>`--dry-run`</nobr> | Affichez la base de données qui sera supprimée, mais ne la supprimez pas. |

## <a name="dotnet-ef-database-update"></a>mise à jour de la base de données dotnet EF

Met à jour la base de données jusqu’à la dernière migration ou à une migration spécifiée.

Arguments :

| Argument      | Description                                                                                                                                                                                                                                                     |
|:--------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<MIGRATION>` | Migration cible. Les migrations peuvent être identifiées par leur nom ou par leur ID. Le nombre 0 est un cas spécial qui signifie *avant la première migration* et entraîne la restauration de toutes les migrations. Si aucune migration n’est spécifiée, la commande prend par défaut la dernière migration. |

Les exemples suivants mettent à jour la base de données vers une migration spécifiée. Le premier utilise le nom de la migration et le second utilise l’ID de migration :

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate
```

## <a name="dotnet-ef-dbcontext-info"></a>informations sur la DbContext dotnet EF

Obtient des informations sur un type de `DbContext`.

## <a name="dotnet-ef-dbcontext-list"></a>liste de DbContext EF dotnet

Répertorie les types de `DbContext` disponibles.

## <a name="dotnet-ef-dbcontext-scaffold"></a>structure de l’DbContext dotnet EF

Génère du code pour une `DbContext` et des types d’entités pour une base de données. Pour que cette commande génère un type d’entité, la table de base de données doit avoir une clé primaire.

Arguments :

| Argument       | Description                                                                                                                                                                                                             |
|:---------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<CONNECTION>` | Chaîne de connexion à la base de données. Pour les projets ASP.NET Core 2. x, la valeur peut être *Name =\<nom de la chaîne de connexion >* . Dans ce cas, le nom provient des sources de configuration qui sont configurées pour le projet. |
| `<PROVIDER>`   | Fournisseur à utiliser. En général, il s’agit du nom du package NuGet, par exemple : `Microsoft.EntityFrameworkCore.SqlServer`.                                                                                           |

Options :

|                 | Option                                   | Description                                                                                                                                                                    |
|:----------------|:-----------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-d</nobr> | `--data-annotations`                     | Utilisez des attributs pour configurer le modèle (dans la mesure du possible). Si cette option est omise, seule l’API Fluent est utilisée.                                                                |
| `-c`            | `--context <NAME>`                       | Nom de la classe `DbContext` à générer.                                                                                                                                 |
|                 | `--context-dir <PATH>`                   | Répertoire dans lequel placer le fichier de classe `DbContext`. Les chemins d’accès sont relatifs au répertoire du projet. Les espaces de noms sont dérivés des noms de dossiers.                                 |
| `-f`            | `--force`                                | Remplacer les fichiers existants.                                                                                                                                                      |
| `-o`            | `--output-dir <PATH>`                    | Répertoire dans lequel placer les fichiers de classe d’entité. Les chemins d’accès sont relatifs au répertoire du projet.                                                                                       |
|                 | <nobr>`--schema <SCHEMA_NAME>...`</nobr> | Schémas des tables pour lesquelles générer des types d’entité. Pour spécifier plusieurs schémas, répétez `--schema` pour chacun d’entre eux. Si cette option est omise, tous les schémas sont inclus.          |
| `-t`            | `--table <TABLE_NAME>`...                | Tables pour lesquelles générer des types d’entité. Pour spécifier plusieurs tables, répétez `-t` ou `--table` pour chacune d’elles. Si cette option est omise, toutes les tables sont incluses.                |
|                 | `--use-database-names`                   | Utilisez les noms de table et de colonne exactement tels qu’ils apparaissent dans la base de données. Si cette option est omise, les noms de base de données sont modifiés pour C# être plus conformes aux conventions de style de nom. |

L’exemple suivant génère la structure de tous les schémas et tables et place les nouveaux fichiers dans le dossier *Models* .

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

L’exemple suivant génère uniquement les tables sélectionnées et crée le contexte dans un dossier distinct avec un nom spécifié :

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext
```

## <a name="dotnet-ef-migrations-add"></a>ajout des migrations dotnet EF

Ajoute une nouvelle migration.

Arguments :

| Argument | Description                |
|:---------|:---------------------------|
| `<NAME>` | Nom de la migration. |

Options :

|                   | Option                             | Description                                                                                                      |
|:------------------|:-----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <nobr>`-o`</nobr> | <nobr>`--output-dir <PATH>`</nobr> | Répertoire (et sous-espace de noms) à utiliser. Les chemins d’accès sont relatifs au répertoire du projet. La valeur par défaut est « migrations ». |

## <a name="dotnet-ef-migrations-list"></a>Liste des migrations dotnet EF

Répertorie les migrations disponibles.

## <a name="dotnet-ef-migrations-remove"></a>suppression des migrations dotnet EF

Supprime la dernière migration (restaure les modifications de code qui ont été effectuées pour la migration).

Options :

|                   | Option    | Description                                                                     |
|:------------------|:----------|:--------------------------------------------------------------------------------|
| <nobr>`-f`</nobr> | `--force` | Rétablissez la migration (annulez les modifications qui ont été appliquées à la base de données). |

## <a name="dotnet-ef-migrations-script"></a>script de migrations dotnet EF

Génère un script SQL à partir des migrations.

Arguments :

| Argument | Description                                                                                                                                                   |
|:---------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<FROM>` | Début de la migration. Les migrations peuvent être identifiées par leur nom ou par leur ID. Le nombre 0 est un cas spécial qui signifie *avant la première migration*. La valeur par défaut est 0. |
| `<TO>`   | Fin de la migration. La valeur par défaut est la dernière migration.                                                                                                         |

Options :

|                   | Option            | Description                                                        |
|:------------------|:------------------|:-------------------------------------------------------------------|
| <nobr>`-o`</nobr> | `--output <FILE>` | Fichier dans lequel écrire le script.                                   |
| `-i`              | `--idempotent`    | Générez un script qui peut être utilisé sur une base de données lors d’une migration. |

L’exemple suivant crée un script pour la migration InitialCreate :

```dotnetcli
dotnet ef migrations script 0 InitialCreate
```

L’exemple suivant crée un script pour toutes les migrations après la migration de InitialCreate.

```dotnetcli
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Migrations](xref:core/managing-schemas/migrations/index)
* [Reconstitution de la logique des produits](xref:core/managing-schemas/scaffolding)
