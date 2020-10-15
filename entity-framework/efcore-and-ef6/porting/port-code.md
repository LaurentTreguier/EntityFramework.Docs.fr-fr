---
title: Portage à partir de EF6 vers EF Core-Portage d’un Code-Based modèle-EF
description: Informations spécifiques sur le portage d’une application de modèle Entity Framework 6 basée sur du code pour Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 819c3bc0eba140c023cdcd5038a4cd63c300ed4c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064222"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>Portage d’un modèle de Code-Based EF6 vers EF Core

Si vous avez lu tous les avertissements et que vous êtes prêt à utiliser le port, voici quelques conseils pour vous aider à démarrer.

## <a name="install-ef-core-nuget-packages"></a>Installer EF Core des packages NuGet

Pour utiliser EF Core, vous installez le package NuGet pour le fournisseur de base de données que vous souhaitez utiliser. Par exemple, lorsque vous ciblez SQL Server, vous devez installer `Microsoft.EntityFrameworkCore.SqlServer` . Pour plus d’informations, consultez [fournisseurs de bases de données](xref:core/providers/index) .

Si vous envisagez d’utiliser des migrations, vous devez également installer le `Microsoft.EntityFrameworkCore.Tools` Package.

Il est parfait de conserver le package NuGet EF6 (EntityFramework) installé, car EF Core et EF6 peuvent être utilisés côte à côte dans la même application. Toutefois, si vous n’avez pas l’intention d’utiliser EF6 dans les zones de votre application, la désinstallation du package permet d’obtenir des erreurs de compilation sur des portions de code nécessitant votre attention.

## <a name="swap-namespaces"></a>Permuter les espaces de noms

La plupart des API que vous utilisez dans EF6 se trouvent dans l' `System.Data.Entity` espace de noms (et les sous-espaces de noms connexes). La première modification du code consiste à basculer vers l' `Microsoft.EntityFrameworkCore` espace de noms. En général, vous démarrez avec votre fichier de code de contexte dérivé, puis vous travaillez à partir de là, en résolvant les erreurs de compilation à mesure qu’elles se produisent.

## <a name="context-configuration-connection-etc"></a>Configuration du contexte (connexion, etc.)

Comme décrit dans [la section s’assurer EF Core fonctionne pour votre application](xref:efcore-and-ef6/porting/index), EF Core a moins de magie pour détecter la base de données à laquelle se connecter. Vous devez substituer la `OnConfiguring` méthode dans votre contexte dérivé et utiliser l’API spécifique du fournisseur de base de données pour configurer la connexion à la base de données.

La plupart des applications EF6 stockent la chaîne de connexion dans le fichier d’application `App/Web.config` . Dans EF Core, vous lisez cette chaîne de connexion à l’aide de l' `ConfigurationManager` API. Vous devrez peut-être ajouter une référence à l' `System.Configuration` assembly de Framework pour pouvoir utiliser cette API.

```csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="update-your-code"></a>Mettre à jour votre code

À ce stade, il s’agit de traiter les erreurs de compilation et de consulter le code pour voir si les changements de comportement ont un impact sur vous.

## <a name="existing-migrations"></a>Migrations existantes

Il n’existe pas vraiment un moyen pratique de porter des migrations EF6 existantes vers EF Core.

Si possible, il est préférable de supposer que toutes les migrations précédentes de EF6 ont été appliquées à la base de données, puis de commencer à migrer le schéma à partir de ce point à l’aide de EF Core. Pour ce faire, vous devez utiliser la `Add-Migration` commande pour ajouter une migration une fois que le modèle est porté sur EF Core. Ensuite, vous supprimez tout le code `Up` des `Down` méthodes et de la migration par génération de modèles automatique. Les migrations suivantes seront comparées au modèle lors de la génération de modèles automatique de la migration initiale.

## <a name="test-the-port"></a>Tester le port

Simplement parce que votre application est compilée, ne signifie pas qu’elle est correctement reportée vers EF Core. Vous devrez tester toutes les zones de votre application pour vous assurer qu’aucune des modifications de comportement n’a un impact négatif sur votre application.
