---
title: Utilisation d’un projet de migrations distinct-EF Core
description: Utilisation d’un projet de migration distinct pour la gestion des schémas de base de données avec Entity Framework Core
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 2d1c093e0bb307584e2bf19cb93deec98aa10692
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429804"
---
# <a name="using-a-separate-migrations-project"></a>Utilisation d’un projet de migrations distinct

Vous souhaiterez peut-être stocker vos migrations dans un autre projet que celui contenant votre `DbContext` . Vous pouvez également utiliser cette stratégie pour gérer plusieurs ensembles de migrations, par exemple, un pour le développement et un autre pour les mises à niveau vers la version finale.

> [!TIP]
> Vous pouvez afficher cet [exemple sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations).

## <a name="steps"></a>Étapes

1. Créer un nouveau projet de bibliothèque de classes.

2. Ajoutez une référence à votre projet DbContext.

3. Déplacez les fichiers de migration et d’instantané de modèle dans la bibliothèque de classes.
   > [!TIP]
   > Si vous n’avez pas de migrations existantes, générez-en une dans le projet contenant le DbContext, puis déplacez-le.
   > Cela est important car si le projet de migrations ne contient pas de migration existante, la commande Add-Migration ne pourra pas trouver DbContext.

4. Configurez l’assembly des migrations :

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. Ajoutez une référence à votre projet de migration à partir du projet de **démarrage** .

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   Si cela provoque une dépendance circulaire, vous pouvez mettre à jour le chemin de sortie de base du projet **migrations** à la place :

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

Si vous avez tout effectué correctement, vous devez être en mesure d’ajouter de nouvelles migrations au projet.

## <a name="net-core-cli"></a>[CLI .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
