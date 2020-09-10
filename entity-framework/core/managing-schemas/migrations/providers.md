---
title: Migrations avec plusieurs fournisseurs-EF Core
description: Utilisation de migrations pour gérer les schémas de base de données lors du ciblage de plusieurs fournisseurs de bases de données avec Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: df38af6ac700a530894b98e1f29bbe804831bad5
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619193"
---
# <a name="migrations-with-multiple-providers"></a>Migrations avec plusieurs fournisseurs

Les [outils de EF corent][1] uniquement les migrations de l’échafaudage pour le fournisseur actif. Toutefois, il peut arriver que vous souhaitiez utiliser plusieurs fournisseurs (par exemple Microsoft SQL Server et SQLite) avec votre DbContext. Il existe deux façons de gérer cela avec les migrations. Vous pouvez gérer deux ensembles de migrations, un pour chaque fournisseur, ou les fusionner dans un ensemble unique qui peut fonctionner sur les deux.

## <a name="two-migration-sets"></a>Deux ensembles de migration

Dans la première approche, vous générez deux migrations pour chaque modification de modèle.

Pour ce faire, vous pouvez placer chaque ensemble de migration [dans un assembly distinct][2] et basculer manuellement le fournisseur actif (et l’assembly de migration) entre l’ajout des deux migrations.

Une autre approche qui facilite l’utilisation des outils consiste à créer un nouveau type qui dérive de votre DbContext et qui remplace le fournisseur actif. Ce type est utilisé au moment de la conception lors de l’ajout ou de l’application de migrations.

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> Étant donné que chaque jeu de migration utilise ses propres types DbContext, cette approche ne nécessite pas l’utilisation d’un assembly de migrations distinct.

Lorsque vous ajoutez une nouvelle migration, spécifiez les types de contexte.

### <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> Vous n’avez pas besoin de spécifier le répertoire de sortie pour les migrations suivantes, car elles sont créées en tant que frères au dernier.

## <a name="one-migration-set"></a>Un jeu de migration

Si vous n’aimez pas avoir deux ensembles de migrations, vous pouvez les combiner manuellement en un seul ensemble qui peut être appliqué aux deux fournisseurs.

Les annotations peuvent coexister car un fournisseur ignore les annotations qu’il ne comprend pas. Par exemple, une colonne de clé primaire qui fonctionne avec Microsoft SQL Server et SQLite peut se présenter comme suit.

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

Si les opérations peuvent être appliquées uniquement pour un fournisseur, ou si elles sont différentes entre les fournisseurs, utilisez la `ActiveProvider` propriété pour déterminer quel fournisseur est actif :

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: xref:core/miscellaneous/cli/index
  [2]: xref:core/managing-schemas/migrations/projects
