---
title: Migrations - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: b94ac567644a9d98a05a40857cc072c500203370
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562557"
---
<a name="migrations"></a><span data-ttu-id="c8e8a-102">Migrations</span><span class="sxs-lookup"><span data-stu-id="c8e8a-102">Migrations</span></span>
==========

<span data-ttu-id="c8e8a-103">Un modèle de données change au cours du développement et perd sa synchronisation avec la base de données.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-103">A data model changes during development and gets out of sync with the database.</span></span> <span data-ttu-id="c8e8a-104">Vous pouvez supprimer la base de données et laisser EF en créer une qui correspond au modèle, mais cette procédure entraîne la perte de données.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-104">You can drop the database and let EF create a new one that matches the model, but this procedure results in the loss of data.</span></span> <span data-ttu-id="c8e8a-105">La fonctionnalité de migration dans EF Core permet de mettre à jour de manière incrémentielle le schéma de la base de données pour qu’il reste synchronisé avec le modèle de données de l’application tout en conservant les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="c8e8a-106">Les migrations incluent des outils en ligne de commande et des API qui aident à effectuer les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c8e8a-106">Migrations includes command-line tools and APIs that help with the following tasks:</span></span>

* <span data-ttu-id="c8e8a-107">[Créer une migration](#create-a-migration).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-107">[Create a migration](#create-a-migration).</span></span> <span data-ttu-id="c8e8a-108">Générez du code qui peut mettre à jour la base de données pour la synchroniser avec un ensemble de modifications du modèle.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-108">Generate code that can update the database to sync it with a set of model changes.</span></span>
* <span data-ttu-id="c8e8a-109">[Mettre à jour la base de données](#update-the-database).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-109">[Update the database](#update-the-database).</span></span> <span data-ttu-id="c8e8a-110">Appliquez des migrations en attente pour mettre à jour le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-110">Apply pending migrations to update the database schema.</span></span>
* <span data-ttu-id="c8e8a-111">[Personnaliser le code de migration](#customize-migration-code).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-111">[Customize migration code](#customize-migration-code).</span></span> <span data-ttu-id="c8e8a-112">Le code généré doit parfois être modifié ou complété.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-112">Sometimes the generated code needs to be modified or supplemented.</span></span>
* <span data-ttu-id="c8e8a-113">[Supprimer une migration](#remove-a-migration).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-113">[Remove a migration](#remove-a-migration).</span></span> <span data-ttu-id="c8e8a-114">Supprimez le code généré.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-114">Delete the generated code.</span></span>
* <span data-ttu-id="c8e8a-115">[Rétablir une migration](#revert-a-migration).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-115">[Revert a migration](#revert-a-migration).</span></span> <span data-ttu-id="c8e8a-116">Annulez les modifications apportées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-116">Undo the database changes.</span></span>
* <span data-ttu-id="c8e8a-117">[Générer des scripts SQL](#generate-sql-scripts).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-117">[Generate SQL scripts](#generate-sql-scripts).</span></span> <span data-ttu-id="c8e8a-118">Vous aurez peut-être besoin d’un script pour mettre à jour une base de données de production ou pour résoudre des problèmes liés au code de migration.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-118">You might need a script to update a production database or to troubleshoot migration code.</span></span>
* <span data-ttu-id="c8e8a-119">[Appliquer des migrations au moment de l’exécution](#apply-migrations-at-runtime).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-119">[Apply migrations at runtime](#apply-migrations-at-runtime).</span></span> <span data-ttu-id="c8e8a-120">Quand les mises à jour au moment du design et l’exécution de scripts ne sont pas les meilleures options, appelez la méthode `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-120">When design-time updates and running scripts aren't the best options, call the `Migrate()` method.</span></span>

<a name="install-the-tools"></a><span data-ttu-id="c8e8a-121">Installer les outils</span><span class="sxs-lookup"><span data-stu-id="c8e8a-121">Install the tools</span></span>
-----------------

<span data-ttu-id="c8e8a-122">Installez les [outils en ligne de commande](xref:core/miscellaneous/cli/index) :</span><span class="sxs-lookup"><span data-stu-id="c8e8a-122">Install the [command-line tools](xref:core/miscellaneous/cli/index):</span></span>
* <span data-ttu-id="c8e8a-123">Pour Visual Studio, nous vous recommandons les [outils de la Console du Gestionnaire de package](xref:core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-123">For Visual Studio, we recommend the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="c8e8a-124">Pour d’autres environnements de développement, choisissez les [outils CLI .NET Core](xref:core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-124">For other development environments, choose the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span>

<a name="create-a-migration"></a><span data-ttu-id="c8e8a-125">Créer une migration</span><span class="sxs-lookup"><span data-stu-id="c8e8a-125">Create a migration</span></span>
------------------

<span data-ttu-id="c8e8a-126">Une fois que vous avez [défini votre modèle initial](xref:core/modeling/index), il est temps de créer la base de données.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-126">After you've [defined your initial model](xref:core/modeling/index), it's time to create the database.</span></span> <span data-ttu-id="c8e8a-127">Pour ajouter une migration initiale, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-127">To add an initial migration, run the following command.</span></span>

``` powershell
Add-Migration InitialCreate
```
``` Console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="c8e8a-128">Trois fichiers sont ajoutés à votre projet sous le répertoire **Migrations** :</span><span class="sxs-lookup"><span data-stu-id="c8e8a-128">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="c8e8a-129">**XXXXXXXXXXXXXX_InitialCreate.cs** : fichier principal des migrations.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-129">**XXXXXXXXXXXXXX_InitialCreate.cs**--The main migrations file.</span></span> <span data-ttu-id="c8e8a-130">Contient les opérations nécessaires à l’application de la migration (dans `Up()`) et à sa restauration (dans `Down()`).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-130">Contains the operations necessary to apply the migration (in `Up()`) and to revert it (in `Down()`).</span></span>
* <span data-ttu-id="c8e8a-131">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs** : fichier de métadonnées des migrations.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-131">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="c8e8a-132">Contient des informations utilisées par EF.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-132">Contains information used by EF.</span></span>
* <span data-ttu-id="c8e8a-133">**MyContextModelSnapshot.cs** : instantané de votre modèle actuel.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-133">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="c8e8a-134">Permet de déterminer ce qui a changé pendant l’ajout de la migration suivante.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-134">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="c8e8a-135">L’horodatage dans le nom des fichiers permet de conserver ces derniers dans l’ordre chronologique et de voir ainsi la progression des modifications.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-135">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

> [!TIP]
> <span data-ttu-id="c8e8a-136">Vous êtes libre de déplacer les fichiers Migrations et de changer leur espace de noms.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-136">You are free to move Migrations files and change their namespace.</span></span> <span data-ttu-id="c8e8a-137">Les migrations sont créées en tant que sœurs de la dernière migration.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-137">New migrations are created as siblings of the last migration.</span></span>

<a name="update-the-database"></a><span data-ttu-id="c8e8a-138">Mettre à jour la base de données</span><span class="sxs-lookup"><span data-stu-id="c8e8a-138">Update the database</span></span>
-------------------

<span data-ttu-id="c8e8a-139">Ensuite, appliquez la migration à la base de données pour créer le schéma.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-139">Next, apply the migration to the database to create the schema.</span></span>

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

<a name="customize-migration-code"></a><span data-ttu-id="c8e8a-140">Personnaliser le code de migration</span><span class="sxs-lookup"><span data-stu-id="c8e8a-140">Customize migration code</span></span>
------------------------

<span data-ttu-id="c8e8a-141">Une fois votre modèle EF Core modifié, le schéma de base de données risque de ne plus être synchronisé. Pour le mettre à jour, ajoutez une autre migration.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-141">After making changes to your EF Core model, the database schema might be out of sync. To bring it up to date, add another migration.</span></span> <span data-ttu-id="c8e8a-142">Le nom de la migration peut être utilisé comme un message de validation dans un système de gestion de versions.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-142">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="c8e8a-143">Par exemple, vous pouvez choisir un nom tel que *AjouterÉvaluationsProduit* si la modification est une nouvelle classe d’entité pour les évaluations.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-143">For example, you might choose a name like *AddProductReviews* if the change is a new entity class for reviews.</span></span>

``` powershell
Add-Migration AddProductReviews
```
``` Console
dotnet ef migrations add AddProductReviews
```

<span data-ttu-id="c8e8a-144">Une fois que la migration a été structurée (que du code a été généré pour elle), vérifiez si le code est exact et ajoutez, supprimez ou modifiez toutes les opérations nécessaires pour pouvoir l’appliquer correctement.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-144">Once the migration is scaffolded (code generated for it), review the code for accuracy and add, remove or modify any operations required to apply it correctly.</span></span>

<span data-ttu-id="c8e8a-145">Par exemple, une migration peut contenir les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="c8e8a-145">For example, a migration might contain the following operations:</span></span>

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

<span data-ttu-id="c8e8a-146">Bien que ces opérations rendent le schéma de base de données compatible, elles ne conservent pas les noms de client existants.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-146">While these operations make the database schema compatible, they don't preserve the existing customer names.</span></span> <span data-ttu-id="c8e8a-147">Pour l’améliorer, réécrivez la migration comme suit.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-147">To make it better, rewrite it as follows.</span></span>

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
> <span data-ttu-id="c8e8a-148">Le processus de structuration de migration vous avertit quand une opération peut entraîner une perte de données (par exemple la suppression d’une colonne).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-148">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="c8e8a-149">Si cet avertissement s’affiche, veillez particulièrement à examiner si le code de migration est exact.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-149">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

<span data-ttu-id="c8e8a-150">Appliquez la migration à la base de données à l’aide de la commande appropriée.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-150">Apply the migration to the database using the appropriate command.</span></span>

``` powershell
Update-Database
```
``` Console
dotnet ef database update
```

### <a name="empty-migrations"></a><span data-ttu-id="c8e8a-151">Migrations vides</span><span class="sxs-lookup"><span data-stu-id="c8e8a-151">Empty migrations</span></span>

<span data-ttu-id="c8e8a-152">Il est parfois utile d’ajouter une migration sans apporter de modification au modèle.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-152">Sometimes it's useful to add a migration without making any model changes.</span></span> <span data-ttu-id="c8e8a-153">Dans ce cas, l’ajout d’une nouvelle migration crée des fichiers de code avec des classes vides.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-153">In this case, adding a new migration creates code files with empty classes.</span></span> <span data-ttu-id="c8e8a-154">Vous pouvez personnaliser cette migration pour effectuer des opérations qui ne sont pas directement liées au modèle EF Core.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-154">You can customize this migration to perform operations that don't directly relate to the EF Core model.</span></span> <span data-ttu-id="c8e8a-155">Voici quelques exemples de ce que vous pouvez gérer de cette façon :</span><span class="sxs-lookup"><span data-stu-id="c8e8a-155">Some things you might want to manage this way are:</span></span>

* <span data-ttu-id="c8e8a-156">Recherche en texte intégral</span><span class="sxs-lookup"><span data-stu-id="c8e8a-156">Full-Text Search</span></span>
* <span data-ttu-id="c8e8a-157">Fonctions</span><span class="sxs-lookup"><span data-stu-id="c8e8a-157">Functions</span></span>
* <span data-ttu-id="c8e8a-158">Procédures stockées</span><span class="sxs-lookup"><span data-stu-id="c8e8a-158">Stored procedures</span></span>
* <span data-ttu-id="c8e8a-159">Déclencheurs</span><span class="sxs-lookup"><span data-stu-id="c8e8a-159">Triggers</span></span>
* <span data-ttu-id="c8e8a-160">Affichages</span><span class="sxs-lookup"><span data-stu-id="c8e8a-160">Views</span></span>

<a name="remove-a-migration"></a><span data-ttu-id="c8e8a-161">Supprimer une migration</span><span class="sxs-lookup"><span data-stu-id="c8e8a-161">Remove a migration</span></span>
------------------
<span data-ttu-id="c8e8a-162">Parfois, vous ajoutez une migration et réalisez que vous devez apporter des modifications supplémentaires à votre modèle EF Core avant de l’appliquer.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-162">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="c8e8a-163">Pour supprimer la dernière migration, utilisez cette commande.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-163">To remove the last migration, use this command.</span></span>

``` powershell
Remove-Migration
```
``` Console
dotnet ef migrations remove
```

<span data-ttu-id="c8e8a-164">Après avoir supprimé la migration, vous pouvez apporter les modifications supplémentaires au modèle et la rajouter.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-164">After removing the migration, you can make the additional model changes and add it again.</span></span>

<a name="revert-a-migration"></a><span data-ttu-id="c8e8a-165">Rétablir une migration</span><span class="sxs-lookup"><span data-stu-id="c8e8a-165">Revert a migration</span></span>
------------------
<span data-ttu-id="c8e8a-166">Si vous avez déjà appliqué une migration (ou plusieurs migrations) à la base de données, mais que vous devez la restaurer, vous pouvez utiliser la même commande que celle servant à appliquer des migrations, mais en spécifiant le nom de la migration à restaurer.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-166">If you already applied a migration (or several migrations) to the database but need to revert it, you can use the same command to apply migrations, but specify the name of the migration you want to roll back to.</span></span>

``` powershell
Update-Database LastGoodMigration
```
``` Console
dotnet ef database update LastGoodMigration
```

<a name="generate-sql-scripts"></a><span data-ttu-id="c8e8a-167">Générer des scripts SQL</span><span class="sxs-lookup"><span data-stu-id="c8e8a-167">Generate SQL scripts</span></span>
--------------------
<span data-ttu-id="c8e8a-168">Quand vous déboguez vos migrations ou que vous les déployez sur une base de données de production, il est utile de générer un script SQL.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-168">When debugging your migrations or deploying them to a production database, it's useful to generate a SQL script.</span></span> <span data-ttu-id="c8e8a-169">Vous pouvez ensuite revoir le script et l’affiner en fonction des besoins d’une base de données de production.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-169">The script can then be further reviewed for accuracy and tuned to fit the needs of a production database.</span></span> <span data-ttu-id="c8e8a-170">Vous pouvez également utiliser le script conjointement avec une technologie de déploiement.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-170">The script can also be used in conjunction with a deployment technology.</span></span> <span data-ttu-id="c8e8a-171">La commande de base est la suivante.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-171">The basic command is as follows.</span></span>

``` powershell
Script-Migration
```
``` Console
dotnet ef migrations script
```

<span data-ttu-id="c8e8a-172">Il existe plusieurs options pour cette commande.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-172">There are several options to this command.</span></span>

<span data-ttu-id="c8e8a-173">La migration **from** doit être la dernière migration appliquée à la base de données avant l’exécution du script.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-173">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="c8e8a-174">Si aucune migration n’a été appliquée, spécifiez `0` (il s’agit de la valeur par défaut).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-174">If no migrations have been applied, specify `0` (this is the default).</span></span>

<span data-ttu-id="c8e8a-175">La migration **to** est la dernière migration à appliquer à la base de données après l’exécution du script.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-175">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="c8e8a-176">Par défaut, il s’agit de la dernière migration dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-176">This defaults to the last migration in your project.</span></span>

<span data-ttu-id="c8e8a-177">Un script **idempotent** peut également être généré.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-177">An **idempotent** script can optionally be generated.</span></span> <span data-ttu-id="c8e8a-178">Ce script n’applique les migrations que si elles n’ont pas déjà été appliquées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-178">This script only applies migrations if they haven't already been applied to the database.</span></span> <span data-ttu-id="c8e8a-179">Cela est utile si vous ne savez pas exactement ce que la dernière migration a appliqué à la base de données ou si vous effectuez un déploiement sur plusieurs bases de données pouvant chacune être liée à une migration différente.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-179">This is useful if you don't exactly know what the last migration applied to the database was or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<a name="apply-migrations-at-runtime"></a><span data-ttu-id="c8e8a-180">Appliquer des migrations au moment de l’exécution</span><span class="sxs-lookup"><span data-stu-id="c8e8a-180">Apply migrations at runtime</span></span>
---------------------------
<span data-ttu-id="c8e8a-181">Certaines applications sont susceptibles d’appliquer des migrations au moment de l’exécution (au démarrage ou à la première exécution).</span><span class="sxs-lookup"><span data-stu-id="c8e8a-181">Some apps may want to apply migrations at runtime during startup or first run.</span></span> <span data-ttu-id="c8e8a-182">Ces opérations nécessitent l’utilisation de la méthode `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-182">Do this using the `Migrate()` method.</span></span>

<span data-ttu-id="c8e8a-183">Cette méthode s’appuie sur le service `IMigrator`, qui peut être utilisé pour des scénarios plus avancés.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-183">This method builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="c8e8a-184">Utilisez `DbContext.GetService<IMigrator>()` pour y accéder.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-184">Use `DbContext.GetService<IMigrator>()` to access it.</span></span>

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
> * <span data-ttu-id="c8e8a-185">Cette approche ne s’adresse pas à tout un chacun.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-185">This approach isn't for everyone.</span></span> <span data-ttu-id="c8e8a-186">Bien qu’elle soit idéale pour les applications avec une base de données locale, la plupart des applications nécessitent une stratégie de déploiement plus robuste, telle que la génération de scripts SQL.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-186">While it's great for apps with a local database, most applications will require more robust deployment strategy like generating SQL scripts.</span></span>
> * <span data-ttu-id="c8e8a-187">N’appelez pas `EnsureCreated()` avant `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-187">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="c8e8a-188">`EnsureCreated()` ignore Migrations pour créer le schéma, ce qui entraîne l’échec de `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-188">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>

<a name="next-steps"></a><span data-ttu-id="c8e8a-189">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="c8e8a-189">Next steps</span></span>
----------

<span data-ttu-id="c8e8a-190">Pour plus d'informations, consultez <xref:core/miscellaneous/cli/index>.</span><span class="sxs-lookup"><span data-stu-id="c8e8a-190">For more information, see <xref:core/miscellaneous/cli/index>.</span></span>
