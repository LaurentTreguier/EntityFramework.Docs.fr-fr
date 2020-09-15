---
title: Prise en main avec EF Core et Xamarin-EF Core
description: Didacticiel de mise en route pour l’utilisation de Xamarin pour créer des applications mobiles avec Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: b4a7e2260337d74329d309e9db32fe97a2131d73
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619293"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="93300-103">Prise en main avec EF Core et Xamarin</span><span class="sxs-lookup"><span data-stu-id="93300-103">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="93300-104">Dans ce didacticiel, vous allez créer une application [Xamarin. Forms](/xamarin/get-started/what-is-xamarin-forms) qui effectue l’accès aux données sur une base de données SQLite à l’aide de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="93300-104">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="93300-105">Vous pouvez suivre le didacticiel à l’aide de Visual Studio sur Windows ou Visual Studio pour Mac.</span><span class="sxs-lookup"><span data-stu-id="93300-105">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="93300-106">Vous pouvez consulter l’exemple de cet article [sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span><span class="sxs-lookup"><span data-stu-id="93300-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="93300-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="93300-107">Prerequisites</span></span>

<span data-ttu-id="93300-108">Installez l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="93300-108">Install one of the below:</span></span>

* <span data-ttu-id="93300-109">[Visual Studio 2019 version 16,3 ou ultérieure](https://www.visualstudio.com/downloads/) avec cette charge de travail :</span><span class="sxs-lookup"><span data-stu-id="93300-109">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="93300-110">**Développement mobile avec .NET**</span><span class="sxs-lookup"><span data-stu-id="93300-110">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="93300-111">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="93300-111">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="93300-112">Cette [documentation fournit des instructions d’installation pas à pas détaillées](/xamarin/get-started/installation) pour chaque plateforme.</span><span class="sxs-lookup"><span data-stu-id="93300-112">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="93300-113">Télécharger et exécuter l’exemple de projet</span><span class="sxs-lookup"><span data-stu-id="93300-113">Download and run the sample project</span></span>

<span data-ttu-id="93300-114">Pour exécuter et explorer cet exemple d’application, téléchargez le code sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="93300-114">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="93300-115">Une fois téléchargé, ouvrez le fichier solution `EFGettingStarted.sln` dans Visual Studio ou Visual Studio pour Mac et exécutez l’application sur la plateforme de votre choix.</span><span class="sxs-lookup"><span data-stu-id="93300-115">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="93300-116">Lorsque l’application démarre pour la première fois, elle remplit la base de données SQLite locale avec deux entrées représentant les blogs.</span><span class="sxs-lookup"><span data-stu-id="93300-116">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![Capture d’écran de la page de liste de tous les blogs](_static/xamarin-tutorial-1.png)

<span data-ttu-id="93300-118">Cliquez sur le bouton **Ajouter** dans la barre d’outils.</span><span class="sxs-lookup"><span data-stu-id="93300-118">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="93300-119">Une nouvelle page s’affiche et vous permet d’entrer des informations sur un nouveau blog.</span><span class="sxs-lookup"><span data-stu-id="93300-119">A new page will appear that allows you to enter information about a new blog.</span></span>

![Capture d’écran de la page de modification de blog](_static/xamarin-tutorial-2.png)

<span data-ttu-id="93300-121">Renseignez toutes les informations et cliquez sur **Enregistrer** dans la barre d’outils.</span><span class="sxs-lookup"><span data-stu-id="93300-121">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="93300-122">Le nouveau blog est enregistré dans la base de données SQLite de l’application et s’affiche dans la liste.</span><span class="sxs-lookup"><span data-stu-id="93300-122">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="93300-123">Vous pouvez cliquer sur l’une des entrées de blog de la liste pour afficher les publications de ce blog.</span><span class="sxs-lookup"><span data-stu-id="93300-123">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![Capture d’écran de la page de liste des billets de blog](_static/xamarin-tutorial-3.png)

<span data-ttu-id="93300-125">Dans la barre d’outils, cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="93300-125">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="93300-126">Une page s’affiche, qui vous permet de renseigner des informations sur un nouveau billet de blog.</span><span class="sxs-lookup"><span data-stu-id="93300-126">A page then appears that allows you to fill out information about a new blog post.</span></span>

![Capture d’écran de la page Ajouter une nouvelle publication](_static/xamarin-tutorial-4.png)

<span data-ttu-id="93300-128">Renseignez toutes les informations et cliquez sur **Enregistrer** dans la barre d’outils.</span><span class="sxs-lookup"><span data-stu-id="93300-128">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="93300-129">Le nouveau billet sera associé au billet de blog sur lequel vous avez cliqué à l’étape précédente et sera enregistré dans la base de données SQLite de l’application et affiché dans la liste.</span><span class="sxs-lookup"><span data-stu-id="93300-129">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="93300-130">Revenez à la page de la liste des blogs.</span><span class="sxs-lookup"><span data-stu-id="93300-130">Go back to the blog list page.</span></span> <span data-ttu-id="93300-131">Et cliquez sur **Supprimer tout** dans la barre d’outils.</span><span class="sxs-lookup"><span data-stu-id="93300-131">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="93300-132">Tous les blogs et leurs publications correspondantes seront ensuite supprimés de la base de données SQLite de l’application.</span><span class="sxs-lookup"><span data-stu-id="93300-132">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![Capture d’écran de l’application avec tous les blogs supprimés](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="93300-134">Explorer le code</span><span class="sxs-lookup"><span data-stu-id="93300-134">Explore the code</span></span>

<span data-ttu-id="93300-135">Les sections suivantes vous guident tout au long du code de l’exemple de projet qui lit, crée, met à jour et supprime des données d’une base de données SQLite à l’aide de EF Core avec Xamarin. Forms.</span><span class="sxs-lookup"><span data-stu-id="93300-135">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="93300-136">Il est supposé que vous êtes familiarisé avec les rubriques Xamarin. Forms relatives à l' [affichage des données](/xamarin/xamarin-forms/app-fundamentals/data-binding/) et à la [navigation entre les pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span><span class="sxs-lookup"><span data-stu-id="93300-136">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="93300-137">Packages NuGet Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="93300-137">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="93300-138">Pour créer des applications Xamarin. Forms avec EF Core, vous devez installer le package pour le ou les fournisseurs de base de données EF Core que vous souhaitez cibler dans tous les projets de la solution Xamarin. Forms.</span><span class="sxs-lookup"><span data-stu-id="93300-138">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="93300-139">Ce didacticiel utilise le fournisseur SQLite.</span><span class="sxs-lookup"><span data-stu-id="93300-139">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="93300-140">Le package NuGet suivant est nécessaire dans chacun des projets de la solution Xamarin. Forms.</span><span class="sxs-lookup"><span data-stu-id="93300-140">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="93300-141">Classes de modèle</span><span class="sxs-lookup"><span data-stu-id="93300-141">Model classes</span></span>

<span data-ttu-id="93300-142">Chaque table de la base de données SQLite accessible via EF Core est modélisée dans une classe.</span><span class="sxs-lookup"><span data-stu-id="93300-142">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="93300-143">Dans cet exemple, deux classes sont utilisées : `Blog` et `Post` qui se trouvent dans le `Models` dossier.</span><span class="sxs-lookup"><span data-stu-id="93300-143">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="93300-144">Les classes de modèle sont composées uniquement de propriétés, qui modélisent les colonnes de la base de données.</span><span class="sxs-lookup"><span data-stu-id="93300-144">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="93300-145">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="93300-145">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="93300-146">La `Posts` propriété définit une relation parent-enfant entre `Blog` et `Post` .</span><span class="sxs-lookup"><span data-stu-id="93300-146">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="93300-147">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="93300-147">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="93300-148">Les `BlogId` `Blog` Propriétés et relient à l' `Blog` objet parent pour l’instance de `Post` .</span><span class="sxs-lookup"><span data-stu-id="93300-148">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="93300-149">Contexte de données</span><span class="sxs-lookup"><span data-stu-id="93300-149">Data context</span></span>

<span data-ttu-id="93300-150">La `BloggingContext` classe se trouve dans le `Services` dossier et hérite de la `DbContext` classe EF Core.</span><span class="sxs-lookup"><span data-stu-id="93300-150">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="93300-151">Un `DbContext` est utilisé pour regrouper les requêtes et les modifications de base de données.</span><span class="sxs-lookup"><span data-stu-id="93300-151">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="93300-152">Les deux propriétés de cette classe de type `DbSet` sont utilisées pour fonctionner sur les tables sous-jacentes représentant les blogs et les publications.</span><span class="sxs-lookup"><span data-stu-id="93300-152">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="93300-153">`SQLitePCL.Batteries_V2.Init()`Est nécessaire dans le constructeur pour lancer SQLite sur iOS.</span><span class="sxs-lookup"><span data-stu-id="93300-153">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="93300-154">La `OnConfiguring` fonction définit l’emplacement de la base de données SQLite sur l’appareil physique.</span><span class="sxs-lookup"><span data-stu-id="93300-154">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="93300-155">Créer, lire, mettre à jour et supprimer</span><span class="sxs-lookup"><span data-stu-id="93300-155">Create, read, update & delete</span></span>

<span data-ttu-id="93300-156">Voici quelques exemples de l’application dans laquelle EF Core est utilisé pour accéder à SQLite.</span><span class="sxs-lookup"><span data-stu-id="93300-156">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="93300-157">Lire</span><span class="sxs-lookup"><span data-stu-id="93300-157">Read</span></span>

* <span data-ttu-id="93300-158">Retourne tous les enregistrements.</span><span class="sxs-lookup"><span data-stu-id="93300-158">Return all records.</span></span>
  * <span data-ttu-id="93300-159">La `OnAppearing` fonction de `BlogsPage.xaml.cs` retourne tous les `Blog` enregistrements et les stocke dans une `List` variable.</span><span class="sxs-lookup"><span data-stu-id="93300-159">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="93300-160">Retourner des enregistrements spécifiques.</span><span class="sxs-lookup"><span data-stu-id="93300-160">Return specific records.</span></span>
  * <span data-ttu-id="93300-161">La `OnAppearing` fonction de `PostsPage.xaml.cs` retourne des `Post` enregistrements qui contiennent un spécifique `BlogId` .</span><span class="sxs-lookup"><span data-stu-id="93300-161">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="93300-162">Créer</span><span class="sxs-lookup"><span data-stu-id="93300-162">Create</span></span>

* <span data-ttu-id="93300-163">Insérez un nouvel enregistrement.</span><span class="sxs-lookup"><span data-stu-id="93300-163">Insert a new record.</span></span>
  * <span data-ttu-id="93300-164">La `Save_Clicked` fonction de `AddBlogPage.xaml.cs` insère un nouvel `Blog` objet dans la base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="93300-164">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="93300-165">Update</span><span class="sxs-lookup"><span data-stu-id="93300-165">Update</span></span>

* <span data-ttu-id="93300-166">Mettre à jour un enregistrement existant.</span><span class="sxs-lookup"><span data-stu-id="93300-166">Update an existing record.</span></span>
  * <span data-ttu-id="93300-167">La `Save_Clicked` fonction de `AddPostPage.xaml.cs` met à jour un `Blog` objet existant avec un nouveau `Post` .</span><span class="sxs-lookup"><span data-stu-id="93300-167">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
```csharp
var newPost = new Post
{
    BlogId = BlogId,
    Content = postCell.Text,
    Title = titleCell.Text
};

using (var blogContext = new BloggingContext())
{
    var blog = await blogContext
        .Blogs
        .FirstAsync(b => b.BlogId == BlogId);

    blog.Posts.Add(newPost);

    await blogContext.SaveChangesAsync();
}
```

### <a name="delete"></a><span data-ttu-id="93300-168">DELETE</span><span class="sxs-lookup"><span data-stu-id="93300-168">Delete</span></span>

* <span data-ttu-id="93300-169">Supprimer tous les enregistrements avec des enregistrements en cascade vers enfants.</span><span class="sxs-lookup"><span data-stu-id="93300-169">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="93300-170">La `DeleteAll_Clicked` fonction de `BlogsPage.xaml.cs` supprime tous les `Blog` enregistrements de la base de données SQLite et répercute les suppressions sur tous les `Blog` enregistrements enfants `Post` .</span><span class="sxs-lookup"><span data-stu-id="93300-170">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="93300-171">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="93300-171">Next steps</span></span>

<span data-ttu-id="93300-172">Dans ce guide de prise en main, vous avez appris à utiliser une application Xamarin. Forms pour accéder à une base de données SQLite à l’aide de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="93300-172">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="93300-173">Autres Entity Framework Core sujets intéressants pour les développeurs Xamarin :</span><span class="sxs-lookup"><span data-stu-id="93300-173">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="93300-174">Configuration d’un `DbContext`</span><span class="sxs-lookup"><span data-stu-id="93300-174">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="93300-175">Découvrir les [expressions de requête LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="93300-175">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="93300-176">[Configurer votre modèle](xref:core/modeling/index) pour spécifier des éléments tels que la longueur [requise](xref:core/modeling/entity-properties#required-and-optional-properties) et la longueur [maximale](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="93300-176">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>