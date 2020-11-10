---
title: Informations de référence sur les outils de EF Core (CLI .NET)-EF Core
description: Guide de référence pour les outils de CLI .NET Core Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dotnet
ms.openlocfilehash: 83989b8690236dbec3466cda78c204ab67fd10c4
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431582"
---
# <a name="entity-framework-core-tools-reference---net-core-cli"></a>Informations de référence sur les outils de Entity Framework Core-CLI .NET Core

Les outils de l’interface de ligne de commande (CLI) pour Entity Framework Core effectuer des tâches de développement au moment du Design. Par exemple, ils créent des [migrations](/aspnet/core/data/ef-mvc/migrations), appliquent des migrations et génèrent du code pour un modèle basé sur une base de données existante. Les commandes sont une extension de la commande [dotnet](/dotnet/core/tools) multiplateforme, qui fait partie du [Kit SDK .net Core](https://www.microsoft.com/net/core). Ces outils fonctionnent avec les projets .NET Core.

Lorsque vous utilisez Visual Studio, envisagez d’utiliser les outils de la [console du gestionnaire de package](xref:core/cli/powershell) à la place des outils CLI. Outils de la console du gestionnaire de package :

* Fonctionne avec le projet actif sélectionné dans la **console du gestionnaire de package** sans que vous ayez besoin de basculer manuellement les répertoires.
* Ouvre les fichiers générés par une commande une fois la commande terminée.
* Fournit la saisie semi-automatique par tabulation des commandes, des paramètres, des noms de projets, des types de contexte et des noms de migration.

## <a name="installing-the-tools"></a>Installation des outils

`dotnet ef` peut être installé en tant qu’outil Global ou local. La plupart des développeurs préfèrent installer `dotnet ef` en tant qu’outil Global à l’aide de la commande suivante :

```dotnetcli
dotnet tool install --global dotnet-ef
```

Pour l’utiliser en tant qu’outil local, restaurez les dépendances d’un projet qui le déclare en tant que dépendance de l’outil à l’aide d’un [fichier manifeste d’outil](/dotnet/core/tools/global-tools#install-a-local-tool).

Mettez à jour l’outil Tool à l’aide de la commande suivante :

```dotnetcli
dotnet tool update --global dotnet-ef
```

Avant de pouvoir utiliser les outils sur un projet spécifique, vous devez y ajouter le `Microsoft.EntityFrameworkCore.Design` Package.

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

### <a name="verify-installation"></a>Vérifier l’installation

Exécutez les commandes suivantes pour vérifier que EF Core outils CLI sont correctement installés :

  ```dotnetcli
  dotnet ef
  ```

La sortie de la commande identifie la version des outils en cours d’utilisation :

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="update-the-tools"></a>Mettre à jour Data Lake Tools

Utilisez `dotnet tool update --global dotnet-ef` pour mettre à jour les outils globaux vers la dernière version disponible. Si les outils sont installés localement dans votre projet, utilisez `dotnet tool update dotnet-ef` . Installez une version spécifique en ajoutant `--version <VERSION>` à votre commande. Pour plus d’informations, consultez la section [mise à jour](/dotnet/core/tools/dotnet-tool-update) de la documentation de l’outil dotnet.

## <a name="using-the-tools"></a>Utilisation des outils

Avant d’utiliser les outils, vous devrez peut-être créer un projet de démarrage ou définir l’environnement.

### <a name="target-project-and-startup-project"></a>Projet cible et projet de démarrage

Les commandes font référence à un *projet* et à un *projet de démarrage*.

* Le *projet* est également appelé *projet cible* , car il s’agit de l’emplacement où les commandes ajoutent ou suppriment des fichiers. Par défaut, le projet dans le répertoire actif est le projet cible. Vous pouvez spécifier un projet différent comme projet cible à l’aide de l' <nobr>`--project`</nobr> option.

* Le *projet de démarrage* est celui que les outils génèrent et exécutent. Les outils doivent exécuter le code de l’application au moment de la conception pour obtenir des informations sur le projet, telles que la chaîne de connexion à la base de données et la configuration du modèle. Par défaut, le projet dans le répertoire actif est le projet de démarrage. Vous pouvez spécifier un projet différent comme projet de démarrage à l’aide de l' <nobr>`--startup-project`</nobr> option.

Le projet de démarrage et le projet cible sont souvent le même projet. Un scénario classique dans lequel il s’agit de projets distincts est le suivant :

* Le contexte de EF Core et les classes d’entité se trouvent dans une bibliothèque de classes .NET Core.
* Une application console .NET Core ou une application Web fait référence à la bibliothèque de classes.

Il est également possible de [Placer le code de migrations dans une bibliothèque de classes distincte du contexte de EF Core](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Autres frameworks cibles

Les outils CLI fonctionnent avec les projets .NET Core et les projets de .NET Framework. Les applications qui ont le modèle EF Core dans une bibliothèque de classes .NET Standard peuvent ne pas avoir de projet .NET Core ou .NET Framework. C’est le cas, par exemple, des applications Xamarin et plateforme Windows universelle. Dans ce cas, vous pouvez créer un projet d’application console .NET Core dont le seul but est d’agir comme projet de démarrage pour les outils. Le projet peut être un projet factice sans code réel &mdash; , il n’est nécessaire que pour fournir une cible pour les outils.

Pourquoi un projet factice est-il nécessaire ? Comme mentionné précédemment, les outils doivent exécuter le code de l’application au moment de la conception. Pour ce faire, ils doivent utiliser le Runtime .NET Core. Lorsque le modèle de EF Core se trouve dans un projet qui cible .NET Core ou .NET Framework, les outils de EF Core empruntent le runtime du projet. Ils ne peuvent pas le faire si le modèle de EF Core se trouve dans une bibliothèque de classes .NET Standard. Le .NET Standard n’est pas une implémentation .NET réelle. Il s’agit d’une spécification d’un ensemble d’API que les implémentations .NET doivent prendre en charge. Par conséquent, .NET Standard n’est pas suffisant pour que les outils de EF Core exécutent le code d’application. Le projet factice que vous créez à utiliser comme projet de démarrage fournit une plateforme cible concrète dans laquelle les outils peuvent charger la bibliothèque de classes .NET Standard.

### <a name="aspnet-core-environment"></a>Environnement de ASP.NET Core

Pour spécifier [l’environnement](/aspnet/core/fundamentals/environments) pour les projets ASP.net Core, définissez la variable d’environnement **ASPNETCORE_ENVIRONMENT** avant d’exécuter les commandes.

À compter de EF Core 5,0, des arguments supplémentaires peuvent également être passés dans Program. CreateHostBuilder, ce qui vous permet de spécifier l’environnement sur la ligne de commande :

```dotnetcli
dotnet ef database update -- --environment Production
```

> [!TIP]
> Le `--` jeton indique `dotnet ef` à traiter tout ce qui suit comme argument et ne tente pas de les analyser en tant qu’options. Les arguments supplémentaires non utilisés par `dotnet ef` sont transmis à l’application.

## <a name="common-options"></a>Options courantes

| Option                                         | Court             | Description                                                                                                                                                                                                                                                   |
|:-----------------------------------------------|:------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--json`                                       |                   | Affichez la sortie JSON.                                                                                                                                                                                                                                             |
| `--context <DBCONTEXT>`                        | <nobr>`-c`</nobr> | Classe `DbContext` à utiliser. Nom de classe uniquement ou qualifié complet avec des espaces de noms.  Si cette option est omise, EF Core trouvera la classe de contexte. S’il existe plusieurs classes de contexte, cette option est obligatoire.                                            |
| `--project <PROJECT>`                          | `-p`              | Chemin d’accès relatif au dossier du projet cible.  La valeur par défaut est le dossier actif.                                                                                                                                                              |
| `--startup-project <PROJECT>`                  | `-s`              | Chemin d’accès relatif au dossier du projet de démarrage. La valeur par défaut est le dossier actif.                                                                                                                                                              |
| `--framework <FRAMEWORK>`                      |                   | [Moniker du Framework cible](/dotnet/standard/frameworks#supported-target-framework-versions) pour la version [cible du .NET Framework](/dotnet/standard/frameworks).  Utilisez lorsque le fichier projet spécifie plusieurs frameworks cibles et que vous souhaitez en sélectionner un. |
| <nobr>`--configuration <CONFIGURATION>`</nobr> |                   | Configuration de build, par exemple : `Debug` ou `Release` .                                                                                                                                                                                                   |
| `--runtime <IDENTIFIER>`                       |                   | Identificateur du runtime cible pour lequel restaurer les packages. Pour connaître les identificateurs de runtime, consultez le [catalogue des identificateurs de runtime](/dotnet/core/rid-catalog).                                                                                                      |
| `--no-build`                                   |                   | Ne générez pas le projet. Destiné à être utilisé lorsque la build est à jour.                                                                                                                                                                                    |
| `--help`                                       | `-h`              | Affichez les informations d’aide.                                                                                                                                                                                                                                        |
| `--verbose`                                    | `-v`              | Affichez la sortie détaillée.                                                                                                                                                                                                                                          |
| `--no-color`                                   |                   | Ne pas colorier la sortie.                                                                                                                                                                                                                                        |
| `--prefix-output`                              |                   | Sortie de préfixe avec niveau.                                                                                                                                                                                                                                     |

À partir de EF Core 5,0, tous les arguments supplémentaires sont passés à l’application.

## <a name="dotnet-ef-database-drop"></a>dépôt de base de données EF dotnet

Supprime la base de données.

Options :

| Option                   | Court             | Description                                              |
|:-------------------------|:------------------|:---------------------------------------------------------|
| `--force`                | <nobr>`-f`</nobr> | Ne pas confirmer.                                           |
| <nobr>`--dry-run`</nobr> |                   | Affichez la base de données qui sera supprimée, mais ne la supprimez pas. |

Les [Options communes](#common-options) sont répertoriées ci-dessus.

## <a name="dotnet-ef-database-update"></a>mise à jour de la base de données dotnet EF

Met à jour la base de données jusqu’à la dernière migration ou à une migration spécifiée.

Arguments :

| Argument                   | Description                                                                                                                                                                                                                                                     |
|:---------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<MIGRATION>`</nobr> | Migration cible. Les migrations peuvent être identifiées par leur nom ou par leur ID. Le nombre 0 est un cas spécial qui signifie *avant la première migration* et entraîne la restauration de toutes les migrations. Si aucune migration n’est spécifiée, la commande prend par défaut la dernière migration. |

Options :

| Option                                    | Description                                                                                                                      |
|:------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------|
|  <nobr>`--connection <CONNECTION>`</nobr> | Chaîne de connexion à la base de données. La valeur par défaut est celle spécifiée dans `AddDbContext` ou `OnConfiguring` . Ajouté dans EF Core 5,0. |

Les [Options communes](#common-options) sont répertoriées ci-dessus.

Les exemples suivants mettent à jour la base de données vers une migration spécifiée. La première utilise le nom de la migration et la seconde utilise l’ID de migration et une connexion spécifiée :

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>informations sur la DbContext dotnet EF

Obtient des informations sur un `DbContext` type.

Les [Options communes](#common-options) sont répertoriées ci-dessus.

## <a name="dotnet-ef-dbcontext-list"></a>liste de DbContext EF dotnet

Répertorie les `DbContext` types disponibles.

Les [Options communes](#common-options) sont répertoriées ci-dessus.

## <a name="dotnet-ef-dbcontext-scaffold"></a>structure de l’DbContext dotnet EF

Génère du code pour les `DbContext` types d’entité et pour une base de données. Pour que cette commande génère un type d’entité, la table de base de données doit avoir une clé primaire.

Arguments :

| Argument                    | Description                                                                                                                                                                                                             |
|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<CONNECTION>`</nobr> | Chaîne de connexion à la base de données. Pour les projets ASP.NET Core 2. x, la valeur peut être *name \<name of connection string> =*. Dans ce cas, le nom provient des sources de configuration qui sont configurées pour le projet. |
| `<PROVIDER>`                | Fournisseur à utiliser. En général, il s’agit du nom du package NuGet, par exemple : `Microsoft.EntityFrameworkCore.SqlServer` .                                                                                           |

Options :

| Option                                   | Court             | Description                                                                                                                                                                    |
|:-----------------------------------------|:------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--data-annotations`                     | <nobr>`-d`</nobr> | Utilisez des attributs pour configurer le modèle (dans la mesure du possible). Si cette option est omise, seule l’API Fluent est utilisée.                                                                |
| `--context <NAME>`                       | `-c`              | Nom de la `DbContext` classe à générer.                                                                                                                                 |
| `--context-dir <PATH>`                   |                   | Répertoire dans lequel placer le `DbContext` fichier de classe. Les chemins d’accès sont relatifs au répertoire du projet. Les espaces de noms sont dérivés des noms de dossiers.                                 |
| `--context-namespace <NAMESPACE>`        |                   | Espace de noms à utiliser pour la `DbContext` classe générée. Remarque : remplace `--namespace` . Ajouté dans EF Core 5,0.                                                                 |
| `--force`                                | `-f`              | Remplacer les fichiers existants.                                                                                                                                                      |
| `--output-dir <PATH>`                    | `-o`              | Répertoire dans lequel placer les fichiers de classe d’entité. Les chemins d’accès sont relatifs au répertoire du projet.                                                                                       |
| `--namespace <NAMESPACE>`                | `-n`              | Espace de noms à utiliser pour toutes les classes générées. La valeur par défaut est générée à partir de l’espace de noms racine et du répertoire de sortie. Ajouté dans EF Core 5,0.                                  |
| <nobr>`--schema <SCHEMA_NAME>...`</nobr> |                   | Schémas des tables pour lesquelles générer des types d’entité. Pour spécifier plusieurs schémas, répétez `--schema` l’opération pour chacun d’entre eux. Si cette option est omise, tous les schémas sont inclus.          |
| `--table <TABLE_NAME>`...                | `-t`              | Tables pour lesquelles générer des types d’entité. Pour spécifier plusieurs tables, répétez `-t` ou `--table` pour chacune d’elles. Si cette option est omise, toutes les tables sont incluses.                |
| `--use-database-names`                   |                   | Utilisez les noms de table et de colonne exactement tels qu’ils apparaissent dans la base de données. Si cette option est omise, les noms de base de données sont modifiés pour être plus conformes aux conventions de style de nom C#. |
| `--no-onconfiguring`                     |                   | Supprime la génération de la `OnConfiguring` méthode dans la `DbContext` classe générée. Ajouté dans EF Core 5,0.                                                                  |
| `--no-pluralize`                         |                   | N’utilisez pas pluraliseur. Ajouté dans EF Core 5,0                                                                                                                                 |

Les [Options communes](#common-options) sont répertoriées ci-dessus.

L’exemple suivant génère la structure de tous les schémas et tables et place les nouveaux fichiers dans le dossier *Models* .

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

L’exemple suivant génère uniquement les tables sélectionnées et crée le contexte dans un dossier distinct avec un nom et un espace de noms spécifiés :

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

L’exemple suivant lit la chaîne de connexion à partir du jeu de configuration du projet à l’aide de l' [outil secret Manager](/aspnet/core/security/app-secrets#secret-manager).

```dotnetcli
dotnet user-secrets set ConnectionStrings.Blogging "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Blogging"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Blogging Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="dotnet-ef-dbcontext-script"></a>script dotnet EF DbContext

Génère un script SQL à partir de DbContext. Ignore les migrations. Ajouté dans EF Core 3,0.

Options :

| Option                         | Court             | Description                      |
| ------------------------------ | ----------------- | -------------------------------- |
| <nobr>`--output <FILE>`</nobr> | <nobr>`-o`</nobr> | Fichier dans lequel écrire le résultat. |

Les [Options communes](#common-options) sont répertoriées ci-dessus.

## <a name="dotnet-ef-migrations-add"></a>ajout des migrations dotnet EF

Ajoute une nouvelle migration.

Arguments :

| Argument              | Description                |
|:----------------------|:---------------------------|
| <nobr>`<NAME>`</nobr> | Nom de la migration. |

Options :

| Option                                 | Court             | Description                                                                                                            |
|:---------------------------------------|:------------------|:-----------------------------------------------------------------------------------------------------------------------|
| `--output-dir <PATH>`                  | <nobr>`-o`</nobr> | Répertoire utilisé pour générer les fichiers. Les chemins d’accès sont relatifs au répertoire du projet cible. La valeur par défaut est « migrations ».   |
| <nobr>`--namespace <NAMESPACE>`</nobr> | `-n`              | Espace de noms à utiliser pour les classes générées. La valeur par défaut est générée à partir du répertoire de sortie. Ajouté dans EF Core 5,0. |

Les [Options communes](#common-options) sont répertoriées ci-dessus.

## <a name="dotnet-ef-migrations-list"></a>Liste des migrations dotnet EF

Répertorie les migrations disponibles.

Options :

| Option                                   | Description                                                                                                                  |
| ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| <nobr>`--connection <CONNECTION>`</nobr> | Chaîne de connexion à la base de données. La valeur par défaut est celle spécifiée dans AddDbContext ou OnConfiguring. Ajouté dans EF Core 5,0. |
| `--no-connect`                           | Ne vous connectez pas à la base de données. Ajouté dans EF Core 5,0.                                                                         |

Les [Options communes](#common-options) sont répertoriées ci-dessus.

## <a name="dotnet-ef-migrations-remove"></a>suppression des migrations dotnet EF

Supprime la dernière migration (restaure les modifications de code qui ont été effectuées pour la migration).

Options :

| Option                 | Court             | Description                                                                     |
|:-----------------------|:------------------|:--------------------------------------------------------------------------------|
| <nobr>`--force`</nobr> | <nobr>`-f`</nobr> | Rétablissez la migration (annulez les modifications qui ont été appliquées à la base de données). |

Les [Options communes](#common-options) sont répertoriées ci-dessus.

## <a name="dotnet-ef-migrations-script"></a>script de migrations dotnet EF

Génère un script SQL à partir des migrations.

Arguments :

| Argument              | Description                                                                                                                                                   |
|:----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<FROM>`</nobr> | Début de la migration. Les migrations peuvent être identifiées par leur nom ou par leur ID. Le nombre 0 est un cas spécial qui signifie *avant la première migration*. La valeur par défaut est 0. |
| `<TO>`                | Fin de la migration. La valeur par défaut est la dernière migration.                                                                                                         |

Options :

| Option                           | Court             | Description                                                        |
|:---------------------------------|:------------------|:-------------------------------------------------------------------|
| `--output <FILE>`                | <nobr>`-o`</nobr> | Fichier dans lequel écrire le script.                                   |
| `--idempotent`                   | `-i`              | Générez un script qui peut être utilisé sur une base de données lors d’une migration. |
| <nobr>`--no-transactions`</nobr> |                   | Ne générez pas d’instructions de transaction SQL. Ajouté dans EF Core 5,0.   |

Les [Options communes](#common-options) sont répertoriées ci-dessus.

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
* [Rétroconception](xref:core/managing-schemas/scaffolding)
