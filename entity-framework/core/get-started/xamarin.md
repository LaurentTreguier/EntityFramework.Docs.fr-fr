---
title: Prise en main avec EF Core et Xamarin-EF Core
description: Didacticiel de mise en route pour l’utilisation de Xamarin pour créer des applications mobiles avec Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: c19c70903926c2c8ff4b51cca7781859af7e3567
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128782"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="6c25d-103">Prise en main avec EF Core et Xamarin</span><span class="sxs-lookup"><span data-stu-id="6c25d-103">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="6c25d-104">Dans ce didacticiel, vous allez créer une application [Xamarin. Forms](/xamarin/get-started/what-is-xamarin-forms) qui effectue l’accès aux données sur une base de données SQLite à l’aide de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6c25d-104">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="6c25d-105">Vous pouvez suivre le didacticiel à l’aide de Visual Studio sur Windows ou Visual Studio pour Mac.</span><span class="sxs-lookup"><span data-stu-id="6c25d-105">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="6c25d-106">Vous pouvez afficher cet [exemple sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span><span class="sxs-lookup"><span data-stu-id="6c25d-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6c25d-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="6c25d-107">Prerequisites</span></span>

<span data-ttu-id="6c25d-108">Installez l’un des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="6c25d-108">Install one of the below:</span></span>

* <span data-ttu-id="6c25d-109">[Visual Studio 2019 version 16,3 ou ultérieure](https://www.visualstudio.com/downloads/) avec cette charge de travail :</span><span class="sxs-lookup"><span data-stu-id="6c25d-109">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="6c25d-110">**Développement mobile avec .NET**</span><span class="sxs-lookup"><span data-stu-id="6c25d-110">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="6c25d-111">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="6c25d-111">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="6c25d-112">Cette [documentation fournit des instructions d’installation pas à pas détaillées](/xamarin/get-started/installation) pour chaque plateforme.</span><span class="sxs-lookup"><span data-stu-id="6c25d-112">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="6c25d-113">Télécharger et exécuter l’exemple de projet</span><span class="sxs-lookup"><span data-stu-id="6c25d-113">Download and run the sample project</span></span>

<span data-ttu-id="6c25d-114">Pour exécuter et explorer cet exemple d’application, téléchargez le code sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="6c25d-114">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="6c25d-115">Une fois téléchargé, ouvrez le fichier solution `EFGettingStarted.sln` dans Visual Studio ou Visual Studio pour Mac et exécutez l’application sur la plateforme de votre choix.</span><span class="sxs-lookup"><span data-stu-id="6c25d-115">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="6c25d-116">Lorsque l’application démarre pour la première fois, elle remplit la base de données SQLite locale avec deux entrées représentant les blogs.</span><span class="sxs-lookup"><span data-stu-id="6c25d-116">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![Capture d’écran de la page de liste de tous les blogs](_static/xamarin-tutorial-1.png)

<span data-ttu-id="6c25d-118">Cliquez sur le bouton **Ajouter** dans la barre d’outils.</span><span class="sxs-lookup"><span data-stu-id="6c25d-118">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="6c25d-119">Une nouvelle page s’affiche et vous permet d’entrer des informations sur un nouveau blog.</span><span class="sxs-lookup"><span data-stu-id="6c25d-119">A new page will appear that allows you to enter information about a new blog.</span></span>

![Capture d’écran de la page de modification de blog](_static/xamarin-tutorial-2.png)

<span data-ttu-id="6c25d-121">Renseignez toutes les informations et cliquez sur **Enregistrer** dans la barre d’outils.</span><span class="sxs-lookup"><span data-stu-id="6c25d-121">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="6c25d-122">Le nouveau blog est enregistré dans la base de données SQLite de l’application et s’affiche dans la liste.</span><span class="sxs-lookup"><span data-stu-id="6c25d-122">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="6c25d-123">Vous pouvez cliquer sur l’une des entrées de blog de la liste pour afficher les publications de ce blog.</span><span class="sxs-lookup"><span data-stu-id="6c25d-123">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![Capture d’écran de la page de liste des billets de blog](_static/xamarin-tutorial-3.png)

<span data-ttu-id="6c25d-125">Dans la barre d’outils, cliquez sur **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="6c25d-125">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="6c25d-126">Une page s’affiche, qui vous permet de renseigner des informations sur un nouveau billet de blog.</span><span class="sxs-lookup"><span data-stu-id="6c25d-126">A page then appears that allows you to fill out information about a new blog post.</span></span>

![Capture d’écran de la page Ajouter une nouvelle publication](_static/xamarin-tutorial-4.png)

<span data-ttu-id="6c25d-128">Renseignez toutes les informations et cliquez sur **Enregistrer** dans la barre d’outils.</span><span class="sxs-lookup"><span data-stu-id="6c25d-128">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="6c25d-129">Le nouveau billet sera associé au billet de blog sur lequel vous avez cliqué à l’étape précédente et sera enregistré dans la base de données SQLite de l’application et affiché dans la liste.</span><span class="sxs-lookup"><span data-stu-id="6c25d-129">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="6c25d-130">Revenez à la page de la liste des blogs.</span><span class="sxs-lookup"><span data-stu-id="6c25d-130">Go back to the blog list page.</span></span> <span data-ttu-id="6c25d-131">Et cliquez sur **Supprimer tout** dans la barre d’outils.</span><span class="sxs-lookup"><span data-stu-id="6c25d-131">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="6c25d-132">Tous les blogs et leurs publications correspondantes seront ensuite supprimés de la base de données SQLite de l’application.</span><span class="sxs-lookup"><span data-stu-id="6c25d-132">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![Capture d’écran de l’application avec tous les blogs supprimés](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="6c25d-134">Explorer le code</span><span class="sxs-lookup"><span data-stu-id="6c25d-134">Explore the code</span></span>

<span data-ttu-id="6c25d-135">Les sections suivantes vous guident tout au long du code de l’exemple de projet qui lit, crée, met à jour et supprime des données d’une base de données SQLite à l’aide de EF Core avec Xamarin. Forms.</span><span class="sxs-lookup"><span data-stu-id="6c25d-135">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="6c25d-136">Il est supposé que vous êtes familiarisé avec les rubriques Xamarin. Forms relatives à l' [affichage des données](/xamarin/xamarin-forms/app-fundamentals/data-binding/) et à la [navigation entre les pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span><span class="sxs-lookup"><span data-stu-id="6c25d-136">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6c25d-137">Entity Framework Core utilise la réflexion pour appeler des fonctions que l’éditeur de liens Xamarin. iOS peut supprimer en même temps que les configurations en mode **Release** .</span><span class="sxs-lookup"><span data-stu-id="6c25d-137">Entity Framework Core uses reflection to invoke functions which the Xamarin.iOS linker may strip out while in **Release** mode configurations.</span></span> <span data-ttu-id="6c25d-138">Vous pouvez éviter cela de deux manières.</span><span class="sxs-lookup"><span data-stu-id="6c25d-138">You can avoid that in one of two ways.</span></span>
>
> * <span data-ttu-id="6c25d-139">La première consiste à ajouter des `--linkskip System.Core` **arguments mTouch supplémentaires** dans les options de **génération iOS** .</span><span class="sxs-lookup"><span data-stu-id="6c25d-139">The first is to add `--linkskip System.Core` to the **Additional mtouch arguments** in the **iOS Build** options.</span></span>
> * <span data-ttu-id="6c25d-140">Vous pouvez également définir le comportement de l' **éditeur de liens** Xamarin. iOS sur `Don't Link` dans les options de **génération iOS** .</span><span class="sxs-lookup"><span data-stu-id="6c25d-140">Alternatively set the Xamarin.iOS **Linker behavior** to `Don't Link` in the **iOS Build** options.</span></span>
> <span data-ttu-id="6c25d-141">[Cet article explique plus en détail l’éditeur de liens Xamarin. iOS](/xamarin/ios/deploy-test/linker) , y compris comment définir le comportement sur Xamarin. iOS.</span><span class="sxs-lookup"><span data-stu-id="6c25d-141">[This article explains more about the Xamarin.iOS linker](/xamarin/ios/deploy-test/linker) including how to set the behavior on Xamarin.iOS.</span></span>
>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="6c25d-142">Packages NuGet Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6c25d-142">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="6c25d-143">Pour créer des applications Xamarin. Forms avec EF Core, vous devez installer le package pour le ou les fournisseurs de base de données EF Core que vous souhaitez cibler dans tous les projets de la solution Xamarin. Forms.</span><span class="sxs-lookup"><span data-stu-id="6c25d-143">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="6c25d-144">Ce didacticiel utilise le fournisseur SQLite.</span><span class="sxs-lookup"><span data-stu-id="6c25d-144">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="6c25d-145">Le package NuGet suivant est nécessaire dans chacun des projets de la solution Xamarin. Forms.</span><span class="sxs-lookup"><span data-stu-id="6c25d-145">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="6c25d-146">Classes de modèle</span><span class="sxs-lookup"><span data-stu-id="6c25d-146">Model classes</span></span>

<span data-ttu-id="6c25d-147">Chaque table de la base de données SQLite accessible via EF Core est modélisée dans une classe.</span><span class="sxs-lookup"><span data-stu-id="6c25d-147">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="6c25d-148">Dans cet exemple, deux classes sont utilisées : `Blog` et `Post` qui se trouvent dans le `Models` dossier.</span><span class="sxs-lookup"><span data-stu-id="6c25d-148">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="6c25d-149">Les classes de modèle sont composées uniquement de propriétés, qui modélisent les colonnes de la base de données.</span><span class="sxs-lookup"><span data-stu-id="6c25d-149">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="6c25d-150">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="6c25d-150">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="6c25d-151">La `Posts` propriété définit une relation parent-enfant entre `Blog` et `Post` .</span><span class="sxs-lookup"><span data-stu-id="6c25d-151">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="6c25d-152">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="6c25d-152">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="6c25d-153">Les `BlogId` `Blog` Propriétés et relient à l' `Blog` objet parent pour l’instance de `Post` .</span><span class="sxs-lookup"><span data-stu-id="6c25d-153">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="6c25d-154">Contexte de données</span><span class="sxs-lookup"><span data-stu-id="6c25d-154">Data context</span></span>

<span data-ttu-id="6c25d-155">La `BloggingContext` classe se trouve dans le `Services` dossier et hérite de la `DbContext` classe EF Core.</span><span class="sxs-lookup"><span data-stu-id="6c25d-155">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="6c25d-156">Un `DbContext` est utilisé pour regrouper les requêtes et les modifications de base de données.</span><span class="sxs-lookup"><span data-stu-id="6c25d-156">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="6c25d-157">Les deux propriétés de cette classe de type `DbSet` sont utilisées pour fonctionner sur les tables sous-jacentes représentant les blogs et les publications.</span><span class="sxs-lookup"><span data-stu-id="6c25d-157">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="6c25d-158">`SQLitePCL.Batteries_V2.Init()`Est nécessaire dans le constructeur pour lancer SQLite sur iOS.</span><span class="sxs-lookup"><span data-stu-id="6c25d-158">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="6c25d-159">La `OnConfiguring` fonction définit l’emplacement de la base de données SQLite sur l’appareil physique.</span><span class="sxs-lookup"><span data-stu-id="6c25d-159">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="6c25d-160">Créer, lire, mettre à jour et supprimer</span><span class="sxs-lookup"><span data-stu-id="6c25d-160">Create, read, update & delete</span></span>

<span data-ttu-id="6c25d-161">Voici quelques exemples de l’application dans laquelle EF Core est utilisé pour accéder à SQLite.</span><span class="sxs-lookup"><span data-stu-id="6c25d-161">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="6c25d-162">Lecture</span><span class="sxs-lookup"><span data-stu-id="6c25d-162">Read</span></span>

* <span data-ttu-id="6c25d-163">Retourne tous les enregistrements.</span><span class="sxs-lookup"><span data-stu-id="6c25d-163">Return all records.</span></span>
  * <span data-ttu-id="6c25d-164">La `OnAppearing` fonction de `BlogsPage.xaml.cs` retourne tous les `Blog` enregistrements et les stocke dans une `List` variable.</span><span class="sxs-lookup"><span data-stu-id="6c25d-164">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="6c25d-165">Retourner des enregistrements spécifiques.</span><span class="sxs-lookup"><span data-stu-id="6c25d-165">Return specific records.</span></span>
  * <span data-ttu-id="6c25d-166">La `OnAppearing` fonction de `PostsPage.xaml.cs` retourne des `Post` enregistrements qui contiennent un spécifique `BlogId` .</span><span class="sxs-lookup"><span data-stu-id="6c25d-166">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="6c25d-167">Créer</span><span class="sxs-lookup"><span data-stu-id="6c25d-167">Create</span></span>

* <span data-ttu-id="6c25d-168">Insérez un nouvel enregistrement.</span><span class="sxs-lookup"><span data-stu-id="6c25d-168">Insert a new record.</span></span>
  * <span data-ttu-id="6c25d-169">La `Save_Clicked` fonction de `AddBlogPage.xaml.cs` insère un nouvel `Blog` objet dans la base de données SQLite.</span><span class="sxs-lookup"><span data-stu-id="6c25d-169">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="6c25d-170">Update</span><span class="sxs-lookup"><span data-stu-id="6c25d-170">Update</span></span>

* <span data-ttu-id="6c25d-171">Mettre à jour un enregistrement existant.</span><span class="sxs-lookup"><span data-stu-id="6c25d-171">Update an existing record.</span></span>
  * <span data-ttu-id="6c25d-172">La `Save_Clicked` fonction de `AddPostPage.xaml.cs` met à jour un `Blog` objet existant avec un nouveau `Post` .</span><span class="sxs-lookup"><span data-stu-id="6c25d-172">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>

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

### <a name="delete"></a><span data-ttu-id="6c25d-173">Supprimer</span><span class="sxs-lookup"><span data-stu-id="6c25d-173">Delete</span></span>

* <span data-ttu-id="6c25d-174">Supprimer tous les enregistrements avec des enregistrements en cascade vers enfants.</span><span class="sxs-lookup"><span data-stu-id="6c25d-174">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="6c25d-175">La `DeleteAll_Clicked` fonction de `BlogsPage.xaml.cs` supprime tous les `Blog` enregistrements de la base de données SQLite et répercute les suppressions sur tous les `Blog` enregistrements enfants `Post` .</span><span class="sxs-lookup"><span data-stu-id="6c25d-175">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="6c25d-176">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="6c25d-176">Next steps</span></span>

<span data-ttu-id="6c25d-177">Dans ce guide de prise en main, vous avez appris à utiliser une application Xamarin. Forms pour accéder à une base de données SQLite à l’aide de Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6c25d-177">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="6c25d-178">Autres Entity Framework Core sujets intéressants pour les développeurs Xamarin :</span><span class="sxs-lookup"><span data-stu-id="6c25d-178">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="6c25d-179">Configuration d’un `DbContext`</span><span class="sxs-lookup"><span data-stu-id="6c25d-179">Configuring a `DbContext`</span></span>](xref:core/dbcontext-configuration/index)
* <span data-ttu-id="6c25d-180">Découvrir les [expressions de requête LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="6c25d-180">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="6c25d-181">[Configurer votre modèle](xref:core/modeling/index) pour spécifier des éléments tels que la longueur [requise](xref:core/modeling/entity-properties#required-and-optional-properties) et la longueur [maximale](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="6c25d-181">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
