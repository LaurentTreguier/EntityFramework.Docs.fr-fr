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
# <a name="migrations-overview"></a>Vue d’ensemble des migrations

Dans les projets réels, les modèles de données changent à mesure que les fonctionnalités sont implémentées : de nouvelles entités ou propriétés sont ajoutées et supprimées, et les schémas de base de données doivent être modifiés en conséquence afin d’être synchronisés avec l’application. La fonctionnalité de migration dans EF Core permet de mettre à jour de manière incrémentielle le schéma de la base de données pour qu’il reste synchronisé avec le modèle de données de l’application tout en conservant les données existantes dans la base de données.

À un niveau élevé, les migrations fonctionnent de la façon suivante :

* Lors de l’introduction d’une modification de modèle de données, le développeur utilise EF Core outils pour ajouter une migration correspondante décrivant les mises à jour nécessaires pour maintenir la synchronisation du schéma de base de données. EF Core compare le modèle actuel par rapport à un instantané de l’ancien modèle pour déterminer les différences et génère des fichiers sources de migration ; les fichiers peuvent être suivis dans le contrôle de code source de votre projet comme tout autre fichier source.
* Une fois qu’une nouvelle migration a été générée, elle peut être appliquée à une base de données de différentes façons. EF Core enregistre toutes les migrations appliquées dans une table d’historique spéciale, ce qui lui permet de savoir quelles migrations ont été appliquées.

Le reste de cette page est un guide pas à pas pour le débutant en vue de l’utilisation des migrations. Consultez les autres pages de cette section pour obtenir des informations plus approfondies.

## <a name="getting-started"></a>Prise en main

Supposons que vous venez de terminer votre première EF Core application, qui contient le modèle simple suivant :

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

Pendant le développement, vous avez peut-être utilisé les [API de création et de suppression](xref:core/managing-schemas/ensure-created) pour itérer rapidement, en modifiant votre modèle si nécessaire. mais maintenant que votre application va passer en production, vous devez disposer d’un moyen de faire évoluer le schéma en toute sécurité sans supprimer l’intégralité de la base de données.

### <a name="install-the-tools"></a>Installer les outils

Tout d’abord, vous devez installer les [outils en ligne de commande EF Core](xref:core/miscellaneous/cli/index):

* Nous recommandons généralement d’utiliser les [outils de CLI .net Core](xref:core/miscellaneous/cli/dotnet), qui fonctionnent sur toutes les plateformes.
* Si vous avez l’habitude de travailler dans Visual Studio ou si vous avez des difficultés avec les migrations EF6, vous pouvez également utiliser les outils de la [console du gestionnaire de package](xref:core/miscellaneous/cli/powershell).

### <a name="create-your-first-migration"></a>Créer votre première migration

Vous êtes maintenant prêt à ajouter votre première migration ! Demandez à EF Core de créer une migration nommée **InitialCreate**:

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

EF Core créera un répertoire nommé **migrations** dans votre projet et générera des fichiers. Il est judicieux d’inspecter ce qui est exactement EF Core généré, et éventuellement de le modifier, mais nous allons ignorer cela pour l’instant.

### <a name="create-your-database-and-schema"></a>Créer votre base de données et votre schéma

À ce stade, EF peut créer votre base de données et créer votre schéma à partir de la migration. Pour ce faire, procédez comme suit :

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

Voilà, votre application est prête à être exécutée sur votre nouvelle base de données, et vous n’avez pas besoin d’écrire une seule ligne de SQL. Notez que cette méthode d’application des migrations est idéale pour le développement local, mais est moins adaptée aux environnements de production. pour plus d’informations, consultez la [page application des migrations](xref:core/managing-schemas/migrations/applying) .

### <a name="evolving-your-model"></a>Évolution de votre modèle

Quelques jours se sont écoulés, et vous êtes invité à ajouter un horodateur de création à vos blogs. Vous avez effectué les modifications nécessaires à votre application et votre modèle ressemble maintenant à ce qui suit :

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

Votre modèle et votre base de données de production ne sont plus synchronisés. nous devons ajouter une nouvelle colonne à votre schéma de base de données. Nous allons créer une nouvelle migration pour ce qui suit :

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

Notez que nous fournissons des migrations un nom descriptif pour faciliter la compréhension ultérieure de l’historique du projet.

Comme il ne s’agit pas de la première migration du projet, EF Core compare maintenant votre modèle mis à jour par rapport à un instantané de l’ancien modèle, avant l’ajout de la colonne ; l’instantané de modèle est l’un des fichiers générés par EF Core lorsque vous ajoutez une migration, et est archivé dans le contrôle de code source. Sur la base de cette comparaison, EF Core détecte qu’une colonne a été ajoutée et ajoute la migration appropriée.

Vous pouvez maintenant appliquer votre migration comme précédemment :

#### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

Notez que cette fois, EF détecte que la base de données existe déjà. En outre, lorsque la première migration était appliquée ci-dessus, ce fait a été enregistré dans une table d’historique des migrations spéciale de votre base de données. Cela permet à EF d’appliquer automatiquement la nouvelle migration uniquement.

### <a name="next-steps"></a>Étapes suivantes

Vous ne disposez que d’une brève présentation des migrations. Consultez les autres pages de documentation pour en savoir plus sur la [gestion des migrations](xref:core/managing-schemas/migrations/managing), [leur application](xref:core/managing-schemas/migrations/applying)et d’autres aspects. La [référence de l’outil CLI .net Core](xref:core/miscellaneous/cli/index) contient également des informations utiles sur les différentes commandes.
