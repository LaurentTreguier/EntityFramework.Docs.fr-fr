---
title: Migrations Code First automatique-EF6
description: Migrations Code First automatique dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/automatic
ms.openlocfilehash: 8152cdf642258a30d98f3750bf1ca4ccd2859978
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066406"
---
# <a name="automatic-code-first-migrations"></a>Migrations Code First automatique
La migration automatique vous permet d’utiliser Migrations Code First sans avoir de fichier de code dans votre projet pour chaque modification que vous apportez. Toutes les modifications ne peuvent pas être appliquées automatiquement. par exemple, les renommages de colonne nécessitent l’utilisation d’une migration basée sur le code.

> [!NOTE]
> Cet article suppose que vous savez utiliser Migrations Code First dans les scénarios de base. Si ce n’est pas le cas, vous devez lire [migrations code First](xref:ef6/modeling/code-first/migrations/index) avant de continuer.

## <a name="recommendation-for-team-environments"></a>Recommandation pour les environnements d’équipe

Vous pouvez intercaler des migrations automatiques et basées sur du code, mais cela n’est pas recommandé dans les scénarios de développement d’équipe. Si vous faites partie d’une équipe de développeurs qui utilisent le contrôle de code source, vous devez utiliser des migrations purement automatiques ou des migrations purement basées sur le code. Étant donné les limitations des migrations automatiques, nous vous recommandons d’utiliser des migrations basées sur du code dans des environnements d’équipe.

## <a name="building-an-initial-model--database"></a>Génération d’un modèle et d’une base de données initiaux

Avant de commencer à utiliser les migrations, nous avons besoin d’un projet et d’un modèle Code First. Pour cette procédure pas à pas nous utilisons le modèle canonique **Blog** et **Post**.

-   Créer une application console **MigrationsAutomaticDemo**
-   Ajoutez la dernière version du package NuGet **EntityFramework** au projet
    -   **Outils -&gt; Gestionnaire de package de bibliothèque -&gt; Console du Gestionnaire de package**
    -   Exécutez la commande **Install-Package EntityFramework**
-   Ajoutez un fichier **Model.cs** avec le code indiqué ci-dessous. Ce code définit une seule classe **Blog** qui constitue notre modèle de domaine et une classe **BlogContext** qui est notre contexte EF Code First

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
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

-   Maintenant que nous avons un modèle, utilisons-le pour accéder aux données. Mettez à jour le fichier **Program.cs** avec le code indiqué ci-dessous.

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsAutomaticDemo
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

-   Exécutez votre application et vous verrez qu’une base de données **MigrationsAutomaticCodeDemo. BlogContext** est créée pour vous.

    ![Base de données locale](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>Activation des migrations

Modifions un peu plus notre modèle.

-   Nous introduisons une propriété Url à la classe Blog.

``` csharp
    public string Url { get; set; }
```

Si vous réexécutez l’application, vous obtenez une exception InvalidOperationException indiquant *Le modèle permettant la sauvegarde du contexte 'BlogContext' a changé depuis la création de la base de données. Utilisez Migrations Code First pour mettre à jour la base de données (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*

Comme le suggère l’exception, utilisons donc Migrations Code First. Étant donné que nous voulons utiliser des migrations automatiques, nous allons spécifier le commutateur **– EnableAutomaticMigrations** .

-   Exécutez la commande **Enable-migrations-EnableAutomaticMigrations** dans la console du gestionnaire de package. cette commande a ajouté un dossier **migrations** à notre projet. Ce nouveau dossier contient un fichier :

-   **La classe Configuration.** Cette classe vous permet de configurer le comportement des migrations pour votre contexte. Pour cette procédure pas à pas, nous utilisons simplement la configuration par défaut.
    *Comme il n’y a qu’un seul contexte Code First dans votre projet, Enable-Migrations a automatiquement renseigné le type de contexte auquel s’applique cette configuration.*

 

## <a name="your-first-automatic-migration"></a>Votre première migration automatique

Migrations Code First a deux commandes principales que nous allons découvrir maintenant.

-   **Add-Migration** génère automatiquement la prochaine migration en fonction des changements de votre modèle depuis la création de la dernière migration
-   **Update-Database** applique toutes les migrations en attente à la base de données

Nous allons éviter d’utiliser des Add-Migration (sauf si cela est vraiment nécessaire) et se concentrer sur la possibilité Migrations Code First de calculer et d’appliquer automatiquement les modifications. Nous allons utiliser **Update-Database** pour récupérer migrations code First pour transmettre les modifications apportées à notre modèle (la nouvelle propriété **blog. ur**l) à la base de données.

-   Exécutez la commande **Update-Database** dans la console du gestionnaire de package.

La base de données **MigrationsAutomaticDemo. BlogContext** est maintenant mise à jour pour inclure la colonne **URL** dans la table **blogs** .

 

## <a name="your-second-automatic-migration"></a>Votre deuxième migration automatique

Nous allons effectuer une autre modification et laisser Migrations Code First envoyer automatiquement les modifications à la base de données.

-   Ajoutons aussi une nouvelle classe **Post**

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

-   Nous allons aussi ajouter une collection **Posts** à la classe **Blog** pour former l’autre extrémité de la relation entre **Blog** et **Post**

``` csharp
    public virtual List<Post> Posts { get; set; }
```

Utilisez **à présent Update-Database** pour mettre à jour la base de données. Cette fois, nous spécifions l’indicateur **–Verbose** pour pouvoir voir le code SQL que Migrations Code First exécute.

-   Exécutez la commande **Update-Database –Verbose** dans la Console du Gestionnaire de Package.

## <a name="adding-a-code-based-migration"></a>Ajout d’une migration basée sur du code

Examinons maintenant ce que nous pourrions utiliser pour la migration basée sur le code.

-   Nous allons ajouter une propriété **Rating** à la classe **blog**

``` csharp
    public int Rating { get; set; }
```

Nous pourrions simplement exécuter **Update-Database** pour envoyer ces modifications à la base de données. Toutefois, nous ajoutons une colonne blogs qui n’accepte pas les valeurs null **.** si des données existent dans la table, elles reçoivent la valeur CLR par défaut du type de données de la nouvelle colonne (l’évaluation est un entier, donc **0**). Toutefois, nous voulons spécifier une valeur par défaut égale à **3** pour que les lignes existantes de la table **Blogs** commencent avec un classement correct.
Nous allons utiliser la commande Add-Migration pour écrire cette modification dans une migration basée sur du code afin de pouvoir la modifier. La commande **Add-migration** nous permet d’attribuer un nom à ces migrations, nous appelons simplement le nôtre **AddBlogRating**.

-   Exécutez la commande **Add-migration AddBlogRating** dans la console du gestionnaire de package.
-   Dans le dossier **migrations** , nous disposons désormais d’une nouvelle migration **AddBlogRating** . Le nom du fichier de migration est précédé d’un horodateur pour faciliter le classement. Modifions le code généré pour spécifier une valeur par défaut de 3 pour blog. Rating (ligne 10 dans le code ci-dessous)

*La migration a également un fichier code-behind qui capture certaines métadonnées. Ces métadonnées permettront à Migrations Code First de répliquer les migrations automatiques que nous avons effectuées avant cette migration basée sur le code. Cela est important si un autre développeur souhaite exécuter nos migrations ou quand il est temps de déployer notre application.*

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

Notre migration modifiée est prête, utilisons **Update-Database** pour mettre à jour la base de données.

-   Exécutez la commande **Update-Database** dans la console du gestionnaire de package.

## <a name="back-to-automatic-migrations"></a>Retour aux migrations automatiques

Nous sommes maintenant libres de revenir aux migrations automatiques pour nos modifications plus simples. Migrations Code First s’occupera des migrations automatiques et basées sur du code dans l’ordre correct en fonction des métadonnées qu’il stocke dans le fichier code-behind pour chaque migration basée sur le code.

-   Nous allons ajouter une propriété poster. Abstract à notre modèle

``` csharp
    public string Abstract { get; set; }
```

À présent, nous pouvons utiliser **Update-Database** pour savoir migrations code First transmettre cette modification à la base de données à l’aide d’une migration automatique.

-   Exécutez la commande **Update-Database** dans la console du gestionnaire de package.

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, vous avez vu comment utiliser des migrations automatiques pour transmettre des modifications de modèle à la base de données. Vous avez également vu comment structurer et exécuter des migrations basées sur du code entre des migrations automatiques lorsque vous avez besoin de davantage de contrôle.
