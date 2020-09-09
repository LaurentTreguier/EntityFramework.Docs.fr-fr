---
title: Informations de référence sur les outils de EF Core (console du gestionnaire de package)-EF Core
description: Guide de référence pour le Entity Framework Core console du gestionnaire de package Visual Studio
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 84ca41dc08f7bc813ee9491b66fc91b2854c7632
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617863"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Référence des outils de Entity Framework Core-console du gestionnaire de package dans Visual Studio

Les outils de la console du gestionnaire de package (PMC) pour Entity Framework Core effectuer des tâches de développement au moment du Design. Par exemple, ils créent des [migrations](/aspnet/core/data/ef-mvc/migrations), appliquent des migrations et génèrent du code pour un modèle basé sur une base de données existante. Les commandes s’exécutent dans Visual Studio à l’aide de la [console du gestionnaire de package](/nuget/tools/package-manager-console). Ces outils fonctionnent avec les projets .NET Framework et .NET Core.

Si vous n’utilisez pas Visual Studio, nous vous recommandons d’utiliser les [outils en ligne de commande EF Core à](xref:core/miscellaneous/cli/dotnet) la place. Les outils de CLI .NET Core sont inter-plateformes et s’exécutent dans une invite de commandes.

## <a name="installing-the-tools"></a>Installation des outils

Les procédures d’installation et de mise à jour des outils diffèrent entre ASP.NET Core 2.1 + et les versions antérieures ou d’autres types de projets.

### <a name="aspnet-core-version-21-and-later"></a>ASP.NET Core version 2,1 et versions ultérieures

Les outils sont inclus automatiquement dans un projet ASP.NET Core 2.1 +, car le `Microsoft.EntityFrameworkCore.Tools` package est inclus dans le [AspNetCore](/aspnet/core/fundamentals/metapackage-app).

Par conséquent, vous n’avez rien à faire pour installer les outils, mais vous devez effectuer les opérations suivantes :

* Restaurez les packages avant d’utiliser les outils d’un nouveau projet.
* Installez un package pour mettre à jour les outils vers une version plus récente.

Pour vous assurer que vous obtenez la version la plus récente des outils, nous vous recommandons également d’effectuer les étapes suivantes :

* Modifiez votre fichier *. csproj* et ajoutez une ligne spécifiant la dernière version du package [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) . Par exemple, le fichier *. csproj* peut inclure un `ItemGroup` qui ressemble à ceci :

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

Mettez à jour les outils lorsque vous recevez un message comme dans l’exemple suivant :

> La version des outils de EF Core « 2.1.1-RTM-30846 » est plus ancienne que celle du runtime « 2.1.3-RTM-32065 ». Mettez à jour les outils des dernières fonctionnalités et correctifs de bogues.

Pour mettre à jour les outils :

* Installez le dernier kit SDK .NET Core.
* Effectuez la mise à jour de Visual Studio vers la dernière version.
* Modifiez le fichier *. csproj* afin qu’il inclue une référence de package au package d’outils le plus récent, comme indiqué plus haut.

### <a name="other-versions-and-project-types"></a>Autres types de versions et de projets

Installez les outils de la console du gestionnaire de package en exécutant la commande suivante dans la **console du gestionnaire de package**:

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Mettez à jour les outils en exécutant la commande suivante dans la **console du gestionnaire de package**.

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>Vérifier l’installation

Vérifiez que les outils sont installés en exécutant la commande suivante :

``` powershell
Get-Help about_EntityFrameworkCore
```

La sortie ressemble à ceci (il ne vous indique pas la version des outils que vous utilisez) :

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a>Utilisation des outils

Avant d’utiliser les outils :

* Comprenez la différence entre le projet cible et le projet de démarrage.
* Découvrez comment utiliser les outils avec des bibliothèques de classes .NET Standard.
* Pour les projets ASP.NET Core, définissez l’environnement.

### <a name="target-and-startup-project"></a>Projet de démarrage et cible

Les commandes font référence à un *projet* et à un *projet de démarrage*.

* Le *projet* est également appelé *projet cible* , car il s’agit de l’emplacement où les commandes ajoutent ou suppriment des fichiers. Par défaut, le **projet par défaut** sélectionné dans la **console du gestionnaire de package** est le projet cible. Vous pouvez spécifier un projet différent comme projet cible à l’aide de l' <nobr>`--project`</nobr> option.

* Le *projet de démarrage* est celui que les outils génèrent et exécutent. Les outils doivent exécuter le code de l’application au moment de la conception pour obtenir des informations sur le projet, telles que la chaîne de connexion à la base de données et la configuration du modèle. Par défaut, le **projet de démarrage** dans **Explorateur de solutions** est le projet de démarrage. Vous pouvez spécifier un projet différent comme projet de démarrage à l’aide de l' <nobr>`--startup-project`</nobr> option.

Le projet de démarrage et le projet cible sont souvent le même projet. Un scénario classique dans lequel il s’agit de projets distincts est le suivant :

* Le contexte de EF Core et les classes d’entité se trouvent dans une bibliothèque de classes .NET Core.
* Une application console .NET Core ou une application Web fait référence à la bibliothèque de classes.

Il est également possible de [Placer le code de migrations dans une bibliothèque de classes distincte du contexte de EF Core](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Autres frameworks cibles

Les outils de la console du gestionnaire de package fonctionnent avec .NET Core ou des projets .NET Framework. Les applications qui ont le modèle EF Core dans une bibliothèque de classes .NET Standard peuvent ne pas avoir de projet .NET Core ou .NET Framework. C’est le cas, par exemple, des applications Xamarin et plateforme Windows universelle. Dans ce cas, vous pouvez créer un projet d’application console .NET Core ou .NET Framework dont l’objectif est d’agir comme projet de démarrage pour les outils. Le projet peut être un projet factice sans code réel &mdash; , il n’est nécessaire que pour fournir une cible pour les outils.

Pourquoi un projet factice est-il nécessaire ? Comme mentionné précédemment, les outils doivent exécuter le code de l’application au moment de la conception. Pour ce faire, ils doivent utiliser le Runtime .NET Core ou .NET Framework. Lorsque le modèle de EF Core se trouve dans un projet qui cible .NET Core ou .NET Framework, les outils de EF Core empruntent le runtime du projet. Ils ne peuvent pas le faire si le modèle de EF Core se trouve dans une bibliothèque de classes .NET Standard. Le .NET Standard n’est pas une implémentation .NET réelle. Il s’agit d’une spécification d’un ensemble d’API que les implémentations .NET doivent prendre en charge. Par conséquent, .NET Standard n’est pas suffisant pour que les outils de EF Core exécutent le code d’application. Le projet factice que vous créez à utiliser comme projet de démarrage fournit une plateforme cible concrète dans laquelle les outils peuvent charger la bibliothèque de classes .NET Standard.

### <a name="aspnet-core-environment"></a>Environnement de ASP.NET Core

Pour spécifier l’environnement pour les projets ASP.NET Core, définissez **env : ASPNETCORE_ENVIRONMENT** avant d’exécuter les commandes.

## <a name="common-parameters"></a>Paramètres communs

Le tableau suivant montre les paramètres qui sont communs à toutes les commandes EF Core :

| Paramètre                 | Description                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -Context \<String>        | Classe `DbContext` à utiliser. Nom de classe uniquement ou qualifié complet avec des espaces de noms.  Si ce paramètre est omis, EF Core recherche la classe de contexte. S’il existe plusieurs classes de contexte, ce paramètre est obligatoire. |
| -Projet \<String>        | Projet cible. Si ce paramètre est omis, le **projet par défaut** pour la **console du gestionnaire de package** est utilisé comme projet cible.                                                                             |
| <nobr>-StartupProject</nobr>\<String> | Projet de démarrage. Si ce paramètre est omis, le **projet de démarrage** dans les propriétés de la **solution** est utilisé comme projet cible.                                                                                 |
| -Verbose                  | Affichez la sortie détaillée.                                                                                                                                                                                                 |

Pour afficher des informations d’aide sur une commande, utilisez la commande de PowerShell `Get-Help` .

> [!TIP]
> Les paramètres Context, Project et StartupProject prennent en charge l’expansion de tabulation.

## <a name="add-migration"></a>Ajouter une migration

Ajoute une nouvelle migration.

Paramètres :

| Paramètre                         | Description                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Name \<String><nobr>       | Nom de la migration. Il s’agit d’un paramètre positionnel qui est obligatoire.                                              |
| <nobr>-OutputDir \<String></nobr> | Répertoire utilisé pour générer les fichiers. Les chemins d’accès sont relatifs au répertoire du projet cible. La valeur par défaut est « migrations ». |
| <nobr>-Espace de noms \<String></nobr> | Espace de noms à utiliser pour les classes générées. La valeur par défaut est générée à partir du répertoire de sortie. (Disponible à partir de EFCore 5.0.0 et versions ultérieures.) |

## <a name="drop-database"></a>Supprimer la base de données

Supprime la base de données.

Paramètres :

| Paramètre | Description                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | Affichez la base de données qui sera supprimée, mais ne la supprimez pas. |

## <a name="get-dbcontext"></a>Acquérir-DbContext

Obtient des informations sur un `DbContext` type.

## <a name="remove-migration"></a>Remove-Migration

Supprime la dernière migration (restaure les modifications de code qui ont été effectuées pour la migration).

Paramètres :

| Paramètre | Description                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | Rétablissez la migration (annulez les modifications qui ont été appliquées à la base de données). |

## <a name="scaffold-dbcontext"></a>Génération de modèles automatique-DbContext

Génère du code pour les `DbContext` types d’entité et pour une base de données. Pour `Scaffold-DbContext` que puisse générer un type d’entité, la table de base de données doit avoir une clé primaire.

Paramètres :

| Paramètre                          | Description                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Connexion \<String></nobr> | Chaîne de connexion à la base de données. Pour les projets ASP.NET Core 2. x, la valeur peut être *name \<name of connection string> =*. Dans ce cas, le nom provient des sources de configuration qui sont configurées pour le projet. Il s’agit d’un paramètre positionnel qui est obligatoire. |
| <nobr>-Fournisseur \<String></nobr>   | Fournisseur à utiliser. En général, il s’agit du nom du package NuGet, par exemple : `Microsoft.EntityFrameworkCore.SqlServer` . Il s’agit d’un paramètre positionnel qui est obligatoire.                                                                                           |
| -OutputDir \<String>               | Répertoire dans lequel placer les fichiers. Les chemins d’accès sont relatifs au répertoire du projet.                                                                                                                                                                                             |
| -ContextDir \<String>              | Répertoire dans lequel placer le `DbContext` fichier. Les chemins d’accès sont relatifs au répertoire du projet.                                                                                                                                                               |
| -Espace de noms \<String>               | Espace de noms à utiliser pour toutes les classes générées. La valeur par défaut est générée à partir de l’espace de noms racine et du répertoire de sortie. (Disponible à partir de EFCore 5.0.0 et versions ultérieures.) |
| -L’attribut contextnamespace \<String>        | Espace de noms à utiliser pour la `DbContext` classe générée. Remarque : remplace `-Namespace` . (Disponible à partir de EFCore 5.0.0 et versions ultérieures.) |
| -Context \<String>                 | Nom de la `DbContext` classe à générer.                                                                                                                                                                                                                          |
| -Schémas \<String[]>               | Schémas des tables pour lesquelles générer des types d’entité. Si ce paramètre est omis, tous les schémas sont inclus.                                                                                                                                                             |
| -Tables \<String[]>                | Tables pour lesquelles générer des types d’entité. Si ce paramètre est omis, toutes les tables sont incluses.                                                                                                                                                                         |
| -DataAnnotations                   | Utilisez des attributs pour configurer le modèle (dans la mesure du possible). Si ce paramètre est omis, seule l’API Fluent est utilisée.                                                                                                                                                      |
| -UseDatabaseNames                  | Utilisez les noms de table et de colonne exactement tels qu’ils apparaissent dans la base de données. Si ce paramètre est omis, les noms de base de données sont modifiés pour être plus conformes aux conventions de style de nom C#.                                                                                       |
| -Force                             | Remplacer les fichiers existants.                                                                                                                                                                                                                                               |
| -NoOnConfiguring                   | Supprime la génération de la `OnConfiguring` méthode dans la `DbContext` classe générée. (Disponible à partir de EFCore 5.0.0 et versions ultérieures.) |

Exemple :

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

Exemple qui génère uniquement les tables sélectionnées et crée le contexte dans un dossier distinct avec un nom et un espace de noms spécifiés :

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a>Script-migration

Génère un script SQL qui applique toutes les modifications d’une migration sélectionnée à une autre migration sélectionnée.

Paramètres :

| Paramètre                | Description                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-À partir de*\<String>        | Début de la migration. Les migrations peuvent être identifiées par leur nom ou par leur ID. Le nombre 0 est un cas spécial qui signifie *avant la première migration*. La valeur par défaut est 0.                                                              |
| *-To*\<String>          | Fin de la migration. La valeur par défaut est la dernière migration.                                                                                                                                                                      |
| <nobr>-Idempotent</nobr> | Générez un script qui peut être utilisé sur une base de données lors d’une migration.                                                                                                                                                         |
| -Sortie \<String>        | Fichier dans lequel écrire le résultat. Si ce paramètre est omis, le fichier est créé avec un nom généré dans le même dossier que celui dans lequel les fichiers d’exécution de l’application sont créés, par exemple : */obj/Debug/netcoreapp2.1/ghbkztfz.SQL/*. |

> [!TIP]
> Les paramètres to, from et Output prennent en charge l’expansion de tabulation.

L’exemple suivant crée un script pour la migration InitialCreate, en utilisant le nom de la migration.

```powershell
Script-Migration -To InitialCreate
```

L’exemple suivant crée un script pour toutes les migrations après la migration de InitialCreate, à l’aide de l’ID de migration.

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a>Mettre à jour-base de données

Met à jour la base de données jusqu’à la dernière migration ou à une migration spécifiée.

| Paramètre                           | Description                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-Migration*\<String></nobr> | Migration cible. Les migrations peuvent être identifiées par leur nom ou par leur ID. Le nombre 0 est un cas spécial qui signifie *avant la première migration* et entraîne la restauration de toutes les migrations. Si aucune migration n’est spécifiée, la commande prend par défaut la dernière migration. |
| <nobr>-Connexion \<String></nobr>  | Chaîne de connexion à la base de données. La valeur par défaut est celle spécifiée dans `AddDbContext` ou `OnConfiguring` . |

> [!TIP]
> Le paramètre de migration prend en charge l’expansion de tabulation.

L’exemple suivant rétablit toutes les migrations.

```powershell
Update-Database -Migration 0
```

Les exemples suivants mettent à jour la base de données vers une migration spécifiée. La première utilise le nom de la migration et la seconde utilise l’ID de migration et une connexion spécifiée :

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Migrations](xref:core/managing-schemas/migrations/index)
* [Rétroconception](xref:core/managing-schemas/scaffolding)
