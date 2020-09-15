---
title: Code First à une nouvelle base de données-EF6
description: Code First à une nouvelle base de données dans Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 96d76162383149c045178954196e1294331918a8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070365"
---
# <a name="code-first-to-a-new-database"></a>Code First to a New Database (Création d’une nouvelle base de données avec Code First)
Cette vidéo et la procédure pas à pas fournissent une introduction au développement Code First ciblant une nouvelle base de données. Ce scénario comprend le ciblage d’une base de données qui n’existe pas et Code First créera, ou une base de données vide à laquelle Code First ajoutera de nouvelles tables. Code First vous permet de définir votre modèle à l’aide de \# classes C ou VB.net. Une configuration supplémentaire peut éventuellement être effectuée à l’aide d’attributs sur vos classes et propriétés ou à l’aide d’une API Fluent.

## <a name="watch-the-video"></a>Regarder la vidéo
Cette vidéo fournit une introduction au développement Code First ciblant une nouvelle base de données. Ce scénario comprend le ciblage d’une base de données qui n’existe pas et Code First créera, ou une base de données vide à laquelle Code First ajoutera de nouvelles tables. Code First vous permet de définir votre modèle à l’aide de classes C# ou VB.Net. Une configuration supplémentaire peut éventuellement être effectuée à l’aide d’attributs sur vos classes et propriétés ou à l’aide d’une API Fluent.

**Présentée par** : [Rowan Miller](https://romiller.com/)

**Vidéo**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)  |  [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez avoir au moins Visual Studio 2010 ou Visual Studio 2012 installé.

Si vous utilisez Visual Studio 2010, [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) doit également être installé.

## <a name="1-create-the-application"></a>1. créer l’application

Pour simplifier les choses, nous allons créer une application console de base qui utilise Code First pour effectuer l’accès aux données.

-   Ouvrir Visual Studio
-   **Fichier- &gt; nouveau- &gt; projet...**
-   Sélectionnez **Windows** dans le menu de gauche et dans l' **application console** .
-   Entrez **CodeFirstNewDatabaseSample** comme nom
-   Sélectionnez **OK**.

## <a name="2-create-the-model"></a>2. créer le modèle

Nous allons définir un modèle très simple à l’aide de classes. Nous allons simplement les définir dans le fichier Program.cs, mais dans une application réelle, vous fractionnez vos classes en fichiers distincts et éventuellement dans un projet distinct.

Sous la définition de classe de programme dans Program.cs, ajoutez les deux classes suivantes.

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

Vous remarquerez que les deux propriétés de navigation (blog. publications et post. blog) sont virtuelles. Cela active la fonctionnalité de chargement différé de Entity Framework. Le chargement différé signifie que le contenu de ces propriétés est chargé automatiquement à partir de la base de données lorsque vous tentez d’y accéder.

## <a name="3-create-a-context"></a>3. créer un contexte

À présent, il est temps de définir un contexte dérivé, qui représente une session avec la base de données, ce qui nous permet d’interroger et d’enregistrer des données. Nous définissons un contexte qui dérive de System. Data. Entity. DbContext et expose une tente de DbSet typée &lt; &gt; pour chaque classe dans notre modèle.

Nous commençons à utiliser les types de la Entity Framework, donc nous devons ajouter le package NuGet EntityFramework.

-   **Projet : &gt; gérer les packages NuGet...**
    Remarque : Si vous n’avez pas la **gestion des packages NuGet...** option vous devez installer la [dernière version de NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)
-   Sélectionner l’onglet **en ligne**
-   Sélectionner le package **EntityFramework**
-   Cliquez sur **Install**.

Ajoutez une instruction using pour System. Data. Entity en haut de Program.cs.

``` csharp
using System.Data.Entity;
```

Sous la classe de publication dans Program.cs, ajoutez le contexte dérivé suivant.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

Voici une liste complète de ce que Program.cs doit maintenant contenir.

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

C’est tout le code dont nous avons besoin pour commencer à stocker et à récupérer les données. Évidemment, il y a beaucoup de choses en coulisses et nous examinerons cela dans un moment, mais commençons par le voir en action.

## <a name="4-reading--writing-data"></a>4. lecture & écriture de données

Implémentez la méthode main dans Program.cs comme indiqué ci-dessous. Ce code crée une nouvelle instance de notre contexte, puis l’utilise pour insérer un nouveau blog. Il utilise ensuite une requête LINQ pour récupérer tous les blogs de la base de données classée par ordre alphabétique par titre.

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

Vous pouvez maintenant exécuter l’application et la tester.

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a>Où sont mes données ?

Par la Convention DbContext a créé une base de données pour vous.

-   Si une instance locale de SQL Express est disponible (installée par défaut avec Visual Studio 2010), Code First a créé la base de données sur cette instance.
-   Si SQL Express n’est pas disponible, Code First essaiera et utilisera la [base de données locale (installée](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) par défaut avec Visual Studio 2012)
-   La base de données est nommée après le nom qualifié complet du contexte dérivé, dans notre cas **CodeFirstNewDatabaseSample. BloggingContext**

Il s’agit uniquement des conventions par défaut et il existe différentes façons de modifier la base de données que Code First utilise, plus d’informations sont disponibles dans la rubrique **How DbContext Découvre le modèle et la connexion à la base de données** .
Vous pouvez vous connecter à cette base de données à l’aide de Explorateur de serveurs dans Visual Studio

-   **Vue- &gt; Explorateur de serveurs**
-   Cliquez avec le bouton droit sur **connexions de données** , puis sélectionnez **Ajouter une connexion...**
-   Si vous n’êtes pas connecté à une base de données à partir de Explorateur de serveurs avant de devoir sélectionner Microsoft SQL Server comme source de données

    ![Sélectionner une source de données](~/ef6/media/selectdatasource.png)

-   Connectez-vous à la base de données locale ou SQL Express, en fonction de celle que vous avez installée

Nous pouvons maintenant inspecter le schéma créé par Code First.

![Schéma initial](~/ef6/media/schemainitial.png)

DbContext a utilisé les classes à inclure dans le modèle en examinant les propriétés DbSet que nous avons définies. Il utilise ensuite l’ensemble de conventions d’Code First par défaut pour déterminer les noms de tables et de colonnes, déterminer les types de données, rechercher les clés primaires, etc. Plus loin dans cette procédure pas à pas, nous allons voir comment vous pouvez remplacer ces conventions.

## <a name="5-dealing-with-model-changes"></a>5. traitement des modifications de modèle

À présent, il est temps d’apporter des modifications à notre modèle, lorsque nous effectuons ces modifications, nous devons également mettre à jour le schéma de base de données. Pour ce faire, nous allons utiliser une fonctionnalité appelée Migrations Code First, ou migrations pour Short.

Les migrations nous permettent d’avoir un ensemble ordonné d’étapes qui décrivent comment mettre à niveau (et rétrograder) notre schéma de base de données. Chacune de ces étapes, appelée migration, contient du code qui décrit les modifications à appliquer. 

La première étape consiste à activer Migrations Code First pour notre BloggingContext.

-   **Outils- &gt; Gestionnaire de package de bibliothèque- &gt; console du gestionnaire de package**
-   Exécutez la commande **Enable-Migrations** dans la Console du Gestionnaire de Package
-   Un nouveau dossier migrations a été ajouté à notre projet qui contient deux éléments :
    -   **Configuration.cs** : ce fichier contient les paramètres que les migrations vont utiliser pour la migration de BloggingContext. Nous n’avons pas besoin de modifier quoi que ce soit pour cette procédure pas à pas, mais ici, vous pouvez spécifier des données de départ, inscrire des fournisseurs pour d’autres bases de données, modifier l’espace de noms dans lequel les migrations sont générées, etc.
    -   ** &lt; horodatage &gt; \_ InitialCreate.cs** – il s’agit de votre première migration, elle représente les modifications qui ont déjà été appliquées à la base de données pour la faire passer d’une base de données vide à une base de données qui comprend les blogs et les tables des publications. Bien que nous puissions laisser Code First créer automatiquement ces tables pour nous, maintenant que nous avons choisi des migrations, elles ont été converties en migration. Code First a également enregistré dans notre base de données locale que cette migration a déjà été appliquée. L’horodateur sur le nom de fichier est utilisé à des fins de classement.

    Nous allons maintenant apporter une modification à notre modèle, ajouter une propriété URL à la classe de blog :

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   Exécutez la commande **Add-migration AddUrl** dans la console du gestionnaire de package.
    La commande Add-migration vérifie les modifications apportées depuis votre dernière migration et génère une nouvelle migration avec les modifications détectées. Nous pouvons attribuer un nom à la migration. dans ce cas, nous appelons la migration « AddUrl ».
    Le code de génération de modèles automatique indique que nous devons ajouter une colonne d’URL, qui peut contenir des données de chaîne, au dbo. Table blogs. Si nécessaire, nous pourrions modifier le code de génération de modèles automatique, mais cela n’est pas nécessaire dans ce cas.

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
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

-   Exécutez la commande **Update-Database** dans la console du gestionnaire de package. Cette commande applique toutes les migrations en attente à la base de données. Notre migration InitialCreate a déjà été appliquée afin que les migrations appliquent simplement notre nouvelle migration AddUrl.
    Conseil : vous pouvez utiliser le commutateur **– Verbose** lors de l’appel de Update-Database pour voir le SQL en cours d’exécution sur la base de données.

La nouvelle colonne URL est maintenant ajoutée à la table blogs dans la base de données :

![Schéma avec URL](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a>6. Annotations de données

Jusqu’à présent, nous permettons à EF de découvrir le modèle à l’aide de ses conventions par défaut, mais il y a des moments où nos classes ne suivent pas les conventions et nous devons être en mesure d’effectuer une configuration supplémentaire. Il existe deux options pour cela ; Nous allons examiner les annotations de données dans cette section, puis l’API Fluent dans la section suivante.

-   Nous allons ajouter une classe d’utilisateur à notre modèle

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   Nous devons également ajouter un ensemble à notre contexte dérivé

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   Si nous avons essayé d’ajouter une migration, nous obtenons une erreur indiquant «*EntityType’User’n’a aucune clé définie. Définissez la clé pour cet EntityType.»* comme EF n’a aucun moyen de savoir que le nom d’utilisateur doit être la clé primaire de l’utilisateur.
-   Nous allons utiliser des annotations de données maintenant afin d’ajouter une instruction using en haut de Program.cs

```csharp
using System.ComponentModel.DataAnnotations;
```

-   Annotez maintenant la propriété UserName pour identifier qu’il s’agit de la clé primaire

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   Utilisez la commande **Add-migration adduser** pour générer automatiquement la structure d’une migration et appliquer ces modifications à la base de données.
-   Exécutez la commande **Update-Database** pour appliquer la nouvelle migration à la base de données.

La nouvelle table est maintenant ajoutée à la base de données :

![Schéma avec des utilisateurs](~/ef6/media/schemawithusers.png)

La liste complète des annotations prises en charge par EF est la suivante :

-   [KeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [StringLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [MaxLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [ConcurrencyCheckAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [RequiredAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [TimestampAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [ComplexTypeAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [ColumnAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [TableAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [InversePropertyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [ForeignKeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [DatabaseGeneratedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [NotMappedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a>7. API Fluent

Dans la section précédente, nous avons vu comment utiliser des annotations de données pour compléter ou remplacer ce qui a été détecté par Convention. L’autre façon de configurer le modèle consiste à utiliser l’API Fluent Code First.

La plupart des configurations de modèle peuvent être effectuées à l’aide d’annotations de données simples. L’API Fluent est un moyen plus avancé de spécifier une configuration de modèle qui couvre tout ce que les annotations de données peuvent faire en plus d’une configuration plus avancée qui n’est pas possible avec les annotations de données. Les annotations de données et l’API Fluent peuvent être utilisées ensemble.

Pour accéder à l’API Fluent, vous devez substituer la méthode OnModelCreating dans DbContext. Supposons que nous voulions renommer la colonne dans laquelle User. DisplayName est stocké pour afficher le \_ nom.

-   Remplacez la méthode OnModelCreating sur BloggingContext par le code suivant :

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   Utilisez la commande **Add-migration ChangeDisplayName** pour générer automatiquement une migration pour appliquer ces modifications à la base de données.
-   Exécutez la commande **Update-Database** pour appliquer la nouvelle migration à la base de données.

La colonne DisplayName est maintenant renommée en nom d’affichage \_ :

![Schéma dont le nom d’affichage a été renommé](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, nous avons examiné Code First développement à l’aide d’une nouvelle base de données. Nous avons défini un modèle à l’aide de classes, puis j’ai utilisé ce modèle pour créer une base de données et stocker et récupérer des données. Une fois la base de données créée, nous avons utilisé Migrations Code First pour modifier le schéma à mesure de l’évolution du modèle. Nous avons également vu comment configurer un modèle à l’aide d’annotations de données et de l’API Fluent.
