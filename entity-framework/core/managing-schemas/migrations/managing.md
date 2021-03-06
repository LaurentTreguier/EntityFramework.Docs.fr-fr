---
title: Gestion des migrations-EF Core
description: Ajout, suppression et gestion des migrations de schémas de base de données avec Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: ea2bf0e14214369af61bd1aedc932d90fc5f90c5
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106165863"
---
# <a name="managing-migrations"></a>Gestion des migrations

À mesure que votre modèle change, les migrations sont ajoutées et supprimées dans le cadre du développement normal, et les fichiers de migration sont archivés dans le contrôle de code source de votre projet. Pour gérer les migrations, vous devez d’abord installer les [outils en ligne de commande EF Core](xref:core/cli/index).

> [!TIP]
> Si `DbContext` se trouve dans un assembly différent du projet de démarrage, vous pouvez spécifier explicitement les projets cible et de démarrage dans les [outils de la console du gestionnaire de package](xref:core/cli/powershell#target-and-startup-project) ou dans les [outils CLI .NET Core](xref:core/cli/dotnet#target-project-and-startup-project).

## <a name="add-a-migration"></a>Ajouter une migration

Une fois votre modèle modifié, vous pouvez ajouter une migration pour cette modification :

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

Le nom de la migration peut être utilisé comme un message de validation dans un système de gestion de versions. Par exemple, vous pouvez choisir un nom comme *AddBlogCreatedTimestamp* si la modification est une nouvelle `CreatedTimestamp` propriété de votre `Blog` entité.

Trois fichiers sont ajoutés à votre projet sous le répertoire **Migrations** :

* **XXXXXXXXXXXXXX_AddCreatedTimestamp. cs**: fichier de migration principal. Contient les opérations nécessaires à l’application de la migration (dans `Up`) et à sa restauration (dans `Down`).
* **XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. cs**: fichier de métadonnées de migrations. Contient des informations utilisées par EF.
* **MyContextModelSnapshot.cs** : instantané de votre modèle actuel. Permet de déterminer ce qui a changé pendant l’ajout de la migration suivante.

L’horodatage dans le nom des fichiers permet de conserver ces derniers dans l’ordre chronologique et de voir ainsi la progression des modifications.

### <a name="namespaces"></a>Espaces de noms

Vous êtes libre de déplacer les fichiers Migrations et de changer leur espace de noms manuellement. Les migrations sont créées en tant que sœurs de la dernière migration. Vous pouvez également spécifier le répertoire au moment de la génération comme suit :

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --output-dir Your/Directory
```

> [!NOTE]
> Dans EF Core 5,0, vous pouvez également modifier l’espace de noms indépendamment du répertoire à l’aide de `--namespace` .

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -OutputDir Your\Directory
```

> [!NOTE]
> Dans EF Core 5,0, vous pouvez également modifier l’espace de noms indépendamment du répertoire à l’aide de `-Namespace` .

***

## <a name="customize-migration-code"></a>Personnaliser le code de migration

Bien que EF Core crée généralement des migrations précises, vous devez toujours examiner le code et vous assurer qu’il correspond à la modification souhaitée. dans certains cas, il est même nécessaire de le faire.

### <a name="column-renames"></a>Renommages de colonnes

Un exemple notable dans lequel la personnalisation des migrations est nécessaire est lors de l’attribution d’un nouveau nom à une propriété. Par exemple, si vous renommez une propriété de `Name` en `FullName` , EF Core génère la migration suivante :

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

EF Core n’est généralement pas en mesure de savoir quand l’intention est de supprimer une colonne et d’en créer une (deux modifications distinctes) et de renommer une colonne. Si la migration ci-dessus est appliquée telle quelle, tous vos noms de clients seront perdus. Pour renommer une colonne, remplacez la migration générée ci-dessus par ce qui suit :

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> Le processus de structuration de migration vous avertit quand une opération peut entraîner une perte de données (par exemple la suppression d’une colonne). Si cet avertissement s’affiche, veillez particulièrement à examiner si le code de migration est exact.

### <a name="adding-raw-sql"></a>Ajout de SQL brut

L’attribution d’un nouveau nom à une colonne peut être obtenue via une API intégrée, dans de nombreux cas, ce qui n’est pas possible. Par exemple, nous pouvons souhaiter remplacer les `FirstName` Propriétés et existantes `LastName` par une seule nouvelle `FullName` propriété. La migration générée par EF Core sera la suivante :

```csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);
```

Comme précédemment, cela entraînerait une perte de données indésirable. Pour transférer les données des anciennes colonnes, nous réorganisons les migrations et introduisons une opération SQL brute comme suit :

```csharp
migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET FullName = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a>Modifications arbitraires par le biais de SQL brut

Le SQL brut peut également être utilisé pour gérer des objets de base de données qui ne sont pas pris en charge par EF Core. Pour ce faire, ajoutez une migration sans modifier le modèle. une migration vide sera générée, que vous pourrez ensuite remplir avec des opérations SQL brutes.

Par exemple, la migration suivante crée une procédure stockée SQL Server :

```csharp
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

> [!TIP]
> `EXEC` est utilisé lorsqu’une instruction doit être la première ou une seule dans un lot SQL. Il peut également être utilisé pour contourner les erreurs de l’analyseur dans des scripts de migration idempotent qui peuvent se produire lorsque des colonnes référencées n’existent pas actuellement sur une table.

Cela peut être utilisé pour gérer tous les aspects de votre base de données, notamment :

* Procédures stockées
* Recherche en texte intégral
* Fonctions
* Déclencheurs
* Vues

Dans la plupart des cas, EF Core encapsule automatiquement chaque migration dans sa propre transaction lors de l’application des migrations. Malheureusement, certaines opérations de migration ne peuvent pas être effectuées au sein d’une transaction dans certaines bases de données ; dans ce cas, vous pouvez refuser la transaction en passant `suppressTransaction: true` à `migrationBuilder.Sql` .

Si `DbContext` se trouve dans un assembly différent du projet de démarrage, vous pouvez spécifier explicitement les projets cible et de démarrage dans les [outils de la console du gestionnaire de package](xref:core/cli/powershell#target-and-startup-project) ou dans les [outils CLI .NET Core](xref:core/cli/dotnet#target-project-and-startup-project).

## <a name="remove-a-migration"></a>Supprimer une migration

Parfois, vous ajoutez une migration et réalisez que vous devez apporter des modifications supplémentaires à votre modèle EF Core avant de l’appliquer. Pour supprimer la dernière migration, utilisez cette commande.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Remove-Migration
```

***

Après avoir supprimé la migration, vous pouvez apporter les modifications supplémentaires au modèle et la rajouter.

> [!WARNING]
> Évitez de supprimer toutes les migrations qui ont déjà été appliquées aux bases de données de production. Cela signifie que vous ne pourrez pas restaurer ces migrations à partir des bases de données et que vous risquez de rompre les hypothèses formulées par les migrations suivantes.

## <a name="listing-migrations"></a>Liste des migrations

Vous pouvez répertorier toutes les migrations existantes comme suit :

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations list
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

> [!NOTE]
> Cette commande a été introduite dans EF Core 5,0.

```powershell
Get-Migration
```

***

## <a name="resetting-all-migrations"></a>Réinitialisation de toutes les migrations

Dans certains cas extrêmes, il peut être nécessaire de supprimer toutes les migrations et de recommencer. Vous pouvez facilement effectuer cette opération en supprimant votre dossier **migrations** et en supprimant votre base de données. à ce stade, vous pouvez créer une nouvelle migration initiale, qui contiendra l’ensemble de votre schéma actuel.

Il est également possible de réinitialiser toutes les migrations et d’en créer une seule sans perdre vos données. Cela est parfois appelé « débogage » et implique un travail manuel :

* Supprimer votre dossier **migrations**
* Créer une nouvelle migration et générer un script SQL pour celle-ci
* Dans votre base de données, supprimez toutes les lignes de la table de l’historique des migrations.
* Insérez une seule ligne dans l’historique des migrations, pour enregistrer que la première migration a déjà été appliquée, puisque vos tables sont déjà en cours d’exécution. L’instruction SQL INSERT est la dernière opération du script SQL générée ci-dessus.

> [!WARNING]
> Tout [Code de migration personnalisé](#customize-migration-code) est perdu lorsque le dossier **migrations** est supprimé.  Toutes les personnalisations doivent être appliquées manuellement à la nouvelle migration initiale afin d’être conservées.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Entity Framework Core outils de référence-CLI .net Core](xref:core/cli/dotnet) : comprend des commandes pour mettre à jour, supprimer, ajouter, supprimer, etc.
* [Référence des outils de Entity Framework Core-console du gestionnaire de package dans Visual Studio](xref:core/cli/powershell) : comprend des commandes pour mettre à jour, supprimer, ajouter, supprimer, etc.
