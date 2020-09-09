---
title: Utilisation d’un projet de migrations distinct-EF Core
description: Utilisation d’un projet de migration distinct pour la gestion des schémas de base de données avec Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: a3f0ed96c6a8e3e8629d9a4bb1610fcbfe6ca043
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617914"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="c3cda-103">Utilisation d’un projet de migrations distinct</span><span class="sxs-lookup"><span data-stu-id="c3cda-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="c3cda-104">Vous souhaiterez peut-être stocker vos migrations dans un autre assembly que celui contenant votre `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="c3cda-104">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="c3cda-105">Vous pouvez également utiliser cette stratégie pour gérer plusieurs ensembles de migrations, par exemple, un pour le développement et un autre pour les mises à niveau vers la version finale.</span><span class="sxs-lookup"><span data-stu-id="c3cda-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="c3cda-106">Action à réaliser...</span><span class="sxs-lookup"><span data-stu-id="c3cda-106">To do this...</span></span>

1. <span data-ttu-id="c3cda-107">Créer un nouveau projet de bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="c3cda-107">Create a new class library.</span></span>

2. <span data-ttu-id="c3cda-108">Ajoutez une référence à votre assembly DbContext.</span><span class="sxs-lookup"><span data-stu-id="c3cda-108">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="c3cda-109">Déplacez les fichiers de migration et d’instantané de modèle dans la bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="c3cda-109">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="c3cda-110">Si vous n’avez pas de migrations existantes, générez-en une dans le projet contenant le DbContext, puis déplacez-le.</span><span class="sxs-lookup"><span data-stu-id="c3cda-110">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="c3cda-111">Cela est important car si l’assembly de migrations ne contient pas de migration existante, la commande Add-migration ne pourra pas trouver DbContext.</span><span class="sxs-lookup"><span data-stu-id="c3cda-111">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="c3cda-112">Configurez l’assembly des migrations :</span><span class="sxs-lookup"><span data-stu-id="c3cda-112">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="c3cda-113">Ajoutez une référence à votre assembly de migration à partir de l’assembly de démarrage.</span><span class="sxs-lookup"><span data-stu-id="c3cda-113">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="c3cda-114">Si cela provoque une dépendance circulaire, mettez à jour le chemin de sortie de la bibliothèque de classes :</span><span class="sxs-lookup"><span data-stu-id="c3cda-114">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="c3cda-115">Si vous avez tout effectué correctement, vous devez être en mesure d’ajouter de nouvelles migrations au projet.</span><span class="sxs-lookup"><span data-stu-id="c3cda-115">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="c3cda-116">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="c3cda-116">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="c3cda-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3cda-117">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
