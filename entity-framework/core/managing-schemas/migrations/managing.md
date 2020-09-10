---
title: Gestion des migrations-EF Core
description: Ajout, suppression et gestion des migrations de schémas de base de données avec Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: 366824cecab57a0f1744fa58cc12e5d3f6675723
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617960"
---
# <a name="managing-migrations"></a><span data-ttu-id="6959b-103">Gestion des migrations</span><span class="sxs-lookup"><span data-stu-id="6959b-103">Managing Migrations</span></span>

<span data-ttu-id="6959b-104">À mesure que votre modèle change, les migrations sont ajoutées et supprimées dans le cadre du développement normal, et les fichiers de migration sont archivés dans le contrôle de code source de votre projet.</span><span class="sxs-lookup"><span data-stu-id="6959b-104">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="6959b-105">Pour gérer les migrations, vous devez d’abord installer les [outils en ligne de commande EF Core](xref:core/miscellaneous/cli/index).</span><span class="sxs-lookup"><span data-stu-id="6959b-105">To manage migrations, you must first install the [EF Core command-line tools](xref:core/miscellaneous/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="6959b-106">Si `DbContext` se trouve dans un assembly différent du projet de démarrage, vous pouvez spécifier explicitement les projets cible et de démarrage dans les [outils de la console du gestionnaire de package](xref:core/miscellaneous/cli/powershell#target-and-startup-project) ou dans les [outils CLI .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="6959b-106">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="6959b-107">Ajouter une migration</span><span class="sxs-lookup"><span data-stu-id="6959b-107">Add a migration</span></span>

<span data-ttu-id="6959b-108">Une fois votre modèle modifié, vous pouvez ajouter une migration pour cette modification :</span><span class="sxs-lookup"><span data-stu-id="6959b-108">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="6959b-109">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="6959b-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="6959b-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6959b-110">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="6959b-111">Le nom de la migration peut être utilisé comme un message de validation dans un système de gestion de versions.</span><span class="sxs-lookup"><span data-stu-id="6959b-111">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="6959b-112">Par exemple, vous pouvez choisir un nom comme *AddBlogCreatedTimestamp* si la modification est une nouvelle `CreatedTimestamp` propriété de votre `Blog` entité.</span><span class="sxs-lookup"><span data-stu-id="6959b-112">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="6959b-113">Trois fichiers sont ajoutés à votre projet sous le répertoire **Migrations** :</span><span class="sxs-lookup"><span data-stu-id="6959b-113">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="6959b-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp. cs**: fichier de migration principal.</span><span class="sxs-lookup"><span data-stu-id="6959b-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs**--The main migrations file.</span></span> <span data-ttu-id="6959b-115">Contient les opérations nécessaires à l’application de la migration (dans `Up`) et à sa restauration (dans `Down`).</span><span class="sxs-lookup"><span data-stu-id="6959b-115">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="6959b-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. cs**: fichier de métadonnées de migrations.</span><span class="sxs-lookup"><span data-stu-id="6959b-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="6959b-117">Contient des informations utilisées par EF.</span><span class="sxs-lookup"><span data-stu-id="6959b-117">Contains information used by EF.</span></span>
* <span data-ttu-id="6959b-118">**MyContextModelSnapshot.cs** : instantané de votre modèle actuel.</span><span class="sxs-lookup"><span data-stu-id="6959b-118">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="6959b-119">Permet de déterminer ce qui a changé pendant l’ajout de la migration suivante.</span><span class="sxs-lookup"><span data-stu-id="6959b-119">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="6959b-120">L’horodatage dans le nom des fichiers permet de conserver ces derniers dans l’ordre chronologique et de voir ainsi la progression des modifications.</span><span class="sxs-lookup"><span data-stu-id="6959b-120">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="6959b-121">Espaces de noms</span><span class="sxs-lookup"><span data-stu-id="6959b-121">Namespaces</span></span>

<span data-ttu-id="6959b-122">Vous êtes libre de déplacer les fichiers Migrations et de changer leur espace de noms manuellement.</span><span class="sxs-lookup"><span data-stu-id="6959b-122">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="6959b-123">Les migrations sont créées en tant que sœurs de la dernière migration.</span><span class="sxs-lookup"><span data-stu-id="6959b-123">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="6959b-124">Vous pouvez également spécifier l’espace de noms au moment de la génération comme suit :</span><span class="sxs-lookup"><span data-stu-id="6959b-124">Alternatively, you can specify the namespace at generation time as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="6959b-125">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="6959b-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="6959b-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6959b-126">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="customize-migration-code"></a><span data-ttu-id="6959b-127">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="6959b-127">Customize migration code</span></span>

<span data-ttu-id="6959b-128">Bien que EF Core crée généralement des migrations précises, vous devez toujours examiner le code et vous assurer qu’il correspond à la modification souhaitée. dans certains cas, il est même nécessaire de le faire.</span><span class="sxs-lookup"><span data-stu-id="6959b-128">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="6959b-129">Renommages de colonnes</span><span class="sxs-lookup"><span data-stu-id="6959b-129">Column renames</span></span>

<span data-ttu-id="6959b-130">Un exemple notable dans lequel la personnalisation des migrations est nécessaire est lors de l’attribution d’un nouveau nom à une propriété.</span><span class="sxs-lookup"><span data-stu-id="6959b-130">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="6959b-131">Par exemple, si vous renommez une propriété de `Name` en `FullName` , EF Core génère la migration suivante :</span><span class="sxs-lookup"><span data-stu-id="6959b-131">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```c#
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="6959b-132">EF Core n’est généralement pas en mesure de savoir quand l’intention est de supprimer une colonne et d’en créer une (deux modifications distinctes) et de renommer une colonne.</span><span class="sxs-lookup"><span data-stu-id="6959b-132">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="6959b-133">Si la migration ci-dessus est appliquée telle quelle, tous vos noms de clients seront perdus.</span><span class="sxs-lookup"><span data-stu-id="6959b-133">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="6959b-134">Pour renommer une colonne, remplacez la migration générée ci-dessus par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="6959b-134">To rename a column, replace the above generated migration with the following:</span></span>

```c#
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="6959b-135">Le processus de structuration de migration vous avertit quand une opération peut entraîner une perte de données (par exemple la suppression d’une colonne).</span><span class="sxs-lookup"><span data-stu-id="6959b-135">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="6959b-136">Si cet avertissement s’affiche, veillez particulièrement à examiner si le code de migration est exact.</span><span class="sxs-lookup"><span data-stu-id="6959b-136">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="6959b-137">Ajout de SQL brut</span><span class="sxs-lookup"><span data-stu-id="6959b-137">Adding raw SQL</span></span>

<span data-ttu-id="6959b-138">L’attribution d’un nouveau nom à une colonne peut être obtenue via une API intégrée, dans de nombreux cas, ce qui n’est pas possible.</span><span class="sxs-lookup"><span data-stu-id="6959b-138">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="6959b-139">Par exemple, nous pouvons souhaiter remplacer les `FirstName` Propriétés et existantes `LastName` par une seule nouvelle `FullName` propriété.</span><span class="sxs-lookup"><span data-stu-id="6959b-139">For example, we may want to replace existing `FirstName` and `LastName` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="6959b-140">La migration générée par EF Core sera la suivante :</span><span class="sxs-lookup"><span data-stu-id="6959b-140">The migration generated by EF Core will be the following:</span></span>

``` csharp
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

<span data-ttu-id="6959b-141">Comme précédemment, cela entraînerait une perte de données indésirable.</span><span class="sxs-lookup"><span data-stu-id="6959b-141">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="6959b-142">Pour transférer les données des anciennes colonnes, nous réorganisons les migrations et introduisons une opération SQL brute comme suit :</span><span class="sxs-lookup"><span data-stu-id="6959b-142">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

``` csharp
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

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="6959b-143">Modifications arbitraires par le biais de SQL brut</span><span class="sxs-lookup"><span data-stu-id="6959b-143">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="6959b-144">Le SQL brut peut également être utilisé pour gérer des objets de base de données qui ne sont pas pris en charge par EF Core.</span><span class="sxs-lookup"><span data-stu-id="6959b-144">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="6959b-145">Pour ce faire, ajoutez une migration sans modifier le modèle. une migration vide sera générée, que vous pourrez ensuite remplir avec des opérations SQL brutes.</span><span class="sxs-lookup"><span data-stu-id="6959b-145">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="6959b-146">Par exemple, la migration suivante crée une procédure stockée SQL Server :</span><span class="sxs-lookup"><span data-stu-id="6959b-146">For example, the following migration creates a SQL Server stored procedure:</span></span>

```c#
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

<span data-ttu-id="6959b-147">Cela peut être utilisé pour gérer tous les aspects de votre base de données, notamment :</span><span class="sxs-lookup"><span data-stu-id="6959b-147">This can be used to manage any aspect of your database, including:</span></span>

* <span data-ttu-id="6959b-148">Procédures stockées</span><span class="sxs-lookup"><span data-stu-id="6959b-148">Stored procedures</span></span>
* <span data-ttu-id="6959b-149">Recherche en texte intégral</span><span class="sxs-lookup"><span data-stu-id="6959b-149">Full-Text Search</span></span>
* <span data-ttu-id="6959b-150">Fonctions</span><span class="sxs-lookup"><span data-stu-id="6959b-150">Functions</span></span>
* <span data-ttu-id="6959b-151">Déclencheurs</span><span class="sxs-lookup"><span data-stu-id="6959b-151">Triggers</span></span>
* <span data-ttu-id="6959b-152">Vues</span><span class="sxs-lookup"><span data-stu-id="6959b-152">Views</span></span>

<span data-ttu-id="6959b-153">Dans la plupart des cas, EF Core encapsule automatiquement chaque migration dans sa propre transaction lors de l’application des migrations.</span><span class="sxs-lookup"><span data-stu-id="6959b-153">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="6959b-154">Malheureusement, certaines opérations de migration ne peuvent pas être effectuées au sein d’une transaction dans certaines bases de données ; dans ce cas, vous pouvez refuser la transaction en passant `suppressTransaction: true` à `migrationBuilder.Sql` .</span><span class="sxs-lookup"><span data-stu-id="6959b-154">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="6959b-155">Si `DbContext` se trouve dans un assembly différent du projet de démarrage, vous pouvez spécifier explicitement les projets cible et de démarrage dans les [outils de la console du gestionnaire de package](xref:core/miscellaneous/cli/powershell#target-and-startup-project) ou dans les [outils CLI .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="6959b-155">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="6959b-156">Supprimer une migration</span><span class="sxs-lookup"><span data-stu-id="6959b-156">Remove a migration</span></span>

<span data-ttu-id="6959b-157">Parfois, vous ajoutez une migration et réalisez que vous devez apporter des modifications supplémentaires à votre modèle EF Core avant de l’appliquer.</span><span class="sxs-lookup"><span data-stu-id="6959b-157">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="6959b-158">Pour supprimer la dernière migration, utilisez cette commande.</span><span class="sxs-lookup"><span data-stu-id="6959b-158">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="6959b-159">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="6959b-159">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="6959b-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6959b-160">Visual Studio</span></span>](#tab/vs)

``` powershell
Remove-Migration
```

***

<span data-ttu-id="6959b-161">Après avoir supprimé la migration, vous pouvez apporter les modifications supplémentaires au modèle et la rajouter.</span><span class="sxs-lookup"><span data-stu-id="6959b-161">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="6959b-162">Veillez à ne pas supprimer les migrations qui sont déjà appliquées aux bases de données de production.</span><span class="sxs-lookup"><span data-stu-id="6959b-162">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="6959b-163">Si vous ne le faites pas, vous ne pourrez pas le restaurer et risquez de rompre les hypothèses formulées par les migrations suivantes.</span><span class="sxs-lookup"><span data-stu-id="6959b-163">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="6959b-164">Liste des migrations</span><span class="sxs-lookup"><span data-stu-id="6959b-164">Listing migrations</span></span>

<span data-ttu-id="6959b-165">Vous pouvez répertorier toutes les migrations existantes comme suit :</span><span class="sxs-lookup"><span data-stu-id="6959b-165">You can list all existing migrations as follows:</span></span>

```dotnetcli
dotnet ef migrations list
```

## <a name="resetting-all-migrations"></a><span data-ttu-id="6959b-166">Réinitialisation de toutes les migrations</span><span class="sxs-lookup"><span data-stu-id="6959b-166">Resetting all migrations</span></span>

<span data-ttu-id="6959b-167">Dans certains cas extrêmes, il peut être nécessaire de supprimer toutes les migrations et de recommencer.</span><span class="sxs-lookup"><span data-stu-id="6959b-167">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="6959b-168">Vous pouvez facilement effectuer cette opération en supprimant votre dossier **migrations** et en supprimant votre base de données. à ce stade, vous pouvez créer une nouvelle migration initiale, qui contiendra l’ensemble du schéma actuel.</span><span class="sxs-lookup"><span data-stu-id="6959b-168">This can be easily done by deleting your **Migrations** folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="6959b-169">Il est également possible de réinitialiser toutes les migrations et d’en créer une seule sans perdre vos données.</span><span class="sxs-lookup"><span data-stu-id="6959b-169">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="6959b-170">Cela est parfois appelé « débogage » et implique un travail manuel :</span><span class="sxs-lookup"><span data-stu-id="6959b-170">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="6959b-171">Supprimer votre dossier **migrations**</span><span class="sxs-lookup"><span data-stu-id="6959b-171">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="6959b-172">Créer une nouvelle migration et générer un script SQL pour celle-ci</span><span class="sxs-lookup"><span data-stu-id="6959b-172">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="6959b-173">Dans votre base de données, supprimez toutes les lignes de la table de l’historique des migrations.</span><span class="sxs-lookup"><span data-stu-id="6959b-173">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="6959b-174">Insérez une seule ligne dans l’historique des migrations, pour enregistrer que la première migration a déjà été appliquée, puisque vos tables sont déjà en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="6959b-174">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="6959b-175">L’instruction INSERT SEQL est la dernière opération du script SQL générée ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="6959b-175">The insert SEQL is the last operation in the SQL script generated above.</span></span>
