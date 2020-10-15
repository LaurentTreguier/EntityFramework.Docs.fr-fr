---
title: Code First à une base de données existante-EF6
description: Code First à une base de données existante dans Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/existing-database
ms.openlocfilehash: 2305236f68cfdc9f6e1582cca1163abf380b4075
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066419"
---
# <a name="code-first-to-an-existing-database"></a>Code First à une base de données existante
Cette vidéo et la procédure pas à pas fournissent une introduction au développement Code First ciblant une base de données existante. Code First vous permet de définir votre modèle à l’aide de \# classes C ou VB.net. Éventuellement, une configuration supplémentaire peut être effectuée à l’aide d’attributs sur vos classes et propriétés ou à l’aide d’une API Fluent.

## <a name="watch-the-video"></a>Regarder la vidéo
Cette vidéo est [désormais disponible sur Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).

## <a name="pre-requisites"></a>Prérequis

Pour effectuer cette procédure pas à pas, vous devez avoir installé **Visual Studio 2012** ou **Visual Studio 2013** .

Vous aurez également besoin de la version **6,1** (ou ultérieure) du **Entity Framework Tools pour Visual Studio** . Consultez [obtenir des Entity Framework](xref:ef6/fundamentals/install) pour plus d’informations sur l’installation de la dernière version du Entity Framework Tools.

## <a name="1-create-an-existing-database"></a>1. créer une base de données existante

En général, lorsque vous ciblez une base de données existante, elle est déjà créée, mais pour cette procédure pas à pas, nous devons créer une base de données à laquelle accéder.

Commençons par générer la base de données.

-   Ouvrir Visual Studio
-   **Vue- &gt; Explorateur de serveurs**
-   Cliquez avec le bouton droit sur **connexions de données- &gt; Ajouter une connexion...**
-   Si vous n’êtes pas connecté à une base de données à partir de **Explorateur de serveurs** avant de devoir sélectionner **Microsoft SQL Server** comme source de données

    ![Sélectionner une source de données](~/ef6/media/selectdatasource.png)

-   Connectez-vous à votre instance de base de données locale et entrez les **blogs** comme nom de base de données

    ![Connexion de base de données locale](~/ef6/media/localdbconnection.png)

-   Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .

    ![Boîte de dialogue créer une base de données](~/ef6/media/createdatabasedialog.png)

-   La nouvelle base de données s’affiche alors dans Explorateur de serveurs, cliquez dessus avec le bouton droit et sélectionnez **nouvelle requête** .
-   Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a>2. créer l’application

Pour simplifier les choses, nous allons créer une application console de base qui utilise Code First pour effectuer l’accès aux données :

-   Ouvrir Visual Studio
-   **Fichier- &gt; nouveau- &gt; projet...**
-   Sélectionnez **Windows** dans le menu de gauche et dans l' **application console** .
-   Entrez **CodeFirstExistingDatabaseSample** comme nom
-   Sélectionnez **OK**.

 

## <a name="3-reverse-engineer-model"></a>3. modèle d’ingénierie à rebours

Nous utiliserons le Entity Framework Tools pour Visual Studio pour nous aider à générer du code initial à mapper à la base de données. Ces outils génèrent simplement du code que vous pouvez également taper manuellement si vous préférez.

-   **Projet- &gt; Ajouter un nouvel élément...**
-   Sélectionnez **données** dans le menu de gauche, puis **ADO.NET Entity Data Model**
-   Entrez **BloggingContext** comme nom et cliquez sur **OK** .
-   Cela lance l' **assistant Entity Data Model**
-   Sélectionnez **Code First dans la base de données** , puis cliquez sur **suivant** .

    ![Assistant CFE](~/ef6/media/wizardonecfe.png)

-   Sélectionnez la connexion à la base de données que vous avez créée dans la première section, puis cliquez sur **suivant** .

    ![Assistant 2 CFE](~/ef6/media/wizardtwocfe.png)

-   Cochez la case en regard de **tables** pour importer toutes les tables, puis cliquez sur **Terminer** .

    ![Assistant trois CFE](~/ef6/media/wizardthreecfe.png)

Une fois que le processus d’ingénierie à rebours est terminé, un certain nombre d’éléments ont été ajoutés au projet, examinons ce qui a été ajouté.

### <a name="configuration-file"></a>Fichier de configuration

Un fichier de App.config a été ajouté au projet, ce fichier contient la chaîne de connexion à la base de données existante.

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

*Vous remarquerez également d’autres paramètres dans le fichier de configuration. il s’agit de paramètres EF par défaut qui indiquent Code First où créer les bases de données. Étant donné que nous allons mapper à une base de données existante, ces paramètres seront ignorés dans notre application.*

### <a name="derived-context"></a>Contexte dérivé

Une classe **BloggingContext** a été ajoutée au projet. Le contexte représente une session avec la base de données, ce qui nous permet d’interroger et d’enregistrer des données.
Le contexte expose une ** &lt; &gt; tente DbSet** pour chaque type dans notre modèle. Vous remarquerez également que le constructeur par défaut appelle un constructeur de base à l’aide de la syntaxe **Name =** . Cela indique Code First que la chaîne de connexion à utiliser pour ce contexte doit être chargée à partir du fichier de configuration.

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

*Vous devez toujours utiliser la syntaxe **Name =** lorsque vous utilisez une chaîne de connexion dans le fichier de configuration. Cela garantit que si la chaîne de connexion n’est pas présente, Entity Framework lève une exception au lieu de créer une nouvelle base de données par Convention.*

### <a name="model-classes"></a>Classes de modèle

Enfin, un **blog** et une classe de **publication** ont également été ajoutés au projet. Il s’agit des classes de domaine qui composent notre modèle. Vous verrez des annotations de données appliquées aux classes pour spécifier la configuration dans laquelle les conventions d’Code First ne sont pas alignées sur la structure de la base de données existante. Par exemple, vous verrez l’annotation **StringLength** sur **blog.Name** et **blog. URL** , car ils ont une longueur maximale de **200** dans la base de données (la code First par défaut est d’utiliser la longueur maximale prise en charge par le fournisseur de base de données- **nvarchar (max)** dans SQL Server).

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a>4. lecture & écriture de données

Maintenant que nous avons un modèle, il est temps de l’utiliser pour accéder à certaines données. Implémentez la méthode **main** dans **Program.cs** comme indiqué ci-dessous. Ce code crée une nouvelle instance de notre contexte, puis l’utilise pour insérer un nouveau **blog**. Il utilise ensuite une requête LINQ pour récupérer tous les **blogs** de la base de données classée par ordre alphabétique par **titre**.

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
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a>Que se passe-t-il si ma base de données change ?

L’Assistant Code First à la base de données est conçu pour générer un ensemble de classes de point de départ que vous pouvez ensuite ajuster et modifier. Si votre schéma de base de données change, vous pouvez modifier manuellement les classes ou exécuter un autre ingénieur inverse pour remplacer les classes.

## <a name="using-code-first-migrations-to-an-existing-database"></a>Utilisation de Migrations Code First à une base de données existante

Si vous souhaitez utiliser Migrations Code First avec une base de données existante, consultez [migrations code First à une base de données existante](xref:ef6/modeling/code-first/migrations/existing-database).

## <a name="summary"></a>Résumé

Dans cette procédure pas à pas, nous avons examiné Code First développement à l’aide d’une base de données existante. Nous avons utilisé le Entity Framework Tools pour Visual Studio pour rétroconcevoir un ensemble de classes mappées à la base de données et pouvant être utilisé pour stocker et récupérer des données.
