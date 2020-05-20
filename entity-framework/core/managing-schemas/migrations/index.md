---
title: Migrations - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: c87864b3430d3cd42729c13ddde33c0cd9de9308
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672988"
---
# <a name="migrations"></a>Migrations

Un modèle de données change au cours du développement et perd sa synchronisation avec la base de données. Vous pouvez supprimer la base de données et laisser EF en créer une qui correspond au modèle, mais cette procédure entraîne la perte de données. La fonctionnalité de migration dans EF Core permet de mettre à jour de manière incrémentielle le schéma de la base de données pour qu’il reste synchronisé avec le modèle de données de l’application tout en conservant les données existantes dans la base de données.

Les migrations incluent des outils en ligne de commande et des API qui aident à effectuer les tâches suivantes :

* [Créer une migration](#create-a-migration). Générez du code qui peut mettre à jour la base de données pour la synchroniser avec un ensemble de modifications du modèle.
* [Mettre à jour la base de données](#update-the-database). Appliquez des migrations en attente pour mettre à jour le schéma de base de données.
* [Personnaliser le code de migration](#customize-migration-code). Le code généré doit parfois être modifié ou complété.
* [Supprimer une migration](#remove-a-migration). Supprimez le code généré.
* [Rétablir une migration](#revert-a-migration). Annulez les modifications apportées à la base de données.
* [Générer des scripts SQL](#generate-sql-scripts). Vous aurez peut-être besoin d’un script pour mettre à jour une base de données de production ou pour résoudre des problèmes liés au code de migration.
* [Appliquer des migrations au moment de l’exécution](#apply-migrations-at-runtime). Quand les mises à jour au moment du design et l’exécution de scripts ne sont pas les meilleures options, appelez la méthode `Migrate()`.

> [!TIP]
> Si `DbContext` se trouve dans un assembly différent du projet de démarrage, vous pouvez spécifier explicitement les projets cible et de démarrage dans les [outils de la console du gestionnaire de package](xref:core/miscellaneous/cli/powershell#target-and-startup-project) ou dans les [outils CLI .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).

## <a name="install-the-tools"></a>Installer les outils

Installez les [outils en ligne de commande](xref:core/miscellaneous/cli/index) :

* Pour Visual Studio, nous vous recommandons les [outils de la Console du Gestionnaire de package](xref:core/miscellaneous/cli/powershell).
* Pour d’autres environnements de développement, choisissez les [outils CLI .NET Core](xref:core/miscellaneous/cli/dotnet).

## <a name="create-a-migration"></a>Créer une migration

Une fois que vous avez [défini votre modèle initial](xref:core/modeling/index), il est temps de créer la base de données. Pour ajouter une migration initiale, exécutez la commande suivante.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

Trois fichiers sont ajoutés à votre projet sous le répertoire **Migrations** :

* **XXXXXXXXXXXXXX_InitialCreate.cs** : fichier principal des migrations. Contient les opérations nécessaires à l’application de la migration (dans `Up()`) et à sa restauration (dans `Down()`).
* **XXXXXXXXXXXXXX_InitialCreate.Designer.cs** : fichier de métadonnées des migrations. Contient des informations utilisées par EF.
* **MyContextModelSnapshot.cs** : instantané de votre modèle actuel. Permet de déterminer ce qui a changé pendant l’ajout de la migration suivante.

L’horodatage dans le nom des fichiers permet de conserver ces derniers dans l’ordre chronologique et de voir ainsi la progression des modifications.

### <a name="namespaces"></a>Espaces de noms

Vous êtes libre de déplacer les fichiers Migrations et de changer leur espace de noms manuellement. Les migrations sont créées en tant que sœurs de la dernière migration.

Vous pouvez aussi utiliser `-Namespace` (console du gestionnaire de package) ou `--namespace` ( CLI .NET Core) pour spécifier l’espace de noms au moment de la génération.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="update-the-database"></a>Mettre à jour la base de données

Ensuite, appliquez la migration à la base de données pour créer le schéma.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

## <a name="customize-migration-code"></a>Personnaliser le code de migration

Une fois votre modèle EF Core modifié, le schéma de base de données risque de ne plus être synchronisé. Pour le mettre à jour, ajoutez une autre migration. Le nom de la migration peut être utilisé comme un message de validation dans un système de gestion de versions. Par exemple, vous pouvez choisir un nom tel que *AjouterÉvaluationsProduit* si la modification est une nouvelle classe d’entité pour les évaluations.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddProductReviews
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddProductReviews
```

***

Une fois que la migration a été structurée (que du code a été généré pour elle), vérifiez si le code est exact et ajoutez, supprimez ou modifiez toutes les opérations nécessaires pour pouvoir l’appliquer correctement.

Par exemple, une migration peut contenir les opérations suivantes :

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

Bien que ces opérations rendent le schéma de base de données compatible, elles ne conservent pas les noms de client existants. Pour l’améliorer, réécrivez la migration comme suit.

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

> [!TIP]
> Le processus de structuration de migration vous avertit quand une opération peut entraîner une perte de données (par exemple la suppression d’une colonne). Si cet avertissement s’affiche, veillez particulièrement à examiner si le code de migration est exact.

Appliquez la migration à la base de données à l’aide de la commande appropriée.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

### <a name="empty-migrations"></a>Migrations vides

Il est parfois utile d’ajouter une migration sans apporter de modification au modèle. Dans ce cas, l’ajout d’une nouvelle migration crée des fichiers de code avec des classes vides. Vous pouvez personnaliser cette migration pour effectuer des opérations qui ne sont pas directement liées au modèle EF Core. Voici quelques exemples de ce que vous pouvez gérer de cette façon :

* Recherche en texte intégral
* Fonctions
* Procédures stockées
* Déclencheurs
* Affichages

## <a name="remove-a-migration"></a>Supprimer une migration

Parfois, vous ajoutez une migration et réalisez que vous devez apporter des modifications supplémentaires à votre modèle EF Core avant de l’appliquer. Pour supprimer la dernière migration, utilisez cette commande.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Remove-Migration
```

***

Après avoir supprimé la migration, vous pouvez apporter les modifications supplémentaires au modèle et la rajouter.

## <a name="revert-a-migration"></a>Rétablir une migration

Si vous avez déjà appliqué une migration (ou plusieurs migrations) à la base de données, mais que vous devez la restaurer, vous pouvez utiliser la même commande que celle servant à appliquer des migrations, mais en spécifiant le nom de la migration à restaurer.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update LastGoodMigration
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database LastGoodMigration
```

***

## <a name="generate-sql-scripts"></a>Générer des scripts SQL

Quand vous déboguez vos migrations ou que vous les déployez sur une base de données de production, il est utile de générer un script SQL. Vous pouvez ensuite revoir le script et l’affiner en fonction des besoins d’une base de données de production. Vous pouvez également utiliser le script conjointement avec une technologie de déploiement. La commande de base est la suivante.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>Utilisation de base
```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>Avec From (To implicite)
Génère un script SQL à partir de cette migration vers la dernière migration.
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a>Avec From et To
Génère un script SQL à partir de la migration `from` vers la migration `to` spécifiée.
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
Vous pouvez utiliser un `from` plus récent que le `to` afin de générer un script de restauration. *Prenez note des scénarios de pertes de données potentielles.*

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>Utilisation de base
``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>Avec From (To implicite)
Génère un script SQL à partir de cette migration vers la dernière migration.
```powershell
Script-Migration 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a>Avec From et To
Génère un script SQL à partir de la migration `from` vers la migration `to` spécifiée.
```powershell
Script-Migration 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
Vous pouvez utiliser un `from` plus récent que le `to` afin de générer un script de restauration. *Prenez note des scénarios de pertes de données potentielles.*

***

Il existe plusieurs options pour cette commande.

La migration **from** doit être la dernière migration appliquée à la base de données avant l’exécution du script. Si aucune migration n’a été appliquée, spécifiez `0` (il s’agit de la valeur par défaut).

La migration **to** est la dernière migration à appliquer à la base de données après l’exécution du script. Par défaut, il s’agit de la dernière migration dans votre projet.

Un script **idempotent** peut également être généré. Ce script n’applique les migrations que si elles n’ont pas déjà été appliquées à la base de données. Cela est utile si vous ne savez pas exactement ce que la dernière migration a appliqué à la base de données ou si vous effectuez un déploiement sur plusieurs bases de données pouvant chacune être liée à une migration différente.

## <a name="apply-migrations-at-runtime"></a>Appliquer des migrations au moment de l’exécution

Certaines applications sont susceptibles d’appliquer des migrations au moment de l’exécution (au démarrage ou à la première exécution). Ces opérations nécessitent l’utilisation de la méthode `Migrate()`.

Cette méthode s’appuie sur le service `IMigrator`, qui peut être utilisé pour des scénarios plus avancés. Utilisez `myDbContext.GetInfrastructure().GetService<IMigrator>()` pour y accéder.

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
>
> * Cette approche ne s’adresse pas à tout un chacun. Bien qu’elle soit idéale pour les applications avec une base de données locale, la plupart des applications nécessitent une stratégie de déploiement plus robuste, telle que la génération de scripts SQL.
> * N’appelez pas `EnsureCreated()` avant `Migrate()`. `EnsureCreated()` ignore Migrations pour créer le schéma, ce qui entraîne l’échec de `Migrate()`.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez <xref:core/miscellaneous/cli/index>.
