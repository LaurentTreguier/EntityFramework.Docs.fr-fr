---
title: Vue d’ensemble des migrations – EF Core
description: Vue d’ensemble de la gestion des schémas de la base de données avec Entity Framework Core à l’aide des migrations.
author: bricelam
ms.date: 10/28/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 60546d92704e0bb350e1df7f3bcc69171109b85b
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166070"
---
# <a name="migrations-overview"></a>Vue d’ensemble des migrations

Dans les projets réels, les modèles de données changent à mesure que les fonctionnalités sont implémentées : de nouvelles entités ou propriétés sont ajoutées et supprimées, et les schémas de base de données doivent être modifiés en conséquence afin d’être synchronisés avec l’application. La fonctionnalité de migration dans EF Core permet de mettre à jour de manière incrémentielle le schéma de la base de données pour qu’il reste synchronisé avec le modèle de données de l’application tout en conservant les données existantes dans la base de données.

Dans les grandes lignes, les migrations fonctionnent de la façon suivante :

* Lors de l’introduction d’une modification du modèle de données, le développeur utilise des outils EF Core afin d’ajouter une migration correspondante décrivant les mises à jour nécessaires pour préserver la synchronisation du schéma de la base de données. EF Core compare le modèle actuel à un instantané de l’ancien modèle pour déterminer les différences et génère des fichiers sources de migration. Ces fichiers peuvent, comme n’importe quel fichier source, faire l’objet d’un suivi dans le contrôle de code source du projet.
* Une fois générée, la nouvelle migration peut être appliquée à une base de données de différentes façons. EF Core enregistre toutes les migrations appliquées dans une table d’historique spéciale, ce qui lui permet de savoir quelles migrations ont été appliquées.

La suite de cette page est un guide pas à pas d’utilisation des migrations à destination des débutants. Pour plus d’informations, consultez les autres pages de cette section .

## <a name="getting-started"></a>Prise en main

Supposons que vous venez de terminer votre première application EF Core, qui contient le modèle simple suivant :

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

Pendant le développement, vous avez peut-être utilisé les [API de création et de suppression](xref:core/managing-schemas/ensure-created) pour effectuer des itérations rapides, en modifiant votre modèle selon les besoins. Cependant, maintenant que votre application est sur le point de passer en production, il vous faut un moyen de faire évoluer le schéma de manière sécurisée sans supprimer l’intégralité de la base de données.

### <a name="install-the-tools"></a>Installer les outils

Tout d’abord, vous devez installer les [outils en ligne de commande EF Core](xref:core/cli/index) :

* Nous recommandons généralement d’utiliser les [outils CLI .NET Core](xref:core/cli/dotnet), qui fonctionnent sur toutes les plateformes.
* Si vous avez l’habitude de travailler dans Visual Studio ou que vous connaissez les migrations EF6, vous pouvez également utiliser les [outils de la console du gestionnaire de package](xref:core/cli/powershell).

### <a name="create-your-first-migration"></a>Première création d’une migration

Il est temps d’ajouter votre première migration. Demandez à EF Core de créer une migration nommée **InitialCreate** :

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate
```

***

EF Core créera un répertoire nommé **Migrations** dans votre projet et générera des fichiers. Il est judicieux d’inspecter précisément les fichiers en question, et éventuellement de les modifier, mais nous allons nous en passer pour l’instant.

### <a name="create-your-database-and-schema"></a>Création d’une base de données et d’un schéma

Vous pouvez maintenant demander à EF de créer votre base de données et votre schéma à partir de la migration, par différents moyens :

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database
```

***

Votre application est prête à s’exécuter sur votre nouvelle base de données, sans que vous ayez eu à écrire une seule ligne de code SQL. Sachez que cette méthode d’application des migrations, idéale pour le développement local, est moins adaptée aux environnements de production. Pour plus d’informations, consultez la page [Application des migrations](xref:core/managing-schemas/migrations/applying).

### <a name="evolving-your-model"></a>Évolution du modèle

Au bout de quelques jours, il vous est demandé d’ajouter un timestamp de création à vos blogs. Vous avez apporté les modifications nécessaires à votre application. Votre modèle se présente maintenant ainsi :

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

Votre modèle et votre base de données de production ne sont plus synchronisés. Vous devez donc ajouter une nouvelle colonne au schéma de votre base de données. Créons pour cela une nouvelle migration :

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

Comme vous pouvez le constater, nous donnons aux migrations un nom descriptif pour faciliter par la suite la compréhension de l’historique du projet.

Comme il ne s’agit pas de la première migration du projet, EF Core compare maintenant votre modèle mis à jour à un instantané de l’ancien modèle avant ajout de la colonne, à savoir l’un des fichiers générés par EF Core à l’ajout d’une migration, archivé dans le contrôle de code source. Sur la base de cette comparaison, EF Core détecte qu’une colonne a été ajoutée et ajoute la migration correspondante.

Vous pouvez maintenant appliquer votre migration comme tout à l’heure :

<!--markdownlint-disable MD024-->

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database
```

<!--markdownlint-enable MD024-->

***

Cette fois, EF détecte que la base de données existe déjà. Par ailleurs, lors de l’application de la première migration, ce fait a été enregistré dans une table d’historique des migrations spéciale de la base de données. Ainsi, EF peut appliquer automatiquement la nouvelle migration uniquement.

### <a name="excluding-parts-of-your-model"></a>Exclusion de parties de votre modèle

> [!NOTE]
> Cette fonctionnalité a été introduite EF dans Core 5,0.

Parfois, vous pouvez faire référence à des types à partir d’un autre DbContext. Cela peut entraîner des conflits de migration. Pour éviter cela, excluez le type des migrations de l’un des DbContexts.

[!code-csharp[](../../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs#TableExcludeFromMigrations)]

### <a name="next-steps"></a>Étapes suivantes

Cette page ne constitue qu’une courte initiation aux migrations. Pour plus d’informations sur la [gestion des migrations](xref:core/managing-schemas/migrations/managing), leur [application](xref:core/managing-schemas/migrations/applying) et d’autres aspects, consultez les autres pages de la documentation. La [référence de l’outil CLI .NET Core](xref:core/cli/index) contient également des informations utiles sur les différentes commandes.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Entity Framework Core outils de référence-CLI .net Core](xref:core/cli/dotnet) : comprend des commandes pour mettre à jour, supprimer, ajouter, supprimer, etc.
* [Référence des outils de Entity Framework Core-console du gestionnaire de package dans Visual Studio](xref:core/cli/powershell) : comprend des commandes pour mettre à jour, supprimer, ajouter, supprimer, etc.
* [EF Core session réunions](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) de la Communauté, en passant par les nouvelles fonctionnalités de migration de EF Core 5,0.
