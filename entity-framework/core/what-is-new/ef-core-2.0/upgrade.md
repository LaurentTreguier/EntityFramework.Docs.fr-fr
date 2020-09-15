---
title: Mise à niveau des versions précédentes vers EF Core 2-EF Core
description: Instructions et notes relatives à la mise à niveau vers Entity Framework Core 2,0
author: divega
ms.date: 08/13/2017
uid: core/what-is-new/ef-core-2.0/upgrade
ms.openlocfilehash: bdc0cfe8c0be4a83f8c78ba2ac66bb1e18cea0f7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072341"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>Mise à niveau des applications des versions précédentes vers EF Core 2,0

Nous avons pris la possibilité d’affiner de manière significative nos API et comportements existants dans 2,0. Il existe quelques améliorations qui peuvent nécessiter la modification du code d’application existant, même si nous pensons que pour la majorité des applications, l’impact sera faible, dans la plupart des cas nécessitant juste une recompilation et des modifications guidées minimes pour remplacer les API obsolètes.

La mise à jour d’une application existante vers EF Core 2,0 peut nécessiter les éléments suivants :

1. Mise à niveau de l’implémentation .NET cible de l’application vers une implémentation qui prend en charge .NET Standard 2,0. Pour plus d’informations, consultez [implémentations .net prises en charge](xref:core/platforms/index) .

2. Identifiez un fournisseur pour la base de données cible qui est compatible avec EF Core 2,0. Consultez [EF Core 2,0 requiert un fournisseur de base de données 2,0](#ef-core-20-requires-a-20-database-provider) ci-dessous.

3. Mise à niveau de tous les packages de EF Core (Runtime et outils) vers 2,0. Pour plus d’informations, consultez [installation de EF Core](xref:core/get-started/install/index) .

4. Apportez les modifications nécessaires au code pour compenser les modifications avec rupture décrites dans le reste de ce document.

## <a name="aspnet-core-now-includes-ef-core"></a>ASP.NET Core comprend maintenant EF Core

Les applications ciblant ASP.NET Core 2.0 peuvent utiliser EF Core 2.0 sans dépendances supplémentaires en plus des fournisseurs de base de données tiers. Toutefois, les applications ciblant des versions antérieures de ASP.NET Core doivent effectuer une mise à niveau vers ASP.NET Core 2,0 afin d’utiliser EF Core 2,0. Pour plus d’informations sur la mise à niveau des applications ASP.NET Core vers 2,0, consultez [la documentation ASP.net Core sur le sujet](/aspnet/core/migration/1x-to-2x/).

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>Nouvelle méthode d’obtention des services d’application dans ASP.NET Core

Le modèle recommandé pour les applications Web ASP.NET Core a été mis à jour pour 2,0 d’une manière qui a enfreint la logique au moment de la conception EF Core utilisée dans 1. x. Précédemment, au moment du design, EF Core essaiera d’appeler `Startup.ConfigureServices` directement pour accéder au fournisseur de services de l’application. Dans ASP.NET Core 2,0, la configuration est initialisée en dehors de la `Startup` classe. Les applications qui utilisent EF Core généralement accéder à leur chaîne de connexion à partir de la configuration, de sorte qu' `Startup` elles ne suffisent plus. Si vous mettez à niveau une application ASP.NET Core 1. x, vous risquez de recevoir l’erreur suivante lors de l’utilisation des outils de EF Core.

> Aucun constructeur sans paramètre n’a été trouvé sur’ApplicationContext'. Ajoutez un constructeur sans paramètre à’ApplicationContext’ou ajoutez une implémentation de’IDesignTimeDbContextFactory &lt; ApplicationContext &gt; 'dans le même assembly que’ApplicationContext'

Un nouveau Hook au moment du design a été ajouté au modèle par défaut de ASP.NET Core 2.0. La `Program.BuildWebHost` méthode statique permet à EF Core d’accéder au fournisseur de services de l’application au moment du Design. Si vous mettez à niveau une application ASP.NET Core 1. x, vous devrez mettre à jour la `Program` classe pour qu’elle ressemble à ce qui suit.

``` csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

L’adoption de ce nouveau modèle lors de la mise à jour des applications vers 2,0 est vivement recommandée et est nécessaire pour que des fonctionnalités de produit telles que Entity Framework Core migrations fonctionnent. L’autre alternative courante consiste à [implémenter *IDesignTimeDbContextFactory \<TContext> *](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).

## <a name="idbcontextfactory-renamed"></a>IDbContextFactory renommée

Afin de prendre en charge divers modèles d’application et de permettre aux utilisateurs de mieux contrôler la façon dont ils `DbContext` sont utilisés au moment de la conception, nous avons, par le passé, fourni l' `IDbContextFactory<TContext>` interface. Au moment du design, les outils de EF Core découvrent les implémentations de cette interface dans votre projet et l’utilisent pour créer des `DbContext` objets.

Cette interface a un nom très général qui induire certains utilisateurs à essayer de les réutiliser pour d’autres `DbContext` scénarios de création. Ils ont été interceptés contre la protection lorsque les outils EF ont ensuite essayé d’utiliser leur implémentation au moment de la conception et ont provoqué l’échec des commandes comme `Update-Database` ou `dotnet ef database update` .

Pour pouvoir communiquer la sémantique forte au moment de la conception de cette interface, nous l’avons renommée en `IDesignTimeDbContextFactory<TContext>` .

Pour la version 2,0 `IDbContextFactory<TContext>` , existe toujours, mais est marqué comme obsolète.

## <a name="dbcontextfactoryoptions-removed"></a>DbContextFactoryOptions supprimé

En raison des modifications apportées à l’ASP.NET Core 2,0 décrites ci-dessus, nous avons constaté que `DbContextFactoryOptions` n’était plus nécessaire sur la nouvelle `IDesignTimeDbContextFactory<TContext>` interface. Voici les alternatives que vous devez utiliser à la place.

| DbContextFactoryOptions | Alternative                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext. BaseDirectory                                     |
| ContentRootPath         | Répertoire. GetCurrentDirectory ()                              |
| EnvironmentName         | Environment. GetEnvironmentVariable ("ASPNETCORE_ENVIRONMENT") |

## <a name="design-time-working-directory-changed"></a>Modification du répertoire de travail au moment du design

Les modifications apportées au ASP.NET Core 2,0 ont également nécessité le répertoire de travail utilisé par `dotnet ef` pour s’aligner avec le répertoire de travail utilisé par Visual Studio lors de l’exécution de votre application. L’un des effets secondaires observables est que les noms de fichiers SQLite sont désormais relatifs au répertoire du projet, et non au répertoire de sortie comme s’ils étaient utilisés.

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2,0 requiert un fournisseur de base de données 2,0

Pour EF Core 2,0, nous avons apporté de nombreuses simplifications et améliorations au fonctionnement des fournisseurs de bases de données. Cela signifie que les fournisseurs 1.0. x et 1.1. x ne fonctionneront pas avec EF Core 2,0.

Les fournisseurs SQL Server et SQLite sont fournis par l’équipe EF et les versions 2,0 seront disponibles dans le cadre de la version 2,0. Les fournisseurs tiers Open source pour [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)et [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) sont mis à jour pour 2,0. Pour tous les autres fournisseurs, contactez le rédacteur du fournisseur.

## <a name="logging-and-diagnostics-events-have-changed"></a>Les événements de journalisation et de diagnostic ont changé

Remarque : ces modifications ne doivent pas avoir d’impact sur la plupart du code d’application.

Les ID d’événement pour les messages envoyés à un [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) ont été modifiés dans 2,0. Les ID d’événement sont maintenant uniques dans le code EF Core. En outre, ces messages suivent désormais le modèle standard de la journalisation structurée utilisé, par exemple, par le modèle MVC.

Les catégories d’enregistreurs d’événements ont également changé. Il existe désormais un jeu connu de catégories accessibles par le biais de [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs).

Les événements [DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) utilisent désormais les mêmes noms d’ID d’événements que les `ILogger` messages correspondants. Les charges utiles d’événement sont tous des types nominaux dérivés de [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata).

Les ID d’événement, les types de charge utile et les catégories sont documentés dans les classes [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) et [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) .

Les ID ont également été déplacés de Microsoft. EntityFrameworkCore. infrastructure vers le nouvel espace de noms Microsoft. EntityFrameworkCore. Diagnostics.

## <a name="ef-core-relational-metadata-api-changes"></a>EF Core les modifications de l’API de métadonnées relationnelles

EF Core 2.0 génère désormais un [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) différent par fournisseur utilisé. Cela est généralement transparent pour l’application. Cela a facilité la simplification des API de métadonnées de niveau inférieur, de telle sorte que tout accès aux _concepts de métadonnées relationnelles communs_ soit toujours effectué via un appel à `.Relational` au lieu de `.SqlServer` , `.Sqlite` , etc. Par exemple, le code 1.1. x ressemble à ceci :

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

Doit maintenant être écrit comme suit :

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

Au lieu d’utiliser des méthodes telles que `ForSqlServerToTable` , les méthodes d’extension sont désormais disponibles pour écrire du code conditionnel basé sur le fournisseur actuel en cours d’utilisation. Exemple :

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

Notez que cette modification s’applique uniquement aux API/métadonnées définies pour _tous les_ fournisseurs relationnels. L’API et les métadonnées restent les mêmes lorsqu’elles sont spécifiques à un seul fournisseur. Par exemple, les index cluster sont spécifiques à SQL Server `ForSqlServerIsClustered` et  `.SqlServer().IsClustered()` doivent donc toujours être utilisés.

## <a name="dont-take-control-of-the-ef-service-provider"></a>Ne prenez pas le contrôle du fournisseur de services EF

EF Core utilise un `IServiceProvider` conteneur d’injection de dépendances interne pour son implémentation interne. Les applications doivent autoriser EF Core à créer et à gérer ce fournisseur, sauf dans des cas spéciaux. Envisagez fortement de supprimer tous les appels à `UseInternalServiceProvider` . Si une application a besoin d’appeler `UseInternalServiceProvider` , envisagez de [soumettre un problème](https://github.com/dotnet/efcore/Issues) afin que nous puissions étudier d’autres façons de gérer votre scénario.

`AddEntityFramework`L’appel de, `AddEntityFrameworkSqlServer` , etc. n’est pas requis par le code d’application, sauf si `UseInternalServiceProvider` est également appelé. Supprimez tous les appels existants à `AddEntityFramework` ou `AddEntityFrameworkSqlServer` , etc. `AddDbContext` doit toujours être utilisé de la même façon qu’avant.

## <a name="in-memory-databases-must-be-named"></a>Les bases de données en mémoire doivent être nommées

La base de données en mémoire sans nom globale a été supprimée, mais toutes les bases de données en mémoire doivent être nommées. Exemple :

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

Crée/utilise une base de données nommée « MyDatabase ». Si `UseInMemoryDatabase` est à nouveau appelé avec le même nom, la même base de données en mémoire est utilisée, ce qui lui permet d’être partagé par plusieurs instances de contexte.

## <a name="read-only-api-changes"></a>Modifications de l’API en lecture seule

`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave` et `IsStoreGeneratedAlways` ont été obsolètes et remplacés par [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) et [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior). Ces comportements s’appliquent à n’importe quelle propriété (non seulement les propriétés générées par le magasin) et déterminent la façon dont la valeur de la propriété doit être utilisée lors de l’insertion dans une ligne de base de données ( `BeforeSaveBehavior` ) ou lors de la mise à jour d’une ligne de base de données existante ( `AfterSaveBehavior` ).

Les propriétés marquées comme [ValueGenerated. OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (par exemple, pour les colonnes calculées) ignorent par défaut toute valeur actuellement définie sur la propriété. Cela signifie qu’une valeur générée par le magasin sera toujours obtenue, qu’une valeur ait été définie ou modifiée sur l’entité suivie. Cela peut être modifié en définissant un différent `Before\AfterSaveBehavior` .

## <a name="new-clientsetnull-delete-behavior"></a>Nouveau comportement de suppression de ClientSetNull

Dans les versions précédentes, [DeleteBehavior. Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) avait un comportement pour les entités suivies par le contexte qui a plus de `SetNull` sémantiques correspondantes. Dans EF Core 2,0, un nouveau `ClientSetNull` comportement a été introduit comme valeur par défaut pour les relations facultatives. Ce comportement a une `SetNull` sémantique pour les entités suivies et le `Restrict` comportement pour les bases de données créées à l’aide de EF Core. Dans notre expérience, il s’agit des comportements les plus attendus/utiles pour les entités suivies et la base de données. `DeleteBehavior.Restrict` est maintenant respecté pour les entités suivies lorsqu’elles sont définies pour des relations facultatives.

## <a name="provider-design-time-packages-removed"></a>Packages au moment de la conception du fournisseur supprimés

Le `Microsoft.EntityFrameworkCore.Relational.Design` package a été supprimé. Son contenu a été consolidé dans `Microsoft.EntityFrameworkCore.Relational` et `Microsoft.EntityFrameworkCore.Design` .

Cela se propage dans les packages au moment de la conception du fournisseur. Ces packages ( `Microsoft.EntityFrameworkCore.Sqlite.Design` , `Microsoft.EntityFrameworkCore.SqlServer.Design` , etc.) ont été supprimés et leur contenu est consolidé dans les packages de fournisseurs principaux.

Pour activer `Scaffold-DbContext` ou `dotnet ef dbcontext scaffold` dans EF Core 2,0, il vous suffit de référencer le package de fournisseur unique :

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
