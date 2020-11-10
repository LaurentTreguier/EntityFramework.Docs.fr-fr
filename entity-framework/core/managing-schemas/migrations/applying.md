---
title: Application de migrations-EF Core
description: Stratégies d’application des migrations de schéma vers des bases de données de production et de développement à l’aide de Entity Framework Core
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: 7ff84636fb0999941b832c6a2d65d77b0ad368c5
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429847"
---
# <a name="applying-migrations"></a><span data-ttu-id="72518-103">Application de migrations</span><span class="sxs-lookup"><span data-stu-id="72518-103">Applying Migrations</span></span>

<span data-ttu-id="72518-104">Une fois vos migrations ajoutées, elles doivent être déployées et appliquées à vos bases de données.</span><span class="sxs-lookup"><span data-stu-id="72518-104">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="72518-105">Il existe plusieurs stratégies pour y parvenir, dont certaines sont plus appropriées pour les environnements de production, et d’autres pour le cycle de vie du développement.</span><span class="sxs-lookup"><span data-stu-id="72518-105">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="72518-106">Quelle que soit votre stratégie de déploiement, Inspectez toujours les migrations générées et testez-les avant de les appliquer à une base de données de production.</span><span class="sxs-lookup"><span data-stu-id="72518-106">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="72518-107">Une migration peut supprimer une colonne lorsque l’intention est de la renommer ou peut échouer pour différentes raisons lorsqu’elle est appliquée à une base de données.</span><span class="sxs-lookup"><span data-stu-id="72518-107">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="72518-108">Scripts SQL</span><span class="sxs-lookup"><span data-stu-id="72518-108">SQL scripts</span></span>

<span data-ttu-id="72518-109">La méthode recommandée pour déployer des migrations vers une base de données de production consiste à générer des scripts SQL.</span><span class="sxs-lookup"><span data-stu-id="72518-109">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="72518-110">Les avantages de cette stratégie sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="72518-110">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="72518-111">La précision des scripts SQL peut être vérifiée ; Cela est important, car l’application de modifications de schéma aux bases de données de production est une opération potentiellement dangereuse qui peut impliquer une perte de données.</span><span class="sxs-lookup"><span data-stu-id="72518-111">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="72518-112">Dans certains cas, les scripts peuvent être réglés pour répondre aux besoins spécifiques d’une base de données de production.</span><span class="sxs-lookup"><span data-stu-id="72518-112">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="72518-113">Les scripts SQL peuvent être utilisés conjointement avec une technologie de déploiement et peuvent même être générés dans le cadre de votre processus d’intégration continue.</span><span class="sxs-lookup"><span data-stu-id="72518-113">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="72518-114">Les scripts SQL peuvent être fournis à un administrateur de bases de donnée et peuvent être gérés et archivés séparément.</span><span class="sxs-lookup"><span data-stu-id="72518-114">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="72518-115">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="72518-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="72518-116">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="72518-116">Basic Usage</span></span>

<span data-ttu-id="72518-117">L’exemple suivant génère un script SQL à partir d’une base de données vide vers la dernière migration :</span><span class="sxs-lookup"><span data-stu-id="72518-117">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="72518-118">Avec From (To implicite)</span><span class="sxs-lookup"><span data-stu-id="72518-118">With From (to implied)</span></span>

<span data-ttu-id="72518-119">L’exemple suivant génère un script SQL à partir de la migration donnée vers la dernière migration.</span><span class="sxs-lookup"><span data-stu-id="72518-119">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="72518-120">Avec From et To</span><span class="sxs-lookup"><span data-stu-id="72518-120">With From and To</span></span>

<span data-ttu-id="72518-121">L’exemple suivant génère un script SQL à partir de la `from` migration spécifiée vers la `to` migration spécifiée.</span><span class="sxs-lookup"><span data-stu-id="72518-121">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="72518-122">Vous pouvez utiliser un `from` plus récent que le `to` afin de générer un script de restauration.</span><span class="sxs-lookup"><span data-stu-id="72518-122">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="72518-123">Prenez note des scénarios de pertes de données potentielles.</span><span class="sxs-lookup"><span data-stu-id="72518-123">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="72518-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72518-124">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="72518-125">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="72518-125">Basic Usage</span></span>

<span data-ttu-id="72518-126">L’exemple suivant génère un script SQL à partir d’une base de données vide vers la dernière migration :</span><span class="sxs-lookup"><span data-stu-id="72518-126">The following generates a SQL script from a blank database to the latest migration:</span></span>

```powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="72518-127">Avec From (To implicite)</span><span class="sxs-lookup"><span data-stu-id="72518-127">With From (to implied)</span></span>

<span data-ttu-id="72518-128">L’exemple suivant génère un script SQL à partir de la migration donnée vers la dernière migration.</span><span class="sxs-lookup"><span data-stu-id="72518-128">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="72518-129">Avec From et To</span><span class="sxs-lookup"><span data-stu-id="72518-129">With From and To</span></span>

<span data-ttu-id="72518-130">L’exemple suivant génère un script SQL à partir de la `from` migration spécifiée vers la `to` migration spécifiée.</span><span class="sxs-lookup"><span data-stu-id="72518-130">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```

<span data-ttu-id="72518-131">Vous pouvez utiliser un `from` plus récent que le `to` afin de générer un script de restauration.</span><span class="sxs-lookup"><span data-stu-id="72518-131">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="72518-132">*Prenez note des scénarios de pertes de données potentielles.*</span><span class="sxs-lookup"><span data-stu-id="72518-132">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="72518-133">La génération de script accepte les deux arguments suivants dans, indiquez la plage de migrations à générer :</span><span class="sxs-lookup"><span data-stu-id="72518-133">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="72518-134">La migration **from** doit être la dernière migration appliquée à la base de données avant l’exécution du script.</span><span class="sxs-lookup"><span data-stu-id="72518-134">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="72518-135">Si aucune migration n’a été appliquée, spécifiez `0` (il s’agit de la valeur par défaut).</span><span class="sxs-lookup"><span data-stu-id="72518-135">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="72518-136">La migration **to** est la dernière migration à appliquer à la base de données après l’exécution du script.</span><span class="sxs-lookup"><span data-stu-id="72518-136">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="72518-137">Par défaut, il s’agit de la dernière migration dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="72518-137">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="72518-138">Scripts SQL idempotent</span><span class="sxs-lookup"><span data-stu-id="72518-138">Idempotent SQL scripts</span></span>

<span data-ttu-id="72518-139">Les scripts SQL générés ci-dessus ne peuvent être appliqués que pour modifier votre schéma d’une migration vers une autre. il vous incombe d’appliquer le script de manière appropriée et uniquement à la base de données dans l’état de migration approprié.</span><span class="sxs-lookup"><span data-stu-id="72518-139">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="72518-140">EF Core prend également en charge la génération de scripts **idempotent** , qui vérifient en interne les migrations qui ont déjà été appliquées (par le biais de la table d’historique des migrations) et ne s’appliquent qu’à celles qui sont manquantes.</span><span class="sxs-lookup"><span data-stu-id="72518-140">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="72518-141">Cela est utile si vous ne savez pas exactement quelle est la dernière migration appliquée à la base de données, ou si vous déployez sur plusieurs bases de données qui peuvent chacune être dans une migration différente.</span><span class="sxs-lookup"><span data-stu-id="72518-141">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="72518-142">La commande suivante génère des migrations idempotent :</span><span class="sxs-lookup"><span data-stu-id="72518-142">The following generates idempotent migrations:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="72518-143">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="72518-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

### <a name="visual-studio"></a>[<span data-ttu-id="72518-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72518-144">Visual Studio</span></span>](#tab/vs)

```powershell
Script-Migration -Idempotent
```

<span data-ttu-id="72518-145">\*\*_</span><span class="sxs-lookup"><span data-stu-id="72518-145">\*\*_</span></span>

## <a name="command-line-tools"></a><span data-ttu-id="72518-146">Outils de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="72518-146">Command-line tools</span></span>

<span data-ttu-id="72518-147">Les outils en ligne de commande EF peuvent être utilisés pour appliquer des migrations à une base de données.</span><span class="sxs-lookup"><span data-stu-id="72518-147">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="72518-148">Bien qu’elle soit productive pour le développement et le test de migrations locaux, cette approche n’est pas idéale pour la gestion des bases de données de production :</span><span class="sxs-lookup"><span data-stu-id="72518-148">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

<span data-ttu-id="72518-149">_ Les commandes SQL sont appliquées directement par l’outil, sans donner au développeur la possibilité de les inspecter ou de les modifier.</span><span class="sxs-lookup"><span data-stu-id="72518-149">_ The SQL commands are applied directly by the tool, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="72518-150">Cela peut être dangereux dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="72518-150">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="72518-151">Le kit de développement logiciel (SDK) .NET et l’outil EF doivent être installés sur les serveurs de production.</span><span class="sxs-lookup"><span data-stu-id="72518-151">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="72518-152">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="72518-152">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="72518-153">Le code suivant met à jour votre base de données avec la dernière migration :</span><span class="sxs-lookup"><span data-stu-id="72518-153">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="72518-154">Le code suivant met à jour votre base de données vers une migration donnée :</span><span class="sxs-lookup"><span data-stu-id="72518-154">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="72518-155">Notez que cela peut être utilisé pour revenir à une migration antérieure également.</span><span class="sxs-lookup"><span data-stu-id="72518-155">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="72518-156">Prenez note des scénarios de pertes de données potentielles.</span><span class="sxs-lookup"><span data-stu-id="72518-156">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="72518-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72518-157">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="72518-158">Le code suivant met à jour votre base de données avec la dernière migration :</span><span class="sxs-lookup"><span data-stu-id="72518-158">The following updates your database to the latest migration:</span></span>

```powershell
Update-Database
```

<span data-ttu-id="72518-159">Le code suivant met à jour votre base de données vers une migration donnée :</span><span class="sxs-lookup"><span data-stu-id="72518-159">The following updates your database to a given migration:</span></span>

```powershell
Update-Database AddNewTables
```

<span data-ttu-id="72518-160">Notez que cela peut être utilisé pour revenir à une migration antérieure également.</span><span class="sxs-lookup"><span data-stu-id="72518-160">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="72518-161">Prenez note des scénarios de pertes de données potentielles.</span><span class="sxs-lookup"><span data-stu-id="72518-161">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="72518-162">Pour plus d’informations sur l’application des migrations à l’aide des outils en ligne de commande, consultez la référence sur les [outils de EF Core](xref:core/cli/index).</span><span class="sxs-lookup"><span data-stu-id="72518-162">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="72518-163">Appliquer des migrations au moment de l’exécution</span><span class="sxs-lookup"><span data-stu-id="72518-163">Apply migrations at runtime</span></span>

<span data-ttu-id="72518-164">Il est possible que l’application elle-même applique des migrations par programme, en général au démarrage.</span><span class="sxs-lookup"><span data-stu-id="72518-164">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="72518-165">Bien qu’elle soit productive pour le développement et le test de migrations locaux, cette approche n’est pas appropriée pour la gestion des bases de données de production, pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="72518-165">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="72518-166">Si plusieurs instances de votre application sont en cours d’exécution, les deux applications peuvent tenter d’appliquer la migration simultanément et échouer (ou pire, entraîner une altération des données).</span><span class="sxs-lookup"><span data-stu-id="72518-166">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="72518-167">De même, si une application accède à la base de données alors qu’une autre application la migre, cela peut entraîner de graves problèmes.</span><span class="sxs-lookup"><span data-stu-id="72518-167">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="72518-168">L’application doit disposer d’un accès avec élévation de privilèges pour modifier le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="72518-168">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="72518-169">Il est généralement conseillé de limiter les autorisations de base de données de l’application en production.</span><span class="sxs-lookup"><span data-stu-id="72518-169">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="72518-170">Il est important de pouvoir restaurer une migration appliquée en cas de problème.</span><span class="sxs-lookup"><span data-stu-id="72518-170">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="72518-171">Les autres stratégies le fournissent facilement et en toute simplicité.</span><span class="sxs-lookup"><span data-stu-id="72518-171">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="72518-172">Les commandes SQL sont appliquées directement par le programme, sans donner aux développeurs la possibilité de les inspecter ou de les modifier.</span><span class="sxs-lookup"><span data-stu-id="72518-172">The SQL commands are applied directly by the program, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="72518-173">Cela peut être dangereux dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="72518-173">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="72518-174">Pour appliquer des migrations par programme, appelez `context.Database.Migrate()` .</span><span class="sxs-lookup"><span data-stu-id="72518-174">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="72518-175">Par exemple, une application ASP.NET classique peut effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="72518-175">For example, a typical ASP.NET application can do the following:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

<span data-ttu-id="72518-176">Notez que s' `Migrate()` appuie sur le `IMigrator` service, qui peut être utilisé pour des scénarios plus avancés.</span><span class="sxs-lookup"><span data-stu-id="72518-176">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="72518-177">Utilisez `myDbContext.GetInfrastructure().GetService<IMigrator>()` pour y accéder.</span><span class="sxs-lookup"><span data-stu-id="72518-177">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="72518-178">Réfléchissez soigneusement avant d’utiliser cette approche en production.</span><span class="sxs-lookup"><span data-stu-id="72518-178">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="72518-179">L’expérience a montré que la simplicité de cette stratégie de déploiement est dépensée par les problèmes qu’elle crée.</span><span class="sxs-lookup"><span data-stu-id="72518-179">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="72518-180">Envisagez de générer des scripts SQL à partir de migrations à la place.</span><span class="sxs-lookup"><span data-stu-id="72518-180">Consider generating SQL scripts from migrations instead.</span></span>
> * <span data-ttu-id="72518-181">N’appelez pas `EnsureCreated()` avant `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="72518-181">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="72518-182">`EnsureCreated()` ignore Migrations pour créer le schéma, ce qui entraîne l’échec de `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="72518-182">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
