---
title: Migrations avec plusieurs fournisseurs-EF Core
description: Utilisation de migrations pour gérer les schémas de base de données lors du ciblage de plusieurs fournisseurs de bases de données avec Entity Framework Core
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: feed19abb188eebc473386b67fac62848e682d96
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024093"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="143f5-103">Migrations avec plusieurs fournisseurs</span><span class="sxs-lookup"><span data-stu-id="143f5-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="143f5-104">Les [outils de EF corent](xref:core/cli/index) uniquement les migrations de l’échafaudage pour le fournisseur actif.</span><span class="sxs-lookup"><span data-stu-id="143f5-104">The [EF Core Tools](xref:core/cli/index) only scaffold migrations for the active provider.</span></span> <span data-ttu-id="143f5-105">Toutefois, il peut arriver que vous souhaitiez utiliser plusieurs fournisseurs (par exemple Microsoft SQL Server et SQLite) avec votre DbContext.</span><span class="sxs-lookup"><span data-stu-id="143f5-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="143f5-106">Gérez-la en conservant plusieurs ensembles de migrations, un pour chaque fournisseur, et en ajoutant une migration à chaque modification de modèle pour chaque modèle.</span><span class="sxs-lookup"><span data-stu-id="143f5-106">Handle this by maintaining multiple sets of migrations--one for each provider--and adding a migration to each for every model change.</span></span>

## <a name="using-multiple-context-types"></a><span data-ttu-id="143f5-107">Utilisation de plusieurs types de contexte</span><span class="sxs-lookup"><span data-stu-id="143f5-107">Using multiple context types</span></span>

<span data-ttu-id="143f5-108">Une façon de créer plusieurs jeux de migration consiste à utiliser un type DbContext par fournisseur.</span><span class="sxs-lookup"><span data-stu-id="143f5-108">One way to create multiple migration sets is to use one DbContext type per provider.</span></span>

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

<span data-ttu-id="143f5-109">Spécifiez le type de contexte lors de l’ajout de nouvelles migrations.</span><span class="sxs-lookup"><span data-stu-id="143f5-109">Specify the context type when adding new migrations.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="143f5-110">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="143f5-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="143f5-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="143f5-111">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="143f5-112">Vous n’avez pas besoin de spécifier le répertoire de sortie pour les migrations suivantes, car elles sont créées en tant que frères au dernier.</span><span class="sxs-lookup"><span data-stu-id="143f5-112">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="using-one-context-type"></a><span data-ttu-id="143f5-113">Utilisation d’un type de contexte</span><span class="sxs-lookup"><span data-stu-id="143f5-113">Using one context type</span></span>

<span data-ttu-id="143f5-114">Il est également possible d’utiliser un type DbContext.</span><span class="sxs-lookup"><span data-stu-id="143f5-114">It's also possible to use one DbContext type.</span></span> <span data-ttu-id="143f5-115">Cela nécessite actuellement le déplacement des migrations dans un assembly distinct.</span><span class="sxs-lookup"><span data-stu-id="143f5-115">This currently requires moving the migrations into a separate assembly.</span></span> <span data-ttu-id="143f5-116">Pour obtenir des instructions sur la configuration de vos projets, reportez-vous à [la rubrique utilisation d’un projet de migration distinct](xref:core/managing-schemas/migrations/projects) .</span><span class="sxs-lookup"><span data-stu-id="143f5-116">Please refer to [Using a Separate Migrations Project](xref:core/managing-schemas/migrations/projects) for instructions on setting up your projects.</span></span>

> [!TIP]
> <span data-ttu-id="143f5-117">Vous pouvez afficher cet [exemple sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/TwoProjectMigrations).</span><span class="sxs-lookup"><span data-stu-id="143f5-117">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/TwoProjectMigrations).</span></span>

<span data-ttu-id="143f5-118">À partir de EF Core 5,0, vous pouvez passer des arguments dans l’application à partir des outils.</span><span class="sxs-lookup"><span data-stu-id="143f5-118">Starting in EF Core 5.0, you can pass arguments into the app from the tools.</span></span> <span data-ttu-id="143f5-119">Cela peut permettre un flux de travail plus rationalisé qui évite d’avoir à apporter des modifications manuelles au projet lors de l’exécution des outils.</span><span class="sxs-lookup"><span data-stu-id="143f5-119">This can enable a more streamlined workflow that avoids having to make manual changes to the project when running the tools.</span></span>

<span data-ttu-id="143f5-120">Voici un modèle qui fonctionne bien lorsque vous utilisez un [hôte générique](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="143f5-120">Here's one pattern that works well when using a [Generic Host](/dotnet/core/extensions/generic-host).</span></span>

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

<span data-ttu-id="143f5-121">Étant donné que le générateur d’hôte par défaut lit la configuration à partir d’arguments de ligne de commande, vous pouvez spécifier le fournisseur lors de l’exécution des outils.</span><span class="sxs-lookup"><span data-stu-id="143f5-121">Since the default host builder reads configuration from command-line arguments, you can specify the provider when running the tools.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="143f5-122">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="143f5-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> <span data-ttu-id="143f5-123">Le `--` jeton indique `dotnet ef` à traiter tout ce qui suit comme argument et ne tente pas de les analyser en tant qu’options.</span><span class="sxs-lookup"><span data-stu-id="143f5-123">The `--` token directs `dotnet ef` to treat everything that follows as an argument and not try to parse them as options.</span></span> <span data-ttu-id="143f5-124">Les arguments supplémentaires non utilisés par `dotnet ef` sont transmis à l’application.</span><span class="sxs-lookup"><span data-stu-id="143f5-124">Any extra arguments not used by `dotnet ef` are forwarded to the app.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="143f5-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="143f5-125">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> <span data-ttu-id="143f5-126">La possibilité de spécifier des arguments supplémentaires pour l’application a été ajoutée dans EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="143f5-126">The ability to specify additional arguments for the app was added in EF Core 5.0.</span></span> <span data-ttu-id="143f5-127">Si vous utilisez une version antérieure, spécifiez à la place des valeurs de configuration avec des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="143f5-127">If you're using an older version, specify configuration values with environment variables instead.</span></span>
