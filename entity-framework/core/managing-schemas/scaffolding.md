---
title: Ingénierie à rebours-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 41204de8cd8c4f292afa16b209eb5302eaf74905
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538443"
---
# <a name="reverse-engineering"></a><span data-ttu-id="68146-102"> Reconstitution de la logique des produits</span><span class="sxs-lookup"><span data-stu-id="68146-102">Reverse Engineering</span></span>

<span data-ttu-id="68146-103">L’ingénierie à rebours est le processus de génération de modèles automatique des classes de type d’entité et une classe DbContext basée sur un schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="68146-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="68146-104">Il peut être effectué à l' `Scaffold-DbContext` aide de la commande des outils de la console du gestionnaire de package `dotnet ef dbcontext scaffold` (PMC) EF Core ou de la commande des outils de l’interface de ligne de commande (CLI) .net.</span><span class="sxs-lookup"><span data-stu-id="68146-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="68146-105">En cours d'installation</span><span class="sxs-lookup"><span data-stu-id="68146-105">Installing</span></span>

<span data-ttu-id="68146-106">Avant l’ingénierie à rebours, vous devez installer les [Outils PMC](xref:core/miscellaneous/cli/powershell) (Visual Studio uniquement) ou les [Outils CLI](xref:core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="68146-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="68146-107">Pour plus d’informations, consultez les liens.</span><span class="sxs-lookup"><span data-stu-id="68146-107">See links for details.</span></span>

<span data-ttu-id="68146-108">Vous devez également installer un [fournisseur de base de données](xref:core/providers/index) approprié pour le schéma de base de données que vous souhaitez rétroconcevoir.</span><span class="sxs-lookup"><span data-stu-id="68146-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="68146-109">Chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="68146-109">Connection string</span></span>

<span data-ttu-id="68146-110">Le premier argument de la commande est une chaîne de connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="68146-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="68146-111">Les outils utilisent cette chaîne de connexion pour lire le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="68146-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="68146-112">La façon dont vous utilisez les guillemets et les séquences d’échappement de la chaîne de connexion dépend du shell que vous utilisez pour exécuter la commande.</span><span class="sxs-lookup"><span data-stu-id="68146-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="68146-113">Reportez-vous à la documentation de votre shell pour obtenir des informations spécifiques.</span><span class="sxs-lookup"><span data-stu-id="68146-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="68146-114">Par exemple, PowerShell vous oblige à placer le `$` caractère dans une séquence `\`d’échappement, mais pas.</span><span class="sxs-lookup"><span data-stu-id="68146-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="68146-115">Configuration et secrets de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="68146-115">Configuration and User Secrets</span></span>

<span data-ttu-id="68146-116">Si vous avez un projet ASP.NET Core, vous pouvez utiliser la `Name=<connection-string>` syntaxe pour lire la chaîne de connexion à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="68146-116">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="68146-117">Cela fonctionne bien avec l' [outil secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) pour conserver le mot de passe de votre base de données distinct de votre code base.</span><span class="sxs-lookup"><span data-stu-id="68146-117">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="68146-118">Nom du fournisseur</span><span class="sxs-lookup"><span data-stu-id="68146-118">Provider name</span></span>

<span data-ttu-id="68146-119">Le deuxième argument est le nom du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="68146-119">The second argument is the provider name.</span></span> <span data-ttu-id="68146-120">Le nom du fournisseur est généralement identique au nom du package NuGet du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="68146-120">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="68146-121">Spécification de tables</span><span class="sxs-lookup"><span data-stu-id="68146-121">Specifying tables</span></span>

<span data-ttu-id="68146-122">Toutes les tables du schéma de base de données sont rétroconçues dans les types d’entités par défaut.</span><span class="sxs-lookup"><span data-stu-id="68146-122">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="68146-123">Vous pouvez limiter les tables qui sont rétroconçues en spécifiant des schémas et des tables.</span><span class="sxs-lookup"><span data-stu-id="68146-123">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

<span data-ttu-id="68146-124">Le `-Schemas` paramètre dans PMC et l' `--schema` option dans l’interface CLI peuvent être utilisés pour inclure chaque table dans un schéma.</span><span class="sxs-lookup"><span data-stu-id="68146-124">The `-Schemas` parameter in PMC and the `--schema` option in the CLI can be used to include every table within a schema.</span></span>

<span data-ttu-id="68146-125">`-Tables`(PMC) et `--table` (CLI) peuvent être utilisés pour inclure des tables spécifiques.</span><span class="sxs-lookup"><span data-stu-id="68146-125">`-Tables` (PMC) and `--table` (CLI) can be used to include specific tables.</span></span>

<span data-ttu-id="68146-126">Pour inclure plusieurs tables dans PMC, utilisez un tableau.</span><span class="sxs-lookup"><span data-stu-id="68146-126">To include multiple tables in PMC, use an array.</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="68146-127">Pour inclure plusieurs tables dans l’interface CLI, spécifiez l’option plusieurs fois.</span><span class="sxs-lookup"><span data-stu-id="68146-127">To include multiple tables in the CLI, specify the option multiple times.</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a><span data-ttu-id="68146-128">Préservation des noms</span><span class="sxs-lookup"><span data-stu-id="68146-128">Preserving names</span></span>

<span data-ttu-id="68146-129">Les noms de table et de colonne sont fixes pour mieux correspondre aux conventions de nommage .NET pour les types et les propriétés par défaut.</span><span class="sxs-lookup"><span data-stu-id="68146-129">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="68146-130">La spécification `-UseDatabaseNames` du commutateur dans PMC ou `--use-database-names` de l’option dans l’interface CLI désactivera ce comportement en conservant autant que possible les noms de bases de données originaux.</span><span class="sxs-lookup"><span data-stu-id="68146-130">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="68146-131">Les identificateurs .NET non valides seront toujours fixes et les noms synthétisés, tels que les propriétés de navigation, seront toujours conformes aux conventions d’affectation de noms .NET.</span><span class="sxs-lookup"><span data-stu-id="68146-131">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="68146-132">API Fluent ou annotations de données</span><span class="sxs-lookup"><span data-stu-id="68146-132">Fluent API or Data Annotations</span></span>

<span data-ttu-id="68146-133">Par défaut, les types d’entités sont configurés à l’aide de l’API Fluent.</span><span class="sxs-lookup"><span data-stu-id="68146-133">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="68146-134">Spécifiez `-DataAnnotations` (PMC) `--data-annotations` ou (CLI) pour utiliser à la place des annotations de données lorsque cela est possible.</span><span class="sxs-lookup"><span data-stu-id="68146-134">Specify `-DataAnnotations` (PMC) or `--data-annotations` (CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="68146-135">Par exemple, l’utilisation de l’API Fluent entraîne l’échafaudage suivant :</span><span class="sxs-lookup"><span data-stu-id="68146-135">For example, using the Fluent API will scaffold this:</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="68146-136">Lorsque vous utilisez des annotations de données, vous générez une structure :</span><span class="sxs-lookup"><span data-stu-id="68146-136">While using Data Annotations will scaffold this:</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="68146-137">Nom de DbContext</span><span class="sxs-lookup"><span data-stu-id="68146-137">DbContext name</span></span>

<span data-ttu-id="68146-138">Le nom de classe DbContext généré par génération de modèles automatique sera le nom de la base de données suffixée *par défaut* .</span><span class="sxs-lookup"><span data-stu-id="68146-138">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="68146-139">Pour spécifier un autre, utilisez `-Context` dans PMC et `--context` dans l’interface CLI.</span><span class="sxs-lookup"><span data-stu-id="68146-139">To specify a different one, use `-Context` in PMC and `--context` in the CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="68146-140">Répertoires et espaces de noms</span><span class="sxs-lookup"><span data-stu-id="68146-140">Directories and namespaces</span></span>

<span data-ttu-id="68146-141">Les classes d’entité et une classe DbContext sont intégrées au répertoire racine du projet et utilisent l’espace de noms par défaut du projet.</span><span class="sxs-lookup"><span data-stu-id="68146-141">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span> <span data-ttu-id="68146-142">Vous pouvez spécifier le répertoire dans lequel les classes sont échafaudées à l' `-OutputDir` aide `--output-dir` de (PMC) ou (CLI).</span><span class="sxs-lookup"><span data-stu-id="68146-142">You can specify the directory where classes are scaffolded using `-OutputDir` (PMC) or `--output-dir` (CLI).</span></span>

<span data-ttu-id="68146-143">Vous pouvez également utiliser `-ContextDir` (PMC) et `--context-dir` (CLI) pour générer une structure de la classe DbContext dans un répertoire distinct des classes de type d’entité.</span><span class="sxs-lookup"><span data-stu-id="68146-143">You can also use `-ContextDir` (PMC) and `--context-dir` (CLI) to scaffold the DbContext class into a separate directory from the entity type classes.</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

 <span data-ttu-id="68146-144">Par défaut, l’espace de noms est l’espace de noms racine, ainsi que les noms de tous les sous-répertoires sous le répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="68146-144">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="68146-145">Toutefois, vous pouvez remplacer l’espace de noms pour toutes les classes `-Namespace` de sortie à l' `--namespace` aide de (PMC) ou (CLI).</span><span class="sxs-lookup"><span data-stu-id="68146-145">However you can override the namespace for all output classes by using `-Namespace` (PMC) or `--namespace` (CLI).</span></span> <span data-ttu-id="68146-146">Vous pouvez également remplacer l’espace de noms uniquement pour la classe DbContext `-ContextNamespace` à l’aide de `--context-namespace` (PMC) ou (CLI).</span><span class="sxs-lookup"><span data-stu-id="68146-146">You can also override the namespace for just the DbContext class using `-ContextNamespace` (PMC) or `--context-namespace` (CLI).</span></span>

``` powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

## <a name="how-it-works"></a><span data-ttu-id="68146-147">Fonctionnement</span><span class="sxs-lookup"><span data-stu-id="68146-147">How it works</span></span>

<span data-ttu-id="68146-148">L’ingénierie à rebours commence par lire le schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="68146-148">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="68146-149">Il lit les informations sur les tables, les colonnes, les contraintes et les index.</span><span class="sxs-lookup"><span data-stu-id="68146-149">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="68146-150">Ensuite, il utilise les informations de schéma pour créer un modèle de EF Core.</span><span class="sxs-lookup"><span data-stu-id="68146-150">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="68146-151">Les tables sont utilisées pour créer des types d’entité ; les colonnes sont utilisées pour créer des propriétés. et les clés étrangères sont utilisées pour créer des relations.</span><span class="sxs-lookup"><span data-stu-id="68146-151">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="68146-152">Enfin, le modèle est utilisé pour générer le code.</span><span class="sxs-lookup"><span data-stu-id="68146-152">Finally, the model is used to generate code.</span></span> <span data-ttu-id="68146-153">Les classes de type d’entité, l’API Fluent et les annotations de données correspondantes sont échafaudées afin de recréer le même modèle à partir de votre application.</span><span class="sxs-lookup"><span data-stu-id="68146-153">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="68146-154">Limites</span><span class="sxs-lookup"><span data-stu-id="68146-154">Limitations</span></span>

* <span data-ttu-id="68146-155">Tout ce qui concerne un modèle peut être représenté à l’aide d’un schéma de base de données.</span><span class="sxs-lookup"><span data-stu-id="68146-155">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="68146-156">Par exemple, les informations sur les [**hiérarchies d’héritage**](../modeling/inheritance.md), les [**types détenus**](../modeling/owned-entities.md)et le [**fractionnement de table**](../modeling/table-splitting.md) ne sont pas présentes dans le schéma de la base de données.</span><span class="sxs-lookup"><span data-stu-id="68146-156">For example, information about [**inheritance hierarchies**](../modeling/inheritance.md), [**owned types**](../modeling/owned-entities.md), and [**table splitting**](../modeling/table-splitting.md) are not present in the database schema.</span></span> <span data-ttu-id="68146-157">Pour cette raison, ces constructions ne feront jamais l’effet d’une rétroconception.</span><span class="sxs-lookup"><span data-stu-id="68146-157">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="68146-158">En outre, **certains types de colonne** peuvent ne pas être pris en charge par le fournisseur EF Core.</span><span class="sxs-lookup"><span data-stu-id="68146-158">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="68146-159">Ces colonnes ne sont pas incluses dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="68146-159">These columns won't be included in the model.</span></span>
* <span data-ttu-id="68146-160">Vous pouvez définir des [**jetons d’accès concurrentiel**](../modeling/concurrency.md), dans un modèle EF Core pour empêcher deux utilisateurs de mettre à jour la même entité en même temps.</span><span class="sxs-lookup"><span data-stu-id="68146-160">You can define [**concurrency tokens**](../modeling/concurrency.md), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="68146-161">Certaines bases de données ont un type spécial pour représenter ce type de colonne (par exemple, rowversion dans SQL Server), auquel cas nous pouvons rétroconcevoir ces informations. Toutefois, les autres jetons d’accès concurrentiel ne feront pas l’être par rétroconception.</span><span class="sxs-lookup"><span data-stu-id="68146-161">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="68146-162">[La fonctionnalité de type référence Nullable de C# 8](/dotnet/csharp/tutorials/nullable-reference-types) n’est actuellement pas prise en charge dans l’ingénierie à rebours : EF Core génère toujours du code C# qui suppose que la fonctionnalité est désactivée.</span><span class="sxs-lookup"><span data-stu-id="68146-162">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="68146-163">Par exemple, les colonnes de texte Nullable seront échafaudées en tant que propriété `string` de type `string?`, et non, avec l’API Fluent ou les annotations de données utilisées pour configurer si une propriété est requise ou non.</span><span class="sxs-lookup"><span data-stu-id="68146-163">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="68146-164">Vous pouvez modifier le code de génération de modèles automatique et les remplacer par des annotations de possibilité de valeur null.</span><span class="sxs-lookup"><span data-stu-id="68146-164">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="68146-165">La prise en charge de la génération de modèles automatique pour les types de référence Nullable est suivie par le problème [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span><span class="sxs-lookup"><span data-stu-id="68146-165">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="68146-166">Personnalisation du modèle</span><span class="sxs-lookup"><span data-stu-id="68146-166">Customizing the model</span></span>

<span data-ttu-id="68146-167">Le code généré par EF Core est votre code.</span><span class="sxs-lookup"><span data-stu-id="68146-167">The code generated by EF Core is your code.</span></span> <span data-ttu-id="68146-168">N’hésitez pas à la modifier.</span><span class="sxs-lookup"><span data-stu-id="68146-168">Feel free to change it.</span></span> <span data-ttu-id="68146-169">Elle sera régénérée uniquement si vous retrouvez à nouveau le même modèle.</span><span class="sxs-lookup"><span data-stu-id="68146-169">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="68146-170">Le code de génération de modèles automatique représente *un* modèle qui peut être utilisé pour accéder à la base de données, mais ce n’est certainement pas le *seul* modèle qui peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="68146-170">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="68146-171">Personnalisez les classes de type d’entité et la classe DbContext en fonction de vos besoins.</span><span class="sxs-lookup"><span data-stu-id="68146-171">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="68146-172">Par exemple, vous pouvez choisir de renommer des types et des propriétés, d’introduire des hiérarchies d’héritage ou de fractionner une table en plusieurs entités.</span><span class="sxs-lookup"><span data-stu-id="68146-172">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="68146-173">Vous pouvez également supprimer des index non uniques, des séquences inutilisées et des propriétés de navigation, des propriétés scalaires facultatives et des noms de contrainte à partir du modèle.</span><span class="sxs-lookup"><span data-stu-id="68146-173">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="68146-174">Vous pouvez également ajouter des constructeurs, des méthodes, des propriétés, etc. supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="68146-174">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="68146-175">utilisation d’une autre classe partielle dans un fichier séparé.</span><span class="sxs-lookup"><span data-stu-id="68146-175">using another partial class in a separate file.</span></span> <span data-ttu-id="68146-176">Cette approche fonctionne même lorsque vous envisagez de réactiver le modèle.</span><span class="sxs-lookup"><span data-stu-id="68146-176">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="68146-177">Mise à jour du modèle</span><span class="sxs-lookup"><span data-stu-id="68146-177">Updating the model</span></span>

<span data-ttu-id="68146-178">Après avoir apporté des modifications à la base de données, vous devrez peut-être mettre à jour votre modèle de EF Core pour refléter ces modifications.</span><span class="sxs-lookup"><span data-stu-id="68146-178">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="68146-179">Si les modifications de la base de données sont simples, il peut être plus facile d’apporter manuellement les modifications à votre modèle de EF Core.</span><span class="sxs-lookup"><span data-stu-id="68146-179">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="68146-180">Par exemple, le fait de renommer une table ou une colonne, de supprimer une colonne ou de mettre à jour le type d’une colonne est une modification triviale dans le code.</span><span class="sxs-lookup"><span data-stu-id="68146-180">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="68146-181">Toutefois, les modifications les plus importantes ne sont pas aussi faciles à effectuer manuellement.</span><span class="sxs-lookup"><span data-stu-id="68146-181">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="68146-182">Un flux de travail courant consiste à rétroconcevoir à nouveau le modèle à partir `-Force` de la base de `--force` données en utilisant (PMC) ou (CLI) pour remplacer le modèle existant par un modèle mis à jour.</span><span class="sxs-lookup"><span data-stu-id="68146-182">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="68146-183">Une autre fonctionnalité couramment demandée est la possibilité de mettre à jour le modèle à partir de la base de données tout en préservant la personnalisation, comme les renommages, les hiérarchies de types, etc. Utilisez [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) de problème pour suivre la progression de cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="68146-183">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="68146-184">Si vous rérétroconcevez le modèle à partir de la base de données, toutes les modifications que vous avez apportées aux fichiers seront perdues.</span><span class="sxs-lookup"><span data-stu-id="68146-184">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
