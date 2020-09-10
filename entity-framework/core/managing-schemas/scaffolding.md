---
title: Ingénierie à rebours-EF Core
description: Rétroconception d’un modèle à partir d’une base de données existante à l’aide de Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: e88be834aa1f2f3be41657f07bc7ace3e34c58aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619215"
---
# <a name="reverse-engineering"></a> Reconstitution de la logique des produits

L’ingénierie à rebours est le processus de génération de modèles automatique des classes de type d’entité et une classe DbContext basée sur un schéma de base de données. Il peut être effectué à l’aide `Scaffold-DbContext` de la commande des outils de la console du gestionnaire de package (PMC) EF Core ou `dotnet ef dbcontext scaffold` de la commande des outils de l’interface de ligne de commande (CLI) .net.

## <a name="installing"></a>Installation de

Avant l’ingénierie à rebours, vous devez installer les [Outils PMC](xref:core/miscellaneous/cli/powershell) (Visual Studio uniquement) ou les [Outils CLI](xref:core/miscellaneous/cli/dotnet). Pour plus d’informations, consultez les liens.

Vous devez également installer un [fournisseur de base de données](xref:core/providers/index) approprié pour le schéma de base de données que vous souhaitez rétroconcevoir.

## <a name="connection-string"></a>Chaîne de connexion

Le premier argument de la commande est une chaîne de connexion à la base de données. Les outils utilisent cette chaîne de connexion pour lire le schéma de la base de données.

La façon dont vous utilisez les guillemets et les séquences d’échappement de la chaîne de connexion dépend du shell que vous utilisez pour exécuter la commande. Reportez-vous à la documentation de votre shell pour obtenir des informations spécifiques. Par exemple, PowerShell vous oblige à placer le caractère dans une séquence d’échappement `$` , mais pas `\` .

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a>Configuration et secrets de l’utilisateur

Si vous avez un projet ASP.NET Core, vous pouvez utiliser la `Name=<connection-string>` syntaxe pour lire la chaîne de connexion à partir de la configuration.

Cela fonctionne bien avec l' [outil secret Manager](/aspnet/core/security/app-secrets#secret-manager) pour conserver le mot de passe de votre base de données distinct de votre code base.

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>Nom du fournisseur

Le deuxième argument est le nom du fournisseur. Le nom du fournisseur est généralement identique au nom du package NuGet du fournisseur.

## <a name="specifying-tables"></a>Spécification de tables

Toutes les tables du schéma de base de données sont rétroconçues dans les types d’entités par défaut. Vous pouvez limiter les tables qui sont rétroconçues en spécifiant des schémas et des tables.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

L' `--schema` option peut être utilisée pour inclure chaque table dans un schéma, tandis que `--table` peut être utilisé pour inclure des tables spécifiques.

Pour inclure plusieurs tables, spécifiez l’option plusieurs fois :

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

L' `-Schemas` option peut être utilisée pour inclure chaque table dans un schéma, tandis que `-Tables` peut être utilisé pour inclure des tables spécifiques.

Pour inclure plusieurs tables, utilisez un tableau :

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

***

## <a name="preserving-names"></a>Préservation des noms

Les noms de table et de colonne sont fixes pour mieux correspondre aux conventions de nommage .NET pour les types et les propriétés par défaut. Si vous spécifiez le `-UseDatabaseNames` commutateur dans PMC ou l' `--use-database-names` option dans le CLI .net Core désactivez ce comportement en conservant autant que possible les noms de bases de données d’origine. Les identificateurs .NET non valides seront toujours fixes et les noms synthétisés, tels que les propriétés de navigation, seront toujours conformes aux conventions d’affectation de noms .NET.

## <a name="fluent-api-or-data-annotations"></a>API Fluent ou annotations de données

Par défaut, les types d’entités sont configurés à l’aide de l’API Fluent. Spécifiez `-DataAnnotations` (PMC) ou `--data-annotations` (CLI .net Core) pour utiliser à la place des annotations de données lorsque cela est possible.

Par exemple, l’utilisation de l’API Fluent entraîne l’échafaudage suivant :

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

Lorsque vous utilisez des annotations de données, vous générez une structure :

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>Nom de DbContext

Le nom de classe DbContext généré par génération de modèles automatique sera le nom de la base de données suffixée *par défaut* . Pour spécifier un autre, utilisez `-Context` dans PMC et `--context` dans le CLI .net core.

## <a name="directories-and-namespaces"></a>Répertoires et espaces de noms

Les classes d’entité et une classe DbContext sont intégrées au répertoire racine du projet et utilisent l’espace de noms par défaut du projet.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

Vous pouvez spécifier le répertoire dans lequel les classes sont échafaudées à l’aide de `--output-dir` , et `--context-dir` peut être utilisé pour générer un modèle de structure de la classe DbContext dans un répertoire distinct de celui des classes de type d’entité :

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

Par défaut, l’espace de noms est l’espace de noms racine, ainsi que les noms de tous les sous-répertoires sous le répertoire racine du projet. Toutefois, à partir de EFCore 5,0, vous pouvez remplacer l’espace de noms pour toutes les classes de sortie à l’aide de `--namespace` . Vous pouvez également remplacer l’espace de noms uniquement pour la classe DbContext à l’aide des `--context-namespace` éléments suivants :

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

Vous pouvez spécifier le répertoire dans lequel les classes sont échafaudées à l’aide de `-OutputDir` , et `-ContextDir` peut être utilisé pour générer un modèle de structure de la classe DbContext dans un répertoire distinct de celui des classes de type d’entité :

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

Par défaut, l’espace de noms est l’espace de noms racine, ainsi que les noms de tous les sous-répertoires sous le répertoire racine du projet. Toutefois, à partir de EFCore 5,0, vous pouvez remplacer l’espace de noms pour toutes les classes de sortie à l’aide de `-Namespace` . Vous pouvez également remplacer l’espace de noms uniquement pour la classe DbContext à l’aide de `-ContextNamespace` .

``` powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a>Fonctionnement

L’ingénierie à rebours commence par lire le schéma de base de données. Il lit les informations sur les tables, les colonnes, les contraintes et les index.

Ensuite, il utilise les informations de schéma pour créer un modèle de EF Core. Les tables sont utilisées pour créer des types d’entité ; les colonnes sont utilisées pour créer des propriétés. et les clés étrangères sont utilisées pour créer des relations.

Enfin, le modèle est utilisé pour générer le code. Les classes de type d’entité, l’API Fluent et les annotations de données correspondantes sont échafaudées afin de recréer le même modèle à partir de votre application.

## <a name="limitations"></a>Limites

* Tout ce qui concerne un modèle peut être représenté à l’aide d’un schéma de base de données. Par exemple, les informations sur les [**hiérarchies d’héritage**](xref:core/modeling/inheritance), les [**types détenus**](xref:core/modeling/owned-entities)et le [**fractionnement de table**](xref:core/modeling/table-splitting) ne sont pas présentes dans le schéma de la base de données. Pour cette raison, ces constructions ne feront jamais l’effet d’une rétroconception.
* En outre, **certains types de colonne** peuvent ne pas être pris en charge par le fournisseur EF Core. Ces colonnes ne sont pas incluses dans le modèle.
* Vous pouvez définir des [**jetons d’accès concurrentiel**](xref:core/modeling/concurrency), dans un modèle EF Core pour empêcher deux utilisateurs de mettre à jour la même entité en même temps. Certaines bases de données ont un type spécial pour représenter ce type de colonne (par exemple, rowversion dans SQL Server), auquel cas nous pouvons rétroconcevoir ces informations. Toutefois, les autres jetons d’accès concurrentiel ne feront pas l’être par rétroconception.
* [La fonctionnalité de type référence Nullable de C# 8](/dotnet/csharp/tutorials/nullable-reference-types) n’est actuellement pas prise en charge dans l’ingénierie à rebours : EF Core génère toujours du code C# qui suppose que la fonctionnalité est désactivée. Par exemple, les colonnes de texte Nullable seront échafaudées en tant que propriété de type `string` , et non `string?` , avec l’API Fluent ou les annotations de données utilisées pour configurer si une propriété est requise ou non. Vous pouvez modifier le code de génération de modèles automatique et les remplacer par des annotations de possibilité de valeur null. La prise en charge de la génération de modèles automatique pour les types de référence Nullable est suivie par le problème [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).

## <a name="customizing-the-model"></a>Personnalisation du modèle

Le code généré par EF Core est votre code. N’hésitez pas à la modifier. Elle sera régénérée uniquement si vous retrouvez à nouveau le même modèle. Le code de génération de modèles automatique représente *un* modèle qui peut être utilisé pour accéder à la base de données, mais ce n’est certainement pas le *seul* modèle qui peut être utilisé.

Personnalisez les classes de type d’entité et la classe DbContext en fonction de vos besoins. Par exemple, vous pouvez choisir de renommer des types et des propriétés, d’introduire des hiérarchies d’héritage ou de fractionner une table en plusieurs entités. Vous pouvez également supprimer des index non uniques, des séquences inutilisées et des propriétés de navigation, des propriétés scalaires facultatives et des noms de contrainte à partir du modèle.

Vous pouvez également ajouter des constructeurs, des méthodes, des propriétés, etc. supplémentaires. utilisation d’une autre classe partielle dans un fichier séparé. Cette approche fonctionne même lorsque vous envisagez de réactiver le modèle.

## <a name="updating-the-model"></a>Mise à jour du modèle

Après avoir apporté des modifications à la base de données, vous devrez peut-être mettre à jour votre modèle de EF Core pour refléter ces modifications. Si les modifications de la base de données sont simples, il peut être plus facile d’apporter manuellement les modifications à votre modèle de EF Core. Par exemple, le fait de renommer une table ou une colonne, de supprimer une colonne ou de mettre à jour le type d’une colonne est une modification triviale dans le code.

Toutefois, les modifications les plus importantes ne sont pas aussi faciles à effectuer manuellement. Un flux de travail courant consiste à rétroconcevoir à nouveau le modèle à partir de la base de données en utilisant `-Force` (PMC) ou `--force` (CLI) pour remplacer le modèle existant par un modèle mis à jour.

Une autre fonctionnalité couramment demandée est la possibilité de mettre à jour le modèle à partir de la base de données tout en préservant la personnalisation, comme les renommages, les hiérarchies de types, etc. Utilisez [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) de problème pour suivre la progression de cette fonctionnalité.

> [!WARNING]
> Si vous rérétroconcevez le modèle à partir de la base de données, toutes les modifications que vous avez apportées aux fichiers seront perdues.
