---
title: Outils et extensions - EF Core
description: Outils et extensions externes pour Entity Framework Core.
author: ErikEJ
ms.date: 11/22/2020
uid: core/extensions/index
ms.openlocfilehash: 4136710567d0f6b088a0dd5ad9895d8b93313e5e
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635404"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="8aae9-103">Outils et extensions EF Core</span><span class="sxs-lookup"><span data-stu-id="8aae9-103">EF Core Tools & Extensions</span></span>

<span data-ttu-id="8aae9-104">Ces outils et extensions fournissent des fonctionnalités supplémentaires pour Entity Framework Core 2.1 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="8aae9-104">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="8aae9-105">Les extensions sont générées par des sources diverses et ne sont pas gérées dans le cadre du projet Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8aae9-105">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="8aae9-106">Quand vous envisagez une extension tierce, veillez à évaluer ses caractéristiques, notamment en termes de qualité, de gestion des licences, de compatibilité et de prise en charge, pour vérifier qu’elle répond à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="8aae9-106">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="8aae9-107">En particulier, une extension créée pour une version antérieure d’EF Core peut nécessiter une mise à jour avant de fonctionner avec les versions les plus récentes.</span><span class="sxs-lookup"><span data-stu-id="8aae9-107">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="8aae9-108">Outils</span><span class="sxs-lookup"><span data-stu-id="8aae9-108">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="8aae9-109">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="8aae9-109">LLBLGen Pro</span></span>

<span data-ttu-id="8aae9-110">LLBLGen Pro est une solution de modélisation d’entités qui prend en charge Entity Framework et Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8aae9-110">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="8aae9-111">Il vous permet de définir facilement votre modèle d’entités et de le mapper à votre base de données, en utilisant Database First ou Model First : vous pouvez donc commencer à écrire des requêtes immédiatement.</span><span class="sxs-lookup"><span data-stu-id="8aae9-111">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="8aae9-112">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-112">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8aae9-113">Site web</span><span class="sxs-lookup"><span data-stu-id="8aae9-113">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="8aae9-114">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="8aae9-114">Devart Entity Developer</span></span>

<span data-ttu-id="8aae9-115">Entity Developer est un Concepteur O/RM puissant pour ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access et LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="8aae9-115">Entity Developer is a powerful O/RM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="8aae9-116">Il prend en charge la conception visuelle de modèles EF Core, selon une approche Model First ou Database First, et la génération de code C# ou Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="8aae9-116">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="8aae9-117">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-117">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="8aae9-118">Site web</span><span class="sxs-lookup"><span data-stu-id="8aae9-118">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="8aae9-119">nHydrate ORM pour Entity Framework</span><span class="sxs-lookup"><span data-stu-id="8aae9-119">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="8aae9-120">Un O/RM qui crée des classes fortement typées et extensibles pour Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="8aae9-120">An O/RM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="8aae9-121">Le code généré est Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8aae9-121">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="8aae9-122">Il n’y a aucune différence.</span><span class="sxs-lookup"><span data-stu-id="8aae9-122">There is no difference.</span></span> <span data-ttu-id="8aae9-123">Il ne s’agit pas d’un remplacement d’EF ou d’un O/RM personnalisé.</span><span class="sxs-lookup"><span data-stu-id="8aae9-123">This is not a replacement for EF or a custom O/RM.</span></span> <span data-ttu-id="8aae9-124">Il s’agit d’une couche de modélisation visuelle qui permet à une équipe de gérer des schémas de base de données complexes.</span><span class="sxs-lookup"><span data-stu-id="8aae9-124">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="8aae9-125">Il convient aux logiciels SCM tels que Git, permettant à plusieurs utilisateurs d’accéder à votre modèle avec un minimum de conflits.</span><span class="sxs-lookup"><span data-stu-id="8aae9-125">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="8aae9-126">Le programme d’installation effectue le suivi des modifications du modèle et crée des scripts de mise à niveau.</span><span class="sxs-lookup"><span data-stu-id="8aae9-126">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="8aae9-127">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-127">For EF Core: 3.</span></span>

[<span data-ttu-id="8aae9-128">Site Github</span><span class="sxs-lookup"><span data-stu-id="8aae9-128">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="8aae9-129">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="8aae9-129">EF Core Power Tools</span></span>

<span data-ttu-id="8aae9-130">EF Core Power Tools est une extension Visual Studio qui expose différentes tâches EF Core au moment du design dans une interface utilisateur simple.</span><span class="sxs-lookup"><span data-stu-id="8aae9-130">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="8aae9-131">Elle inclut l’ingénierie à rebours des classes DbContext et d’entité à partir de bases de données existantes et des [packages DAC SQL Server](/sql/relational-databases/data-tier-applications/data-tier-applications), la gestion des migrations de base de données et les visualisations de modèles.</span><span class="sxs-lookup"><span data-stu-id="8aae9-131">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="8aae9-132">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-132">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="8aae9-133">Wiki GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-133">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="8aae9-134">Entity Framework Visual Editor</span><span class="sxs-lookup"><span data-stu-id="8aae9-134">Entity Framework Visual Editor</span></span>

<span data-ttu-id="8aae9-135">Entity Framework éditeur visuel est une extension Visual Studio qui ajoute un Concepteur O/RM pour la conception visuelle d’EF 6 et des classes EF Core.</span><span class="sxs-lookup"><span data-stu-id="8aae9-135">Entity Framework Visual Editor is a Visual Studio extension that adds an O/RM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="8aae9-136">Le code étant généré à l’aide de modèles T4, il peut être personnalisé pour répondre à tous les besoins.</span><span class="sxs-lookup"><span data-stu-id="8aae9-136">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="8aae9-137">Il prend en charge les associations d’héritage, unidirectionnelles et bidirectionnelles, les énumérations ainsi que la possibilité de colorer le code de vos classes et d’ajouter des blocs de texte pour expliquer les parties potentiellement obscures de votre conception.</span><span class="sxs-lookup"><span data-stu-id="8aae9-137">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="8aae9-138">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-138">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="8aae9-139">Marketplace</span><span class="sxs-lookup"><span data-stu-id="8aae9-139">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="8aae9-140">CatFactory</span><span class="sxs-lookup"><span data-stu-id="8aae9-140">CatFactory</span></span>

<span data-ttu-id="8aae9-141">CatFactory est un moteur de génération de modèles automatique pour .NET Core qui peut automatiser la génération de classes DbContext, d’entités, de configurations de mappage et de classes de dépôts à partir d’une base de données SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8aae9-141">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="8aae9-142">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="8aae9-142">For EF Core: 2.</span></span>

[<span data-ttu-id="8aae9-143">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-143">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="8aae9-144">Générateur Entity Framework Core de LoreSoft</span><span class="sxs-lookup"><span data-stu-id="8aae9-144">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="8aae9-145">Entity Framework Core Generator (efg) est un outil CLI .NET Core qui peut générer des modèles EF Core à partir d’une base de données existante, comme `dotnet ef dbcontext scaffold`, mais qui prend également en charge la [regénération](https://efg.loresoft.com/en/latest/regeneration/) de code safe à travers le remplacement de région ou l’analyse des fichiers de mappage.</span><span class="sxs-lookup"><span data-stu-id="8aae9-145">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="8aae9-146">Cet outil prend en charge la génération de code de mappeur d’objet, de validation et de modèles de vue.</span><span class="sxs-lookup"><span data-stu-id="8aae9-146">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="8aae9-147">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="8aae9-147">For EF Core: 2.</span></span>

<span data-ttu-id="8aae9-148">[Tutoriel](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="8aae9-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="8aae9-149">Extensions</span><span class="sxs-lookup"><span data-stu-id="8aae9-149">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="8aae9-150">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="8aae9-150">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="8aae9-151">Bibliothèque de plug-ins qui permet l’enregistrement automatique des changements de données effectués par EF Core dans une table d’historique.</span><span class="sxs-lookup"><span data-stu-id="8aae9-151">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="8aae9-152">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-152">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8aae9-153">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-153">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="8aae9-154">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="8aae9-154">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="8aae9-155">La mise en cache de second niveau est un cache de requêtes.</span><span class="sxs-lookup"><span data-stu-id="8aae9-155">Second level caching is a query cache.</span></span> <span data-ttu-id="8aae9-156">Les résultats des commandes EF sont stockés dans le cache, de façon que les mêmes commandes EF récupèrent leurs données auprès du cache au lieu d’être réexécutées sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="8aae9-156">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="8aae9-157">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-157">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="8aae9-158">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-158">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="8aae9-159">Geco</span><span class="sxs-lookup"><span data-stu-id="8aae9-159">Geco</span></span>

<span data-ttu-id="8aae9-160">Geco (Generator Console) est un générateur de code simple basé sur un projet de console qui s’exécute sur .NET Core et qui utilise des chaînes interpolées C# pour la génération de code.</span><span class="sxs-lookup"><span data-stu-id="8aae9-160">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="8aae9-161">Geco inclut un générateur de modèle inverse pour EF Core avec prise en charge de la pluralisation, de la singularisation et des modèles modifiables.</span><span class="sxs-lookup"><span data-stu-id="8aae9-161">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="8aae9-162">Il fournit également un générateur de script de données de départ, un exécuteur de scripts et un nettoyeur de base de données.</span><span class="sxs-lookup"><span data-stu-id="8aae9-162">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="8aae9-163">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="8aae9-163">For EF Core: 2.</span></span>

[<span data-ttu-id="8aae9-164">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-164">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="8aae9-165">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="8aae9-165">EntityFrameworkCore.Scaffolding.Handlebars</span></span>

<span data-ttu-id="8aae9-166">Permet de personnaliser des classes ayant fait l’objet d’une rétroconception à partir d’une base de données existante à l’aide de la chaîne d’outils Entity Framework Core avec des modèles Handlebars.</span><span class="sxs-lookup"><span data-stu-id="8aae9-166">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="8aae9-167">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-167">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="8aae9-168">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-168">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="8aae9-169">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="8aae9-169">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="8aae9-170">NeinLinq étend les fonctionnalités des fournisseurs LINQ comme Entity Framework pour permettre la réutilisation de fonctions, la réécriture des requêtes et la génération de requêtes dynamiques à l’aide de sélecteurs et de prédicats traduisibles.</span><span class="sxs-lookup"><span data-stu-id="8aae9-170">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="8aae9-171">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-171">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="8aae9-172">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-172">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="8aae9-173">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="8aae9-173">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="8aae9-174">Plug-in pour Microsoft.EntityFrameworkCore prenant en charge un dépôt, les modèles d’unités de travail et les bases de données multiples avec des transactions distribuées.</span><span class="sxs-lookup"><span data-stu-id="8aae9-174">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="8aae9-175">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-175">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8aae9-176">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-176">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="8aae9-177">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="8aae9-177">EFCore.BulkExtensions</span></span>

<span data-ttu-id="8aae9-178">Extensions EF Core pour les opérations en bloc (Insert, Update, Delete).</span><span class="sxs-lookup"><span data-stu-id="8aae9-178">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="8aae9-179">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-179">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8aae9-180">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-180">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="8aae9-181">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="8aae9-181">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="8aae9-182">Ajoute la pluralisation au moment du design.</span><span class="sxs-lookup"><span data-stu-id="8aae9-182">Adds design-time pluralization.</span></span> <span data-ttu-id="8aae9-183">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-183">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8aae9-184">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-184">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="8aae9-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="8aae9-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="8aae9-186">Reprise de l’attribut [Index] (avec extension pour la création de modèles).</span><span class="sxs-lookup"><span data-stu-id="8aae9-186">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="8aae9-187">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-187">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="8aae9-188">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-188">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="8aae9-189">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="8aae9-189">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="8aae9-190">Fournit un wrapper autour du fournisseur de base de données In-Memory EF Core.</span><span class="sxs-lookup"><span data-stu-id="8aae9-190">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="8aae9-191">Il fonctionne alors plus comme un fournisseur relationnel.</span><span class="sxs-lookup"><span data-stu-id="8aae9-191">Makes it act more like a relational provider.</span></span> <span data-ttu-id="8aae9-192">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="8aae9-192">For EF Core: 2.</span></span>

[<span data-ttu-id="8aae9-193">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-193">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="8aae9-194">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="8aae9-194">EFCore.TemporalSupport</span></span>

<span data-ttu-id="8aae9-195">Implémentation de la prise en charge temporelle.</span><span class="sxs-lookup"><span data-stu-id="8aae9-195">An implementation of temporal support.</span></span> <span data-ttu-id="8aae9-196">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="8aae9-196">For EF Core: 2.</span></span>

[<span data-ttu-id="8aae9-197">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-197">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="8aae9-198">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="8aae9-198">EfCoreTemporalTable</span></span>

<span data-ttu-id="8aae9-199">Exécutez facilement des requêtes temporelles sur votre base de données favorite à l’aide de méthodes d’extension introduites : `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="8aae9-199">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="8aae9-200">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-200">For EF Core: 3.</span></span>

[<span data-ttu-id="8aae9-201">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-201">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="8aae9-202">EntityFrameworkCore. TemporalTables</span><span class="sxs-lookup"><span data-stu-id="8aae9-202">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="8aae9-203">Bibliothèque d’extensions pour Entity Framework Core qui permet aux développeurs qui utilisent SQL Server de se servir facilement des tables temporelles.</span><span class="sxs-lookup"><span data-stu-id="8aae9-203">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="8aae9-204">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-204">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8aae9-205">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-205">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="8aae9-206">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="8aae9-206">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="8aae9-207">Cache des requêtes de second niveau hautes performances.</span><span class="sxs-lookup"><span data-stu-id="8aae9-207">A high-performance second-level query cache.</span></span> <span data-ttu-id="8aae9-208">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="8aae9-208">For EF Core: 2.</span></span>

[<span data-ttu-id="8aae9-209">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-209">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="8aae9-210">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="8aae9-210">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="8aae9-211">Le fournisseur d’Entity Framework Core NCache est un fournisseur de cache de second niveau distribué pour la mise en cache des résultats de requêtes.</span><span class="sxs-lookup"><span data-stu-id="8aae9-211">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="8aae9-212">L’architecture distribuée de NCache la rend plus évolutive et hautement disponible.</span><span class="sxs-lookup"><span data-stu-id="8aae9-212">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="8aae9-213">Par EF Core 2, 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-213">For EF Core 2, 3.</span></span>

[<span data-ttu-id="8aae9-214">Site web</span><span class="sxs-lookup"><span data-stu-id="8aae9-214">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entityframeworkcoretriggered"></a><span data-ttu-id="8aae9-215">EntityFrameworkCore. déclenché</span><span class="sxs-lookup"><span data-stu-id="8aae9-215">EntityFrameworkCore.Triggered</span></span>

<span data-ttu-id="8aae9-216">Déclencheurs pour EF Core.</span><span class="sxs-lookup"><span data-stu-id="8aae9-216">Triggers for EF Core.</span></span> <span data-ttu-id="8aae9-217">Répondez aux modifications dans votre DbContext avant et après qu’elles ont été validées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="8aae9-217">Respond to changes in your DbContext before and after they are committed to the database.</span></span> <span data-ttu-id="8aae9-218">Les déclencheurs sont entièrement asynchrones et prennent en charge l’injection de dépendances, l’héritage, la cascade et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="8aae9-218">Triggers are fully asynchronous and support dependency injection, inheritance, cascading and more.</span></span> <span data-ttu-id="8aae9-219">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-219">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="8aae9-220">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-220">GitHub repository</span></span>](https://github.com/koenbeuk/EntityFrameworkCore.Triggered)

### <a name="entity-framework-plus"></a><span data-ttu-id="8aae9-221">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="8aae9-221">Entity Framework Plus</span></span>

<span data-ttu-id="8aae9-222">Étend votre DbContext avec des fonctionnalités telles que : Include Filter (Inclure le filtre), Auditing (Audit), Caching (Mise en cache), Query Future (Requête ultérieure), Batch Delete (Suppression par lot), Batch Update (Mise à jour par lot), et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="8aae9-222">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="8aae9-223">Pour EF Core : 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-223">For EF Core: 2, 3, 5.</span></span>

<span data-ttu-id="8aae9-224">[Site web](https://entityframework-plus.net/)
[Référentiel GitHub](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="8aae9-224">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="8aae9-225">Extensions d’Entity Framework</span><span class="sxs-lookup"><span data-stu-id="8aae9-225">Entity Framework Extensions</span></span>

<span data-ttu-id="8aae9-226">Étend votre DbContext avec des opérations en bloc hautes performances : BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="8aae9-226">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="8aae9-227">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-227">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8aae9-228">Site web</span><span class="sxs-lookup"><span data-stu-id="8aae9-228">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="8aae9-229">Expressionify</span><span class="sxs-lookup"><span data-stu-id="8aae9-229">Expressionify</span></span>

<span data-ttu-id="8aae9-230">Ajoutez la prise en charge de l’appel des méthodes d’extension dans les expressions lambda LINQ.</span><span class="sxs-lookup"><span data-stu-id="8aae9-230">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="8aae9-231">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-231">For EF Core: 3.</span></span>

[<span data-ttu-id="8aae9-232">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-232">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="elinq"></a><span data-ttu-id="8aae9-233">ELinq</span><span class="sxs-lookup"><span data-stu-id="8aae9-233">ELinq</span></span>

<span data-ttu-id="8aae9-234">Technologie LINQ (Language Integrated Query) pour les bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="8aae9-234">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="8aae9-235">Elle vous permet d’utiliser C# pour écrire des requêtes fortement typées.</span><span class="sxs-lookup"><span data-stu-id="8aae9-235">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="8aae9-236">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-236">For EF Core: 3.</span></span>

- <span data-ttu-id="8aae9-237">Prise en charge complète de C# pour la création de requêtes : plusieurs instructions dans une expression lambda, variables, fonctions, et ainsi de suite</span><span class="sxs-lookup"><span data-stu-id="8aae9-237">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="8aae9-238">Aucun écart sémantique avec SQL.</span><span class="sxs-lookup"><span data-stu-id="8aae9-238">No semantic gap with SQL.</span></span> <span data-ttu-id="8aae9-239">ELinq déclare des instructions SQL (comme `SELECT` , `FROM` , `WHERE` ) en tant que méthodes C# de première classe, en combinant la syntaxe familière avec IntelliSense, la cohérence des types et la refactorisation.</span><span class="sxs-lookup"><span data-stu-id="8aae9-239">ELinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="8aae9-240">Ainsi, SQL devient simplement « une autre » bibliothèque de classes qui expose son API localement, littéralement *« Language Integrated SQL »* .</span><span class="sxs-lookup"><span data-stu-id="8aae9-240">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="8aae9-241">Site web</span><span class="sxs-lookup"><span data-stu-id="8aae9-241">Website</span></span>](https://entitylinq.com/)

### <a name="ramses"></a><span data-ttu-id="8aae9-242">Ramses</span><span class="sxs-lookup"><span data-stu-id="8aae9-242">Ramses</span></span>

<span data-ttu-id="8aae9-243">Hooks de cycle de vie (pour SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="8aae9-243">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="8aae9-244">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-244">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="8aae9-245">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-245">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="8aae9-246">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="8aae9-246">EFCore.NamingConventions</span></span>

<span data-ttu-id="8aae9-247">Tous les noms de table et de colonne deviennent automatiquement snake_case, tout en MAJUSCULES ou tout en minuscules.</span><span class="sxs-lookup"><span data-stu-id="8aae9-247">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="8aae9-248">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-248">For EF Core: 3.</span></span>

[<span data-ttu-id="8aae9-249">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-249">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="8aae9-250">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="8aae9-250">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="8aae9-251">Ajoute une prise en charge native à EntityFrameworkCore pour SQL Server pour les types NodaTime.</span><span class="sxs-lookup"><span data-stu-id="8aae9-251">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="8aae9-252">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-252">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="8aae9-253">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-253">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="8aae9-254">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="8aae9-254">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="8aae9-255">Extensions LINQ à Entity Framework Core 3.1 pour prendre en charge l’interrogation des tables temporelles Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8aae9-255">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="8aae9-256">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-256">For EF Core: 3.</span></span>

[<span data-ttu-id="8aae9-257">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-257">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="8aae9-258">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="8aae9-258">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="8aae9-259">Ajoute la prise en charge de hierarchyid au fournisseur SQL Server EF Core.</span><span class="sxs-lookup"><span data-stu-id="8aae9-259">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="8aae9-260">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-260">For EF Core: 3.</span></span>

[<span data-ttu-id="8aae9-261">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-261">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="8aae9-262">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="8aae9-262">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="8aae9-263">Autre traducteur de requêtes LINQ pour les expressions SQL.</span><span class="sxs-lookup"><span data-stu-id="8aae9-263">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="8aae9-264">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-264">For EF Core: 3, 5.</span></span>

<span data-ttu-id="8aae9-265">Prend en charge les fonctionnalités SQL avancées, telles que les CTE, la copie en bloc, les indicateurs de table, les fonctions de fenêtrage, les tables temporaires et les opérations de création/mise à jour/suppression côté base de données.</span><span class="sxs-lookup"><span data-stu-id="8aae9-265">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="8aae9-266">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-266">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="8aae9-267">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="8aae9-267">EFCore.SoftDelete</span></span>

<span data-ttu-id="8aae9-268">Implémentation pour la suppression réversible d’entités.</span><span class="sxs-lookup"><span data-stu-id="8aae9-268">An implementation for soft deleting entities.</span></span> <span data-ttu-id="8aae9-269">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-269">For EF Core: 3.</span></span>

[<span data-ttu-id="8aae9-270">NuGet</span><span class="sxs-lookup"><span data-stu-id="8aae9-270">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a><span data-ttu-id="8aae9-271">EntityFrameworkCore.ConfigurationManager</span><span class="sxs-lookup"><span data-stu-id="8aae9-271">EntityFrameworkCore.ConfigurationManager</span></span>

<span data-ttu-id="8aae9-272">Étend EF Core pour résoudre les chaînes de connexion à partir de App.config. Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="8aae9-272">Extends EF Core to resolve connection strings from App.config. For EF Core: 3.</span></span>

[<span data-ttu-id="8aae9-273">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="8aae9-273">GitHub repository</span></span>](https://github.com/efcore/EFCore.ConfigurationManager)

### <a name="detached-mapper"></a><span data-ttu-id="8aae9-274">Mappeur détaché</span><span class="sxs-lookup"><span data-stu-id="8aae9-274">Detached Mapper</span></span>

<span data-ttu-id="8aae9-275">Mappeur DTO-Entity avec gestion de la composition/de l’agrégation (semblable à GraphDiff).</span><span class="sxs-lookup"><span data-stu-id="8aae9-275">A DTO-Entity mapper with composition/aggregation handling (similar to GraphDiff).</span></span> <span data-ttu-id="8aae9-276">Pour EF Core : 3, 5.</span><span class="sxs-lookup"><span data-stu-id="8aae9-276">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="8aae9-277">NuGet</span><span class="sxs-lookup"><span data-stu-id="8aae9-277">NuGet</span></span>](https://www.nuget.org/packages/Detached.Mappers.EntityFramework)
