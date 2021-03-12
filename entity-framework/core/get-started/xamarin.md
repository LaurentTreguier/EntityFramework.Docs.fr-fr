---
title: Prise en main avec EF Core et Xamarin-EF Core
description: Didacticiel de mise en route pour l’utilisation de Xamarin pour créer des applications mobiles avec Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: edec7dfec3d6ac8313f9fc07af8844e4a19c3bb2
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024275"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a>Prise en main avec EF Core et Xamarin

Dans ce didacticiel, vous allez créer une application [Xamarin. Forms](/xamarin/get-started/what-is-xamarin-forms) qui effectue l’accès aux données sur une base de données SQLite à l’aide de Entity Framework Core.

Vous pouvez suivre le didacticiel à l’aide de Visual Studio sur Windows ou Visual Studio pour Mac.

> [!TIP]
> Vous pouvez afficher cet [exemple sur GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Xamarin).

## <a name="prerequisites"></a>Prérequis

Installez l’un des éléments suivants :

* [Visual Studio 2019 version 16,3 ou ultérieure](https://www.visualstudio.com/downloads/) avec cette charge de travail :
  * **Développement mobile avec .NET**
* [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/)

Cette [documentation fournit des instructions d’installation pas à pas détaillées](/xamarin/get-started/installation) pour chaque plateforme.

## <a name="download-and-run-the-sample-project"></a>Télécharger et exécuter l’exemple de projet

Pour exécuter et explorer cet exemple d’application, téléchargez le code sur GitHub.

Une fois téléchargé, ouvrez le fichier solution `EFGettingStarted.sln` dans Visual Studio ou Visual Studio pour Mac et exécutez l’application sur la plateforme de votre choix.

Lorsque l’application démarre pour la première fois, elle remplit la base de données SQLite locale avec deux entrées représentant les blogs.

![Capture d’écran de la page de liste de tous les blogs](_static/xamarin-tutorial-1.png)

Cliquez sur le bouton **Ajouter** dans la barre d’outils.

Une nouvelle page s’affiche et vous permet d’entrer des informations sur un nouveau blog.

![Capture d’écran de la page de modification de blog](_static/xamarin-tutorial-2.png)

Renseignez toutes les informations et cliquez sur **Enregistrer** dans la barre d’outils. Le nouveau blog est enregistré dans la base de données SQLite de l’application et s’affiche dans la liste.

Vous pouvez cliquer sur l’une des entrées de blog de la liste pour afficher les publications de ce blog.

![Capture d’écran de la page de liste des billets de blog](_static/xamarin-tutorial-3.png)

Dans la barre d’outils, cliquez sur **Ajouter** .

Une page s’affiche, qui vous permet de renseigner des informations sur un nouveau billet de blog.

![Capture d’écran de la page Ajouter une nouvelle publication](_static/xamarin-tutorial-4.png)

Renseignez toutes les informations et cliquez sur **Enregistrer** dans la barre d’outils.

Le nouveau billet sera associé au billet de blog sur lequel vous avez cliqué à l’étape précédente et sera enregistré dans la base de données SQLite de l’application et affiché dans la liste.

Revenez à la page de la liste des blogs. Et cliquez sur **Supprimer tout** dans la barre d’outils. Tous les blogs et leurs publications correspondantes seront ensuite supprimés de la base de données SQLite de l’application.

![Capture d’écran de l’application avec tous les blogs supprimés](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a>Explorer le code

Les sections suivantes vous guident tout au long du code de l’exemple de projet qui lit, crée, met à jour et supprime des données d’une base de données SQLite à l’aide de EF Core avec Xamarin. Forms.

Il est supposé que vous êtes familiarisé avec les rubriques Xamarin. Forms relatives à l' [affichage des données](/xamarin/xamarin-forms/app-fundamentals/data-binding/) et à la [navigation entre les pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).

> [!IMPORTANT]
> Entity Framework Core utilise la réflexion pour appeler des fonctions que l’éditeur de liens Xamarin. iOS peut supprimer en même temps que les configurations en mode **Release** . Vous pouvez éviter cela de deux manières.
>
> * La première consiste à ajouter des `--linkskip System.Core` **arguments mTouch supplémentaires** dans les options de **génération iOS** .
> * Vous pouvez également définir le comportement de l' **éditeur de liens** Xamarin. iOS sur `Don't Link` dans les options de **génération iOS** .
> [Cet article explique plus en détail l’éditeur de liens Xamarin. iOS](/xamarin/ios/deploy-test/linker) , y compris comment définir le comportement sur Xamarin. iOS. (Cette approche n’est pas idéale, car elle peut entraîner le rejet du magasin).

## <a name="entity-framework-core-nuget-packages"></a>Packages NuGet Entity Framework Core

Pour créer des applications Xamarin. Forms avec EF Core, vous devez installer le package pour le ou les fournisseurs de base de données EF Core que vous souhaitez cibler dans tous les projets de la solution Xamarin. Forms. Ce didacticiel utilise le fournisseur SQLite.

Le package NuGet suivant est nécessaire dans chacun des projets de la solution Xamarin. Forms.

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a>Classes de modèle

Chaque table de la base de données SQLite accessible via EF Core est modélisée dans une classe. Dans cet exemple, deux classes sont utilisées : `Blog` et `Post` qui se trouvent dans le `Models` dossier.

Les classes de modèle sont composées uniquement de propriétés, qui modélisent les colonnes de la base de données.

* **Blog.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* La `Posts` propriété définit une relation parent-enfant entre `Blog` et `Post` .

* **Post.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* Les `BlogId` `Blog` Propriétés et relient à l' `Blog` objet parent pour l’instance de `Post` .

## <a name="data-context"></a>Contexte de données

La `BloggingContext` classe se trouve dans le `Services` dossier et hérite de la `DbContext` classe EF Core. Un `DbContext` est utilisé pour regrouper les requêtes et les modifications de base de données.

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* Les deux propriétés de cette classe de type `DbSet` sont utilisées pour fonctionner sur les tables sous-jacentes représentant les blogs et les publications.
* `SQLitePCL.Batteries_V2.Init()`Est nécessaire dans le constructeur pour lancer SQLite sur iOS.
* La `OnConfiguring` fonction définit l’emplacement de la base de données SQLite sur l’appareil physique.

## <a name="create-read-update--delete"></a>Créer, lire, mettre à jour et supprimer

Voici quelques exemples de l’application dans laquelle EF Core est utilisé pour accéder à SQLite.

### <a name="read"></a>Lire

* Retourne tous les enregistrements.
  * La `OnAppearing` fonction de `BlogsPage.xaml.cs` retourne tous les `Blog` enregistrements et les stocke dans une `List` variable.

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* Retourner des enregistrements spécifiques.
  * La `OnAppearing` fonction de `PostsPage.xaml.cs` retourne des `Post` enregistrements qui contiennent un spécifique `BlogId` .

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a>Créer

* Insérez un nouvel enregistrement.
  * La `Save_Clicked` fonction de `AddBlogPage.xaml.cs` insère un nouvel `Blog` objet dans la base de données SQLite.

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a>Update

* Mettre à jour un enregistrement existant.
  * La `Save_Clicked` fonction de `AddPostPage.xaml.cs` met à jour un `Blog` objet existant avec un nouveau `Post` .

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

### <a name="delete"></a>Supprimer

* Supprimer tous les enregistrements avec des enregistrements en cascade vers enfants.
  * La `DeleteAll_Clicked` fonction de `BlogsPage.xaml.cs` supprime tous les `Blog` enregistrements de la base de données SQLite et répercute les suppressions sur tous les `Blog` enregistrements enfants `Post` .

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de prise en main, vous avez appris à utiliser une application Xamarin. Forms pour accéder à une base de données SQLite à l’aide de Entity Framework Core.

Autres Entity Framework Core sujets intéressants pour les développeurs Xamarin :

* [Configuration d’un `DbContext`](xref:core/dbcontext-configuration/index)
* Découvrir les [expressions de requête LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [Configurer votre modèle](xref:core/modeling/index) pour spécifier des éléments tels que la longueur [requise](xref:core/modeling/entity-properties#required-and-optional-properties) et la longueur [maximale](xref:core/modeling/entity-properties#maximum-length)
