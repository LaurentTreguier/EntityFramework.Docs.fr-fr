---
title: Outils et extensions - EF Core
description: Outils et extensions externes pour Entity Framework Core.
author: ErikEJ
ms.date: 02/21/2021
uid: core/extensions/index
ms.openlocfilehash: 5034454cc23e34d28b26ff28cccee818663376c6
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166116"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="82d6c-103">Outils et extensions EF Core</span><span class="sxs-lookup"><span data-stu-id="82d6c-103">EF Core Tools & Extensions</span></span>

<span data-ttu-id="82d6c-104">Ces outils et extensions fournissent des fonctionnalités supplémentaires pour Entity Framework Core 2.1 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="82d6c-104">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="82d6c-105">Les extensions sont générées par des sources diverses et ne sont pas gérées dans le cadre du projet Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="82d6c-105">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="82d6c-106">Quand vous envisagez une extension tierce, veillez à évaluer ses caractéristiques, notamment en termes de qualité, de gestion des licences, de compatibilité et de prise en charge, pour vérifier qu’elle répond à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="82d6c-106">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="82d6c-107">En particulier, une extension créée pour une version antérieure d’EF Core peut nécessiter une mise à jour avant de fonctionner avec les versions les plus récentes.</span><span class="sxs-lookup"><span data-stu-id="82d6c-107">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="82d6c-108">Outils</span><span class="sxs-lookup"><span data-stu-id="82d6c-108">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="82d6c-109">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="82d6c-109">LLBLGen Pro</span></span>

<span data-ttu-id="82d6c-110">LLBLGen Pro est une solution de modélisation d’entités qui prend en charge Entity Framework et Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="82d6c-110">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="82d6c-111">Il vous permet de définir facilement votre modèle d’entités et de le mapper à votre base de données, en utilisant Database First ou Model First : vous pouvez donc commencer à écrire des requêtes immédiatement.</span><span class="sxs-lookup"><span data-stu-id="82d6c-111">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="82d6c-112">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-112">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="82d6c-113">Site web</span><span class="sxs-lookup"><span data-stu-id="82d6c-113">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="82d6c-114">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="82d6c-114">Devart Entity Developer</span></span>

<span data-ttu-id="82d6c-115">Entity Developer est un Concepteur O/RM puissant pour ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access et LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="82d6c-115">Entity Developer is a powerful O/RM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="82d6c-116">Il prend en charge la conception visuelle de modèles EF Core, selon une approche Model First ou Database First, et la génération de code C# ou Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="82d6c-116">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="82d6c-117">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-117">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="82d6c-118">Site web</span><span class="sxs-lookup"><span data-stu-id="82d6c-118">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="82d6c-119">nHydrate ORM pour Entity Framework</span><span class="sxs-lookup"><span data-stu-id="82d6c-119">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="82d6c-120">Un O/RM qui crée des classes fortement typées et extensibles pour Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="82d6c-120">An O/RM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="82d6c-121">Le code généré est Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="82d6c-121">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="82d6c-122">Il n’y a aucune différence.</span><span class="sxs-lookup"><span data-stu-id="82d6c-122">There is no difference.</span></span> <span data-ttu-id="82d6c-123">Il ne s’agit pas d’un remplacement d’EF ou d’un O/RM personnalisé.</span><span class="sxs-lookup"><span data-stu-id="82d6c-123">This is not a replacement for EF or a custom O/RM.</span></span> <span data-ttu-id="82d6c-124">Il s’agit d’une couche de modélisation visuelle qui permet à une équipe de gérer des schémas de base de données complexes.</span><span class="sxs-lookup"><span data-stu-id="82d6c-124">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="82d6c-125">Il convient aux logiciels SCM tels que Git, permettant à plusieurs utilisateurs d’accéder à votre modèle avec un minimum de conflits.</span><span class="sxs-lookup"><span data-stu-id="82d6c-125">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="82d6c-126">Le programme d’installation effectue le suivi des modifications du modèle et crée des scripts de mise à niveau.</span><span class="sxs-lookup"><span data-stu-id="82d6c-126">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="82d6c-127">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-127">For EF Core: 3.</span></span>

[<span data-ttu-id="82d6c-128">Dépôt github</span><span class="sxs-lookup"><span data-stu-id="82d6c-128">Github repository</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="82d6c-129">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="82d6c-129">EF Core Power Tools</span></span>

<span data-ttu-id="82d6c-130">EF Core Power Tools est une extension Visual Studio qui expose différentes tâches EF Core au moment du design dans une interface utilisateur simple.</span><span class="sxs-lookup"><span data-stu-id="82d6c-130">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="82d6c-131">Elle inclut l’ingénierie à rebours des classes DbContext et d’entité à partir de bases de données existantes et des [packages DAC SQL Server](/sql/relational-databases/data-tier-applications/data-tier-applications), la gestion des migrations de base de données et les visualisations de modèles.</span><span class="sxs-lookup"><span data-stu-id="82d6c-131">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="82d6c-132">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-132">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-133">Wiki GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-133">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="82d6c-134">Entity Framework Visual Editor</span><span class="sxs-lookup"><span data-stu-id="82d6c-134">Entity Framework Visual Editor</span></span>

<span data-ttu-id="82d6c-135">Entity Framework éditeur visuel est une extension Visual Studio qui ajoute un Concepteur O/RM pour la conception visuelle d’EF 6 et des classes EF Core.</span><span class="sxs-lookup"><span data-stu-id="82d6c-135">Entity Framework Visual Editor is a Visual Studio extension that adds an O/RM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="82d6c-136">Le code étant généré à l’aide de modèles T4, il peut être personnalisé pour répondre à tous les besoins.</span><span class="sxs-lookup"><span data-stu-id="82d6c-136">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="82d6c-137">Il prend en charge les associations d’héritage, unidirectionnelles et bidirectionnelles, les énumérations ainsi que la possibilité de colorer le code de vos classes et d’ajouter des blocs de texte pour expliquer les parties potentiellement obscures de votre conception.</span><span class="sxs-lookup"><span data-stu-id="82d6c-137">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="82d6c-138">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-138">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="82d6c-139">Marketplace</span><span class="sxs-lookup"><span data-stu-id="82d6c-139">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="82d6c-140">CatFactory</span><span class="sxs-lookup"><span data-stu-id="82d6c-140">CatFactory</span></span>

<span data-ttu-id="82d6c-141">CatFactory est un moteur de génération de modèles automatique pour .NET Core qui peut automatiser la génération de classes DbContext, d’entités, de configurations de mappage et de classes de dépôts à partir d’une base de données SQL Server.</span><span class="sxs-lookup"><span data-stu-id="82d6c-141">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="82d6c-142">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="82d6c-142">For EF Core: 2.</span></span>

[<span data-ttu-id="82d6c-143">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-143">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="82d6c-144">Générateur Entity Framework Core de LoreSoft</span><span class="sxs-lookup"><span data-stu-id="82d6c-144">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="82d6c-145">Entity Framework Core Generator (efg) est un outil CLI .NET Core qui peut générer des modèles EF Core à partir d’une base de données existante, comme `dotnet ef dbcontext scaffold`, mais qui prend également en charge la [regénération](https://efg.loresoft.com/en/latest/regeneration/) de code safe à travers le remplacement de région ou l’analyse des fichiers de mappage.</span><span class="sxs-lookup"><span data-stu-id="82d6c-145">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="82d6c-146">Cet outil prend en charge la génération de code de mappeur d’objet, de validation et de modèles de vue.</span><span class="sxs-lookup"><span data-stu-id="82d6c-146">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="82d6c-147">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="82d6c-147">For EF Core: 2.</span></span>

<span data-ttu-id="82d6c-148">[Tutoriel](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="82d6c-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="82d6c-149">Extensions</span><span class="sxs-lookup"><span data-stu-id="82d6c-149">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="82d6c-150">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="82d6c-150">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="82d6c-151">Bibliothèque de plug-ins qui permet l’enregistrement automatique des changements de données effectués par EF Core dans une table d’historique.</span><span class="sxs-lookup"><span data-stu-id="82d6c-151">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="82d6c-152">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-152">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="82d6c-153">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-153">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="82d6c-154">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="82d6c-154">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="82d6c-155">La mise en cache de second niveau est un cache de requêtes.</span><span class="sxs-lookup"><span data-stu-id="82d6c-155">Second level caching is a query cache.</span></span> <span data-ttu-id="82d6c-156">Les résultats des commandes EF sont stockés dans le cache, de façon que les mêmes commandes EF récupèrent leurs données auprès du cache au lieu d’être réexécutées sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="82d6c-156">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="82d6c-157">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-157">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-158">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-158">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="82d6c-159">Geco</span><span class="sxs-lookup"><span data-stu-id="82d6c-159">Geco</span></span>

<span data-ttu-id="82d6c-160">Geco (Generator Console) est un générateur de code simple basé sur un projet de console qui s’exécute sur .NET Core et qui utilise des chaînes interpolées C# pour la génération de code.</span><span class="sxs-lookup"><span data-stu-id="82d6c-160">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="82d6c-161">Geco inclut un générateur de modèle inverse pour EF Core avec prise en charge de la pluralisation, de la singularisation et des modèles modifiables.</span><span class="sxs-lookup"><span data-stu-id="82d6c-161">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="82d6c-162">Il fournit également un générateur de script de données de départ, un exécuteur de scripts et un nettoyeur de base de données.</span><span class="sxs-lookup"><span data-stu-id="82d6c-162">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="82d6c-163">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="82d6c-163">For EF Core: 2.</span></span>

[<span data-ttu-id="82d6c-164">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-164">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="82d6c-165">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="82d6c-165">EntityFrameworkCore.Scaffolding.Handlebars</span></span>

<span data-ttu-id="82d6c-166">Permet de personnaliser des classes ayant fait l’objet d’une rétroconception à partir d’une base de données existante à l’aide de la chaîne d’outils Entity Framework Core avec des modèles Handlebars.</span><span class="sxs-lookup"><span data-stu-id="82d6c-166">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="82d6c-167">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-167">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="82d6c-168">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-168">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="82d6c-169">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="82d6c-169">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="82d6c-170">NeinLinq étend les fonctionnalités des fournisseurs LINQ comme Entity Framework pour permettre la réutilisation de fonctions, la réécriture des requêtes et la génération de requêtes dynamiques à l’aide de sélecteurs et de prédicats traduisibles.</span><span class="sxs-lookup"><span data-stu-id="82d6c-170">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="82d6c-171">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-171">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="82d6c-172">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-172">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="82d6c-173">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="82d6c-173">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="82d6c-174">Plug-in pour Microsoft.EntityFrameworkCore prenant en charge un dépôt, les modèles d’unités de travail et les bases de données multiples avec des transactions distribuées.</span><span class="sxs-lookup"><span data-stu-id="82d6c-174">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="82d6c-175">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-175">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="82d6c-176">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-176">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="82d6c-177">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="82d6c-177">EFCore.BulkExtensions</span></span>

<span data-ttu-id="82d6c-178">Extensions EF Core pour les opérations en bloc (Insert, Update, Delete).</span><span class="sxs-lookup"><span data-stu-id="82d6c-178">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="82d6c-179">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-179">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="82d6c-180">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-180">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="82d6c-181">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="82d6c-181">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="82d6c-182">Ajoute la pluralisation au moment du design.</span><span class="sxs-lookup"><span data-stu-id="82d6c-182">Adds design-time pluralization.</span></span> <span data-ttu-id="82d6c-183">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-183">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="82d6c-184">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-184">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="82d6c-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="82d6c-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="82d6c-186">Reprise de l’attribut [Index] (avec extension pour la création de modèles).</span><span class="sxs-lookup"><span data-stu-id="82d6c-186">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="82d6c-187">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-187">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="82d6c-188">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-188">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="verifyentityframework"></a><span data-ttu-id="82d6c-189">Verify. EntityFramework</span><span class="sxs-lookup"><span data-stu-id="82d6c-189">Verify.EntityFramework</span></span>

<span data-ttu-id="82d6c-190">Étend [verify](https://github.com/VerifyTests/Verify) pour autoriser les tests d’instantané avec EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="82d6c-190">Extends [Verify](https://github.com/VerifyTests/Verify) to allow snapshot testing with EntityFramework.</span></span> <span data-ttu-id="82d6c-191">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-191">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-192">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-192">GitHub repository</span></span>](https://github.com/VerifyTests/Verify.EntityFramework)

### <a name="localdb"></a><span data-ttu-id="82d6c-193">LocalDb</span><span class="sxs-lookup"><span data-stu-id="82d6c-193">LocalDb</span></span>

<span data-ttu-id="82d6c-194">Fournit un wrapper autour de [SQL Server Express](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb) base de données locale pour simplifier l’exécution de tests sur Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="82d6c-194">Provides a wrapper around [SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb) to simplify running tests against Entity Framework.</span></span> <span data-ttu-id="82d6c-195">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-195">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-196">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-196">GitHub repository</span></span>](https://github.com/SimonCropp/LocalDb)

### <a name="effluentvalidation"></a><span data-ttu-id="82d6c-197">EfFluentValidation</span><span class="sxs-lookup"><span data-stu-id="82d6c-197">EfFluentValidation</span></span>

<span data-ttu-id="82d6c-198">Ajoute la prise en charge de [FluentValidation](https://fluentvalidation.net/) à Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="82d6c-198">Adds [FluentValidation](https://fluentvalidation.net/) support to Entity Framework.</span></span> <span data-ttu-id="82d6c-199">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-199">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-200">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-200">GitHub repository</span></span>](https://github.com/SimonCropp/EfFluentValidation)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="82d6c-201">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="82d6c-201">EFCore.TemporalSupport</span></span>

<span data-ttu-id="82d6c-202">Implémentation de la prise en charge temporelle.</span><span class="sxs-lookup"><span data-stu-id="82d6c-202">An implementation of temporal support.</span></span> <span data-ttu-id="82d6c-203">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="82d6c-203">For EF Core: 2.</span></span>

[<span data-ttu-id="82d6c-204">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-204">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="82d6c-205">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="82d6c-205">EfCoreTemporalTable</span></span>

<span data-ttu-id="82d6c-206">Exécutez facilement des requêtes temporelles sur votre base de données favorite à l’aide de méthodes d’extension introduites : `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="82d6c-206">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="82d6c-207">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-207">For EF Core: 3.</span></span>

[<span data-ttu-id="82d6c-208">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-208">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="82d6c-209">EntityFrameworkCore. TemporalTables</span><span class="sxs-lookup"><span data-stu-id="82d6c-209">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="82d6c-210">Bibliothèque d’extensions pour Entity Framework Core qui permet aux développeurs qui utilisent SQL Server de se servir facilement des tables temporelles.</span><span class="sxs-lookup"><span data-stu-id="82d6c-210">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="82d6c-211">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-211">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="82d6c-212">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-212">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="82d6c-213">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="82d6c-213">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="82d6c-214">Cache des requêtes de second niveau hautes performances.</span><span class="sxs-lookup"><span data-stu-id="82d6c-214">A high-performance second-level query cache.</span></span> <span data-ttu-id="82d6c-215">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="82d6c-215">For EF Core: 2.</span></span>

[<span data-ttu-id="82d6c-216">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-216">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="82d6c-217">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="82d6c-217">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="82d6c-218">Le fournisseur d’Entity Framework Core NCache est un fournisseur de cache de second niveau distribué pour la mise en cache des résultats de requêtes.</span><span class="sxs-lookup"><span data-stu-id="82d6c-218">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="82d6c-219">L’architecture distribuée de NCache la rend plus évolutive et hautement disponible.</span><span class="sxs-lookup"><span data-stu-id="82d6c-219">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="82d6c-220">Par EF Core 2, 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-220">For EF Core 2, 3.</span></span>

[<span data-ttu-id="82d6c-221">Site web</span><span class="sxs-lookup"><span data-stu-id="82d6c-221">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entityframeworkcoretriggered"></a><span data-ttu-id="82d6c-222">EntityFrameworkCore. déclenché</span><span class="sxs-lookup"><span data-stu-id="82d6c-222">EntityFrameworkCore.Triggered</span></span>

<span data-ttu-id="82d6c-223">Déclencheurs pour EF Core.</span><span class="sxs-lookup"><span data-stu-id="82d6c-223">Triggers for EF Core.</span></span> <span data-ttu-id="82d6c-224">Répondez aux modifications dans votre DbContext avant et après qu’elles ont été validées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="82d6c-224">Respond to changes in your DbContext before and after they are committed to the database.</span></span> <span data-ttu-id="82d6c-225">Les déclencheurs sont entièrement asynchrones et prennent en charge l’injection de dépendances, l’héritage, la cascade et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="82d6c-225">Triggers are fully asynchronous and support dependency injection, inheritance, cascading and more.</span></span> <span data-ttu-id="82d6c-226">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-226">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-227">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-227">GitHub repository</span></span>](https://github.com/koenbeuk/EntityFrameworkCore.Triggered)

### <a name="entity-framework-plus"></a><span data-ttu-id="82d6c-228">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="82d6c-228">Entity Framework Plus</span></span>

<span data-ttu-id="82d6c-229">Étend votre DbContext avec des fonctionnalités telles que : Include Filter (Inclure le filtre), Auditing (Audit), Caching (Mise en cache), Query Future (Requête ultérieure), Batch Delete (Suppression par lot), Batch Update (Mise à jour par lot), et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="82d6c-229">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="82d6c-230">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-230">For EF Core: 2, 3, 5.</span></span>

<span data-ttu-id="82d6c-231">[Site web](https://entityframework-plus.net/)
[Référentiel GitHub](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="82d6c-231">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="82d6c-232">Extensions d’Entity Framework</span><span class="sxs-lookup"><span data-stu-id="82d6c-232">Entity Framework Extensions</span></span>

<span data-ttu-id="82d6c-233">Étend votre DbContext avec des opérations en bloc hautes performances : BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="82d6c-233">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="82d6c-234">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-234">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="82d6c-235">Site web</span><span class="sxs-lookup"><span data-stu-id="82d6c-235">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="82d6c-236">Expressionify</span><span class="sxs-lookup"><span data-stu-id="82d6c-236">Expressionify</span></span>

<span data-ttu-id="82d6c-237">Ajoutez la prise en charge de l’appel des méthodes d’extension dans les expressions lambda LINQ.</span><span class="sxs-lookup"><span data-stu-id="82d6c-237">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="82d6c-238">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-238">For EF Core: 3.</span></span>

[<span data-ttu-id="82d6c-239">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-239">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="elinq"></a><span data-ttu-id="82d6c-240">ELinq</span><span class="sxs-lookup"><span data-stu-id="82d6c-240">ELinq</span></span>

<span data-ttu-id="82d6c-241">Technologie LINQ (Language Integrated Query) pour les bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="82d6c-241">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="82d6c-242">Elle vous permet d’utiliser C# pour écrire des requêtes fortement typées.</span><span class="sxs-lookup"><span data-stu-id="82d6c-242">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="82d6c-243">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-243">For EF Core: 3.</span></span>

- <span data-ttu-id="82d6c-244">Prise en charge complète de C# pour la création de requêtes : plusieurs instructions dans une expression lambda, variables, fonctions, et ainsi de suite</span><span class="sxs-lookup"><span data-stu-id="82d6c-244">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="82d6c-245">Aucun écart sémantique avec SQL.</span><span class="sxs-lookup"><span data-stu-id="82d6c-245">No semantic gap with SQL.</span></span> <span data-ttu-id="82d6c-246">ELinq déclare des instructions SQL (comme `SELECT` , `FROM` , `WHERE` ) en tant que méthodes C# de première classe, en combinant la syntaxe familière avec IntelliSense, la cohérence des types et la refactorisation.</span><span class="sxs-lookup"><span data-stu-id="82d6c-246">ELinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="82d6c-247">Ainsi, SQL devient simplement « une autre » bibliothèque de classes qui expose son API localement, littéralement *« Language Integrated SQL »* .</span><span class="sxs-lookup"><span data-stu-id="82d6c-247">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="82d6c-248">Site web</span><span class="sxs-lookup"><span data-stu-id="82d6c-248">Website</span></span>](https://entitylinq.com/)

### <a name="ramses"></a><span data-ttu-id="82d6c-249">Ramses</span><span class="sxs-lookup"><span data-stu-id="82d6c-249">Ramses</span></span>

<span data-ttu-id="82d6c-250">Hooks de cycle de vie (pour SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="82d6c-250">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="82d6c-251">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-251">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="82d6c-252">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-252">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="82d6c-253">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="82d6c-253">EFCore.NamingConventions</span></span>

<span data-ttu-id="82d6c-254">Tous les noms de table et de colonne deviennent automatiquement snake_case, tout en MAJUSCULES ou tout en minuscules.</span><span class="sxs-lookup"><span data-stu-id="82d6c-254">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="82d6c-255">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-255">For EF Core: 3.</span></span>

[<span data-ttu-id="82d6c-256">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-256">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="82d6c-257">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="82d6c-257">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="82d6c-258">Ajoute une prise en charge native à EntityFrameworkCore pour SQL Server pour les types NodaTime.</span><span class="sxs-lookup"><span data-stu-id="82d6c-258">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="82d6c-259">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-259">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-260">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-260">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="82d6c-261">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="82d6c-261">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="82d6c-262">Extensions LINQ à Entity Framework Core 3.1 pour prendre en charge l’interrogation des tables temporelles Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="82d6c-262">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="82d6c-263">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-263">For EF Core: 3.</span></span>

[<span data-ttu-id="82d6c-264">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-264">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="82d6c-265">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="82d6c-265">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="82d6c-266">Ajoute la prise en charge de hierarchyid au fournisseur SQL Server EF Core.</span><span class="sxs-lookup"><span data-stu-id="82d6c-266">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="82d6c-267">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-267">For EF Core: 3.</span></span>

[<span data-ttu-id="82d6c-268">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-268">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="82d6c-269">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="82d6c-269">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="82d6c-270">Autre traducteur de requêtes LINQ pour les expressions SQL.</span><span class="sxs-lookup"><span data-stu-id="82d6c-270">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="82d6c-271">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-271">For EF Core: 3, 5.</span></span>

<span data-ttu-id="82d6c-272">Prend en charge les fonctionnalités SQL avancées, telles que les CTE, la copie en bloc, les indicateurs de table, les fonctions de fenêtrage, les tables temporaires et les opérations de création/mise à jour/suppression côté base de données.</span><span class="sxs-lookup"><span data-stu-id="82d6c-272">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="82d6c-273">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-273">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="82d6c-274">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="82d6c-274">EFCore.SoftDelete</span></span>

<span data-ttu-id="82d6c-275">Implémentation pour la suppression réversible d’entités.</span><span class="sxs-lookup"><span data-stu-id="82d6c-275">An implementation for soft deleting entities.</span></span> <span data-ttu-id="82d6c-276">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-276">For EF Core: 3.</span></span>

[<span data-ttu-id="82d6c-277">NuGet</span><span class="sxs-lookup"><span data-stu-id="82d6c-277">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a><span data-ttu-id="82d6c-278">EntityFrameworkCore.ConfigurationManager</span><span class="sxs-lookup"><span data-stu-id="82d6c-278">EntityFrameworkCore.ConfigurationManager</span></span>

<span data-ttu-id="82d6c-279">Étend EF Core pour résoudre les chaînes de connexion à partir de App.config. Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-279">Extends EF Core to resolve connection strings from App.config. For EF Core: 3.</span></span>

[<span data-ttu-id="82d6c-280">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-280">GitHub repository</span></span>](https://github.com/efcore/EFCore.ConfigurationManager)

### <a name="detached-mapper"></a><span data-ttu-id="82d6c-281">Mappeur détaché</span><span class="sxs-lookup"><span data-stu-id="82d6c-281">Detached Mapper</span></span>

<span data-ttu-id="82d6c-282">Mappeur DTO-Entity avec gestion de la composition/de l’agrégation (semblable à GraphDiff).</span><span class="sxs-lookup"><span data-stu-id="82d6c-282">A DTO-Entity mapper with composition/aggregation handling (similar to GraphDiff).</span></span> <span data-ttu-id="82d6c-283">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-283">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-284">NuGet</span><span class="sxs-lookup"><span data-stu-id="82d6c-284">NuGet</span></span>](https://www.nuget.org/packages/Detached.Mappers.EntityFramework)

### <a name="entityframeworkcoresqlitenodatime"></a><span data-ttu-id="82d6c-285">EntityFrameworkCore. sqlite. NodaTime</span><span class="sxs-lookup"><span data-stu-id="82d6c-285">EntityFrameworkCore.Sqlite.NodaTime</span></span>

<span data-ttu-id="82d6c-286">Ajoute la prise en charge des types [NodaTime](https://nodatime.org) lors de l’utilisation de [SQLite](https://sqlite.org).</span><span class="sxs-lookup"><span data-stu-id="82d6c-286">Adds support for [NodaTime](https://nodatime.org) types when using [SQLite](https://sqlite.org).</span></span> <span data-ttu-id="82d6c-287">Pour EF Core : 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-287">For EF Core: 5.</span></span>

[<span data-ttu-id="82d6c-288">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-288">GitHub repository</span></span>](https://github.com/khellang/EFCore.Sqlite.NodaTime)

### <a name="erikejentityframeworkcoresqlserverdacpac"></a><span data-ttu-id="82d6c-289">ErikEJ. EntityFrameworkCore. SqlServer. dacpac</span><span class="sxs-lookup"><span data-stu-id="82d6c-289">ErikEJ.EntityFrameworkCore.SqlServer.Dacpac</span></span>

<span data-ttu-id="82d6c-290">Active l’ingénierie à rebours d’un modèle de EF Core à partir d’un SQL Server package d’application de la couche données (. dacpac).</span><span class="sxs-lookup"><span data-stu-id="82d6c-290">Enables reverse engineering an EF Core model from a SQL Server data-tier application package (.dacpac).</span></span> <span data-ttu-id="82d6c-291">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-291">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-292">Wiki GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-292">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki/ErikEJ.EntityFrameworkCore.SqlServer.Dacpac)

### <a name="erikejentityframeworkcoredgmlbuilder"></a><span data-ttu-id="82d6c-293">ErikEJ. EntityFrameworkCore. DgmlBuilder</span><span class="sxs-lookup"><span data-stu-id="82d6c-293">ErikEJ.EntityFrameworkCore.DgmlBuilder</span></span>

<span data-ttu-id="82d6c-294">Générez du contenu DGML (Graph) qui visualise votre DbContext.</span><span class="sxs-lookup"><span data-stu-id="82d6c-294">Generate DGML (Graph) content that visualizes your DbContext.</span></span> <span data-ttu-id="82d6c-295">Ajoute la méthode d’extension AsDgml () à la classe DbContext.</span><span class="sxs-lookup"><span data-stu-id="82d6c-295">Adds the AsDgml() extension method to the DbContext class.</span></span> <span data-ttu-id="82d6c-296">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-296">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-297">Wiki GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-297">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki/Inspect-your-DbContext-model)

### <a name="entityframeworkexceptions"></a><span data-ttu-id="82d6c-298">EntityFramework. exceptions</span><span class="sxs-lookup"><span data-stu-id="82d6c-298">EntityFramework.Exceptions</span></span>

<span data-ttu-id="82d6c-299">Lorsque vous utilisez Entity Framework Core toutes les exceptions de base de données sont encapsulées dans exception dbupdateexception.</span><span class="sxs-lookup"><span data-stu-id="82d6c-299">When using Entity Framework Core all database exceptions are wrapped in DbUpdateException.</span></span> <span data-ttu-id="82d6c-300">EntityFramework. exceptions gère tous les détails spécifiques à la base de données pour rechercher la contrainte qui a été violée et vous permet d’utiliser des exceptions typées telles que `UniqueConstraintException` , `CannotInsertNullException` ,, `MaxLengthExceededException` `NumericOverflowException` , `ReferenceConstraintException` lorsque votre requête enfreint des contraintes de base de données.</span><span class="sxs-lookup"><span data-stu-id="82d6c-300">EntityFramework.Exceptions handles all the database-specific details to find which constraint was violated and allows you to use typed exceptions such as `UniqueConstraintException`, `CannotInsertNullException`, `MaxLengthExceededException`, `NumericOverflowException`, `ReferenceConstraintException` when your query violates database constraints.</span></span>

<span data-ttu-id="82d6c-301">Prend en charge SQL Server, Postgres, MySql, SQLite et Oracle</span><span class="sxs-lookup"><span data-stu-id="82d6c-301">Supports SQL Server, Postgres, MySql, SQLite and Oracle</span></span>

<span data-ttu-id="82d6c-302">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-302">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="82d6c-303">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-303">GitHub Repository</span></span>](https://github.com/Giorgi/EntityFramework.Exceptions)

### <a name="efcoreauditing"></a><span data-ttu-id="82d6c-304">EFCoreAuditing</span><span class="sxs-lookup"><span data-stu-id="82d6c-304">EFCoreAuditing</span></span>

<span data-ttu-id="82d6c-305">Bibliothèque pour Entity Framework Core prendre en charge l’enregistrement automatique de l’historique des modifications de données (journalisation d’audit), la suppression réversible et la fonctionnalité de convention d’affectation de noms snake_case.</span><span class="sxs-lookup"><span data-stu-id="82d6c-305">A Library for Entity Framework Core to support automatically recording data changes history (audit logging), soft-delete, and snake_case naming convention functionality.</span></span> <span data-ttu-id="82d6c-306">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="82d6c-306">For EF Core: 3.</span></span>

[<span data-ttu-id="82d6c-307">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-307">GitHub Repository</span></span>](https://github.com/OKTAYKIR/EFCoreAuditing)

### <a name="entityframeworkcorefsharp"></a><span data-ttu-id="82d6c-308">EntityFrameworkCore. FSharp</span><span class="sxs-lookup"><span data-stu-id="82d6c-308">EntityFrameworkCore.FSharp</span></span>

<span data-ttu-id="82d6c-309">Ajoute la prise en charge au moment du design F # à EF Core.</span><span class="sxs-lookup"><span data-stu-id="82d6c-309">Adds F# design-time support to EF Core.</span></span> <span data-ttu-id="82d6c-310">Pour EF Core : 5.</span><span class="sxs-lookup"><span data-stu-id="82d6c-310">For EF Core: 5.</span></span>

[<span data-ttu-id="82d6c-311">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="82d6c-311">GitHub repository</span></span>](https://github.com/efcore/EFCore.FSharp)
