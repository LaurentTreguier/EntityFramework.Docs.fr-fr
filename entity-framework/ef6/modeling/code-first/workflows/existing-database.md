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
# <a name="code-first-to-an-existing-database"></a><span data-ttu-id="70f41-103">Code First à une base de données existante</span><span class="sxs-lookup"><span data-stu-id="70f41-103">Code First to an Existing Database</span></span>
<span data-ttu-id="70f41-104">Cette vidéo et la procédure pas à pas fournissent une introduction au développement Code First ciblant une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="70f41-104">This video and step-by-step walkthrough provide an introduction to Code First development targeting an existing database.</span></span> <span data-ttu-id="70f41-105">Code First vous permet de définir votre modèle à l’aide de \# classes C ou VB.net.</span><span class="sxs-lookup"><span data-stu-id="70f41-105">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="70f41-106">Éventuellement, une configuration supplémentaire peut être effectuée à l’aide d’attributs sur vos classes et propriétés ou à l’aide d’une API Fluent.</span><span class="sxs-lookup"><span data-stu-id="70f41-106">Optionally additional configuration can be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="70f41-107">Regarder la vidéo</span><span class="sxs-lookup"><span data-stu-id="70f41-107">Watch the video</span></span>
<span data-ttu-id="70f41-108">Cette vidéo est [désormais disponible sur Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).</span><span class="sxs-lookup"><span data-stu-id="70f41-108">This video is [now available on Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="70f41-109">Prérequis</span><span class="sxs-lookup"><span data-stu-id="70f41-109">Pre-Requisites</span></span>

<span data-ttu-id="70f41-110">Pour effectuer cette procédure pas à pas, vous devez avoir installé **Visual Studio 2012** ou **Visual Studio 2013** .</span><span class="sxs-lookup"><span data-stu-id="70f41-110">You will need to have **Visual Studio 2012** or **Visual Studio 2013** installed to complete this walkthrough.</span></span>

<span data-ttu-id="70f41-111">Vous aurez également besoin de la version **6,1** (ou ultérieure) du **Entity Framework Tools pour Visual Studio** .</span><span class="sxs-lookup"><span data-stu-id="70f41-111">You will also need version **6.1** (or later) of the **Entity Framework Tools for Visual Studio** installed.</span></span> <span data-ttu-id="70f41-112">Consultez [obtenir des Entity Framework](xref:ef6/fundamentals/install) pour plus d’informations sur l’installation de la dernière version du Entity Framework Tools.</span><span class="sxs-lookup"><span data-stu-id="70f41-112">See [Get Entity Framework](xref:ef6/fundamentals/install) for information on installing the latest version of the Entity Framework Tools.</span></span>

## <a name="1-create-an-existing-database"></a><span data-ttu-id="70f41-113">1. créer une base de données existante</span><span class="sxs-lookup"><span data-stu-id="70f41-113">1. Create an Existing Database</span></span>

<span data-ttu-id="70f41-114">En général, lorsque vous ciblez une base de données existante, elle est déjà créée, mais pour cette procédure pas à pas, nous devons créer une base de données à laquelle accéder.</span><span class="sxs-lookup"><span data-stu-id="70f41-114">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="70f41-115">Commençons par générer la base de données.</span><span class="sxs-lookup"><span data-stu-id="70f41-115">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="70f41-116">Ouvrir Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70f41-116">Open Visual Studio</span></span>
-   <span data-ttu-id="70f41-117">**Vue- &gt; Explorateur de serveurs**</span><span class="sxs-lookup"><span data-stu-id="70f41-117">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="70f41-118">Cliquez avec le bouton droit sur **connexions de données- &gt; Ajouter une connexion...**</span><span class="sxs-lookup"><span data-stu-id="70f41-118">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="70f41-119">Si vous n’êtes pas connecté à une base de données à partir de **Explorateur de serveurs** avant de devoir sélectionner **Microsoft SQL Server** comme source de données</span><span class="sxs-lookup"><span data-stu-id="70f41-119">If you haven’t connected to a database from **Server Explorer** before you’ll need to select **Microsoft SQL Server** as the data source</span></span>

    ![Sélectionner une source de données](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="70f41-121">Connectez-vous à votre instance de base de données locale et entrez les **blogs** comme nom de base de données</span><span class="sxs-lookup"><span data-stu-id="70f41-121">Connect to your LocalDB instance, and enter **Blogging** as the database name</span></span>

    ![Connexion de base de données locale](~/ef6/media/localdbconnection.png)

-   <span data-ttu-id="70f41-123">Sélectionnez **OK** . vous serez invité à créer une nouvelle base de données, sélectionnez **Oui** .</span><span class="sxs-lookup"><span data-stu-id="70f41-123">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![Boîte de dialogue créer une base de données](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="70f41-125">La nouvelle base de données s’affiche alors dans Explorateur de serveurs, cliquez dessus avec le bouton droit et sélectionnez **nouvelle requête** .</span><span class="sxs-lookup"><span data-stu-id="70f41-125">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="70f41-126">Copiez le code SQL suivant dans la nouvelle requête, cliquez avec le bouton droit sur la requête et sélectionnez **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="70f41-126">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

## <a name="2-create-the-application"></a><span data-ttu-id="70f41-127">2. créer l’application</span><span class="sxs-lookup"><span data-stu-id="70f41-127">2. Create the Application</span></span>

<span data-ttu-id="70f41-128">Pour simplifier les choses, nous allons créer une application console de base qui utilise Code First pour effectuer l’accès aux données :</span><span class="sxs-lookup"><span data-stu-id="70f41-128">To keep things simple we will build a basic console application that uses Code First to do the data access:</span></span>

-   <span data-ttu-id="70f41-129">Ouvrir Visual Studio</span><span class="sxs-lookup"><span data-stu-id="70f41-129">Open Visual Studio</span></span>
-   <span data-ttu-id="70f41-130">**Fichier- &gt; nouveau- &gt; projet...**</span><span class="sxs-lookup"><span data-stu-id="70f41-130">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="70f41-131">Sélectionnez **Windows** dans le menu de gauche et dans l' **application console** .</span><span class="sxs-lookup"><span data-stu-id="70f41-131">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="70f41-132">Entrez **CodeFirstExistingDatabaseSample** comme nom</span><span class="sxs-lookup"><span data-stu-id="70f41-132">Enter **CodeFirstExistingDatabaseSample** as the name</span></span>
-   <span data-ttu-id="70f41-133">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="70f41-133">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="70f41-134">3. modèle d’ingénierie à rebours</span><span class="sxs-lookup"><span data-stu-id="70f41-134">3. Reverse Engineer Model</span></span>

<span data-ttu-id="70f41-135">Nous utiliserons le Entity Framework Tools pour Visual Studio pour nous aider à générer du code initial à mapper à la base de données.</span><span class="sxs-lookup"><span data-stu-id="70f41-135">We will use the Entity Framework Tools for Visual Studio to help us generate some initial code to map to the database.</span></span> <span data-ttu-id="70f41-136">Ces outils génèrent simplement du code que vous pouvez également taper manuellement si vous préférez.</span><span class="sxs-lookup"><span data-stu-id="70f41-136">These tools are just generating code that you could also type by hand if you prefer.</span></span>

-   <span data-ttu-id="70f41-137">**Projet- &gt; Ajouter un nouvel élément...**</span><span class="sxs-lookup"><span data-stu-id="70f41-137">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="70f41-138">Sélectionnez **données** dans le menu de gauche, puis **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="70f41-138">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="70f41-139">Entrez **BloggingContext** comme nom et cliquez sur **OK** .</span><span class="sxs-lookup"><span data-stu-id="70f41-139">Enter **BloggingContext** as the name and click **OK**</span></span>
-   <span data-ttu-id="70f41-140">Cela lance l' **assistant Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="70f41-140">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="70f41-141">Sélectionnez **Code First dans la base de données** , puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="70f41-141">Select **Code First from Database** and click **Next**</span></span>

    ![Assistant CFE](~/ef6/media/wizardonecfe.png)

-   <span data-ttu-id="70f41-143">Sélectionnez la connexion à la base de données que vous avez créée dans la première section, puis cliquez sur **suivant** .</span><span class="sxs-lookup"><span data-stu-id="70f41-143">Select the connection to the database you created in the first section and click **Next**</span></span>

    ![Assistant 2 CFE](~/ef6/media/wizardtwocfe.png)

-   <span data-ttu-id="70f41-145">Cochez la case en regard de **tables** pour importer toutes les tables, puis cliquez sur **Terminer** .</span><span class="sxs-lookup"><span data-stu-id="70f41-145">Click the checkbox next to **Tables** to import all tables and click **Finish**</span></span>

    ![Assistant trois CFE](~/ef6/media/wizardthreecfe.png)

<span data-ttu-id="70f41-147">Une fois que le processus d’ingénierie à rebours est terminé, un certain nombre d’éléments ont été ajoutés au projet, examinons ce qui a été ajouté.</span><span class="sxs-lookup"><span data-stu-id="70f41-147">Once the reverse engineer process completes a number of items will have been added to the project, let's take a look at what's been added.</span></span>

### <a name="configuration-file"></a><span data-ttu-id="70f41-148">Fichier de configuration</span><span class="sxs-lookup"><span data-stu-id="70f41-148">Configuration file</span></span>

<span data-ttu-id="70f41-149">Un fichier de App.config a été ajouté au projet, ce fichier contient la chaîne de connexion à la base de données existante.</span><span class="sxs-lookup"><span data-stu-id="70f41-149">An App.config file has been added to the project, this file contains the connection string to the existing database.</span></span>

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

<span data-ttu-id="70f41-150">*Vous remarquerez également d’autres paramètres dans le fichier de configuration. il s’agit de paramètres EF par défaut qui indiquent Code First où créer les bases de données. Étant donné que nous allons mapper à une base de données existante, ces paramètres seront ignorés dans notre application.*</span><span class="sxs-lookup"><span data-stu-id="70f41-150">*You’ll notice some other settings in the configuration file too, these are default EF settings that tell Code First where to create databases. Since we are mapping to an existing database these setting will be ignored in our application.*</span></span>

### <a name="derived-context"></a><span data-ttu-id="70f41-151">Contexte dérivé</span><span class="sxs-lookup"><span data-stu-id="70f41-151">Derived Context</span></span>

<span data-ttu-id="70f41-152">Une classe **BloggingContext** a été ajoutée au projet.</span><span class="sxs-lookup"><span data-stu-id="70f41-152">A **BloggingContext** class has been added to the project.</span></span> <span data-ttu-id="70f41-153">Le contexte représente une session avec la base de données, ce qui nous permet d’interroger et d’enregistrer des données.</span><span class="sxs-lookup"><span data-stu-id="70f41-153">The context represents a session with the database, allowing us to query and save data.</span></span>
<span data-ttu-id="70f41-154">Le contexte expose une \*\* &lt; &gt; tente DbSet\*\* pour chaque type dans notre modèle.</span><span class="sxs-lookup"><span data-stu-id="70f41-154">The context exposes a **DbSet&lt;TEntity&gt;** for each type in our model.</span></span> <span data-ttu-id="70f41-155">Vous remarquerez également que le constructeur par défaut appelle un constructeur de base à l’aide de la syntaxe **Name =** .</span><span class="sxs-lookup"><span data-stu-id="70f41-155">You’ll also notice that the default constructor calls a base constructor using the **name=** syntax.</span></span> <span data-ttu-id="70f41-156">Cela indique Code First que la chaîne de connexion à utiliser pour ce contexte doit être chargée à partir du fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="70f41-156">This tells Code First that the connection string to use for this context should be loaded from the configuration file.</span></span>

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

<span data-ttu-id="70f41-157">*Vous devez toujours utiliser la syntaxe **Name =** lorsque vous utilisez une chaîne de connexion dans le fichier de configuration. Cela garantit que si la chaîne de connexion n’est pas présente, Entity Framework lève une exception au lieu de créer une nouvelle base de données par Convention.*</span><span class="sxs-lookup"><span data-stu-id="70f41-157">*You should always use the **name=** syntax when you are using a connection string in the config file. This ensures that if the connection string is not present then Entity Framework will throw rather than creating a new database by convention.*</span></span>

### <a name="model-classes"></a><span data-ttu-id="70f41-158">Classes de modèle</span><span class="sxs-lookup"><span data-stu-id="70f41-158">Model classes</span></span>

<span data-ttu-id="70f41-159">Enfin, un **blog** et une classe de **publication** ont également été ajoutés au projet.</span><span class="sxs-lookup"><span data-stu-id="70f41-159">Finally, a **Blog** and **Post** class have also been added to the project.</span></span> <span data-ttu-id="70f41-160">Il s’agit des classes de domaine qui composent notre modèle.</span><span class="sxs-lookup"><span data-stu-id="70f41-160">These are the domain classes that make up our model.</span></span> <span data-ttu-id="70f41-161">Vous verrez des annotations de données appliquées aux classes pour spécifier la configuration dans laquelle les conventions d’Code First ne sont pas alignées sur la structure de la base de données existante.</span><span class="sxs-lookup"><span data-stu-id="70f41-161">You'll see Data Annotations applied to the classes to specify configuration where the Code First conventions would not align with the structure of the existing database.</span></span> <span data-ttu-id="70f41-162">Par exemple, vous verrez l’annotation **StringLength** sur **blog.Name** et **blog. URL** , car ils ont une longueur maximale de **200** dans la base de données (la code First par défaut est d’utiliser la longueur maximale prise en charge par le fournisseur de base de données- **nvarchar (max)** dans SQL Server).</span><span class="sxs-lookup"><span data-stu-id="70f41-162">For example, you'll see the **StringLength** annotation on **Blog.Name** and **Blog.Url** since they have a maximum length of **200** in the database (the Code First default is to use the maximun length supported by the database provider - **nvarchar(max)** in SQL Server).</span></span>

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

## <a name="4-reading--writing-data"></a><span data-ttu-id="70f41-163">4. lecture & écriture de données</span><span class="sxs-lookup"><span data-stu-id="70f41-163">4. Reading & Writing Data</span></span>

<span data-ttu-id="70f41-164">Maintenant que nous avons un modèle, il est temps de l’utiliser pour accéder à certaines données.</span><span class="sxs-lookup"><span data-stu-id="70f41-164">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="70f41-165">Implémentez la méthode **main** dans **Program.cs** comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="70f41-165">Implement the **Main** method in **Program.cs** as shown below.</span></span> <span data-ttu-id="70f41-166">Ce code crée une nouvelle instance de notre contexte, puis l’utilise pour insérer un nouveau **blog**.</span><span class="sxs-lookup"><span data-stu-id="70f41-166">This code creates a new instance of our context and then uses it to insert a new **Blog**.</span></span> <span data-ttu-id="70f41-167">Il utilise ensuite une requête LINQ pour récupérer tous les **blogs** de la base de données classée par ordre alphabétique par **titre**.</span><span class="sxs-lookup"><span data-stu-id="70f41-167">Then it uses a LINQ query to retrieve all **Blogs** from the database ordered alphabetically by **Title**.</span></span>

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

<span data-ttu-id="70f41-168">Vous pouvez maintenant exécuter l’application et la tester.</span><span class="sxs-lookup"><span data-stu-id="70f41-168">You can now run the application and test it.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a><span data-ttu-id="70f41-169">Que se passe-t-il si ma base de données change ?</span><span class="sxs-lookup"><span data-stu-id="70f41-169">What if My Database Changes?</span></span>

<span data-ttu-id="70f41-170">L’Assistant Code First à la base de données est conçu pour générer un ensemble de classes de point de départ que vous pouvez ensuite ajuster et modifier.</span><span class="sxs-lookup"><span data-stu-id="70f41-170">The Code First to Database wizard is designed to generate a starting point set of classes that you can then tweak and modify.</span></span> <span data-ttu-id="70f41-171">Si votre schéma de base de données change, vous pouvez modifier manuellement les classes ou exécuter un autre ingénieur inverse pour remplacer les classes.</span><span class="sxs-lookup"><span data-stu-id="70f41-171">If your database schema changes you can either manually edit the classes or perform another reverse engineer to overwrite the classes.</span></span>

## <a name="using-code-first-migrations-to-an-existing-database"></a><span data-ttu-id="70f41-172">Utilisation de Migrations Code First à une base de données existante</span><span class="sxs-lookup"><span data-stu-id="70f41-172">Using Code First Migrations to an Existing Database</span></span>

<span data-ttu-id="70f41-173">Si vous souhaitez utiliser Migrations Code First avec une base de données existante, consultez [migrations code First à une base de données existante](xref:ef6/modeling/code-first/migrations/existing-database).</span><span class="sxs-lookup"><span data-stu-id="70f41-173">If you want to use Code First Migrations with an existing database, see [Code First Migrations to an existing database](xref:ef6/modeling/code-first/migrations/existing-database).</span></span>

## <a name="summary"></a><span data-ttu-id="70f41-174">Résumé</span><span class="sxs-lookup"><span data-stu-id="70f41-174">Summary</span></span>

<span data-ttu-id="70f41-175">Dans cette procédure pas à pas, nous avons examiné Code First développement à l’aide d’une base de données existante.</span><span class="sxs-lookup"><span data-stu-id="70f41-175">In this walkthrough we looked at Code First development using an existing database.</span></span> <span data-ttu-id="70f41-176">Nous avons utilisé le Entity Framework Tools pour Visual Studio pour rétroconcevoir un ensemble de classes mappées à la base de données et pouvant être utilisé pour stocker et récupérer des données.</span><span class="sxs-lookup"><span data-stu-id="70f41-176">We used the Entity Framework Tools for Visual Studio to reverse engineer a set of classes that mapped to the database and could be used to store and retrieve data.</span></span>
