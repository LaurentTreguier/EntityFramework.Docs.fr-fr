---
title: Migrations avec plusieurs fournisseurs-EF Core
description: Utilisation de migrations pour gérer les schémas de base de données lors du ciblage de plusieurs fournisseurs de bases de données avec Entity Framework Core
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: fb8c6121a4baccf573e57b52ebeb3fcd29fe2cba
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429778"
---
# <a name="migrations-with-multiple-providers"></a>Migrations avec plusieurs fournisseurs

Les [outils de EF corent](xref:core/cli/index) uniquement les migrations de l’échafaudage pour le fournisseur actif. Toutefois, il peut arriver que vous souhaitiez utiliser plusieurs fournisseurs (par exemple Microsoft SQL Server et SQLite) avec votre DbContext. Gérez-la en conservant plusieurs ensembles de migrations, un pour chaque fournisseur, et en ajoutant une migration à chaque modification de modèle pour chaque modèle.

## <a name="using-multiple-context-types"></a>Utilisation de plusieurs types de contexte

Une façon de créer plusieurs jeux de migration consiste à utiliser un type DbContext par fournisseur.

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

Spécifiez le type de contexte lors de l’ajout de nouvelles migrations.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> Vous n’avez pas besoin de spécifier le répertoire de sortie pour les migrations suivantes, car elles sont créées en tant que frères au dernier.

## <a name="using-one-context-type"></a>Utilisation d’un type de contexte

Il est également possible d’utiliser un type DbContext. Cela nécessite actuellement le déplacement des migrations dans un assembly distinct. Pour obtenir des instructions sur la configuration de vos projets, reportez-vous à [la rubrique utilisation d’un projet de migration distinct](xref:core/managing-schemas/migrations/projects) .

> [!TIP]
> Vous pouvez afficher cet [exemple sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations).

À partir de EF Core 5,0, vous pouvez passer des arguments dans l’application à partir des outils. Cela peut permettre un flux de travail plus rationalisé qui évite d’avoir à apporter des modifications manuelles au projet lors de l’exécution des outils.

Voici un modèle qui fonctionne bien lorsque vous utilisez un [hôte générique](/dotnet/core/extensions/generic-host).

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

Étant donné que le générateur d’hôte par défaut lit la configuration à partir d’arguments de ligne de commande, vous pouvez spécifier le fournisseur lors de l’exécution des outils.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> Le `--` jeton indique `dotnet ef` à traiter tout ce qui suit comme argument et ne tente pas de les analyser en tant qu’options. Les arguments supplémentaires non utilisés par `dotnet ef` sont transmis à l’application.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> La possibilité de spécifier des arguments supplémentaires pour l’application a été ajoutée dans EF Core 5,0. Si vous utilisez une version antérieure, spécifiez à la place des valeurs de configuration avec des variables d’environnement.
