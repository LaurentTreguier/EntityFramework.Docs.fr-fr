---
title: Vue d’ensemble des migrations – EF Core
description: Vue d’ensemble de la gestion des schémas de la base de données avec Entity Framework Core à l’aide des migrations.
author: bricelam
ms.date: 10/28/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: b9547298714af59453aeae6d05742a03c067708b
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003393"
---
# <a name="migrations-overview"></a><span data-ttu-id="aef3c-103">Vue d’ensemble des migrations</span><span class="sxs-lookup"><span data-stu-id="aef3c-103">Migrations Overview</span></span>

<span data-ttu-id="aef3c-104">Dans la pratique, les modèles de données des projets évoluent au fur et à mesure que des fonctionnalités sont implémentées : de nouvelles entités et propriétés sont ajoutées et supprimées, et les schémas de la base de données doivent être modifiés en conséquence pour rester synchronisés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="aef3c-104">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="aef3c-105">La fonctionnalité de migration dans EF Core permet de mettre à jour de manière incrémentielle le schéma de la base de données pour qu’il reste synchronisé avec le modèle de données de l’application tout en conservant les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="aef3c-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="aef3c-106">Dans les grandes lignes, les migrations fonctionnent de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="aef3c-106">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="aef3c-107">Lors de l’introduction d’une modification du modèle de données, le développeur utilise des outils EF Core afin d’ajouter une migration correspondante décrivant les mises à jour nécessaires pour préserver la synchronisation du schéma de la base de données. EF Core compare le modèle actuel à un instantané de l’ancien modèle pour déterminer les différences et génère des fichiers sources de migration. Ces fichiers peuvent, comme n’importe quel fichier source, faire l’objet d’un suivi dans le contrôle de code source du projet.</span><span class="sxs-lookup"><span data-stu-id="aef3c-107">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="aef3c-108">Une fois générée, la nouvelle migration peut être appliquée à une base de données de différentes façons.</span><span class="sxs-lookup"><span data-stu-id="aef3c-108">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="aef3c-109">EF Core enregistre toutes les migrations appliquées dans une table d’historique spéciale, ce qui lui permet de savoir quelles migrations ont été appliquées.</span><span class="sxs-lookup"><span data-stu-id="aef3c-109">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="aef3c-110">La suite de cette page est un guide pas à pas d’utilisation des migrations à destination des débutants.</span><span class="sxs-lookup"><span data-stu-id="aef3c-110">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="aef3c-111">Pour plus d’informations, consultez les autres pages de cette section .</span><span class="sxs-lookup"><span data-stu-id="aef3c-111">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="aef3c-112">Prise en main</span><span class="sxs-lookup"><span data-stu-id="aef3c-112">Getting started</span></span>

<span data-ttu-id="aef3c-113">Supposons que vous venez de terminer votre première application EF Core, qui contient le modèle simple suivant :</span><span class="sxs-lookup"><span data-stu-id="aef3c-113">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="aef3c-114">Pendant le développement, vous avez peut-être utilisé les [API de création et de suppression](xref:core/managing-schemas/ensure-created) pour effectuer des itérations rapides, en modifiant votre modèle selon les besoins. Cependant, maintenant que votre application est sur le point de passer en production, il vous faut un moyen de faire évoluer le schéma de manière sécurisée sans supprimer l’intégralité de la base de données.</span><span class="sxs-lookup"><span data-stu-id="aef3c-114">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="aef3c-115">Installer les outils</span><span class="sxs-lookup"><span data-stu-id="aef3c-115">Install the tools</span></span>

<span data-ttu-id="aef3c-116">Tout d’abord, vous devez installer les [outils en ligne de commande EF Core](xref:core/cli/index) :</span><span class="sxs-lookup"><span data-stu-id="aef3c-116">First, you'll have to install the [EF Core command-line tools](xref:core/cli/index):</span></span>

* <span data-ttu-id="aef3c-117">Nous recommandons généralement d’utiliser les [outils CLI .NET Core](xref:core/cli/dotnet), qui fonctionnent sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="aef3c-117">We generally recommend using the [.NET Core CLI tools](xref:core/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="aef3c-118">Si vous avez l’habitude de travailler dans Visual Studio ou que vous connaissez les migrations EF6, vous pouvez également utiliser les [outils de la console du gestionnaire de package](xref:core/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="aef3c-118">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="aef3c-119">Première création d’une migration</span><span class="sxs-lookup"><span data-stu-id="aef3c-119">Create your first migration</span></span>

<span data-ttu-id="aef3c-120">Il est temps d’ajouter votre première migration.</span><span class="sxs-lookup"><span data-stu-id="aef3c-120">You're now ready to add your first migration!</span></span> <span data-ttu-id="aef3c-121">Demandez à EF Core de créer une migration nommée **InitialCreate** :</span><span class="sxs-lookup"><span data-stu-id="aef3c-121">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="aef3c-122">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="aef3c-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="aef3c-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aef3c-123">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate
```

<span data-ttu-id="aef3c-124">\*\*_</span><span class="sxs-lookup"><span data-stu-id="aef3c-124">\*\*_</span></span>

<span data-ttu-id="aef3c-125">EF Core créera un répertoire appelé _ *migrations*\* dans votre projet et générera des fichiers.</span><span class="sxs-lookup"><span data-stu-id="aef3c-125">EF Core will create a directory called _ *Migrations*\* in your project, and generate some files.</span></span> <span data-ttu-id="aef3c-126">Il est judicieux d’inspecter précisément les fichiers en question, et éventuellement de les modifier, mais nous allons nous en passer pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="aef3c-126">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="aef3c-127">Création d’une base de données et d’un schéma</span><span class="sxs-lookup"><span data-stu-id="aef3c-127">Create your database and schema</span></span>

<span data-ttu-id="aef3c-128">Vous pouvez maintenant demander à EF de créer votre base de données et votre schéma à partir de la migration,</span><span class="sxs-lookup"><span data-stu-id="aef3c-128">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="aef3c-129">par différents moyens :</span><span class="sxs-lookup"><span data-stu-id="aef3c-129">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="aef3c-130">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="aef3c-130">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[<span data-ttu-id="aef3c-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aef3c-131">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database
```

<span data-ttu-id="aef3c-132">\*\*_</span><span class="sxs-lookup"><span data-stu-id="aef3c-132">\*\*_</span></span>

<span data-ttu-id="aef3c-133">Votre application est prête à s’exécuter sur votre nouvelle base de données, sans que vous ayez eu à écrire une seule ligne de code SQL.</span><span class="sxs-lookup"><span data-stu-id="aef3c-133">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="aef3c-134">Sachez que cette méthode d’application des migrations, idéale pour le développement local, est moins adaptée aux environnements de production. Pour plus d’informations, consultez la page [Application des migrations](xref:core/managing-schemas/migrations/applying).</span><span class="sxs-lookup"><span data-stu-id="aef3c-134">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="aef3c-135">Évolution du modèle</span><span class="sxs-lookup"><span data-stu-id="aef3c-135">Evolving your model</span></span>

<span data-ttu-id="aef3c-136">Au bout de quelques jours, il vous est demandé d’ajouter un timestamp de création à vos blogs.</span><span class="sxs-lookup"><span data-stu-id="aef3c-136">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="aef3c-137">Vous avez apporté les modifications nécessaires à votre application. Votre modèle se présente maintenant ainsi :</span><span class="sxs-lookup"><span data-stu-id="aef3c-137">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="aef3c-138">Votre modèle et votre base de données de production ne sont plus synchronisés. Vous devez donc ajouter une nouvelle colonne au schéma de votre base de données.</span><span class="sxs-lookup"><span data-stu-id="aef3c-138">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="aef3c-139">Créons pour cela une nouvelle migration :</span><span class="sxs-lookup"><span data-stu-id="aef3c-139">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="aef3c-140">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="aef3c-140">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="aef3c-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aef3c-141">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

_*_

<span data-ttu-id="aef3c-142">Comme vous pouvez le constater, nous donnons aux migrations un nom descriptif pour faciliter par la suite la compréhension de l’historique du projet.</span><span class="sxs-lookup"><span data-stu-id="aef3c-142">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="aef3c-143">Comme il ne s’agit pas de la première migration du projet, EF Core compare maintenant votre modèle mis à jour à un instantané de l’ancien modèle avant ajout de la colonne, à savoir l’un des fichiers générés par EF Core à l’ajout d’une migration, archivé dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="aef3c-143">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="aef3c-144">Sur la base de cette comparaison, EF Core détecte qu’une colonne a été ajoutée et ajoute la migration correspondante.</span><span class="sxs-lookup"><span data-stu-id="aef3c-144">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="aef3c-145">Vous pouvez maintenant appliquer votre migration comme tout à l’heure :</span><span class="sxs-lookup"><span data-stu-id="aef3c-145">You can now apply your migration as before:</span></span>

<!--markdownlint-disable MD024-->

#### <a name="net-core-cli"></a>[<span data-ttu-id="aef3c-146">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="aef3c-146">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[<span data-ttu-id="aef3c-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aef3c-147">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database
```

<!--markdownlint-enable MD024-->

_*_

<span data-ttu-id="aef3c-148">Cette fois, EF détecte que la base de données existe déjà.</span><span class="sxs-lookup"><span data-stu-id="aef3c-148">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="aef3c-149">Par ailleurs, lors de l’application de la première migration, ce fait a été enregistré dans une table d’historique des migrations spéciale de la base de données. Ainsi, EF peut appliquer automatiquement la nouvelle migration uniquement.</span><span class="sxs-lookup"><span data-stu-id="aef3c-149">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="excluding-parts-of-your-model"></a><span data-ttu-id="aef3c-150">Exclusion de parties de votre modèle</span><span class="sxs-lookup"><span data-stu-id="aef3c-150">Excluding parts of your model</span></span>

> [!NOTE]
> <span data-ttu-id="aef3c-151">Cette fonctionnalité a été introduite EF dans Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="aef3c-151">This feature was introduced EF in Core 5.0.</span></span>

<span data-ttu-id="aef3c-152">Parfois, vous pouvez faire référence à des types à partir d’un autre DbContext.</span><span class="sxs-lookup"><span data-stu-id="aef3c-152">Sometimes you may want to reference types from another DbContext.</span></span> <span data-ttu-id="aef3c-153">Cela peut entraîner des conflits de migration.</span><span class="sxs-lookup"><span data-stu-id="aef3c-153">This can lead to migration conflicts.</span></span> <span data-ttu-id="aef3c-154">Pour éviter cela, excluez le type des migrations de l’un des DbContexts.</span><span class="sxs-lookup"><span data-stu-id="aef3c-154">To prevent this, exclude the type from the migrations of one of the DbContexts.</span></span>

[!code-csharp[](../../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs#TableExcludeFromMigrations)]

### <a name="next-steps"></a><span data-ttu-id="aef3c-155">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="aef3c-155">Next steps</span></span>

<span data-ttu-id="aef3c-156">Cette page ne constitue qu’une courte initiation aux migrations.</span><span class="sxs-lookup"><span data-stu-id="aef3c-156">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="aef3c-157">Pour plus d’informations sur la [gestion des migrations](xref:core/managing-schemas/migrations/managing), leur [application](xref:core/managing-schemas/migrations/applying) et d’autres aspects, consultez les autres pages de la documentation.</span><span class="sxs-lookup"><span data-stu-id="aef3c-157">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="aef3c-158">La [référence de l’outil CLI .NET Core](xref:core/cli/index) contient également des informations utiles sur les différentes commandes.</span><span class="sxs-lookup"><span data-stu-id="aef3c-158">The [.NET Core CLI tool reference](xref:core/cli/index) also contains useful information on the different commands</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aef3c-159">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="aef3c-159">Additional resources</span></span>

<span data-ttu-id="aef3c-160">_ [EF Core session réunions](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) de la communauté en passant par les nouvelles fonctionnalités de migration de EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="aef3c-160">_ [EF Core Community Standup session](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) going over new migration features in EF Core 5.0.</span></span>
