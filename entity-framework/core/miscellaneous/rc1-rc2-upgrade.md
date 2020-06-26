---
title: Mise à niveau de EF Core 1,0 RC1 vers RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 5500addc98a0c0eb314c4d9f9401fa301ce5c6f6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370524"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>Mise à niveau de EF Core 1,0 RC1 vers 1,0 RC2

Cet article fournit des conseils pour déplacer une application générée avec les packages RC1 vers RC2.

## <a name="package-names-and-versions"></a>Noms et versions des packages

Entre RC1 et RC2, nous avons changé de « Entity Framework 7 » en « Entity Framework Core ». Pour plus d’informations sur les raisons de cette modification, consultez le [billet de blog de Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx). En raison de cette modification, nos noms de packages ont été modifiés de à `EntityFramework.*` `Microsoft.EntityFrameworkCore.*` et de nos versions de à `7.0.0-rc1-final` `1.0.0-rc2-final` (ou `1.0.0-preview1-final` pour les outils).

**Vous devrez supprimer complètement les packages RC1, puis installer les packages RC2.** Voici le mappage de certains packages courants.

| Package RC1                                               | Équivalent RC2                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework. MicrosoftSqlServer 7.0.0-RC1-final | Microsoft. EntityFrameworkCore. SqlServer 1.0.0-RC2-final      |
| EntityFramework. SQLite 7.0.0-RC1-final | Microsoft. EntityFrameworkCore. sqlite 1.0.0-RC2-final      |
| EntityFramework7. npgsql 3.1.0-RC1-3     | NpgSql. EntityFrameworkCore. postgres<to be advised>      |
| EntityFramework. SqlServerCompact35 7.0.0-RC1-final | EntityFrameworkCore. SqlServerCompact35 1.0.0-RC2-final      |
| EntityFramework. SqlServerCompact40 7.0.0-RC1-final | EntityFrameworkCore. SqlServerCompact40 1.0.0-RC2-final      |
| EntityFramework. InMemory 7.0.0-RC1-final | Microsoft. EntityFrameworkCore. InMemory 1.0.0-RC2-final      |
| EntityFramework. IBMDataServer 7.0.0-beta1     | Pas encore disponible pour RC2                                            |
| EntityFramework. Commands 7.0.0-RC1-final | Microsoft. EntityFrameworkCore. Tools 1.0.0-Preview1-final |
| EntityFramework. MicrosoftSqlServer. Design 7.0.0-RC1-final | Microsoft. EntityFrameworkCore. SqlServer. Design 1.0.0-RC2-final      |

## <a name="namespaces"></a>Espaces de noms

Avec les noms de packages, les espaces de noms ont été modifiés de `Microsoft.Data.Entity.*` à `Microsoft.EntityFrameworkCore.*` . Vous pouvez gérer cette modification avec une recherche/remplacement de `using Microsoft.Data.Entity` avec `using Microsoft.EntityFrameworkCore` .

## <a name="table-naming-convention-changes"></a>Modification de la Convention d’affectation des noms de table

Une modification fonctionnelle significative que nous avons apportée en RC2 consistait à utiliser le nom de la `DbSet<TEntity>` propriété pour une entité donnée comme nom de table auquel elle est mappée, plutôt que simplement le nom de la classe. Pour plus d’informations sur ce changement, consultez [le numéro d’annonce associé](https://github.com/aspnet/Announcements/issues/167).

Pour les applications RC1 existantes, nous vous recommandons d’ajouter le code suivant au début de votre `OnModelCreating` méthode pour conserver la stratégie d’appellation RC1 :

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

Si vous souhaitez adopter la nouvelle stratégie de nommage, nous vous recommandons de terminer avec succès le reste des étapes de mise à niveau, puis de supprimer le code et de créer une migration pour appliquer les renommages de tables.

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>Modifications de AddDbContext/Startup.cs (projets ASP.NET Core uniquement)

Dans RC1, vous deviez ajouter des services de Entity Framework au fournisseur de services d’application `Startup.ConfigureServices(...)` :

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

Dans RC2, vous pouvez supprimer les appels à `AddEntityFramework()` , `AddSqlServer()` , etc. :

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

Vous devez également ajouter un constructeur, à votre contexte dérivé, qui prend des options de contexte et les passe au constructeur de base. Cela est nécessaire, car nous avons supprimé une partie de la magie de l’glissé en arrière-plan :

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>Passage d’un IServiceProvider

Si vous avez du code RC1 qui passe un `IServiceProvider` au contexte, il a été déplacé vers `DbContextOptions` , plutôt que comme un paramètre de constructeur séparé. Utilisez `DbContextOptionsBuilder.UseInternalServiceProvider(...)` pour définir le fournisseur de services.

### <a name="testing"></a>Test

Le scénario le plus courant pour effectuer cette tâche consistait à contrôler l’étendue d’une base de données InMemory lors du test. Pour obtenir un exemple de cette procédure avec RC2, consultez l’article sur les [tests](testing/index.md) mis à jour.

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>Résolution des services internes à partir du fournisseur de services d’application (projets ASP.NET Core uniquement)

Si vous disposez d’une application ASP.NET Core et que vous souhaitez que EF résolve les services internes à partir du fournisseur de services d’application, il existe une surcharge de `AddDbContext` qui vous permet de configurer ce qui suit :

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> Nous vous recommandons d’autoriser EF à gérer ses propres services en interne, sauf si vous avez une raison d’associer les services EF internes à votre fournisseur de services d’application. La raison principale pour laquelle vous pouvez effectuer cette opération consiste à utiliser votre fournisseur de services d’application pour remplacer les services utilisés en interne par EF.

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a>Commandes DNX => CLI .NET Core (projets ASP.NET Core uniquement)

Si vous avez précédemment utilisé les `dnx ef` commandes pour les projets ASP.net 5, ceux-ci ont été déplacés vers des `dotnet ef` commandes. La même syntaxe de commande s’applique toujours. Vous pouvez utiliser `dotnet ef --help` pour les informations de syntaxe.

La façon dont les commandes sont inscrites a changé en RC2, en raison du remplacement de DNX par le CLI .NET Core. Les commandes sont désormais inscrites dans une `tools` section dans `project.json` :

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> Si vous utilisez Visual Studio, vous pouvez désormais utiliser la console du gestionnaire de package pour exécuter des commandes EF pour les projets ASP.NET Core (cela n’était pas pris en charge dans RC1). Vous devez toujours enregistrer les commandes dans la `tools` section de `project.json` pour effectuer cette opération.

## <a name="package-manager-commands-require-powershell-5"></a>Les commandes du gestionnaire de package requièrent PowerShell 5

Si vous utilisez les commandes Entity Framework dans la console du gestionnaire de package dans Visual Studio, vous devez vous assurer que PowerShell 5 est installé. Il s’agit d’une exigence temporaire qui sera supprimée dans la prochaine version (pour plus d’informations, consultez [#5327 du problème](https://github.com/aspnet/EntityFramework/issues/5327) ).

## <a name="using-imports-in-projectjson"></a>Utilisation de « Imports » dans project.jssur

Certaines des dépendances de EF Core ne prennent pas encore en charge .NET Standard. EF Core dans les projets .NET Standard et .NET Core peuvent nécessiter l’ajout de « Imports » à project.jssous forme de solution de contournement temporaire.

Lorsque vous ajoutez EF, la restauration NuGet affiche ce message d’erreur :

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

La solution consiste à importer manuellement le profil portable « portable-net451 + WIN8 ». Cela force NuGet à traiter ces fichiers binaires qui correspondent à ce qui est fourni comme une infrastructure compatible avec .NET Standard, même s’ils ne le sont pas. Bien que « portable-net451 + WIN8 » ne soit pas compatible 100% avec .NET Standard, il est suffisamment compatible pour la transition de PCL vers .NET Standard. Les importations peuvent être supprimées lorsque les dépendances EF finissent par être mises à niveau vers .NET Standard.

Plusieurs infrastructures peuvent être ajoutées à « Imports » dans la syntaxe de tableau. D’autres importations peuvent être nécessaires si vous ajoutez des bibliothèques supplémentaires à votre projet.

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

Consultez [#5176 de problèmes](https://github.com/aspnet/EntityFramework/issues/5176).
