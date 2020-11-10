---
title: Gestion des migrations-EF Core
description: Ajout, suppression et gestion des migrations de schémas de base de données avec Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: 81f7cec54510d95b1e2432d56ff95110224fd9bf
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429848"
---
# <a name="managing-migrations"></a><span data-ttu-id="3f834-103">Gestion des migrations</span><span class="sxs-lookup"><span data-stu-id="3f834-103">Managing Migrations</span></span>

<span data-ttu-id="3f834-104">À mesure que votre modèle change, les migrations sont ajoutées et supprimées dans le cadre du développement normal, et les fichiers de migration sont archivés dans le contrôle de code source de votre projet.</span><span class="sxs-lookup"><span data-stu-id="3f834-104">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="3f834-105">Pour gérer les migrations, vous devez d’abord installer les [outils en ligne de commande EF Core](xref:core/cli/index).</span><span class="sxs-lookup"><span data-stu-id="3f834-105">To manage migrations, you must first install the [EF Core command-line tools](xref:core/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="3f834-106">Si `DbContext` se trouve dans un assembly différent du projet de démarrage, vous pouvez spécifier explicitement les projets cible et de démarrage dans les [outils de la console du gestionnaire de package](xref:core/cli/powershell#target-and-startup-project) ou dans les [outils CLI .NET Core](xref:core/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="3f834-106">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="3f834-107">Ajouter une migration</span><span class="sxs-lookup"><span data-stu-id="3f834-107">Add a migration</span></span>

<span data-ttu-id="3f834-108">Une fois votre modèle modifié, vous pouvez ajouter une migration pour cette modification :</span><span class="sxs-lookup"><span data-stu-id="3f834-108">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3f834-109">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f834-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="3f834-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f834-110">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="3f834-111">Le nom de la migration peut être utilisé comme un message de validation dans un système de gestion de versions.</span><span class="sxs-lookup"><span data-stu-id="3f834-111">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="3f834-112">Par exemple, vous pouvez choisir un nom comme *AddBlogCreatedTimestamp* si la modification est une nouvelle `CreatedTimestamp` propriété de votre `Blog` entité.</span><span class="sxs-lookup"><span data-stu-id="3f834-112">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="3f834-113">Trois fichiers sont ajoutés à votre projet sous le répertoire **Migrations** :</span><span class="sxs-lookup"><span data-stu-id="3f834-113">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="3f834-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp. cs** : fichier de migration principal.</span><span class="sxs-lookup"><span data-stu-id="3f834-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs** --The main migrations file.</span></span> <span data-ttu-id="3f834-115">Contient les opérations nécessaires à l’application de la migration (dans `Up`) et à sa restauration (dans `Down`).</span><span class="sxs-lookup"><span data-stu-id="3f834-115">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="3f834-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. cs** : fichier de métadonnées de migrations.</span><span class="sxs-lookup"><span data-stu-id="3f834-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs** --The migrations metadata file.</span></span> <span data-ttu-id="3f834-117">Contient des informations utilisées par EF.</span><span class="sxs-lookup"><span data-stu-id="3f834-117">Contains information used by EF.</span></span>
* <span data-ttu-id="3f834-118">**MyContextModelSnapshot.cs** : instantané de votre modèle actuel.</span><span class="sxs-lookup"><span data-stu-id="3f834-118">**MyContextModelSnapshot.cs** --A snapshot of your current model.</span></span> <span data-ttu-id="3f834-119">Permet de déterminer ce qui a changé pendant l’ajout de la migration suivante.</span><span class="sxs-lookup"><span data-stu-id="3f834-119">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="3f834-120">L’horodatage dans le nom des fichiers permet de conserver ces derniers dans l’ordre chronologique et de voir ainsi la progression des modifications.</span><span class="sxs-lookup"><span data-stu-id="3f834-120">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="3f834-121">Espaces de noms</span><span class="sxs-lookup"><span data-stu-id="3f834-121">Namespaces</span></span>

<span data-ttu-id="3f834-122">Vous êtes libre de déplacer les fichiers Migrations et de changer leur espace de noms manuellement.</span><span class="sxs-lookup"><span data-stu-id="3f834-122">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="3f834-123">Les migrations sont créées en tant que sœurs de la dernière migration.</span><span class="sxs-lookup"><span data-stu-id="3f834-123">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="3f834-124">Vous pouvez également spécifier le répertoire au moment de la génération comme suit :</span><span class="sxs-lookup"><span data-stu-id="3f834-124">Alternatively, you can specify the directory at generation time as follows:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="3f834-125">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f834-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --output-dir Your/Directory
```

> [!NOTE]
> <span data-ttu-id="3f834-126">Dans EF Core 5,0, vous pouvez également modifier l’espace de noms indépendamment du répertoire à l’aide de `--namespace` .</span><span class="sxs-lookup"><span data-stu-id="3f834-126">In EF Core 5.0, you can also change the namespace independently of the directory using `--namespace`.</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="3f834-127">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f834-127">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -OutputDir Your\Directory
```

> [!NOTE]
> <span data-ttu-id="3f834-128">Dans EF Core 5,0, vous pouvez également modifier l’espace de noms indépendamment du répertoire à l’aide de `-Namespace` .</span><span class="sxs-lookup"><span data-stu-id="3f834-128">In EF Core 5.0, you can also change the namespace independently of the directory using `-Namespace`.</span></span>

<span data-ttu-id="3f834-129">\*\*_</span><span class="sxs-lookup"><span data-stu-id="3f834-129">\*\*_</span></span>

## <a name="customize-migration-code"></a><span data-ttu-id="3f834-130">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="3f834-130">Customize migration code</span></span>

<span data-ttu-id="3f834-131">Bien que EF Core crée généralement des migrations précises, vous devez toujours examiner le code et vous assurer qu’il correspond à la modification souhaitée. dans certains cas, il est même nécessaire de le faire.</span><span class="sxs-lookup"><span data-stu-id="3f834-131">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="3f834-132">Renommages de colonnes</span><span class="sxs-lookup"><span data-stu-id="3f834-132">Column renames</span></span>

<span data-ttu-id="3f834-133">Un exemple notable dans lequel la personnalisation des migrations est nécessaire est lors de l’attribution d’un nouveau nom à une propriété.</span><span class="sxs-lookup"><span data-stu-id="3f834-133">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="3f834-134">Par exemple, si vous renommez une propriété de `Name` en `FullName` , EF Core génère la migration suivante :</span><span class="sxs-lookup"><span data-stu-id="3f834-134">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="3f834-135">EF Core n’est généralement pas en mesure de savoir quand l’intention est de supprimer une colonne et d’en créer une (deux modifications distinctes) et de renommer une colonne.</span><span class="sxs-lookup"><span data-stu-id="3f834-135">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="3f834-136">Si la migration ci-dessus est appliquée telle quelle, tous vos noms de clients seront perdus.</span><span class="sxs-lookup"><span data-stu-id="3f834-136">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="3f834-137">Pour renommer une colonne, remplacez la migration générée ci-dessus par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="3f834-137">To rename a column, replace the above generated migration with the following:</span></span>

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="3f834-138">Le processus de structuration de migration vous avertit quand une opération peut entraîner une perte de données (par exemple la suppression d’une colonne).</span><span class="sxs-lookup"><span data-stu-id="3f834-138">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="3f834-139">Si cet avertissement s’affiche, veillez particulièrement à examiner si le code de migration est exact.</span><span class="sxs-lookup"><span data-stu-id="3f834-139">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="3f834-140">Ajout de SQL brut</span><span class="sxs-lookup"><span data-stu-id="3f834-140">Adding raw SQL</span></span>

<span data-ttu-id="3f834-141">L’attribution d’un nouveau nom à une colonne peut être obtenue via une API intégrée, dans de nombreux cas, ce qui n’est pas possible.</span><span class="sxs-lookup"><span data-stu-id="3f834-141">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="3f834-142">Par exemple, nous pouvons souhaiter remplacer les `FirstName` Propriétés et existantes `LastName` par une seule nouvelle `FullName` propriété.</span><span class="sxs-lookup"><span data-stu-id="3f834-142">For example, we may want to replace existing `FirstName` and `LastName` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="3f834-143">La migration générée par EF Core sera la suivante :</span><span class="sxs-lookup"><span data-stu-id="3f834-143">The migration generated by EF Core will be the following:</span></span>

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

<span data-ttu-id="3f834-144">Comme précédemment, cela entraînerait une perte de données indésirable.</span><span class="sxs-lookup"><span data-stu-id="3f834-144">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="3f834-145">Pour transférer les données des anciennes colonnes, nous réorganisons les migrations et introduisons une opération SQL brute comme suit :</span><span class="sxs-lookup"><span data-stu-id="3f834-145">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

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

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="3f834-146">Modifications arbitraires par le biais de SQL brut</span><span class="sxs-lookup"><span data-stu-id="3f834-146">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="3f834-147">Le SQL brut peut également être utilisé pour gérer des objets de base de données qui ne sont pas pris en charge par EF Core.</span><span class="sxs-lookup"><span data-stu-id="3f834-147">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="3f834-148">Pour ce faire, ajoutez une migration sans modifier le modèle. une migration vide sera générée, que vous pourrez ensuite remplir avec des opérations SQL brutes.</span><span class="sxs-lookup"><span data-stu-id="3f834-148">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="3f834-149">Par exemple, la migration suivante crée une procédure stockée SQL Server :</span><span class="sxs-lookup"><span data-stu-id="3f834-149">For example, the following migration creates a SQL Server stored procedure:</span></span>

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
> <span data-ttu-id="3f834-150">`EXEC` est utilisé lorsqu’une instruction doit être la première ou une seule dans un lot SQL.</span><span class="sxs-lookup"><span data-stu-id="3f834-150">`EXEC` is used when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="3f834-151">Il peut également être utilisé pour contourner les erreurs de l’analyseur dans des scripts de migration idempotent qui peuvent se produire lorsque des colonnes référencées n’existent pas actuellement sur une table.</span><span class="sxs-lookup"><span data-stu-id="3f834-151">It can also be used to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="3f834-152">Cela peut être utilisé pour gérer tous les aspects de votre base de données, notamment :</span><span class="sxs-lookup"><span data-stu-id="3f834-152">This can be used to manage any aspect of your database, including:</span></span>

<span data-ttu-id="3f834-153">_ Procédures stockées</span><span class="sxs-lookup"><span data-stu-id="3f834-153">_ Stored procedures</span></span>
* <span data-ttu-id="3f834-154">Recherche en texte intégral</span><span class="sxs-lookup"><span data-stu-id="3f834-154">Full-Text Search</span></span>
* <span data-ttu-id="3f834-155">Fonctions</span><span class="sxs-lookup"><span data-stu-id="3f834-155">Functions</span></span>
* <span data-ttu-id="3f834-156">Déclencheurs</span><span class="sxs-lookup"><span data-stu-id="3f834-156">Triggers</span></span>
* <span data-ttu-id="3f834-157">Vues</span><span class="sxs-lookup"><span data-stu-id="3f834-157">Views</span></span>

<span data-ttu-id="3f834-158">Dans la plupart des cas, EF Core encapsule automatiquement chaque migration dans sa propre transaction lors de l’application des migrations.</span><span class="sxs-lookup"><span data-stu-id="3f834-158">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="3f834-159">Malheureusement, certaines opérations de migration ne peuvent pas être effectuées au sein d’une transaction dans certaines bases de données ; dans ce cas, vous pouvez refuser la transaction en passant `suppressTransaction: true` à `migrationBuilder.Sql` .</span><span class="sxs-lookup"><span data-stu-id="3f834-159">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="3f834-160">Si `DbContext` se trouve dans un assembly différent du projet de démarrage, vous pouvez spécifier explicitement les projets cible et de démarrage dans les [outils de la console du gestionnaire de package](xref:core/cli/powershell#target-and-startup-project) ou dans les [outils CLI .NET Core](xref:core/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="3f834-160">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="3f834-161">Supprimer une migration</span><span class="sxs-lookup"><span data-stu-id="3f834-161">Remove a migration</span></span>

<span data-ttu-id="3f834-162">Parfois, vous ajoutez une migration et réalisez que vous devez apporter des modifications supplémentaires à votre modèle EF Core avant de l’appliquer.</span><span class="sxs-lookup"><span data-stu-id="3f834-162">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="3f834-163">Pour supprimer la dernière migration, utilisez cette commande.</span><span class="sxs-lookup"><span data-stu-id="3f834-163">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3f834-164">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f834-164">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="3f834-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f834-165">Visual Studio</span></span>](#tab/vs)

```powershell
Remove-Migration
```

***

<span data-ttu-id="3f834-166">Après avoir supprimé la migration, vous pouvez apporter les modifications supplémentaires au modèle et la rajouter.</span><span class="sxs-lookup"><span data-stu-id="3f834-166">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="3f834-167">Veillez à ne pas supprimer les migrations qui sont déjà appliquées aux bases de données de production.</span><span class="sxs-lookup"><span data-stu-id="3f834-167">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="3f834-168">Si vous ne le faites pas, vous ne pourrez pas le restaurer et risquez de rompre les hypothèses formulées par les migrations suivantes.</span><span class="sxs-lookup"><span data-stu-id="3f834-168">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="3f834-169">Liste des migrations</span><span class="sxs-lookup"><span data-stu-id="3f834-169">Listing migrations</span></span>

<span data-ttu-id="3f834-170">Vous pouvez répertorier toutes les migrations existantes comme suit :</span><span class="sxs-lookup"><span data-stu-id="3f834-170">You can list all existing migrations as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3f834-171">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f834-171">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations list
```

### <a name="visual-studio"></a>[<span data-ttu-id="3f834-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f834-172">Visual Studio</span></span>](#tab/vs)

> [!NOTE]
> <span data-ttu-id="3f834-173">Cette commande a été ajoutée dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="3f834-173">This command was added in EF Core 5.0.</span></span>

```powershell
Get-Migration
```

<span data-ttu-id="3f834-174">\*\*_</span><span class="sxs-lookup"><span data-stu-id="3f834-174">\*\*_</span></span>

## <a name="resetting-all-migrations"></a><span data-ttu-id="3f834-175">Réinitialisation de toutes les migrations</span><span class="sxs-lookup"><span data-stu-id="3f834-175">Resetting all migrations</span></span>

<span data-ttu-id="3f834-176">Dans certains cas extrêmes, il peut être nécessaire de supprimer toutes les migrations et de recommencer.</span><span class="sxs-lookup"><span data-stu-id="3f834-176">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="3f834-177">Vous pouvez facilement effectuer cette opération en supprimant votre dossier _ *migrations* \* et en supprimant votre base de données. à ce stade, vous pouvez créer une nouvelle migration initiale, qui contiendra l’ensemble du schéma actuel.</span><span class="sxs-lookup"><span data-stu-id="3f834-177">This can be easily done by deleting your _ *Migrations* \* folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="3f834-178">Il est également possible de réinitialiser toutes les migrations et d’en créer une seule sans perdre vos données.</span><span class="sxs-lookup"><span data-stu-id="3f834-178">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="3f834-179">Cela est parfois appelé « débogage » et implique un travail manuel :</span><span class="sxs-lookup"><span data-stu-id="3f834-179">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="3f834-180">Supprimer votre dossier **migrations**</span><span class="sxs-lookup"><span data-stu-id="3f834-180">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="3f834-181">Créer une nouvelle migration et générer un script SQL pour celle-ci</span><span class="sxs-lookup"><span data-stu-id="3f834-181">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="3f834-182">Dans votre base de données, supprimez toutes les lignes de la table de l’historique des migrations.</span><span class="sxs-lookup"><span data-stu-id="3f834-182">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="3f834-183">Insérez une seule ligne dans l’historique des migrations, pour enregistrer que la première migration a déjà été appliquée, puisque vos tables sont déjà en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="3f834-183">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="3f834-184">L’instruction SQL INSERT est la dernière opération du script SQL générée ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="3f834-184">The insert SQL is the last operation in the SQL script generated above.</span></span>

> [!WARNING]
> <span data-ttu-id="3f834-185">Tout [Code de migration personnalisé](#customize-migration-code) est perdu lorsque le dossier **migrations** est supprimé.</span><span class="sxs-lookup"><span data-stu-id="3f834-185">Any [custom migration code](#customize-migration-code) will be lost when the **Migrations** folder is deleted.</span></span>  <span data-ttu-id="3f834-186">Toutes les personnalisations doivent être appliquées manuellement à la nouvelle migration initiale afin d’être conservées.</span><span class="sxs-lookup"><span data-stu-id="3f834-186">Any customizations must be applied to the new initial migration manually in order to be preserved.</span></span>
