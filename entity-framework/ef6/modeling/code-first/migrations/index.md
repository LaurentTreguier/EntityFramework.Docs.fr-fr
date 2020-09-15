---
title: Migrations Code First - EF6
description: Migrations Code First dans Entity Framework 6.
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/index
ms.openlocfilehash: aec3a53816e41bf7ead696fd9edc30618d58b07a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072159"
---
# <a name="code-first-migrations"></a><span data-ttu-id="9d5d7-103">Migrations Code First</span><span class="sxs-lookup"><span data-stu-id="9d5d7-103">Code First Migrations</span></span>
<span data-ttu-id="9d5d7-104">Migrations Code First est la méthode recommandée pour faire évoluer votre schéma de base de données d’application si vous utilisez le flux de travail Code First.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-104">Code First Migrations is the recommended way to evolve your application's database schema if you are using the Code First workflow.</span></span> <span data-ttu-id="9d5d7-105">Les migrations fournissent un ensemble d’outils pour les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="9d5d7-105">Migrations provide a set of tools that allow:</span></span>

1. <span data-ttu-id="9d5d7-106">Créer une base de données initiale qui fonctionne avec votre modèle EF</span><span class="sxs-lookup"><span data-stu-id="9d5d7-106">Create an initial database that works with your EF model</span></span>
2. <span data-ttu-id="9d5d7-107">Générer des migrations pour suivre les changements que vous appliquez à votre modèle EF</span><span class="sxs-lookup"><span data-stu-id="9d5d7-107">Generating migrations to keep track of changes you make to your EF model</span></span>
2. <span data-ttu-id="9d5d7-108">Mettre à jour votre base de données avec ces changements</span><span class="sxs-lookup"><span data-stu-id="9d5d7-108">Keep your database up to date with those changes</span></span>

<span data-ttu-id="9d5d7-109">La procédure suivante fournit une vue d’ensemble de Migrations Code First dans Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-109">The following walkthrough will provide an overview Code First Migrations in Entity Framework.</span></span> <span data-ttu-id="9d5d7-110">Vous pouvez suivre l’intégralité de la procédure pas à pas ou passer à la rubrique qui vous intéresse.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-110">You can either complete the entire walkthrough or skip to the topic you are interested in.</span></span> <span data-ttu-id="9d5d7-111">Les rubriques suivantes sont traitées :</span><span class="sxs-lookup"><span data-stu-id="9d5d7-111">The following topics are covered:</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="9d5d7-112">Génération d’un modèle et d’une base de données initiaux</span><span class="sxs-lookup"><span data-stu-id="9d5d7-112">Building an Initial Model & Database</span></span>

<span data-ttu-id="9d5d7-113">Avant de commencer à utiliser les migrations, nous avons besoin d’un projet et d’un modèle Code First.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-113">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="9d5d7-114">Pour cette procédure pas à pas nous utilisons le modèle canonique **Blog** et **Post**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-114">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="9d5d7-115">Créez une application console **MigrationsDemo**</span><span class="sxs-lookup"><span data-stu-id="9d5d7-115">Create a new **MigrationsDemo** Console application</span></span>
-   <span data-ttu-id="9d5d7-116">Ajoutez la dernière version du package NuGet **EntityFramework** au projet</span><span class="sxs-lookup"><span data-stu-id="9d5d7-116">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="9d5d7-117">**Outils -&gt; Gestionnaire de package de bibliothèque -&gt; Console du Gestionnaire de package**</span><span class="sxs-lookup"><span data-stu-id="9d5d7-117">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="9d5d7-118">Exécutez la commande **Install-Package EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="9d5d7-118">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="9d5d7-119">Ajoutez un fichier **Model.cs** avec le code indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-119">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="9d5d7-120">Ce code définit une seule classe **Blog** qui constitue notre modèle de domaine et une classe **BlogContext** qui est notre contexte EF Code First</span><span class="sxs-lookup"><span data-stu-id="9d5d7-120">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   <span data-ttu-id="9d5d7-121">Maintenant que nous avons un modèle, utilisons-le pour accéder aux données.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-121">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="9d5d7-122">Mettez à jour le fichier **Program.cs** avec le code indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-122">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   <span data-ttu-id="9d5d7-123">Exécutez votre application : une base de données **MigrationsCodeDemo.BlogContext** est créée pour vous.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-123">Run your application and you will see that a **MigrationsCodeDemo.BlogContext** database is created for you.</span></span>

    ![Base de données locale](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="9d5d7-125">Activation des migrations</span><span class="sxs-lookup"><span data-stu-id="9d5d7-125">Enabling Migrations</span></span>

<span data-ttu-id="9d5d7-126">Modifions un peu plus notre modèle.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-126">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="9d5d7-127">Nous introduisons une propriété Url à la classe Blog.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-127">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="9d5d7-128">Si vous réexécutez l’application, vous obtenez une exception InvalidOperationException indiquant *Le modèle permettant la sauvegarde du contexte 'BlogContext' a changé depuis la création de la base de données. Utilisez Migrations Code First pour mettre à jour la base de données (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*</span><span class="sxs-lookup"><span data-stu-id="9d5d7-128">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="9d5d7-129">Comme le suggère l’exception, utilisons donc Migrations Code First.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-129">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="9d5d7-130">La première étape est d’activer les migrations pour notre contexte.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-130">The first step is to enable migrations for our context.</span></span>

-   <span data-ttu-id="9d5d7-131">Exécutez la commande **Enable-Migrations** dans la Console du Gestionnaire de Package</span><span class="sxs-lookup"><span data-stu-id="9d5d7-131">Run the **Enable-Migrations** command in Package Manager Console</span></span>

    <span data-ttu-id="9d5d7-132">Cette commande a ajouté un dossier **Migrations** à notre projet.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-132">This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="9d5d7-133">Ce nouveau dossier contient deux fichiers :</span><span class="sxs-lookup"><span data-stu-id="9d5d7-133">This new folder contains two files:</span></span>

-   <span data-ttu-id="9d5d7-134">**La classe Configuration.**</span><span class="sxs-lookup"><span data-stu-id="9d5d7-134">**The Configuration class.**</span></span> <span data-ttu-id="9d5d7-135">Cette classe vous permet de configurer le comportement des migrations pour votre contexte.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-135">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="9d5d7-136">Pour cette procédure pas à pas, nous utilisons simplement la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-136">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="9d5d7-137">*Comme il n’y a qu’un seul contexte Code First dans votre projet, Enable-Migrations a automatiquement renseigné le type de contexte auquel s’applique cette configuration.*</span><span class="sxs-lookup"><span data-stu-id="9d5d7-137">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>
-   <span data-ttu-id="9d5d7-138">**Une migration InitialCreate**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-138">**An InitialCreate migration**.</span></span> <span data-ttu-id="9d5d7-139">Cette migration a été générée, car Code First a déjà créé une base de données pour nous, avant l’activation des migrations.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-139">This migration was generated because we already had Code First create a database for us, before we enabled migrations.</span></span> <span data-ttu-id="9d5d7-140">Le code dans cette migration générée automatiquement représente les objets qui ont déjà été créés dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-140">The code in this scaffolded migration represents the objects that have already been created in the database.</span></span> <span data-ttu-id="9d5d7-141">Dans notre cas, c’est la table **Blog** avec des colonnes **BlogId** et **Name**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-141">In our case that is the **Blog** table with a **BlogId** and **Name** columns.</span></span> <span data-ttu-id="9d5d7-142">Le nom de fichier contient un horodatage pour faciliter le classement.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-142">The filename includes a timestamp to help with ordering.</span></span>
    <span data-ttu-id="9d5d7-143">*Si la base de données n’a pas encore été créée, cette migration InitialCreate ne peut pas avoir été ajoutée au projet. À la place, la première fois que nous appelons Add-Migration, le code permettant de créer ces tables est généré automatiquement dans une nouvelle migration.*</span><span class="sxs-lookup"><span data-stu-id="9d5d7-143">*If the database had not already been created this InitialCreate migration would not have been added to the project. Instead, the first time we call Add-Migration the code to create these tables would be scaffolded to a new migration.*</span></span>

### <a name="multiple-models-targeting-the-same-database"></a><span data-ttu-id="9d5d7-144">Plusieurs modèles ciblant la même base de données</span><span class="sxs-lookup"><span data-stu-id="9d5d7-144">Multiple Models Targeting the Same Database</span></span>

<span data-ttu-id="9d5d7-145">Quand vous utilisez des versions antérieures à EF6, un seul modèle Code First peut servir à générer/gérer le schéma d’une base de données.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-145">When using versions prior to EF6, only one Code First model could be used to generate/manage the schema of a database.</span></span> <span data-ttu-id="9d5d7-146">Voici le résultat d’une seule table **\_\_MigrationsHistory** par base de données sans aucun moyen d’identifier les entrées qui appartiennent à tel ou tel modèle.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-146">This is the result of a single **\_\_MigrationsHistory** table per database with no way to identify which entries belong to which model.</span></span>

<span data-ttu-id="9d5d7-147">À partir d’EF6, la classe **Configuration** inclut une propriété **ContextKey**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-147">Starting with EF6, the **Configuration** class includes a **ContextKey** property.</span></span> <span data-ttu-id="9d5d7-148">Elle agit comme un identificateur unique pour chaque modèle Code First.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-148">This acts as a unique identifier for each Code First model.</span></span> <span data-ttu-id="9d5d7-149">Une colonne correspondante dans la table **\_\_MigrationsHistory** autorise des entrées provenant de plusieurs modèles pour partager la table.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-149">A corresponding column in the **\_\_MigrationsHistory** table allows entries from multiple models to share the table.</span></span> <span data-ttu-id="9d5d7-150">Par défaut, cette propriété est définie sur le nom complet de votre contexte.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-150">By default, this property is set to the fully qualified name of your context.</span></span>

## <a name="generating--running-migrations"></a><span data-ttu-id="9d5d7-151">Génération et exécution des migrations</span><span class="sxs-lookup"><span data-stu-id="9d5d7-151">Generating & Running Migrations</span></span>

<span data-ttu-id="9d5d7-152">Migrations Code First a deux commandes principales que nous allons découvrir maintenant.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-152">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="9d5d7-153">**Add-Migration** génère automatiquement la prochaine migration en fonction des changements de votre modèle depuis la création de la dernière migration</span><span class="sxs-lookup"><span data-stu-id="9d5d7-153">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="9d5d7-154">**Update-Database** applique toutes les migrations en attente à la base de données</span><span class="sxs-lookup"><span data-stu-id="9d5d7-154">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="9d5d7-155">Nous avons besoin de générer automatiquement une migration pour prendre en charge de la nouvelle propriété Url que nous avons ajoutée.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-155">We need to scaffold a migration to take care of the new Url property we have added.</span></span> <span data-ttu-id="9d5d7-156">La commande **Add-Migration** permet de nommer ces migrations. Appelons la nôtre **AddBlogUrl**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-156">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogUrl**.</span></span>

-   <span data-ttu-id="9d5d7-157">Exécutez la commande **Add-Migration AddBlogUrl** dans la Console du Gestionnaire de Package</span><span class="sxs-lookup"><span data-stu-id="9d5d7-157">Run the **Add-Migration AddBlogUrl** command in Package Manager Console</span></span>
-   <span data-ttu-id="9d5d7-158">Dans le dossier **Migrations**, nous avons maintenant une nouvelle migration **AddBlogUrl**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-158">In the **Migrations** folder we now have a new **AddBlogUrl** migration.</span></span> <span data-ttu-id="9d5d7-159">Le nom de fichier de la migration est préfixé avec un horodatage pour faciliter le classement</span><span class="sxs-lookup"><span data-stu-id="9d5d7-159">The migration filename is pre-fixed with a timestamp to help with ordering</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Blogs", "Url", c => c.String());
            }

            public override void Down()
            {
                DropColumn("dbo.Blogs", "Url");
            }
        }
    }
```

<span data-ttu-id="9d5d7-160">Nous pouvons maintenant apporter des modifications ou effectuer des ajouts à cette migration, mais tout semble convenable.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-160">We could now edit or add to this migration but everything looks pretty good.</span></span> <span data-ttu-id="9d5d7-161">Utilisons **Update-Database** pour appliquer cette migration à la base de données.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-161">Let’s use **Update-Database** to apply this migration to the database.</span></span>

-   <span data-ttu-id="9d5d7-162">Exécutez la commande **Update-Database** dans la Console du Gestionnaire de Package</span><span class="sxs-lookup"><span data-stu-id="9d5d7-162">Run the **Update-Database** command in Package Manager Console</span></span>
-   <span data-ttu-id="9d5d7-163">Migrations Code First compare les migrations de notre dossier **Migrations** avec celles qui ont été appliquées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-163">Code First Migrations will compare the migrations in our **Migrations** folder with the ones that have been applied to the database.</span></span> <span data-ttu-id="9d5d7-164">Il voit que la migration **AddBlogUrl** doit être appliquée et l’exécute.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-164">It will see that the **AddBlogUrl** migration needs to be applied, and run it.</span></span>

<span data-ttu-id="9d5d7-165">La base de données **MigrationsDemo.BlogContext** est désormais mise à jour pour inclure la colonne **Url** dans la table **Blogs**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-165">The **MigrationsDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

## <a name="customizing-migrations"></a><span data-ttu-id="9d5d7-166">Personnalisation des migrations</span><span class="sxs-lookup"><span data-stu-id="9d5d7-166">Customizing Migrations</span></span>

<span data-ttu-id="9d5d7-167">Jusqu’à présent, nous avons généré et exécuté une migration sans la modifier.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-167">So far we’ve generated and run a migration without making any changes.</span></span> <span data-ttu-id="9d5d7-168">Nous allons maintenant modifier le code généré par défaut.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-168">Now let’s look at editing the code that gets generated by default.</span></span>

-   <span data-ttu-id="9d5d7-169">Modifions notre modèle en ajoutant une nouvelle propriété **Rating** à la classe **Blog**</span><span class="sxs-lookup"><span data-stu-id="9d5d7-169">It’s time to make some more changes to our model, let’s add a new **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

-   <span data-ttu-id="9d5d7-170">Ajoutons aussi une nouvelle classe **Post**</span><span class="sxs-lookup"><span data-stu-id="9d5d7-170">Let's also add a new **Post** class</span></span>

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   <span data-ttu-id="9d5d7-171">Nous allons aussi ajouter une collection **Posts** à la classe **Blog** pour former l’autre extrémité de la relation entre **Blog** et **Post**</span><span class="sxs-lookup"><span data-stu-id="9d5d7-171">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="9d5d7-172">Nous utilisons la commande **Add-Migration** pour permettre à Migrations Code First de générer automatiquement la migration qui convient le mieux selon lui.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-172">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span> <span data-ttu-id="9d5d7-173">Appelons cette migration **AddPostClass**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-173">We’re going to call this migration **AddPostClass**.</span></span>

-   <span data-ttu-id="9d5d7-174">Exécutez la commande **Add-Migration AddPostClass** dans la Console du Gestionnaire de Package.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-174">Run the **Add-Migration AddPostClass** command in Package Manager Console.</span></span>

<span data-ttu-id="9d5d7-175">Migrations Code First a obtenu de bons résultats en générant automatiquement ces changements, mais nous voulons modifier certaines choses :</span><span class="sxs-lookup"><span data-stu-id="9d5d7-175">Code First Migrations did a pretty good job of scaffolding these changes, but there are some things we might want to change:</span></span>

1.  <span data-ttu-id="9d5d7-176">Tout d’abord, nous ajoutons un index unique à la colonne **Posts.Title** (ajout aux lignes 22 et 29 dans le code ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="9d5d7-176">First up, let’s add a unique index to **Posts.Title** column (Adding in line 22 & 29 in the code below).</span></span>
2.  <span data-ttu-id="9d5d7-177">Nous ajoutons aussi une colonne **Blogs.Rating** qui n’autorise pas les valeurs Null.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-177">We’re also adding a non-nullable **Blogs.Rating** column.</span></span> <span data-ttu-id="9d5d7-178">Si des données existent déjà dans la table, elles reçoivent le type de données CLR par défaut pour la nouvelle colonne (Rating est un entier, donc la valeur est **0**).</span><span class="sxs-lookup"><span data-stu-id="9d5d7-178">If there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="9d5d7-179">Toutefois, nous voulons spécifier une valeur par défaut égale à **3** pour que les lignes existantes de la table **Blogs** commencent avec un classement correct.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-179">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
    <span data-ttu-id="9d5d7-180">(Vous pouvez voir la valeur par défaut spécifiée sur la ligne 24 du code ci-dessous)</span><span class="sxs-lookup"><span data-stu-id="9d5d7-180">(You can see the default value specified on line 24 of the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

<span data-ttu-id="9d5d7-181">Notre migration modifiée est prête, utilisons **Update-Database** pour mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-181">Our edited migration is ready to go, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="9d5d7-182">Cette fois, nous spécifions l’indicateur **–Verbose** pour pouvoir voir le code SQL que Migrations Code First exécute.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-182">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="9d5d7-183">Exécutez la commande **Update-Database –Verbose** dans la Console du Gestionnaire de Package.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-183">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="data-motion--custom-sql"></a><span data-ttu-id="9d5d7-184">Déplacement de données / code SQL personnalisé</span><span class="sxs-lookup"><span data-stu-id="9d5d7-184">Data Motion / Custom SQL</span></span>

<span data-ttu-id="9d5d7-185">Jusqu’à présent, nous avons vu des opérations de migration qui ne changent pas ni ne déplacent des données. Voyons maintenant un cas où nous avons besoin de déplacer des données.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-185">So far we have looked at migration operations that don’t change or move any data, now let’s look at something that needs to move some data around.</span></span> <span data-ttu-id="9d5d7-186">Le déplacement de données n’est pas encore pris en charge de manière native, mais nous pouvons exécuter des commandes SQL arbitraires à tout moment dans notre script.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-186">There is no native support for data motion yet, but we can run some arbitrary SQL commands at any point in our script.</span></span>

-   <span data-ttu-id="9d5d7-187">Ajoutons une propriété **Post.Abstract** à notre modèle.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-187">Let’s add a **Post.Abstract** property to our model.</span></span> <span data-ttu-id="9d5d7-188">Plus tard, nous allons préremplir la colonne **Abstract** des publications existantes à l’aide de texte du début de la colonne **Content**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-188">Later, we’re going to pre-populate the **Abstract** for existing posts using some text from the start of the **Content** column.</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="9d5d7-189">Nous utilisons la commande **Add-Migration** pour permettre à Migrations Code First de générer automatiquement la migration qui convient le mieux selon lui.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-189">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span>

-   <span data-ttu-id="9d5d7-190">Exécutez la commande **Add-Migration AddPostAbstract** dans la Console du Gestionnaire de Package.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-190">Run the **Add-Migration AddPostAbstract** command in Package Manager Console.</span></span>
-   <span data-ttu-id="9d5d7-191">La migration générée prend en charge les changements de schéma, mais nous voulons aussi préremplir la colonne **Abstract** avec les 100 premiers caractères du contenu de chaque publication.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-191">The generated migration takes care of the schema changes but we also want to pre-populate the **Abstract** column using the first 100 characters of content for each post.</span></span> <span data-ttu-id="9d5d7-192">Pour ce faire, nous repassons à SQL et exécutons une instruction **UPDATE** après l’ajout de la colonne.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-192">We can do this by dropping down to SQL and running an **UPDATE** statement after the column is added.</span></span>
    <span data-ttu-id="9d5d7-193">(Ajout à la ligne 12 dans le code ci-dessous)</span><span class="sxs-lookup"><span data-stu-id="9d5d7-193">(Adding in line 12 in the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

<span data-ttu-id="9d5d7-194">Notre migration modifiée est prête, utilisons **Update-Database** pour mettre à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-194">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="9d5d7-195">Spécifions l’indicateur **–Verbose** pour pouvoir voir le code SQL exécuté sur la base de données.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-195">We’ll specify the **–Verbose** flag so that we can see the SQL being run against the database.</span></span>

-   <span data-ttu-id="9d5d7-196">Exécutez la commande **Update-Database –Verbose** dans la Console du Gestionnaire de Package.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-196">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="migrate-to-a-specific-version-including-downgrade"></a><span data-ttu-id="9d5d7-197">Migrer vers une version spécifique (y compris vers une version antérieure)</span><span class="sxs-lookup"><span data-stu-id="9d5d7-197">Migrate to a Specific Version (Including Downgrade)</span></span>

<span data-ttu-id="9d5d7-198">Jusqu’à présent, nous avons toujours effectué la mise à niveau vers la dernière migration, mais vous pouvez effectuer une mise à niveau vers une version spécifique, voire une version antérieure.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-198">So far we have always upgraded to the latest migration, but there may be times when you want upgrade/downgrade to a specific migration.</span></span>

<span data-ttu-id="9d5d7-199">Supposons que nous voulons migrer notre base de données dans l’état où elle se trouvait après l’exécution de notre migration **AddBlogUrl**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-199">Let’s say we want to migrate our database to the state it was in after running our **AddBlogUrl** migration.</span></span> <span data-ttu-id="9d5d7-200">Nous pouvons utiliser le commutateur **–TargetMigration** pour passer à cette migration antérieure.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-200">We can use the **–TargetMigration** switch to downgrade to this migration.</span></span>

-   <span data-ttu-id="9d5d7-201">Exécutez la commande **Update-Database –TargetMigration: AddBlogUrl** dans la Console du Gestionnaire de Package.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-201">Run the **Update-Database –TargetMigration: AddBlogUrl** command in Package Manager Console.</span></span>

<span data-ttu-id="9d5d7-202">Cette commande exécute le script de passage à une version antérieure pour nos migrations **AddBlogAbstract** et **AddPostClass**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-202">This command will run the Down script for our **AddBlogAbstract** and **AddPostClass** migrations.</span></span>

<span data-ttu-id="9d5d7-203">Si vous voulez restaurer la base de données vide, vous pouvez utiliser la commande **Update-Database –TargetMigration: $InitialDatabase**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-203">If you want to roll all the way back to an empty database then you can use the **Update-Database –TargetMigration: $InitialDatabase** command.</span></span>

## <a name="getting-a-sql-script"></a><span data-ttu-id="9d5d7-204">Obtention d’un script SQL</span><span class="sxs-lookup"><span data-stu-id="9d5d7-204">Getting a SQL Script</span></span>

<span data-ttu-id="9d5d7-205">Si un autre développeur veut ces changements sur son ordinateur, il peut effectuer la synchronisation dès que nous ajoutons nos changements dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-205">If another developer wants these changes on their machine they can just sync once we check our changes into source control.</span></span> <span data-ttu-id="9d5d7-206">Une fois qu’il a récupéré nos nouvelles migrations, il n’a qu’à exécuter la commande Update-Database pour obtenir les changements appliqués localement.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-206">Once they have our new migrations they can just run the Update-Database command to have the changes applied locally.</span></span> <span data-ttu-id="9d5d7-207">Toutefois, pour pousser ces changements sur un serveur de test et, finalement, sur un serveur de production, nous préférons un script SQL que nous pouvons confier à notre administrateur de base de données.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-207">However if we want to push these changes out to a test server, and eventually production, we probably want a SQL script we can hand off to our DBA.</span></span>

-   <span data-ttu-id="9d5d7-208">Exécutez la commande **Update-Database**, mais, cette fois, spécifiez l’indicateur **–Script** pour écrire les changements dans un script au lieu de les appliquer.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-208">Run the **Update-Database** command but this time specify the **–Script** flag so that changes are written to a script rather than applied.</span></span> <span data-ttu-id="9d5d7-209">Nous spécifions aussi une migration source et une migration cible pour générer le script.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-209">We’ll also specify a source and target migration to generate the script for.</span></span> <span data-ttu-id="9d5d7-210">Nous voulons un script pour migrer une base de données vide (**$InitialDatabase**) vers la dernière version (migration **AddPostAbstract**).</span><span class="sxs-lookup"><span data-stu-id="9d5d7-210">We want a script to go from an empty database (**$InitialDatabase**) to the latest version (migration **AddPostAbstract**).</span></span>
    <span data-ttu-id="9d5d7-211">*Si vous ne spécifiez pas de migration cible, Migrations utilise la dernière migration comme cible. Si vous ne spécifiez pas de migration source, Migrations utilise l’état actuel de la base de données.*</span><span class="sxs-lookup"><span data-stu-id="9d5d7-211">*If you don’t specify a target migration, Migrations will use the latest migration as the target. If you don't specify a source migrations, Migrations will use the current state of the database.*</span></span>
-   <span data-ttu-id="9d5d7-212">Exécutez la commande **Update-Database –Script –SourceMigration: $InitialDatabase –TargetMigration: AddPostAbstract** dans la Console du Gestionnaire de Package</span><span class="sxs-lookup"><span data-stu-id="9d5d7-212">Run the **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** command in Package Manager Console</span></span>

<span data-ttu-id="9d5d7-213">Migrations Code First exécute le pipeline de migration, mais il écrit les changements dans un fichier .sql au lieu de les appliquer réellement.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-213">Code First Migrations will run the migration pipeline but instead of actually applying the changes it will write them out to a .sql file for you.</span></span> <span data-ttu-id="9d5d7-214">Une fois que le script est généré, il est ouvert dans Visual Studio pour que vous puissiez le consulter ou l’enregistrer.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-214">Once the script is generated, it is opened for you in Visual Studio, ready for you to view or save.</span></span>

### <a name="generating-idempotent-scripts"></a><span data-ttu-id="9d5d7-215">Génération de scripts idempotents</span><span class="sxs-lookup"><span data-stu-id="9d5d7-215">Generating Idempotent Scripts</span></span>

<span data-ttu-id="9d5d7-216">À partir d’EF6, si vous spécifiez **–SourceMigration $InitialDatabase**, le script généré est « idempotent ».</span><span class="sxs-lookup"><span data-stu-id="9d5d7-216">Starting with EF6, if you specify **–SourceMigration $InitialDatabase** then the generated script will be ‘idempotent’.</span></span> <span data-ttu-id="9d5d7-217">Les scripts idempotents peuvent mettre à niveau une base de données de n’importe quelle version vers la dernière version (ou la version spécifiée si vous utilisez **–TargetMigration**).</span><span class="sxs-lookup"><span data-stu-id="9d5d7-217">Idempotent scripts can upgrade a database currently at any version to the latest version (or the specified version if you use **–TargetMigration**).</span></span> <span data-ttu-id="9d5d7-218">Le script généré inclut une logique pour vérifier la table **\_\_MigrationsHistory** et applique uniquement les changements qui ne l’ont pas déjà été.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-218">The generated script includes logic to check the **\_\_MigrationsHistory** table and only apply changes that haven't been previously applied.</span></span>

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a><span data-ttu-id="9d5d7-219">Mise à niveau automatique au démarrage de l’application (initialiseur MigrateDatabaseToLatestVersion)</span><span class="sxs-lookup"><span data-stu-id="9d5d7-219">Automatically Upgrading on Application Startup (MigrateDatabaseToLatestVersion Initializer)</span></span>

<span data-ttu-id="9d5d7-220">Si vous déployez votre application, vous pouvez lui permettre de mettre automatiquement à niveau la base de données (en appliquant les migrations en attente) quand elle démarre.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-220">If you are deploying your application you may want it to automatically upgrade the database (by applying any pending migrations) when the application launches.</span></span> <span data-ttu-id="9d5d7-221">Pour ce faire, inscrivez l’initialiseur de base de données **MigrateDatabaseToLatestVersion**.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-221">You can do this by registering the **MigrateDatabaseToLatestVersion** database initializer.</span></span> <span data-ttu-id="9d5d7-222">Un initialiseur de base de données contient simplement une logique qui garantit que la base de données est configurée correctement.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-222">A database initializer simply contains some logic that is used to make sure the database is setup correctly.</span></span> <span data-ttu-id="9d5d7-223">Cette logique est exécutée la première fois que le contexte est utilisé dans le processus d’application (**AppDomain**).</span><span class="sxs-lookup"><span data-stu-id="9d5d7-223">This logic is run the first time the context is used within the application process (**AppDomain**).</span></span>

<span data-ttu-id="9d5d7-224">Nous pouvons mettre à jour le fichier **Program.cs**, comme indiqué ci-dessous, afin de définir l’initialiseur **MigrateDatabaseToLatestVersion** pour BlogContext avant d’utiliser le contexte (ligne 14).</span><span class="sxs-lookup"><span data-stu-id="9d5d7-224">We can update the **Program.cs** file, as shown below, to set the **MigrateDatabaseToLatestVersion** initializer for BlogContext before we use the context (Line 14).</span></span> <span data-ttu-id="9d5d7-225">Notez que vous devez également ajouter une instruction using pour l’espace de noms **System.Data.Entity** (ligne 5).</span><span class="sxs-lookup"><span data-stu-id="9d5d7-225">Note that you also need to add a using statement for the **System.Data.Entity** namespace (Line 5).</span></span>

<span data-ttu-id="9d5d7-226">*Quand nous créons une instance de cet initialiseur, nous devons spécifier le type de contexte (**BlogContext**) et la configuration des migrations (**Configuration**) : la configuration des migrations est la classe qui a été ajoutée à notre dossier **Migrations** quand nous avons activé les migrations.*</span><span class="sxs-lookup"><span data-stu-id="9d5d7-226">*When we create an instance of this initializer we need to specify the context type (**BlogContext**) and the migrations configuration (**Configuration**) - the migrations configuration is the class that got added to our **Migrations** folder when we enabled Migrations.*</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion<BlogContext, Configuration>());

                using (var db = new BlogContext())
                {
                    db.Blogs.Add(new Blog { Name = "Another Blog " });
                    db.SaveChanges();

                    foreach (var blog in db.Blogs)
                    {
                        Console.WriteLine(blog.Name);
                    }
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }
        }
    }
```

<span data-ttu-id="9d5d7-227">À partir de maintenant, chaque fois que notre application s’exécute, elle vérifie d’abord si la base de données ciblée est à jour, sinon, elle applique les migrations en attente.</span><span class="sxs-lookup"><span data-stu-id="9d5d7-227">Now whenever our application runs it will first check if the database it is targeting is up-to-date, and apply any pending migrations if it is not.</span></span>
