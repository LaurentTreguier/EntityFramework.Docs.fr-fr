---
title: Utilisation de migrate.exe-EF6
description: Utilisation de migrate.exe dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/migrate-exe
ms.openlocfilehash: f57f56f7e3fe876c7265526bf6541e3c9e91ca7d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066367"
---
# <a name="using-migrateexe"></a>Utilisation de migrate.exe
Migrations Code First peut être utilisé pour mettre à jour une base de données à partir de Visual Studio, mais elle peut également être exécutée à l’aide de l’outil en ligne de commande migrate.exe. Cette page propose une vue d’ensemble rapide de l’utilisation de migrate.exe pour exécuter des migrations sur une base de données.

> [!NOTE]
> Cet article suppose que vous savez utiliser Migrations Code First dans les scénarios de base. Si ce n’est pas le cas, vous devez lire [migrations code First](xref:ef6/modeling/code-first/migrations/index) avant de continuer.

## <a name="copy-migrateexe"></a>Copier migrate.exe

Quand vous installez Entity Framework à l’aide de NuGet migrate.exe se trouve dans le dossier Tools du package téléchargé. Dans les &lt; packages de dossiers du projet &gt; \\ \\ EntityFramework. &lt; outils de version &gt; \\

Une fois que vous avez migrate.exe, vous devez le copier à l’emplacement de l’assembly qui contient vos migrations.

Si votre application cible .NET 4 et non 4,5, vous devez copier le **Redirect.config** dans l’emplacement et le renommer **migrate.exe.config**. Cela permet à migrate.exe d’obtenir les redirections de liaison correctes pour pouvoir localiser l’assembly Entity Framework.

| .NET 4.5                                      | .NET 4,0                                      |
|:----------------------------------------------|:----------------------------------------------|
| ![Fichiers .NET 4,5](~/ef6/media/net45files.png) | ![Fichiers .NET 4,0](~/ef6/media/net40files.png) |

> [!NOTE]
> migrate.exe ne prend pas en charge les assemblys x64.

Une fois que vous avez déplacé migrate.exe vers le dossier approprié, vous devez être en mesure de l’utiliser pour exécuter des migrations sur la base de données. Tout l’utilitaire est conçu pour exécuter des migrations. Il ne peut pas générer des migrations ou créer un script SQL.

## <a name="see-options"></a>Voir les options

``` console
Migrate.exe /?
```

Le message ci-dessus affiche la page d’aide associée à cet utilitaire. Notez que vous devez disposer de l' EntityFramework.dll dans le même emplacement que celui que vous exécutez migrate.exe pour que cela fonctionne.

## <a name="migrate-to-the-latest-migration"></a>Migrer vers la dernière migration

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile="..\\web.config"
```

Lors de l’exécution de migrate.exe le seul paramètre obligatoire est l’assembly, qui est l’assembly qui contient les migrations que vous essayez d’exécuter, mais qui utilise tous les paramètres conventionnels si vous ne spécifiez pas le fichier de configuration.

## <a name="migrate-to-a-specific-migration"></a>Migrer vers une migration spécifique

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /targetMigration="AddTitle"
```

Si vous souhaitez exécuter des migrations vers une migration spécifique, vous pouvez spécifier le nom de la migration. Cette opération exécute toutes les migrations précédentes si nécessaire, jusqu’à ce que vous obteniez la migration spécifiée.

## <a name="specify-working-directory"></a>Spécifier le répertoire de travail

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /startupDirectory="c:\\MyApp"
```

Si votre assembly a des dépendances ou lit des fichiers par rapport au répertoire de travail, vous devez définir startupDirectory.

## <a name="specify-migration-configuration-to-use"></a>Spécifier la configuration de migration à utiliser

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile="..\\web.config"
```

Si vous disposez de plusieurs classes de configuration de migration, les classes héritent de DbMigrationConfiguration, vous devez spécifier lequel sera utilisé pour cette exécution. Cela est spécifié en fournissant le second paramètre facultatif sans commutateur comme indiqué ci-dessus.

## <a name="provide-connection-string"></a>Fournir la chaîne de connexion

``` console
Migrate.exe BlogDemo.dll /connectionString="Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI" /connectionProviderName="System.Data.SqlClient"
```

Si vous souhaitez spécifier une chaîne de connexion au niveau de la ligne de commande, vous devez également fournir le nom du fournisseur. Si vous ne spécifiez pas le nom du fournisseur, une exception est levée.

## <a name="common-problems"></a>Problèmes courants

| Message d’erreur                                                                                                                                                                                                                                                                                                                      | Solution                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Exception non gérée : System. IO. FileLoadException : impossible de charger le fichier ou l’assembly’EntityFramework, version = 5.0.0.0, culture = neutral, PublicKeyToken = b77a5c561934e089 'ou l’une de ses dépendances. La définition du manifeste de l’assembly trouvé ne correspond pas à la référence de l’assembly. (Exception de HRESULT : 0x80131040)         | Cela signifie généralement que vous exécutez une application .NET 4 sans le fichier Redirect.config. Vous devez copier le Redirect.config au même emplacement que migrate.exe et le renommer en migrate.exe.config.                                                                                       |
| Exception non gérée : System. IO. FileLoadException : impossible de charger le fichier ou l’assembly’EntityFramework, version = 4.4.0.0, culture = neutral, PublicKeyToken = b77a5c561934e089 'ou l’une de ses dépendances. La définition du manifeste de l’assembly trouvé ne correspond pas à la référence de l’assembly. (Exception de HRESULT : 0x80131040)          | Cette exception signifie que vous exécutez une application .NET 4,5 avec le Redirect.config copié à l’emplacement migrate.exe. Si votre application est .NET 4,5, vous n’avez pas besoin d’avoir le fichier de configuration avec les redirections à l’intérieur de. Supprimez le fichier migrate.exe.config.                                    |
| ERREUR : impossible de mettre à jour la base de données pour qu’elle corresponde au modèle actuel, car des modifications sont en attente et la migration automatique est désactivée. Écrivez les modifications de modèle en attente dans une migration basée sur le code ou activez la migration automatique. Affectez la valeur true à DbMigrationsConfiguration. AutomaticMigrationsEnabled pour activer la migration automatique. | Cette erreur se produit en cas d’exécution de la migration lorsque vous n’avez pas créé de migration pour gérer les modifications apportées au modèle et que la base de données ne correspond pas au modèle. L’ajout d’une propriété à une classe de modèle, puis l’exécution de migrate.exe sans créer de migration pour mettre à niveau la base de données en est un exemple. |
| ERREUR : le type n’est pas résolu pour le membre’System. Data. Entity. migrations. Design. ToolingFacade + UpdateRunner, EntityFramework, version = 5.0.0.0, culture = neutral, PublicKeyToken = b77a5c561934e089 '.                                                                                                                                       | Cette erreur peut être causée par la spécification d’un répertoire de démarrage incorrect. Il doit s’agir de l’emplacement de migrate.exe                                                                                                                                                                                      |
| Exception non gérée : System. NullReferenceException : la référence d’objet n’est pas définie sur une instance d’un objet. <br/>   dans System. Data. Entity. migrations. Console. Program. main (String [] args)                                                                                                                                             | Cela peut être dû à la non-spécification d’un paramètre obligatoire pour un scénario que vous utilisez. Par exemple, en spécifiant une chaîne de connexion sans spécifier le nom du fournisseur.                                                                                                                        |
| ERREUR : plus d’un type de configuration de migrations a été trouvé dans l’assembly’ClassLibrary1 '. Spécifiez le nom de l’un à utiliser.                                                                                                                                                                                                  | Comme l’indique l’erreur, il y a plus d’une classe de configuration dans l’assembly donné. Vous devez utiliser le commutateur/configurationType pour spécifier l’utilisation de.                                                                                                                                           |
| ERREUR : impossible de charger le fichier ou l’assembly' &lt; AssemblyName &gt; 'ou l’une de ses dépendances. Le nom ou le code base de l’assembly donné n’est pas valide. (Exception de HRESULT : 0x80131047)                                                                                                                                                    | Cela peut être dû à la spécification d’un nom d’assembly de manière incorrecte ou sans                                                                                                                                                                                                                          |
| ERREUR : impossible de charger le fichier ou l’assembly' &lt; AssemblyName &gt; 'ou l’une de ses dépendances. Tentative de chargement d’un programme au format incorrect.                                                                                                                                                                          | Cela se produit si vous essayez d’exécuter des migrate.exe sur une application x64. EF 5,0 et versions antérieures ne fonctionnent que sur x86.                                                                                                                                                                                |
