---
title: Utilisation d’un projet de migrations distinct-EF Core
description: Utilisation d’un projet de migration distinct pour la gestion des schémas de base de données avec Entity Framework Core
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 9a6b8977f9d7bcdae0fb9aea6966a7eb43e9e7db
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024145"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="2f20e-103">Utilisation d’un projet de migrations distinct</span><span class="sxs-lookup"><span data-stu-id="2f20e-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="2f20e-104">Vous souhaiterez peut-être stocker vos migrations dans un autre projet que celui contenant votre `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="2f20e-104">You may want to store your migrations in a different project than the one containing your `DbContext`.</span></span> <span data-ttu-id="2f20e-105">Vous pouvez également utiliser cette stratégie pour gérer plusieurs ensembles de migrations, par exemple, un pour le développement et un autre pour les mises à niveau vers la version finale.</span><span class="sxs-lookup"><span data-stu-id="2f20e-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

> [!TIP]
> <span data-ttu-id="2f20e-106">Vous pouvez afficher cet [exemple sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/ThreeProjectMigrations).</span><span class="sxs-lookup"><span data-stu-id="2f20e-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Schemas/ThreeProjectMigrations).</span></span>

## <a name="steps"></a><span data-ttu-id="2f20e-107">Étapes</span><span class="sxs-lookup"><span data-stu-id="2f20e-107">Steps</span></span>

1. <span data-ttu-id="2f20e-108">Créer un nouveau projet de bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="2f20e-108">Create a new class library.</span></span>

2. <span data-ttu-id="2f20e-109">Ajoutez une référence à votre projet DbContext.</span><span class="sxs-lookup"><span data-stu-id="2f20e-109">Add a reference to your DbContext project.</span></span>

3. <span data-ttu-id="2f20e-110">Déplacez les fichiers de migration et d’instantané de modèle dans la bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="2f20e-110">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="2f20e-111">Si vous n’avez pas de migrations existantes, générez-en une dans le projet contenant le DbContext, puis déplacez-le.</span><span class="sxs-lookup"><span data-stu-id="2f20e-111">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="2f20e-112">Cela est important car si le projet de migrations ne contient pas de migration existante, la commande Add-Migration ne pourra pas trouver DbContext.</span><span class="sxs-lookup"><span data-stu-id="2f20e-112">This is important because if the migrations project does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="2f20e-113">Configurez l’assembly des migrations :</span><span class="sxs-lookup"><span data-stu-id="2f20e-113">Configure the migrations assembly:</span></span>

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. <span data-ttu-id="2f20e-114">Ajoutez une référence à votre projet de migration à partir du projet de **démarrage** .</span><span class="sxs-lookup"><span data-stu-id="2f20e-114">Add a reference to your migrations project from the **startup** project.</span></span>

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   <span data-ttu-id="2f20e-115">Si cela provoque une dépendance circulaire, vous pouvez mettre à jour le chemin de sortie de base du projet **migrations** à la place :</span><span class="sxs-lookup"><span data-stu-id="2f20e-115">If this causes a circular dependency, you can update the base output path of the **migrations** project instead:</span></span>

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

<span data-ttu-id="2f20e-116">Si vous avez tout effectué correctement, vous devez être en mesure d’ajouter de nouvelles migrations au projet.</span><span class="sxs-lookup"><span data-stu-id="2f20e-116">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="2f20e-117">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="2f20e-117">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="2f20e-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f20e-118">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
