---
title: Outils et extensions - EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: f450742b761ef3daf35e32cf87c63a8ee8a7b8c3
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526405"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="9054b-102">Outils et extensions EF Core</span><span class="sxs-lookup"><span data-stu-id="9054b-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="9054b-103">Ces outils et extensions fournissent des fonctionnalités supplémentaires pour Entity Framework Core 2.1 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="9054b-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="9054b-104">Les extensions sont générées par des sources diverses et ne sont pas gérées dans le cadre du projet Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="9054b-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="9054b-105">Quand vous envisagez une extension tierce, veillez à évaluer ses caractéristiques, notamment en termes de qualité, de gestion des licences, de compatibilité et de prise en charge, pour vérifier qu’elle répond à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="9054b-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="9054b-106">En particulier, une extension créée pour une version antérieure d’EF Core peut nécessiter une mise à jour avant de fonctionner avec les versions les plus récentes.</span><span class="sxs-lookup"><span data-stu-id="9054b-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="9054b-107">Outils</span><span class="sxs-lookup"><span data-stu-id="9054b-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="9054b-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="9054b-108">LLBLGen Pro</span></span>

<span data-ttu-id="9054b-109">LLBLGen Pro est une solution de modélisation d’entités qui prend en charge Entity Framework et Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="9054b-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="9054b-110">Il vous permet de définir facilement votre modèle d’entités et de le mapper à votre base de données, en utilisant Database First ou Model First : vous pouvez donc commencer à écrire des requêtes immédiatement.</span><span class="sxs-lookup"><span data-stu-id="9054b-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="9054b-111">Pour EF Core : 2, 3</span><span class="sxs-lookup"><span data-stu-id="9054b-111">For EF Core: 2, 3</span></span>

[<span data-ttu-id="9054b-112">Site web</span><span class="sxs-lookup"><span data-stu-id="9054b-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="9054b-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="9054b-113">Devart Entity Developer</span></span>

<span data-ttu-id="9054b-114">Entity Developer est un concepteur ORM pour ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access et LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="9054b-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="9054b-115">Il prend en charge la conception visuelle de modèles EF Core, selon une approche Model First ou Database First, et la génération de code C# ou Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="9054b-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="9054b-116">Pour EF Core : 1, 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="9054b-116">For EF Core: 1, 2, 3, 5.</span></span>

[<span data-ttu-id="9054b-117">Site web</span><span class="sxs-lookup"><span data-stu-id="9054b-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="9054b-118">nHydrate ORM pour Entity Framework</span><span class="sxs-lookup"><span data-stu-id="9054b-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="9054b-119">ORM qui crée des classes fortement typées et extensibles pour Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="9054b-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="9054b-120">Le code généré est Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="9054b-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="9054b-121">Il n’y a aucune différence.</span><span class="sxs-lookup"><span data-stu-id="9054b-121">There is no difference.</span></span> <span data-ttu-id="9054b-122">Il ne s’agit pas d’un remplacement d’EF ni d’un ORM personnalisé.</span><span class="sxs-lookup"><span data-stu-id="9054b-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="9054b-123">Il s’agit d’une couche de modélisation visuelle qui permet à une équipe de gérer des schémas de base de données complexes.</span><span class="sxs-lookup"><span data-stu-id="9054b-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="9054b-124">Il convient aux logiciels SCM tels que Git, permettant à plusieurs utilisateurs d’accéder à votre modèle avec un minimum de conflits.</span><span class="sxs-lookup"><span data-stu-id="9054b-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="9054b-125">Le programme d’installation effectue le suivi des modifications du modèle et crée des scripts de mise à niveau.</span><span class="sxs-lookup"><span data-stu-id="9054b-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="9054b-126">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-126">For EF Core: 3.</span></span>

[<span data-ttu-id="9054b-127">Site Github</span><span class="sxs-lookup"><span data-stu-id="9054b-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="9054b-128">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="9054b-128">EF Core Power Tools</span></span>

<span data-ttu-id="9054b-129">EF Core Power Tools est une extension Visual Studio qui expose différentes tâches EF Core au moment du design dans une interface utilisateur simple.</span><span class="sxs-lookup"><span data-stu-id="9054b-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="9054b-130">Elle inclut l’ingénierie à rebours des classes DbContext et d’entité à partir de bases de données existantes et des [packages DAC SQL Server](/sql/relational-databases/data-tier-applications/data-tier-applications), la gestion des migrations de base de données et les visualisations de modèles.</span><span class="sxs-lookup"><span data-stu-id="9054b-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="9054b-131">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="9054b-132">Wiki GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="9054b-133">Entity Framework Visual Editor</span><span class="sxs-lookup"><span data-stu-id="9054b-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="9054b-134">Entity Framework Visual Editor est une extension de Visual Studio qui ajoute un concepteur ORM permettant de concevoir visuellement des classes EF 6 et EF Core.</span><span class="sxs-lookup"><span data-stu-id="9054b-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="9054b-135">Le code étant généré à l’aide de modèles T4, il peut être personnalisé pour répondre à tous les besoins.</span><span class="sxs-lookup"><span data-stu-id="9054b-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="9054b-136">Il prend en charge les associations d’héritage, unidirectionnelles et bidirectionnelles, les énumérations ainsi que la possibilité de colorer le code de vos classes et d’ajouter des blocs de texte pour expliquer les parties potentiellement obscures de votre conception.</span><span class="sxs-lookup"><span data-stu-id="9054b-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="9054b-137">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-137">For EF Core: 2.</span></span>

[<span data-ttu-id="9054b-138">Marketplace</span><span class="sxs-lookup"><span data-stu-id="9054b-138">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="9054b-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="9054b-139">CatFactory</span></span>

<span data-ttu-id="9054b-140">CatFactory est un moteur de génération de modèles automatique pour .NET Core qui peut automatiser la génération de classes DbContext, d’entités, de configurations de mappage et de classes de dépôts à partir d’une base de données SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9054b-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="9054b-141">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-141">For EF Core: 2.</span></span>

[<span data-ttu-id="9054b-142">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="9054b-143">Générateur Entity Framework Core de LoreSoft</span><span class="sxs-lookup"><span data-stu-id="9054b-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="9054b-144">Entity Framework Core Generator (efg) est un outil CLI .NET Core qui peut générer des modèles EF Core à partir d’une base de données existante, comme `dotnet ef dbcontext scaffold`, mais qui prend également en charge la [regénération](https://efg.loresoft.com/en/latest/regeneration/) de code safe à travers le remplacement de région ou l’analyse des fichiers de mappage.</span><span class="sxs-lookup"><span data-stu-id="9054b-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="9054b-145">Cet outil prend en charge la génération de code de mappeur d’objet, de validation et de modèles de vue.</span><span class="sxs-lookup"><span data-stu-id="9054b-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="9054b-146">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-146">For EF Core: 2.</span></span>

<span data-ttu-id="9054b-147">[Tutoriel](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="9054b-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="9054b-148">Extensions</span><span class="sxs-lookup"><span data-stu-id="9054b-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="9054b-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="9054b-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="9054b-150">Bibliothèque de plug-ins qui permet l’enregistrement automatique des changements de données effectués par EF Core dans une table d’historique.</span><span class="sxs-lookup"><span data-stu-id="9054b-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="9054b-151">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-151">For EF Core: 2.</span></span>

[<span data-ttu-id="9054b-152">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="9054b-153">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="9054b-153">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="9054b-154">La mise en cache de second niveau est un cache de requêtes.</span><span class="sxs-lookup"><span data-stu-id="9054b-154">Second level caching is a query cache.</span></span> <span data-ttu-id="9054b-155">Les résultats des commandes EF sont stockés dans le cache, de façon que les mêmes commandes EF récupèrent leurs données auprès du cache au lieu d’être réexécutées sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="9054b-155">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="9054b-156">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-156">For EF Core: 3.</span></span>

[<span data-ttu-id="9054b-157">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-157">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="9054b-158">Geco</span><span class="sxs-lookup"><span data-stu-id="9054b-158">Geco</span></span>

<span data-ttu-id="9054b-159">Geco (Generator Console) est un générateur de code simple basé sur un projet de console qui s’exécute sur .NET Core et qui utilise des chaînes interpolées C# pour la génération de code.</span><span class="sxs-lookup"><span data-stu-id="9054b-159">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="9054b-160">Geco inclut un générateur de modèle inverse pour EF Core avec prise en charge de la pluralisation, de la singularisation et des modèles modifiables.</span><span class="sxs-lookup"><span data-stu-id="9054b-160">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="9054b-161">Il fournit également un générateur de script de données de départ, un exécuteur de scripts et un nettoyeur de base de données.</span><span class="sxs-lookup"><span data-stu-id="9054b-161">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="9054b-162">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-162">For EF Core: 2.</span></span>

[<span data-ttu-id="9054b-163">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-163">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="9054b-164">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="9054b-164">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="9054b-165">Permet de personnaliser des classes ayant fait l’objet d’une rétroconception à partir d’une base de données existante à l’aide de la chaîne d’outils Entity Framework Core avec des modèles Handlebars.</span><span class="sxs-lookup"><span data-stu-id="9054b-165">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="9054b-166">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-166">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="9054b-167">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-167">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="9054b-168">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9054b-168">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="9054b-169">NeinLinq étend les fonctionnalités des fournisseurs LINQ comme Entity Framework pour permettre la réutilisation de fonctions, la réécriture des requêtes et la génération de requêtes dynamiques à l’aide de sélecteurs et de prédicats traduisibles.</span><span class="sxs-lookup"><span data-stu-id="9054b-169">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="9054b-170">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-170">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="9054b-171">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-171">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="9054b-172">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="9054b-172">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="9054b-173">Plug-in pour Microsoft.EntityFrameworkCore prenant en charge un dépôt, les modèles d’unités de travail et les bases de données multiples avec des transactions distribuées.</span><span class="sxs-lookup"><span data-stu-id="9054b-173">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="9054b-174">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-174">For EF Core: 2.</span></span>

[<span data-ttu-id="9054b-175">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-175">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="9054b-176">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="9054b-176">EFCore.BulkExtensions</span></span>

<span data-ttu-id="9054b-177">Extensions EF Core pour les opérations en bloc (Insert, Update, Delete).</span><span class="sxs-lookup"><span data-stu-id="9054b-177">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="9054b-178">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-178">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="9054b-179">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-179">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="9054b-180">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="9054b-180">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="9054b-181">Ajoute la pluralisation au moment du design.</span><span class="sxs-lookup"><span data-stu-id="9054b-181">Adds design-time pluralization.</span></span> <span data-ttu-id="9054b-182">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-182">For EF Core: 2.</span></span>

[<span data-ttu-id="9054b-183">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-183">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="9054b-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="9054b-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="9054b-185">Reprise de l’attribut [Index] (avec extension pour la création de modèles).</span><span class="sxs-lookup"><span data-stu-id="9054b-185">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="9054b-186">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-186">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="9054b-187">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-187">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="9054b-188">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="9054b-188">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="9054b-189">Fournit un wrapper autour du fournisseur de base de données In-Memory EF Core.</span><span class="sxs-lookup"><span data-stu-id="9054b-189">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="9054b-190">Il fonctionne alors plus comme un fournisseur relationnel.</span><span class="sxs-lookup"><span data-stu-id="9054b-190">Makes it act more like a relational provider.</span></span> <span data-ttu-id="9054b-191">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-191">For EF Core: 2.</span></span>

[<span data-ttu-id="9054b-192">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-192">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="9054b-193">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="9054b-193">EFCore.TemporalSupport</span></span>

<span data-ttu-id="9054b-194">Implémentation de la prise en charge temporelle.</span><span class="sxs-lookup"><span data-stu-id="9054b-194">An implementation of temporal support.</span></span> <span data-ttu-id="9054b-195">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-195">For EF Core: 2.</span></span>

[<span data-ttu-id="9054b-196">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-196">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="9054b-197">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="9054b-197">EfCoreTemporalTable</span></span>

<span data-ttu-id="9054b-198">Exécutez facilement des requêtes temporelles sur votre base de données favorite à l’aide de méthodes d’extension introduites : `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="9054b-198">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="9054b-199">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-199">For EF Core: 3.</span></span>

[<span data-ttu-id="9054b-200">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-200">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="9054b-201">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="9054b-201">EFCore.TimeTraveler</span></span>

<span data-ttu-id="9054b-202">Autorisez des requêtes Entity Framework Core complètes sur [la table d’historique temporelle SQL Server](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) à l’aide du code, des entités et des mappages EF Core que vous avez déjà définis.</span><span class="sxs-lookup"><span data-stu-id="9054b-202">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="9054b-203">Voyagez dans le temps en encapsulant votre code dans `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span><span class="sxs-lookup"><span data-stu-id="9054b-203">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="9054b-204">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-204">For EF Core: 3.</span></span>

[<span data-ttu-id="9054b-205">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-205">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="9054b-206">EntityFrameworkCore. TemporalTables</span><span class="sxs-lookup"><span data-stu-id="9054b-206">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="9054b-207">Bibliothèque d’extensions pour Entity Framework Core qui permet aux développeurs qui utilisent SQL Server de se servir facilement des tables temporelles.</span><span class="sxs-lookup"><span data-stu-id="9054b-207">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="9054b-208">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-208">For EF Core: 2.</span></span>

[<span data-ttu-id="9054b-209">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-209">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="9054b-210">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="9054b-210">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="9054b-211">Cache des requêtes de second niveau hautes performances.</span><span class="sxs-lookup"><span data-stu-id="9054b-211">A high-performance second-level query cache.</span></span> <span data-ttu-id="9054b-212">Pour EF Core : 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-212">For EF Core: 2.</span></span>

[<span data-ttu-id="9054b-213">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-213">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="9054b-214">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="9054b-214">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="9054b-215">Le fournisseur d’Entity Framework Core NCache est un fournisseur de cache de second niveau distribué pour la mise en cache des résultats de requêtes.</span><span class="sxs-lookup"><span data-stu-id="9054b-215">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="9054b-216">L’architecture distribuée de NCache la rend plus évolutive et hautement disponible.</span><span class="sxs-lookup"><span data-stu-id="9054b-216">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="9054b-217">Pour EF Core 2.</span><span class="sxs-lookup"><span data-stu-id="9054b-217">For EF Core 2.</span></span>

[<span data-ttu-id="9054b-218">Site web</span><span class="sxs-lookup"><span data-stu-id="9054b-218">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entity-framework-plus"></a><span data-ttu-id="9054b-219">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="9054b-219">Entity Framework Plus</span></span>

<span data-ttu-id="9054b-220">Étend votre DbContext avec des fonctionnalités telles que : Include Filter (Inclure le filtre), Auditing (Audit), Caching (Mise en cache), Query Future (Requête ultérieure), Batch Delete (Suppression par lot), Batch Update (Mise à jour par lot), et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="9054b-220">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="9054b-221">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-221">For EF Core: 2, 3.</span></span>

<span data-ttu-id="9054b-222">[Site web](https://entityframework-plus.net/)
[Référentiel GitHub](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="9054b-222">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="9054b-223">Extensions d’Entity Framework</span><span class="sxs-lookup"><span data-stu-id="9054b-223">Entity Framework Extensions</span></span>

<span data-ttu-id="9054b-224">Étend votre DbContext avec des opérations en bloc hautes performances : BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="9054b-224">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="9054b-225">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-225">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="9054b-226">Site web</span><span class="sxs-lookup"><span data-stu-id="9054b-226">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="9054b-227">Expressionify</span><span class="sxs-lookup"><span data-stu-id="9054b-227">Expressionify</span></span>

<span data-ttu-id="9054b-228">Ajoutez la prise en charge de l’appel des méthodes d’extension dans les expressions lambda LINQ.</span><span class="sxs-lookup"><span data-stu-id="9054b-228">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="9054b-229">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-229">For EF Core: 3.</span></span>

[<span data-ttu-id="9054b-230">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-230">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="9054b-231">XLinq</span><span class="sxs-lookup"><span data-stu-id="9054b-231">XLinq</span></span>

<span data-ttu-id="9054b-232">Technologie LINQ (Language Integrated Query) pour les bases de données relationnelles.</span><span class="sxs-lookup"><span data-stu-id="9054b-232">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="9054b-233">Elle vous permet d’utiliser C# pour écrire des requêtes fortement typées.</span><span class="sxs-lookup"><span data-stu-id="9054b-233">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="9054b-234">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-234">For EF Core: 3.</span></span>

- <span data-ttu-id="9054b-235">Prise en charge complète de C# pour la création de requêtes : plusieurs instructions dans une expression lambda, variables, fonctions, et ainsi de suite</span><span class="sxs-lookup"><span data-stu-id="9054b-235">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="9054b-236">Aucun écart sémantique avec SQL.</span><span class="sxs-lookup"><span data-stu-id="9054b-236">No semantic gap with SQL.</span></span> <span data-ttu-id="9054b-237">XLinq déclare les instructions SQL (comme `SELECT`, `FROM` et `WHERE`) en tant que méthodes C# de première classe, combinant la syntaxe familière avec IntelliSense, la cohérence des types et la refactorisation.</span><span class="sxs-lookup"><span data-stu-id="9054b-237">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="9054b-238">Ainsi, SQL devient simplement « une autre » bibliothèque de classes qui expose son API localement, littéralement *« Language Integrated SQL »* .</span><span class="sxs-lookup"><span data-stu-id="9054b-238">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="9054b-239">Site web</span><span class="sxs-lookup"><span data-stu-id="9054b-239">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="9054b-240">Ramses</span><span class="sxs-lookup"><span data-stu-id="9054b-240">Ramses</span></span>

<span data-ttu-id="9054b-241">Hooks de cycle de vie (pour SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="9054b-241">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="9054b-242">Pour EF Core : 2, 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-242">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="9054b-243">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-243">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="9054b-244">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="9054b-244">EFCore.NamingConventions</span></span>

<span data-ttu-id="9054b-245">Tous les noms de table et de colonne deviennent automatiquement snake_case, tout en MAJUSCULES ou tout en minuscules.</span><span class="sxs-lookup"><span data-stu-id="9054b-245">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="9054b-246">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-246">For EF Core: 3.</span></span>

[<span data-ttu-id="9054b-247">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-247">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="9054b-248">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="9054b-248">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="9054b-249">Ajoute une prise en charge native à EntityFrameworkCore pour SQL Server pour les types NodaTime.</span><span class="sxs-lookup"><span data-stu-id="9054b-249">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="9054b-250">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-250">For EF Core: 3.</span></span>

[<span data-ttu-id="9054b-251">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-251">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="9054b-252">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="9054b-252">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="9054b-253">Extensions LINQ à Entity Framework Core 3.1 pour prendre en charge l’interrogation des tables temporelles Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9054b-253">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="9054b-254">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-254">For EF Core: 3.</span></span>

[<span data-ttu-id="9054b-255">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-255">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="9054b-256">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="9054b-256">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="9054b-257">Ajoute la prise en charge de hierarchyid au fournisseur SQL Server EF Core.</span><span class="sxs-lookup"><span data-stu-id="9054b-257">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="9054b-258">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-258">For EF Core: 3.</span></span>

[<span data-ttu-id="9054b-259">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-259">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="9054b-260">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9054b-260">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="9054b-261">Autre traducteur de requêtes LINQ pour les expressions SQL.</span><span class="sxs-lookup"><span data-stu-id="9054b-261">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="9054b-262">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-262">For EF Core: 3.</span></span>

<span data-ttu-id="9054b-263">Prend en charge les fonctionnalités SQL avancées, telles que les CTE, la copie en bloc, les indicateurs de table, les fonctions de fenêtrage, les tables temporaires et les opérations de création/mise à jour/suppression côté base de données.</span><span class="sxs-lookup"><span data-stu-id="9054b-263">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="9054b-264">Dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="9054b-264">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="9054b-265">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="9054b-265">EFCore.SoftDelete</span></span>

<span data-ttu-id="9054b-266">Implémentation pour la suppression réversible d’entités.</span><span class="sxs-lookup"><span data-stu-id="9054b-266">An implementation for soft deleting entities.</span></span> <span data-ttu-id="9054b-267">Pour EF Core : 3.</span><span class="sxs-lookup"><span data-stu-id="9054b-267">For EF Core: 3.</span></span>

[<span data-ttu-id="9054b-268">NuGet</span><span class="sxs-lookup"><span data-stu-id="9054b-268">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)
