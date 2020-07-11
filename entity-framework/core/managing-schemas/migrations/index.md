---
title: Vue d’ensemble des migrations-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 8539a8da6f0051d3737efc583f0adfaf05fb2d3d
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238227"
---
# <a name="migrations-overview"></a><span data-ttu-id="0f43e-102">Vue d’ensemble des migrations</span><span class="sxs-lookup"><span data-stu-id="0f43e-102">Migrations Overview</span></span>

<span data-ttu-id="0f43e-103">Dans les projets réels, les modèles de données changent à mesure que les fonctionnalités sont implémentées : de nouvelles entités ou propriétés sont ajoutées et supprimées, et les schémas de base de données doivent être modifiés en conséquence afin d’être synchronisés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="0f43e-103">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="0f43e-104">La fonctionnalité de migration dans EF Core permet de mettre à jour de manière incrémentielle le schéma de la base de données pour qu’il reste synchronisé avec le modèle de données de l’application tout en conservant les données existantes dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0f43e-104">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="0f43e-105">À un niveau élevé, les migrations fonctionnent de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="0f43e-105">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="0f43e-106">Lors de l’introduction d’une modification de modèle de données, le développeur utilise EF Core outils pour ajouter une migration correspondante décrivant les mises à jour nécessaires pour maintenir la synchronisation du schéma de base de données. EF Core compare le modèle actuel par rapport à un instantané de l’ancien modèle pour déterminer les différences et génère des fichiers sources de migration ; les fichiers peuvent être suivis dans le contrôle de code source de votre projet comme tout autre fichier source.</span><span class="sxs-lookup"><span data-stu-id="0f43e-106">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="0f43e-107">Une fois qu’une nouvelle migration a été générée, elle peut être appliquée à une base de données de différentes façons.</span><span class="sxs-lookup"><span data-stu-id="0f43e-107">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="0f43e-108">EF Core enregistre toutes les migrations appliquées dans une table d’historique spéciale, ce qui lui permet de savoir quelles migrations ont été appliquées.</span><span class="sxs-lookup"><span data-stu-id="0f43e-108">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="0f43e-109">Le reste de cette page est un guide pas à pas pour le débutant en vue de l’utilisation des migrations.</span><span class="sxs-lookup"><span data-stu-id="0f43e-109">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="0f43e-110">Consultez les autres pages de cette section pour obtenir des informations plus approfondies.</span><span class="sxs-lookup"><span data-stu-id="0f43e-110">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="0f43e-111">Prise en main</span><span class="sxs-lookup"><span data-stu-id="0f43e-111">Getting started</span></span>

<span data-ttu-id="0f43e-112">Supposons que vous venez de terminer votre première EF Core application, qui contient le modèle simple suivant :</span><span class="sxs-lookup"><span data-stu-id="0f43e-112">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="0f43e-113">Pendant le développement, vous avez peut-être utilisé les [API de création et de suppression](xref:core/managing-schemas/ensure-created) pour itérer rapidement, en modifiant votre modèle si nécessaire. mais maintenant que votre application va passer en production, vous devez disposer d’un moyen de faire évoluer le schéma en toute sécurité sans supprimer l’intégralité de la base de données.</span><span class="sxs-lookup"><span data-stu-id="0f43e-113">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="0f43e-114">Installer les outils</span><span class="sxs-lookup"><span data-stu-id="0f43e-114">Install the tools</span></span>

<span data-ttu-id="0f43e-115">Tout d’abord, vous devez installer les [outils en ligne de commande EF Core](xref:core/miscellaneous/cli/index):</span><span class="sxs-lookup"><span data-stu-id="0f43e-115">First, you'll have to install the [EF Core command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="0f43e-116">Nous recommandons généralement d’utiliser les [outils de CLI .net Core](xref:core/miscellaneous/cli/dotnet), qui fonctionnent sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="0f43e-116">We generally recommend using the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="0f43e-117">Si vous avez l’habitude de travailler dans Visual Studio ou si vous avez des difficultés avec les migrations EF6, vous pouvez également utiliser les outils de la [console du gestionnaire de package](xref:core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="0f43e-117">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="0f43e-118">Créer votre première migration</span><span class="sxs-lookup"><span data-stu-id="0f43e-118">Create your first migration</span></span>

<span data-ttu-id="0f43e-119">Vous êtes maintenant prêt à ajouter votre première migration !</span><span class="sxs-lookup"><span data-stu-id="0f43e-119">You're now ready to add your first migration!</span></span> <span data-ttu-id="0f43e-120">Demandez à EF Core de créer une migration nommée **InitialCreate**:</span><span class="sxs-lookup"><span data-stu-id="0f43e-120">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="0f43e-121">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="0f43e-121">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="0f43e-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f43e-122">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="0f43e-123">EF Core créera un répertoire nommé **migrations** dans votre projet et générera des fichiers.</span><span class="sxs-lookup"><span data-stu-id="0f43e-123">EF Core will create a directory called **Migrations** in your project, and generate some files.</span></span> <span data-ttu-id="0f43e-124">Il est judicieux d’inspecter ce qui est exactement EF Core généré, et éventuellement de le modifier, mais nous allons ignorer cela pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="0f43e-124">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="0f43e-125">Créer votre base de données et votre schéma</span><span class="sxs-lookup"><span data-stu-id="0f43e-125">Create your database and schema</span></span>

<span data-ttu-id="0f43e-126">À ce stade, EF peut créer votre base de données et créer votre schéma à partir de la migration.</span><span class="sxs-lookup"><span data-stu-id="0f43e-126">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="0f43e-127">Pour ce faire, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="0f43e-127">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="0f43e-128">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="0f43e-128">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="0f43e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f43e-129">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="0f43e-130">Voilà, votre application est prête à être exécutée sur votre nouvelle base de données, et vous n’avez pas besoin d’écrire une seule ligne de SQL.</span><span class="sxs-lookup"><span data-stu-id="0f43e-130">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="0f43e-131">Notez que cette méthode d’application des migrations est idéale pour le développement local, mais est moins adaptée aux environnements de production. pour plus d’informations, consultez la [page application des migrations](xref:core/managing-schemas/migrations/applying) .</span><span class="sxs-lookup"><span data-stu-id="0f43e-131">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="0f43e-132">Évolution de votre modèle</span><span class="sxs-lookup"><span data-stu-id="0f43e-132">Evolving your model</span></span>

<span data-ttu-id="0f43e-133">Quelques jours se sont écoulés, et vous êtes invité à ajouter un horodateur de création à vos blogs.</span><span class="sxs-lookup"><span data-stu-id="0f43e-133">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="0f43e-134">Vous avez effectué les modifications nécessaires à votre application et votre modèle ressemble maintenant à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="0f43e-134">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="0f43e-135">Votre modèle et votre base de données de production ne sont plus synchronisés. nous devons ajouter une nouvelle colonne à votre schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="0f43e-135">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="0f43e-136">Nous allons créer une nouvelle migration pour ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="0f43e-136">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="0f43e-137">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="0f43e-137">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="0f43e-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f43e-138">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="0f43e-139">Notez que nous fournissons des migrations un nom descriptif pour faciliter la compréhension ultérieure de l’historique du projet.</span><span class="sxs-lookup"><span data-stu-id="0f43e-139">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="0f43e-140">Comme il ne s’agit pas de la première migration du projet, EF Core compare maintenant votre modèle mis à jour par rapport à un instantané de l’ancien modèle, avant l’ajout de la colonne ; l’instantané de modèle est l’un des fichiers générés par EF Core lorsque vous ajoutez une migration, et est archivé dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="0f43e-140">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="0f43e-141">Sur la base de cette comparaison, EF Core détecte qu’une colonne a été ajoutée et ajoute la migration appropriée.</span><span class="sxs-lookup"><span data-stu-id="0f43e-141">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="0f43e-142">Vous pouvez maintenant appliquer votre migration comme précédemment :</span><span class="sxs-lookup"><span data-stu-id="0f43e-142">You can now apply your migration as before:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="0f43e-143">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="0f43e-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="0f43e-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f43e-144">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="0f43e-145">Notez que cette fois, EF détecte que la base de données existe déjà.</span><span class="sxs-lookup"><span data-stu-id="0f43e-145">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="0f43e-146">En outre, lorsque la première migration était appliquée ci-dessus, ce fait a été enregistré dans une table d’historique des migrations spéciale de votre base de données. Cela permet à EF d’appliquer automatiquement la nouvelle migration uniquement.</span><span class="sxs-lookup"><span data-stu-id="0f43e-146">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="next-steps"></a><span data-ttu-id="0f43e-147">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="0f43e-147">Next steps</span></span>

<span data-ttu-id="0f43e-148">Vous ne disposez que d’une brève présentation des migrations.</span><span class="sxs-lookup"><span data-stu-id="0f43e-148">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="0f43e-149">Consultez les autres pages de documentation pour en savoir plus sur la [gestion des migrations](xref:core/managing-schemas/migrations/managing), [leur application](xref:core/managing-schemas/migrations/applying)et d’autres aspects.</span><span class="sxs-lookup"><span data-stu-id="0f43e-149">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="0f43e-150">La [référence de l’outil CLI .net Core](xref:core/miscellaneous/cli/index) contient également des informations utiles sur les différentes commandes.</span><span class="sxs-lookup"><span data-stu-id="0f43e-150">The [.NET Core CLI tool reference](xref:core/miscellaneous/cli/index) also contains useful information on the different commands</span></span>
