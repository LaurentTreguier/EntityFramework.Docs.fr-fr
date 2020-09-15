---
title: Application de migrations-EF Core
description: Stratégies d’application des migrations de schéma vers des bases de données de production et de développement à l’aide de Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: 50937f96e63f9bda2ffcf5e94dabf688ee2ba1f3
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071887"
---
# <a name="applying-migrations"></a><span data-ttu-id="2f5d1-103">Application de migrations</span><span class="sxs-lookup"><span data-stu-id="2f5d1-103">Applying Migrations</span></span>

<span data-ttu-id="2f5d1-104">Une fois vos migrations ajoutées, elles doivent être déployées et appliquées à vos bases de données.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-104">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="2f5d1-105">Il existe plusieurs stratégies pour y parvenir, dont certaines sont plus appropriées pour les environnements de production, et d’autres pour le cycle de vie du développement.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-105">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="2f5d1-106">Quelle que soit votre stratégie de déploiement, Inspectez toujours les migrations générées et testez-les avant de les appliquer à une base de données de production.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-106">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="2f5d1-107">Une migration peut supprimer une colonne lorsque l’intention est de la renommer ou peut échouer pour différentes raisons lorsqu’elle est appliquée à une base de données.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-107">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="2f5d1-108">Scripts SQL</span><span class="sxs-lookup"><span data-stu-id="2f5d1-108">SQL scripts</span></span>

<span data-ttu-id="2f5d1-109">La méthode recommandée pour déployer des migrations vers une base de données de production consiste à générer des scripts SQL.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-109">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="2f5d1-110">Les avantages de cette stratégie sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-110">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="2f5d1-111">La précision des scripts SQL peut être vérifiée ; Cela est important, car l’application de modifications de schéma aux bases de données de production est une opération potentiellement dangereuse qui peut impliquer une perte de données.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-111">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="2f5d1-112">Dans certains cas, les scripts peuvent être réglés pour répondre aux besoins spécifiques d’une base de données de production.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-112">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="2f5d1-113">Les scripts SQL peuvent être utilisés conjointement avec une technologie de déploiement et peuvent même être générés dans le cadre de votre processus d’intégration continue.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-113">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="2f5d1-114">Les scripts SQL peuvent être fournis à un administrateur de bases de donnée et peuvent être gérés et archivés séparément.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-114">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2f5d1-115">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f5d1-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="2f5d1-116">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="2f5d1-116">Basic Usage</span></span>

<span data-ttu-id="2f5d1-117">L’exemple suivant génère un script SQL à partir d’une base de données vide vers la dernière migration :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-117">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="2f5d1-118">Avec From (To implicite)</span><span class="sxs-lookup"><span data-stu-id="2f5d1-118">With From (to implied)</span></span>

<span data-ttu-id="2f5d1-119">L’exemple suivant génère un script SQL à partir de la migration donnée vers la dernière migration.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-119">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="2f5d1-120">Avec From et To</span><span class="sxs-lookup"><span data-stu-id="2f5d1-120">With From and To</span></span>

<span data-ttu-id="2f5d1-121">L’exemple suivant génère un script SQL à partir de la `from` migration spécifiée vers la `to` migration spécifiée.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-121">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="2f5d1-122">Vous pouvez utiliser un `from` plus récent que le `to` afin de générer un script de restauration.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-122">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="2f5d1-123">Prenez note des scénarios de pertes de données potentielles.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-123">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="2f5d1-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f5d1-124">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="2f5d1-125">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="2f5d1-125">Basic Usage</span></span>

<span data-ttu-id="2f5d1-126">L’exemple suivant génère un script SQL à partir d’une base de données vide vers la dernière migration :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-126">The following generates a SQL script from a blank database to the latest migration:</span></span>

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="2f5d1-127">Avec From (To implicite)</span><span class="sxs-lookup"><span data-stu-id="2f5d1-127">With From (to implied)</span></span>

<span data-ttu-id="2f5d1-128">L’exemple suivant génère un script SQL à partir de la migration donnée vers la dernière migration.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-128">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="2f5d1-129">Avec From et To</span><span class="sxs-lookup"><span data-stu-id="2f5d1-129">With From and To</span></span>

<span data-ttu-id="2f5d1-130">L’exemple suivant génère un script SQL à partir de la `from` migration spécifiée vers la `to` migration spécifiée.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-130">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```
<span data-ttu-id="2f5d1-131">Vous pouvez utiliser un `from` plus récent que le `to` afin de générer un script de restauration.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-131">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="2f5d1-132">*Prenez note des scénarios de pertes de données potentielles.*</span><span class="sxs-lookup"><span data-stu-id="2f5d1-132">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="2f5d1-133">La génération de script accepte les deux arguments suivants dans, indiquez la plage de migrations à générer :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-133">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="2f5d1-134">La migration **from** doit être la dernière migration appliquée à la base de données avant l’exécution du script.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-134">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="2f5d1-135">Si aucune migration n’a été appliquée, spécifiez `0` (il s’agit de la valeur par défaut).</span><span class="sxs-lookup"><span data-stu-id="2f5d1-135">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="2f5d1-136">La migration **to** est la dernière migration à appliquer à la base de données après l’exécution du script.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-136">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="2f5d1-137">Par défaut, il s’agit de la dernière migration dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-137">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="2f5d1-138">Scripts SQL idempotent</span><span class="sxs-lookup"><span data-stu-id="2f5d1-138">Idempotent SQL scripts</span></span>

<span data-ttu-id="2f5d1-139">Les scripts SQL générés ci-dessus ne peuvent être appliqués que pour modifier votre schéma d’une migration vers une autre. il vous incombe d’appliquer le script de manière appropriée et uniquement à la base de données dans l’état de migration approprié.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-139">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="2f5d1-140">EF Core prend également en charge la génération de scripts **idempotent** , qui vérifient en interne les migrations qui ont déjà été appliquées (par le biais de la table d’historique des migrations) et ne s’appliquent qu’à celles qui sont manquantes.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-140">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="2f5d1-141">Cela est utile si vous ne savez pas exactement quelle est la dernière migration appliquée à la base de données, ou si vous déployez sur plusieurs bases de données qui peuvent chacune être dans une migration différente.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-141">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="2f5d1-142">La commande suivante génère des migrations idempotent :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-142">The following generates idempotent migrations:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="2f5d1-143">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f5d1-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[<span data-ttu-id="2f5d1-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f5d1-144">Visual Studio</span></span>](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a><span data-ttu-id="2f5d1-145">Outils de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2f5d1-145">Command-line tools</span></span>

<span data-ttu-id="2f5d1-146">Les outils en ligne de commande EF peuvent être utilisés pour appliquer des migrations à une base de données.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-146">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="2f5d1-147">Bien qu’elle soit productive pour le développement et le test de migrations locaux, cette approche n’est pas idéale pour la gestion des bases de données de production :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-147">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

* <span data-ttu-id="2f5d1-148">Les commandes SQL sont appliquées directement par l’outil, sans donner aux développeurs la possibilité de les inspecter ou de les modifier.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-148">The SQL commands are applied directly by the tool, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="2f5d1-149">Cela peut être dangereux dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-149">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="2f5d1-150">Le kit de développement logiciel (SDK) .NET et l’outil EF doivent être installés sur les serveurs de production.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-150">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2f5d1-151">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f5d1-151">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="2f5d1-152">Le code suivant met à jour votre base de données avec la dernière migration :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-152">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="2f5d1-153">Le code suivant met à jour votre base de données vers une migration donnée :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-153">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="2f5d1-154">Notez que cela peut être utilisé pour revenir à une migration antérieure également.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-154">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="2f5d1-155">Prenez note des scénarios de pertes de données potentielles.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-155">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="2f5d1-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f5d1-156">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="2f5d1-157">Le code suivant met à jour votre base de données avec la dernière migration :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-157">The following updates your database to the latest migration:</span></span>

``` powershell
Update-Database
```

<span data-ttu-id="2f5d1-158">Le code suivant met à jour votre base de données vers une migration donnée :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-158">The following updates your database to a given migration:</span></span>

``` powershell
Update-Database AddNewTables
```

<span data-ttu-id="2f5d1-159">Notez que cela peut être utilisé pour revenir à une migration antérieure également.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-159">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="2f5d1-160">Prenez note des scénarios de pertes de données potentielles.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-160">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="2f5d1-161">Pour plus d’informations sur l’application des migrations à l’aide des outils en ligne de commande, consultez la référence sur les [outils de EF Core](xref:core/miscellaneous/cli/index).</span><span class="sxs-lookup"><span data-stu-id="2f5d1-161">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/miscellaneous/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="2f5d1-162">Appliquer des migrations au moment de l’exécution</span><span class="sxs-lookup"><span data-stu-id="2f5d1-162">Apply migrations at runtime</span></span>

<span data-ttu-id="2f5d1-163">Il est possible que l’application elle-même applique des migrations par programme, en général au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-163">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="2f5d1-164">Bien qu’elle soit productive pour le développement et le test de migrations locaux, cette approche n’est pas appropriée pour la gestion des bases de données de production, pour les raisons suivantes :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-164">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="2f5d1-165">Si plusieurs instances de votre application sont en cours d’exécution, les deux applications peuvent tenter d’appliquer la migration simultanément et échouer (ou pire, entraîner une altération des données).</span><span class="sxs-lookup"><span data-stu-id="2f5d1-165">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="2f5d1-166">De même, si une application accède à la base de données alors qu’une autre application la migre, cela peut entraîner de graves problèmes.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-166">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="2f5d1-167">L’application doit disposer d’un accès avec élévation de privilèges pour modifier le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-167">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="2f5d1-168">Il est généralement conseillé de limiter les autorisations de base de données de l’application en production.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-168">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="2f5d1-169">Il est important de pouvoir restaurer une migration appliquée en cas de problème.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-169">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="2f5d1-170">Les autres stratégies le fournissent facilement et en toute simplicité.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-170">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="2f5d1-171">Les commandes SQL sont appliquées directement par le programme, sans donner aux développeurs la possibilité de les inspecter ou de les modifier.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-171">The SQL commands are applied directly by the program, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="2f5d1-172">Cela peut être dangereux dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-172">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="2f5d1-173">Pour appliquer des migrations par programme, appelez `context.Database.Migrate()` .</span><span class="sxs-lookup"><span data-stu-id="2f5d1-173">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="2f5d1-174">Par exemple, une application ASP.NET classique peut effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="2f5d1-174">For example, a typical ASP.NET application can do the following:</span></span>

```c#
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

<span data-ttu-id="2f5d1-175">Notez que s' `Migrate()` appuie sur le `IMigrator` service, qui peut être utilisé pour des scénarios plus avancés.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-175">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="2f5d1-176">Utilisez `myDbContext.GetInfrastructure().GetService<IMigrator>()` pour y accéder.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-176">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="2f5d1-177">Réfléchissez soigneusement avant d’utiliser cette approche en production.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-177">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="2f5d1-178">L’expérience a montré que la simplicité de cette stratégie de déploiement est dépensée par les problèmes qu’elle crée.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-178">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="2f5d1-179">Envisagez plutôt d’utiliser des scripts SQL.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-179">Consider using SQL scripts instead.</span></span>
> * <span data-ttu-id="2f5d1-180">N’appelez pas `EnsureCreated()` avant `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-180">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="2f5d1-181">`EnsureCreated()` ignore Migrations pour créer le schéma, ce qui entraîne l’échec de `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="2f5d1-181">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
