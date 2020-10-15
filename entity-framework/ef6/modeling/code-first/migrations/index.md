---
title: Migrations Code First - EF6
description: Migrations Code First dans Entity Framework 6.
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/index
ms.openlocfilehash: 7b58ed82202e5a0b7e8676020e32deaac39bcf70
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064976"
---
# <a name="code-first-migrations"></a>Migrations Code First
Migrations Code First est la méthode recommandée pour faire évoluer votre schéma de base de données d’application si vous utilisez le flux de travail Code First. Les migrations fournissent un ensemble d’outils pour les opérations suivantes :

1. Créer une base de données initiale qui fonctionne avec votre modèle EF
2. Générer des migrations pour suivre les changements que vous appliquez à votre modèle EF
2. Mettre à jour votre base de données avec ces changements

La procédure pas à pas suivante fournit une vue d’ensemble des Migrations Code First dans Entity Framework. Vous pouvez suivre l’intégralité de la procédure pas à pas ou passer à la rubrique qui vous intéresse. Les rubriques suivantes sont traitées :

## <a name="building-an-initial-model--database"></a>Génération d’un modèle et d’une base de données initiaux

Avant de commencer à utiliser les migrations, nous avons besoin d’un projet et d’un modèle Code First. Pour cette procédure pas à pas nous utilisons le modèle canonique **Blog** et **Post**.

-   Créez une application console **MigrationsDemo**
-   Ajoutez la dernière version du package NuGet **EntityFramework** au projet
    -   **Outils -&gt; Gestionnaire de package de bibliothèque -&gt; Console du Gestionnaire de package**
    -   Exécutez la commande **Install-Package EntityFramework**
-   Ajoutez un fichier **Model.cs** avec le code indiqué ci-dessous. Ce code définit une seule classe **Blog** qui constitue notre modèle de domaine et une classe **BlogContext** qui est notre contexte EF Code First

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

-   Maintenant que nous avons un modèle, utilisons-le pour accéder aux données. Mettez à jour le fichier **Program.cs** avec le code indiqué ci-dessous.

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

-   Exécutez votre application : une base de données **MigrationsCodeDemo.BlogContext** est créée pour vous.

    ![Base de données locale](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>Activation des migrations

Modifions un peu plus notre modèle.

-   Nous introduisons une propriété Url à la classe Blog.

``` csharp
    public string Url { get; set; }
```

Si vous réexécutez l’application, vous obtenez une exception InvalidOperationException indiquant *Le modèle permettant la sauvegarde du contexte 'BlogContext' a changé depuis la création de la base de données. Utilisez Migrations Code First pour mettre à jour la base de données (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*

Comme le suggère l’exception, utilisons donc Migrations Code First. La première étape est d’activer les migrations pour notre contexte.

-   Exécutez la commande **Enable-Migrations** dans la Console du Gestionnaire de Package

    Cette commande a ajouté un dossier **Migrations** à notre projet. Ce nouveau dossier contient deux fichiers :

-   **La classe Configuration.** Cette classe vous permet de configurer le comportement des migrations pour votre contexte. Pour cette procédure pas à pas, nous utilisons simplement la configuration par défaut.
    *Comme il n’y a qu’un seul contexte Code First dans votre projet, Enable-Migrations a automatiquement renseigné le type de contexte auquel s’applique cette configuration.*
-   **Une migration InitialCreate**. Cette migration a été générée, car Code First a déjà créé une base de données pour nous, avant l’activation des migrations. Le code dans cette migration générée automatiquement représente les objets qui ont déjà été créés dans la base de données. Dans notre cas, c’est la table **Blog** avec des colonnes **BlogId** et **Name**. Le nom de fichier contient un horodatage pour faciliter le classement.
    *Si la base de données n’a pas encore été créée, cette migration InitialCreate ne peut pas avoir été ajoutée au projet. À la place, la première fois que nous appelons Add-Migration, le code permettant de créer ces tables est généré automatiquement dans une nouvelle migration.*

### <a name="multiple-models-targeting-the-same-database"></a>Plusieurs modèles ciblant la même base de données

Quand vous utilisez des versions antérieures à EF6, un seul modèle Code First peut servir à générer/gérer le schéma d’une base de données. Voici le résultat d’une seule table **\_\_MigrationsHistory** par base de données sans aucun moyen d’identifier les entrées qui appartiennent à tel ou tel modèle.

À partir d’EF6, la classe **Configuration** inclut une propriété **ContextKey**. Elle agit comme un identificateur unique pour chaque modèle Code First. Une colonne correspondante dans la table **\_\_MigrationsHistory** autorise des entrées provenant de plusieurs modèles pour partager la table. Par défaut, cette propriété est définie sur le nom complet de votre contexte.

## <a name="generating--running-migrations"></a>Génération et exécution des migrations

Migrations Code First a deux commandes principales que nous allons découvrir maintenant.

-   **Add-Migration** génère automatiquement la prochaine migration en fonction des changements de votre modèle depuis la création de la dernière migration
-   **Update-Database** applique toutes les migrations en attente à la base de données

Nous avons besoin de générer automatiquement une migration pour prendre en charge de la nouvelle propriété Url que nous avons ajoutée. La commande **Add-Migration** permet de nommer ces migrations. Appelons la nôtre **AddBlogUrl**.

-   Exécutez la commande **Add-Migration AddBlogUrl** dans la Console du Gestionnaire de Package
-   Dans le dossier **Migrations**, nous avons maintenant une nouvelle migration **AddBlogUrl**. Le nom de fichier de la migration est préfixé avec un horodatage pour faciliter le classement

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

Nous pouvons maintenant apporter des modifications ou effectuer des ajouts à cette migration, mais tout semble convenable. Utilisons **Update-Database** pour appliquer cette migration à la base de données.

-   Exécutez la commande **Update-Database** dans la Console du Gestionnaire de Package
-   Migrations Code First compare les migrations de notre dossier **Migrations** avec celles qui ont été appliquées à la base de données. Il voit que la migration **AddBlogUrl** doit être appliquée et l’exécute.

La base de données **MigrationsDemo.BlogContext** est désormais mise à jour pour inclure la colonne **Url** dans la table **Blogs**.

## <a name="customizing-migrations"></a>Personnalisation des migrations

Jusqu’à présent, nous avons généré et exécuté une migration sans la modifier. Nous allons maintenant modifier le code généré par défaut.

-   Modifions notre modèle en ajoutant une nouvelle propriété **Rating** à la classe **Blog**

``` csharp
    public int Rating { get; set; }
```

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

Nous utilisons la commande **Add-Migration** pour permettre à Migrations Code First de générer automatiquement la migration qui convient le mieux selon lui. Appelons cette migration **AddPostClass**.

-   Exécutez la commande **Add-Migration AddPostClass** dans la Console du Gestionnaire de Package.

Migrations Code First a obtenu de bons résultats en générant automatiquement ces changements, mais nous voulons modifier certaines choses :

1.  Tout d’abord, nous ajoutons un index unique à la colonne **Posts.Title** (ajout aux lignes 22 et 29 dans le code ci-dessous).
2.  Nous ajoutons aussi une colonne **Blogs.Rating** qui n’autorise pas les valeurs Null. Si des données existent déjà dans la table, elles reçoivent le type de données CLR par défaut pour la nouvelle colonne (Rating est un entier, donc la valeur est **0**). Toutefois, nous voulons spécifier une valeur par défaut égale à **3** pour que les lignes existantes de la table **Blogs** commencent avec un classement correct.
    (Vous pouvez voir la valeur par défaut spécifiée sur la ligne 24 du code ci-dessous)

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

Notre migration modifiée est prête, utilisons **Update-Database** pour mettre à jour la base de données. Cette fois, nous spécifions l’indicateur **–Verbose** pour pouvoir voir le code SQL que Migrations Code First exécute.

-   Exécutez la commande **Update-Database –Verbose** dans la Console du Gestionnaire de Package.

## <a name="data-motion--custom-sql"></a>Déplacement de données / code SQL personnalisé

Jusqu’à présent, nous avons vu des opérations de migration qui ne changent pas ni ne déplacent des données. Voyons maintenant un cas où nous avons besoin de déplacer des données. Le déplacement de données n’est pas encore pris en charge de manière native, mais nous pouvons exécuter des commandes SQL arbitraires à tout moment dans notre script.

-   Ajoutons une propriété **Post.Abstract** à notre modèle. Plus tard, nous allons préremplir la colonne **Abstract** des publications existantes à l’aide de texte du début de la colonne **Content**.

``` csharp
    public string Abstract { get; set; }
```

Nous utilisons la commande **Add-Migration** pour permettre à Migrations Code First de générer automatiquement la migration qui convient le mieux selon lui.

-   Exécutez la commande **Add-Migration AddPostAbstract** dans la Console du Gestionnaire de Package.
-   La migration générée prend en charge les changements de schéma, mais nous voulons aussi préremplir la colonne **Abstract** avec les 100 premiers caractères du contenu de chaque publication. Pour ce faire, nous repassons à SQL et exécutons une instruction **UPDATE** après l’ajout de la colonne.
    (Ajout à la ligne 12 dans le code ci-dessous)

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

Notre migration modifiée est prête, utilisons **Update-Database** pour mettre à jour la base de données. Spécifions l’indicateur **–Verbose** pour pouvoir voir le code SQL exécuté sur la base de données.

-   Exécutez la commande **Update-Database –Verbose** dans la Console du Gestionnaire de Package.

## <a name="migrate-to-a-specific-version-including-downgrade"></a>Migrer vers une version spécifique (y compris vers une version antérieure)

Jusqu’à présent, nous avons toujours effectué la mise à niveau vers la dernière migration, mais vous pouvez effectuer une mise à niveau vers une version spécifique, voire une version antérieure.

Supposons que nous voulons migrer notre base de données dans l’état où elle se trouvait après l’exécution de notre migration **AddBlogUrl**. Nous pouvons utiliser le commutateur **–TargetMigration** pour passer à cette migration antérieure.

-   Exécutez la commande **Update-Database –TargetMigration: AddBlogUrl** dans la Console du Gestionnaire de Package.

Cette commande exécute le script de passage à une version antérieure pour nos migrations **AddBlogAbstract** et **AddPostClass**.

Si vous voulez restaurer la base de données vide, vous pouvez utiliser la commande **Update-Database –TargetMigration: $InitialDatabase**.

## <a name="getting-a-sql-script"></a>Obtention d’un script SQL

Si un autre développeur veut ces changements sur son ordinateur, il peut effectuer la synchronisation dès que nous ajoutons nos changements dans le contrôle de code source. Une fois qu’il a récupéré nos nouvelles migrations, il n’a qu’à exécuter la commande Update-Database pour obtenir les changements appliqués localement. Toutefois, pour pousser ces changements sur un serveur de test et, finalement, sur un serveur de production, nous préférons un script SQL que nous pouvons confier à notre administrateur de base de données.

-   Exécutez la commande **Update-Database**, mais, cette fois, spécifiez l’indicateur **–Script** pour écrire les changements dans un script au lieu de les appliquer. Nous spécifions aussi une migration source et une migration cible pour générer le script. Nous voulons un script pour migrer une base de données vide (**$InitialDatabase**) vers la dernière version (migration **AddPostAbstract**).
    *Si vous ne spécifiez pas de migration cible, Migrations utilise la dernière migration comme cible. Si vous ne spécifiez pas de migration source, Migrations utilise l’état actuel de la base de données.*
-   Exécutez la commande **Update-Database –Script –SourceMigration: $InitialDatabase –TargetMigration: AddPostAbstract** dans la Console du Gestionnaire de Package

Migrations Code First exécute le pipeline de migration, mais il écrit les changements dans un fichier .sql au lieu de les appliquer réellement. Une fois que le script est généré, il est ouvert dans Visual Studio pour que vous puissiez le consulter ou l’enregistrer.

### <a name="generating-idempotent-scripts"></a>Génération de scripts idempotents

À partir d’EF6, si vous spécifiez **–SourceMigration $InitialDatabase**, le script généré est « idempotent ». Les scripts idempotents peuvent mettre à niveau une base de données de n’importe quelle version vers la dernière version (ou la version spécifiée si vous utilisez **–TargetMigration**). Le script généré inclut une logique pour vérifier la table **\_\_MigrationsHistory** et applique uniquement les changements qui ne l’ont pas déjà été.

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a>Mise à niveau automatique au démarrage de l’application (initialiseur MigrateDatabaseToLatestVersion)

Si vous déployez votre application, vous pouvez lui permettre de mettre automatiquement à niveau la base de données (en appliquant les migrations en attente) quand elle démarre. Pour ce faire, inscrivez l’initialiseur de base de données **MigrateDatabaseToLatestVersion**. Un initialiseur de base de données contient simplement une logique qui garantit que la base de données est configurée correctement. Cette logique est exécutée la première fois que le contexte est utilisé dans le processus d’application (**AppDomain**).

Nous pouvons mettre à jour le fichier **Program.cs**, comme indiqué ci-dessous, afin de définir l’initialiseur **MigrateDatabaseToLatestVersion** pour BlogContext avant d’utiliser le contexte (ligne 14). Notez que vous devez également ajouter une instruction using pour l’espace de noms **System.Data.Entity** (ligne 5).

*Quand nous créons une instance de cet initialiseur, nous devons spécifier le type de contexte (**BlogContext**) et la configuration des migrations (**Configuration**) : la configuration des migrations est la classe qui a été ajoutée à notre dossier **Migrations** quand nous avons activé les migrations.*

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

À partir de maintenant, chaque fois que notre application s’exécute, elle vérifie d’abord si la base de données ciblée est à jour, sinon, elle applique les migrations en attente.
