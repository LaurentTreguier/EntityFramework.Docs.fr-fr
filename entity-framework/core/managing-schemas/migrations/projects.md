---
title: Utilisation d’un projet de migrations distinct-EF Core
description: Utilisation d’un projet de migration distinct pour la gestion des schémas de base de données avec Entity Framework Core
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 6f28027c320f0d1c13d13bef7d8227b2bb68df91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062488"
---
# <a name="using-a-separate-migrations-project"></a>Utilisation d’un projet de migrations distinct

Vous souhaiterez peut-être stocker vos migrations dans un autre assembly que celui contenant votre `DbContext` . Vous pouvez également utiliser cette stratégie pour gérer plusieurs ensembles de migrations, par exemple, un pour le développement et un autre pour les mises à niveau vers la version finale.

Action à réaliser...

1. Créer un nouveau projet de bibliothèque de classes.

2. Ajoutez une référence à votre assembly DbContext.

3. Déplacez les fichiers de migration et d’instantané de modèle dans la bibliothèque de classes.
   > [!TIP]
   > Si vous n’avez pas de migrations existantes, générez-en une dans le projet contenant le DbContext, puis déplacez-le.
   > Cela est important car si l’assembly de migrations ne contient pas de migration existante, la commande Add-Migration ne pourra pas trouver DbContext.

4. Configurez l’assembly des migrations :

   ```csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. Ajoutez une référence à votre assembly de migration à partir de l’assembly de démarrage.
   * Si cela provoque une dépendance circulaire, mettez à jour le chemin de sortie de la bibliothèque de classes :

     ```xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

Si vous avez tout effectué correctement, vous devez être en mesure d’ajouter de nouvelles migrations au projet.

## <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
